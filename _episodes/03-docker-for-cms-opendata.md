---
title: "Using Docker with the CMS open data"
teaching: Self-guided
exercises: 40 
questions:
- "How do I use docker to effectively interface with the CMS open data?"
- "What container images are available for my work with the CMS open data?"
objectives:
- "Download the CMSSW open data docker image"
- "Open your own CMSSW open data container and check that graphical windows open"
- "Download the ROOT and python images and build your own container"
- "Restart an existing container"
- "Copy files into or out of the container"
- "Delete and rebuild containers"
keypoints:
- "You have now set up a docker container as a working enviroment for CMS open data. You know how to open a graphical window in it and how to pass files between your own computer and the container."

---

## Overview

This exercise will walk you through setting up and familiarizing yourself with Docker, so that
you can effectively use it to interface with the CMS open data. It is *not* meant to completely
cover containers and everything you can do with Docker. <!--- Reach out to the organizers
using the [dedicated Mattermost channel][mattermost]
if we are missing something. -->

Three types of [container images](https://gitlab.cern.ch/cms-cloud/cmssw-docker-opendata/-/blob/master/README.md) are provided: one with the CMS software (CMSSW) compatible with the released data, and two others with [ROOT and python](https://cms-opendata-workshop.github.io/workshop2022-lesson-cpp-root-python/) libraries needed in this tutorial. The CMSSW container is mandatory if you want to access the CMS data in AOD and MiniAOD formats (you will learn about them later), as you will not be able to install CMSSW software on your own computer. The two others are provided to make setting up and using ROOT and/or python libraries easier for you for this tutorial, but if you wish, you can also install them on your computer. All container images come with [VNC](https://gitlab.cern.ch/cms-cloud/cmssw-docker-opendata/-/blob/master/README.md#use-vnc) for the graphical use interface. To connect to the VNC server of the container, you will need a VNC viewer (VNC viewer (TigerVNC, RealVNC, TightVNC, OSX built-in VNC viewer, etc.) installed on your local machine. 

For different CMSSW container images, some guidance can be found on the
[Open Data Portal introduction to Docker](http://opendata.cern.ch/docs/cms-guide-docker). In this tutorial, we will use the container image needed for the CMS open data from 2015. The use of graphical interfaces, such the graphics window from ROOT, depends on the operating system of your computer. Therefore, in the following, separate instructions are given for Windows WSL, Linux and MacOS.


## Download the docker image for CMSSW open data and start a container

The first time you start a container, a docker image file gets downloaded from an image registry. The CMSSW open data image is large (6.6GB) and it may take very long to
download, depending on the speed of your internet
connection. After the download, a container created from that image starts. The image download needs to be done only once. Afterwards, when starting a container, it will find the downloaded image on your computer, and it will be much faster.

The containers do not have editors and it is expected that you mount your working directory from the local computer to the container, and use your normal editor for editing the files. Note that all your compiling and executing still has to be done *in the Docker container*!

First, before you start up your container, create a local directory
where you will be doing your code development. In the example below, it is called
`cms_open_data_work` and it will live in the `$HOME` directory. You may choose a different location and a shorter directory name if you like. :)

> ## Local machine
> ~~~
> cd # This is to make sure I'm in my home directory
> mkdir cms_open_data_work
> ~~~
> {: .language-bash}
{: .challenge}

> ## Warning!
> If you do not create the directory on your local computer before creating the container, the directory is created automatically but with the wrong user/group. When starting the container, you will get a message `cannot make directory CMSSW_7_6_7 Permission denied`. In that case, delete the directory with `rm -rf cms_open_data_work/`, and remove the failing container with `docker rm <container-name>` so that you can use the same name. In the following, we will use `my_od` as the container name.
{: .callout}

Start the container following the instructions below depending on the operating system you are using.

<div id="docker-run">

    <div>
        <ul class="nav nav-tabs" role="tablist">
        <li role="presentation" class="active"><a data-os="linux" href="#shell-linux" aria-controls="Linux" role="tab" data-toggle="tab">Linux</a></li>
        <li role="presentation"><a data-os="windows" href="#shell-windows" aria-controls="Windows" role="tab" data-toggle="tab">Windows WSL2</a></li>
        <li role="presentation"><a data-os="macos" href="#shell-macos" aria-controls="MacOS" role="tab" data-toggle="tab">MacOS</a></li>
        </ul>

        <div class="tab-content">

            <article role="tabpanel" class="tab-pane active" id="shell-linux">

<p>We will use the <code class="language-plaintext highlighter-rouge">docker run</code> command to create the container (downloading the appropriate image if it is the first time) and start it right away.</p>

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>docker run -it --name my_od --net=host --env="DISPLAY" -v $HOME/.Xauthority:/home/cmsusr/.Xauthority:rw  -v ${HOME}/cms_open_data_work:/home/cmsusr cmsopendata/cmssw_7_6_7-slc6_amd64_gcc493 /bin/bash
</code></pre></div></div>
                       
<div class="language-plaintext output highlighter-rouge"><div class="highlight"><pre class="highlight"><code>Setting up CMSSW_7_6_7
CMSSW should now be available.
This is a standalone image for CMSSW_7_6_7 slc6_amd64_gcc493.
bash-4.1$
</code></pre></div></div>

<p>This is now a bash shell in the CMS open data environment in which you have access to a complete CMS software release that is appropriate for interfacing with the 2015 13 TeV datasets.</p>
              
<p>As there are rate limits for pulls from Docker Hub, you may get the following error message: <code class="language-plaintext highlighter-rouge">docker: Error response from daemon: toomanyrequests: You have reached your pull rate limit. You may increase the limit by authenticating and upgrading.</code>. In that case, try later (the limit is per 6 hours) or use the mirror <code class="language-plaintext highlighter-rouge">gitlab-registry.cern.ch/cms-cloud/cmssw-docker-opendata/cmssw_7_6_7-slc6_amd64_gcc493</code> instead of <code class="language-plaintext highlighter-rouge">cmsopendata/cmssw_7_6_7-slc6_amd64_gcc493</code>.</p>

<p>Now let’s understand the options that were used for the <code class="language-plaintext highlighter-rouge">docker run</code> command.</p>

<ul>
  <li>First, the <code class="language-plaintext highlighter-rouge">-it</code> (or <code class="language-plaintext highlighter-rouge">-i</code>) option means to start the container in interactive mode. Essentially, it means that you will end up inside the running container.</li>
  <li>We assign a name to the container using the <code class="language-plaintext highlighter-rouge">--name</code> switch, so that we can refer back to this environment and still access any files we created in there. You can, of course, choose a different name than <code class="language-plaintext highlighter-rouge">my_od</code>.</li>
  <li>The <code class="language-plaintext highlighter-rouge">--net=host</code> switch will allow you to use the host network (Internet access) in the container.</li>
  <li>The <code class="language-plaintext highlighter-rouge">--env</code> switch will forward the appropiate <code class="language-plaintext highlighter-rouge">DISPLAY</code> environmental variable from the host machine to the container so X11-forwarding (the ability to open graphical windows inside the container) can be achieved.</li>
  <li>For X11-forwarding to be functional, your local <code class="language-plaintext highlighter-rouge">$HOME/.Xauthority</code> file needs to be mounted as the <code class="language-plaintext highlighter-rouge">/home/cmsusr/.Xauthority</code> file inside the container.  We do this using the <code class="language-plaintext highlighter-rouge">--volume</code> (or <code class="language-plaintext highlighter-rouge">-v</code>) switch. Note that the colon (<code class="language-plaintext highlighter-rouge">:</code>) symbol separates the source and destination points for the mounting procedure. In addition, the <code class="language-plaintext highlighter-rouge">rw</code> tag is given (aslo separated by <code class="language-plaintext highlighter-rouge">:</code>) so it can be read and written if necessary. </li>
  <li>With <code class="language-plaintext highlighter-rouge">-v ${HOME}/cms_open_data_work:/home/cmsusr</code>, the working directory <code class="language-plaintext highlighter-rouge">cms_open_data_work</code> that you created in your home directory is mounted with the <code class="language-plaintext highlighter-rouge">-v</code> option into the container's <code class="language-plaintext highlighter-rouge">/home/cmsusr</code> directory. This makes it possible to edit files in the CMSSW area of your container with your normal editor on your local computer.</li>
  <li><code class="language-plaintext highlighter-rouge">cmsopendata/cmssw_7_6_7-slc6_amd64_gcc493</code> is the name of the image we will use. If no label is prepended, Docker assumes that it resides in <a href="https://hub.docker.com/">Docker Hub</a>, the official image repository of Docker.</li>
  <li>Finally, the <code class="language-plaintext highlighter-rouge">/bin/bash</code> option will throw the container into a <code class="language-plaintext highlighter-rouge">bash</code> shell when running interactively.</li>
</ul>

<p>For a more complete listing of options, see <a href="https://docs.docker.com/engine/reference/commandline/container_run/">the official Docker documentation</a> on the <code class="language-plaintext highlighter-rouge">docker run</code> command.</p>

<p>To test that X11-forwarding works, start the ROOT program by typing <code class="language-plaintext highlighter-rouge">root</code> in the container prompt. In ROOT prompts , type <code class="language-plaintext highlighter-rouge">TBrowser t</code> to open the ROOT graphical window. If the graphical window opens you are all set and you can exit from ROOT either by choosing the option from the TBrowser window or by typing <code class="language-plaintext highlighter-rouge">.q</code> in the ROOT prompt.</p>

<p>Make sure that you can copy instructions from a browser page to the container terminal. One thing you can try is <code class="language-plaintext highlighter-rouge">Shift+Ctrl+V</code> when pasting into your container terminal, rather than <code class="language-plaintext highlighter-rouge">Ctrl-V</code>. That sometimes will work. If not, you will see later in these instructions how to pass files from your local computer to the container.</p>

<p>Then type <code class="language-plaintext highlighter-rouge">exit</code> to leave the container.</p>

<p>If you find that X11 forwarding is not working and the ROOT graphical window does not open, try typing the following before starting your Docker container.</p>
<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>xhost <span class="nb">local</span>:root
</code></pre></div></div>

<p>If everything works fine, you are ready to continue with the lesson.</p>

<blockquote class="solution">
  <h2 id="if-you-still-have-problems-with-x11-forwarding">If you still have problems with X11 forwarding</h2>

  <p><strong>In the case you are having problems with X11 forwarding</strong>, there is the option of using a VNC application installed in the container image:</p>

  <div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>docker run -it --name my_od -P -p 5901:5901  -p 6080:6080 -v ${HOME}/cms_open_data_work:/home/cmsusr cmsopendata/cmssw_7_6_7-slc6_amd64_gcc493:latest /bin/bash
</code></pre></div>  </div>
  
  <p>This container has a VNC application installed to allow opening graphical windows on a remote machine (seen from the container, your own computer is a remote machine). When you use the container for the first time do the following:</p>

  <div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>bash-4.1 <span class="nv">$ </span>echo "source /usr/local/vnc_utils.sh" >> /home/cmsusr/.bashrc
bash-4.1 <span class="nv">$ </span>source /usr/local/vnc_utils.sh
</code></pre></div></div>

  <p>Then, start the application with <code class="language-plaintext highlighter-rouge">start_vnc</code> from your container prompt, and choose a password. You will need to start it every time you use the container (if you want to open graphics windows), but you will define the password only at the first time.</p>

  <div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>bash-4.1 <span class="nv">$ </span>start_vnc
</code></pre></div></div>

  <div class="language-plaintext output highlighter-rouge"><div class="highlight"><pre class="highlight"><code>You will require a password to access your desktops.

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
</code></pre></div>  </div>

  <p>When you do this the first time, download a VNC viewer to your local machine from, e.g., <a href="https://sourceforge.net/projects/tigervnc/files/stable/1.11.0/">TigerVNC</a>. You can then access the GUI in TigerVNC Viewer with the address given in the startup message with the the password you’ve chosen. It opens with an xterminal of your container. To test, start ROOT by typing <code class="language-plaintext highlighter-rouge">root</code> in the container terminal prompt. In the ROOT prompt, type <code class="language-plaintext highlighter-rouge">TBrowser t</code> to open the ROOT graphical window. If the graphical window opens you are all set and you can exit from ROOT either by choosing the “Quit Root” option from Browser menu of the TBrowser window or by typing <code class="language-plaintext highlighter-rouge">.q</code> in the ROOT prompt.</p>

  <p>You can copy from the VNC Viewer terminal by selecting with the mouse, and paste to it by a middle mouse button click. If you are using a touchpad, you may need to define “middle mouse button” in Settings -&gt; Devices -&gt; Touchpad. You can set it to a three-finger tap in “Taps” menu under “Three finger gestures”, or to another selection of your choice.</p>

  <p>Importantly, stop the VNC server before exiting the container. If you don’t do it, you will not be able to open the graphics window next time you use the same container. Do the following:</p>

  <div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>bash-4.1 <span class="nv">$ </span><span class="nb">stop_vnc</span>
bash-4.1 <span class="nv">$ </span><span class="nb">exit</span>
</code></pre></div>  </div>
</blockquote>

            </article><!-- linux  -->

            <article role="tabpanel" class="tab-pane" id="shell-windows">

<p>We will use the <code class="language-plaintext highlighter-rouge">docker run</code> command to create the container (downloading the appropriate image if it is the first time) and start it right away.</p>

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>docker run -it --name my_od -P -p 5901:5901 -p 6080:6080 -v ${HOME}/cms_open_data_work:/home/cmsusr cmsopendata/cmssw_7_6_7-slc6_amd64_gcc493 /bin/bash
</code></pre></div></div>

<div class="language-plaintext output highlighter-rouge"><div class="highlight"><pre class="highlight"><code>Setting up CMSSW_7_6_7
CMSSW should now be available.
This is a standalone image for CMSSW_7_6_7 slc6_amd64_gcc493.
bash-4.1$
</code></pre></div></div>

<p>This is now a bash shell in the CMS open data environment in which you have access to a complete CMS software release that is appropriate for interfacing with the 2015 13 TeV datasets.</p>
              
<p>As there are rate limits for pulls from Docker Hub, you may get the following error message: <code class="language-plaintext highlighter-rouge">docker: Error response from daemon: toomanyrequests: You have reached your pull rate limit. You may increase the limit by authenticating and upgrading.</code>. In that case, try later (the limit is per 6 hours) or use the mirror <code class="language-plaintext highlighter-rouge">gitlab-registry.cern.ch/cms-cloud/cmssw-docker-opendata/cmssw_7_6_7-slc6_amd64_gcc493</code> instead of <code class="language-plaintext highlighter-rouge">cmsopendata/cmssw_7_6_7_vnc</code>.</p>

<p>If the docker command exits without giving you the output above, see <a href="https://opendata-forum.cern.ch/t/running-cms-opendata-containers-in-wsl2/30">this post</a> in the CERN Open Data forum (note in particular that the <code class="language-plaintext highlighter-rouge">.wslconfig</code> file that you need to add must not have a file extension, if Windows adds it automatically, rename the file).</p>

<p>Now let’s understand the options that were used for the <code class="language-plaintext highlighter-rouge">docker run</code> command.</p>

<ul>
  <li>First, the <code class="language-plaintext highlighter-rouge">-it</code> (or <code class="language-plaintext highlighter-rouge">-i</code>) option means to start the container in interactive mode. Essentially, it means that you will end up inside the running container.</li>
  <li>We assign a name to the container using the <code class="language-plaintext highlighter-rouge">--name</code> switch, so that we can refer back to this environment and still access any files we created in there. You can, of course, choose a different name than <code class="language-plaintext highlighter-rouge">my_od</code>.</li>
  <li>The options <code class="language-plaintext highlighter-rouge">-P -p 5901:5901 -p 6080:6080</code> open/publish ports from the container to the local host, needed for the graphical windows</li>
  <li>With <code class="language-plaintext highlighter-rouge">-v ${HOME}/cms_open_data_work:/home/cmsusr</code>, the working directory <code class="language-plaintext highlighter-rouge">cms_open_data_work</code> that you created in your home directory is mounted with the <code class="language-plaintext highlighter-rouge">-v</code> option into the container's <code class="language-plaintext highlighter-rouge">/home/cmsusr</code> directory. This makes it possible to edit files in the CMSSW area of your container with your normal editor on your local computer.</li>
  <li><code class="language-plaintext highlighter-rouge">cmsopendata/cmssw_7_6_7-slc6_amd64_gcc493</code> is the name of the image we will use.  If no label is prepended, Docker assumes that it resides in <a href="https://hub.docker.com/">Docker Hub</a>, the official image repository of Docker.</li>
  <li>Finally, the <code class="language-plaintext highlighter-rouge">/bin/bash</code> option will throw the container into a <code class="language-plaintext highlighter-rouge">bash</code> shell when running interactively.</li>
</ul>

<p>For a more complete listing of options, see <a href="https://docs.docker.com/engine/reference/commandline/container_run/">the official Docker documentation</a> on the <code class="language-plaintext highlighter-rouge">docker run</code> command.</p>

<p>Now, first make sure that you can copy instructions from a browser page to the container terminal. It works in the same manner as the local WSL linux terminal, i.e. you can usually copy from other sources with <code class="language-plaintext highlighter-rouge">Ctrl+C</code> and then paste into your container terminal with mouse right click. Copy from the terminal itself by selecting the text to be copied. If this does not work, you will see later in these instructions how to pass files from your local computer to the container.</p>

<p>This container has a VNC application installed to allow opening graphical windows on a remote machine (seen from the container, your own computer is a remote machine). When you use the container for the first time do the following:</p>

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>bash-4.1 <span class="nv">$ </span>echo "source /usr/local/vnc_utils.sh" >> /home/cmsusr/.bashrc
bash-4.1 <span class="nv">$ </span>source /usr/local/vnc_utils.sh
</code></pre></div></div>

<p>Then, start the application with <code class="language-plaintext highlighter-rouge">start_vnc</code> from your container prompt, and choose a password. You will need to start it every time you use the container (if you want to open graphics windows), but you will define the password only at the first time.</p>

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>bash-4.1 <span class="nv">$ </span>start_vnc
</code></pre></div></div>

<div class="language-plaintext output highlighter-rouge"><div class="highlight"><pre class="highlight"><code>You will require a password to access your desktops.

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
</code></pre></div></div>

<p>When you do this the first time, download a VNC viewer to your local machine from <a href="https://sourceforge.net/projects/tigervnc/files/stable/1.11.0/">TigerVNC</a>. You can then access the GUI in TigerVNC Viewer with the address given in the startup message with the the password you’ve chosen. It opens with an xterminal of your container. If it does not open, it may be that the Windows firewall is blocking it. In that case, check <a href="https://opendata-forum.cern.ch/t/windows-firewall-issue/68">these instructions</a>. To test, start ROOT by typing <code class="language-plaintext highlighter-rouge">root</code> in the container terminal prompt. In the ROOT prompt, type <code class="language-plaintext highlighter-rouge">TBrowser t</code> to open the ROOT graphical window. If the graphical window opens you are all set and you can exit from ROOT either by choosing the “Quit Root” option from Browser menu of the TBrowser window or by typing <code class="language-plaintext highlighter-rouge">.q</code> in the ROOT prompt.</p>

<p>You can copy from the VNC Viewer terminal by selecting with the mouse, and paste to it by a middle mouse button click. If you are using a touchpad, you may need to define “middle mouse button” in Settings -&gt; Devices -&gt; Touchpad. You can set it to a three-finger tap in “Taps” menu under “Three finger gestures”, or to another selection of your choice.</p>

<p>Importantly, stop the VNC server before exiting the container. If you don’t do it, you will not be able to open the graphics window next time you use the same container. Do the following:</p>

  <div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>bash-4.1 <span class="nv">$ </span><span class="nb">stop_vnc</span>
bash-4.1 <span class="nv">$ </span><span class="nb">exit</span>
</code></pre></div></div>

            </article><!-- Windows  -->


            <article role="tabpanel" class="tab-pane" id="shell-macos">

<p>We will use the <code class="language-plaintext highlighter-rouge">docker run</code> command to create the container (downloading the appropriate image if it is the first time) and start it right away.</p>

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>docker run -it --name my_od -P -p 5901:5901 -p 6080:6080 -v ${HOME}/cms_open_data_work:/home/cmsusr cmsopendata/cmssw_7_6_7-slc6_amd64_gcc493 /bin/bash
</code></pre></div></div>

<div class="language-plaintext output highlighter-rouge"><div class="highlight"><pre class="highlight"><code>Setting up CMSSW_7_6_7
CMSSW should now be available.
This is a standalone image for CMSSW_7_6_7 slc6_amd64_gcc493.
bash-4.1$
</code></pre></div></div>

<p>This is now a bash shell in the CMS open data environment in which you have access to a complete CMS software release that is appropriate for interfacing with the 2015 13 TeV datasets.</p>

<p>As there are rate limits for pulls from Docker Hub, you may get the following error message: <code class="language-plaintext highlighter-rouge">docker: Error response from daemon: toomanyrequests: You have reached your pull rate limit. You may increase the limit by authenticating and upgrading.</code>. In that case, try later (the limit is per 6 hours) or use the mirror <code class="language-plaintext highlighter-rouge">gitlab-registry.cern.ch/cms-cloud/cmssw-docker-opendata/cmssw_7_6_7-slc6_amd64_gcc493</code> instead of <code class="language-plaintext highlighter-rouge">cmsopendata/cmssw_7_6_7-slc6_amd64_gcc493</code>.</p>
              
<p>Now let’s understand the options that were used for the <code class="language-plaintext highlighter-rouge">docker run</code> command.</p>

<ul>
  <li>First, the <code class="language-plaintext highlighter-rouge">-it</code> (or <code class="language-plaintext highlighter-rouge">-i</code>) option means to start the container in interactive mode. Essentially, it means that you will end up inside the running container.</li>
  <li>We assign a name to the container using the <code class="language-plaintext highlighter-rouge">--name</code> switch, so that we can refer back to this environment and still access any files we created in there. You can, of course, choose a different name than <code class="language-plaintext highlighter-rouge">my_od</code>.</li>
  <li>The options <code class="language-plaintext highlighter-rouge">-P -p 5901:5901 -p 6080:6080</code> open/publish ports from the container to the local host, needed for the graphical windows</li>
  <li>With <code class="language-plaintext highlighter-rouge">-v ${HOME}/cms_open_data_work:/home/cmsusr</code>, the working directory <code class="language-plaintext highlighter-rouge">cms_open_data_work</code> that you created in your home directory is mounted with the <code class="language-plaintext highlighter-rouge">-v</code> option into the container's <code class="language-plaintext highlighter-rouge">/home/cmsusr</code> directory. This makes it possible to edit files in the CMSSW area of your container with your normal editor on your local computer.</li>
  <li><code class="language-plaintext highlighter-rouge">cmsopendata/cmssw_7_6_7-slc6_amd64_gcc493</code> is the name of the image we will use.  If no label is prepended, Doc<li>With <code class="language-plaintext highlighter-rouge">-v ${HOME}/cms_open_data_work:/home/cmsusr</code>, the working directory <code class="language-plaintext highlighter-rouge">cms_open_data_work</code> that you created in your home directory is mounted with the <code class="language-plaintext highlighter-rouge">-v</code> option into the container's <code class="language-plaintext highlighter-rouge">/home/cmsusr</code> directory. This makes it possible to edit files in the CMSSW area of your container with your normal editor on your local computer.</li>ker assumes that it resides in <a href="https://hub.docker.com/">Docker Hub</a>, the official image repository of Docker.</li>
  <li>Finally, the <code class="language-plaintext highlighter-rouge">/bin/bash</code> option will throw the container into a <code class="language-plaintext highlighter-rouge">bash</code> shell when running interactively.</li>
</ul>

<p>For a more complete listing of options, see <a href="https://docs.docker.com/engine/reference/commandline/container_run/">the official Docker documentation</a> on the <code class="language-plaintext highlighter-rouge">docker run</code> command.</p>

  <p>This container has a VNC application installed to allow opening graphical windows on a remote machine (seen from the container, your own computer is a remote machine). When you use the container for the first time do the following:</p>

  <div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>bash-4.1 <span class="nv">$ </span>echo "source /usr/local/vnc_utils.sh" >> /home/cmsusr/.bashrc
bash-4.1 <span class="nv">$ </span>source /usr/local/vnc_utils.sh
</code></pre></div></div>

  <p>Then, start the application with <code class="language-plaintext highlighter-rouge">start_vnc</code> from your container prompt, and choose a password. You will need to start it every time you use the container (if you want to open graphics windows), but you will define the password only at the first time.</p>

  <div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>bash-4.1 <span class="nv">$ </span>start_vnc
</code></pre></div></div>

<div class="language-plaintext output highlighter-rouge"><div class="highlight"><pre class="highlight"><code>You will require a password to access your desktops.

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
</code></pre></div></div>

<p>You can access the GUI in the Mac VNC viewer. The first time you do this, enter your computer’s “Settings” menu and turn on “screen sharing” from the “Computer Settings” options, then click on “VNC Viewers” and enter the password you chose. Open the VNC viewer from “Finder” by choosing “connect to server” from the “Go” tab. Paste the “MacOS” address given in the container’s VNC startup message and connect. It opens with an xterminal of your container. To test, start ROOT by typing <code class="language-plaintext highlighter-rouge">root</code> in the container terminal prompt. In the ROOT prompt, type <code class="language-plaintext highlighter-rouge">TBrowser t</code> to open the ROOT graphical window. If the graphical window opens you are all set and you can exit from ROOT either by choosing the “Quit Root” option from Browser menu of the TBrowser window or by typing <code class="language-plaintext highlighter-rouge">.q</code> in the ROOT prompt.</p>

<p>Importantly, stop the VNC server before exiting the container. If you don’t do it, you will not be able to open the graphics window next time you use the same container. Do the following:</p>

  <div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>~/CMSSW_7_6_7/src <span class="nv">$ </span><span class="nb">stop_vnc</span>
~/CMSSW_7_6_7/src <span class="nv">$ </span><span class="nb">exit</span>
</code></pre></div></div>

          </article><!-- Mac  -->

        </div> <!-- tab-contents  -->

    </div><!-- nav-tabs  -->
</div><!-- docker-run  -->

## Download the docker images for ROOT and python tools and start container

Containers for ROOT and python libraries are provided for your convenience. These containers can be used in the [C++, ROOT and python tools lesson](https://cms-opendata-workshop.github.io/workshop2022-lesson-cpp-root-python/03-root-and-cpp-read-and-write/index.html).

### ROOT container

ROOT is included in the CMSSW container, but it is an old version because it needs to be compatible with the environment needed to access CMS open data AOD and MiniAOD files. In this tutorial, and in your work with CMS open data, you will often work on data that have been derived from the AOD or MiniAOD files and are not tied to a specific ROOT version. Therefore, a container with more recent ROOT version is provided.

First, create a working directory on your local computer:

~~~
cd
mkdir cms_open_data_root
~~~
{: .language-bash}

Then download the ROOT container image and start the container with

~~~
docker run -it --name my_root -P -p 5901:5901 -p 6080:6080 -v ${HOME}/cms_open_data_root:/home/cmsusr/cms_open_data_root gitlab-registry.cern.ch/cms-cloud/root-vnc:latest
~~~
{: .language-bash}

The container image has VNC installed in a similar way as the CMSSW container and you can start the graphics windows with `vnc_start`.

The default VNC password is `cms.cern`.

### Python tools container

ROOT is not the only option for analysis of CMS open data. A container image is provided with all python libraries that will be needed in this tutorial.

First, create a working directory on your local computer:

~~~
cd
mkdir cms_open_data_python
~~~
{: .language-bash}

Then download the python container image and start the container with

~~~
docker run -it --name my_python -P -p 5901:5901 -p 6080:6080 -v ${HOME}/cms_open_data_python:/home/cmsusr/cms_open_data_python gitlab-registry.cern.ch/cms-cloud/python-vnc:latest
~~~
{: .language-bash}

The container image has VNC installed in a similar way as the CMSSW container and you can start the graphics windows with `vnc_start`.

The default VNC password is `cms.cern`.

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
7719a7d74190        cmsopendata/cmssw_7_6_7   "/opt/cms/entrypoint…"   9 minutes ago       Exited (0) 2 minutes ago                        happy_greider
8939ade0bfac        cmsopendata/cmssw_7_6_7   "/opt/cms/entrypoint…"   16 hours ago        Exited (128) 16 hours ago                       hungry_bhaskara
e914cef3c45a        cmsopendata/cmssw_7_6_7   "/opt/cms/entrypoint…"   6 days ago          Exited (1) 9 minutes ago                        beautiful_tereshkova
b3a888c059f7        cmsopendata/cmssw_7_6_7   "/opt/cms/entrypoint…"   13 days ago         Exited (0) 13 days ago                          affectionate_ardinghelli
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
docker cp my_od:/home/cmsusr/CMSSW_7_6_7/src/test.tmp .
~~~
{: .language-bash}

If you want to copy a file *into* a container instance, it works the way you might expect.
Suppose you have a local file called ```localfile.tmp```. You can copy it into the same instance
as follows.

~~~
docker cp localfile.tmp my_od:/home/cmsusr/CMSSW_7_6_7/src/
~~~
{: .language-bash}


## Stopping and removing containers

As you are learning how to use Docker, you may find yourself with multiple containers. Or maybe
you started a container with your favourite name with some set of flags and now you want use that same name but with new flags. In that case, you will want to stop the container and remove it.

A container stops when you type the ```exit``` command in the container prompt. It may happen that you accidentally close the terminal where the container is running. In that case, the container will not stop and it will remain running. You can list the running containers with ```docker ps```. You can either return to the container using its name (here "my_od") with the ```attach``` command on your local machine and then exit normally from the container prompt:

~~~
docker attach my_od
exit
~~~
{: .language-bash}

or **stop** the container with

~~~
docker stop my_od
~~~
{: .language-bash}

To stop **all** running containers:

~~~
docker stop $(docker ps -q)
~~~
{: .language-bash}

To **remove** the container "my_od", you would type the following. Note that this will delete the container and all files that you may have created in it.

~~~
docker rm my_od
~~~
{: .language-bash}

To remove **all** containers:
~~~
docker rm $(docker ps -aq)
~~~
{: .language-bash}

> ## Don't worry!
> Note that these commands *will not* remove the actual Docker *image* that you downloaded and may have taken
> quite some time to download! Whew!
>
{: .callout}


{% include links.md %}
