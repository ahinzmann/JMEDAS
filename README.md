# Jet CMS DAS Exercise

## CMS DAS June  2023
  
### Introduction
This tutorial is intended to provide you with the basic you need in order to deal with jets in your analysis. We start with the basics of what is a jet, how are they reconstructed, what algorithms are used, etc. Then we give examples with scripts on how to access jets and use them in your analysis frameworks, including corrections and systematics. In the second part of the exercise, we examine jet substructure algorithms, which have many uses including identification of hadronic decays of heavy SM particles like top quarks, W, Z, and H bosons, as well as mitigation of pileup and others.

This tutorial is designed to be executed as notebooks in SWAN. Also script based versions of the
exercises are provided, which can be executed in lxplus.

## Slides
At the exercise session, we will cover the short version of the slides [CMSDAS_Jets_intro_2023.pdf](https://twiki.cern.ch/twiki/pub/CMS/SWGuideCMSDataAnalysisSchoolLPC2023JetExercise/CMSDAS_Jets_intro_2023.pdf).
A longer version of the slides is also provided [CMSDAS_Jets_intro_2023_longer.pdf](https://docs.google.com/presentation/d/1TKgKRX_BV885NoKyxCdbFZKEjYn_IG5TDsZp9IYgPQk/edit#slide=id.p1).


## Facilitators
- Ashley Parker
- Andris Potrebko
- Mikael Myllymäki

## Mattermost channel
[Jets & jet substructure short exercise channel](https://mattermost.web.cern.ch/cmsdascern2023/channels/jets--jet-substructure-short-exercise)

## Run exercises in SWAN
This version of the same tutorial uses Jupyer Notebooks as a browser-based development environment at [CERN-SWAN](https://swan.cern.ch/) the content of these notebooks is the same as the one in lxplus, it is just a different set up.

### Setup
Go to [https://swan.cern.ch](https://swan.cern.ch) and log in with your CERN account. After that you need to configure your environment, please use these settings:

![add image](images/SWAN_configenv.png)

The most important configuration is the software stack, which has to be ```97a Python2```. After that click on start the session.
Once you are in ```My Projects```, create a new project by clicking on the plus icon on the right part of ```My Projects```. Enter the project name you like, for this example we will use CMSDAS_jetExercise.

## Checkout the code
Open up a terminal by clicking on the icon:

![add image](images/SWAN_terminal.png)

Once there, you are in your cernbox home area, and you can follow these steps:

```
cd SWAN_projects/CMSDAS_jetExercise/
wget https://raw.githubusercontent.com/cms-jet/JMEDAS/DASJan2022/setup-libraries_SWAN.sh
source setup-libraries_SWAN.sh 
```
This will take a while, but basically you are setting your CMSSW environment, cloning some packages, and creating the kernel used in this exercises. If the compilation is succesful, you should see something similar to this at the end of the messages:

```
Loaded CMSSW_10_6_6 into hats-jec!
```

After this you can go to ```~/CMSDAS_jetExercise/DAS/``` and continue with the tutorial. 
The previous steps you have to do it once. Additionally, two important things:

-You need to follow the instructions below to set your grid certificate.
-If you try to access any of the notebooks any other day, it will require you to confirm the kernel. For that please select the ```hats-jec``` option.

## Grid certificate

To access data stored remotely in different places, you need to set your grid certificate. 

 * *For runing on *lxplus*, you only need to run (to get a valid certificate):
```bash
voms-proxy-init -voms cms -valid 192:00
```
 * *SWAN* still does not have a simple way of setting this certificate internally, but we can use a workaround. First, open a "normal" connection to lxplus/cmslpc from your computer (NOT from the SWAN command line), execute `voms-proxy-init -voms cms -valid 192:00` and look at the prints. Your certificate is located in a file with a name like this: `/tmp/x509up_u00000`(lxplus) `~/x509up_u00000`(cmslpc) (with some other numbers instead of 0000). If the location is not printed out (on cmslpc), you can run `echo $X509_USER_PROXY` to find the location. Copy it to your cernbox area like this:
```bash
cp /tmp/x509up_u0000  /eos/home-X/Y/    ###  (on lxplus) where X is the first letter of your cern user id, and Y is your cern user id. `~/x509up_u0000` has to be replaced to the location of the created proxy.
#scp $X509_USER_PROXY Y@lxplus.cern.ch:/eos/home-X/Y/    ###  (from cmslpc) where X is the first letter of your cern user id, and Y is your cern user id.  `~/x509up_u0000` has to be replaced to the location of the created proxy.
```
Now you are ready to activate your certificate in jupyter notebooks in SWAN by first changing the second line of the cell with the location of your certificate file, and then running (i.e. clicking 'play' in) a cell that looks like this:
```python
import os
os.environ['X509_USER_PROXY'] = '{}/x509up_00000'.format(os.environ["HOME"])   ### remember to change this line with what you did above
if os.path.isfile(os.environ['X509_USER_PROXY']):
    print("Found proxy at {}".format(os.environ['X509_USER_PROXY']))
else:
    print("Failed to find proxy at {}".format(os.environ['X509_USER_PROXY']))
os.environ['X509_CERT_DIR'] = '/cvmfs/cms.cern.ch/grid/etc/grid-security/certificates'
os.environ['X509_VOMS_DIR'] = '/cvmfs/cms.cern.ch/grid/etc/grid-security/vomsdir'
```
The cell should be present in every exercise where you need the authentication.
_REMEMBER_ to do this every day that you will try to access remote files in SWAN.


### Tutorial
Once you've completed the setup instructions, change to the directory ```~/SWAN_projects/CMSDAS_jetExercise/DAS/``` in SWAN. Information on the separate tutorial can be found in the "notebooks" subdirectory.


## Run exercises in lxplus

Open a terminal/console, connect to lxplus and prepare your working area (instructions are in bash shell syntax):

```
ssh -Y <username>@lxplus.cern.ch
mkdir JMEDAS2023
cd JMEDAS2023

export SCRAM_ARCH=slc7_amd64_gcc700
source /cvmfs/cms.cern.ch/cmsset_default.sh
scramv1 -n CMSSW10618_JMEDAS  CMSSW_10_6_18   ### choose an appropriate directory name instead of `CMSSW10618_JMEDAS`
cd CMSSW10618_JMEDAS/src
cmsenv

git clone https://github.com/AndrissP/JMEDAS.git Analysis/JMEDAS
git clone https://github.com/cms-jet/JetToolbox Analysis/JetToolbox -b jetToolbox_102X_v3
cd Analysis/JMEDAS
scram b -j 4
```

In some exercises we also need to access files in remote servers, so activate your grid certificate:
```
voms-proxy-init -voms cms -valid 192:00
```

_Optional_. If you like seeing your working directory in the commandline, you can do also this by adding a line to ~/.bashrc and activating it with the 'source' command:

```
echo "PS1='\W\$ '" >> ~/.bashrc
source ~/.bashrc
```

Now, you can start with the `basics.py` script in Section 1.

## Additional Information & Resources

  - [JERC Subgroup Twiki Page](https://twiki.cern.ch/twiki/bin/view/CMS/JetEnergyScale)
  - [The new run 3 JERC subgroup webpage](https://cms-jerc.web.cern.ch/)
    - [JEC and JER Reference Sample Page](https://twiki.cern.ch/twiki/bin/view/CMS/JERCReference)
    - [WorkBook Page on Jet Energy Corrections](https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBookJetEnergyCorrections?redirectedfrom=CMS.WorkBookJetEnergyCorrections)
    - [WorkBook Page on Jet Energy Resolution](https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBookJetEnergyResolution)
  - [JetMET/JERC/JMAR Weekly Meetings](https://indico.cern.ch/categoryDisplay.py?categId=1308)
  - [Run2 Weekly Discussion Group](https://indico.cern.ch/category/7082/)
    - Every other week there is a meeting on jets and pileup
  - SQLite files, text files, and tarballs
    - [JEC Database](https://github.com/cms-jet/JECDatabase)
    - [JER Database](https://github.com/cms-jet/JRDatabase)
  - [JetToolbox Twiki Page](https://twiki.cern.ch/twiki/bin/view/CMS/JetToolbox)
  - [2017 MiniAOD Twiki Page](https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBookMiniAOD2017)
