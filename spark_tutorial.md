# Apache Spark on Easley

Apache Spark is a parallel/distributed computing environment designed for processing large, complex data sets. Compared to other large-scale parallel programming environments (e.g. MPI), it is generally easier to program, especially for data-centric workloads. You write a Java, Scala, or Python program that coordinates parallel data processing across many worker processes.

This tutorial explains how to run Spark on the UNM CARC Easley cluster using Slurm. All files referenced here are available at <https://github.com/graviton28/QuickBytes/tree/master/spark>.

---

## The Basic Spark Model

Spark uses a Single-Program-Multiple-Data (SPMD) model. You write one program that runs on a single **master** node; that master orchestrates many parallel **workers**, each of which processes a slice of the overall data.

The core distributed data abstraction is the **Resilient Distributed Dataset (RDD)**. In practice you will more often work with **DataFrames**, a higher-level, column-oriented layer on top of RDDs.

- **Transformations** (`map`, `filter`, `groupBy`) are lazy and run in parallel across workers.
- **Actions** (`collect`, `count`, `show`) trigger execution and return results to the master program.

---

## The Canonical Simple Example: Word Count in Python

```python
from pyspark.sql import SparkSession
from operator import add
import sys

spark = SparkSession.builder.appName("WordCount").getOrCreate()
lines = spark.read.text(sys.argv[1]).rdd.map(lambda r: r[0])
words = lines.flatMap(lambda line: line.split())
counts = words.map(lambda word: (word, 1)).reduceByKey(add)
output = counts.collect()

for word, count in sorted(output, key=lambda x: -x[1])[:20]:
    print(f"{word}: {count}")
```

---

## Required Modules on Easley

```bash
module load spark/3.5.1-kn2k
```

> **Important:** The generic `module load spark` command loads `spark/3.5.1-sewd` by default, which is **missing Hadoop/SLF4J dependencies** and will fail with `NoClassDefFoundError: org/slf4j/Logger`. Always load the explicit version shown above.

The `spark/3.5.1-kn2k` module sets `$SPARK_ROOT` (not `$SPARK_HOME` or `$SPARK_DIR`). Set `SPARK_HOME` manually:

```bash
export SPARK_HOME=$SPARK_ROOT
```

For DataFrame/Pandas/Matplotlib work, also load a Python environment:

```bash
module load miniforge3
conda activate spark-env
```

---

## The `slurm-spark-submit` Script

`slurm-spark-submit` is the Slurm-native replacement for the legacy `pbs-spark-submit` script. It:

1. Loads the correct Spark module and maps `SPARK_ROOT` → `SPARK_HOME`.
2. Reads the allocated node list from `$SLURM_JOB_NODELIST`.
3. Starts the Spark master directly on the first allocated node.
4. Starts one worker per node via `ssh`, explicitly passing through `SPARK_HOME`, `JAVA_HOME`, `SPARK_DIST_CLASSPATH`, and `PYSPARK_PYTHON` so each worker has the correct environment.
5. If a Python script is passed as an argument, submits it with `spark-submit` and shuts the cluster down afterward.
6. If no script is passed, leaves the cluster running for interactive `pyspark` use.

Make it executable once:
```bash
chmod +x slurm-spark-submit
```

---

## Running Interactively

```bash
salloc --nodes=1 --ntasks-per-node=1 --cpus-per-task=4 --mem=16G --time=00:30:00 --partition=general
module load spark/3.5.1-kn2k
./slurm-spark-submit
```

The script prints a master URL, e.g.:
MASTER_URL=spark://easley002:7077
Connect with `pyspark`:
```bash
export PYSPARK_PYTHON=~/.conda/envs/spark-env/bin/python
export PYSPARK_DRIVER_PYTHON=~/.conda/envs/spark-env/bin/python
pyspark --master spark://easley002:7077
```

```python
>>> sc.parallelize(range(100)).sum()
4950
```

> **Note:** `slurm-spark-submit` sets `PYSPARK_PYTHON`/`PYSPARK_DRIVER_PYTHON` internally for any application it submits directly, but if you connect with `pyspark` by hand afterward, you must export these in your own shell too — otherwise you'll see `PYTHON_VERSION_MISMATCH` errors if your conda environment's Python differs from the system default (3.9).

---

## Running in Batch

```bash
#!/bin/bash
#SBATCH --job-name=spark-wordcount
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=4
#SBATCH --mem=16G
#SBATCH --time=00:10:00
#SBATCH --output=wordcount_%j.log
#SBATCH --partition=general

module load spark/3.5.1-kn2k
export SPARK_HOME=$SPARK_ROOT

SCRATCHDIR="/tmp/spark-${SLURM_JOB_ID}"
mkdir -p "$SCRATCHDIR"
cp "$SLURM_SUBMIT_DIR/wordcount.py" "$SCRATCHDIR/"
cp "$SLURM_SUBMIT_DIR/big.txt"      "$SCRATCHDIR/"
cd "$SCRATCHDIR"

bash "$SLURM_SUBMIT_DIR/slurm-spark-submit" \
    wordcount.py big.txt > wordcount.log 2>&1

cp wordcount.log "$SLURM_SUBMIT_DIR/"
```

Submit with:
```bash
sbatch wordcount.sh
```

Monitor with `squeue --me`, then check `wordcount_<jobid>.log` and `wordcount.log`.

---

## Multi-Node Clusters

Request more nodes and the script handles the rest:
```bash
salloc --nodes=2 --ntasks-per-node=1 --cpus-per-task=4 --mem=16G --time=00:30:00 --partition=general
./slurm-spark-submit
```

Verify both workers are alive:
```bash
ps aux | grep -E "Master|Worker" | grep -v grep
ss -tlnp | grep 7077
```

> **Note:** Worker/master `.out` logs under `/tmp/spark-$SLURM_JOB_ID/logs/` may appear to contain only the launch command with no further output. This is expected with this Spack build's default log4j configuration and does not indicate failure — use `ps aux` and a real `pyspark` test to confirm cluster health instead of relying on log contents.

Test distribution across nodes:
```python
>>> sc.parallelize(range(1000), 32).mapPartitions(lambda it: [sum(1 for _ in it)]).collect()
```
A list of 32 nonzero partition sizes confirms work was spread across all allocated nodes.

---

## DataFrames, Pandas, and Plotting

Once connected, you can load data into distributed DataFrames and pull aggregated results back to the driver for plotting:

```python
import matplotlib
matplotlib.use("PDF")
import matplotlib.pyplot as plt
import pandas as pd
from pyspark.sql.functions import month

monthly = (
    df.withColumn("Month", month("Date"))
    .groupBy("Month").count()
    .orderBy("Month")
    .collect()
)

pdf = pd.DataFrame(monthly, columns=["month", "crime_count"])
pdf.plot(figsize=(20, 10), kind="line", x="month", y="crime_count",
         color="red", linewidth=8, legend=False)
plt.savefig("crimes-by-month.pdf")
```

---

## Known Issues on Easley

### Wrong default Spark module
`module load spark` loads `spark/3.5.1-sewd`, which is missing Hadoop/SLF4J dependencies. Always load the explicit version:
```bash
module load spark/3.5.1-kn2k
```

### SPARK_HOME is not set by the module
The module sets `$SPARK_ROOT`, not `$SPARK_HOME`. Map it manually:
```bash
export SPARK_HOME=$SPARK_ROOT
```

### Python version mismatch between driver and workers
PySparkRuntimeError: [PYTHON_VERSION_MISMATCH] Python in worker has

different version (3, 9) than that in driver 3.11
Fix by setting both variables to your conda environment's Python:
```bash
export PYSPARK_PYTHON=~/.conda/envs/spark-env/bin/python
export PYSPARK_DRIVER_PYTHON=~/.conda/envs/spark-env/bin/python
```

### Worker startup logs appear empty
Logs in `/tmp/spark-$SLURM_JOB_ID/logs/` may show only the launch command. This is normal — verify cluster health with `ps aux` and a live `pyspark` test instead.

---

## Further Reading

- **Spark Streaming** — DStreams for micro-batched data processing.
- **Spark ML** — scalable regression, classification, and clustering.
- **Spark SQL** — run ANSI SQL directly against DataFrames via `spark.sql(...)`.
- Official documentation: <https://spark.apache.org/docs/3.5.1/>
