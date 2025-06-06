Reconstructing WR and N mass by LHE particle 

- [[Genlevel_properties_250203.pdf]] 의 마지막 페이지 AK8 WR4000 N100에서 WR근처외에 왼쪽에 다른 피크가 있는데 해당 피크가 Offshell WR 로 인한 피크라고 설명듣고 , WR을 LHE로도 reco하는걸 추천받음. 
![[Pasted image 20250211133136.png|300]]
---------------
>[!note]  LHE reco 방식
>1. ud , sc , tau 를 모아 pt sorting 
>2. ud(sc) + tau[1] -> N 
>3. N + tau[0] -> WR

Result:


![[WR_recomass.png|200]]

![[WR_recomass(1).png|200]]

![[WR_recomass(2).png|200]]

![[N_recomass.png|200]]


![[N_recomass2.png|200]]

![[N_recomass4.png|200]]

- N이 클때도 low mass에서 peak 이 생김 . -> N 거의 0
- N 도 마찬가지로 low mass 일때 peak 이 생김 
--> N 이 작을때 LHE property를 분석 
tau 랑 quark mass 왜 소수점 단위??

>[!answer] LHE 에서는 질량을 매번 계산하기에 연산이 많아 저장되어 있는건 numerical error 만 저장되어 있음.

>[!question] 왜 low mass 에서 peak 가 생기나?
>쿼크의 갯수를 들어오는거 두개 나가는거 두개의 경우를 택해야 하는데 들어오는거 두개만 있고 나가는 쿼크가 없는 경우 까지 카운트 해서 매우 작은 질량의 N이 reco 된것 

+) LHE 의 정의 

Les Houches Event 로 hadronic process로 p-p interaction에서의 main process 를 말함 . 
즉 Leading order의 파인만 다이어그램과 동일하다고 볼 수 있음 

-> genparticle 에서의 hadronic process status == LHE particle 



