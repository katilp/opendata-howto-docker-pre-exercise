It will be easier to write instructions (and or modifications) for different OS in the standard `md` format here and then just copy the whole, rendered `html` code in the appropriate sections of the `03-docker-for-cms-opendata` episode. This page is not linked from anywhere (it is hidden). The rendered code can be seen with `CTRL+U`.

## Linux instructions

### Downloading and start

We will use the `docker run` command to create the container (downloading the appropriate image if it is the first time) and start it right away.

~~~
docker run -it --name my_od --net=host --env="DISPLAY" -v $HOME/.Xauthority:/home/cmsusr/.Xauthority:rw  cmsopendata/cmssw_5_3_32 /bin/bash
~~~
{: .language-bash}

~~~
Setting up CMSSW_5_3_32
CMSSW should now be available.
[21:53:43] cmsusr@docker-desktop ~/CMSSW_5_3_32/src $
~~~
{: .output}

This is now a bash shell in the CMS open data environment in which you have access to a complete CMS software release that is appropriate for interfacing with the 2011 and 2012 7 and 8 TeV datasets.

Now let's understand the options that were used for the `docker run` command.

* First, the `-it` (or `-i`) option means to start the container in interactive mode. Essentially, it means that you will end up inside the running container.
* We assign a name to the container using the `--name` switch, so that we can refer back to this environment and still access any files we created in there. You can, of course, choose a different name than `my_od`.
* The `--net=host` switch will allow you to use the host network (Internet access) in the container.
* The `--env` switch will forward the appropiate `DISPLAY` environmental variable from the host machine to the container so X11-forwarding (the ability to open graphical windows inside the container) can be achieved.
* For X11-forwarding to be functional, your local `$HOME/.Xauthority` file needs to be mounted as the `/home/cmsusr/.Xauthority` file inside the container.  We do this using the `--volume` (or `-v`) switch. Note that the colon (`:`) symbol separates the source and destination points for the mounting procedure. In addition, the `rw` tag is given (aslo separated by `:`) so it can be read and written if necessary. **Optionally**, you could mount any directory from your local machine to the container using the `-v` option.  This is sometimes useful; for instance, by adding `-v /home/joe/playground:/playground` to the command line, the `playground` area can be mounted on the container and serve as a shared area between your local machine and the container. You will check out an example below.
* `cmsopendata/cmssw_5_3_32` is the name of the image we will use.  If no label is prepended, Docker assumes that it resides in [Docker Hub](https://hub.docker.com/), the official image repository of Docker.
* Finally, the `/bin/bash` option will throw the container into a `bash` shell when running interactively.

For a more complete listing of options, see [the official Docker documentation](https://docs.docker.com/engine/reference/commandline/container_run/) on the `docker run` command.

To test that X11-forwarding works, start the ROOT program by typing `root` in the container prompt. In ROOT prompts , type `TBrowser t` to open the ROOT graphical window. If the graphical window opens you are all set and you can exit from ROOT either by choosing the option from the TBrowser window or by typing `.q` in the ROOT prompt.

Make sure that you can copy instructions from a browser page to the container terminal. One thing you can try is `Shift+Ctrl+V` when pasting into your container terminal, rather than `Ctrl-V`. That sometimes will work. If not, you will see later in these instructions how to pass files from your local computer to the container.

Then type `exit` to leave the container.

If you find that X11 forwarding is not working and the ROOT graphical window does not open, try typing the following before starting your Docker container.
~~~
xhost local:root
~~~
{: .language-bash}

### Mounting a local volume example

Your full `docker run ...` command would then look like this:

~~~
docker run -it --name my_od --net=host --env="DISPLAY" -v $HOME/.Xauthority:/home/cmsusr/.Xauthority:rw   -v ${HOME}/cms_open_data_work:/home/cmsusr/cms_open_data_work cmsopendata/cmssw_5_3_32 /bin/bash
~~~
{: .language-bash}


## Windows instructions

### Downloading and start

Start the image download and open the container with

~~~
docker run -it --name my_od -P -p 5901:5901 cmsopendata/cmssw_5_3_32_vnc:latest /bin/bash
~~~
{: .language-bash}

~~~
Setting up CMSSW_5_3_32
CMSSW should now be available.
~/CMSSW_5_3_32/src $
~~~
{: .output}

This is now a bash shell in the CMS open data environment in which you have access to a complete CMS software release that is appropriate for interfacing with the 2011 and 2012 7 and 8 TeV datasets.

If the docker command exits without giving you the output above, see [this post](https://opendata-forum.cern.ch/t/running-cms-opendata-containers-in-wsl2/30) in the CERN Open Data forum (note in particular that the `.wslconfig` file that you need to add must not have a file extension, if Windows adds it automatically, rename the file).

Now let's understand the options that were used for the `docker run` command.

* First, the `-it` (or `-i`) option means to start the container in interactive mode. Essentially, it means that you will end up inside the running container.
* We assign a name to the container using the `--name` switch, so that we can refer back to this environment and still access any files we created in there. You can, of course, choose a different name than `my_od`.
* The options `-P -p 5901:5901` open/publish a port from the container to the local host, needed for the graphical windows
* `cmsopendata/cmssw_5_3_32_vnc:latest` is the name (and `:version`) of the image we will use.  If no label is prepended, Docker assumes that it resides in [Docker Hub](https://hub.docker.com/), the official image repository of Docker.
* Finally, the `/bin/bash` option will throw the container into a `bash` shell when running interactively.

For a more complete listing of options, see [the official Docker documentation](https://docs.docker.com/engine/reference/commandline/container_run/) on the `docker run` command.

Now, first make sure that you can copy instructions from a browser page to the container terminal. It works in the same manner as the local WSL linux terminal, i.e. you can usually copy from other sources with `Ctrl+C` and then paste into your container terminal with mouse right click. Copy from the terminal itself by selecting the text to be copied. If this does not work, you will see later in these instructions how to pass files from your local computer to the container.

This container has a VNC application installed to allow opening graphical windows on a remote machine (seen from the container, your own computer is a remote machine). Start the application with `start_vnc` from your container prompt, and choose a password. You will need to start it every time you use the container (if you want to open graphics windows), but you will define the password only at the first time.

~~~
~/CMSSW_5_3_32/src $ start_vnc
~~~
{: .language-bash}

~~~
You will require a password to access your desktops.

Password:
Verify:
xauth:  file /home/cmsusr/.Xauthority does not exist

New 'myvnc:1' desktop is e0ca768960bf:1

Starting applications specified in /home/cmsusr/.vnc/xstartup
Log file is /home/cmsusr/.vnc/e0ca768960bf:1.log

VNC connection points:
        VNC viewer address: 127.0.0.1:5901
        OSX built-in VNC viewer command: open vnc://127.0.0.1:5901
To kill the vncserver enter 'vncserver -kill :1'
~~~
{: .output}

When you do this the first time, download a VNC viewer to your local machine from [TigerVNC](https://sourceforge.net/projects/tigervnc/files/stable/1.11.0/). You can then access the GUI in TigerVNC Viewer with the address given in the startup message with the the password you've chosen. It opens with an xterminal of your container. To test, start ROOT by typing `root` in the container terminal prompt. In the ROOT prompt, type `TBrowser t` to open the ROOT graphical window. If the graphical window opens you are all set and you can exit from ROOT either by choosing the "Quit Root" option from Browser menu of the TBrowser window or by typing `.q` in the ROOT prompt.

You can copy from the VNC Viewer terminal by selecting with the mouse, and paste to it by a middle mouse button click. If you are using a touchpad, you may need to define "middle mouse button" in Settings -> Devices -> Touchpad. You can set it to a three-finger tap in "Taps" menu under "Three finger gestures", or to another selection of your choice.

Importantly, take note of the command to kill the vncserver in the startup message, and before exiting the container type it in the container prompt. If you don't do it, you will not be able to open the graphics window next time you use the same container. Then exit the container.

~~~
~/CMSSW_5_3_32/src $ vncserver -kill :1
~/CMSSW_5_3_32/src $ exit
~~~
{: .language-bash}

### Mounting a local volume example

Your full `docker run ...` command would then look like this:

~~~
docker run -it --name my_od -P -p 5901:5901 -v ${HOME}/cms_open_data_work:/home/cmsusr/cms_open_data_work cmsopendata/cmssw_5_3_32_vnc:latest /bin/bash
~~~
{: .language-bash}

## Mac-OS instructions

### Downloading and start

Start the image download and open the container with

~~~
docker run -it --name my_od -P -p 5901:5901 cmsopendata/cmssw_5_3_32_vnc:latest /bin/bash
~~~
{: .language-bash}

~~~
Setting up CMSSW_5_3_32
CMSSW should now be available.
~/CMSSW_5_3_32/src $
~~~
{: .output}

This is now a bash shell in the CMS open data environment in which you have access to a complete CMS software release that is appropriate for interfacing with the 2011 and 2012 7 and 8 TeV datasets.

Now let's understand the options that were used for the `docker run` command.

* First, the `-it` (or `-i`) option means to start the container in interactive mode. Essentially, it means that you will end up inside the running container.
* We assign a name to the container using the `--name` switch, so that we can refer back to this environment and still access any files we created in there. You can, of course, choose a different name than `my_od`.
* The options `-P -p 5901:5901` open/publish a port from the container to the local host, needed for the graphical windows
* `cmsopendata/cmssw_5_3_32_vnc:latest` is the name (and `:version`) of the image we will use.  If no label is prepended, Docker assumes that it resides in [Docker Hub](https://hub.docker.com/), the official image repository of Docker.
* Finally, the `/bin/bash` option will throw the container into a `bash` shell when running interactively.

For a more complete listing of options, see [the official Docker documentation](https://docs.docker.com/engine/reference/commandline/container_run/) on the `docker run` command.

This container has a VNC application installed to allow opening graphical windows on a remote machine (seen from the container, your own computer is a remote machine). Start the application with `start_vnc` from your container prompt, and choose a password. You will need to start it every time you use the container (if you want to open graphics windows), but you will define the password only at the first time.

~~~
~/CMSSW_5_3_32/src $ start_vnc
~~~
{: .language-bash}

~~~
You will require a password to access your desktops.

Password:
Verify:
xauth:  file /home/cmsusr/.Xauthority does not exist

New 'myvnc:1' desktop is e0ca768960bf:1

Starting applications specified in /home/cmsusr/.vnc/xstartup
Log file is /home/cmsusr/.vnc/e0ca768960bf:1.log

VNC connection points:
        VNC viewer address: 127.0.0.1:5901
        OSX built-in VNC viewer command: open vnc://127.0.0.1:5901
        To kill the vncserver enter 'vncserver -kill :1'
~~~
{: .output}

You can access the GUI in the Mac VNC viewer. The first time you do this, enter your computer's "Settings" menu and turn on "screen sharing" from the "Computer Settings" options, then click on "VNC Viewers" and enter the password you chose. Open the VNC viewer from "Finder" by choosing "connect to server" from the "Go" tab. Paste the "MacOS" address given in the container's VNC startup message and connect. It opens with an xterminal of your container. To test, start ROOT by typing `root` in the container terminal prompt. In the ROOT prompt, type `TBrowser t` to open the ROOT graphical window. If the graphical window opens you are all set and you can exit from ROOT either by choosing the "Quit Root" option from Browser menu of the TBrowser window or by typing `.q` in the ROOT prompt.

Importantly, take note of the command to kill the vncserver in the startup message, and before exiting the container type it in the container prompt. If you don't do it, you will not be able to open the graphics window next time you use the same container. Then exit the container.

~~~
~/CMSSW_5_3_32/src $ vncserver -kill :1
~/CMSSW_5_3_32/src $ exit
~~~
{: .language-bash}


### Mounting a local volume example

Check this on Mac!
~~~
docker run -it --name my_od --net=host --env="DISPLAY" -v $HOME/.Xauthority:/home/cmsusr/.Xauthority:rw   -v ${HOME}/cms_open_data_work:/home/cmsusr/cms_open_data_work cmsopendata/cmssw_5_3_32 /bin/bash
~~~
{: .language-bash}
