---
author:
- |
    Brian Moore\
    University Networking and Research Computing (UNRC)\
    [Brian.Moore@sdstate.edu](Brian.Moore@sdstate.edu)
title: ' VNC access to kojack: tunneling with PuTTY'
---

Purpose
=======

For on-campus users needing access to a VNC session on one of the UNRC
servers (in this case `kojack`), currently the VNC port range (5900+) is
blocked from the student wireless network, however a direct ssh
connection is possible (using the IP for kojack instead of the name).
Here we describe using the Windows PuTTY program to configure tunneling
(through port 22) to access to a persistent VNC session.

Existing Documentation
======================

We are currently presenting our documentation in a [shared Box.com
documentation
folder](https://sdsu.box.com/s/aet477q974ad872vz4v7v24jq5vxer6u),
<https://goo.gl/a8Z5MU>. In the folder, the `Access_to_KOJACK.pdf`
document outlines how to connect to `kojack`, via command line shell and
VNC (on-demand and peristent sessions).

Finding your VNC screen number/port number
==========================================

The tunneling process will only work with with a predictable port
number, so the persistent VNC method is required. If you have a session
running, you will need to find your screen number; if you don’t have a
session running, you will have to start one and note the screen number.

Checking to see if you already have a VNC session
-------------------------------------------------

List all processes and search (grep) for your username then also grep
for the vnc string.

``` {frame="single"}
mooreb@kojack:~> ps aux|grep mooreb|grep vnc
mooreb   17537  0.0  0.0  91176 15880 ?        S    13:36   0:00 Xvnc :56 -desktop X -httpd /usr/share/vnc/classes -auth /home/mooreb/.Xauthority -geometry 1280x800 -depth 24 -rfbwait 120000 -rfbauth /home/mooreb/.vnc/passwd -rfbport 5956 -fp /usr/share/fonts/misc:unscaled,/usr/share/fonts/local,/usr/share/fonts/75dpi:unscaled,/usr/share/fonts/100dpi:unscaled,/usr/share/fonts/Type1,/usr/share/fonts/URW,/usr/share/fonts/Speedo,/usr/share/fonts/truetype,/usr/share/fonts/uni,/usr/share/fonts/CID -noreset
mooreb   17888  0.0  0.0   5524   808 pts/61   S+   13:58   0:00 grep vnc
```

In this case we see I have an existing vnc session, on screen `:56`, or
in other words port 5956. VNC ports start at 5900 and go up, with the
last two digits in the port number corresponding the the VNC screen
number.

Starting a persisent VNC session
--------------------------------

At the terminal prompt, just type `vncserver`. If it is your first time
creating a VNC session, you will be prompted to create a VNC password.
Note: the default screen resolution of a VNC session is quite low and
fixed. If you want to create a session with a higher resolution you can
use the `-geometry` switch.

``` {.console}
mooreb@kojack:~> vncserver -geometry 1280x800

Warning: kojack:9 is taken because of /tmp/.X11-unix/X9
Remove this file if there is no X server kojack:9

Warning: kojack:17 is taken because of /tmp/.X17-lock
Remove this file if there is no X server kojack:17

New 'X' desktop is kojack:56

Starting applications specified in /home/mooreb/.vnc/xstartup
Log file is /home/mooreb/.vnc/kojack:56.log
```

PuTTY settings to tunnel VNC port
=================================

Open a PuTTY session and make a connection to `kojack`.

In the open PuTTY window, you can right click on the window bar at the
top to bring up the menu. Navagate to the *Change Settings* menu. Click
the *Tunnels* option under the *SSH* category.

![image](putty_change_settings){width="3.0in"}![image](putty_tunnels_settings){width="3.0in"}

In the *Source Port* field enter the local port. In this example we will
use 5956, to match the remote port. In the *Destination* field, enter
`kojack.jacks.local:5956` (for this example–use own VNC port here.

Click the *Add* button and then click *Apply*.

![image](putty_tunnels_with_entries){width="5.0in"}

VNC session to tunneled port
============================

Open the VNC viewer program. In the server field, now put
`localhost:5956` (use your own VNC port in place of the example).

![image](vnc_localhost){width="3.0in"}

This should then connect you to your VNC session on `kojack`. You will
be prompted for your VNC password.

Saving the session with the tunnel configuration
================================================

If you want to have the tunnel run every time you start PuTTY, you can
go to the *Session* section and save the session, giving it a new name,
before you quit your PuTTY session.
