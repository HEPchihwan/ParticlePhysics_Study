```
condor.jds

+JobBatchName = "@@JobBatchName@@"
+JobFlavour = "@@JobFlavour@@"

Universe = vanilla 

Executable = run.sh
 
Error = err/$(ClusterId).$(ProcId).err
Output = out/$(ClusterId).$(ProcId).out
Log = log/$(ClusterId).log
environment = "CLUSTER=$(Cluster); PROCESS=$(Process); ID=$(ProcId) "

#request_cpus = @@request_cpus@@
#request_memory = @@request_memory@@

#use_x509userproxy = True
#x509userproxy = @@x509userproxy@@

should_transfer_files = yes

+SingularityImage = "/cvmfs/singularity.opensciencegrid.org/cmssw/cms:rhel9"

Queue 20
```

```
setup.sh

#!/usr/bin/env bash

#lscpu

#cat /etc/os-release

echo ""

echo "HELLO "

echo "${ID}"

echo ""
```