---
title: "Using Docker with the CMS open data"
teaching: Self-guided
exercises: 40 min
questions:
- "How do I use docker to effectively interface with the CMS open data?"
objectives:
- "Download the CMS open data docker image"
- "Open your own CMS open data container and check that graphical windows open"
- "Restart the same container"
- "Copy files into or out of the container"
- "Delete and rebuild containers"
- "Share a local directory from your computer to the container (pass a volume)"
keypoints:
- "You have now set up a docker container as a working enviroment for CMS open data and you know how to open a graphical window in it and how to pass files between your own computer and the container."

---

## Overview

This exercise will walk you through setting up and familiarizing yourself with Docker, so that
you can effectively use it to interface with the CMS open data. It is *not* meant to completely 
cover containers and everything you can do with Docker, but reach out to the organizers
using the [dedicated Mattermost channel](https://mattermost.web.cern.ch/cmsodws2021/channels/docker-pre-exercise)
if we are missing something. 

Some guidance can be found on the 
[Open Data Portal introduction to Docker](http://opendata.cern.ch/docs/cms-guide-docker). However, the use of graphical interfaces, such the graphics window from ROOT, depends on the operating system of your computer. Therefore, in the following, separate instructions are given for Windows WSL, Linux and MacOS.



## Download the docker image for CMS open data and start a container

The first time you start a container, a docker image file gets downloaded from an image registry. The CMS open data image is large and it may take some time to 
download, even as long as 20-30 minutes, depending on the speed of your internet
connection. After the download, a container created from that image starts. The download needs to be done only once. Afterwards, when starting a container, it will find the downloaded image on your computer, and it will be much faster. 

Before typing the full command it might be worth having a look at the options passed with it. For a more complete listing of options, see [the official Docker documentation](https://docs.docker.com/engine/reference/commandline/container_run/) on the ```run``` command or simply type ````docker run --help```. 

To start a CMS open data container and open it in a bash shell, one would need only type

~~~
docker run -it <container-name> /bin/bash

~~~
{: .language-bash}

The ```-it``` (or ```-i```) option means to start the container in interactive mode.

In the following, we will assign a ```name``` to the container so that we can refer back
to this environment and still access any files we created in there. You can, of course,
choose a different name than ```my_od```! :)

~~~
... --name my_od ...

~~~
{: .language-bash}


Depending on your operating system, we will pass some other options which will be explained below.

<div id="docker-run">

    <div>
        <ul class="nav nav-tabs" role="tablist">
        <li role="presentation" class="active"><a data-os="linux" href="#shell-linux" aria-controls="Linux" role="tab" data-toggle="tab">Linux</a></li>
        <li role="presentation"><a data-os="windows" href="#shell-windows" aria-controls="Windows" role="tab" data-toggle="tab">Windows</a></li>
        <li role="presentation"><a data-os="macos" href="#shell-macos" aria-controls="MacOS" role="tab" data-toggle="tab">MacOS</a></li>
        </ul>

        <div class="tab-content">

            <article role="tabpanel" class="tab-pane active" id="shell-linux">

<p>Start the image download and open the container with</p>
<div class="language-bash highlighter-rouge">
<div class="highlight"><pre class="highlight">
<code>
docker run -it --name my_od --net=host --env="DISPLAY" -v $HOME/.Xauthority:/home/cmsusr/.Xauthority:rw  cmsopendata/cmssw_5_3_32 /bin/bash
</code></pre></div></div>
<div class="language-plaintext output highlighter-rouge"><div class="highlight"><pre class="highlight"><code>
Setting up CMSSW_5_3_32
CMSSW should now be available.
[21:53:43] cmsusr@docker-desktop ~/CMSSW_5_3_32/src $
</code></pre></div></div>

<p>This is now a ```bash``` shell in the CMS open data environment in which you have access to a complete CMS software release that
is appropriate for interfacing with the 2011 and 2012 7 and 8 TeV datasets.</p>

<p> The following options give us X11-forwarding:</p>

<div class="language-bash highlighter-rouge">
<div class="highlight"><pre class="highlight">
<code>
... --net=host --env="DISPLAY" --volume="$HOME/.Xauthority:/home/cmsusr/.Xauthority:rw"  ...
</code></pre></div></div>

<p>To test that X11-forwarding works, start ROOT by typing <code>root</code> in the container prompt. In the ROOT prompt, type <code>TBrowser t</code> to open the ROOT graphical window. If the graphical window opens you are all set and you can exit from ROOT either by choosing the option from the TBrowser window or by typing <code>.q</code> in the ROOT prompt. Then type <code>exit</code> to leave the container.</p>

<p>If you find that X11 forwarding is not working and the ROOT graphical window does not open, try typing the following before starting your Docker container.</p>

<div class="language-bash highlighter-rouge">
<div class="highlight"><pre class="highlight">
<code>
xhost local:root
</code></pre></div></div>




            </article><!-- linux  -->

            <article role="tabpanel" class="tab-pane" id="shell-windows">

<p>Start the image download and open the container with</p>
<div class="language-bash highlighter-rouge">
<div class="highlight"><pre class="highlight">
<code>
docker run -it --name cms_od -P -p 5901:5901 -p 6080:6080 cmsopendata/cmssw_5_3_32_vnc:latest /bin/bash
</code></pre></div></div>
<div class="language-plaintext output highlighter-rouge"><div class="highlight"><pre class="highlight"><code>
Setting up CMSSW_5_3_32
CMSSW should now be available.
[16:42:38] cmsusr@f3d2e685fafc ~/CMSSW_5_3_32/src $
</code></pre></div></div>

<p>This is now a ```bash``` shell in the CMS open data environment in which you have access to a complete CMS software release that
is appropriate for interfacing with the 2011 and 2012 7 and 8 TeV datasets.</p>

<p>If the docker command exits without giving you the output above, 
see <a href="https://opendata-forum.cern.ch/t/running-cms-opendata-containers-in-wsl2/30">this post</a>
in the CERN Open Data forum (note in particular that the <code>.wslconfig</code> file that you need to add must not have a file extension, if Windows adds it automatically, rename the file).</p>

<p> The following options open a port from the container to the local host, needed for the graphical windows:</p>

<div class="language-bash highlighter-rouge">
<div class="highlight"><pre class="highlight">
<code>
... -p 5901:5901 -p 6080:6080  ...
</code></pre></div></div>

<p>This container has a VNC application installed to allow opening graphical windows on a remote machine (seen from the container, your own computer is a remote machine). Start the application with <code>start_vnc</code> from your container prompt, and choose a password.
</p>

<div class="language-bash highlighter-rouge">
<div class="highlight"><pre class="highlight">
<code>
~/CMSSW_5_3_32/src $ start_vnc
</code></pre></div></div>
<div class="language-plaintext output highlighter-rouge"><div class="highlight"><pre class="highlight"><code>
You will require a password to access your desktops.

Password:
Verify:
xauth:  file /home/cmsusr/.Xauthority does not exist

New 'myvnc:1' desktop is f3d2e685fafc:1

Starting applications specified in /home/cmsusr/.vnc/xstartup
Log file is /home/cmsusr/.vnc/f3d2e685fafc:1.log

[1] 109
VNC connection points:
        VNC viewer address: 127.0.0.1:5901
        OSX built-in VNC viewer command: open vnc://127.0.0.1:5901
        Web browser URL: http://127.0.0.1:6080/vnc.html?host=127.0.0.1&port=6080

To stop noVNC enter 'pkill -9 -P 109'
To kill the vncserver enter 'vncserver -kill :1'
</code></pre></div></div>

<p>Open the Web browser URL given in the startup message in a browser and connect with the password you've chosen. To test, start ROOT by typing <code>root</code> in the container prompt. In the ROOT prompt, type <code>TBrowser t</code> to open the ROOT graphical window. Check that it opens in the VNC tab in your broswer. If the graphical window opens you are all set and you can exit from ROOT either by choosing the option from the TBrowser window or by typing <code>.q</code> in the ROOT prompt.
</p>

<p> Importantly, take note of the two commands to stop noVNC and kill the vncserver in the startup message, and before exiting the container type them in the container prompt. Then exit the container.
</p>

<div class="language-bash highlighter-rouge">
<div class="highlight"><pre class="highlight">
<code>
~/CMSSW_5_3_32/src $ pkill -9 -P 109
~/CMSSW_5_3_32/src $ vncserver -kill :1
~/CMSSW_5_3_32/src $ exit
</code></pre></div></div>


            </article><!-- Windows  -->


            <article role="tabpanel" class="tab-pane" id="shell-macos">
            
<p>Start the image download and open the container with... just testing the format in the following:</p>

<div class="language-plaintext source highlighter-rouge"><div class="highlight"><pre class="highlight"><code>
Code line
Second line
</code></pre></div></div>

<div class="language-bash highlighter-rouge">
<div class="highlight"><pre class="highlight">
<code>
Code line with bash
Another code line with bash
</code></pre></div></div>

<div class="language-plaintext output highlighter-rouge"><div class="highlight"><pre class="highlight"><code>
Output line
Second output line
</code></pre></div></div>

            </article><!-- Mac  -->

        </div> <!-- tab-contents  -->

    </div><!-- nav-tabs  -->
</div><!-- docker-run  -->

## Coming back to the same container

 You can come back to the same container you've used earlier with the ```docker start ...``` command. Note that running the ```docker run ...``` command as before would create a new container from the image you've downloaded. This would be a new environment, and any files that you've made or any code that you've written before will not be there! To go to the same working area with all our files and code saved each time you will need to start the existing container.

There are two ways to do this: by giving your container instance a *name* or by making sure you
reference the *container id*. The former approach is probably easier and preferred, but we discuss 
both below. 

### Start container by name

The easiest way to start a container that you want to return to is using the name as defined with the ```--name``` option in the ```docker run ...``` command before. 
Use ```-i``` (or ```-it```) for opening the container in interactive mode.  

So to re-```start``` your container

~~~
docker start -i my_od
~~~
{: .language-bash}


### Start container by container ID or by name assigned by Docker

If you did not name your container, you will need to find the container ID or the container name assigned automatically by docker to return to the exact same container as before. 
First of all, you want to see the list of containers you have locally. To do this, run the following
command
~~~
docker ps -a
~~~
{: .language-bash}

You'll see a list of containers that may look something like the following (the exact output will vary from user to user).

~~~
CONTAINER ID        IMAGE                      COMMAND                  CREATED             STATUS                      PORTS               NAMES
4f323c317b90        hello-world                "/hello"                 3 minutes ago       Exited (0) 3 minutes ago                        modest_jang
7719a7d74190        cmsopendata/cmssw_5_3_32   "/opt/cms/entrypoint…"   9 minutes ago       Exited (0) 2 minutes ago                        happy_greider
8939ade0bfac        cmsopendata/cmssw_5_3_32   "/opt/cms/entrypoint…"   16 hours ago        Exited (128) 16 hours ago                       hungry_bhaskara
e914cef3c45a        cmsopendata/cmssw_5_3_32   "/opt/cms/entrypoint…"   6 days ago          Exited (1) 9 minutes ago                        beautiful_tereshkova
b3a888c059f7        cmsopendata/cmssw_5_3_32   "/opt/cms/entrypoint…"   13 days ago         Exited (0) 13 days ago                          affectionate_ardinghelli
~~~
{: .output}

You can restart a container with the ```CONTAINER ID``` or with the ```NAME```. In the above example, I know that I've been using the most 
recent CMS open data container, with ```7719a7d74190``` as ```CONTAINER ID``` and ```happy_greider``` as ```NAME```. To restart it, I can run one of the following commands. Note that you 
would want to change the ```CONTAINER ID``` or  ```NAME``` for your particular case. 

~~~
docker start -i 7719a7d74190
~~~
{: .language-bash}

or 

~~~
docker start -i happy_greider
~~~
{: .language-bash}


Voila! You should be back in the same container.

> ## CHALLENGE! Test persistence
>
> Go into the container and create a test file using some
> simple shell commands. Type the following exactly as you see it.
> It will dump some text into a file and then print the contents
> of the file to the screen
> ~~~
> echo "I am still here" > test.tmp
> cat test.tmp
> 
> ~~~
> {: .language-bash}
>
> After you've done this, exit out of the container and start it again.
> If you did it correctly, you should be able to list the contents
> of the directory with ```ls -l``` and see your file from before!
> If not, check that you followed all the instructions
> above correctly or contact the facilitators. 
{: .challenge}

## Copy file(s) into or out of a container

Sometimes you will want to copy a file directly into or out of a container. Let's start with copying
a file *out*. 

Suppose you have created your **my_od** container *and* you did the challenge
question above to *Test persistence*. In your container, there should be a file now 
called ```test.tmp``` Run the following on your *local* machine and *not* in the container. 
It should copy the file out and onto your local machine where you can inspect it. 

~~~
docker cp my_od:/home/cmsusr/CMSSW_5_3_32/src/test.tmp .
~~~
{: .language-bash}

If you want to copy a file *into* a container instance, it works the way you might expect. 
Suppose you have a local file called ```localfile.tmp```. You can copy it into the same instance
as follows.

~~~
docker cp localfile.tmp my_od:/home/cmsusr/CMSSW_5_3_32/src/
~~~
{: .language-bash}


## Stopping and deleting containers

As you are learning how to use Docker, you may find yourself with multiple containers. Or maybe
you started a container with your favourite name with some set of flags and now you want use that same name but with new flags. In that case, you will want to stop and remove the running
containers. 

To **stop** all containers you would type the following on your local machine. 

~~~
docker stop $(docker ps -aq)
~~~
{: .bash}

To **remove** all containers, you would type the following on your local machine. 

~~~
docker rm $(docker ps -aq)
~~~
{: .bash}

> ## Don't worry!
> Note that these commands *will not* remove the actual Docker *image* that you downloaded and may have taken
> quite some time to download! Whew!
>
{: .callout}


## Mounting a local volume

Sometimes you may want to mount a filesystem from your local machine or some other remote system
so that your docker container can see it. Let's first see how this is done in a general way. 

The basic usage is 

~~~
docker run -v <path on host>:<path in container> <image>
~~~
{: .language-bash}

Where the `path on host` is the full path to the local file system/directory you want to 
make available in the container. The `path in container` is where it will be mounted in your
Docker container. 

There are more options and if you want to read more, please visit the 
[official Docker documentation](https://docs.docker.com/storage/volumes/).

When working with the CMS open data, you will find yourself using this approach to have a local working directory for all your editing/version control, etc.
Note that all your compiling and executing still has to be done *in the Docker container*! 
But having your source code also visible on your local laptop/desktop will make things easier for you. 

Let's try this. First, before you start up your container, create a local directory
where you will be doing your code development. In the example below, I'm calling it
`cms_open_data_work` and it will live in my `$HOME` directory. You may choose a shorter directory name if you like. :)

> ## Local machine
> ~~~
> cd # This is to make sure I'm in my home directory
> mkdir cms_open_data_work
> ~~~
> {: .bash}
{: .challenge}

Then fire up your Docker container, adding the following
~~~
-v ${HOME}/cms_open_data_work:/home/cmsusr
~~~
{: .bash}

Your full `docker run ...` command would then look like this

<div id="docker-run-with-mount">

    <div>
        <ul class="nav nav-tabs" role="tablist">
        <li role="presentation" class="active"><a data-os="linux" href="#shell-linux" aria-controls="Linux" role="tab" data-toggle="tab">Linux</a></li>
        <li role="presentation"><a data-os="windows" href="#shell-windows" aria-controls="Windows" role="tab" data-toggle="tab">Windows</a></li>
        <li role="presentation"><a data-os="macos" href="#shell-macos" aria-controls="MacOS" role="tab" data-toggle="tab">MacOS</a></li>
        </ul>

        <div class="tab-content">

            <article role="tabpanel" class="tab-pane active" id="shell-linux">

<div class="language-bash highlighter-rouge">
<div class="highlight"><pre class="highlight">
<code>
docker run -it --name my_od --net=host --env="DISPLAY" -v $HOME/.Xauthority:/home/cmsusr/.Xauthority:rw   -v ${HOME}/cms_open_data_work:/home/cmsusr/cms_open_data_work cmsopendata/cmssw_5_3_32 /bin/bash
</code></pre></div></div>

            </article><!-- Linux  -->

            <article role="tabpanel" class="tab-pane" id="shell-windows">

<div class="language-bash highlighter-rouge">
<div class="highlight"><pre class="highlight">
<code>
docker run -it --name cms_od -P -p 5901:5901 -p 6080:6080 -v ${HOME}/cms_open_data_work:/home/cmsusr/cms_open_data_work cmsopendata/cmssw_5_3_32_vnc:latest /bin/bash
</code></pre></div></div>

            </article><!-- Windows  -->


            <article role="tabpanel" class="tab-pane" id="shell-macos">

<p> FIXME: Check this on Mac! </p>
<div class="language-bash highlighter-rouge">
<div class="highlight"><pre class="highlight">
<code>
docker run -it --name my_od --net=host --env="DISPLAY" -v $HOME/.Xauthority:/home/cmsusr/.Xauthority:rw   -v ${HOME}/cms_open_data_work:/home/cmsusr/cms_open_data_work cmsopendata/cmssw_5_3_32 /bin/bash
</code></pre></div></div>

            </article><!-- Mac  -->         

        </div> <!-- tab-contents  -->

    </div><!-- nav-tabs  -->
</div><!-- docker-run-with-mount  -->            


~~~
Setting up CMSSW_5_3_32
CMSSW should now be available.
~/CMSSW_5_3_32/src $
~~~
{: .output}

When your Docker container starts up, it puts you in `/home/cmsusr/CMSSW_5_3_32/src`, but your new mounted directory is `/home/cmsusr/cms_open_data_work`. 
The easiest thing to do is to create a soft link to that directory from inside `/home/cmsusr/CMSSW_5_3_32/src` using `ln -s ...` as shown below,
and then do your work in that directory. 

> ## Warning!
> Sometimes the local volume is mounted in the Docker container as the wrong user/group. It should be
> `cmsusr` but sometimes is mounted as `cmsinst`. Note that in the following set of commands, we add a line
> to change the user/group with the `chown` command. 
>
> If this is an issue, you'll also need to do this in the container for any new directories you check out
> on your local machine.
{: .callout}


> ## Docker container
> ~~~
> cd /home/cmsusr/CMSSW_5_3_32/src
> sudo chown -R cmsusr.cmsusr ~/cms_open_data_work/ # this is only needed if owner is not cmsusr
> ln -s ~/cms_open_data_work/
> cd /home/cmsusr/CMSSW_5_3_32/src/cms_open_data_work/
> ~~~
> {: .language-bash}
{: .prereq}

Now, open a new terminal on your local machine (or simply exit out of your container) and you can use that to check out one of the git repositories
you'll be working with. We will see that in the next section.



{% include links.md %}

