

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

### Command-line file transfers

On UNIX/Linux or Mac systems, we can also transfer files from system to system by using the command line, for example by using the `scp` command, which stands for “secure copy” but more importantly *remote* secure copy. `scp` is similar to `cp` but either source or target is on another system. See Wikipedia article [Secure copy](https://en.wikipedia.org/wiki/Secure_copy).

Example: copying a file from blackjack to another system:

```console
mooreb@blackjack:~> scp gsl/gsl-1.14.tar.gz  mooreb@silvertip.jacks.local:
Password:
gsl-1.14.tar.gz                             100% 3089KB   3.0MB/s   00:00
mooreb@blackjack:~>
```

By default, the file will be copied to the top level folder of the user
specified on the remote system. This can be modified by adding
sub-folders on the remote end.

```console
mooreb@blackjack:~> scp fdt.jar mooreb@silvertip.jacks.local:folder/subfolder
```

Of course you must know that these folders exist on the target.

When invoking the `cp` or `scp` command, if the target name is a folder,
the file is put into the folder. If the target name does not exist, the
file is copied and given the target name.

Note that both cp and scp have a recursive option, `-r`, that will
operate on a folder instead of a file, and recursively copy the entire
tree.
