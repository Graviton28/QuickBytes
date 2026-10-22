# Transferring data with Globus

Globus is a web-based file transfer service. Instead of using `scp` or `rsync` in a terminal, you choose a source and a destination in your web browser, and Globus runs the transfer in the background. Globus retries failed transfers, verifies file integrity, and sends you an email when the transfer is done. You can close your browser while a transfer is running.

CARC has three Globus collections, one for each of the main storage areas described in the [storage and backup QuickByte](https://github.com/UNM-CARC/QuickBytes/blob/master/storage_and_backup.md):

* **UNM CARC home** - your home directory, `/users/your-user-name/`
* **UNM CARC projects** - your project directories
* **UNM CARC scratch** - your scratch directory, `/carc/scratch/users/your-user-name/`

This QuickByte covers setting up Globus for the first time and transferring files between two of these collections. To transfer files between CARC and your own computer with `scp` or `rsync`, see the [transferring data QuickByte](https://github.com/UNM-CARC/QuickBytes/blob/master/transfer_data.md).

* Globus Web App: https://app.globus.org
* Globus Documentation: https://docs.globus.org

## Setting up Globus

You only need to do these steps the first time you use Globus.

#### 1. Open Globus and log in with your UNM account

Go to [app.globus.org](https://app.globus.org). You can also get there from the [CARC homepage](https://carc.unm.edu) by opening the "HPC Resources" menu and clicking "Transfer Data (Globus)".

![](/Images/globus-img1.png)

Under "Use your organizational login", search for and select "University of New Mexico", then click "Continue". You will be sent to the UNM login page. Log in with your UNM NetID and password.

![](/Images/globus-img2.png)

#### 2. Allow UNM to share your information

UNM will ask permission to send your name and email to CILogon, which Globus uses to verify university logins. Select "Ask me again if information to be provided to this service changes" so you are not asked every time, then click "Accept".

![](/Images/globus-img3.png)

#### 3. Create your Globus account

The first time you log in, Globus will ask if you want to link to an existing Globus account. If you have not used Globus before, click "Continue".

![](/Images/globus-img4.png)

Check the box to agree to the Terms of Service and Privacy Policy, click "Continue", and click "Allow" when prompted.

![](/Images/globus-img5.png)

You should now see the Globus Dashboard. Use the menu on the left side of the page to move between pages. This QuickByte uses the "File Manager" and "Activity" pages.

![](/Images/globus-img6.png)

#### 4. Link your CARC identity

Your UNM account logs you in to Globus, but Globus also needs your CARC username to access files on CARC systems. Click "File Manager" on the left, click the "Collection" search bar, and search for "UNM CARC". Select any of the three CARC collections. The example below uses "UNM CARC projects". Globus will say that authentication/consent is required. Click "Continue".

![](/Images/globus-img7.png)

Click "Link a UNM CARC (carc.unm.edu) identity", log in with your CARC username and password, and click "Allow" when prompted.

![](/Images/globus-img8.png)

The first time you open each of the other CARC collections, Globus may ask you to authenticate again. Click "Use my your-user-name@carc.unm.edu identity".

![](/Images/globus-img9.png)

The collection will now show your files. To go to a specific directory, type its path into the "Path" bar.

![](/Images/globus-img10.png)

## Transferring files between CARC collections

The example below copies files from a directory in home to scratch. The same steps work for any two of the CARC collections.

#### 1. Open the two-panel view

In the File Manager, click the middle "Panels" button in the top right to show two collections side by side. The left panel keeps the collection you already had open.

![](/Images/globus-img11.png)

#### 2. Choose the source and destination

In the left panel, select the source collection and path. In this example, that is "UNM CARC home" at `/users/your-user-name/testing/gaussian_review/`. In the right panel, search for the destination collection and enter a path. In this example, that is "UNM CARC scratch" at `/carc/scratch/users/your-user-name/`.

![](/Images/globus-img12.png)

#### 3. Review the transfer options (optional)

Click "Transfer & Timer Options" to see more settings for the transfer. The defaults work for most transfers. Some useful options are:

* **apply sync level** - only copies files that are new or have changed on the source, similar to `rsync`. This is useful when you run the same transfer again after some files have changed.
* **preserve source file modification times** - by default, copied files get the time of the transfer as their modification time.
* **Schedule Start / Repeat** - start the transfer at a later time, or repeat it on a schedule.

File integrity verification and encryption are both on by default. The CARC collections require encryption.

![](/Images/globus-img13.png)

#### 4. Select files and start the transfer

Check the boxes next to the files or folders you want to transfer, or click "select all".

Click the "Start" button on the origin side, above the panel you are transferring from. Each "Start" button sends files from its own panel to the other panel. In this example, the left "Start" button sends files from home (left) to scratch (right). Clicking the "Start" button on the destination side will not transfer the files you selected.

A green "Transfer request submitted successfully" message will appear.

![](/Images/globus-img14.png)

#### 5. Check on the transfer

Click "View details" on the green message, or click "Activity" on the left menu, to see the status of your transfer. Large transfers can take a long time, and you do not need to keep the page open.

![](/Images/globus-img15.png)

Globus will also send you an email with a summary when the transfer finishes.

![](/Images/globus-img16.png)

#### 6. Refresh the destination

The destination panel does not update on its own. After the transfer finishes, it will still show the folder as it was before the transfer. Click "refresh list" on the destination panel to see your transferred files.

![](/Images/globus-img17.png)

*This quickbyte was validated on 10/22/2026*
