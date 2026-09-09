```

[1/6] 파일 분석 중: ./ochy/nihms-348188.pdf
📌 제목: 지면 및 트레드밀 러닝 중 발 접지(Footstrike) 및 발 떼기(Toe-off)의 운동학적 식별 방법 비교
📝 요약: 본 연구는 지면 반력기(Forceplate)를 사용할 수 없는 환경에서 **운동학적 데이터(마커 기반)만으로 러닝의 지지기(Stance phase) 시작점인 발 접지(Footstrike)와 종료점인 발 떼기(Toe-off)를 정확하게 식별하는 방법**을 비교 분석했습니다. 5가지 운동학적 방법 중 발 접지는 뒤꿈치 마커의 최소 수직 위치와 수직 속도 변화가, 발 떼기는 최대 무릎 신전 시점이 가장 유효하고 신뢰할 수 있는 지표임을 밝혀냈습니다.
📏 수치 및 기준: 1. 분석 목적 및 ochy의 인사이트: Halpe-26과 같은 스켈레톤 데이터 기반의 러닝 분석 시스템을 구축하기 위해서는 보행 주기(Gait Cycle)를 정확히 나누는 것이 필수적입니다. 특히 지면 반력기 없이 2D/3D 좌표값만으로 '언제 발이 땅에 닿았고(FS), 언제 떨어졌는지(TO)'를 판별해야 케이던스, 지면 접촉 시간, 듀티 팩터 등의 핵심 지표를 산출할 수 있습니다. ochy는 이 논문을 통해 센서 없이 영상 기반 좌표 데이터만으로 지지기를 정의할 수 있는 가장 정밀한 수학적/역학적 트리거를 찾고자 했습니다.

2. 분석 흐름 및 중요 포인트: 지면 반력(vGRF) 20 N 초과/미만을 골드 스탠다드로 설정하고, 5가지 운동학적 추정 방법의 절대 오차(Absolute Error)를 비교하여 신뢰도를 검증하는 흐름으로 진행되었습니다.

3. 피쳐값 구현 방법 (구현 로직):
   포스플레이트(힘판)에서 측정한 '수직 지면 반발력(vGRF)'을 절대적인 정답 기준 -> goldstandard
- 발 접지(Footstrike) 식별 방법:  vGRF > 20뉴턴을 초과
  (1) FPOSV: 뒤꿈치(distal heel) 마커의 수직 위치가 최소값이 되는 시점.
  The minimum vertical position of the distal heel (DIHE) marker (on heel counter base) was used to identify FS." (Methods - 2. Foot Vertical Position)
  (2) FVELV: 뒤꿈치 마커의 수직 속도가 음수(-)에서 양수(+)로 변하는 시점. 
  The change in vertical velocities from negative to positive of the DIHE... markers was used to determine FS..." (Methods - 4. Foot Vertical Velocity)
  (3) FSDIS: 천골(sacrum)과 뒤꿈치 마커 사이의 진행 방향 변위가 최대 양수가 되는 시점.
FS was defined at the time of maximum positive displacement in the direction of progression between the sacrum and DIHE." (Methods - 3. Foot-Sacrum Displacement)
  (4) ANGA: 시상면에서 발의 각가속도가 국소 최소값(local minimum)을 갖는 시점.
  - FS was defined at the time of the local minimum of foot angular acceleration in the sagittal plane." (Methods - 5. Angular Acceleration)
    
- 발 떼기(Toe-off) 식별 방법: vGRF < 20 
  (1) PKEXT: 무릎 신전(knee extension)이 최대가 되는 시점 (가장 정확함, 오차 4.9ms에서 5.2ms).
  while the second peak knee extension was used to identify TO." (Methods - 1. Peak Knee Extension)
  Toe-off was best identified using peak knee extension, with absolute errors of 4.9 ms for overground running and 5.2 ms for treadmill running." (Abstract)
  
  (2) FPOSV: 두 번째 중족골두(2nd metatarsal head) 마커의 수직 위치가 최소값이 되는 시점.
  The minimum vertical position of the 2nd metatarsal head (MTH2) marker was used to identify TO." (Methods - 2. Foot Vertical Position)
  (3) FSDIS: 두 번째 중족골두와 천골 사이의 전후 변위가 최대 음수가 되는 시점.
  - TO was defined as the maximum negative displacement, along the lab anterior-posterior axis, between the MTH2 and sacrum." (Methods - 3. Foot-Sacrum Displacement)
  (4) FVELV: 두 번째 중족골두 마커의 수직 속도가 음수(-)에서 양수(+)로 변하는 시점.
  "The change in vertical velocities from negative to positive of the... MTH2 markers was used to determine... TO, respectively." (Methods - 4. Foot Vertical Velocity)
  (5) ANGA: 시상면에서 정강이(shank)의 각가속도가 국소 최소값을 갖는 시점.
 TO was defined at the time of the local minimum of the shank angular acceleration in the sagittal plane" (Methods - 5. Angular Acceleration)

4. 올바른 자세 수치 및 부상 인과관계: 내용 없음 (본 논문은 '올바른 자세'의 기준이 아닌, '이벤트 식별 방법'의 정확도를 다루는 논문임).
"Our results suggested that different kinematic methods should be used to identify FS and TO. Specifically, **FVELV and FPOSV were most accurate for FS and PKEXT was most accurate for TO.**"

- For identifying footstrike, foot vertical velocity (FPOSV) and foot vertical position (FVELV) were the **most valid and consistent** for overground and treadmill running."
    
- "For identifying toe-off, peak knee extension (PKEXT) was the **most valid and consistent** for overground and treadmill running."
  
[2/6] 파일 분석 중: ./ochy/Ochy_AI_Validation.pdf
📌 제목: 스마트폰 애플리케이션을 이용한 달리기 자세 분석의 정밀도
📝 요약: 본 연구는 달리기 분석에 특화되어 학습된 포즈 추정 모델인 'Ochy'와 범용 모델인 'Mediapipe'의 성능을 마커 기반 모션 캡처 시스템(Qualisys)과 **비교 검증**한 논문입니다. 연구 결과, Ochy 모델이 시공간적 파라미터(걸음 빈도, 지면 접촉 시간, 스윙 시간)와 주요 관절 각도(무릎, 고관절 등) 추정에서 Mediapipe보다 유의미하게 낮은 오차와 높은 상관관계를 보이며 더 정밀한 분석이 가능함을 입증하였습니다.
📏 수치 및 기준: 1. 분석 목적 및 흐름: Ochy는 기존의 범용 포즈 추정 모델(Mediapipe 등)이 정적 또는 느린 동작 데이터로 학습되어, 달리기의 빠른 사지 움직임, 짧은 지면 접촉 시간, 관절 가려짐(occlusion) 상황에서 정확도가 떨어진다는 문제점에 주목했습니다. 이를 해결하기 위해 달리기 동작에 특화된 커스텀 모델(Ochy)을 개발하였고, 단일 카메라 및 낮은 프레임레이트(60Hz)라는 실제 스마트폰 환경에서도 신뢰할 수 있는 생체역학적 지표를 추출할 수 있는지 검증하고자 했습니다.

2. 중요 분석 포인트 및 인사이트: Ochy는 특히 무릎과 고관절의 각도 추정 오차를 줄이는 데 집중했으며, 이는 달리기 자세 분석의 핵심 지표이기 때문입니다.especially at the knee and hip joints, confirming the primary hypothesis.

Among all joints, the knee exhibited the lowest MAE at each speed, followed by the hip, ankle, and elbow. This result is **consistent with the biomechanical nature of running**, where knee motion primarily occurs in the sagittal plane..." 또한, 속도가 느릴수록 모션 블러가 감소하여 정확도가 높아진다는 가설을 통해 데이터 수집 환경의 중요성을 분석했습니다. 이를 통해 **고가의 장비 없이도 스마트폰 기반의 실용적인 달리기 자세 분석 도구를 구현할 수 있다는 인사이트**를 얻고자 했습니다.

3. 피쳐값 구현 방법: 포즈 추정 모델(Ochy, Mediapipe)을 통해 추출된 2D 관절 좌표 데이터를 기반으로 다음의 지표들을 계산하였습니다. 
(1) 시공간적 파라미터: 걸음 빈도(step frequency), 지면 접촉 시간(ground contact time), 스윙 시간(swing time)을 계산함. 
> "From the identified foot strike and toe-off events, we computed the **step frequency** (steps per minute) as the inverse of step duration, **ground contact time** (s) defined as the duration the foot remained in contact with the ground during the gait cycle and **swing time** (s), defined as the period during which the foot was off the ground within a gait cycle."
>
(2) 운동학적 파라미터: 하체 및 상체 관절 각도(무릎, 발목, 고관절, 팔꿈치)를 계산함. 구체적인 수학적 공식은 명시되지 않았으나, 모델이 출력한 관절 좌표 간의 각도 및 시간차를 이용해 구현되었습니다.
For both systems, sagittal-plane joint angles were computed for the **hip** (flexion/extension), **knee** (flexion/extension), **ankle** (dorsiflexion/plantarflexion), and **elbow** (flexion/extension) **using 2D coordinates of anatomical landmarks**."

4. 올바른 달리기 자세 수치: 내용 없음 (본 논문은 올바른 자세의 기준치를 제시하는 것이 아니라, 측정 도구의 정밀도를 검증하는 연구임)
"These methods rely on **analyzing characteristic patterns in joint trajectories (e.g., vertical position and velocity of the ankle marker) to infer gait events from 2D motion data**."

For the markerless system, foot strike and toe-off were estimated using kinematic-based methods: foot strike was determined using the approach described by [10], while toe-off was identified following the method outlined by [11]. These methods rely on analyzing characteristic patterns in joint trajectories (e.g., vertical position and velocity of the ankle marker) to infer gait events from 2D motion data.

[10]: Milner, C. E. & Paquette, M. R. A kinematic method to detect foot contact during running for all foot strike patterns. Journal of biomechanics 48, 3502–3505 (2015).
[11]: Fellin, R. E., Rose, W. C., Royer, T. D. & Davis, I. S. Comparison of methods for kinematic identification of footstrike and toe-off during overground and treadmill running. Journal of science and medicine in sport 13, 646–650 (2010).
4. 부상 인과관계 및 예측 지표: 내용 없음

[3/6] 파일 분석 중: ./ochy/running-technique-is-an-important-component-of-running.pdf
📌 제목: 달리기 기술이 달리기 경제성과 퍼포먼스의 중요한 구성 요소이다
📝 요약: 97명의 다양한 수준의 러너를 대상으로 3D 운동학적 분석을 수행하여, 달리기 기술(kinematics)이 에너지 효율(Running Economy, RE)과 실제 경기 성적(Season's Best, SB)에 미치는 영향을 분석한 연구입니다. 분석 결과, 특정 관절 각도와 골반 움직임 등의 지표가 에너지 소비량과 퍼포먼스의 상당 부분을 설명한다는 것을 입증하였습니다.
📏 수치 및 기준: 1. 분석 목적 및 흐름: ochy는 '올바른 자세'에 대한 직관적인 믿음을 넘어, 실제로 어떤 운동학적 지표가 에너지 효율(LEc)을 높이고 성적(SB)을 향상시키는지 통계적 근거를 찾기 위해 이 논문을 분석했습니다. **연구진은 운동학적 지표를 5가지 카테고리(수직 진동, 제동, 자세, 보폭 파라미터, 하지 각도)로 분류**하고, 이를 에너지 비용 및 성적과 **상관관계 및 회귀 분석**으로 연결하는 흐름으로 진행했습니다.

2. 구체적인 역학적 기준 및 수치:
- 에너지 효율(RE/LEc) 결정 지표 (변동성의 39퍼센트 설명): 
  - 신장 대비 지면 접촉 중 골반 수직 진동 (Pelvis vertical oscillation during ground contact normalized to height)
  - 지면 접촉 중 최소 무릎 관절 각도 (Minimum knee joint angle during ground contact)
  - 최소 수평 골반 속도 (Minimum horizontal pelvis velocity)
- 퍼포먼스(SB time) 결정 지표 (변동성의 31퍼센트 설명):
  - 최소 수평 골반 속도 (Minimum horizontal pelvis velocity)
  - 정강이 접지 각도 (Shank touchdown angle)
  - 듀티 팩터 (Duty factor)
  - 상체 전경 자세 (Trunk forward lean)

Sagittal plane foot angle at touchdown xFATD 8.87 T 9.39 (j11.4, 24.0) 
Sagittal plane shank angle at touchdown xSATD 8.99 T 3.47 (0.835, 16.4) 
Sagittal plane thigh angle at touchdow
2. 부상 및 이상 징후 인과관계: 내용 없음

3. 피쳐 구현 방법: 56개의 역반사 마커를 관절 중심과 신체 랜드마크에 부착하고 3D 자동 모션 캡처 시스템을 사용하여 측정했습니다. 17개 신체 세그먼트에 대해 '감소된 관성 모델(reduced set inertia model)'을 적용하여 세그먼트별 관성 파라미터를 계산했으며, 이를 통해 관절 중심 위치와 신체 질량 중심(CM)의 위치를 결정하여 각도와 속도 데이터를 추출했습니다.

4. ochy의 인사이트: 2D 스켈레톤 데이터에서 추출 가능한 '정강이 접지 각도', '듀티 팩터', '상체 기울기' 등이 실제 러닝 퍼포먼스와 직결되는 핵심 지표임을 확인했습니다. 이를 통해 AI 에이전트가 사용자에게 단순한 자세 교정이 아닌, 에너지 효율과 성적 향상을 위한 구체적인 수치 기반의 피드백을 제공할 수 있는 근거를 확보하고자 했습니다.
피드백 가이드 라인
- **수직 진동과 제동은 최소화:** 골반이 위아래로 튀는 현상(수직 진동)과 발이 땅에 닿을 때 앞으로 나아가는 속도가 줄어드는 현상(최소 수평 골반 속도의 절대값)은 **작을수록 좋습니다.**
    
- **지면 접촉 시간 및 비율(Duty Factor) 감소:** 전체 보폭 주기 중 발이 땅에 닿아있는 비율(DF)은 **낮을수록 퍼포먼스에 유리**합니다.
    
- **발 닿을 때 정강이는 수직에 가깝게:** 발이 땅에 닿는 순간 정강이가 앞으로 뻗어나간 각도($xSA_{TD}$)는 **작을수록(즉, 수직에 가까울수록)** 제동을 줄이고 퍼포먼스를 높입니다 (오버스트라이딩 방지).
    
- **스탠스 구간에서 무릎 굽힘 최소화:** 지면에 발이 닿아 체중을 지지할 때 무릎이 과도하게 굽혀지지 않고 **곧게 펴져 있을수록(각도가 0에 가까울수록)** 다리의 강성(Stiffness)이 유지되어 에너지 효율이 높습니다.
  Hence, we recommend consistent forward velocity of the pelvis, with minimal vertical oscillation and transverse rotation for enhancing economy and performance, and it may be that improving the stride parameters (DF, GCT, and SLH) and lower limb angles identified (xSATD, $xKAGC, and $xHAGC xKAGC,MIN) may help to enhance these aspects of pelvis movement. Given the apparent importance of technique to performance, it is recommended that runners dedicate an appropriate proportion of their preparation to technical development 

It is therefore recommended that runners and coaches be attentive to stride parameters (lower DF, shorter GCT, and shorter stride length) and lower limb angles (more vertical shank and plantarflexed foot at touchdown, and a smaller range of motion of the knee and hip during stance) in part to optimize pelvis movement (minimal braking, vertical oscillation, and transverse rotation), and ultimately enhance performance

[4/6] 파일 분석 중: ./ochy/40279_2016_Article_474.pdf
📌 제목: 경제적인 달리기 기술이 존재하는가? 달리기 효율에 영향을 미치는 수정 가능한 생체역학적 요인에 대한 리뷰
📝 요약: 이 논문은 달리기 효율(Running Economy, RE)에 영향을 미치는 수정 가능한 생체역학적 요인들을 분석한 리뷰 논문입니다. 연구 결과, 적절한 보폭 범위 유지, 낮은 수직 진폭, 높은 다리 강성, 발가락 이지 시 적은 다리 신전, 큰 보폭 각도 등이 RE 향상에 기여하며, 특히 추진 단계의 역학적 요소가 RE와 가장 밀접한 관련이 있음을 밝혀냈습니다.
📏 수치 및 기준: ochy는 사용자의 달리기 자세를 평가하고 효율성을 높이는 피드백을 제공하기 위해, 단순히 '좋은 자세'라는 추상적 개념이 아닌 '에너지 효율(RE)'이라는 정량적 지표와 연결된 생체역학적 근거를 찾고자 이 논문을 분석하였습니다. 분석의 흐름은 RE의 정의에서 시작하여 시공간적 요인, 운동학적 요인, 운동역학적 요인, 신경근 요인 순으로 효율성에 기여하는 요소를 추출하는 방식으로 진행되었습니다.

1. 분석 기준 및 수치
- 보폭(Stride Length): 선호하는 보폭에서 마이너스 3퍼센트에서 선호 보폭까지의 범위를 최적 범위로 봅니다. 선호 보폭보다 6퍼센트 이상 벗어날 경우 RE에 부정적인 영향을 미칩니다.
  Collectively, these results suggest there is an optimal stride length 'range' that trained runners can acutely adopt without compromising their RE. This range appears to be the preferred stride length minus 3% to the preferred stride length."
  
whereas stride length deviations greater than 6% are detrimental to RE
  **Halpe26 추출 방법:** 양발의 발뒤꿈치(Heel) 또는 발끝(Toe) 키포인트가 지면에 닿는 시점(Initial Contact) 간의 2D 픽셀/실측 거리를 계산
- 수직 진폭(Vertical Oscillation): 수직 진폭이 낮을수록 체중 지지에 필요한 대사 비용과 중력에 대항하는 기계적 에너지 비용이 감소하여 RE가 향상됩니다.

Table 1의 Beneficial에 근거 지표 있음 
- 발가락 이지(Toe-off): 발가락이 지면에서 떨어질 때 다리 신전(Leg extension)이 적을수록 효율적입니다.
  comparisons as being beneficial for RE is a less extended leg at toe-off... Evidence has shown that this can be achieved through less plantarflexion and/or less knee extension as the runner pushges off the ground
- Leg Extension at Toe-off
"One of the few kinematic variables to have strong support from both cross- and intra-individual comparisons as being beneficial for RE is a less extended leg at toe-ofㄱ

- 보폭 각도(Stride Angles): 더 큰 보폭 각도가 RE에 유익한 것으로 나타났습니다.
  Another kinematic during the push-off phase that has been associated with better RE is stride angle
, which is defined as the angle of the parabletangent of the CoM at toe-off...

- 다리 강성(Leg Stiffness): 더 높은 다리 강성이 RE 향상에 도움이 됩니다.
Several intrinsic factors that appear to benefit RE are ... **greater leg stiffness**..." (Conclusion)
- 관성 모멘트(Moment of Inertia): 하지의 관성 모멘트가 낮을수록 효율적입니다.
potentially reduce the leg’s moment of inertia, lowering the energy required to flex the leg during the swing phase
  
- 추진 단계(Propulsion): 지면 반력(GRF)과 다리 축이 일직선으로 정렬될 때 효율이 높아집니다.(GRF는 3D로만 구학 ㅣ가능)
- 상체: 팔 흔들기(Arm swing)를 유지하는 것이 중요합니다.

2. 부상 및 이상 징후 인과관계
- 보폭의 과도한 변화(6퍼센트 이상)는 에너지 효율을 급격히 떨어뜨려 조기 피로를 유발할 수 있습니다.
- 높은 수직 진폭은 불필요한 에너지 소모를 증가시켜 달리기 성능을 저하시키는 지표가 됩니다.

3. 피쳐값 구현 방법
- 해당 논문은 기존 연구들을 종합한 리뷰 논문으로, 2D 스켈레톤 데이터 등을 이용한 구체적인 피쳐 구현 알고리즘이나 수식은 제시되지 않았습니다. (없음)

[5/6] 파일 분석 중: ./ochy/1-s2.0-S0021929020306114-main.pdf
📌 제목: 원형으로 달리기: 푸리에 급수를 이용한 달리기 운동학 기술
📝 요약: 본 연구는 인간의 달리기 운동학(kinematics)을 효율적으로 표현하기 위해 푸리에 급수(Fourier series)의 유효성을 탐색했습니다. 78명의 러너로부터 수집한 285회의 트레드밀 주행 데이터를 분석한 결과, 5개 이하의 푸리에 계수 쌍만으로도 대부분의 관절 각도를 매우 정확하게(RMSD 0.5도 미만, 상관계수 0.99 초과) 재현할 수 있음을 확인했습니다. 이는 기존 시계열 데이터보다 약 10배 더 압축된 형태로 달리기 자세를 저장하고 분석할 수 있음을 시사합니다.
📏 수치 및 기준: 1. 분석 목적 및 흐름: 본 논문은 '올바른 자세'의 수치를 정의하는 것이 아니라, 복잡한 달리기 관절 움직임을 어떻게 하면 가장 효율적이고 정확하게 수학적으로 표현(Representation)할 수 있는지를 연구했습니다. 분석 흐름은 [마커 데이터 수집 -> 근골격 모델을 통한 관절 각도 추출 -> FFT를 통한 스트라이드 주파수 결정 -> 평균 스트라이드 계산 -> 푸리에 급수 근사화 -> 원본 데이터와 비교 검증] 순으로 진행되었습니다.

2. ochy의 인사이트 및 사용 이유: ochy는 Halpe-26과 같은 2D 스켈레톤 데이터에서 매 프레임의 좌표값을 저장하는 대신, 달리기라는 주기적 운동의 특성을 이용하여 이를 몇 개의 '계수(Coefficient)'로 압축하여 표현하는 방법론을 찾기 위해 이 논문을 분석했습니다. 이를 통해 개별 러너의 자세 패턴을 단순한 수치 세트로 변환하여, 표준 자세와의 차이를 빠르게 계산하거나 데이터 저장 용량을 획기적으로 줄이는 인사이트를 얻고자 했습니다.

3. 피쳐값 구현 방법: 
- 데이터 추출: 3D 모션 캡처 마커 데이터를 AnyBody 모델링 시스템에 입력하여 85개의 독립적인 관절 각도(예: 발목 저측 굴곡, 고관절 굴곡 등)를 추출했습니다.
- 주파수 결정: FFT(고속 푸리에 변환)를 사용하여 모든 관절 각도에서 공통적인 기본 주파수(stride frequency, x)를 찾아냈습니다.
- 스트라이드 분할: 주기 T = 2pi / x를 계산하여 전체 기록을 개별 스트라이드 단위로 분할하고, 이를 평균하여 '평균 스트라이드' 시계열 데이터를 생성했습니다.
- 푸리에 계수 산출: 평균 스트라이드 데이터 f(t)를 푸리에 급수 식(yt = a0 + sum(ai cos(ixt) + bi sin(ixt)))에 대입하여, 적분 과정을 통해 각 차수(i=1~10)에 해당하는 계수 ai와 bi 값을 산출했습니다.
- 검증: 산출된 계수로 복원한 곡선과 원본 시계열 데이터 간의 RMSD(제곱평균제곱근 오차)와 피어슨 상관계수(r)를 계산하여 정확도를 평가했습니다.

4. 올바른 달리기 자세 수치: 내용 없음

5. 부상 및 이상 징후 인과관계: 내용 없음

[6/6] 파일 분석 중: ./ochy/jeb192047.pdf
📌 제목: 지면 접촉 시간이 러닝 효율성에 미치는 영향: 짧은 것이 항상 더 좋은 것은 아니다
📝 요약: 이 연구는 지면 접촉 시간의 비율인 듀티 팩터(Duty Factor, DF)가 낮은 그룹(DFlow)과 높은 그룹(DFhigh) 간의 무게중심(COM) 이동과 에너지 비용(EC)을 비교 분석했습니다. 분석 결과, DFlow 그룹은 제동 및 추진 단계에서 더 대칭적인 패턴을 보였고, DFhigh 그룹은 수직 이동을 제한하고 수평 전진을 선호하는 경향을 보였습니다. 하지만 두 그룹 간의 에너지 효율성(EC)에는 유의미한 차이가 없었으며, 이는 개인의 신체 조건에 따라 서로 다른 러닝 전략이 동일하게 효율적일 수 있음을 시사합니다.
📏 수치 및 기준: 1. 분석 목적 및 ochy의 인사이트: ochy는 '올바른 달리기 자세'의 절대적 기준을 찾기 위해 이 논문을 분석했습니다. 특히 지면 접촉 시간(tc)을 줄이는 것이 무조건 효율적인지, 아니면 특정 수치(DF)가 최적인지를 확인하여 2D 스켈레톤 데이터에서 추출할 피드백 지표를 설정하고자 했습니다. 이를 통해 '단일한 정답 자세'보다는 '에너지 효율적인 전략의 다양성'이라는 인사이트를 얻었으며, DF 수치에 따라 서로 다른 역학적 특성이 나타남을 확인했습니다.

2. 역학적 기준 및 수치:
   . Overall, the two running forms (i.e. high and low DF), which can be distinguished by a simple measurement of running step temporal parameter
- 듀티 팩터(DF) 기준: 일반적인 러닝 시 DF 값은 0.500 미만이어야 합니다.
- DFlow 그룹(낮은 DF) 평균: 0.330 +- 0.018
- DFhigh 그룹(높은 DF) 평균: 0.385 +- 0.028
- DFlow 특성: 제동(braking)과 추진(propulsion) 단계의 시간 및 수직 무게중심(COM) 변위가 더 대칭적이며, 탄성 에너지 재사용(spring-mass model)에 더 가깝습니다.
- DFhigh 특성: 지면 접촉 중 수직 COM 변위를 제한하고 수평 전진을 더 선호하는 전략을 사용합니다.
# 💡 프로젝트 적용 시 매우 중요한 주의사항: "영상 프레임 레이트(FPS)"

이 논문의 Table 3를 보면 러너들의 지면 접촉 시간($t_c$)은 보통 **0.17초 ~ 0.28초** 사이로 매우 짧습니다.

- 만약 일반적인 환경의 **30 FPS (초당 30장)** 영상으로 분석한다면, 0.2초는 불과 **6프레임** 단위로 쪼개집니다. 모델이 1~2프레임만 오차를 내도 DF 수치에 큰 왜곡이 생기게 됩니다.
    
- 따라서 이 피쳐를 모델에서 정확하게 추출하시려면, 분석에 사용할 입력 영상이 최소 60 FPS 이상 (권장 120~240 FPS의 스마트폰 슬로우 모션 영상)이어야 데이터의 신뢰도를 확보할 수 있습니다.

3. 부상 및 이상 징후 인과관계: 구체적인 부상 예측 지표는 명시되지 않았으나, 수직 강성(vertical stiffness)이 무한히 증가할 수 없으며 지면 접촉 시 해부학적 구조의 무결성을 보존하기 위해 제한된다는 점을 언급하여, 과도한 강성 추구가 신체 구조에 부담을 줄 수 있음을 시사합니다.

4. 피쳐값 구현 방법:
- 데이터 수집: 7개의 적외선 카메라와 35개의 반사 마커를 사용하여 200Hz로 3D 운동학 데이터 수집.
- 무게중심(COM) 계산: 신체를 15개의 강체 세그먼트(머리, 팔, 몸통, 골반, 허벅지, 종아리, 발 등)로 나누고, 각 세그먼트의 형상과 표준 회귀 방정식을 기반으로 질량 및 COM 위치를 할당하여 전신 COM을 계산함.
- 이벤트 정의:
  - Footstrike(착지): 발 중간 지점(mid-foot landmark)의 수직 속도가 정점 수직 속도에 도달하기 전 국소 최소값에 도달하는 시점.
  - Toe-off(이지): 발가락 마커가 수직 위치 7cm에 도달하기 전 수직 가속도가 정점에 도달하는 시점.
- 시간 지표 구현:
  - tc(지면 접촉 시간): 착지부터 이지까지의 시간.
  - ts(스윙 시간): 이지부터 다음 착지까지의 시간.
  - ta(공중 체류 시간): 한쪽 발의 이지부터 반대쪽 발의 착지까지의 시간.
- DF(듀티 팩터): 러닝 스텝 중 지면 접촉 시간(tc)이 차지하는 상대적 비율로 계산함.
  
  ### 골반(Pelvis/Mid-Hip) 중심점을 COM으로 대체 (가장 추천)

가장 보편적이고 실용적인 방법입니다. 달리기 시 사람의 신체 무게중심은 보통 배꼽 살짝 아래인 골반 근처에 위치합니다.

- **추출 방법:** Halpe26에서 출력되는 왼쪽 골반(L_Hip)과 오른쪽 골반(R_Hip) 키포인트의 2D 좌표(x, y)의 중간점(Mid-point)을 구합니다.
    
- **활용:** 이 점을 2D COM으로 간주하고 프레임별로 추적하면, 이 논문에서 다루는 핵심 지표인 전체 수직 진동폭($\Delta z$)이나 접촉 시 수평 이동($\Delta y_c$)을 아주 쉽게 계산할 수 있습니다.
    

### 2. 2D 인체 분절 파라미터(Anthropometric Tables) 적용 (더 정교한 방법)

신체 각 부위의 평균 무게 비율(예: 뎀스터(Dempster) 또는 드 레바(de Leva)의 인체 분절 데이터)을 Halpe26 키포인트에 가중치로 곱해서 구하는 방식입니다. (참고로 보고 계신 논문에서도 Dempster의 방식을 3D로 사용했습니다.)

- **추출 방법:** Halpe26이 머리, 몸통, 허벅지, 종아리, 팔 등의 키포인트를 모두 잡아주므로, 각 분절의 2D 중심점을 구합니다.
    
- **계산식 예시:** $X_{com} = \sum (각 관절 X좌표 \times 해당 부위 질량비율)$
    
- **활용:** 팔다리가 앞뒤로 교차할 때 미세하게 변하는 무게중심의 이동까지 2D 상에서 수학적으로 꽤 정교하게 추정할 수 있습니

==================================================
```
