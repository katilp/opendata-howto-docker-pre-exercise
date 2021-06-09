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

The Docker container we just created provides CMS computing environment to be used with the 2011 and 2012 CMS open data. The Docker container uses Scientific Linux CERN.  As it was mentioned before, it comes equipped with the [ROOT](http://root.cern.ch/) framework and the version of [CMS Software - CMSSW](http://cms-sw.github.io/) compatible with the CMS open data.

Access to the data is through [XRootD](https://xrootd.slac.stanford.edu/).

## Run a simple *demo* for testing and validating

The validation procedure tests that the CMS environment is installed and operational on your Docker container, and that you have access to the CMS Open Data files. The test job also accesses the "conditions data", i.e. non-collision data often needed for analysis from specific servers and caches them.  This last action will save us time during the workshop.  These steps also give you a quick introduction to the CMS environment.

Verify first that you are in ```~/CMSSW_5_3_32/src``` directory. You can see that in the container prompt.

Create a working directory for the demo analyzer, change to that directory and create a *skeleton* for the analyzer:

~~~
mkdir Demo
cd Demo
mkedanlzr DemoAnalyzer
~~~
{: .language-bash}

Compile the code:

~~~
scram b
~~~
{: .language-bash}

You can safely ignore the warning.

Before launching the job, let's modify the configuration file (do not worry, you will learn about all this stuff in a different [lesson](https://cms-opendata-workshop.github.io/workshop-lesson-cmssw/)) so it is able access a CMS open data file and cache the conditions data.  As it was mentioned, this will save us time later.

Open the `demoanalyzer_cfg.py` file using the `vi` editor ([here](https://www.thegeekdiary.com/basic-vi-commands-cheat-sheet/) you can find a good cheatsheet for that editor). If you're an absolute command line editor hater, you can also copy the file to the shared volume ```~/cms_open_data_work``` and edit it in your local computer and copy it back again to ```DemoAnalyzer/demoanalyzer_cfg.py```

~~~
vi DemoAnalyzer/demoanalyzer_cfg.py
~~~
{: .language-bash}

Replace `file:myfile.root` with `root://eospublic.cern.ch//eos/opendata/cms/Run2011A/ElectronHad/AOD/12Oct2013-v1/20001/001F9231-F141-E311-8F76-003048F00942.root` to point to an example file.

Chage also the maximum number of events to 10.  I.e., change `-1`to `10` in `process.maxEvents = cms.untracked.PSet( input = cms.untracked.int32(-1))`.

In addition, insert, below the *PoolSource* module, the following lines:

```
#needed to cache the conditions data
process.load('Configuration.StandardSequences.FrontierConditions_GlobalTag_cff')
process.GlobalTag.globaltag = 'FT_53_LV5_AN1::All'
```

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
>        'root://eospublic.cern.ch//eos/opendata/cms/Run2011A/ElectronHad/AOD/12Oct2013-v1/20001/001F9231-F141-E311-8F76-003048F00942.root'
>    )
> )
> #needed to cache the conditions data
> process.load('Configuration.StandardSequences.FrontierConditions_GlobalTag_cff')
> process.GlobalTag.globaltag = 'FT_53_LV5_AN1::All'
>
> process.demo = cms.EDAnalyzer('DemoAnalyzer'
> )
>
> process.p = cms.Path(process.demo)
> ~~~
> {: .language-python}
{: .solution}


Finally, run the cms executable with our configuration (it may really **take a while**, but the next time you want to run it will be faster):
~~~
cmsRun Demo/DemoAnalyzer/demoanalyzer_cfg.py
~~~
{: .language-bash}

~~~
FIXME: update outpu
~~~
{: .output}

{% include links.md %}

