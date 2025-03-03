>[!note] 
> -  Gen level : 검출기와 상호작용하기 전까지의 단계 
Final state1 :  Tau Jets ,  $q \bar{ q}$ jets , electron($\mu$)
Final state2: Tau Jets , Fatjet ( e, $\mu$)
수많은 Jet들 중에서 efficiency 낮은 jet제거 , jet안에 mu나 el 있으면 그 jet들도 제거-> pt, eta , m 구하기

[[Genlevel_ptetamass_code]]
[[Genlevel_ptetamss_plotting_code]]

<mark style="background: #FF5582A6;">1. No cuts</mark>
	pt , eta 
- GenVisTau , GenJetAK8  leading pt eta   
- GenJet (AK4) , SubGenJetAK8  leading subleading pt eta 
-  leading sub leading ak4 jet pt eta

- tau pri sec/ leading + jet subleading  jet ( $W_{R}$ using AK4 , subjetAK8)
- tau sec + leading jet sub leading  jet ( N using AK4 , subjetAK8 )
- tau pri + leading ak8 jet ( WR )

	mass
- < tau + AK4 > : leading( muon / electron , GenVisTau , GenJet ) , subleading (GenJet)
- < tau + AK8 > : leading ( GenVistau , GenJetAK8)
- < tau + sub AK8 > :  leading ( muon/ electron ,GenVisTau , SubGenJetAK8 ) , subleading ( SubGenJetAK8 )
- tau pri sec/ leading + jet subleading  jet ( $W_{R}$ using AK4 , subjetAK8)
- tau sec + leading jet sub leading  jet ( N using AK4 , subjetAK8 )
- tau pri + leading ak8 jet ( WR )



<mark style="background: #FF5582A6;">
2. Adjusting Cuts </mark>
	Object cuts

|                   | $p_{T}$ | $\|\eta\|$ |
| ----------------- | ------- | ---------- |
| leading GenvisTau | >190    | <2.1       |
| GenJet            | >40     | <2.4       |
| GenJetAK8         | >200    | <2.4       |
| SubGenJetAK8      | >40     | <2.4       |
	 Cleaning ( tau 중에 다른 Jet이랑 많이 겹쳐있거나 , 전자나 뮤온이 isolated 되어있지 않고 Get 안에 있을 때 )

- < Tau 가 다른 AK4 jet안에 지나치게 겹쳐있는 경우 > 
- < Tau 가 다른 AK8 jet안에 지나치게 겹쳐있는 경우 > 
- < e/$\mu$가 AK4 jet안에 들어있는 경우 >
<mark style="background: #FF5582A6;">3. Final Jobs</mark>
	pt , eta 
- GenVisTau , GenJetAK8  leading pt eta   
- GenJet (AK4) , SubGenJetAK8  leading subleading eta 
-  tau pri sec/ leading + jet subleading  jet ( $W_{R}$ using AK4 , subjetAK8)
- tau sec + leading jet sub leading  jet ( N using AK4 , subjetAK8 )
- tau pri + leading ak8 jet ( WR )
	mass
- < tau + AK4 > : leading( muon / electron , GenVisTau , GenJet ) , subleading (GenJet)
- < tau + AK8 > : leading ( GenVistau , GenJetAK8)
- < tau + sub AK8 > :  leading ( muon/ electron , GenVisTau, SubGenJetAK8 ) , subleading ( SubGenJetAK8 )

- Use only tau from lepton
- < $W_{R}$ using AK4 , subjetAK8>tau pri sec/ leading + jet subleading  jet 
- < N using AK4 , subjetAK8 >tau sec + leading jet sub leading  jet 
- <$W_{R}$ >tau pri + leading ak8 jet 

	 
<mark style="background: #FF5582A6;">4. Result</mark>
-  N 에 따른 Jet 
	N 늘어나면 W* boson이 $q\bar{q}$로 붕괴하는 경우 점점 더 boost 되어서 Jet의 각도가 줄어들게 됨 . 

|        | Jet특징                               |
| ------ | ----------------------------------- |
| N low  | boost되어 좁은곳에 모여 있음 -> ak8로 한번에 보기   |
| N high | resolved 되어 두 jet가 퍼짐 -> AK4 2개로 보기 |
25/02/03
- AK8 의 경우 WR 4000에 N 100 인 경우 mass peak이 두개가 생기는데 , 
가능한 secondary WR의 질량이 많아서 distribution이 퍼지게 된다. 
그리고 연속적인 분포가 아니라 한 곳에서 피크가 나오는건 pdf때문이라고 하는데
- 추가로 안 한게 AK4 제트를 못만드는 경우 AK8을 구성하도록 하는 경우를 고려를 안 했음. 
- AK8에서 secondary lepton이 타우인 경우 , AK8 cleaning 다시 정의해야하는거 같음. 