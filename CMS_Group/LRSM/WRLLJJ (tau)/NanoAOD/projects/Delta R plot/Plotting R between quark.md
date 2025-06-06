$W_{R}-N-q \bar{q}$  에서  N의 질량에 따라 두개의 quark 사이의 거리를 봄.
- $W_{R }$의 질량이 고정시 N의 질량이 클 수 록 jet사이의 각도가 커짐 . 
- N은 총 3가지로 $W_{R}-100,\frac{W_{R}}{2},100$ 을 이용함 . 
예시 )
![[Pasted image 20241217130847.png|300]]
                       ( $W_{R}$ 4000) In LHe level 

[[delta R between quark Gen status 8 code]]

- Gen particle 
	: 최종입자까지 만들어진 이벤트로 검출기와 상호작용하지 않은 이벤트임. 
	1. 부모입자와 자식입자를 트리형태로 만들어 놓음
	[[Genpart_deltaR_using_tree code]] , [[Genpart_deltaR_using_tree plot code]]
	결과 : 
	![[canvas_WR1000 2.png|300]]
	![[canvas_WR4000 2.png|300]]
	![[canvas_WR2000 2.png|300]]
	1. genparticle 을 직접 순회하며 입자가 N 인 경우 추가로 순회하여 부모가 해당 N인 쿼크를 찾아 해당 쿼크끼리의 거리를 측정함 .
	[[Genpart_deltaR_not_using_tree code]] , [[Genpart_deltaR_not_using_tree plot code]]
	결과 : 
	![[canvas_WR1000 1.png|300]]
	![[canvas_WR2000 1.png|300]]
	![[canvas_WR4000 1.png|300]]
- LHE particle 
	: 파인만 다이어그램에서의 입자들 까지만  만들어 놓은 이벤트.
	각 이벤트에 대하여 모든 LHE particle 을 가져오고 해당 입자가 쿼크인경우 두 쿼크 사이의 거리를 계산.
	[[LHE_deltaR_plot code]] , [[LHE_deltaR_Merging_plot code]]
	
	- 결과 
	
	![[canvas_WR1000.png|300]]
	![[canvas_WR2000.png|300]]![[canvas_WR4000.png|300]]
	