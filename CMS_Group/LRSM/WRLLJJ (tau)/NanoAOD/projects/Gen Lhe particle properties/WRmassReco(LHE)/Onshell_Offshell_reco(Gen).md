Reconstructing WR and N mass by Gen particle with Hard process

코드 주소 : https://github.com/Chihwan-An/SNU_LRSM/tree/main/nano3/CMSSW_10_6_22/src/PhysicsTools/NanoAODTools/python/postprocessing/analyzer/analyzer/LHE_reco/Gen_HardProcess

[[WRmassreco(LHE)]] 에서 LHE 만을 이용해서 reco 했을경우 motherparticle을 찾아내기 어려워서 genparticle의 hardprocess status flag 를 이용하여 LHE 입자를 사용함

우선 WR reco 했을 경우 여러 문제가 있었는데 , 
- WR이 Hard process , non Hard process 둘다 나옴
- tau mother particle 이 쿼크이 나올때도 있고 WR 이 나올때가 있음. 
- Second WR가 아얘 genparticle 에서 안 나오는 경우가 있음. 
결과 정리하면 

|                     | First WR | tau | N   | tau | Second WR | qq  |
| ------------------- | -------- | --- | --- | --- | --------- | --- |
| N100                | H , XH   |     |     |     | x         |     |
| $\frac{W_{{R}}}{2}$ | H , XH   |     |     |     | x         |     |
| $W_{R} -100$        | H , XH   |     |     |     | H         |     |

- taus mother particle

|           | qq  | WR  |
| --------- | --- | --- |
| H (60%)   | o   | x   |
| X H (30%) | x   | o   |

------------
>[!done] Decay 방법 
>-> qq-> Hadprocess WR -> non Hardprocess WR -> N, tau -> WR* -> qq
>이고 offshell 입자는 genparticle에서 나오지 않음 
>- 그리고 $W_{R}-100$ 에서 second WR이 offshell 임에도 불구하고 genparticle에서 나오는 이유는 onshell 가까이서 생성되기 때문에 마치 Onshell mass 범위에서 onshell 처럼 나오게 된것임.

>[!result] Result
>Hardprocess WR 의 갯수와 non Hardprocess의 갯수는 동일하게 나왔고 , second WR의 경우 항상 offshell로 WR보다 작은곳에서 피크를 형성함. 



![[not_using_tau_from_N__WR_mass.png|400]]

![[3off_shell__secWR_mass.png|400]]

![[2not_using_tau_from_N__WR_mass.png|400]]

![[2off_shell__secWR_mass.png|400]]

![[3not_using_tau_from_N__WR_mass.png|400]]

![[1off_shell__secWR_mass.png|400]]

> [!caution] First WR offshell

![[3offshell__FirstWR_mass.png|400]]

![[2offshell__FirstWR_mass.png|400]]

![[1offshell__FirstWR_mass.png|400]]

- 첫번째 WR reco시 이상한 피크가 보이는곳이 WR4000  N 100인 경우 였음.
WR과 N과의 차이가 크기 때문에 많은 WR을 가질 수 있다고 예상되었고 그로 인해 마지막 사진에서도 유사한 peak를 관찰가능 
- 다른 WR mass 에서도 off shell WR이 관찰되지만 좌표축이 해당 WR과 N의전체로 나눈 비율이기 때문에 WR4000 N100일때 offshell이 많이 나타나 생긴현상으로 예측가능함. 

------------------ 

참고사항 ( 절댓값 양 나타냄 )

------------------

![[not_using_tau_from_N__WR_mass 2.png]]

![[offshell__FirstWR_mass 1.png]]

![[not_using_tau_from_N__WR_mass 3.png]]

![[offshell__FirstWR_mass 2.png]]

![[not_using_tau_from_N__WR_mass 4.png]]

![[offshell__FirstWR_mass 3.png]]








