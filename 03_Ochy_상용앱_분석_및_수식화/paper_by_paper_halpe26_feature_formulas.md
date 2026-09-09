# Ochy 관련 6편 논문별 Halpe-26 피처 및 공식 정리

## 1. 문서 목적

이 문서는 여섯 편의 논문을 다음 순서로 다시 정리한다.

1. 논문의 목적
2. 실험 대상과 방법
3. Halpe-26 2D 키포인트로 계산할 수 있는 피처와 공식
4. 논문에서 참고할 수 있는 기준점 또는 수치
5. 현재 프로젝트에서 구현할 때의 한계
6. 측면 MVP에서 이 논문을 읽어야 하는 정도

여기서 `기준점`은 정상과 비정상을 나누는 임계값을 의미하지 않는다. 논문에 실제로 제시된 사건 검출 기준, 측정 오차, 연구 집단 평균 또는 파형 재현 기준을 뜻한다.

## 2. 공통 좌표와 수식 정의

### 2.1 사용할 Halpe-26 키포인트

| 신체 부위 | Halpe-26 키포인트 |
|---|---|
| 어깨 | left/right shoulder, index 5/6 |
| 팔꿈치 | left/right elbow, index 7/8 |
| 손목 | left/right wrist, index 9/10 |
| 고관절 | left/right hip, index 11/12 |
| 무릎 | left/right knee, index 13/14 |
| 발목 | left/right ankle, index 15/16 |
| 목 | neck, index 18 |
| 골반 중심 | hip center, index 19 |
| 엄지발가락 | left/right big toe, index 20/21 |
| 새끼발가락 | left/right small toe, index 22/23 |
| 뒤꿈치 | left/right heel, index 24/25 |

좌우 hip의 중간점은 다음과 같이 다시 계산해도 된다.

$$
P_t = \frac{H_{L,t} + H_{R,t}}{2}
$$

여기서 $P_t$는 프레임 $t$의 골반 중심이다. 이 점은 실제 전신 COM이 아니라 `pelvis center proxy`다.

발 앞부분은 big toe와 small toe의 평균으로 정의한다.

$$
T_t = \frac{T_{big,t} + T_{small,t}}{2}
$$

### 2.2 화면 좌표계

- 원점은 영상 왼쪽 위다.
- $x$는 오른쪽으로 증가한다.
- $y$는 아래쪽으로 증가한다.
- 화면상 오른쪽으로 달리면 $d=+1$, 왼쪽으로 달리면 $d=-1$로 둔다.
- 위쪽 높이가 필요할 때는 $h=-y$로 변환한다.
- FPS를 $f$라고 하고 프레임 간격은 $\Delta t=1/f$로 둔다.

### 2.3 세 점 관절각

점 $A-B-C$에서 $B$를 꼭짓점으로 하는 각도는 다음과 같다.

$$
\theta(A,B,C)=\cos^{-1}\left(
\frac{(A-B)\cdot(C-B)}{\|A-B\|\|C-B\|}
\right)\frac{180}{\pi}
$$

무릎 굴곡각은 다리가 완전히 펴졌을 때 0도가 되도록 계산한다.

$$
KneeFlex_t = 180^\circ - \theta(Hip_t,Knee_t,Ankle_t)
$$

### 2.4 신체 크기 정규화

카메라 보정이 없으므로 픽셀 거리를 m 또는 cm로 바꾸지 않는다. 영상 속 신체 크기 $L$을 사용하여 비율로 표현한다.

$$
L = median(ShoulderCenter-Pelvis + Pelvis-Knee + Knee-Ankle)
$$

논문에서 standing height로 정규화한 값과 현재 $L$ 정규화값은 같지 않다. 프로젝트 결과에는 `body scale normalized proxy`라고 표시한다.

### 2.5 데이터 품질 조건

모든 피처는 계산에 필요한 키포인트 신뢰도 $c$가 임계값 $\tau$ 이상일 때만 사용한다.

$$
valid_t = \mathbb{1}[\min(c_{required,t}) \ge \tau]
$$

현재 PoC는 전체 관절점 비율만 품질에 사용하지만, 앞으로는 피처별 필수 키포인트 신뢰도를 따로 검사해야 한다.

---

## 3. 논문 1 - Footstrike와 Toe-off 운동학적 식별 방법

### 3.1 목적

힘판을 사용할 수 없는 러닝 분석에서 운동학적 마커 좌표만으로 Initial Contact와 Toe-off를 정확하게 찾을 수 있는 방법을 비교하는 연구다.

이 논문은 올바른 자세나 코칭 기준을 제공하지 않는다. 다른 모든 시간 및 접지 구간 피처를 계산하기 위한 사건 검출 근거다.

### 3.2 실험 대상과 방법

- 지면 달리기 참가자 20명
- 평균 연령 약 25세, 남성 10명
- 모두 rearfoot striker
- 주당 16 km 이상 달리는 recreational runner
- 지면 달리기 속도 3.35 m/s
- 뒤꿈치, 발, 정강이, 허벅지와 골반에 반사 마커 부착
- 힘판의 수직 지면반력으로 사건 정답 생성
- 지면과 트레드밀에서 다섯 가지 운동학적 방법 비교

힘판 정답은 다음과 같다.

- Footstrike: vGRF가 20 N을 초과하는 시점
- Toe-off: vGRF가 20 N 아래로 내려가는 시점

### 3.3 Halpe-26으로 구할 수 있는 피처와 공식

#### A. 뒤꿈치 최소 높이 기반 Initial Contact

논문의 실제 수직 위치는 위쪽이 양수지만 영상에서는 $y$가 아래로 증가한다. 따라서 뒤꿈치가 가장 낮은 시점은 $y$의 국소 최댓값이다.

$$
IC_{heel-pos}=\operatorname*{arg\,max}_{t\in W} y_{heel}(t)
$$

$W$는 예상 착지 주변 검색 구간이다. 영상 전체의 최댓값을 사용하면 다른 stride와 혼동되므로 발이 하강하는 구간마다 국소값을 찾아야 한다.

#### B. 뒤꿈치 수직 속도 변화 기반 Initial Contact

위쪽 높이 $h=-y$를 사용해 중앙차분 속도를 계산한다.

$$
v_h(t)=\frac{h(t+1)-h(t-1)}{2\Delta t}
$$

뒤꿈치가 하강할 때 $v_h<0$, 상승할 때 $v_h>0$가 된다.

$$
IC_{heel-vel}=t \quad where \quad v_h(t-1)<0,\;v_h(t)\ge0
$$

실제 구현에서는 속도 신호를 먼저 평활화하고 예상 착지 구간 안의 zero crossing만 선택한다.

#### C. 최대 무릎 신전 기반 Toe-off

무릎 굴곡각이 최소인 시점이 최대 무릎 신전이다.

$$
TO_{knee}=\operatorname*{arg\,min}_{t\in[IC,next\;IC)} KneeFlex_t
$$

논문에서는 보행 주기 안의 두 번째 peak knee extension을 Toe-off로 사용했다. 현재 코드에 적용할 때는 Initial Contact 이후부터 같은 발의 다음 Initial Contact 이전으로 검색 범위를 제한해야 한다.

#### D. 사건 기반 시간 피처

사건이 확정되면 다음을 계산한다.

$$
GCT = \frac{TO-IC}{f}
$$

$$
SwingTime = \frac{IC_{next,same}-TO}{f}
$$

### 3.4 기준점

- Footstrike 최선 후보의 절대 오차: 약 22.4-24.6 ms
- Toe-off 최대 무릎 신전 방법의 절대 오차: 지면 약 4.9 ms, 트레드밀 약 5 ms
- 위 값은 고속 3D 마커와 힘판을 사용한 연구 오차이며 우리 시스템의 성능이 아니다.

### 3.5 프로젝트 한계

- 연구 참가자가 모두 rearfoot striker다.
- Halpe-26 뒤꿈치는 신발에 붙인 실제 마커보다 불안정하다.
- 25fps에서는 한 프레임이 40 ms이므로 논문의 Footstrike 오차보다 시간 해상도가 낮다.
- 속도 미분은 키포인트 노이즈를 증폭한다.
- 최대 무릎 신전이 항상 실제 Toe-off와 일치한다고 보장할 수 없다.
- 사람 정답 프레임과 비교하는 자체 검증이 필요하다.

### 3.6 읽을 필요성

**반드시 봐야 한다.** Initial Contact와 Toe-off는 cadence, GCT, Swing Time, Duty Factor, 착지 시 각도와 접지 중 ROM의 기반이다. 다만 논문 수식을 그대로 복사하기보다 Halpe-26과 FPS 조건에서 후보 방법을 비교 검증해야 한다.

---

## 4. 논문 2 - Ochy 스마트폰 러닝 자세 분석 검증

### 4.1 목적

단일 저프레임 카메라에서 Ochy와 MediaPipe가 계산한 시공간 및 관절각 피처를 3D 모션 캡처와 지면반력 정답에 비교해 측정 정확도를 검증한다.

이 논문은 올바른 자세 수치를 제시하지 않는다. 측면 2D 영상에서 어떤 피처가 상대적으로 안정적이고 어떤 피처가 불안정한지를 판단하는 자료다.

### 4.2 실험 대상과 방법

- 참가자 9명
- 느린 속도, 편안한 속도, 빠른 속도의 지면 달리기
- 단일 RGB 카메라 60Hz
- Qualisys 마커 기반 모션 캡처 240Hz
- 지면반력 500Hz
- 마커 데이터: 12Hz 4차 zero-lag Butterworth filter
- 2D markerless 좌표: median filter 후 4Hz low-pass filter
- Ochy와 MediaPipe 결과를 MAE, RMSE, ICC, Bland-Altman으로 평가

### 4.3 Halpe-26으로 구할 수 있는 피처와 공식

#### A. Step Frequency 또는 Cadence

연속된 좌우 Initial Contact가 정상적으로 교대한다면:

$$
Cadence = \frac{60}{median((IC_{i+1}-IC_i)/f)}
$$

단위는 steps/min이다.

같은 발의 연속 Initial Contact를 이용하면 한 간격은 stride이므로:

$$
Cadence = \frac{120}{median((IC_{next,same}-IC_{same})/f)}
$$

#### B. Ground Contact Time과 Swing Time

$$
GCT_s=\frac{TO_s-IC_s}{f}
$$

$$
Swing_s=\frac{IC_{next,s}-TO_s}{f}
$$

좌우 평균뿐 아니라 median, IQR, 제외한 사건 수를 함께 저장하는 것이 좋다.

#### C. 무릎 굴곡각

$$
KneeFlex_t=180^\circ-\theta(Hip_t,Knee_t,Ankle_t)
$$

착지 한 프레임, 접지 중 최대값 또는 전체 gait cycle 파형으로 사용할 수 있다.

#### D. 고관절각 대리값

측면 2D에서 같은 쪽 shoulder-hip-knee 각도로 계산한다.

$$
HipDeviation_t=180^\circ-\theta(Shoulder_t,Hip_t,Knee_t)
$$

굴곡과 신전 부호는 진행 방향과 thigh vector를 이용해 별도로 부여해야 한다. 몸통 기울기가 바뀌면 값도 변하므로 3D 해부학적 고관절각과 완전히 같지 않다.

#### E. 발목 관절각

현재 PoC의 발 분절각과 별도로 정의해야 한다.

$$
AnkleIncluded_t=\theta(Knee_t,Ankle_t,Toe_t)
$$

해부학적 dorsiflexion/plantarflexion 0도를 만들려면 neutral calibration과 좌표 부호 정의가 추가로 필요하다. 우선은 `2D ankle included angle`로 저장한다.

#### F. 팔꿈치 굴곡각

$$
ElbowFlex_t=180^\circ-\theta(Shoulder_t,Elbow_t,Wrist_t)
$$

전체 파형 또는 95백분위수와 5백분위수 차이로 ROM을 구할 수 있다.

### 4.4 기준점

| 피처 | Ochy 결과 |
|---|---:|
| Step Frequency MAE | 1.907 steps/min |
| GCT MAE | 0.030 s |
| Swing Time MAE | 0.032 s |
| Step Frequency ICC | 0.972 |
| GCT ICC | 0.680 |
| Swing Time ICC | 0.332 |
| Knee ICC | 0.79 |
| Hip ICC | 0.48 |
| Ankle ICC | 0.16 |
| Elbow ICC | 0.11 |

이 수치는 Ochy 모델의 검증 결과이지 Halpe-26 PoC의 오차 기준이 아니다. 정상 자세 범위로 사용해서도 안 된다.

### 4.5 프로젝트 한계

- 참가자가 9명으로 작다.
- 우리 모델은 Ochy가 아니라 Halpe-26이다.
- Ochy는 60Hz, 현재 test1은 25fps다.
- Knee 외의 Hip, Ankle, Elbow 일치도가 제한적이다.
- 발목각과 팔꿈치각은 사용자 핵심 코칭보다 탐색용이 적절하다.
- 신발 색상, 배경 대비, 속도와 motion blur가 발 키포인트에 영향을 준다.

### 4.6 읽을 필요성

**반드시 봐야 한다.** 프로젝트와 가장 유사한 단일 2D 스마트폰 검증 자료다. 다만 Ochy의 정확도를 우리 모델의 정확도처럼 인용하면 안 된다. 주로 피처별 신뢰도 등급과 촬영 조건을 설계하는 데 사용한다.

---

## 5. 논문 3 - 러닝 기술, 경제성과 경기력

### 5.1 목적

러닝 기술을 여러 운동학적 범주로 나누고, 어떤 피처가 러닝 경제성, 생리학적 성능 지표와 시즌 최고 기록의 차이를 설명하는지 분석한다.

이 논문은 현재 프로젝트에서 어떤 피처를 우선 선택할 것인지 정하는 핵심 근거다.

### 5.2 실험 대상과 방법

- 러너 97명, 여성 47명
- 다양한 경기 수준을 포함
- 56개 반사 마커를 사용한 3D 전신 운동학
- 17개 신체 분절 모델
- 10, 11, 12 km/h에서 측정한 값을 개인별로 평균
- 속도별 약 10 stride 또는 20 step 분석
- 24개 피처를 수직 진동, 제동, 자세, stride parameter, 하지각으로 구분
- 러닝 에너지 비용과 시즌 최고 기록을 상관 및 다중회귀로 분석

### 5.3 Halpe-26으로 구할 수 있는 피처와 공식

#### A. 접지 중 골반 수직 진폭 대리값

$$
PelvisOsc_{stance}=\frac{\max_{t\in[IC,TO]}y_P(t)-\min_{t\in[IC,TO]}y_P(t)}{L}
$$

영상에서는 아래쪽이 양수지만 진폭은 max-min이므로 방향과 무관하다. 논문의 standing height 정규화와 같지 않으므로 proxy로 표시한다.

#### B. 접지 중 최대 무릎 굴곡과 ROM

$$
PeakKneeFlex=\max_{t\in[IC,TO]}KneeFlex_t
$$

$$
KneeROM_{stance}=\max(KneeFlex_t)-\min(KneeFlex_t)
$$

현재 PoC의 excursion은 `PeakKneeFlex - KneeFlex at IC`이므로 논문의 전체 접지 ROM과 약간 다를 수 있다. 두 값을 분리하는 것이 좋다.

#### C. 착지 시 signed 정강이각

발목에서 무릎으로 향하는 벡터를 사용한다.

$$
ShankAngle_{IC}=atan2(d(K_x-A_x),-(K_y-A_y))\frac{180}{\pi}
$$

양수와 음수의 의미를 진행 방향 기준으로 고정한다. 현재처럼 절댓값만 저장하면 앞쪽 기울기와 뒤쪽 기울기를 구분할 수 없다.

#### D. 착지 시 발 분절각

진행 방향의 수평축과 heel-toe 선의 각도다.

$$
FootSegmentAngle_{IC}=atan2(-(T_y-E_y),d(T_x-E_x))\frac{180}{\pi}
$$

$E$는 heel, $T$는 big/small toe 평균이다. 양수는 발끝이 위쪽인 방향으로 정의할 수 있다.

#### E. 착지 시 허벅지각

$$
ThighAngle_{IC}=atan2(d(K_x-H_x),K_y-H_y)\frac{180}{\pi}
$$

정확한 0도와 부호는 논문의 좌표 정의에 맞춰 synthetic test로 검증해야 한다.

#### F. 몸통 전경

$$
TrunkLean_t=atan2(d(N_x-P_x),-(N_y-P_y))\frac{180}{\pi}
$$

논문은 quiet standing 대비 평균 몸통 기울기를 사용했다. 우리 값은 화면 수직축 대비 절대각이므로 개인의 정적 기준 영상을 빼면 더 가까워진다.

$$
RelativeTrunkLean=MeanTrunkLean_{run}-TrunkLean_{quiet}
$$

#### G. Duty Factor

$$
DF=\frac{GCT}{GCT+SwingTime}
$$

#### H. 최소 수평 골반 속도 대리값

골반 중심 $x$를 먼저 평활화한다.

$$
v_P(t)=d\frac{x_P(t+1)-x_P(t-1)}{2\Delta t}
$$

전체 전진 속도의 차이를 제거하기 위해 stride 평균을 뺀다.

$$
v_{rel}(t)=\frac{v_P(t)-\overline{v_P}_{stride}}{L}
$$

$$
MinPelvisVelocityProxy=\min_{t\in[IC,TO]}v_{rel}(t)
$$

이 값은 body lengths/s 단위의 2D proxy이며 논문의 3D pelvis velocity와 동일하지 않다.

### 5.4 기준점

연구 집단의 평균값은 다음과 같다.

| 피처 | 평균 +/- SD |
|---|---:|
| 접지 중 pelvis vertical oscillation / height | 0.046 +/- 0.007 |
| Stride Rate | 83.4 +/- 5.37 strides/min |
| Ground Contact Time | 0.246 +/- 0.022 s |
| Swing Time | 0.477 +/- 0.041 s |
| Duty Factor | 0.340 +/- 0.030 |
| 착지 시 foot angle | 8.87 +/- 9.39 deg |
| 착지 시 shank angle | 8.99 +/- 3.47 deg |
| 접지 중 knee ROM | 28.3 +/- 4.66 deg |

Stride Rate 83.4 strides/min은 약 166.8 steps/min이다. 위 수치는 정상 임계값이 아니다.

세 피처가 에너지 비용 변동의 39%, 네 피처가 시즌 최고 기록 변동의 31%를 함께 설명했다. 이는 개선률이 아니라 회귀모형의 설명력이다.

### 5.5 프로젝트 한계

- 논문은 3D, 현재 프로젝트는 단일 2D다.
- 실제 CM과 3D pelvis rotation을 구할 수 없다.
- 최소 수평 골반 속도는 카메라 원근과 트레드밀 여부에 민감하다.
- 몸통 기울기는 quiet standing 기준 영상이 없으면 정의가 다르다.
- 상관과 회귀 결과를 자세 변경의 인과효과로 해석할 수 없다.
- 작은 무릎 ROM이나 낮은 DF를 모든 사용자에게 무조건 권할 수 없다.

### 5.6 읽을 필요성

**반드시 봐야 한다.** 측면 MVP의 핵심 피처 선정 근거다. 특히 pelvis oscillation, shank angle, knee motion, DF와 trunk lean을 연결한다. 단, Table 2 값을 정상 범위로 가져오지 말고 피처 정의와 연구 관계를 중심으로 사용한다.

---

## 6. 논문 4 - 경제적인 러닝 기술 리뷰

### 6.1 목적

러닝 경제성에 영향을 줄 수 있는 수정 가능한 생체역학 요소를 기존 연구에서 종합하고, 특정한 경제적 러닝 기술을 권고할 수 있는지 검토한다.

### 6.2 실험 대상과 방법

이 논문은 새로운 참가자를 모집한 실험 연구가 아니라 문헌 리뷰다. 시공간, 운동학, 운동역학, 신경근, 신발-지면, 몸통과 상지 요소를 여러 선행연구에서 종합했다.

따라서 이 논문 자체에는 Halpe-26에 바로 옮길 하나의 통일된 실험 프로토콜이나 공식이 없다.

### 6.3 Halpe-26으로 구할 수 있는 피처와 공식

#### A. 개인 선호 stride length 대비 변화

지면 고정 카메라에서 같은 발의 연속 접지 위치를 사용할 수 있다.

$$
StrideLengthProxy_i=\frac{d(x_{heel,IC_{i+1,same}}-x_{heel,IC_{i,same}})}{L}
$$

기준 영상의 개인 선호값과 비교한다.

$$
Change\%=\frac{StrideLengthProxy_{current}-StrideLengthProxy_{baseline}}{StrideLengthProxy_{baseline}}\times100
$$

트레드밀에서는 발의 화면상 이동만으로 실제 stride length를 구할 수 없다. 벨트 속도를 알면 `speed x stride time`으로 추정해야 한다.

#### B. 수직 진폭

Folland 절의 `PelvisOsc_stance` 공식을 사용한다. 실제 COM이 아닌 pelvis proxy로 표시한다.

#### C. Toe-off 시 다리 신전 비율

고관절에서 발목까지의 직선 길이를 허벅지와 종아리 길이 합으로 나눈다.

$$
LegExtensionRatio_{TO}=\frac{\|Hip_{TO}-Ankle_{TO}\|}{\|Hip_{TO}-Knee_{TO}\|+\|Knee_{TO}-Ankle_{TO}\|}
$$

완전히 곧게 펴질수록 1에 가까워진다. knee extension과 plantarflexion을 모두 포함한 논문의 leg extension 개념을 완전히 재현하지는 못한다.

#### D. Arm Swing 대리값

어깨 기준 손목의 진행 방향 전후 범위를 신체 크기로 나눈다.

$$
ArmSwingAPROM=\frac{P_{95}(d(x_W-x_S))-P_5(d(x_W-x_S))}{L}
$$

팔꿈치 ROM보다 실제 팔 전체의 앞뒤 이동을 더 잘 나타낼 수 있다.

#### E. Stride Angle

논문에서는 Toe-off에서 COM 포물선 접선의 각도로 정의한다. 실제 COM과 안정적인 비행 궤적이 없으면 동일한 값을 계산하기 어렵다.

골반 중심 속도로 다음 대리값을 만들 수는 있다.

$$
PelvisTrajectoryAngle_{TO}=atan2(-v_{P,y}(TO),d\,v_{P,x}(TO))
$$

하지만 이는 논문의 stride angle과 동일하지 않으므로 MVP에서는 출력하지 않는 편이 낫다.

### 6.4 기준점

- 숙련 러너의 수학적 최적 stride length는 평균적으로 선호값보다 약 3% 짧았다.
- 선호 stride length에서 -3% 정도의 변화는 경제성을 크게 해치지 않았다는 연구가 정리되어 있다.
- 6%를 넘는 변화가 불리했다는 결과도 소개된다.
- 이 값은 숙련 러너의 개인 선호값 기준이며 보편적 정상 범위가 아니다.

리뷰가 경제성과 관련된 후보로 정리한 항목은 낮은 수직 진폭, 높은 leg stiffness, Toe-off에서 적은 leg extension, 큰 stride angle, arm swing 유지 등이다. 하지만 일부 항목의 연구 결과는 제한적이거나 상충한다.

### 6.5 프로젝트 한계

- 리뷰이므로 피처마다 연구 대상과 방법이 다르다.
- Leg stiffness는 힘 또는 검증된 spring-mass 입력 없이 직접 구하기 어렵다.
- GRF와 다리 축 정렬은 지면반력이 없어 계산할 수 없다.
- 하지 관성모멘트는 분절 질량과 3D 회전 정보가 필요하다.
- 근육 공동수축은 EMG가 필요하다.
- -3%와 6%를 모든 사용자에게 코칭 기준으로 적용하면 안 된다.

### 6.6 읽을 필요성

**봐야 하지만 공식 구현의 1순위 논문은 아니다.** 사용자 피드백을 하나의 절대 정답이 아니라 개인 기준과 여러 피처의 조합으로 설계하는 데 중요하다. 측면 MVP 일정이 촉박하면 Folland 피처를 먼저 구현하고 Moore는 피드백 문장과 개인화 설계 단계에서 자세히 보면 된다.

---

## 7. 논문 5 - Fourier 급수 러닝 운동학 표현

### 7.1 목적

주기적인 러닝 관절각 시계열을 적은 수의 Fourier 계수로 정확하고 압축된 형태로 표현할 수 있는지 검증한다.

이 논문은 올바른 자세, 경제성 또는 부상 피드백 기준을 제공하지 않는다.

### 7.2 실험 대상과 방법

- 러너 78명, 남성 48명과 여성 30명
- amateur부터 elite까지 포함
- 트레드밀 285개 trial
- 36개 반사 마커와 9-camera 3D motion capture
- AnyBody 기반 근골격 모델
- 최종적으로 85개 독립 관절각 분석
- FFT로 기본 stride frequency를 찾음
- 여러 stride를 정렬하고 평균 파형 생성
- 1-10쌍 Fourier coefficient로 파형 근사
- 원본과 RMSD 및 Pearson correlation 비교

### 7.3 Halpe-26으로 구할 수 있는 피처와 공식

먼저 한 stride를 $N$개 지점의 0-100% phase로 정규화한다.

$$
\phi_i=\frac{2\pi i}{N},\quad i=0,1,...,N-1
$$

관절각 파형 $q_i$를 다음과 같이 표현한다.

$$
\hat q(\phi)=a_0+\sum_{n=1}^{K}[a_n\cos(n\phi)+b_n\sin(n\phi)]
$$

이산 계수는 다음처럼 계산할 수 있다.

$$
a_0=\frac{1}{N}\sum_{i=0}^{N-1}q_i
$$

$$
a_n=\frac{2}{N}\sum_{i=0}^{N-1}q_i\cos(n\phi_i)
$$

$$
b_n=\frac{2}{N}\sum_{i=0}^{N-1}q_i\sin(n\phi_i)
$$

복원 오차는 다음과 같다.

$$
RMSD=\sqrt{\frac{1}{N}\sum_{i=0}^{N-1}(q_i-\hat q_i)^2}
$$

Halpe-26에서는 먼저 무릎 굴곡 파형에 적용하고 이후 고관절과 팔꿈치로 확장한다.

### 7.4 기준점

- 대부분의 관절각은 5쌍 이하의 계수에서 Pearson $r>0.99$, RMSD $<0.5^\circ$
- 논문은 정확한 표현을 위해 최소 5쌍의 coefficient pair를 제안
- 이 기준은 깨끗한 3D 관절각 데이터에 대한 결과다.

### 7.5 프로젝트 한계

- Initial Contact가 틀리면 stride phase 정렬도 틀린다.
- Halpe-26 2D 파형은 3D motion capture보다 노이즈가 크다.
- coefficient 자체는 사용자에게 해석 가능한 자세 피처가 아니다.
- 경제성, 성능 또는 부상 위험과의 관계를 이 논문이 검증하지 않았다.
- 단순 저장 공간 절감은 현재 PoC의 핵심 문제가 아니다.

### 7.6 읽을 필요성

**측면 MVP만 만든다면 지금은 안 봐도 된다.** 기본 사건, GCT, DF와 관절각 피처가 안정된 뒤 개인별 파형 비교나 좌우 패턴 분석을 추가할 때 다시 보면 된다. 여섯 논문 중 현재 우선순위가 가장 낮다.

---

## 8. 논문 6 - Duty Factor와 러닝 경제성

### 8.1 목적

접지시간 비율이 낮은 러너와 높은 러너의 움직임 전략과 에너지 비용을 비교하여 짧은 Ground Contact Time이 항상 더 경제적인지 검토한다.

이 논문의 중요한 결론은 Duty Factor를 낮을수록 좋은 단일 점수로 사용하면 안 된다는 것이다.

### 8.2 실험 대상과 방법

- 훈련된 러너 54명 모집, 남성 33명과 여성 21명
- 여러 속도에서 측정한 평균 DF를 기준으로 극단 집단 선정
- 최종 분석은 low DF 20명, high DF 20명
- 각 집단은 남성 12명, 여성 8명
- 7개 적외선 카메라, 35개 반사 마커, 200Hz 3D 운동학
- 400 m 트랙에서 개인 선호 속도 확인
- 트레드밀 10-18 km/h에서 운동학 측정
- 10, 12, 14 km/h의 4분 달리기에서 에너지 비용 측정
- 15개 강체 분절과 인체계측 자료로 실제 전신 COM 계산

### 8.3 Halpe-26으로 구할 수 있는 피처와 공식

#### A. Ground Contact Time, Swing Time, Aerial Time

$$
GCT_s=\frac{TO_s-IC_s}{f}
$$

$$
Swing_s=\frac{IC_{next,s}-TO_s}{f}
$$

$$
AerialTime_s=\frac{IC_{opposite,next}-TO_s}{f}
$$

#### B. Duty Factor

논문 공식은 다음과 같다.

$$
DF=\frac{t_c}{t_s+t_c}
$$

프로젝트에서는 같은 발의 contact와 swing을 사용한다.

$$
DF_s=\frac{GCT_s}{GCT_s+Swing_s}
$$

#### C. 시간 대칭 피처

접지시간의 초기 절반과 후기 절반을 mid-stance 기준으로 나눌 수 있다. 그러나 논문은 COM 사건을 사용했으므로 pelvis proxy를 사용할 경우 다른 지표가 된다.

간단한 좌우 DF 비대칭은 다음과 같이 계산할 수 있다.

$$
DFAsymmetry=\frac{|DF_L-DF_R|}{(|DF_L|+|DF_R|)/2}\times100
$$

#### D. 골반 수직 및 수평 이동 대리값

접지 중 수직 이동:

$$
PelvisVerticalDisplacement_s=\frac{|y_P(TO)-y_P(IC)|}{L}
$$

접지 중 수평 이동:

$$
PelvisForwardDisplacement_s=\frac{d(x_P(TO)-x_P(IC))}{L}
$$

논문의 실제 COM displacement와 같지 않으므로 명칭에 `pelvis proxy`를 포함한다.

### 8.4 기준점

- Low DF group: 0.330 +/- 0.018
- High DF group: 0.385 +/- 0.028
- 논문 참가자 범위는 일반적으로 0.500 미만이었음
- 두 집단의 에너지 비용에는 유의한 주효과가 없었음
- 0.330은 좋은 기준, 0.385는 나쁜 기준이 아니다.

이 값은 집단을 비교하기 위한 기술 통계다. 사용자에게 목표 DF를 제시하는 임계값으로 사용하지 않는다.

### 8.5 프로젝트 한계

- 실제 COM은 15개 분절 질량으로 구했으며 pelvis center와 다르다.
- 사건 하나가 한 프레임만 틀려도 25-30fps에서 DF가 크게 달라진다.
- 2D에서 mid-stance, mid-flight와 braking/propulsion 대칭을 정확하게 나누기 어렵다.
- 연구 참가자는 훈련된 러너이며 treadmill 속도가 통제되었다.
- DF가 낮거나 높다는 사실만으로 경제성, 좋은 자세 또는 부상 위험을 판단할 수 없다.

### 8.6 읽을 필요성

**Duty Factor를 구현하거나 사용자에게 접지시간 피드백을 제공한다면 반드시 봐야 한다.** 낮은 접지시간을 무조건 권하는 오류를 막아준다. MVP에서 DF를 빼기로 결정한다면 나중에 봐도 되지만, 현재 추가 예정 피처이므로 읽는 것이 좋다.

---

## 9. 논문 우선순위 최종 정리

| 우선순위 | 논문 | 판단 | 이유 |
|---:|---|---|---|
| 1 | Fellin 사건 검출 | 반드시 봄 | 모든 접지 기반 피처의 출발점 |
| 2 | Ochy Validation | 반드시 봄 | 2D 스마트폰 측정 신뢰도와 가장 가까움 |
| 3 | Folland | 반드시 봄 | 핵심 피처 선정과 정의의 중심 |
| 4 | Lussiana Duty Factor | DF 사용 시 반드시 봄 | 낮을수록 좋다는 잘못된 해석 방지 |
| 5 | Moore Review | 피드백 설계 단계에서 봄 | 개인화와 다중 피처 해석에 유용 |
| 6 | Fourier | 측면 MVP에서는 생략 가능 | 파형 연구 단계에서 필요 |

## 10. 측면 MVP에 남길 최종 피처

### 바로 구현하고 검증할 피처

1. Initial Contact와 Toe-off 및 사건 신뢰도
2. Cadence
3. Ground Contact Time
4. Swing Time
5. Duty Factor
6. 착지 시 무릎 굴곡각
7. 접지 중 최대 무릎 굴곡
8. 접지 중 무릎 ROM
9. 착지 시 signed 정강이각
10. 발 분절각
11. 화면 수직축 대비 몸통 기울기
12. 접지 중 골반 수직 진폭 대리값

### 사건 검출 이후 추가할 피처

1. 접지 중 고관절 ROM
2. Toe-off 시 leg extension ratio
3. Aerial Time
4. Arm Swing AP ROM
5. 좌우 DF 및 ROM 비대칭

### 지금은 빼도 되는 피처

1. Fourier coefficients
2. Stride Angle
3. 실제 Leg Stiffness
4. GRF-leg axis alignment
5. 실제 COM
6. 근육 공동수축
7. 하지 관성모멘트
8. 임상적 부상 위험 점수

## 11. 최종 주의사항

논문에 나온 평균과 표준편차를 `within_reference`, `above_reference`, `below_reference`로 자동 판정하지 않는다. 앱에는 다음 네 정보를 함께 저장한다.

- `measurement_value`: 측정값
- `measurement_confidence`: 키포인트와 사건의 측정 신뢰도
- `definition_match`: 논문과의 정의 일치도
- `evidence_type`: validation, association, review 또는 representation

사용자 피드백은 측정값, 논문에서 관찰된 관계, 현재 구현의 한계, 작은 행동 cue 순서로 제공한다. 단일 피처로 올바른 자세, 경제성 또는 부상 위험을 확정하지 않는다.
