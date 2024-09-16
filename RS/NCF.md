# 선행학습
### 1) MF(Matrix factorization)

- 부족한 행렬에 대해 데이터가 없는 부분을 model 통해 채워 넣는 과정  

학습을 위한 optimizer
- SGD 확률적 경사하강법
- ALS (sgd에서 행렬 동시에 최적화 시키기 어려워서 등장)

### 3) CF (Collaborative Filtering)

- 추천시스템의 협업 필터링에 딥러닝을 적용한 모델
- 프레임워크에서 행렬 분해를 표현하고 일반화 가능
  
### 3) NCF (Neural Collaborative Filtering)

- 추천시스템의 협업 필터링에 딥러닝을 적용한 모델
- 프레임워크에서 행렬 분해를 표현하고 일반화 가능

# 0. Abstract
- 추천 시스템을 위해 딥러닝을 사용하지만, 다른 탐구에 비해 덜 조사 된 경우가 있음
- CF의 핵심 요소인 사용자와 아이템 간 상호 작용 모델링 -> 행렬 인수 분해 사용
- 신경 구조로 대체함으로 NCF라는 것을 제시함. (신경망 기반 협업 필터링)

# 1. Introduction
- MF의 향상에 많은 연구를 함 (효과성에 비해 단점 및 한계가 있기에..)
- 이웃 기반 모델과 통합, 주제 모델과 결합, 인수 분해 기계로 확장 등.
- 이 논문에서는 DNN에 기반한 방법, NCF의 방법 제시, 다양한 실험을 통한 효율성 증명 등.

  (DNN을 활용하여 노이즈가 많은 암묵적 피드백 신호를 모델링하는 방법)

# 2. Preliminaries
###  2.1 Learning from implicit dataPermalink
M, N이 각각 user와 item 개수라면 user-item 행렬 Y.  
- y_ui = 1은 user u와 item i 간 상호작용이 있다는 것 -> 주의점 : 명시적인(explicit) 선호를 뜻하는 것 아님
- y_ui = 0은 상호작용이 없는 것, item을 비선호 한다는 것 아님
- loss function은 크게 두 가지 : point wise loss function, pairwise loss function

###  2.2 Matrix Factorization
- MF를 이용하는 것으로 행렬 Y를 보다 저차원 행렬로 분해하는 것.

###  2.3 The limitation of Matrix Factorization
- 내적과 같은 linear 모델은 user와 item 간 복잡한 관계를 표현하는데 한계가 있다함.  
![image](https://github.com/user-attachments/assets/2e2d3770-52d2-4c42-81ef-51639c87d95f)
- 

# 3.Neural Collaborative Filtering
![image](https://github.com/user-attachments/assets/c8c3ab53-3258-4446-90ad-ea1acc6bd120)
- **input layer** : user, item의 one-hot encoding 벡터가 Input 사용
- **Embedding Layer** :  input 단계의 sparse 벡터를 dense 벡터로 맵핑하는 단계
- **Neural CF Layers** : user, item latent vector를 concatenation한 벡터를 input 받아 dnn 통과
- **Output Layers** : 추정치를 구하고 user u와 item i의 관련성을 나타냄 (0~1)
- **Learning NCF** : Loss function 정의

### +. Generalized Matrix Factorization (GMF)
- MF는 NCF의 특별한 케이스며 이를 GMF라고 명명함.
- linear하고 fixed한 특징으로 복잡한 관계 표현이 어려움
### +. Multi-Layer Perceptron (MLP)
- non-linea하고 flexible해서 보다 복잡한 관계 표현 용이

![image](https://github.com/user-attachments/assets/91295d38-0c3d-4b99-860f-775d2451e911)
- 이로 GMF와 MLP가 통합해 장점을 살리고 단점을 보완할 수 있다한다.
- 이 모델은 user-item간의 상호 관계를 표현하기 위해 MF의 linearity 와 MLP의 non-linearity를 결합한 것이 특징이며 이 모델을 일컬어 neural matrix factorization

# 4. Experiments
- 

# 5. Conclusion
- 
