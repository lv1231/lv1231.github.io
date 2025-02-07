---
layout: single
title: "[GPU_Programming_01] 바이오&의료 데이터 병렬 처리"
categories: GPU
tag: [Concurrent, Parallel, Distributed]
toc: true
toc_sticky: true
toc_label: "My Table of Contents"
toc_icon: "cog"
author_profile: false
# sidebar:
#     nav: "docs"
---

![junsnote]({{site.url}}/images/nvidia.jpg)

<div class="notice--info">
    <h1>학습목표</h1>
    <ol>
        <li>바이오/의료 데이터 이해</li>
        <li>우리가 병렬 시스템을 만드는 이유 이해</li>
        <li>병렬 프로그램을 작성하는 이유</li>
        <li>병렬 프로그램을 작성 방법 학습</li>
    </ol>
</div>


# 컴퓨팅 발전 및 바이오/의료 데이터 응용

## 컴퓨팅 발전
- 1986~2002년, **마이크로프로세서(CPU)**는 연평균 50% 초고속 성능 향상을 이룸.
- 그 이후, 연평균 20% 성능 향상 수준으로 낮아짐.
    - 메모리 등 하드웨어의 물리적 한계점이 주요인
- 더뎌진 향상 속도의 해결책으로 보다 빠른 CPU를 설계 및 제작에 한계점을 부딪힘.
- 따라서 하나의 통합 회로에 여러 개의 프로세서를 넣는 방법으로 발전함. 
    - 코어 하나의 성능을 높이는 대신, 여러 개의 코어가 들어가는 형태로 변형

## 이후 작업은 프로그래머들의 몫
- 만약 프로그래머들이 이를 인식하지 않거나 어떻게 사용해야 하는지 모르는 경우에 더 많은 프로세서를 추가하는 것은 크게 도움이 되지 않음
    - 대부분의 경우, 직렬 프로그램은 이러한 다중 프로세서들이 제공된다고 하더라도 혜택을 받지 않음 (직렬 <-> 병렬)
    - **병렬 프로그램**: 멀티 코어에 해당되는 프로그램을 개발하는 것
    - 프로그램의 병렬적으로 동작: 여러 코어에서 여러 프로그램이 동시 동작하는 것
    - **병렬 프로그래밍 (Parallel Programming)**: 한 프로그램이 병렬적으로 일을 하여 어떤 작업을 마침.
    - **직렬 프로그래밍 (Serial Programming)**: 단지 하나의 코어를 사용할 수 있는 프로그래밍. 다중 코어의 혜택을 받을 수 없음.

## 왜 우리는 계속해서 성능을 높여야 하는가?
- 계산 능력은 증가하고 있지만, 우리의 계산 문제와 요구도 그에 맞춰 증가하고 있음.
    - Ex. 양자 컴퓨팅, 슈퍼 컴퓨터
- 성능 향상 덕분에 과거에는 상상조차 하지 못한 문제들이 해결되었으며, 인간 유전체 해독과 같은 예가 있음
- 아직 해결되지 않은 더 복잡한 문제들이 해결을 기다리고 있음
    - Ex. 휴먼 지놈, 인간 유전체 문제
    - Climate modeling: 기상 예측
    - Protein folding: Covid Virus 관련 Simulation
    - Drug discovery: 특정 질병에 관한 신약 실험 Simulation
    - Energy research: 신재생 에너지 관련 Computing Simulation
    - Data analysis: Big Data (TB, PB, HB), 초거대 AI

## 바이오/의료 데이터의 예
- 바이오 데이터
    - 생물 구성 정보: Protein folding, etc.
- 의료 데이터
    - 전자 환자 기록 (Eletronic Health Records): 몸무게, 가슴, X-ray, etc.

## 바이오/의료 데이터의 특성
- 다양한 형태의 데이터로 구성
- 의료 데이터의 예
    - 서술형 텍스트 데이터: 환자 상태에 대한 의사 진료 기록 등
    - 단순 수치 데이터: 약 처방 등
    - 이미지 데이터: X-ray 사진이나 의사의 스케치
- 바이오/의료 기술 및 기기의 발전에 따라 데이터의 양 급증
    - 바이오/의료 데이터는 주요 결정 사안(의사 진단 등)에 큰 영향을 미침
    - 바이오/의료 기술이 발전은 더 많은 데이터를 수집할 수 있도록 함
    - "이런 폭증한 데이터를 어떻게 처리할 것이냐?" 하는 것이 문제
        - Solution: 적절한 하드웨어와 데이터들의 연관성 분석

## 바이오/의료 데이터와 컴퓨터 기술
- 컴퓨터는 바이오/의료 데이터의 저장, 검색, 분석 등에 핵심적인 역할
- 의료 임상 데이터의 경우, 기본적으로 불완적으로 치료 결과도 확률적이기 때문에 진단 결과 분석에 컴퓨터/수학 모델이 사용
- 데이터 수집과 신호 처리에 컴퓨터 기술 사용
- 대용량 바이오/의료 데이터의 저장 및 보안에 대용량 정보 처리 및 컴퓨터 보안 기술 사용 

## 바이오/의료 데이터에 대한 병렬 처리 응용
### 01) 분자 양학 시뮬레이션
- 생물학적 공정은 일반적으로 실험 기법 (X선, NMR etc)에 의해 연구
- 그러나, 원자 상호작용 측면에서 더 깊은 통찰력을 얻기 위해 생물학적 고분자 (단백질, DNA, 탄수화물 etc)를 모델링하고 물리 법칙을 따르는 몬테카를로 방법 또는 분자역학(MD) 기법에 의해 그들의 행동을 시뮬레이션
- 직렬 시뮬레이션은 시간이 오래 걸리기 때문에 시뮬레이션 시간 단축을 위해 병렬 처리를 활용한 병렬 시뮬레이션을 사용

### 02) 신약 발견
- 고성능 컴퓨팅을 사용하여 대규모 화학 데이터 기반을 선별하고 합성을 위한 화합물의 우선순위를 지정
- 하루에 최대 10만 개의 분자를 구조 기반으로 선별할 수 있음 (Agarwal 및 Fishwick, 2010)


# 병렬 컴퓨팅이란?
## 왜 우리는 병렬 시스템을 구축해야 하는가?
- 지금까지 성능 향상은 트랜지스터 밀도의 증가로 인해 가능
- 그러나 피할 수 없는 내재적인 문제들이 있음

### 물리학적 설명
- 작은 트랜지스터 = 빠른 프로세서
- 빠른 프로세서 = 증가된 전력 소비
- 증가된 전력 소비 = 증가된 열
- 증가된 열 = 불안정한 프로세스

### 해결책
- 단일 코어 시스템에서 멀티코어 프로세서로 전환
- 코어 (Core) = 중앙처리 장치 (CPU)

### 병렬 프로그램을 작성해야 하는 이유
- 직렬 프로그램의 여러 인스턴스를 실행하는 것은 종종 그다지 유용하지 않음
- 예를 들어, 좋아하는 게임을 여러 개 띄워서 실행시킨다면?
- 우리가 어떤 특정한 프로그램이 빨리 동작하는 것을 원함

### 직렬 프로그램을 어떻게 병렬 프로그램으로?
- 첫 번째 방법은 직렬 프로그램을 처음부터 다시 작성하여 병렬 프로그램으로 만드는 것 -> 시간이 너무 많이 든다는 단점
- 다른 방법으로는 직렬 프로그램을 병렬 프로그램으로 자동으로 변환 시켜주는 프로그램을 작성 -> 매우 어려운 과제이며 현재 성과는 제한적임

### 더 많은 문제들
- 일부 코딩 구조는 자동 프로그램 생성기에 의해 인식되어 병렬 구조로 변환 가능
- 그러나 결과물이 매우 비효율적인 프로그램일 가능성이 높음
- 가끔은 최상의 병렬 솔루션은 물러나서 완전히 새로운 알고리즘을 고안할 수 있음

## 직렬 컴퓨팅 VS 병렬 컴퓨팅 
### 직렬 해법
- n개의 값을 계산하고 그 값을 모두 더함

```C
sum = 0
for (i = 0; i < n; i++){
	x = Compute_next_value(...);
	sum += x;
}
```

- 각 코어가 코드 실행을 완료하면 개별 변수 my_sum에는 Compute_next_value 호출로 계산된 값들의 합이 포함된다.
- 예를 들어, 8개의 코어와 n=24가 있다면, Compute_next_value 호출은 다음과 같은 결과를 반환
- 1,4,3,  9,2,8,  5,1,1,  5,2,7,  2,5,0,  4,1,8,  6,5,1,  2,3,9
- 모든 코어가 개별 my_sum을 계산하는 작업을 완료하면, 그들은 결과를 지정된 마스터 코어에게 보내 전체 합계를 형성하며, 마스터 코어는 최종 결과를 더한다.

### 병렬 해법
- p개의 코어를 가지고 있으며, n보다 훨씬 작은 경우를 가정
- n은 전체 숫자의 갯수
- 각 코어는 대략 n/p개의 값을 부분 합산

```C
my_sum = 0
my_first_i = ...;
my_last_i = ...;
for (my_i = my_first_i; my_i < my_last_i; my_i++){
	my_x = Compute_next_value(...);
	my_sum += my_x;
}

if(Im the master core){
	sum+=my_x;
	for each core other than myself{
		receive value from core;
		sum += value;
	}
	else{
		send my_x to the master;
	}
}
```

|core|0|1|2|3|4|5|6|7|
|---|---|---|---|---|---|---|---|---|
|mysum|8|19|7|15|7|13|12|14|

- Global Sum: 8 + 19 + 7 + 15 + 7 + 13 + 12 + 14 = 95
	- master core = 0

|core|0|1|2|3|4|5|6|7|
|---|---|---|---|---|---|---|---|---|
|mysum|95|19|7|15|7|13|12|14|

## 좀 더 나은 병렬 알고리즘
- 마스터 코어에게 모든 작업을 하게 하지 말고 다른 코어들도 같이 일하도록 만든다.
- 코어들을 짝지어, 코어 0은 코어 1의 결과를 더한다.
- 코어 2는 코어 3의 결과를 더하는 식으로 진행하여 홀수 번호와 짝수 번호의 코어들의 쌍이 함께 작업하도록 한다.
- 이제 짝수 번호의 코어만을 가지고 동일한 프로세스를 반복한다.
- 코어 0은 코어 2의 결과를 더한다.
- 코어 4는 코어 6의 결과를 더하는 식으로 진행한다.
- 다음으로, 4로 나눌 수 있는 번호를 가진 코어들이 프로세스를 반복하고, 이런 식으로 진행한다.
- 최종 결과는 코어 0에 있게 된다.

## 분석
- 첫 번째 예제에서 마스터 코어는 7번의 수신, 7번의 덧셈을 수행
- 두 번째 예제에서 마스터 코어는 3번의 수신, 3번의 덧셈을 수행
- 따라서, 개선된 성능은 2배 이상이다!
- 더 많은 코어를 사용할 경우 차이가 더 현격해짐
	- 1000개의 코어라면
		- 첫 번째 예제에서 마스터 코어는 999번의 수신, 999번의 덧셈을 수행
		- 두 번째 예제에서 마스터 코어는 10번의 수신, 10번의 덧셈을 수행
- 이것은 거의 100배 가까운 성능 개선 !

## 병렬 프로그램은 어떻게 작성할까?
- 병렬 프로그램을 작성하는 방법은 크게 두 가지

### **작업 병렬화 (Task Parallelism)**
- 문제를 해결하는 데 수행되는 다양한 작업들을 코어들 사이에 나눠 분배

### **데이터 병렬화 (Data Parallelism)**
- 문제 해결에 사용되는 데이터를 코어들 사이에 나눠 분배
- 각 코어는 자신의 데이터 일부에 대해 유사한 작업을 수행

### 병렬 작업의 예
- 교수-조교 시험 채첨 비유 (15문제, 300개 시험지, 조교 3명)
- 작업 병렬화: 조교1-100개 시험지,조교2-100개 시험지,조교3-100개 시험지
- 데이터 병렬화: 조교1-문제1~5, 조교2-문제6~10, 조교3-문제11~15

## 작업 조율 (Coordination)
- 일반적으로 코어들은 작업을 조율
- 통신 (Communication)
	- 하나 이상의 코어가 현재의 부분 합계를 다른 코어에게 전송
- 작업 균형 (Load Balancing)
	- 작업을 고르게 분배하여 한 코어가 지나치게 많은 부담이 가지 않도록
- 동기화 (Synchronization)
	- 각 코어가 자체 속도로 작업하기 때문에 코어들이 다른 코어들보다 너무 앞서가지 않도록

## 병렬 프로그래밍의 예
- C/C++를 비롯한 프로그래밍 언어 사용
- 병렬 프로그래밍 모델 예
	- Message-Passing Interface (MPI)
	- Posix Threads (Pthreads)
	- OpenMP

## 병렬 컴퓨팅 시스템 종류
- **공유 메모리 (Shared-memory)**
	- 코어들은 컴퓨터의 메모리에 대한 접근을 공유할 수 있음
	- 코어들은 공유 메모리 위치를 검토하고 업데이트하도록 조정할 수 있음
- **분산 메모리 (Distributed-memory)**
	- 각 코어는 자체 개인 메모리를 가지고 있음
	- 코어들은 명시적으로 네트워크를 통해 메시지를 보내며 통신해야 함

## 유사 용어 비교
- **동시 컴퓨팅 (Concurrent Computing)**
	- 어느 시점에 여러 작업이 진행 중일 수 있는 프로그램
- **병렬 컴퓨팅 (Parallel Computing)**
	- 여러 작업이 긴밀하게 협력하여 문제를 해결하는 프로그램
- **분산 컴퓨팅 (Distributed Computing)**
	- 문제를 해결하기 위해 다른 프로그램과 협력해야 할 수도 있는 프로그램

# 결론
- 물리학의 법칙은 우리를 멀티코어 기술의 문턱으로 이끌었다. 
- 직렬 프로그램은 일반적으로 다중 코어에서 이득을 보기 어렵다. 
- 직렬 프로그램 코드에서 자동 병렬 프로그램 생성은 멀티코어 컴퓨터에서 고성능을 얻기 위한 가장 효율적인 접근 방식은 아니다.
- 병렬 프로그램을 작성하는 방법을 배우는 것은 코어들을 조정하는 방법을 배우는 것을 포함한다. 
- 병렬 프로그램은 보통 매우 복잡하므로, 견고한 프로그램 기술과 개발이 필요하다