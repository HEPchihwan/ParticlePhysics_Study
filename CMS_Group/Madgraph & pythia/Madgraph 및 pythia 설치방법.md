>CMS1 server

MDG 2.9 이하 버전  이전에는 파이썬 2 이전이라 잘 안됨.
2.9 or 3 버전부터는 가능 
source /cvmfs/sft.cern.ch/lcg/views/LCG_106/x86_64-el8-gcc11-opt/setup.sh-> Pythia 8.312
로 파이썬 3 사용 or miniconda 이용
pythia 설치 후 이벤트 돌릴때는 위 cvmfs사용하지 않고 돌려야함. ( intall pythia -> 8.311 버전으로 맞지 않음)
( miniconda 이용 )
cmsrel14_0_0 사용 -> 가능한 목록 어케 찾는지..
scram CMSSW

 Exroot 실행하려면 이렇게 뜨는데 #include <rpc/types.h> 필요. 
이건 
conda install -c conda-forge libtirpc=
확인 ls $CONDA_PREFIX/lib/libtirpc.*

./ExRootLHEFConverter unweighted_events.lhe test.root



>CMS2 server

source /cvmfs/sft.cern.ch/lcg/views/LCG_102/x86_64-centos7-gcc8-opt/setup.sh
cms2에서는 Eroot 문제 없었음 

링크 수정 필요 

wget https://madgraph.mi.infn.it/Downloads/ExRootAnalysis/ExRootAnalysis_V1.1.2.tar.gz
wget https://cms-project-generators.web.cern.ch/cms-project-generators/MG5_aMC_v2.9.13.tar.gz