# MC tau trigger 
### [[ tau&muon_trigger| tau muon trigger code]]
- 타우만을 트리거 했을 경우와 타우와 뮤온을 동시에 트리거 했을 경우 어느경우가 더 효율이 좋을 것인가 
- 타우만을 트리거 했을 경우엔 샘플 수는 적지만 시그널이 많이 포함되어 있을 것이고 , 타우와 뮤온을 동시에 트리거한 경우엔 샘플은 많으나 시그널이 좋지는 않을 것이라고 예상하나 얼마나 효과가 있을지 ,  더 효과가 있을지 체크해보아야 한다. 
// 24.10.31

####  Hadronic Tau [[Pt]] cut
 > purpose : tau&muon trigger , tau trigger 사용 후 추가로 [[Pt]] cut을 적용할 경우 이전과 비교하여 어느 정도의 efficiency 차이가 발생하는가 ? -> 원하는건 pt 적용전에는 차이가 좀 나는데 pt 적용 후에는 차이가 거의 안 나는 걸 희망을 하긴 함 . 

> [!tip] 
> * 1. 2. 3.  스텝 지날때마다 그래프 그려보기 
> * [[DM]] = decay mode 를 의미함 

방법 개요 :  
```c
std::vector<Tau> AnalyzerCore::GetTaus(TString id, double ptmin, double fetamax){
std::vector<Tau> taus = GetAllTaus();
std::vector<Tau> out;
for(unsigned int i=0; i<taus.size(); i++){
if(!( taus.at(i).Pt()>ptmin )){
continue;
}
if(!( fabs(taus.at(i).Eta())<fetamax )){
continue;
}
if(!( taus.at(i).PassID(id) )){
continue;
}
out.push_back( taus.at(i) );
}
return out;
}
```
내 analyzer에  기존 trigger를 지나고 나서 tau를 얻는데
Gettaus에서 ID( 어떤 설정을 해줄꺼냐)는 아마 tau.c의 마지막에서 ID를 내맘대로 설정 할 수 있는거 같고
그럼 결국 out이라는 변수를 받게 되고 tau를 이어 받게 되는데 , tau의 변수 형태가 어떻게 되는지는 봐야할거 같음 ( .size도 되고 ,  (0).pt() 에서 0은 뭔지 모르겄다 ) 
사용할 코드 : 

```c
1. v = gettaus("lrsmtight", pt=0, 2.1) 
 - v : pt sorting
2. v.size()>0 
3. v(0).Pt() > 190  


===================================================
  if(ID=="LRSMTight"){
    if(j_decaymode == 0 || j_decaymode == 1 || j_decaymode == 10 || j_decaymode ==11){
      if(!DecayModeNewDM()) return false;
      if(!( fabs(dZ())<0.2 )) return false;
      if(!( passTIDvJet() && passTIDvMu() && passTIDvEl())) return false;
      return true;
    }
  }

```
tau.c 랑 analyzercore를 주로 이용함 

ID: 
![[스크린샷 2024-11-11 오후 4.03.52.png]]

**결론**
![[heatmap_ratio_v3_Div_bytau.png]]
# Figure of Merit (FoM) 분석
- s/√b 계산을 통한 trigger 효율성 평가
- 백그라운드 대비 시그널 검출 능력 측정
- 다양한 에너지 포인트에서의 trigger FoM 평가

- efficiency 와 purity를 통해 최대로 볼 수 있는 지역을 보는게 FOM 
  eff 는 PID가 1에 가까울 수록 찾기 힘들거고 (backgorund 중에서 진짜 같아 보이는거) ,
   purity는  pid가 1에 가까울수록 찾기 쉬움( 가짜와 진짜 판별) 그래서 FOM 확인전에 eff는 우 하향 , purity는 우상향임을 먼저 확인하고 FOM 확인 가능 
  
  FOM s/√(b+s)
