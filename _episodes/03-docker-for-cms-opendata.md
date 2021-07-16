---
title: "Using Docker with the CMS open data"
teaching: Self-guided
exercises: 40 
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
- "You have now set up a docker container as a working enviroment for CMS open data. You know how to open a graphical window in it and how to pass files between your own computer and the container."

---

## Overview

This exercise will walk you through setting up and familiarizing yourself with Docker, so that
you can effectively use it to interface with the CMS open data. It is *not* meant to completely
cover containers and everything you can do with Docker. Reach out to the organizers
using the [dedicated Mattermost channel][mattermost]
if we are missing something.

Some guidance can be found on the
[Open Data Portal introduction to Docker](http://opendata.cern.ch/docs/cms-guide-docker). However, the use of graphical interfaces, such the graphics window from ROOT, depends on the operating system of your computer. Therefore, in the following, separate instructions are given for Windows WSL, Linux and MacOS.



## Download the docker image for CMS open data and start a container

The first time you start a container, a docker image file gets downloaded from an image registry. The CMS open data image is large and it may take some time to
download, even as long as 20-30 minutes, depending on the speed of your internet
connection. After the download, a container created from that image starts. The download needs to be done only once. Afterwards, when starting a container, it will find the downloaded image on your computer, and it will be much faster.

Please follow the instructions below, depending on the operating system you are using.

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

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>docker run <span class="nt">-it</span> <span class="nt">--name</span> my_od <span class="nt">--net</span><span class="o">=</span>host <span class="nt">--env</span><span class="o">=</span><span class="s2">"DISPLAY"</span> <span class="nt">-v</span> <span class="nv">$HOME</span>/.Xauthority:/home/cmsusr/.Xauthority:rw  cmsopendata/cmssw_5_3_32:latest /bin/bash
</code></pre></div></div>
                       
<div class="language-plaintext output highlighter-rouge"><div class="highlight"><pre class="highlight"><code>Setting up CMSSW_5_3_32
CMSSW should now be available.
[21:53:43] cmsusr@docker-desktop ~/CMSSW_5_3_32/src $
</code></pre></div></div>

<p>This is now a bash shell in the CMS open data environment in which you have access to a complete CMS software release that is appropriate for interfacing with the 2011 and 2012 7 and 8 TeV datasets.</p>
              
<p>As there are rate limits for pulls from Docker Hub, you may get the following error message: <code class="language-plaintext highlighter-rouge">docker: Error response from daemon: toomanyrequests: You have reached your pull rate limit. You may increase the limit by authenticating and upgrading.</code>. In that case, try later (the limit is per 6 hours) or use the mirror <code class="language-plaintext highlighter-rouge">gitlab-registry.cern.ch/cms-cloud/cmssw-docker-opendata/cmssw_5_3_32</code> instead of <code class="language-plaintext highlighter-rouge">cmsopendata/cmssw_5_3_32</code>.</p>

<p>Now let’s understand the options that were used for the <code class="language-plaintext highlighter-rouge">docker run</code> command.</p>

<ul>
  <li>First, the <code class="language-plaintext highlighter-rouge">-it</code> (or <code class="language-plaintext highlighter-rouge">-i</code>) option means to start the container in interactive mode. Essentially, it means that you will end up inside the running container.</li>
  <li>We assign a name to the container using the <code class="language-plaintext highlighter-rouge">--name</code> switch, so that we can refer back to this environment and still access any files we created in there. You can, of course, choose a different name than <code class="language-plaintext highlighter-rouge">my_od</code>.</li>
  <li>The <code class="language-plaintext highlighter-rouge">--net=host</code> switch will allow you to use the host network (Internet access) in the container.</li>
  <li>The <code class="language-plaintext highlighter-rouge">--env</code> switch will forward the appropiate <code class="language-plaintext highlighter-rouge">DISPLAY</code> environmental variable from the host machine to the container so X11-forwarding (the ability to open graphical windows inside the container) can be achieved.</li>
  <li>For X11-forwarding to be functional, your local <code class="language-plaintext highlighter-rouge">$HOME/.Xauthority</code> file needs to be mounted as the <code class="language-plaintext highlighter-rouge">/home/cmsusr/.Xauthority</code> file inside the container.  We do this using the <code class="language-plaintext highlighter-rouge">--volume</code> (or <code class="language-plaintext highlighter-rouge">-v</code>) switch. Note that the colon (<code class="language-plaintext highlighter-rouge">:</code>) symbol separates the source and destination points for the mounting procedure. In addition, the <code class="language-plaintext highlighter-rouge">rw</code> tag is given (aslo separated by <code class="language-plaintext highlighter-rouge">:</code>) so it can be read and written if necessary. <strong>Optionally</strong>, you could mount any directory from your local machine to the container using the <code class="language-plaintext highlighter-rouge">-v</code> option.  This is sometimes useful; for instance, by adding <code class="language-plaintext highlighter-rouge">-v /home/joe/playground:/playground</code> to the command line, the <code class="language-plaintext highlighter-rouge">playground</code> area can be mounted on the container and serve as a shared area between your local machine and the container. You will check out an example below.</li>
  <li><code class="language-plaintext highlighter-rouge">cmsopendata/cmssw_5_3_32:latest</code> is the name (and <code class="language-plaintext highlighter-rouge">:version</code>) of the image we will use. If no label is prepended, Docker assumes that it resides in <a href="https://hub.docker.com/">Docker Hub</a>, the official image repository of Docker.</li>
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

  <p><strong>Only in the case you are having problems with X11 forwarding</strong>, there is the option to create a container with an image with a VNC application installed <code class="language-plaintext highlighter-rouge">cmsopendata/cmssw_5_3_32_vnc:latest</code>:</p>

  <div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>docker run <span class="nt">-it</span> <span class="nt">--name</span> my_od <span class="nt">-P</span> <span class="nt">-p</span> 5901:5901 cmsopendata/cmssw_5_3_32_vnc:latest /bin/bash
</code></pre></div>  </div>

  <p>This application allows opening graphical windows on a remote machine (seen from the container, your own computer is a remote machine). Start the application with <code class="language-plaintext highlighter-rouge">start_vnc</code> from your container prompt, and choose a password. You will need to start it every time you use the container (if you want to open graphics windows), but you will define the password only at the first time.</p>

  <div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>~/CMSSW_5_3_32/src <span class="nv">$ </span>start_vnc
</code></pre></div>  </div>

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

  <p>Importantly, take note of the command to kill the vncserver in the startup message, and before exiting the container type it in the container prompt. If you don’t do it, you will not be able to open the graphics window next time you use the same container. Then exit the container.</p>

  <div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>~/CMSSW_5_3_32/src <span class="nv">$ </span>vncserver <span class="nt">-kill</span> :1
~/CMSSW_5_3_32/src <span class="nv">$ </span><span class="nb">exit</span>
</code></pre></div>  </div>
</blockquote>

            </article><!-- linux  -->

            <article role="tabpanel" class="tab-pane" id="shell-windows">

<p>Start the image download and open the container with</p>

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>docker run <span class="nt">-it</span> <span class="nt">--name</span> my_od <span class="nt">-P</span> <span class="nt">-p</span> 5901:5901 cmsopendata/cmssw_5_3_32_vnc:latest /bin/bash
</code></pre></div></div>

<div class="language-plaintext output highlighter-rouge"><div class="highlight"><pre class="highlight"><code>Setting up CMSSW_5_3_32
CMSSW should now be available.
~/CMSSW_5_3_32/src $
</code></pre></div></div>

<p>This is now a bash shell in the CMS open data environment in which you have access to a complete CMS software release that is appropriate for interfacing with the 2011 and 2012 7 and 8 TeV datasets.</p>
              
<p>As there are rate limits for pulls from Docker Hub, you may get the following error message: <code class="language-plaintext highlighter-rouge">docker: Error response from daemon: toomanyrequests: You have reached your pull rate limit. You may increase the limit by authenticating and upgrading.</code>. In that case, try later (the limit is per 6 hours) or use the mirror <code class="language-plaintext highlighter-rouge">gitlab-registry.cern.ch/cms-cloud/cmssw-docker-opendata/cmssw_5_3_32_vnc</code> instead of <code class="language-plaintext highlighter-rouge">cmsopendata/cmssw_5_3_32_vnc</code>.</p>

<p>If the docker command exits without giving you the output above, see <a href="https://opendata-forum.cern.ch/t/running-cms-opendata-containers-in-wsl2/30">this post</a> in the CERN Open Data forum (note in particular that the <code class="language-plaintext highlighter-rouge">.wslconfig</code> file that you need to add must not have a file extension, if Windows adds it automatically, rename the file).</p>

<p>Now let’s understand the options that were used for the <code class="language-plaintext highlighter-rouge">docker run</code> command.</p>

<ul>
  <li>First, the <code class="language-plaintext highlighter-rouge">-it</code> (or <code class="language-plaintext highlighter-rouge">-i</code>) option means to start the container in interactive mode. Essentially, it means that you will end up inside the running container.</li>
  <li>We assign a name to the container using the <code class="language-plaintext highlighter-rouge">--name</code> switch, so that we can refer back to this environment and still access any files we created in there. You can, of course, choose a different name than <code class="language-plaintext highlighter-rouge">my_od</code>.</li>
  <li>The options <code class="language-plaintext highlighter-rouge">-P -p 5901:5901</code> open/publish a port from the container to the local host, needed for the graphical windows</li>
  <li><code class="language-plaintext highlighter-rouge">cmsopendata/cmssw_5_3_32_vnc:latest</code> is the name (and <code class="language-plaintext highlighter-rouge">:version</code>) of the image we will use.  If no label is prepended, Docker assumes that it resides in <a href="https://hub.docker.com/">Docker Hub</a>, the official image repository of Docker.</li>
  <li>Finally, the <code class="language-plaintext highlighter-rouge">/bin/bash</code> option will throw the container into a <code class="language-plaintext highlighter-rouge">bash</code> shell when running interactively.</li>
</ul>

<p>For a more complete listing of options, see <a href="https://docs.docker.com/engine/reference/commandline/container_run/">the official Docker documentation</a> on the <code class="language-plaintext highlighter-rouge">docker run</code> command.</p>

<p>Now, first make sure that you can copy instructions from a browser page to the container terminal. It works in the same manner as the local WSL linux terminal, i.e. you can usually copy from other sources with <code class="language-plaintext highlighter-rouge">Ctrl+C</code> and then paste into your container terminal with mouse right click. Copy from the terminal itself by selecting the text to be copied. If this does not work, you will see later in these instructions how to pass files from your local computer to the container.</p>

<p>This container has a VNC application installed to allow opening graphical windows on a remote machine (seen from the container, your own computer is a remote machine). Start the application with <code class="language-plaintext highlighter-rouge">start_vnc</code> from your container prompt, and choose a password. You will need to start it every time you use the container (if you want to open graphics windows), but you will define the password only at the first time.</p>

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>~/CMSSW_5_3_32/src <span class="nv">$ </span>start_vnc
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

<p>Importantly, take note of the command to kill the vncserver in the startup message, and before exiting the container type it in the container prompt. If you don’t do it, you will not be able to open the graphics window next time you use the same container. Then exit the container.</p>

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>~/CMSSW_5_3_32/src <span class="nv">$ </span>vncserver <span class="nt">-kill</span> :1
~/CMSSW_5_3_32/src <span class="nv">$ </span><span class="nb">exit</span>
</code></pre></div></div>

            </article><!-- Windows  -->


            <article role="tabpanel" class="tab-pane" id="shell-macos">

<p>Start the image download and open the container with</p>

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>docker run <span class="nt">-it</span> <span class="nt">--name</span> my_od <span class="nt">-P</span> <span class="nt">-p</span> 5901:5901 cmsopendata/cmssw_5_3_32_vnc:latest /bin/bash
</code></pre></div></div>

<div class="language-plaintext output highlighter-rouge"><div class="highlight"><pre class="highlight"><code>Setting up CMSSW_5_3_32
CMSSW should now be available.
~/CMSSW_5_3_32/src $
</code></pre></div></div>

<p>This is now a bash shell in the CMS open data environment in which you have access to a complete CMS software release that is appropriate for interfacing with the 2011 and 2012 7 and 8 TeV datasets.</p>

<p>As there are rate limits for pulls from Docker Hub, you may get the following error message: <code class="language-plaintext highlighter-rouge">docker: Error response from daemon: toomanyrequests: You have reached your pull rate limit. You may increase the limit by authenticating and upgrading.</code>. In that case, try later (the limit is per 6 hours) or use the mirror <code class="language-plaintext highlighter-rouge">gitlab-registry.cern.ch/cms-cloud/cmssw-docker-opendata/cmssw_5_3_32_vnc</code> instead of <code class="language-plaintext highlighter-rouge">cmsopendata/cmssw_5_3_32_vnc</code>.</p>
              
<p>Now let’s understand the options that were used for the <code class="language-plaintext highlighter-rouge">docker run</code> command.</p>

<ul>
  <li>First, the <code class="language-plaintext highlighter-rouge">-it</code> (or <code class="language-plaintext highlighter-rouge">-i</code>) option means to start the container in interactive mode. Essentially, it means that you will end up inside the running container.</li>
  <li>We assign a name to the container using the <code class="language-plaintext highlighter-rouge">--name</code> switch, so that we can refer back to this environment and still access any files we created in there. You can, of course, choose a different name than <code class="language-plaintext highlighter-rouge">my_od</code>.</li>
  <li>The options <code class="language-plaintext highlighter-rouge">-P -p 5901:5901</code> open/publish a port from the container to the local host, needed for the graphical windows</li>
  <li><code class="language-plaintext highlighter-rouge">cmsopendata/cmssw_5_3_32_vnc:latest</code> is the name (and <code class="language-plaintext highlighter-rouge">:version</code>) of the image we will use.  If no label is prepended, Docker assumes that it resides in <a href="https://hub.docker.com/">Docker Hub</a>, the official image repository of Docker.</li>
  <li>Finally, the <code class="language-plaintext highlighter-rouge">/bin/bash</code> option will throw the container into a <code class="language-plaintext highlighter-rouge">bash</code> shell when running interactively.</li>
</ul>

<p>For a more complete listing of options, see <a href="https://docs.docker.com/engine/reference/commandline/container_run/">the official Docker documentation</a> on the <code class="language-plaintext highlighter-rouge">docker run</code> command.</p>

<p>This container has a VNC application installed to allow opening graphical windows on a remote machine (seen from the container, your own computer is a remote machine). Start the application with <code class="language-plaintext highlighter-rouge">start_vnc</code> from your container prompt, and choose a password. You will need to start it every time you use the container (if you want to open graphics windows), but you will define the password only at the first time.</p>

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>~/CMSSW_5_3_32/src <span class="nv">$ </span>start_vnc
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

<p>Importantly, take note of the command to kill the vncserver in the startup message, and before exiting the container type it in the container prompt. If you don’t do it, you will not be able to open the graphics window next time you use the same container. Then exit the container.</p>

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>~/CMSSW_5_3_32/src <span class="nv">$ </span>vncserver <span class="nt">-kill</span> :1
~/CMSSW_5_3_32/src <span class="nv">$ </span><span class="nb">exit</span>
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


## Mounting a local volume

Sometimes you may want to mount a filesystem from your local machine or some other remote system
so that your docker container can see it. Let's first see how this is done in a general way.

The basic usage is

~~~
docker run -v <path on host>:<path in container> <image>
~~~
{: .bash}

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
> {: .language-bash}
{: .challenge}

Then fire up your Docker container, adding the following
~~~
-v ${HOME}/cms_open_data_work:/home/cmsusr
~~~
{: .bash}

Follow the example below, depending on your operating system.

<div id="docker-run-with-mount">

    <div>
        <ul class="nav nav-tabs" role="tablist">
        <li role="presentation" class="active"><a data-os="linux" href="#shell-linux-mnt" aria-controls="Linux" role="tab" data-toggle="tab">Linux</a></li>
        <li role="presentation"><a data-os="windows" href="#shell-windows-mnt" aria-controls="Windows" role="tab" data-toggle="tab">Windows WSL2</a></li>
        <li role="presentation"><a data-os="macos" href="#shell-macos-mnt" aria-controls="MacOS" role="tab" data-toggle="tab">MacOS</a></li>
        </ul>

        <div class="tab-content">

            <article role="tabpanel" class="tab-pane active" id="shell-linux-mnt">

<p>Your full <code class="language-plaintext highlighter-rouge">docker run ...</code> command would then look like this:</p>

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>docker run <span class="nt">-it</span> <span class="nt">--name</span> my_od <span class="nt">--net</span><span class="o">=</span>host <span class="nt">--env</span><span class="o">=</span><span class="s2">"DISPLAY"</span> <span class="nt">-v</span> <span class="nv">$HOME</span>/.Xauthority:/home/cmsusr/.Xauthority:rw   <span class="nt">-v</span> <span class="k">${</span><span class="nv">HOME</span><span class="k">}</span>/cms_open_data_work:/home/cmsusr/cms_open_data_work cmsopendata/cmssw_5_3_32 /bin/bash
</code></pre></div></div>

            </article><!-- Linux  -->

            <article role="tabpanel" class="tab-pane" id="shell-windows-mnt">

<p>Your full <code class="language-plaintext highlighter-rouge">docker run ...</code> command would then look like this:</p>

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>docker run <span class="nt">-it</span> <span class="nt">--name</span> my_od <span class="nt">-P</span> <span class="nt">-p</span> 5901:5901 <span class="nt">-v</span> <span class="k">${</span><span class="nv">HOME</span><span class="k">}</span>/cms_open_data_work:/home/cmsusr/cms_open_data_work cmsopendata/cmssw_5_3_32_vnc:latest /bin/bash
</code></pre></div></div>

            </article><!-- Windows  -->

            <article role="tabpanel" class="tab-pane" id="shell-macos-mnt">

<p>Your full <code class="language-plaintext highlighter-rouge">docker run ...</code> command would then look like this:</p>

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>docker run <span class="nt">-it</span> <span class="nt">--name</span> my_od <span class="nt">-P</span> <span class="nt">-p</span> 5901:5901 <span class="nt">-v</span> <span class="k">${</span><span class="nv">HOME</span><span class="k">}</span>/cms_open_data_work:/home/cmsusr/cms_open_data_work cmsopendata/cmssw_5_3_32_vnc:latest /bin/bash
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
> sudo chown -R cmsusr.cmsusr ~/cms_open_data_work/ # this is only needed if owner of cms_open_data_work is not cmsusr
> ln -s ~/cms_open_data_work/
> cd /home/cmsusr/CMSSW_5_3_32/src/cms_open_data_work/
> ~~~
> {: .language-bash}
{: .prereq}

Now, open a new terminal on your local machine (or simply exit out of your container) and you can use that to check out the git repositories and editing files
you'll be working with. We will see that in the next section.



{% include links.md %}
