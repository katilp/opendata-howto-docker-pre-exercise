---
title: Setup
---
> ## Prerequisites
>
> * A computer with an internet connection
> * A computer with Docker installed or the access privileges to install Docker yourself
> * 2-4 GB of hard drive storage (you may need about 16 GB of memory for all the lessons in the workshop)
> * A familiarity with the shell command-line. If you would like an introduction or a refresher,
> check out the [Software Carpentry lesson on the Unix shell](https://swcarpentry.github.io/shell-novice/).
{: .prereq}


> ## Windows users:
> You are expected to have WSL2 (Windows Subsystem Linux 2) installed and work in the Ubuntu Linux shell (not in the Windows command prompt or the git bash shell).
>
> To install WSL2, run
> ~~~
> wsl --install
> ~~~
> {: .source}
as administrator in Windows PowerShell of Command Prompt as [instructed](https://learn.microsoft.com/en-us/windows/wsl/install).
>
> This will install the Ubuntu Linux shell in which you are expected to work during the open data workshop.
> 
> In order to one of the [containers to work on WSL2](https://opendata-forum.cern.ch/t/running-cms-opendata-containers-in-wsl2/30/2), do the following (replacing `<your-windows-username>` with your actual Windows user name) in the Linux Ubuntu shell:
>
> ~~~
> echo [wsl2] > /mnt/c/Users/<your-windows-username>/.wslconfig
> echo kernelCommandLine = vsyscall=emulate >> /mnt/c/Users/<your-windows-username>/.wslconfig
> ~~~
> {: .language-bash}
>
> The shutdown WSL by typing
> ~~~
> wsl --shutdown
> ~~~
> {: .source}
> as administrator in Windows PowerShell of Command Prompt. Restart by opening the Linux Ubuntu shell.
{: .callout}

{% include links.md %}
