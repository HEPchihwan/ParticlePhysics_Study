# Machine Learning

---

## Introduction

This tutorial has been created based on the talk [Towards the Explainable AI in HEP](https://indico.cern.ch/event/1345869/timetable/).

There are 6 parts introducing from the simplest BDTs to the modified ParticleNet architecture.

  

### Classifying QCD multijets vs TT-hadronic

 0. Plotting input distributions

	- Input distributions used in this classification problem have been shown.
	   sig,bkg 샘플 가져와서 무슨 변수를 사용할 건지 보여줌 
1. Classification using [Random Forests](https://link.springer.com/article/10.1023/A:1010933404324)
	< 학습 후 무작위 샘플을 판별하는데 어느 피쳐가 학습에 큰 기여를 하는가 >
	sig , bkg 샘플 섞은 뒤 해당 샘플의 60프로는 훈련용 샘플 , 30프로는 결과 확인용샘플로 추출 
	- MDI importance and Permutation importance
		MDI : 단순하게 샘플 훈련 후 valid 샘플이용시 어느정도로 잘 맞추는지 판단. 
		만약 모델 -> 훈련 샘플 재 가동시 1이 안 나오면 좀 안 좋게 훈련한 거고 , valid 샘플 가동시 낮게 나오면  훈련샘플에만 잘 맞는 과적합 ( overfitting )이 된것임. 
	- Mitigating an overfitting problem by using permuattion importance of train and valid set
		permutation 모델은 각 feature을 하나씩 빼봤을때 어느정도의 성능 감소가 있냐를 통해서 이게 얼마나 중요한 feature인가를 판단하는 모델임 .

	- overfitting & underfitting 조절하는 방법: 
		tree depth를 조정함.
		 tree depth가 늘어나면 더 비선형적이고 복잡한 데이터구조를 처리하는 대신 샘플데이터에 지나치게 학습된 overfitting 발생 가능. 대신 훈련 성능은 좋을 수 있음 . 
		 tree depth 가 줄어들면 더 간단한 데이터구조를 학습하여 훈련데이터에 과하게 학습되지 않을 수 있는 대신 underfitting 발생 가능 . 대신 훈련성능이 떨어질 수 있음. 
2. Classification using Deep Neural Networks
	< DNN 사용시 여러 피쳐중 어느 피쳐가 중요한 역할을 하고 깊은 신경망중 어느 신경망이 제일 중요하고 , 해당 신경망은 어떤 피쳐의 조합으로 연결됐는지  > 
	- DNN 학습 
		 DNN 함수가 정의 되어 있고 해당 함수를 이용해 iteration을 진행하며 계속 학습시킴. 

	- Black Box 확인 ( 어떤 feature가 성능에 영향을 많이 주는지 )
		- Checking features 	
			- Integrated Gradient :
				global importance( use nonlinear info ) , gives minus plus number 
				구체적으로 어느 확률로 얼마나 예측이 가능한지 알 수 있음 
			- Sailent model : 
				local importance , only gives positive number
		- Checking Nodes ( finding dominant node & coeffeicent of feature)
			- Integrated Gradient
				노드들의 결과값 기여도를 확인 가능하고 , 각 노드에 대해 구체적으로 양수인 경우 결과값 기여 , 음수인 경우 결과값 억제 효과정도도 확인 가능함. 
				그리고 한 노드가 각 feature와 어느정도 결과 의존성을 가지는지도 확인 가능함. 
				*모든 각 노드의 feature 의존성을 모두 합하면  위의 checking feature을 통한 feature의 결과 의존성과 같은 결과가 나와야 하는가 에 대해선  꼭 그렇진 않음 .
				노드간의 상호작용에 의한 결과 의존성도 있기 때문에 서로간의 상호작용이 없는경우는 동일할 수 도 있음. 
	- Simple DNN models using [pytorch](https://pytorch.org/)
	- Attribution methods, applying [Saliency](https://arxiv.org/abs/1711.00867) and [Integrated Gradients](https://arxiv.org/abs/1703.01365) using [CapTum](https://captum.ai/)

### Classifying Light Charged Higgs vs. tt+Z

3. Graph Visualisation
	- Loading dataset and visualising event graphs using [networkx](https://networkx.org/)
	- sig나 bkg 데이터를 받아와 이걸 이용해 eta , phi 값을 구하고 , 해당 입자가 어떤 종류인지도 사용하여 분류. 
	- **GraphDataset**으로 묶어 **PyTorch Geometric**에서 처리할 수 있게 함. (이런 형태로 데이터 만듦)
	- 이를 이용해 eta 와 phi 의 phase space 에서 어떤 입자가 어느정도의 분포를 가지는 지 확인 가능 .
4. Modified ParticleNet

	- Introducing [ParticleNet](https://arxiv.org/abs/1902.08570) architecture and its implementatin using [pyg](https://pytorch-geometric.readthedocs.io/en/latest/)
	- Comparing performance with / without giving edge\_attributes
	- Visualising egde indices with their attention masks
	
		- 노드안의 정보말고도 노드끼리의 피쳐를 새로 줘봤을때 어느정도의 효율성 차이가 나는가 
			- Delta R 이 없을 때 ( 노드안의 피처만을 이용하여 학습을 한 경우  )
				- max size = -1 : 데이터 전부를 이용하여 모델 학습 
				- max size = 100 : 데이터 일부를 이용하여 graph 형태로 나타냄 
			- Delta R 의 피처를 사용하여 학습한 경우 
				-  max size = -1 : 데이터 전부를 이용하여 모델 학습 
				- max size = 100 : 데이터 일부를 이용하여 graph 형태로 나타냄 
			$\therefore$ 노드 사이의 edge의 상호 관계의 강도를 알 수 있음 . 
			
5. Hyperparameter Optimization

	- Optimizing one of the hyperparameters - edge\_dropout\_p
	
	- How to save and reload the pretrained models
	
	 - Drop out이 0.05 혹은 0.2 로 바꾸어 노드간의 edge상호 관계성이 어떻게 변하는지 확인  
6. Surrogated Models
	 - 이전의 particle net에서 KNN 방식으로 학습을 하는데 이를 시각화 하기 위해 여러 학습 샘플중 하나만 골라서 노드 에지 관계를 봤음 . 
		 - KNN 학습 방법의 경우 매 샘플마다 에지가 다르기 때문에 하나만 보는건 정확하지 않음
	
	 - particle Net에는 모든 샘플에 대해 edge importance가 어느정도 되는지 확인이 불가능해 GraphNet을 이용하여 모든 샘플에 대해 시각화가 가능함. 
	
	- How to interpret the input-output relating in model-agnostic approach
	- Training surrogated models

>  [[MachineLearning_Q&A|Q&A|]]


---

- Author: Jin Choi

- Contact: choij@cern.ch

- Last Update: November 27, 2023