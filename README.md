# Image-Recognition-with-CNN
CAPTCHA training and break

1.	프로젝트 배경

인터넷의 발달로 인해 대부분의 업무와 생활이 인터넷을 통해 가능하다. 그만큼 대부분의 사람들이 인터넷을 사용하고 

그러나 수강신청이나 공연 예매, 웹사이트 가입 및 해킹 시에 매크로를 통한 악의적 사용이 증가하고 있다. 특히 매크로로 가입한 웹사이트 사용자 계정을 통한 스팸 메시지 유포나 DDos공격등이 빈번히 발생하고 있고, 누구나 한번쯤은 불편을 겪어보았을 것이다. 이러한 문제점들을 해결하기 위해 사람은 쉽게 인식하지만 봇은 인식이 어렵게 하여 접근을 막는 CAPTCHA(Completely Automated Public Turing test to tell Computers and Humans Apart)가 주로 사용된다. 사람은 변형된 이미지를 쉽게 추론이 가능하지만 봇은 직관적인 추론이 불가하다. 따라서 테스트를 통과하지 못할 경우 봇으로 판정, 접근을 차단하는 방식이다. 하지만 인공지능이 발달함에 따라 봇도 CAPTCHA테스트를 쉽게 통과하고 공격자로 하여금 소기의 목적을 달성하게끔 한다.
이 프로젝트에서는 CNN을 통해 CAPTCHA를 무력화시키는 프로젝트를 진행하고 보안성 향상을 위한 솔루션을 제시한다.


2.	CNN 개발 배경

기존 Fully Connected 신경망에서는 인접하는 계층의 뉴런이 모두 연결되고 출력의 수를 임의로 정할 수 있었다. 하지만 단점이 존재하였는데, 2차원 ,3차원 등 모든 차원의 데이터가 연산을 위해 1차원 데이터로 평탄화를 해줘야 했다. 때문에 Fully Connected 신경망에서는 데이터의 형상을 무시시키는데, 이는 Image Recognition에서 매우 치명적이다. 간단한 손글씨 dataset인 MNIST는 평탄화 작업을 거쳐도 픽셀이 명확하기 때문에 괜찮았지만 사진이나 영상에서는 평탄화된 데이터는 같은 차원의 뉴런으로 취급하여 인식에 매우 어려움을 느끼게 된다.
이 단점을 보완하기 위해 개발된 것이 CNN인데 CNN에서 사용되는 합성곱 계층은 평탄화 작업이 없이 데이터의 형상을 유지한채로 입력받으며, 연산하고, 전달 및 출력하게 된다. 따라서 Image Recognition에서는 CNN이 매우 우수한 성능을 보여준다.

3.	Modeling

3.1	Data Analysis
학습을 진행하기에 앞서 dataset을 분석하면 아래와 같은 결과가 나온다. 1070개의 image 및 label data 중 1040개는 png파일, 30개는 jpg파일로 구성되어 있다. 이번 dataset에서는 jpg파일의 비중이 매우 작기 때문에 따로 전처리 없이 jpg파일을 제외한 png파일로만 학습을 진행하였다. 만약 jpg파일의 비중과 png파일의 비중이 비슷하다면, png파일의 색영역인 rgba를 jpg의 색영역인 rgb에 맞게 a(alpha; 투명도)값을 1로 치환하여 전처리를 진행해야 한다.
CAPTCHA에서 출현하는 문자는 사람이 인식하기에도 혼동을 가져다 줄 수 있는 i와 1등의 문자를 제거하였기 때문에 19개로 구성되어 있다. 아래의 문자 출현빈도 그래프를 보면 대부분의 문자 출현빈도가 비슷하지만 n의 출현빈도가 다른 문자대비 2배임을 알 수 있다.
 

3.2	Evaluation Metric
 
모델의 정확도를 정량적으로 측정하기 위해 측정공식을 사용했다. 여기에서 사용한 Evaluation Metric는 (corrected element/5) 로 정의하여 모든 문자를 정확히 예측한 경우 1점을 부여했다

3.3	Creation of the training and validation datasets
 
학습을 위해 모든 문자를 0~18까지로 encoding을 진행하였다. 또한 인식의 일관성을 위해 사이드의 여백을 crop하고 틀어진 이미지 사이즈를 원래대로 resizing하였고 transpose를 통한 축 순서 변경을 거쳤다.
딥러닝에서의 모델 검증과 평가는 매우 중요한데 이 두가지 프로세스를 정확히 진행하기 위해선 train set과 validation set 즉 test set이 완전히 분리되어 서로의 결과에 영향을 끼치지 않는 독립적인 관계여야 한다. 만약 train set에 validation set이 섞여들어간다면 학습 단계에서 validation set을 학습하게 될 것이고, 이는 문제가 유출된 시험지를 푸는 것과 같다. 이렇게 평가된 모델은 우리가 중간고사 범위에서 매우 중요하다고 배웠던 일반화 성능이 크게 떨어지므로 모델의 일반화 성능이 왜곡되었다고 한다. 이 모델에서는 90%의 train set, 10%의 validation set으로 검증 및 평가를 진행한다.
image data인 X_train은 200*50사이즈를 가진 936장의 사진으로 구성, lable data인 y_train은 5의 길이를 가진 문자열 936개로 구성했다.
validation set에서는 image data인 X_val은 200*50사이즈를 가진 104장의 사진으로 구성, lable data인 y_val은 5의 길이를 가진 문자열 104개로 구성했다.

3.4	Modeling
 
CNN은 Fully Connected Network에 비해 합성곱 계층(Conv)과 풀링 계층(Pooling)이 추가된다. 이 모델에서는 2개의 Conv계층을 사용하였고, 이 계층에서는 동일하게 activation function은 relu로 지정하였는데, sigmoid는 exp연산 때문에 리소스를 많이 필요로 하는 단점이 있어 사용하지 않았다.  padding은 same으로 구성하였고, Pooling계층에서는 MaxPooling에 parameter를 (2, 2)로 지정하였다. 그리고 Dense layer또한 2개로 구성하였는데, dense1 layer는 256, dense2 layer는 각각 256, 64의 출력이 발생한다. output layer는 dense layer로부터 입력 받은 값을 19개의 문자로 다시 반환하는 layer로써 activation function은 softmax를 사용하였다. 

3.5	Training
 
데이터 전처리 과정에서 생성된 cropped image를 대상으로 학습을 진행하였다. 최적의 epochs를 찾기 위해 epochs의 parameter를 10, 20, 30, 40으로 수정하며 수행하였고, 그 결과 25번째 epoch에서부터 정확도가 peak임을 알 수 있었다. 하여 epochs를 30으로 설정하였다.

3.6	Model Performance
 
학습곡선이 train에 완전히 붙은 완벽한 그래프가 그려지진 않았지만 매우 이상적인 추세를 보여준다. 이 그래프의 추세는 학습량이 부족하지도, 과하지도 않음을 나타낸다. 

3.7	Prediction
 
먼저 문자마다 encoding을 통해 0~18까지 순서대로 할당하였다. 각 validation image들은 20*5의 사이즈로 불러왔고, transpose함수를 통해 인덱스로 축의 순서를 변경하였다. 해당 이미지들은 모두 gray scale로 처리하여 예측하였다.
그리고 prediction값과 이미지의 문자를 확인하여 학습이 제대로 이루어졌음을 확인할 수 있었다.


3.8	Model Evaluation
 
이 모델은 93%의 정확도를 보여준다.
 



5.	참고문헌

사이토 고키, “밑바닥부터 시작하는 딥러닝”, 한빛미디어

“CAPTCHA : 2 solutions to break them [>99%]”, ARNAUD R, 2021. 7. 수정
https://www.kaggle.com/arnrob/captcha-2-solutions-to-break-them-99/notebook

양서연. "실용(實用)되는 CAPTCHA를 기반으로 한 CAPTCHA 안전성 분석 연구." 국내석사학위논문 高麗大學校 情報保護大學院, 2014. 서울

송슬기. "보안성 강화를 위한 문자 및 이미지 기반의 하이브리드 CAPTCHA 테스트에 관한 연구." 국내석사학위논문 강원대학교 산업대학원, 2017. 강원도

이현우. "인터넷상에서 자동화된 공격을 막기 위한 CAPTCHA 에 관한 연구." 국내석사학위논문 동국대학교, 2011. 서울
