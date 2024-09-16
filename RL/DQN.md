# 선행학습
### 1) 정책(policy)

- 정책이란 상태 s에서 행동 a를 취할 확률을 모든 상태와 행동에 대해 명시한 것

### 2) 가치함수(Value function)

- 가치함수는 특정 정책의 좋은 정도(상태 s로부터 종료 상태 이르기까지 누적 보상치의 추정치)를 평가

### 3. 상태, 행동, 보상

- state, action, reward

### 4. f:(st,at)→(st+1,rt+1)

- st에서 행동 at를 취하면, 새로운 상태 st+1로 바뀌고 보상 rt+1을 받음

# DQN 이란?
- We refer to convolutional networks trained with our approach as Deep Q-Networks (DQN). 

- DQN은 Deep Q-Networks을 줄인 말인데 convolutional networks를 이용하여 learning하는 방법이다.

# 0. Abstract
- 강화학습을 통해서 고차원 센서로부터 직접 정책(policy)을 컨트롤함으로써 성공적으로 딥러닝 모델을 학습시킴
- 딥러닝 모델은 CNN을 의미
- Q learning으로 학습
- 인풋은 각 픽셀(raw pixel)이 되고 아웃풋은 미래 보상에 대한 value function
- 아타리 게임 2600개에 대한 학습 환경으로부터 학습 알고리즘이나 아키텍쳐 변화없이 학습시킴

# 1.Introduction
- 첫번째 어려운 점은, 딥러닝을 학습시키기 위해서는 엄청나게 많은 수의 라벨링된 데이터가 필요
- 강화학습에서 액션(action)과 보상(reward) 사이에는 딜레이가 존재하는데 이 딜레이는 매우 길어질수도 있어서 학습시 매우 큰 부담
- 두번째 어려운 점은 딥러닝 알고리즘에서는 데이터들의 독립성을 가정하나 강화학습에서는 대부분 상태들이 높은 상관관계를 보임
- 아타리 게임 2600개에 대해 적용
- 각 게임은 인간 플레이어가 클리어하기 어려움
- 목적은 가능한한 많은 게임을 성공적으로 학습하는 single neural network agent를 만드는 것

# 2. Background
- 에이전트는 환경과 상호작용하여 액션을 선택하고 보상을 최대화하는 방향으로 학습
- Q-learning을 사용하여 미래 보상을 최대화하는 최적의 행동-가치 함수를 추정
- Bellman 방정식을 기반으로 Q-network를 반복적으로 업데이트하며, 신경망을 통해 근사치를 학습
- 이 방법은 모델에 독립적이고 off-policy하며, 행동 분포를 따르면서 최적의 전략을 학습
- 탐욕적 전략을 사용하는 동안, ε-greedy 방식으로 탐험과 학습을 병행

# 3. Related Work
- 강화학습 스토리중 가장 잘 알려진 것은 TD gammon은 주사위 놀이를 하는 프로그램인데, 강화학습을 통한 셀프 플레이를 통해 슈퍼 휴먼 레벨 까지 도달
- 최근에는 다시 딥러닝과 강화학습을 혼합시키는 방법이 부활
- 그 이후에도 열심히 연구함...

# 4. Deep Reinforcement Learning
- TD Gammon과 유사한 온라인 접근 방법들과는 반대로, 우리는 experience replay라고 알려진 테크닉을 사용
- 각 타임스텝에 대해 에이전트의 경험을 저장
- 첫번째 장점은, 각 경험 스텝마다 잠재적으로 많은 가중치 업데이트를 사용
- 두번째 장점은, 연속적인 샘플링으로부터 학습을 바로하는 것은 비효율적
- 세번째 장점은, on-policy 학습시, 현재 파라미터가 다음에 학습될 파라미터가 되는 데이터 샘플을 결정
- 실질적으로 우리가 사용하는 알고리즘의 목적은 마지막 N experience tuple을 replay memory에 저장하고 업데이트시 D로부터 균일한 확률로 샘플링하는 것

### 4.1. Preprocessing and Model Architecture
- 전처리 과정으로 인풋 차원을 줄이는 작업
- 본 논문에서의 실험은, 알고리즘 1로부터의 ϕ 함수를 이러한 전처리 과정에 적용시켜 Q-function의 인풋 데이터를 만듦
- 각 가능한 액션들에 대해 분리된 아웃풋 유닛이 존재하고, state representation이 인풋이되는 신경망을 사용하는 아키텍처 사용
- 우리의 아웃풋은 input state에 대한 개별 액션으로부터 예측된 Q-value에 대응
- 이러한 아키텍처를 활용하는 것의 장점은 단 하나의 forward pass로 구성된 신경망으로 주어진 state에 대해 모든 가능한 액션의 Q-value를 계산할 수 있는 능력
- Architecture

![image](https://github.com/user-attachments/assets/c2f8694f-08f3-4d0b-855f-6f82b830ea09)

# 5. Experiments
- 7가지 게임에 대해 종류에 상관없이 동일한 신경망 구조를 사용했고 동일한 학습 알고리즘과 하이퍼 파라미터 세팅을 사용
- 스코어는 긍정적일때는 1점, 부정적일때는 -1점을 주고 바뀌지 않는 경우에는 0점
- 32 짜리의 미니배치로 RMSProp 알고리즘을 사용, behavior policy는 ϵ-greedy를 사용하는데 이때 ϵ은 1 부터 0.1 까지 점차 낮추는데 이 과정은 처음 백만 프레임에 대해 적용되고 그 후에는 0.1로 고정
- simple frame-skipping 기술을 사용, 에이전트는 모든 프레임을 보고 행동을 선택하는게 아니라 매 k번째 프레임을 보고 행동을 선택

### 5.1. Traning and Stability
- 학습 시작 전 랜덤 정책을 실행함으로써 고정된 state 집합을 모으고 해당 state에 대해 Q를 최대화하는 평균치를 추적
- 그림 2에서 가장 오른쪽에 있는 플랏은 average predicted Q가 계속 증가할 뿐만 아니라 변동폭도 작은 것을 볼 수 있음
- 강화학습에 거대한 신경망을 적용시키고 stochastic gradient descent 방법을 안정적으로 사용할 수 있다는 것을 의미
![image](https://github.com/user-attachments/assets/2a9095a0-93cb-439a-b76d-0e9371695516)

### 5.2. Visualizing the Value Function
- 그림 3은 Seaquest라는 게임에서 학습된 value function을 시각화한 것
- 적이 나타났을때 점프하는 동작이 예측값으로 실행되는 것을 보여줍니다(point A).
- 에이전트는 torpedo를 적에게 발사하고 torpedo에 대한 예측값이 최고를 찍은 것을 보여줍니다(point B).
- 적이 사라졌을 때 value가 낮아지는 것을 볼 수 있습니다(point C).
![image](https://github.com/user-attachments/assets/4263efde-62b3-4f3d-b4ef-79183d0d1fcb)

### 5.3. Main Evaluation
- Sarsa가 의미하는 것은 Sarsa 알고리즘을 사용했다는 의미이며, 이 과정에서 손으로 직접 만든 피처를 적용
- Contingency는 Sarsa와 거의 비슷한 접근 방법을 보이지만 에이전트 컨트롤 하에서 학습된 스크린의 일부분을 피처로 사용
- 위 두가지 방법은 풀려고 하는 문제에 대해 사전지식을 얼마나 알고 적용하느냐가 중요
![image](https://github.com/user-attachments/assets/4eee90dc-82e8-4179-bd77-7f48b4dd005c)


# 6. Conclusion
- 이 논문은 강화학습에서의 새로운 딥러닝 모형을 소개
- 인풋데이터로 raw 픽셀을 활용해 아타리 2600 컴퓨터 게임을 마스터하는 능력을 보여줌
- 온라인 Q-learning의variant: 확률적 미니배치와 experience replay modmory를 결합해 강화학습에서 딥러닝 학습을 쉽게 도와즘
- 총 7가지 게임중 6가지 게임에서 좋은 결과를 보여줌
