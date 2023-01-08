---
title: "Using Docker with the CMS open data"
teaching: 10
exercises: 40 
questions:
- "What are the container images needed for the open data workshop?"
- "How do I use the container effectively to interface with the CMS open data?"
objectives:
- "Download the container images needed for the introduction to open data"
- "Open your own CMSSW open data container and check that graphical windows open"
keypoints:
- "The CMSSW open data container is mandatory for working with CMS open data"
- "The containers are large and they need to downloaded in advance before the workshop."

---

## Overview

This exercise will walk you through setting up and familiarizing yourself with Docker, so that
you can effectively use it to interface with CMS and other open data and have the containers needed for the open data workshop downloaded in advance. It is *not* meant to completely
cover containers and everything you can do with Docker.

Three types of container images are provided by the CMS open data group: [one with the CMS software (CMSSW)](https://gitlab.cern.ch/cms-cloud/cmssw-docker-opendata/-/blob/master/README.md) compatible with the released data, and two others with [ROOT](https://gitlab.cern.ch/cms-cloud/root-vnc) and [python](https://gitlab.cern.ch/cms-cloud/python-vnc) libraries useful when working with CMS open data. 

The CMSSW container is mandatory for the exercises related to CMS open data, as you will not be able to install CMSSW software on your own computer. The two others are provided to make setting up and using ROOT and/or python libraries easier in context with CMS open data, but they are not needed in the introduction to open data. If you are interested, you can find out how to use them in the CMS open data workshop [docker pre-exercise](https://cms-opendata-workshop.github.io/workshop2022-lesson-docker/03-docker-for-cms-opendata/#download-the-docker-images-for-root-and-python-tools-and-start-container) from which this lesson has been adapted.

These three images come with [VNC](https://gitlab.cern.ch/cms-cloud/cmssw-docker-opendata/-/blob/master/README.md#use-vnc) for the graphical use interface. It opens directly in a browser window. Optionally, you can also connect to the VNC server of the container using a VNC viewer (VNC viewer (TigerVNC, RealVNC, TightVNC, OSX built-in VNC viewer, etc.) installed on your local machine, but only the browser option for which no additional tools are needed is described in these instructions. On native Linux, you can also use X11-forwarding.

> ## Note!
> Note that some container images are large (some GB). Make sure to download them before traveling to the workshop. Test that you can start a CMSSW container from the image so that eventual problems can be solved before the tutorial session.
{: .callout}

## Download the container images needed in the open data introduction

You should download three container images before the workshop: the CMSSW container image for hands-on work with CMS open data, and the [jupyter/datascience-notebook container image](https://hub.docker.com/r/jupyter/datascience-notebook) and a [python container image](https://hub.docker.com/_/python). The compressed download size for these images are 6.6GB, 1.5GB and 47MB, respectively.

In the bash shell, download the CMSSW container image from the docker image registry with

~~~
docker pull cmsopendata/cmssw_7_6_7-slc6_amd64_gcc493
~~~
{: .language-bash}

Download the jupyter/datascience-notebook container image with

~~~
docker pull jupyter/datascience-notebook
~~~
{: .language-bash}

Download the python container image with

~~~
docker pull python:slim-bullseye
~~~
{: .language-bash}

> ## Note!
> The container would start also without doing the `docker pull ...` before. However, if it has not been done, when you start a container, the image file gets downloaded. After the download, a container created from that image starts. The image download needs to be done only once. Afterwards, when starting a container, it will find the downloaded image on your computer, and it will be much faster.
{: .callout}

## Start a CMSSW open data container

This section shows how to start a CMSSW container so that you can easily edit files and open a graphical user interface from it.

The CMS open data containers do not have modern editors and it is expected that you mount your working directory from the local computer into the container, and use your normal editor for editing the files. Note that all your compiling and executing of the CMSSW still has to be done *in the Docker container*!

Before you start up your container, create a local directory
where you will be doing your code development. In the example below, it is called
`cms_open_data_work` and it will live in the `$HOME` directory.
~~~
cd # This is to make sure I'm in my home directory
mkdir cms_open_data_work
~~~
{: .language-bash}

> ## Warning!
> If you do not create the directory on your local computer before creating the container, the directory will be created automatically at the container start but with the wrong user/group. You would get a message
> ~~~
> cannot make directory CMSSW_7_6_7 Permission denied
> ~~~
> {: .error}
> In that case, delete the directory and remove the failing container with `docker rm <container-name>` with
> ~~~
> rm -rf cms_open_data_work/
> docker rm <container-name>
> ~~~
> {: .language-bash}
> so that you can use the same name. In the following, we will use `my_od` as the container name. And then, remember to create the directory before creating the container!
> 
> If the problem persist, try changing the permissions in the local bash shell with
> ~~~
> chmod 777 cms_open_data_work
> ~~~
> {: .language-bash}
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

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>docker run -it --name my_od --net=host --env="DISPLAY" -v $HOME/.Xauthority:/home/cmsusr/.Xauthority:rw  -v ${HOME}/cms_open_data_work:/code cmsopendata/cmssw_7_6_7-slc6_amd64_gcc493 /bin/bash
</code></pre></div></div>
                       
<div class="language-plaintext output highlighter-rouge"><div class="highlight"><pre class="highlight"><code>Setting up CMSSW_7_6_7
CMSSW should now be available.
This is a standalone image for CMSSW_7_6_7 slc6_amd64_gcc493.
(/code/CMSSW_7_6_7/src)
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
  <li>With <code class="language-plaintext highlighter-rouge">-v ${HOME}/cms_open_data_work:/code</code>, the working directory <code class="language-plaintext highlighter-rouge">cms_open_data_work</code> that you created in your home directory is mounted with the <code class="language-plaintext highlighter-rouge">-v</code> option into the container's <code class="language-plaintext highlighter-rouge">/code</code> directory. This makes it possible to edit files in the CMSSW area of your container with your normal editor on your local computer.</li>
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

  <div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>docker run -it --name my_od -P -p 5901:5901  -p 6080:6080 -v ${HOME}/cms_open_data_work:/code cmsopendata/cmssw_7_6_7-slc6_amd64_gcc493:latest /bin/bash
</code></pre></div>  </div>
  
  <p>This container has a VNC application installed to allow opening graphical windows on a remote machine (seen from the container, your own computer is a remote machine). Start the application with <code class="language-plaintext highlighter-rouge">start_vnc</code> from your container prompt. You will need to start it every time you use the container (if you want to open graphics windows).</p>

  <div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>start_vnc
</code></pre></div></div>

  <div class="language-plaintext output highlighter-rouge"><div class="highlight"><pre class="highlight"><code>xauth:  file /home/cmsusr/.Xauthority does not exist

New 'myvnc:1' desktop is 1df549a6f098:1

Starting applications specified in /home/cmsusr/.vnc/xstartup
Log file is /home/cmsusr/.vnc/1df549a6f098:1.log

[1] 144
VNC connection points:
        VNC viewer address: 127.0.0.1:5901
        HTTP access: http://127.0.0.1:6080/vnc.html
To kill the vncserver enter 'vncserver -kill :1'
</code></pre></div>  </div>

  <p>Open the browser window in the http address given at the start message and connect with the default VNC password <code class="language-plaintext highlighter-rouge">cms.cern</code>. It shows an empty screen to start with and all graphics will pop up there.</p> 
  
  <p>To test, start ROOT by typing <code class="language-plaintext highlighter-rouge">root</code> in the container terminal prompt. In the ROOT prompt, type <code class="language-plaintext highlighter-rouge">TBrowser t</code> to open the ROOT graphical window. If the graphical window opens you are all set and you can exit from ROOT either by choosing the “Quit Root” option from Browser menu of the TBrowser window or by typing <code class="language-plaintext highlighter-rouge">.q</code> in the ROOT prompt.</p>

  <p>Importantly, stop the VNC server before exiting the container. If you don’t do it, you will need to do some cleaning before being able to open the graphics window next time you use the same container. Do the following:</p>

  <div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code> <span class="nb">stop_vnc</span>
 <span class="nb">exit</span>
</code></pre></div>  </div>
</blockquote>

            </article><!-- linux  -->

            <article role="tabpanel" class="tab-pane" id="shell-windows">

<p>We will use the <code class="language-plaintext highlighter-rouge">docker run</code> command to create the container (downloading the appropriate image if it is the first time) and start it right away.</p>

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>docker run -it --name my_od -P -p 5901:5901 -p 6080:6080 -v ${HOME}/cms_open_data_work:/code cmsopendata/cmssw_7_6_7-slc6_amd64_gcc493 /bin/bash
</code></pre></div></div>

<div class="language-plaintext output highlighter-rouge"><div class="highlight"><pre class="highlight"><code>Setting up CMSSW_7_6_7
CMSSW should now be available.
This is a standalone image for CMSSW_7_6_7 slc6_amd64_gcc493.
(/code/CMSSW_7_6_7/src)
</code></pre></div></div>

<p>This is now a bash shell in the CMS open data environment in which you have access to a complete CMS software release that is appropriate for interfacing with the 2015 13 TeV datasets.</p>
              
<p>As there are rate limits for pulls from Docker Hub, you may get the following error message: <code class="language-plaintext highlighter-rouge">docker: Error response from daemon: toomanyrequests: You have reached your pull rate limit. You may increase the limit by authenticating and upgrading.</code>. In that case, try later (the limit is per 6 hours) or use the mirror <code class="language-plaintext highlighter-rouge">gitlab-registry.cern.ch/cms-cloud/cmssw-docker-opendata/cmssw_7_6_7-slc6_amd64_gcc493</code> instead of <code class="language-plaintext highlighter-rouge">cmsopendata/cmssw_7_6_7_vnc</code>.</p>

<blockquote class="testimonial">
  <p>If the docker command exits without giving you the output above, see <a href="https://opendata-forum.cern.ch/t/running-cms-opendata-containers-in-wsl2/30">this post</a> in the CERN Open Data forum (note in particular that the <code class="language-plaintext highlighter-rouge">.wslconfig</code> file that you need to add must not have a file extension, if Windows adds it automatically, rename the file).</p>
</blockquote>

<p>Now let’s understand the options that were used for the <code class="language-plaintext highlighter-rouge">docker run</code> command.</p>

<ul>
  <li>First, the <code class="language-plaintext highlighter-rouge">-it</code> (or <code class="language-plaintext highlighter-rouge">-i</code>) option means to start the container in interactive mode. Essentially, it means that you will end up inside the running container.</li>
  <li>We assign a name to the container using the <code class="language-plaintext highlighter-rouge">--name</code> switch, so that we can refer back to this environment and still access any files we created in there. You can, of course, choose a different name than <code class="language-plaintext highlighter-rouge">my_od</code>.</li>
  <li>The options <code class="language-plaintext highlighter-rouge">-P -p 5901:5901 -p 6080:6080</code> open/publish ports from the container to the local host, needed for the graphical windows</li>
  <li>With <code class="language-plaintext highlighter-rouge">-v ${HOME}/cms_open_data_work:/code</code>, the working directory <code class="language-plaintext highlighter-rouge">cms_open_data_work</code> that you created in your home directory is mounted with the <code class="language-plaintext highlighter-rouge">-v</code> option into the container's <code class="language-plaintext highlighter-rouge">/code</code> directory. This makes it possible to edit files in the CMSSW area of your container with your normal editor on your local computer.</li>
  <li><code class="language-plaintext highlighter-rouge">cmsopendata/cmssw_7_6_7-slc6_amd64_gcc493</code> is the name of the image we will use.  If no label is prepended, Docker assumes that it resides in <a href="https://hub.docker.com/">Docker Hub</a>, the official image repository of Docker.</li>
  <li>Finally, the <code class="language-plaintext highlighter-rouge">/bin/bash</code> option will throw the container into a <code class="language-plaintext highlighter-rouge">bash</code> shell when running interactively.</li>
</ul>

<p>For a more complete listing of options, see <a href="https://docs.docker.com/engine/reference/commandline/container_run/">the official Docker documentation</a> on the <code class="language-plaintext highlighter-rouge">docker run</code> command.</p>

<p>Now, first make sure that you can copy instructions from a browser page to the container terminal. It works in the same manner as the local WSL linux terminal, i.e. you can usually copy from other sources with <code class="language-plaintext highlighter-rouge">Ctrl+C</code> and then paste into your container terminal with mouse right click. Copy from the terminal itself by selecting the text to be copied. If this does not work, you will see later in these instructions how to pass files from your local computer to the container.</p>

<p>This container has a VNC application installed to allow opening graphical windows on a remote machine (seen from the container, your own computer is a remote machine). Start the application with <code class="language-plaintext highlighter-rouge">start_vnc</code> from your container prompt. You will need to start it every time you use the container (if you want to open graphics windows).</p>

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code> start_vnc
</code></pre></div></div>

<div class="language-plaintext output highlighter-rouge"><div class="highlight"><pre class="highlight"><code>xauth:  file /home/cmsusr/.Xauthority does not exist

New 'myvnc:1' desktop is 1df549a6f098:1

Starting applications specified in /home/cmsusr/.vnc/xstartup
Log file is /home/cmsusr/.vnc/1df549a6f098:1.log

[1] 144
VNC connection points:
        VNC viewer address: 127.0.0.1:5901
        HTTP access: http://127.0.0.1:6080/vnc.html
To kill the vncserver enter 'vncserver -kill :1'
</code></pre></div></div>

<p>Open the browser window in the http address given at the start message and connect with the default VNC password <code class="language-plaintext highlighter-rouge">cms.cern</code>. It shows an empty screen to start with and all graphics will pop up there. If it does not open, it may be that the Windows firewall is blocking it. In that case, check <a href="https://opendata-forum.cern.ch/t/windows-firewall-issue/68">these instructions</a>.</p>

<p>To test, start ROOT by typing <code class="language-plaintext highlighter-rouge">root</code> in the container terminal prompt. In the ROOT prompt, type <code class="language-plaintext highlighter-rouge">TBrowser t</code> to open the ROOT graphical window. If the graphical window opens you are all set and you can exit from ROOT either by choosing the “Quit Root” option from Browser menu of the TBrowser window or by typing <code class="language-plaintext highlighter-rouge">.q</code> in the ROOT prompt.</p>

<p>Importantly, stop the VNC server before exiting the container. If you don’t do it, you will need to do some cleaning before being able to open the graphics window next time you use the same container. Do the following:</p>

  <div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code> <span class="nb">stop_vnc</span>
 <span class="nb">exit</span>
</code></pre></div></div>

            </article><!-- Windows  -->


            <article role="tabpanel" class="tab-pane" id="shell-macos">

<p>We will use the <code class="language-plaintext highlighter-rouge">docker run</code> command to create the container (downloading the appropriate image if it is the first time) and start it right away.</p>

<div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>docker run -it --name my_od -P -p 5901:5901 -p 6080:6080 -v ${HOME}/cms_open_data_work:/code cmsopendata/cmssw_7_6_7-slc6_amd64_gcc493 /bin/bash
</code></pre></div></div>

<div class="language-plaintext output highlighter-rouge"><div class="highlight"><pre class="highlight"><code>Setting up CMSSW_7_6_7
CMSSW should now be available.
This is a standalone image for CMSSW_7_6_7 slc6_amd64_gcc493.
(/code/CMSSW_7_6_7/src)
</code></pre></div></div>

<p>This is now a bash shell in the CMS open data environment in which you have access to a complete CMS software release that is appropriate for interfacing with the 2015 13 TeV datasets.</p>
              
 <blockquote class="testimonial">
  <p> Problems have been reported running amd-based containers such as this on MacOS with M1 chip. Increasing the memory available to Docker may help. Please check the possible solutions in <a href="https://opendata-forum.cern.ch/t/cms-open-data-docker-test-and-validate-error/111/12">this post</a> in the CERN Open Data forum. Note, however, that this may help you to open the container, but it is very likely that problems remain when you try to compile code and run jobs in it.</p> <p>The other containers used in this workshop should run fine on MacOS with M1 chip.</p></blockquote>

<p>As there are rate limits for pulls from Docker Hub, you may get the following error message: <code class="language-plaintext highlighter-rouge">docker: Error response from daemon: toomanyrequests: You have reached your pull rate limit. You may increase the limit by authenticating and upgrading.</code>. In that case, try later (the limit is per 6 hours) or use the mirror <code class="language-plaintext highlighter-rouge">gitlab-registry.cern.ch/cms-cloud/cmssw-docker-opendata/cmssw_7_6_7-slc6_amd64_gcc493</code> instead of <code class="language-plaintext highlighter-rouge">cmsopendata/cmssw_7_6_7-slc6_amd64_gcc493</code>.</p>
              
<p>Now let’s understand the options that were used for the <code class="language-plaintext highlighter-rouge">docker run</code> command.</p>

<ul>
  <li>First, the <code class="language-plaintext highlighter-rouge">-it</code> (or <code class="language-plaintext highlighter-rouge">-i</code>) option means to start the container in interactive mode. Essentially, it means that you will end up inside the running container.</li>
  <li>We assign a name to the container using the <code class="language-plaintext highlighter-rouge">--name</code> switch, so that we can refer back to this environment and still access any files we created in there. You can, of course, choose a different name than <code class="language-plaintext highlighter-rouge">my_od</code>.</li>
  <li>The options <code class="language-plaintext highlighter-rouge">-P -p 5901:5901 -p 6080:6080</code> open/publish ports from the container to the local host, needed for the graphical windows</li>
  <li>With <code class="language-plaintext highlighter-rouge">-v ${HOME}/cms_open_data_work:/code</code>, the working directory <code class="language-plaintext highlighter-rouge">cms_open_data_work</code> that you created in your home directory is mounted with the <code class="language-plaintext highlighter-rouge">-v</code> option into the container's <code class="language-plaintext highlighter-rouge">/code</code> directory. This makes it possible to edit files in the CMSSW area of your container with your normal editor on your local computer.</li>
  <li><code class="language-plaintext highlighter-rouge">cmsopendata/cmssw_7_6_7-slc6_amd64_gcc493</code> is the name of the image we will use.  If no label is prepended, Doc<li>With <code class="language-plaintext highlighter-rouge">-v ${HOME}/cms_open_data_work:/code</code>, the working directory <code class="language-plaintext highlighter-rouge">cms_open_data_work</code> that you created in your home directory is mounted with the <code class="language-plaintext highlighter-rouge">-v</code> option into the container's <code class="language-plaintext highlighter-rouge">/code</code> directory. This makes it possible to edit files in the CMSSW area of your container with your normal editor on your local computer.</li>ker assumes that it resides in <a href="https://hub.docker.com/">Docker Hub</a>, the official image repository of Docker.</li>
  <li>Finally, the <code class="language-plaintext highlighter-rouge">/bin/bash</code> option will throw the container into a <code class="language-plaintext highlighter-rouge">bash</code> shell when running interactively.</li>
</ul>

<p>For a more complete listing of options, see <a href="https://docs.docker.com/engine/reference/commandline/container_run/">the official Docker documentation</a> on the <code class="language-plaintext highlighter-rouge">docker run</code> command.</p>

<p>This container has a VNC application installed to allow opening graphical windows on a remote machine (seen from the container, your own computer is a remote machine). Start the application with <code class="language-plaintext highlighter-rouge">start_vnc</code> from your container prompt. You will need to start it every time you use the container (if you want to open graphics windows).</p>

  <div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code>start_vnc
</code></pre></div></div>

<div class="language-plaintext output highlighter-rouge"><div class="highlight"><pre class="highlight"><code>xauth:  file /home/cmsusr/.Xauthority does not exist

New 'myvnc:1' desktop is 1df549a6f098:1

Starting applications specified in /home/cmsusr/.vnc/xstartup
Log file is /home/cmsusr/.vnc/1df549a6f098:1.log

[1] 144
VNC connection points:
        VNC viewer address: 127.0.0.1:5901
        HTTP access: http://127.0.0.1:6080/vnc.html
To kill the vncserver enter 'vncserver -kill :1'
</code></pre></div></div>

<p>Open the browser window in the http address given at the start message and connect with the default VNC password <code class="language-plaintext highlighter-rouge">cms.cern</code>. It shows an empty screen to start with and all graphics will pop up there.</p> 

<p>To test, start ROOT by typing <code class="language-plaintext highlighter-rouge">root</code> in the container terminal prompt. In the ROOT prompt, type <code class="language-plaintext highlighter-rouge">TBrowser t</code> to open the ROOT graphical window. If the graphical window opens you are all set and you can exit from ROOT either by choosing the “Quit Root” option from Browser menu of the TBrowser window or by typing <code class="language-plaintext highlighter-rouge">.q</code> in the ROOT prompt.</p>

<p>Importantly, stop the VNC server before exiting the container. If you don’t do it, you will need to do some cleaning before being able to open the graphics window next time you use the same container. Do the following:</p>

  <div class="language-bash highlighter-rouge"><div class="highlight"><pre class="highlight"><code> <span class="nb">stop_vnc</span>
 <span class="nb">exit</span>
</code></pre></div></div>

          </article><!-- Mac  -->

        </div> <!-- tab-contents  -->

    </div><!-- nav-tabs  -->
</div><!-- docker-run  -->


## Coming back to the same container

You can come back to the same container you've used earlier with the ```docker start ...``` command. 
 
~~~
docker start -i my_od
~~~
{: .language-bash}

 Note that running the ```docker run ...``` command as before would create a new container from the image you've downloaded. This would be a new environment. However, as we are mounting the directory from the local computer into the container, you will see the files from your earlier container even in your new container. Most often, you do not want to create a new container but you would use the existing container to go to the same working area with all our files and code saved.


> ## CHALLENGE! Test persistence
>
> Go into the container and in the `/code/CMSSW_7_6_7/src` directory, create a test file using some
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
> After you've done this, check if you see the file `test.tmp` in your local computer in the `cms_open_data_work/CMSSW_7_6_7/src` directory.
> If you did it correctly, you should be able to list the contents
> of the directory with ```ls -l``` and see your file from before!
> If not, check that you followed all the instructions
> above correctly or contact the facilitators.
>
> Now, exit from the container and remove it with
> ~~~
> docker rm my_od
> ~~~
> {: .language-bash}
>
> Create a new container with the `docker run` command that you used in the first place.
> Check if you see the file that you created before.
>
> Note that with the volume mount, your files will not disappear when you remove the container because they are stored in a directory on the local computer. If you really want to get rid of them, you will have to delete them either on the container or on your local computer.
>
> You can make use of this, for example, when you have forgotten to stop VNC with `stop_vnc` when you exit the container. Probably the quickest way to clean is to remove and recreate the container. When you do it, the files that block the VNC from starting will be removed and as they are not located in the mounted directory, they will not be present when you create a new container. But the files in your working area (as `test.tmp` above) will be there again.
{: .challenge}


## Stopping and removing containers

As you are learning how to use Docker, you may find yourself with multiple containers. Or maybe
you started a container with your favourite name with some set of flags and now you want use that same name but with new flags. In that case, you will want to stop the container and remove it.

A container stops when you type the ```exit``` command in the container prompt. It may happen that you accidentally close the terminal where the container is running. In that case, the container will not stop and it will remain running. You can list the running containers with ```docker ps```. You can either return to the container using its name (here "my_od") with the ```start``` command on your local machine and then exit normally from the container prompt:

~~~
docker start -i my_od
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

To **remove** the container "my_od", you would type the following. Note that this will delete the container and all files, but the files in the `CMSSW_7_6_7/src` directory which is shared with your local computer will remain in your local computer's directory.

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
