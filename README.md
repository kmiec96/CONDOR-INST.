# CONDOR-INST. <br>


This tutorial includes an example on how <br>
to run RapidSim simulation on condor. <br>
 
1. Open lxplus. <br>
 
2. In your home catalog (e.g. /afs/cern.ch/user/t/tkowalski) file create two files: <br>
 
-> test.sub <br>
-> test.sh <br>
 
3. test.sub should include the following lines of code: <br>

-------------------------------------------------------------------------------
#Here the output files must have paths leading to your home dir. You must not set /eos paths here! <br>

executable  = test.sh <br>
universe    = vanilla <br>
output      = /afs/cern.ch/user/t/tkowalski/output.out <br>
error       = /afs/cern.ch/user/t/tkowalski/error.err <br>
log         = /afs/cern.ch/user/t/tkowalski/log.log <br>
+JobFlavour = "tomorrow" <br>
queue <br>

-------------------------------------------------------------------------------

4. test.sh should include: <br>

---------------------------------------------------------------------------------

#!/bin/bash <br>

echo "test.sh started   " <br>

source /cvmfs/lhcb.cern.ch/lib/lcg/releases/LCG_87/gcc/4.9.3/x86_64-slc6/setup.sh <br>
source /cvmfs/lhcb.cern.ch/lib/lcg/releases/LCG_87/ROOT/6.08.02/x86_64-slc6-gcc49-opt/bin/thisroot.sh <br>
source /cvmfs/sft.cern.ch/lcg/views/setupViews.sh LCG_101 x86_64-centos7-gcc11-opt <br>

export EVTGEN_ROOT=/afs/cern.ch/user/t/tkowalski/evtgen <br>
export RAPIDSIM_ROOT=/afs/cern.ch/user/t/tkowalski/RapidSim <br>
export DECAY=Dst2D0pip_D02KmEpVu <br>
export TREE=_tree.root <br>
export HIST=_hists.root <br>

echo $EVTGEN_ROOT <br>
echo $RAPIDSIM_ROOT <br>

ls -h $TMPDIR <br>
cd $TMPDIR <br>
echo $TMPDIR <br>
df -h $TMPDIR <br>

$RAPIDSIM_ROOT/build/src/RapidSim.exe $RAPIDSIM_ROOT/validation/$DECAY 500e6 1 <br>

#Copy output *.root files from temporary storage to eos <br>
#This step is only necessary if your output exceeds 2.5G due to limited space in your home directory. <br>

eos cp $DECAY$TREE /eos/lhcb/user/m/tkowalski/simulation <br>
eos cp $DECAY$HIST /eos/lhcb/user/m/tkowalski/simulation <br>

----------------------------------------------------------------------

5. In order to run your RapidSim simulation go to the directory where you placed test.sh and test.sub and do: <br>

condor_submit test.sub <br>

At startup there should appear *.log file in this directory. At completion you should get *.out and *.err files. <br>


6. If you want to move *.err, *.out, *.log to /eos do: <br>

eos cp /afs/cern.ch/user/m/mkmiec/$LOGG$DECAY$ENDLOG /eos/lhcb/user/t/tkowalski/simulation <br>
eos cp /afs/cern.ch/user/m/mkmiec/$OUTT$DECAY$ENDOUT /eos/lhcb/user/t/tkowalski/simulation <br>
eos cp /afs/cern.ch/user/m/mkmiec/$ERR$DECAY$ENDERR /eos/lhcb/user/t/tkowalski/simulation <br>

-----------------------------------------------------------------------

For more details go to https://batchdocs.web.cern.ch/local/quick.html. <br>
