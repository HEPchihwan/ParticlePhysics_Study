Condor 에 잡을 보내면 일단 스케쥴러에서 내 잡을 관리함 . 
그리고 cpu에다가 내 차례가 되면 내 잡을 배정해줌 .
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
>[!attention] 주의사항
배정 받고 파일을 실행하기 전에 tmp의 비어있는 공간을 할당 받게 되는데 , 
만약 간단한 루트파일 돌리는 스크립트라면 그냥 파일 조작하는 스크립트 보내고 getenv = True로 루트나 파이썬 
사용할 수 있도록 해주면 된다. 
하지만 프로그램을 돌린다면 , 실행파일만 보내서는 실행파일 자체의 유틸 파일들을 찾지 못하고 실행 파일만 덩그러니 가기 때문에 텅빈 tmp 공간에서 시작하게 하지 말고 initialdir 를 실행하는 파일로 설정해야함. 
SNU condor의 경우 tmp 공간이 data6 같은 스토리지에 마운트 되어있기 때문에 이게 가능함. 

```
executable = /data6/Users/snuintern1/genproductions/bin/MadGraph5_aMCatNLO/gridpack_generation.sh
arguments = WRtoNLtoLLJJ_WR1000_N100 /WRtoNLtoLLJJ/cards/LO/WRtoNLtoLLJJ_WR1000_N100 pdmv
log = /data6/Users/snuintern1/genproductions/data/job.log
output = /data6/Users/snuintern1/genproductions/data/job$(process).out
error = /data6/Users/snuintern1/genproductions/data/job$(process).err
getenv = True
should_transfer_files = NO
initialdir = /data6/Users/snuintern1/genproductions/bin/MadGraph5_aMCatNLO
RequestCpus = 3
RequestMemory = 16000
queue 
```
