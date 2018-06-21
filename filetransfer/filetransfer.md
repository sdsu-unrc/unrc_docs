

## File transfers

### Windows

A typical choice for a Windows file transfer helper program is
WinSCP,[*http://winscp.net/eng/index.php*](http://winscp.net/eng/index.php).  Similar to PuTTY, you have the choice of downloading either a Windows installer or a standalone executable.  If you choose to download the portable executable, it will come as a zipped folder.  Expand the folder and move WinSCP.exe onto your desktop.  The initial screen is similar to the PuTTY connection window.  Enter the name of the server you want to connect to (e.g. blackjack.jacks.local) and click Login.

![winscp01 Session screen](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/filetransfer/winscp01.png)


If it is the first time you have connected to the server in question, you might see a warning.

![WinSCP first time connection warning](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/filetransfer/winscp02.png)

Click yes and the WinSCP file transfer screen should appear.

### Mac

For the Mac OS you can use the Terminal app in the Utilities folder
under you Applications folder. One way to quickly open Utilities is from
the Go menu of Finder. Then go to the Terminal application and open it.
This brings up a local command line window on your Mac. From that
command prompt, use the ssh command to connect.

Once the terminal prompt is available, if you wanted to make a connection to, for example, blackjack, type a command like:

```
ssh mooreb@blackjack.jacks.local
```

but substitute your username on the cluster for the example (`mooreb`) above.
