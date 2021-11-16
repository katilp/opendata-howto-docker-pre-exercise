---
title: "Test and validate"
teaching: 10
exercises: 30
questions:
- "What is in the CMS Docker image?"
- "How do I test and validate my Docker container?"
objectives:
- "Learn about the details of the CMS Docker container"
- "Test and validate the CMS Docker image by running a CMSSW job."
keypoints:
- "The CMS Docker image contains all the required ingredients to start analyzing CMS open data."
- "In order to test and validate the Docker container you can run a simple CMSSW job."  
---
> ## Helpline
>
> Remember that we are always available to help.  Our [Mattermost](https://mattermost.web.cern.ch/cmsodws2021/channels/docker-pre-exercise) channel is open.
{: .callout}

## Know your Docker image

The Docker container we just created provides CMS computing environment to be used with the 2015 CMS open data. The Docker container uses Scientific Linux CERN.  As it was mentioned before, it comes equipped with the [ROOT](http://root.cern.ch/) framework and the version of [CMS Software - CMSSW](http://cms-sw.github.io/) compatible with the CMS open data.

Access to the data is through the [XRootD](https://xrootd.slac.stanford.edu/) protocol.

## Run a simple *demo* for testing and validating

The validation procedure tests that the CMS environment is installed and operational on your Docker container, and that you have access to the CMS Open Data files. These steps also give you a quick introduction to the CMS environment.

Verify first that you are in ```~/CMSSW_7_6_7/src``` directory. You can see that in the container prompt.

Now, you could run the following command to create the CMS runtime variables (in the Docker container these variables are already set when you start the container, however, it will not hurt to issue this command again):

~~~
cmsenv
~~~

> ## Work assignment
>
> This is a good moment to go to our [assignment form](https://forms.gle/DDboG1MCcSNRBRHFA) and answer some simple questions for this pre-exercise; you must sign in and <strong style="color: red;">click on the submit button</strong> in order to save your work.  You can go back to edit the form at any time.
{: .challenge}

Create a working directory for the demo analyzer, change to that directory and create a *skeleton* for the analyzer:

~~~
mkdir Demo
cd Demo
mkedanlzr DemoAnalyzer
~~~
{: .language-bash}

Go back to the main `src` area and compile the code:

~~~
cd ..
scram b
~~~
{: .language-bash}

You can safely ignore the warning.

Before launching the job, let's modify the configuration file (do not worry, you will learn about all this stuff in a different [lesson](https://cms-opendata-workshop.github.io/workshop2021-lesson-cmssw/)) so it is able access a CMS open data file.

Open the `demoanalyzer_cfg.py` file using the `nano` editor. 

> Our container comes equiped with legacy software repositories.  Note that you could install a different editor (or any other available program) in the container by issuing, for instance, `sudo yum update` and then `sudo yum install emacs`.
{: .testimonial}

If you're an absolute command line editor hater, you can also copy the file to the shared volume ```~/cms_open_data_work``` (if you created one before) and edit it in your local computer and copy it back again to ```DemoAnalyzer/demoanalyzer_cfg.py```

~~~
nano Demo/DemoAnalyzer/demoanalyzer_cfg.py
~~~
{: .language-bash}

Replace `file:myfile.root` with `'root://eospublic.cern.ch//eos/opendata/cms/Run2012B/DoubleMuParked/AOD/22Jan2013-v1/10000/1EC938EF-ABEC-E211-94E0-90E6BA442F24.root'` to point to an example file.

Chage also the maximum number of events to 10.  I.e., change `-1`to `10` in `process.maxEvents = cms.untracked.PSet( input = cms.untracked.int32(-1))`.

> ## Take a look at the final validation config file
>
> At the end, the config file should look like
>
> ~~~
> import FWCore.ParameterSet.Config as cms
> process = cms.Process("Demo")
> process.load("FWCore.MessageService.MessageLogger_cfi")
> process.maxEvents = cms.untracked.PSet( input = cms.untracked.int32(10) )
> process.source = cms.Source("PoolSource",
> # replace 'myfile.root' with the source file you want to use
>    fileNames = cms.untracked.vstring(
>        'root://eospublic.cern.ch//eos/opendata/cms/Run2012B/DoubleMuParked/AOD/22Jan2013-v1/10000/1EC938EF-ABEC-E211-94E0-90E6BA442F24.root'
>    )
> )
>
> process.demo = cms.EDAnalyzer('DemoAnalyzer'
> )
>
> process.p = cms.Path(process.demo)
> ~~~
> {: .language-python}
{: .solution}


Finally, run the cms executable with our configuration:
~~~
cmsRun Demo/DemoAnalyzer/demoanalyzer_cfg.py
~~~
{: .language-bash}

~~~
210701 04:51:59 606 secgsi_InitProxy: cannot access private key file: /home/cmsusr/.globus/userkey.pem
01-Jul-2021 04:51:59 CEST  Initiating request to open file root://eospublic.cern.ch//eos/opendata/cms/Run2012B/DoubleMuParked/AOD/22Jan2013-v1/10000/1EC938EF-ABEC-E211-94E0-90E6BA442F24.root
01-Jul-2021 04:52:03 CEST  Successfully opened file root://eospublic.cern.ch//eos/opendata/cms/Run2012B/DoubleMuParked/AOD/22Jan2013-v1/10000/1EC938EF-ABEC-E211-94E0-90E6BA442F24.root
Begin processing the 1st record. Run 195013, Event 24425389, LumiSection 66 at 01-Jul-2021 04:52:14.775 CEST
Begin processing the 2nd record. Run 195013, Event 24546773, LumiSection 66 at 01-Jul-2021 04:52:14.776 CEST
Begin processing the 3rd record. Run 195013, Event 24679037, LumiSection 66 at 01-Jul-2021 04:52:14.776 CEST
Begin processing the 4th record. Run 195013, Event 24839453, LumiSection 66 at 01-Jul-2021 04:52:14.777 CEST
Begin processing the 5th record. Run 195013, Event 24894477, LumiSection 66 at 01-Jul-2021 04:52:14.778 CEST
Begin processing the 6th record. Run 195013, Event 24980717, LumiSection 66 at 01-Jul-2021 04:52:14.778 CEST
Begin processing the 7th record. Run 195013, Event 25112869, LumiSection 66 at 01-Jul-2021 04:52:14.779 CEST
Begin processing the 8th record. Run 195013, Event 25484261, LumiSection 66 at 01-Jul-2021 04:52:14.780 CEST
Begin processing the 9th record. Run 195013, Event 25702821, LumiSection 66 at 01-Jul-2021 04:52:14.780 CEST
Begin processing the 10th record. Run 195013, Event 25961949, LumiSection 66 at 01-Jul-2021 04:52:14.781 CEST
01-Jul-2021 04:52:14 CEST  Closed file root://eospublic.cern.ch//eos/opendata/cms/Run2012B/DoubleMuParked/AOD/22Jan2013-v1/10000/1EC938EF-ABEC-E211-94E0-90E6BA442F24.root

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

Congratulations! You are all set with your Docker environment.
