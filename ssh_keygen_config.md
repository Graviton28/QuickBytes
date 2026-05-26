# General SSH Key Configuration
Once you start computing you will be logging in to the CARC systems fairly often, and having to type your username at the machine address will become tedious. In order to alleviate this tedium, it is beneficial to generate SSH keys and an SSH config file. The SSH keys bypass the need to enter your password each time you log in, and the config file stores the addresses of all the machines you are logging in to.

### SSH key generation
First, set up your SSH key. To do this, type the following in the terminal prompt:
```bash
ssh-keygen
```
You will then be asked which file you would like to save your new key under. Press enter here so it will be saved at the default location.

Next, it will ask you to enter a passphrase. We recommend you add a passphrase. You'll enter your password here, and then confirm it by entering that same password a second time.

After this point, your SSH key has been created. You should see the randomart image for your key:
```
The key's randomart image is:
+---[RSA 3072]----+
|            E.o+=|
|         o . = .*|
|        . = oo ==|
|         + oo @.=|
|        S +  B.=o|
|       o   o. .=.|
|      + o + . o+o|
|     o o o o .+ o|
|   .o       .  o |
+----[SHA256]-----+
```
This is a mechanism built into SSH keys which takes each character from the key itself and converts it into a unique image based on the key you just created. It is meant to be a quick and easy way to compare SSH keys to make sure nothing has changed.

After this point, your SSH key will have been created in your `~/.ssh` directory. This directory should now have the following structure:
```bash
$ ls ~/.ssh
id_rsa  id_rsa.pub
```
Now that your SSH key is created on your local machine, we need to copy over the public key to the server you want to connect to.
```bash
ssh-copy-id <CARC-USERNAME>@easley.alliance.unm.edu
```
After you've entered your password, your public SSH key will be copied into your home directory on easley. After this point, whenever you SSH from this machine it will check this key against the private key that was just generated in your `.ssh` directory, and let you in without prompting you for a password.

Since your home directory is shared across all machines at CARC, you only need to do this step once to enable SSH key access across all CARC machines.

### SSH config file
To make logging in to CARC even easier, we also recommend setting up an SSH config file which allows you to simply type `ssh machinename` instead of your username at the machine address. To set up this file, simply copy the example below and save it to a text document in your `ssh` folder, which is found at `~/.ssh/`. Change the user to your CARC username and you are set to log in quickly and efficiently. You can add machines based on which ones you have access to.
```
Host easley
    hostname easley.alliance.unm.edu
    user CHANGEME
    port 22
Host hopper
    hostname hopper.alliance.unm.edu
    user CHANGEME
    port 22
```

# Troubleshooting & Git
Note that on the CARC clusters, by default your SSH configuration file will contain the following:
```
# Added by Warewulf  xxxx-xx-xx
Host *
IdentityFile ~/.ssh/cluster
StrictHostKeyChecking=no
```
This helps ensure you're able to connect freely across all of the CARC clusters; for example, while logged in to easley you can just type `ssh easley`.

The issue here will arise if you need to add a new SSH key for some reason; say, you need to add an SSH key so you're able to make edits to a git repository from the CARC clusters. If this is the case, you can start by creating a new SSH key as explained in the previous steps of this tutorial.

You should then edit your `~/.ssh/config` file as mentioned above, and change the Host line under the Warewulf defined section to the following:
```
# Added by Warewulf  xxxx-xx-xx
Host easley*
IdentityFile ~/.ssh/cluster
StrictHostKeyChecking=no
```
This will ensure git will use the default key on the system when cloning with SSH (which will be the new one you just created), and will properly verify your credentials after adding the new public key to your GitHub account. If you do not do this step, you will receive a permission error when trying to clone or push to a git repo using SSH.

*This quickbyte was validated on 5/26/2026*
