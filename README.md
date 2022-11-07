# CONDOR-INST.


This tutorial includes an example on how
to run RapidSim simulation on condor.
 
1. Open lxplus.
 
2. In your home catalog (e.g. /afs/cern.ch/user/t/tkowalski) file create two files:
 
-> test.sub
-> test.sh
 
3. test.sub should include the following lines of code:

-------------------------------------------------------------------------------
#Here the output files must have paths leading to your home dir. You must not set /eos paths here!

executable  = test.sh
universe    = vanilla
output      = /afs/cern.ch/user/t/tkowalski/output.out
error       = /afs/cern.ch/user/t/tkowalski/error.err
log         = /afs/cern.ch/user/t/tkowalski/log.log
+JobFlavour = "tomorrow"
queue

-------------------------------------------------------------------------------

4. test.sh should include:

---------------------------------------------------------------------------------
#!/bin/bash

echo "test.sh started   "

source /cvmfs/lhcb.cern.ch/lib/lcg/releases/LCG_87/gcc/4.9.3/x86_64-slc6/setup.sh
source /cvmfs/lhcb.cern.ch/lib/lcg/releases/LCG_87/ROOT/6.08.02/x86_64-slc6-gcc49-opt/bin/thisroot.sh
source /cvmfs/sft.cern.ch/lcg/views/setupViews.sh LCG_101 x86_64-centos7-gcc11-opt

export EVTGEN_ROOT=/afs/cern.ch/user/t/tkowalski/evtgen
export RAPIDSIM_ROOT=/afs/cern.ch/user/t/tkowalski/RapidSim
export DECAY=Dst2D0pip_D02KmEpVu
export TREE=_tree.root
export HIST=_hists.root

echo $EVTGEN_ROOT
echo $RAPIDSIM_ROOT

ls -h $TMPDIR
cd $TMPDIR
echo $TMPDIR
df -h $TMPDIR

$RAPIDSIM_ROOT/build/src/RapidSim.exe $RAPIDSIM_ROOT/validation/$DECAY 500e6 1

#Copy output *.root files from temporary storage to eos
#This step is only necessary if your output exceeds 2.5G due to limited space in your home directory.

eos cp $DECAY$TREE /eos/lhcb/user/m/tkowalski/simulation
eos cp $DECAY$HIST /eos/lhcb/user/m/tkowalski/simulation 
----------------------------------------------------------------------

5. In order to run your RapidSim simulation go to the directory where you placed test.sh and test.sub and do:

condor_submit test.sub

At startup there should appear *.log file in this directory. At completion you should get *.out and *.err files.


6. If you want to move *.err, *.out, *.log to /eos do:

eos cp /afs/cern.ch/user/m/mkmiec/$LOGG$DECAY$ENDLOG /eos/lhcb/user/t/tkowalski/simulation
eos cp /afs/cern.ch/user/m/mkmiec/$OUTT$DECAY$ENDOUT /eos/lhcb/user/t/tkowalski/simulation
eos cp /afs/cern.ch/user/m/mkmiec/$ERR$DECAY$ENDERR /eos/lhcb/user/t/tkowalski/simulation

-----------------------------------------------------------------------
For more details go to https://batchdocs.web.cern.ch/local/quick.html.
