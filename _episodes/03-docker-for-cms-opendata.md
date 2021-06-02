---
title: "Using Docker with the CMS open data - NB: update in progess"
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
- "You have now set up a docker container as a working enviroment for CMS open data and you know how to open a graphical window in it and how to pass files between you own computer and the container."

---

## Overview

This exercise will walk you through setting up and familiarizing yourself with Docker, so that
you can effectively use it to interface with the CMS open data. It is *not* meant to completely 
cover containers and everything you can do with Docker, but reach out to the organizers
using the [dedicated Mattermost channel](https://mattermost.web.cern.ch/cmsodws2021/channels/docker-pre-exercise)
if we are missing something. 

Some guidance can be found on the 
[Open Data Portal introduction to Docker](http://opendata.cern.ch/docs/cms-guide-docker). However, the use of graphical interfaces, such the graphics window from ROOT, depends on the operating system of your computer. Therefore, in the following, separate instructions are given for Windows WSL, Linux and MacOS.



## Using the proper image for CMS open data

The first time you run ```docker run``` command with an image name, it starts downloading the image from an image registry. The CMS open data image is large and it may take some time to 
download, even as long as 20-30 minutes, depending on the speed of your internet
connection. When the image download is completed, it will create a container from that image and it will give a ```bash``` shell in which you have access to a complete CMS software release that
is appropriate for interfacing with the 2011 and 2012 7 and 8 TeV datasets. 

Note that the download needs to be done only once. Afterwards, even if you start a new container, it will find the downloaded image on your computer, and it will be much faster. 

Before typing the full command it might be worth breaking it down for the interested user. For a more complete listing of options, see [the official Docker documentation](https://docs.docker.com/engine/reference/commandline/container_run/) on the ```run``` command. 

To start a CMS open data container and open it in a bash shell, one would need only type

~~~
docker run -it <container-name> /bin/bash

~~~
{: .language-bash}

The ```-it``` (or ```-i```) option means to start the container in interactive mode

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
        <li role="presentation"><a data-os="linux" href="#shell-linux" aria-controls="Linux" role="tab" data-toggle="tab">Linux</a></li>
        <li role="presentation" class="active"><a data-os="windows" href="#shell-windows" aria-controls="Windows" role="tab" data-toggle="tab">Windows</a></li>
        <li role="presentation"><a data-os="macos" href="#shell-macos" aria-controls="MacOS" role="tab" data-toggle="tab">MacOS</a></li>
        </ul>

        <div class="tab-content">

            <article role="tabpanel" class="tab-pane" id="shell-linux">

<p>Start the image download and open the container with</p>
<div class="language-plaintext bash highlighter-rouge">
<div class="highlight"><pre class="highlight">
<code>
docker run -it --name my_od --net=host --env="DISPLAY" -v $HOME/.Xauthority:/home/cmsusr/.Xauthority:rw  cmsopendata/cmssw_5_3_32 /bin/bash
</code>
<code>
Setting up CMSSW_5_3_32
CMSSW should now be available.
[21:53:43] cmsusr@docker-desktop ~/CMSSW_5_3_32/src $
</code></pre></div></div>

<p> The following options give us X11-forwarding:</p>

<div class="language-plaintext bash highlighter-rouge">
<div class="highlight"><pre class="highlight">
<code>
... --net=host --env="DISPLAY" --volume="$HOME/.Xauthority:/home/cmsusr/.Xauthority:rw"  ...
</code></pre></div></div>

<p>Start ROOT by typing <code>root</code> in the container prompt. In the ROOT prompt, type <code>TBrowser t</code> to open the ROOT graphical window. Exit from ROOT either by choosing the option from the TBrowser window or by typing <code>.q</code> in the ROOT prompt. Then type <code>exit</code> to leave the container.</p>

<p>If you find that X11 forwarding is _not_ working and the ROOT graphical window does not open, try typing the following before starting your Docker container.

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nb">echo</span> <span class="s2">
xhost local:root
</span>
</code></pre></div></div>




            </article><!-- linux  -->

            <article role="tabpanel" class="tab-pane active" id="shell-windows">

<p>Start the image download and open the container with</p>
<div class="language-plaintext bash highlighter-rouge">
<div class="highlight"><pre class="highlight">
<code>
docker run -it --name cms_od -P -p 5901:5901 -p 6080:6080 cmsopendata/cmssw_5_3_32_vnc:latest /bin/bash
</code>
<code>
Setting up CMSSW_5_3_32
CMSSW should now be available.
[16:42:38] cmsusr@f3d2e685fafc ~/CMSSW_5_3_32/src $
</code></pre></div></div>

<p>If the docker command exits without giving you the output above, 
see <a href="https://opendata-forum.cern.ch/t/running-cms-opendata-containers-in-wsl2/30">this post</a>
in the CERN Open Data forum. Note in particular that the <code>.wslconfig</code> file that you need to add must not have a file extension. If Windows adds it automatically, rename the file.</p>

<p>This container has a VNC application installed to allow opening graphical windows on a remote machine (seen from the container, your own computer is a remote machine). Start the application with <code>start_vnc</code> from your container prompt, and choose a password.
</p>

<div class="language-plaintext bash highlighter-rouge">
<div class="highlight"><pre class="highlight">
<code>
[16:42:38] cmsusr@f3d2e685fafc ~/CMSSW_5_3_32/src $ start_vnc

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

<p>Open the URL given in the startup message in a browser and connect with the password you've chosen. To test, start ROOT by typing <code>root</code> in the container prompt. In the ROOT prompt, type <code>TBrowser t</code> to open the ROOT graphical window. Check that it opens in the VNC tab in your broswer. Exit from ROOT either by choosing the option from the TBrowser window or by typing <code>.q</code> in the ROOT prompt.
</p>

<p> Importantly, take note of the two commands to stop noVNC and kill the vncserver in the startup message, and before exiting the container type them in the container prompt. Then exit the container.
</p>

<div class="language-plaintext bash highlighter-rouge">
<div class="highlight"><pre class="highlight">
<code>
[16:43:41] cmsusr@f3d2e685fafc ~/CMSSW_5_3_32/src $ pkill -9 -P 109
[16:43:50] cmsusr@f3d2e685fafc ~/CMSSW_5_3_32/src $ vncserver -kill :1
Killing Xvnc process ID 101
[1]+  Exit 137                /usr/local/novnc/utils/launch.sh --vnc 127.0.0.1:5901 > /dev/null 2>&1
[16:43:56] cmsusr@f3d2e685fafc ~/CMSSW_5_3_32/src $ exit
exit
</code></pre></div></div>


            </article><!-- Windows  -->


            <article role="tabpanel" class="tab-pane" id="shell-macos">
            
<p>Start the image download and open the container with... just testing the format in the following:</p>

<div class="language-plaintext source highlighter-rouge"><div class="highlight"><pre class="highlight"><code>
Code line
Second line
</code></pre></div></div>

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nb">echo</span> <span class="s2">
Code line with bash
Another code line with bash
</span>
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


### Start/Attach container by container ID

If you did not name your container instance but still want to return to a very specifc
environment, you will need to ```start``` and then ```attach``` to the exact same container as before. 
First of all, you want to see what other Docker processes we have running. To do this, run the following
command
~~~
docker ps -a
~~~
{: .language-bash}

You'll see a list of docker processes that may look something like the following (the exact output
        will vary from user to user).

~~~
CONTAINER ID        IMAGE                      COMMAND                  CREATED             STATUS                      PORTS               NAMES
4f323c317b90        hello-world                "/hello"                 3 minutes ago       Exited (0) 3 minutes ago                        modest_jang
7719a7d74190        cmsopendata/cmssw_5_3_32   "/opt/cms/entrypoint…"   9 minutes ago       Exited (0) 2 minutes ago                        happy_greider
8939ade0bfac        cmsopendata/cmssw_5_3_32   "/opt/cms/entrypoint…"   16 hours ago        Exited (128) 16 hours ago                       hungry_bhaskara
e914cef3c45a        cmsopendata/cmssw_5_3_32   "/opt/cms/entrypoint…"   6 days ago          Exited (1) 9 minutes ago                        beautiful_tereshkova
b3a888c059f7        cmsopendata/cmssw_5_3_32   "/opt/cms/entrypoint…"   13 days ago         Exited (0) 13 days ago                          affectionate_ardinghelli
~~~
{: .output}

You'll want to attach using the ```CONTAINER ID```. In the above example, I know that I've been using the most 
recent CMS open data container, ```7719a7d74190```. So to reattach, I run the following line
which will ```start``` and ```attach``` all in one line. Note that you 
would want to change the ```CONTAINER ID``` for your particular case. 

~~~
docker start -a 7719a7d74190
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
question above to *Test persistence*. In your docker image, there should be a file now 
called ```test.tmp``` Run the following on your *local* machine and *not* in a docker environment. 
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


### Additional flags

Later on in this lesson we will show you two additional arguments to this command, both related to 
mounting local directories on your laptop/desktop 
such that it will be visible in the Docker container. 

One example we will show you will walk you through creating a local working directory for your 
analysis code. This means that you can edit your scripts or files
*locally* and exectute them in Docker. It will give you much greater flexibility in using whatever
backup or version control you are comfortable with. 

As these flags are discussed, we will modify this primary `docker` command in those sections.

### Stopping docker instances

As you are learning how to use Docker, you may find yourself with multiple instances running. Or maybe
you started an instance with your favourite name with some set of flags and now you want to re-start
that same instance but with new flags. In that case, you will want to stop and remove the running
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
> Note that these commands *will not* remove the actual Docker *files* that you downloaded and may have taken
> quite some time to download! Whew!
>
{: .callout}


## Mounting a local volume

Sometimes you may want to mount a filesystem from your local machine or some other remote system
so that your docker image can see it. Let's first see how this is done in a general way. 

The basic usage is 

~~~
docker run -v <path on host>:<path in container> <image>
~~~
{: .language-bash}

Where the `path on host` is the full path to the local file system/directory you want to 
make visible to docker. The `path in container` is where it will be mounted in your
Docker container. 

There are more options and if you want to read more, please visit the 
[official Docker documentation](https://docs.docker.com/storage/volumes/).

When working with the CMS open data, you will find yourself using this approach to have a local working directory for all your editing/version control, etc.
Note that all your compiling and executing still has to be done *in the Docker container*! 
But having your source code also visible on your local laptop/desktop will make things easier for you. 

Let's try this. First, before you start up your Docker image, create a local directory
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
-v ${HOME}/cms_open_data_work:/home/cmsusr:shared
~~~
{: .bash}

Your full `docker` command would then look like this

> ## Local machine
> ~~~
> docker run -it --name myopendataproject --net=host --env="DISPLAY" -v $HOME/.Xauthority:/home/cmsusr/.Xauthority:rw -v ${HOME}/cms_open_data_work:/home/cmsusr/cms_open_data_work:shared cmsopendata/cmssw_5_3_32 /bin/bash
> ~~~
> {: .language-bash}
{: .challenge}

~~~
Setting up CMSSW_5_3_32
CMSSW should now be available.
[21:53:43] cmsusr@docker-desktop ~/CMSSW_5_3_32/src $
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
> If this is an issue, you'll also need to do this in Docker for any new directories you check out
> on your local machine.
{: .callout}


> ## Docker container
> ~~~
> cd /home/cmsusr/CMSSW_5_3_32/src
> sudo chown -R cmsusr.cmsusr ~/cms_open_data_work/
> ln -s ~/cms_open_data_work/
> cd /home/cmsusr/CMSSW_5_3_32/src/cms_open_data_work/
> ~~~
> {: .language-bash}
{: .prereq}

Now, open a new terminal on your local machine (or simply exit out of your container) and check out one of the repositories
you'll be working with. If you are not familiar with git/Github, check out the [Git pre-exercises](https://swcarpentry.github.io/git-novice/). 

> ## Local machine
> ~~~
> cd ~/cms_open_data_work
> git clone https://github.com/katilp/AOD2NanoAODOutreachTool.git AOD2NanoAOD
> ~~~
> {: .language-bash}
{: .challenge}
~~~
Cloning into 'AOD2NanoAOD'...
remote: Enumerating objects: 60, done.
remote: Counting objects: 100% (60/60), done.
remote: Compressing objects: 100% (54/54), done.
remote: Total 343 (delta 29), reused 13 (delta 5), pack-reused 283
Receiving objects: 100% (343/343), 743.11 KiB | 461.00 KiB/s, done.
Resolving deltas: 100% (162/162), done.
~~~
{: .output}

Next, go back into your Docker container (either in your other window or by restarting *that same* container, and see if you can 
see this new directory that you checked out on your local machine. 

> ## Docker container
> ~~~
> cd /home/cmsusr/CMSSW_5_3_32/src/cms_open_data_work
> ls -l
> ~~~
> {: .language-bash}
{: .prereq}
~~~
total 4
drwxr-xr-x 8 cmsinst cmsinst 4096 Sep 26 20:48 AOD2NanoAOD
~~~
{: .output}

Voila! You now have a workflow where you can edit files *locally*, using whatever
tools are on your local machine, and then *exectute* them in the Docker 
environment. 

Let's try compiling and running this new code!
Note that to actually *compile* the code, we want to be in the
`/home/cmsusr/CMSSW_5_3_32/src` directory.

> ## Docker container
> ~~~
> cd /home/cmsusr/CMSSW_5_3_32/src
> sudo chown -R cmsusr.cmsusr cms_open_data_work/AOD2NanoAOD/
> scram b
> ~~~
> {: .language-bash}
{: .prereq}
~~~
Reading cached build data
>> Local Products Rules ..... started
>> Local Products Rules ..... done
>> Building CMSSW version CMSSW_5_3_32 ----
>> Entering Package cms_open_data_work/AOD2NanoAOD
>> Creating project symlinks
Entering library rule at cms_open_data_work/AOD2NanoAOD
>> Compiling edm plugin /home/cmsusr/CMSSW_5_3_32/src/cms_open_data_work/AOD2NanoAOD/src/AOD2NanoAOD.cc 
>> Building edm plugin tmp/slc6_amd64_gcc472/src/cms_open_data_work/AOD2NanoAOD/src/cms_open_data_workAOD2NanoAOD/libcms_open_data_workAOD2NanoAOD.so
Leaving library rule at cms_open_data_work/AOD2NanoAOD
@@@@ Running edmWriteConfigs for cms_open_data_workAOD2NanoAOD
--- Registered EDM Plugin: cms_open_data_workAOD2NanoAOD
>> Leaving Package cms_open_data_work/AOD2NanoAOD
>> Package cms_open_data_work/AOD2NanoAOD built
>> Subsystem cms_open_data_work built
>> Local Products Rules ..... started
>> Local Products Rules ..... done
gmake[1]: Entering directory `/home/cmsusr/CMSSW_5_3_32'
>> Creating project symlinks
>> Done python_symlink
>> Compiling python modules cfipython/slc6_amd64_gcc472
>> Compiling python modules python
>> Compiling python modules src/cms_open_data_work/AOD2NanoAOD/python
>> All python modules compiled
@@@@ Refreshing Plugins:edmPluginRefresh
>> Pluging of all type refreshed.
>> Done generating edm plugin poisoned information
gmake[1]: Leaving directory `/home/cmsusr/CMSSW_5_3_32'
~~~
{: .output}

And now we can run it! The following command may take anywhere from 10-20 minutes to run.

> ## Docker container
> ~~~
> cd /home/cmsusr/CMSSW_5_3_32/src/cms_open_data_work/AOD2NanoAOD/
> cmsRun configs/data_cfg.py
> ~~~
> {: .bash}
{: .prereq}
~~~
200926 22:12:20 802 secgsi_InitProxy: cannot access private key file: /home/cmsusr/.globus/userkey.pem
26-Sep-2020 22:46:14 CEST  Initiating request to open file root://eospublic.cern.ch//eos/opendata/cms/Run2012B/TauPlusX/AOD/22Jan2013-v1/20000/0040CF04-8E74-E211-AD0C-00266CFFA344.root
26-Sep-2020 22:46:17 CEST  Successfully opened file root://eospublic.cern.ch//eos/opendata/cms/Run2012B/TauPlusX/AOD/22Jan2013-v1/20000/0040CF04-8E74-E211-AD0C-00266CFFA344.root
26-Sep-2020 22:51:14 CEST  Closed file root://eospublic.cern.ch//eos/opendata/cms/Run2012B/TauPlusX/AOD/22Jan2013-v1/20000/0040CF04-8E74-E211-AD0C-00266CFFA344.root

=============================================

MessageLogger Summary

 type     category        sev    module        subroutine        count    total
 ---- -------------------- -- ---------------- ----------------  -----    -----
    1 fileAction           -s file_close                             1        1
    2 fileAction           -s file_open                              2        2

 type    category    Examples: run/evt        run/evt          run/evt
 ---- -------------------- ---------------- ---------------- ----------------
    1 fileAction           PostEndRun
    2 fileAction           pre-events       pre-events

Severity    # Occurrences   Total Occurrences
--------    -------------   -----------------
System                  3                   3
~~~
{: .output}




{% include links.md %}

