# Transferring data with Globus

Globus is a web-based file transfer service built for moving research data quickly and reliably. Instead of keeping a terminal open for `scp` or `rsync`, you pick a source and a destination in your web browser and Globus handles the transfer in the background — retrying on failure, verifying file integrity, and emailing you when it's done. You can close your browser while a transfer runs.

CARC provides three Globus collections, one for each of the main storage areas described in our [storage and backup QuickByte](https://github.com/UNM-CARC/QuickBytes/blob/master/storage_and_backup.md):

* **UNM CARC home** - your home directory, `/users/your-user-name/`
* **UNM CARC projects** - your project directories
* **UNM CARC scratch** - your scratch directory, `/carc/scratch/users/your-user-name/`

This QuickByte walks through setting up Globus for the first time and then transferring files between two of these collections. For transferring files between CARC and your own computer with `scp` or `rsync`, see the [transferring data QuickByte](https://github.com/UNM-CARC/QuickBytes/blob/master/transfer_data.md).

* Globus Web App: https://app.globus.org
* Globus Documentation: https://docs.globus.org

## Setting up Globus

You only need to do these steps the first time you use Globus.

#### 1. Open Globus and log in with your UNM account

Go to [app.globus.org](https://app.globus.org). You can also get there from the [CARC homepage](https://carc.unm.edu) by opening the "HPC Resources" menu and clicking "Transfer Data (Globus)".

![](/Images/globus-img1.png)

Under "Use your organizational login", search for and select "University of New Mexico", then click "Continue". This will take you to the familiar UNM login page, where you log in with your UNM NetID and password.

![](/Images/globus-img2.png)

#### 2. Allow UNM to share your information

UNM will ask permission to send your name and email to CILogon, the service Globus uses to verify university logins. Select "Ask me again if information to be provided to this service changes" so you aren't asked every time, then click "Accept".

![](/Images/globus-img3.png)

#### 3. Create your Globus account

Since this is your first time logging in, Globus will offer to link to an existing Globus account. Unless you've used Globus before with a different login, click "Continue".

![](/Images/globus-img4.png)

Check the box agreeing to the Terms of Service and Privacy Policy, then click "Continue".

![](/Images/globus-img5.png)

#### 4. Allow the Globus Web App

Click "Allow" so the Globus Web App can manage transfers on your behalf.

![](/Images/globus-img6.png)

You should now see the Globus Dashboard. The menu on the left side of the page is how you get around Globus — the two pages we'll use are "File Manager" and "Activity".

![](/Images/globus-img7.png)

#### 5. Link your CARC identity

Logging in with your UNM account gets you into Globus, but to access files on CARC systems Globus also needs to know your CARC username. Click "File Manager" on the left, then click the "Collection" search bar and search for "UNM CARC". Select any of the three CARC collections — "UNM CARC projects" in the example below. Globus will say that authentication/consent is required. Click "Continue".

![](/Images/globus-img8.png)

Click "Link a UNM CARC (carc.unm.edu) identity" and log in with your CARC username and password.

![](/Images/globus-img9.png)

Once you've logged in, click "Allow".

![](/Images/globus-img10.png)

The first time you open each of the other CARC collections, Globus may ask you to authenticate again. Since your CARC identity is now linked, just click "Use my your-user-name@carc.unm.edu identity".

![](/Images/globus-img11.png)

Once linked, the collection will show your files, just like `ls` would on the cluster. You can type a directory into the "Path" bar to jump straight to it.

![](/Images/globus-img12.png)

## Transferring files between CARC collections

The example below copies files from a directory in home to scratch, but the same steps work for any combination of the three CARC collections.

#### 1. Open the two-panel view

In the File Manager, click the middle "Panels" button in the top right to show two collections side by side. The left panel will keep whatever collection you already had open.

![](/Images/globus-img13.png)

#### 2. Choose the source and destination

In the left panel, select the source collection and path — here, "UNM CARC home" at `/users/your-user-name/testing/gaussian_review/`. In the right panel, search for the destination collection and enter a path — here, "UNM CARC scratch" at `/carc/scratch/users/your-user-name/`.

![](/Images/globus-img14.png)

#### 3. Review the transfer options (optional)

Clicking "Transfer & Timer Options" shows extra settings for the transfer. The defaults are fine for most transfers. A few that are useful to know:

* **apply sync level** - only copies files that are new or have changed on the source, similar to `rsync`. Useful for re-running the same transfer after some of your files change.
* **preserve source file modification times** - by default, copied files get the time of the transfer as their modification time.
* **Schedule Start / Repeat** - start the transfer later, or repeat it on a schedule.

Note that file integrity verification and encryption are both on by default, and encryption is required by the CARC collections.

![](/Images/globus-img15.png)

#### 4. Select files and start the transfer

Check the boxes next to the files or folders you want to transfer, or click "select all".

Next, click the "Start" button on the **origin** side, above the panel you are transferring **from**. Each "Start" button sends files away from its own panel, so in this example the left "Start" button sends files from home (left) to scratch (right). Clicking the "Start" button on the destination side won't transfer the files you selected.

A green "Transfer request submitted successfully" message will appear.

![](/Images/globus-img16.png)

#### 5. Check on the transfer

Click "View details" on the green message, or "Activity" on the left menu, to see the status of your transfer. Large transfers can take a while — you don't need to keep the page open.

![](/Images/globus-img17.png)

Globus will also email you when the transfer finishes, with a summary of what was transferred.

![](/Images/globus-img18.png)

#### 6. Refresh the destination

The destination panel does **not** update on its own — even after the transfer has finished, it will still show the folder as it was before. Back in the File Manager, click "refresh list" on the destination panel to see your transferred files.

![](/Images/globus-img19.png)

*This quickbyte was validated on 10/22/2026*
