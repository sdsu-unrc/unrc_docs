
### Windows

You will need to download a program to make a command-line terminal
connection. A typical choice is PuTTY,
[*http://www.chiark.greenend.org.uk/\~sgtatham/putty/*.](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
You can download and run the Windows installer, or you can just download
the executable of the program itself, putty.exe, and put it directly on
your desktop. When you start PuTTY, you will be presented with a
connection screen. Assuming you want to make your first connection to
flapjack, enter flapjack.jacks.local in the Host Name field.

![](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/connecting/image1.jpg)

When you click Open you should see the black PuTTY terminal window
appear and then a message similar to the following:

![](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/connecting/image2.jpg)

This message about the encryption key should appear only the first time
you connect to a given server from a particular computer. PuTTY is an
encrypted protocol, but the initial connection has to be made in the
clear to transfer the key. So click yes. Then you will see the login
prompt for the server. Enter your username and then your password. Note
that no characters will appear at all as you type the password.

![](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/connecting/image3.jpg)

When you have successfully logged in you will see the motd and the
command line system prompt.

![](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/connecting/image4.jpg)


### Mac

For the Mac OS you can use the Terminal app in the Utilities folder
under you Applications folder. One way to quickly open Utilities is from
the Go menu of Finder. Then go to the Terminal application and open it.
This brings up a local command line window on your Mac. From that
command prompt, use the ssh command to connect.

Once the terminal prompt is available, type ssh
Username@cluster.jacks.local where cluster is the node you wish to
connect to.

![](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/connecting/image5.png)
