
### Windows

You will need to download a program to make a command-line terminal
connection. A typical choice is PuTTY,
[*http://www.chiark.greenend.org.uk/\~sgtatham/putty/*.](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
You can download and run the Windows installer, or you can just download
the executable of the program itself, putty.exe, and put it directly on
your desktop. When you start PuTTY, you will be presented with a
connection screen. Assuming you want to make your first connection to
blackjack, enter blackjack.jacks.local in the Host Name field.

![PuTTY Session screen](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/connecting/puttycap01.png)

When you click Open you should see the black PuTTY terminal window
appear and then a message similar to the following:

![PuTTY first time connection warning](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/connecting/puttycap02.png)

This message about the encryption key should appear only the first time
you connect to a given server from a particular computer. PuTTY is an
encrypted protocol, but the initial connection has to be made in the
clear to transfer the key. So click yes. Then you will see the login
prompt for the server. Enter your username and then your password. Note
that no characters will appear at all as you type the password.

![PuTTY terminal password prompt](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/connecting/puttycap03cropped.png)

When you have successfully logged in you will see the motd and the
command line system prompt.

![PuTTY terminal session successful login example on blackjack](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/connecting/puttycap04.png)


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

![Apple Mac terminal ssh connection](https://raw.githubusercontent.com/sdsu-unrc/unrc_docs/master/connecting/mactermcap.png)
