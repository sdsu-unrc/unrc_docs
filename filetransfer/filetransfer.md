

## File transfers

### Windows

A typical choice for a Windows file transfer helper program is
WinSCP,[*http://winscp.net/eng/index.php*](http://winscp.net/eng/index.php).  Similar to PuTTY, you have the choice of downloading either a Windows installer or a standalone executable.  If you choose to download the portable executable, it will come as a zipped folder.  Expand the folder and move WinSCP.exe onto your desktop.  The initial screen is similar to the PuTTY connection window.  Enter the name of the server you want to connect to (e.g. blackjack.jacks.local) and click Login.

![winscp01 Session screen](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/filetransfer/winscp01.png)


If it is the first time you have connected to the server in question, you might see a warning.

![WinSCP first time connection warning](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/filetransfer/winscp02.png)

Click yes and the WinSCP file transfer screen should appear.  After that you should be presented with with two side-by-side windows with a view of the files systems on both machines.

![WinSCP file transfer window](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/filetransfer/winscp03.png)

Now at this point you can navigate your file systems independently within each window,
find the file you want to transfer, then transfer it either by dragging and dropping, or using the context menu (right click on a file) to see the options.


### Mac

A commonly used GUI file trasnfer program is FileZilla (https://filezilla-project.org, or use the App Store).  Once installed and started, it functions very similar to WinSCP.

![FileZilla file transfer window](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/filetransfer/filezilla.png)


Note here that we enter the server name with the prefix sftp:// to force the secure ftp protocol. Another way to ensure this is to put 22 in the Port entry.
