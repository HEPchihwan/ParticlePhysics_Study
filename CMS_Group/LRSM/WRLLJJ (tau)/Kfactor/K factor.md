genproduction -> physics 계산 툴
시현선배 -> nanoaod 처럼 reco 하는 툴 

Nlo/lo 의 signal crosssection 을 이용해서 lo 에 값을 보정하여 background 로 분리시 픽이 통계적으로 안 보일 확률을 줄일 수 있는 역할을 함

여기서 signal cross section 은 pp -> wr 곱하기 wr 의 decay branch ratio 를 곱하며 이 branch ratio 또한 phase space 에 영향을 받아 decaywidth 가 결정되어 계산됨.

kfactor 구할때는 QCD를 넣기 위해 형식상 N 과 타우까지만 만들게 되는데 그 이후 BR은 동일하므로 굳이 계산 안해도 서로 지워짐 .
> [!warning]
> 하지만 여기서 나오는 N의 flavor의 질량에 따라 cross section이 바뀜 
> 애초에 N이 나오지 않게 만들었음에도 N( e mu ) 의 질량에 따라 total cross section이 변하게 됨 . 

- Nlo cross section 과 LO cross section 이 다른 점 :
	LO 와 다르게 gluon radiation 과 loop diagram 을 포함하게 됨 
	만약 gluon radiation 이 일어나지 않은 이벤트가 있었다면 추가로 발생하는 이벤트를 추가하여 
	cross section을 아얘 더해주는 개념 



> [!info]
> 적용한 조건들 
> $ebeam1 -> 6500  
> $ebeam2 -> 6500  
> $parton_shower -> PYTHIA8  
>
> $ickkw -> 0  
> define p = g u c d s u ~~c~~ d ~~s~~  
> define j = p
> maxjetflavor =4

--------
-> Result
cross section 구해보니까 WR 질량이 작을때는 상관이 없는데 클때는 pdf variation이 너무 커짐 (~ $\pm$ 100%)
![[plot4_nlo_with_uncertainties.png|400]]



>[!Question]
>1. off shell 로 만들어진 WR은 x가 작은 대신 원래 잘 안 만들어지고 , onshell의 경우 x가 커서 cross section이 작은데 이 둘이 비교해보면 cross over 하는 지점은 어딜까?
>2. 왜 high WR mass 일때 N이 작으면 pdf variation이 작고 ,  N 이 크면 pdf variation이 큰가?


1-> 한 mass region을 잡아서 그 mass의 cross section에 onshell / off shell 을 퍼센트로 나누면 각각의 cross section이 나오고 이러면 전체 mass 에 대해 onshell cross section 과 off shell cross section 을 비교 해볼 수 있을 듯 함 .  

->2 예상 : off shell 양이 훨씬 많아 져서 pdf variation 이 줄어드는거 아닐까 싶음
-> 확인 할 수 있는 방법이 genparticle ( pythia ) 6000에서 돌린거에서 On off shell 개수 비교해보면 될거 같음 . 

--> 방법 정리 :
1. 각 mass 에 해당하는 on / off shell 의 갯수를 구함.
2. 각 mass 에 해당하는 on off shell 의 비율을 구함
3. 각 mass 에 해당하는 cross section에 해당 비율을 곱하여 각 mass 의 on off shell cross section을 구함 . 
4. 그림으로는 두개를 그리는 데 하나는 3개의 N에 대해서만 on , off shell cross section 그림  , 그리고 두번째 그림은 on/off shell cross section을 비율을 2d plot으로 
