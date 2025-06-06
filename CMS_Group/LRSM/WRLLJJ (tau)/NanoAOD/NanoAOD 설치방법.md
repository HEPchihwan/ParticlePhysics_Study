```
mkdir build
cd build 
cmsrel CMSSW_10_6_22
cd src
cmsenv
git clone https://github.com/cms-nanoAOD/nanoAOD-tools.git PhysicsTools/NanoAODTools
cd PhysicsTools/NanoAODTools/
cmsenv 
scram b


cd src/Physicstools/NanoAODTools/python/postprocessing/
에 analyzer 폴더 만들고 ,

여기 안에 실행 파일들 , AnalyzerHelper , ID  , 결과파일들을 넣어 주면 됨 

```