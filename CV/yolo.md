# YOLO
#### **"You Only Look Once: Unified, Real-Time Object Detection"** 논문은 You Only Look Once의 의미인 한 번만 봐도 된다는 의미도 있다. 속도가 빠르다는 장점도 있다. YOLO의 장점, detection 방식 등을 소개한다. 
---

### **0. Abstract (초록)**

YOLO의 새로운 접근방식을 적용한다. 이것은 이미지 전체에 대해 하나의 신경망이 한 번의 계산만으로 bounding bos와 클래스 확률을 예측한다. (Bounding box는 개체의 위치를 알려주기 위해 객체의 둘레를 감싼 직사각형 박스이다. 클래스 확률은 bounding box로 둘러싸인 객체가 어떤 클래스에 해당하는 지에 관한 확률이다.) 객체 검출 파이프라인이 하나의 신경망으로 구성되어있어 end-to-end 형식이다. YOLO의 통합된 모델을 굉장히 빠르게 처리한다. 

---

### **1. Introduction (서론)**

#### **DPM과 R-CNN기반**
DPM(Deformable parts models)은 이미지 전체를 거쳐 슬라이딩 윈도 방식으로 객체 검출하는 모델이다. R-CNN은 이미지 안에서 bounding box를 생성하기 위해 region proposal 방법을 사용한다.  이 둘의 단점인 느리거나 복잠함 때문에 YOLO라는 방식이 나왔다. 절차들을 하나의 회귀 문제로 재정의 한 것이다.

#### **YOLO 장점**
YOLO는 단일 신경망 구조라 구성이 단순하고, 빠르다. 주변 정보까지 학습하여 이미지 전체를 처리하기 때문에 background error가 적다. 훈련 단계에서 보지 못한 새로운 이미지에 대해서도 검출 정확도가 높다. (그러나 SOTA 객체 검출 모델에 비해 정확도가 다소 떨어진다.

---

### **2. Unified Detection**

YOLO는 객체 검출의 개별 요소를 단일 신경망으로 통합한 모델이다. 입력 이미지를 S*S 그리드로 나눈다. 특정 그리드 안에 위치한다면, 그 그리드 셀이 해당 객체를 검출해야한다. IOU(intersection over union)는 실제와 예측의 합집합 면적 대비 교집합 면적의 비율을 뜻한다. 아무 객체가 없다면 값은 0이다. 

#### **2.1. Network Design**
YOLO는 CNN의 구조로 디자인 되어있다.  
컨볼루션 계층 -> 전결합 계층으로 구성.
(신경망 구조는 이미지 분류에 사용되는 GoogLeNet에서 따왔다.)

#### **2.2. Training**
- **연산 속도 향상**: 부동소수점 연산보다 정수 연산이 더 간단하고 빠르다.
- **메모리 절약**: 비트 수를 줄이므로 모델의 메모리 사용량이 크게 감소한다. 이는 특히 제한된 자원을 가진 모바일 및 임베디드 시스템에서 유리하다.

- ImageNet 데이터 셋으로 YOLO의 앞단 20개의 컨볼루션 계층을 사전 훈련시킨다.
- 사전 훈련된 20개의 컨볼루션 계층 뒤에 4개의 컨볼루션 계층 및 2개의 전결합 계층을 추가한다.
- YOLO 신경망의 마지막 계층에는 선형 활성화 함수(linear activation function)를 적용하고, 나머지 모든 계층에는 leaky ReLU를 적용한다.
- 구조상 문제 해결을 위해 아래 3가지 개선안을 적용한다.
    1) localization loss와 classification loss 중 localization loss의 가중치를 증가시킨다.
    2) 객체가 없는 그리드 셀의 confidence loss보다 객체가 존재하는 그리드 셀의 confidence loss의 가중치를 증가시킨다.
    3) bounding box의 너비(widht)와 높이(hegith)에 square root를 취해준 값을 loss function으로 사용한다.
  - 과적합(overfitting)을 막기 위해 드롭아웃(dropout)과 data augmentation을 적용한다.

#### **2.3. Inference**
훈련 단계랑 마찬가지로, 추론에서도 테스트 이미지로부터 객체 검출 시 하나의 신경망 계산만 하면 된다. (YOLO는 R-CNN 등과 다르게 하나의 신경망 계산만 필요해서 속도가 빠르다.) 그러나 그리드 디자인은 한 가지 단점이 있다. 하나의 객체를 여러 그리드 셀에 동시 검출하는 경우가 있다는 것이다. 한마디로 여러 그리드 셀에서 bounding box를 예측할 수 있다는 것이다. 다중 검출이라는 문제다. 비 최대 억제라는 방법을 통해 개선하기도 한다. 

#### **2.4. Limitations of YOLO**
하나의 그리드 셀마다 두 개의 바운딩 박스를 예측한다. 그리고 오직 하나의 객체만 검출한다. -> 공간적 제약을 야기한다. (‘하나의 그리드 셀은 오직 하나의 객체만 검출하므로 하나의 그리드 셀에 두 개 이상의 객체가 붙어있다면 이를 잘 검출하지 못하는 문제’) 마지막으로 YOLO 모델은 큰 bounding box와 작은 거에 동일한 가중치를 둔다는 단점이 있다. 큰 것은 괜찮더라도, 작은 것은 위치가 조금만 달라져도 성능 차가 크다. (부정확한 localization)
 
---

### **3. Comparison to Other Detection Systems**

#### **DPM**
슬라이딩 윈도 방식을 사용한다. 하나로 연결된 파이프라인이 아니라 서로 분리된 파이프라인으로 구성되어있다. 특징 추출, 위치 파악, bounding box 예측 등 수행한다. (YOLO는 분리된 파이프라인을 하나의 컨볼루션 신경망으로 대체한 모델이다. 그래서 더 빠르고 정확하다.)

#### **R-CNN**
슬라이딩 윈도 대신 region proposal 방식으로 객체를 검출한다. Selective search 방식으로 여러 bounding box 만들고 -> feature 추출 -> SVM으로 점수 측정. 그 후 선형 모델로 bounding box를 조정하고, 비 최대 억제로 중복된 검출을 제거한다. (단계별 튜닝을 통해 속도가 굉장히 느리다. 정확성이 높아도 한계가 있다.)

---

### **4. Experiments**

#### **4.1. Comparison to Other Real-Time Systems**
- **Fast YOLO**는 파스칼 데이터 셋 기준 가장 빠른 객체 검출 모델.(DPM보다 2배 높은 정확도)  
- **Fast DPM** 은 mAP 약간 하락하며 속도 향상 모델. 실시간 검출 사용하기에는 아직 속독 느림. 정확도 약간 떨어짐.  
- **R-CNN minus R모델**은 selective search를 정적 bounding box proposal 대체한 모델이다. 실시간 검출은 여전히 부족.  
- **Faster R-CNN** 은 bounding box proposal을 위해 selective search를 사용하지 않고 신경망을 사용한다. 그리하여 기존의 R-CNN 계열보다는 속도가 빨라졌지만 여전히 YOLO에 비해서는 몇 배나 느리다.
￼
#### **4.2 Analysis**

<img width="691" alt="Fast R-CNN" src="https://github.com/user-attachments/assets/bbbeee3c-0c59-439e-bf58-a7059c260305">

￼
#### **4. 5. Generalizability: Person Detection in Artwork**

YOLO 훈련 단계에서 접하지 못한 새로운 이미지도 잘 검출한다.


---

### **5. Real-Time Detection In The Wild**

YOLO는 컴퓨터 비전 애플리케이션에 활용할 수 있는 빠르고 정확한 객체 검출 모델이다.

￼<img width="817" alt="스크린샷 2024-10-16 오후 4 56 32" src="https://github.com/user-attachments/assets/53fd78af-faac-4d40-b604-52ec009ce790">

(실수도 있지만, 잘 예측하는 편)

---

### **6. Conclusion (결론)**

YOLO는 단순하면서도 빠르고 정확하다. 또한 훈련 단계에서 보지 못한 새 이미지에 대해서도 객체를 잘 검출한다. 즉, 새로운 이미지에 대해서도 강건해 애프리케이션에서도 충분히 활용할만하다고 생각된다.
