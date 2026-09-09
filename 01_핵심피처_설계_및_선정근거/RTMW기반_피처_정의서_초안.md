# RTMW 기반 러닝 자세 분석 피처 정의서

## 문서 목적

이 문서는 러닝 자세 분석 앱에서 사용할 최종 핵심 피처와 파생변수를 RTMW의 COCO-WholeBody 133개 키포인트 체계에 맞춰 정의한다.

앱의 측정값은 일반 측면 RGB 영상에서 얻은 2D 키포인트 기반 대리지표다. Vicon, 지면반력계 또는 해부학적 마커로 측정한 실험실 값과 완전히 동일하다고 표현하지 않는다.

### 공통 원칙

- 논문 수치를 곧바로 정상·비정상 경계로 사용하지 않는다.
- 측정 정의, 연구 대상, 속도, 경사도와 장비가 충분히 유사할 때만 참고값으로 사용한다.
- 집단 공통 임계값의 근거가 부족하면 동일 조건에서 측정한 개인 기준선과 반복 변화를 우선한다.
- RTMW가 직접 제공하지 않는 C7, sacrum, COM(신체의 무게중심), Initial Contact 및 Toe-off는 대리지표 또는 추정 이벤트라고 표시한다.
- 유료 원문, 404, Not Found, 빈 파일 및 원문에서 확인되지 않는 수치는 근거로 채택하지 않는다.

# 참고(여기서 지표를 사용해도 될 것 같습니다.)
Google for Developers

[Pose landmark detection guide  |  Google AI Edge  |  Google for Developers](https://developers.google.com/edge/mediapipe/solutions/vision/pose_landmarker?_gl=1*h5rx1l*_up*MQ..*_ga*MzI0Nzg1MjY3LjE3ODcwNTA0MDM.*_ga_SM8HXJ53K2*czE3ODcwNTA0MDMkbzEkZzAkdDE3ODcwNTA0MDMkajYwJGwwJGgw)
---

# F1. 전방 기울기 : 측면 촬영 시 특화된 변수

## 1. RTMW 키포인트

| 관절        |  왼쪽 | 오른쪽 |
| --------- | --: | --: |
| Shoulder  |   5 |   6 |
| Hip       |  11 |  12 |
| Ankle     |  15 |  16 |
| Big toe   |  17 |  20 |
| Small toe |  18 |  21 |
| Heel      |  19 |  22 |

공식 키포인트 정의: [OpenMMLab COCO-WholeBody 설정](https://github.com/open-mmlab/mmpose/blob/main/configs/_base_/datasets/coco_wholebody.py)

RTMW에는 C7과 sacrum 키포인트가 없다. 따라서 다음 대리점을 사용한다.

```text
shoulder_center(어꺠) : C7
= (left_shoulder + right_shoulder) / 2

pelvis_center(골반) : sacrum
= (left_hip + right_hip) / 2
```

## 2. 기본 피처 `postural_lean_deg`

**한글명:** 전신 전방 기울기각  
**정의:** 지지측 발목에서 어깨 중심으로 향하는 선분과 영상 수직축 사이의 부호 있는 각도

```text
하단점 = 현재 지지측 ankle
상단점 = shoulder_center
```

영상 좌표에서 아래쪽으로 갈수록 `y`가 증가한다고 할 때:

```python
postural_lean_deg = degrees(
    atan2(
        running_direction
        * (shoulder_center_x - stance_ankle_x),
        stance_ankle_y - shoulder_center_y
    )
)
```

진행 방향:

```text
화면 오른쪽으로 달림: running_direction = +1
화면 왼쪽으로 달림: running_direction = -1
```

해석:

- 양수: 진행 방향으로 기울어짐
- 0° 부근: 수직에 가까움
- 음수: 진행 방향 반대로 기울어짐

RTMW는 지면 접촉을 직접 알려주지 않으므로 발목·뒤꿈치·발가락의 위치와 속도로 지지발을 별도 추정해야 한다.

## 3. 기본 피처 `torso_flexion_deg`

**한글명:** 몸통 전방 굽힘각  
**정의:** 골반 중심에서 어깨 중심으로 향하는 선분과 영상 수직축 사이의 부호 있는 각도

```python
torso_flexion_deg = degrees(
    atan2(
        running_direction
        * (shoulder_center_x - pelvis_center_x),
        pelvis_center_y - shoulder_center_y
    )
)
```

해석:

- 양수: 어깨가 골반보다 진행 방향 앞쪽
- 0° 부근: 몸통이 수직
- 음수: 어깨가 골반보다 뒤쪽

## 4. 논문 정의와 RTMW 정의의 대응
- **논문 제목**: **The effect of forward postural lean on running economy, kinematics, and muscle activation**
- **저자 & 연도**: Nina M. Carson, Daniel H. Aslan, Justus D. Ortega (2024, _PLOS ONE_)
- **DOI**: `10.1371/journal.pone.0302249` ([원문 링크](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0302249))

| 측정값           | 논문 기준점    | RTMW 앱 기준점   | 판정      |
| ------------- | --------- | ------------ | ------- |
| Postural lean | 지지측 발목–C7 | 지지측 발목–어깨 중심 | 2D 대리지표 |
| Torso flexion | sacrum–C7 | 골반 중심–어깨 중심  | 2D 대리지표 |

보고서 표현:

> 본 앱의 F1 각도는 Carson et al.(2024)의 측정 개념을 참고하되, C7 및 천골 마커를 제공하지 않는 RTMW의 특성을 고려하여 어깨 중심과 골반 중심을 사용한 2D 영상 기반 대리지표로 구현한다.

저장 예시:

```json
{
  "feature": "postural_lean_deg",
  "method": "rtmw_2d_proxy",
  "upper_landmark": "shoulder_center",
  "lower_landmark": "stance_ankle",
  "reference_axis": "image_vertical"
}
```

## 5. F1 파생변수

### `lean_strategy_ratio_pct`

```text
torso_flexion_deg / postural_lean_deg × 100
```

**정의:** 전체 전방 기울기 대비 몸통 굽힘각의 상대적 비율

분모가 0°에 가까우면 비율이 폭증하므로 계산 제외 조건이 필요하다.

```python
if abs(postural_lean_deg) < minimum_lean_deg:
    lean_strategy_ratio_pct = None
```

이 값은 0~100%에 반드시 머무르지 않으며 100% 초과 또는 음수가 나올 수 있다.

**근거 수준:** 실험적 파생지표  
**검증된 임계값:** 없음

Carson 연구에서 몸통 전략의 torso flexion이 발목 전략보다 34% 컸다는 결과는 이 비율의 34% 임계값을 의미하지 않는다. 이것에 대한 논문이나 정보가 더 있는지 찾아보겠다.

### 기존 `ankle_component_deg`(참고: deg는 degree: 각도)

```text
ankle_component_deg= postural_lean_deg - torso_flexion_deg
```

이 값은 실제 발목 관절각이 아니다. 권장 변수명은 다음과 같다. (단순 변수명입니다.)

```text
non_torso_lean_component_deg
```

**정의:** 전신 전방 기울기에서 몸통 굽힘 성분을 차감한 각도 차이

기존 이름을 유지한다면 다음 주석을 표시한다.

> `ankle_component_deg`는 실제 발목 관절각이 아니라 전체 전방 기울기에서 몸통 굽힘 성분을 차감한 실험적 추정값이다.
> 
밑의 값들은 postural_lean_deg 를 구하면 자동으로 구할 수 있다.
### `mean_postural_lean_deg`

**정의:** 각 보폭의 평균 전방 기울기를 계산한 뒤 보폭 평균들을 동일 가중치로 평균한 값

```text
1. 각 보폭에서 평균 postural_lean_deg 계산
2. 보폭별 평균을 동일 가중치로 평균
```

### `max_postural_lean_deg`

**정의:** 분석 구간에서 전방 기울기가 가장 크게 검출된 값

적용 전 처리:

- 낮은 RTMW confidence 프레임 제외
- 좌표 스무딩
- 급격한 키포인트 튐 제거

원시 최댓값은 한 프레임의 오류에 민감하므로 내부 검증용 `p95_postural_lean_deg`도 함께 저장하는 것이 좋다.

### `lean_variability_deg`

```text
각 보폭의 평균 postural_lean_deg에 대한 표준편차
```

프레임 전체의 표준편차가 아니라 **보폭 간 변동성**으로 정의한다.

### `baseline_lean_change_deg`

```text
baseline_lean= 현재 mean_postural_lean_deg - 개인 baseline_mean_postural_lean_deg
```

- 양수: 개인 기준선보다 더 앞으로 기울어짐
- 음수: 개인 기준선보다 더 수직 또는 뒤쪽으로 변화

비교 조건:

- 유사한 러닝 속도
- 동일한 카메라 방향과 촬영 높이
- 동일한 트레드밀 경사도
- 유사한 분석 시간과 피로 상태

## 6. F1 근거 해석

Carson et al.은 postural lean을 지지측 발목–C7 선분과 수직축 사이의 각도, torso flexion을 sacrum–C7 선분과 수직축 사이의 각도로 정의했다. 참가자 16명이 3.58m/s 트레드밀에서 달렸고, 200Hz Vicon과 39개 마커로 20보폭을 분석했다.

확인된 연구 조건별 값:

| 조건                     |               평균 postural lean |
| ---------------------- | -----------------------------: |
| Upright 조건(똑바른 자세)     |                     1.7 ± 0.7° |
| Moderate lean(적당한 기울기) |                     4.3 ± 0.8° |
| Maximum lean(큰 전방 기울기) | 평균 8.2°, SD 1.9°, 범위 6.1–11.5° |

큰 전방 기울기 조건에서는 순 대사비용이 최대 8% 증가했다. 그러나 의도적으로 자세를 변경한 실험 조건이므로 8.2°를 정상·위험 경계로 사용할 수 없다.
**이것에 대한 기준이 표준화되거나 더 많은 정보, 논문이 있는걸 찾겠다**

무료 원문: [Carson et al., PLOS ONE, 2024](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0302249)

## 7. 사용자 피드백 설계

가능한 피드백:

> 현재 전방 기울기는 같은 속도의 개인 기준선보다 증가했습니다. 일부 트레드밀 연구에서는 큰 전방 기울기가 대사비용 증가와 관련됐지만, 속도와 측정 방법에 따라 결과가 달라질 수 있습니다.

> 몸 전체의 기울기에 비해 몸통 굽힘이 상대적으로 커졌습니다. 허리만 접히는지, 발목부터 자연스럽게 기울어지는지 함께 확인해 보세요.

사용하면 안 되는 피드백:

> 전방 기울기가 8°이므로 잘못된 자세입니다.

> `lean_strategy_ratio_pct`가 34%를 넘었으므로 몸통 전략입니다.

> `ankle_component_deg`는 실제 발목 관절각입니다.

## 8. F1 데이터 후보

| 자료                 | 활용도   | 용도                         |
| ------------------ | ----- | -------------------------- |
| Carson PLOS 연구     | 높음    | 측정 정의와 실험 조건별 변화           |
| Van Hooren OSF 데이터 | 매우 높음 | 속도·경사·케이던스·전방 몸통 기울기 조건 비교 |
| 자체 RTMW 측면 영상      | 필수    | RTMW 대리지표의 오차와 반복성 검증      |

- [RTMW 논문 무료 원문](https://arxiv.org/abs/2407.08634)
- [Van Hooren 공개 OSF 데이터](https://osf.io/7qbxc/overview)
- [Van Hooren 데이터 설명 논문](https://pubmed.ncbi.nlm.nih.gov/39822723/)

Van Hooren 데이터는 C3D 마커와 OpenSim 결과를 제공하지만 RGB 영상은 아니므로 RTMW 정확도를 직접 평가하지는 못한다. 실험실 기준의 방향성과 조건별 변화를 검증하는 데 사용한다.

## 9. F1 최종 분류

| 변수                             | 상태               |
| ------------------------------ | ---------------- |
| `postural_lean_deg`            | 핵심 RTMW 2D 대리지표  |
| `torso_flexion_deg`            | 핵심 RTMW 2D 대리지표  |
| `lean_strategy_ratio_pct`      | 실험적 파생지표, 임계값 없음 |
| `non_torso_lean_component_deg` | 실험적 파생지표         |
| `mean_postural_lean_deg`       | 채택 가능            |
| `max_postural_lean_deg`        | 채택 가능, 이상치 처리 필수 |
| `lean_variability_deg`         | 채택 가능            |
| `baseline_lean_change_deg`     | 채택 가능, 조건 일치 필수  |

---

# F2. 착지 시 무릎 굴곡각
**(측면 적합/ 정면, 후면: 제한적(정면에서는 무릎 굽힘이 축소·왜곡됨))**
**Differences in Lower Extremity Kinematics Between High School Cross-Country and Young Adult Recreational Runners**

[](https://ijspt.scholasticahq.com/article/18821-differences-in-lower-extremity-kinematics-between-high-school-cross-country-and-young-adult-recreational-runners?utm_source=chatgpt.com)[https://ijspt.scholasticahq.com/article/18821-differences-in-lower-extremity-kinematics-between-high-school-cross-country-and-young-adult-recreational-runners](https://ijspt.scholasticahq.com/article/18821-differences-in-lower-extremity-kinematics-between-high-school-cross-country-and-young-adult-recreational-runners)
## 1. RTMW 키포인트

| 관절        |  왼쪽 | 오른쪽 |
| --------- | --: | --: |
| Hip       |  11 |  12 |
| Knee      |  13 |  14 |
| Ankle     |  15 |  16 |
| Big toe   |  17 |  20 |
| Small toe |  18 |  21 |
| Heel      |  19 |  22 |

## 2. 무릎 굴곡각 계산 정의

### `knee_flexion_deg`

**한글명:** 무릎 굴곡각  
**정의:** **측면 영상**에서 Hip–Knee–Ankle 세 점(ex 왼쪽 오른쪽 왼쪽= 11, 14, 15)으로 계산한 무릎의 2D 시상면 굴곡각
**모든 프레임에서 계속 계산되는 시계열 값**
joint_internal_angle은 공식 그대로 각도 입니다. 
![[Pasted image 20260819111222.png|158]]
knee_flexion_deg
![[Pasted image 20260819111323.png|299]]
```text
joint_internal_angle = angle(hip - knee, ankle - knee)

knee_flexion_deg = 180° - joint_internal_angle
```

- 다리가 거의 펴짐: 0°에 가까움
- 무릎이 굽혀질수록: 값 증가
- 굴곡: 양수
- 과신전: 필요하면 음수로 별도 처리

## 3. Initial Contact 정의(기본 피처 설명을 위해 넣었습니다.): IC

`initial_contact`는 해당 발이 공중 또는 스윙 상태에서 지면과 처음 접촉하는 시점이다. RTMW는 접촉을 직접 출력하지 않으므로 앱에서 검출되는 이벤트는 `estimated_initial_contact`다.

판정에는 Ankle, Heel, Big toe, Small toe를 사용한다.

1. 발이 하강하다가 영상상 최저 위치 부근에 도달
2. Heel 또는 toe의 수직 이동속도가 급격히 감소
3. 이후 여러 프레임 동안 발 높이가 비교적 안정
4. 같은 발에서 일정 시간 이내 중복 이벤트 제거
5. 좌우 발 독립 추적

저장 이벤트명:

```text
left_estimated_ic
right_estimated_ic
```
![[Pasted image 20260819111751.png|356]]
## 4. 기본 피처 `knee_flexion_at_ic_deg`

**정의:** 추정 Initial Contact 프레임에서 착지측 Hip–Knee–Ankle로 계산한 무릎 굴곡각
기존의 knee_flexion과 다르게 IC 프레임 타이밍의 값 하나만 추출합니다.

```text
왼발 IC: left_hip → left_knee → left_ankle
오른발 IC: right_hip → right_knee → right_ankle
```

분석 단위는 한 번의 착지당 하나의 각도다.

```json
{
  "side": "left",
  "event": "estimated_initial_contact",
  "frame": 153,
  "knee_flexion_at_ic_deg": 14.8,
  "method": "rtmw_2d_sagittal_proxy"
}
```

## 5. F2 파생변수(Knee_flexion_deg만 알아도 다 가능)

### `max_knee_flexion_stance_deg`

IC부터 해당 발의 toe-off까지 입각기에서 관찰된 최대 무릎 굴곡각이다.

```text
max(knee_flexion_deg during stance)
```

RTMW만으로 toe-off를 확정하기 어려우므로 초기 MVP에서는 `estimated_max_knee_flexion_stance_deg`로 표시한다.

### `total_knee_flexion_deg`

```text
total= max_knee_flexion_stance_deg - knee_flexion_at_ic_deg
```

착지 이후 추가로 굽혀진 각도다.

### `mean_knee_flexion_ic_deg`

```text
mean= mean(all valid knee_flexion_at_ic_deg)
```

### `left_mean_knee_flexion_deg`

```text
left= mean(left knee flexion at left IC)
```

### `right_mean_knee_flexion_deg`

```text
right= mean(right knee flexion at right IC)
```

### `lr_difference_deg`

```text
lr_diff= left_mean_knee_flexion_deg - right_mean_knee_flexion_deg
```

- 양수: 왼쪽이 더 굽혀짐
- 음수: 오른쪽이 더 굽혀짐

차이의 크기만 표시할 때:

```text
knee_lr_absolute_difference_deg = abs(left - right)
```

### `knee_asymmetry_pct`

```text
abs(left_mean - right_mean) / ((left_mean + right_mean) / 2)
× 100
```

방향을 알려주지 않으므로 `lr_difference_deg`와 함께 저장한다. **특정 퍼센트를 위험 기준으로 사용하지 않는다.**

### `knee_flexion_sd_deg`

모든 유효 착지의 `knee_flexion_at_ic_deg` 표준편차다. 좌우가 섞이는 영향을 피하기 위해 다음 값도 분리할 수 있다.

```text
left_knee_flexion_sd_deg
right_knee_flexion_sd_deg
```

### `knee_flexion_cv_pct`

```text
cv_pct= knee_flexion_sd_deg / mean_knee_flexion_ic_deg
× 100
```

평균이 0°에 가까우면 계산하지 않는다. 근거가 확보될 때까지 개인 반복 일관성 지표로 사용한다.

### 기존 `baseline_change_deg`

권장 변수명:

```text
baseline_knee_flexion_change_deg
```

```text
baseline_knee_flexion_change_deg= 
현재 mean_knee_flexion_ic_deg - 개인baseline_mean_knee_flexion_ic_deg
```

### `knee_angle_trend_deg`

```text
trend_deg= 영상 후반부 mean_knee_flexion_at_ic - 영상 전반부 mean_knee_flexion_at_ic
```

- 양수: 후반부 굴곡 증가
- 음수: 후반부 굴곡 감소

**이 값만으로 피로를 단정하지 않는다.**

## 6. F2 근거 해석

무릎 굴곡은 착지와 입각기에서 충격 흡수에 관여하지만 특정 각도 하나만으로 부상을 예측할 수 있다는 근거는 충분하지 않다.

**2023년 전향적 연구에서는 IC 무릎 굴곡각이나 peak knee flexion이 전체 러닝 부상 발생과 연관된다는 결과를 확인하지 못했다.**

- [Dillon et al., PLOS ONE, 2023](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0288814)

2021년 체계적 문헌고찰에서는 여러 피로 연구를 종합했을 때 IC 무릎 굴곡각이 증가하는 경향을 보고했지만 연구 프로토콜과 환경에 따라 결과가 달랐다.

- [Apte et al., Frontiers in Physiology, 2021](https://pubmed.ncbi.nlm.nih.gov/34512370/)

2026년 체계적 문헌고찰도 과거 부상과 훈련 부하가 중요하며 생체역학 변수의 예측력은 일관되지 않다고 정리했다.

- [Senthil et al., 2026](https://pubmed.ncbi.nlm.nih.gov/42221209/)

## 7. 사용자 피드백 설계

**가능한 피드백:**

> **영상 후반부에서 착지 시 무릎 굴곡각이 평소보다 증가했습니다.** 이런 변화는 피로 상황의 러닝 연구에서도 관찰되지만, 속도나 러닝 전략의 영향도 받을 수 있습니다.
> 그러나 이는 영상 길이가 너무 짧으면 판단이 힘들 수 있을 거라고 생각

> **오른쪽과 왼쪽 착지의 평균 무릎 굴곡각에 차이가 관찰되었습니다.** 한 번의 촬영만으로 문제를 판단하기보다 같은 조건에서 반복 확인하는 것이 좋습니다.

사용하면 안 되는 피드백:

> 무릎각이 15°이므로 부상 위험이 높습니다.

> 좌우 차이가 10%이므로 비정상입니다.

> 후반부 무릎각이 증가했으므로 피로가 확실합니다.

## 8. F2 데이터 후보

| 데이터 | 활용도 | 용도 |
|---|---|---|
| Van Hooren OSF 데이터 | 높음 | 지면반력 기반 IC, 관절각, 속도·경사·케이던스 비교 |
| Fukuchi 데이터 | 높음 | 여러 속도에서 하지 관절각 변화 검증 |
| Running Injury Clinic Dataset | 후보 | 많은 참가자의 3D 마커 분포 확인 |
| AthleticsPose | 보조 | 키포인트 계산 코드와 각도 산출 검증 |

- [Van Hooren 공개 OSF 데이터](https://osf.io/7qbxc/overview)
- [Running Injury Clinic Kinematic Dataset](https://plus.figshare.com/articles/dataset/Running_Injury_Clinic_Kinematic_Dataset/24255795)

## 9. F2 최종 분류

| 변수 | 상태 |
|---|---|
| `knee_flexion_at_ic_deg` | 핵심 피처 |
| `max_knee_flexion_stance_deg` | 핵심 파생 피처, toe-off 검출 필요 |
| `total_knee_flexion_deg` | 채택 가능 |
| `mean_knee_flexion_ic_deg` | 채택 가능 |
| `left_mean_knee_flexion_deg` | 채택 가능 |
| `right_mean_knee_flexion_deg` | 채택 가능 |
| `lr_difference_deg` | 채택 가능 |
| `knee_asymmetry_pct` | 탐색적 지표, 임계값 없음 |
| `knee_flexion_sd_deg` | 개인 일관성 지표 |
| `knee_flexion_cv_pct` | 개인 일관성 지표 |
| `baseline_knee_flexion_change_deg` | 개인 변화 지표 |
| `knee_angle_trend_deg` | 영상 내 변화 지표 |

---

# F3. 분당 케이던스: 측면 / 정면, 후면 모두 적합 변수
**착지 이벤트만 정확히 잡히면 모든 방향에서 가능**
**논문1. Effects of Step Rate Manipulation on Joint Mechanics during Runni**

[https://pmc.ncbi.nlm.nih.gov/articles/PMC3022995/](https://pmc.ncbi.nlm.nih.gov/articles/PMC3022995/)
## 1. RTMW 키포인트와 착지 이벤트

F3는 F2와 동일한 Ankle, Heel, Big toe, Small toe 키포인트와 공통 `estimated_initial_contact` 이벤트를 사용한다.

RTMW는 cadence, Initial Contact, Toe-off 또는 GCT를 직접 출력하지 않는다.

## 2. 기본 피처 `cadence_spm`

**한글명:** 분당 케이던스  
**정의:** 1분 동안 발생하는 좌우 전체 착지 횟수

```text
cadence_spm = 전체 착지 횟수 / 분석시간(초) × 60
```

착지 간격 기반 계산:

```text
cadence_spm = 60 / mean_step_time_sec
```

분석 경계가 보폭 중간에 걸릴 수 있으므로 두 계산 결과가 유사한지 확인한다.

## 3. F3 파생변수

### `left_step_count`

```text
count(left_estimated_ic)
```

### `right_step_count`

```text
count(right_estimated_ic)
```

좌우 횟수는 결과뿐 아니라 가림, 키포인트 누락, 중복 이벤트 및 좌우 ID 전환을 확인하는 QC 지표다. 영상 시작·종료 시점 때문에 1회 차이는 자연스럽게 발생할 수 있다.

### `mean_step_time_sec`

시간순으로 정렬된 모든 연속 착지 사이의 평균 시간이다.

```text
left IC → right IC
right IC → left IC

mean_step_time_sec = mean(diff(all_ic_timestamps))
```

### 기존 `left_step_interval_sec`

왼발 착지에서 다음 왼발 착지까지의 시간이므로 권장 변수명은 다음과 같다.

```text
left_stride_time_sec = mean(left_IC[i+1] - left_IC[i])
```

### 기존 `right_step_interval_sec`

```text
right_stride_time_sec = mean(right_IC[i+1] - right_IC[i])
```

| 구간 | 정확한 명칭 |
|---|---|
| 왼발 착지 → 오른발 착지 | Step time |
| 오른발 착지 → 왼발 착지 | Step time |
| 왼발 착지 → 다음 왼발 착지 | Left stride time |
| 오른발 착지 → 다음 오른발 착지 | Right stride time |

### `rhythm_asymmetry_pct`(step_time_asymmetry_pct)
**좌우 발의 박자 차이를 백분율로 나타낸 피처**

```text
left_to_right_step_time_sec = 왼발 IC → 다음 오른발 IC 평균

right_to_left_step_time_sec = 오른발 IC → 다음 왼발 IC 평균
```

```text
rhythm_asymmetry_pct
= abs(left_to_right_step_time - right_to_left_step_time)
/
((left_to_right_step_time + right_to_left_step_time) / 2)
× 100
```
해석은 간단합니다.

- `0%`: 좌우 착지 간격이 완전히 동일
- 값이 클수록: 좌우 착지 리듬 차이가 큼
- 한 프레임의 자세가 아니라 **여러 번의 IC 이벤트로 계산하는 시간 기반 피처**
방향 저장:

```text
rhythm_asymmetry_direction_sec= (left_to_right_step_time - right_to_left_step_time)
```

### `cadence_variability_cv_pct`(step_time_cv_pct)
러닝 중에 케이던스가 얼마나 흔들리는지를 나타내는 지표입니다.
실제 계산 대상은 각 착지 사이의 step time이다.
계산식은 CV입니다(변동계수)

```text
cadence_variability_cv_pct
= SD(all_step_times)
/
Mean(all_step_times)
× 100
```

더 정확한 이름 후보는 `step_time_cv_pct`다.
시계열 케이던스 데이터를 이용하여 구간을 나누고 그려서 유저들에게 러닝 리듬에 대한 피드백이 가능합니다.
![[Pasted image 20260819114052.png|370]]![[Pasted image 20260819114111.png|366]]
### `baseline_delta_spm`

```
current_cadence_spm - baseline_cadence_spm
```

### `baseline_delta_pct`

```
(current_cadence_spm - baseline_cadence_spm)
/
baseline_cadence_spm
× 100
```

속도가 다르다면 케이던스 차이를 자세 변화로만 해석하면 안 된다.

### `cadence_trend_spm`

```
후반부 cadence_spm - 전반부 cadence_spm
```

내부 저장:

```text
cadence_first_half_spm
cadence_second_half_spm
cadence_trend_spm
```
![[Pasted image 20260819114551.png]]
개인 기준 케이던스는 같은 환경, 속도에서의 여러 회차를 거치며 나오는 spm의 중앙값입니다.
저희는 짧은 런닝 영상으로 피드백을 하기 떄문에 많은 런닝 데이터가 모여도 동일한 환경, 속도가 아니기 때문에 개인 기준 케이던스를 구할 수는 있지만 힘들다고 생각합니다
하지만 저희가 야외에서의 런닝 외에도 일정한 환경에서의 런닝또한 지원한다면 개인 기준 케이던스를 수월하게 구할 수 있습니다. 이는 마라톤이나 런닝을 잘하고 싶은 사람들에게 좋을 서비스라고 생각합니다.
## 4. RTMW 품질관리

- Ankle, Heel, Big toe, Small toe의 confidence 검사
- 동일 발 중복 착지 제거
- 일반적인 `L → R → L → R` 순서 검사
- 좌우 ID 전환과 가림 검사
- `analysis_duration_sec`, `valid_step_count`, `left_step_count`, `right_step_count` 저장
- 최소 영상 길이와 착지 수는 실제 검증 후 결정

## 5. F3 근거 해석

180spm은 모든 러너에게 적용되는 절대 정답이 아니다. 케이던스는 속도, 신장, 다리 길이, 숙련도, 경사도, 피로 및 환경의 영향을 받는다.(따라서 위의 baseline_spm, baseline_pct는 적절하지 않습니다...)

- [Souza, 근거 기반 러닝 영상 분석, 2015](https://pmc.ncbi.nlm.nih.gov/articles/PMC4714754/)

일정한 속도에서 step rate를 증가시키면 step length와 일부 관절 역학이 변할 수 있다.

- [Heiderscheit et al., 2011](https://pmc.ncbi.nlm.nih.gov/articles/PMC3022995/)

2022년 체계적 문헌고찰에서는 step rate 증가가 여러 생체역학 변수에 영향을 준다는 근거가 있었지만 실제 부상 발생을 줄인다는 결론은 충분하지 않았다.

- [Anderson et al., 2022](https://pmc.ncbi.nlm.nih.gov/articles/PMC9441414/)

```text
생체역학적 부하 지표가 변화함
≠
실제 부상 위험이 확실히 감소함
```

2024년 PLOS ONE 연구에서는 속도가 증가할수록 케이던스도 증가했다. 장치와 속도에 따라 기준 시스템과 오차가 나타났으므로 속도별 개인 기준선을 고려한다.

- [Mason et al., PLOS ONE, 2024](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0312952)

## 6. 사용자 피드백 설계

가능한 피드백:

> 현재 케이던스는 168spm입니다. 케이던스는 속도와 신체 특성에 따라 달라지므로 180spm을 모든 러너의 절대 기준으로 사용하지 않습니다.

> 같은 속도의 개인 기준선보다 케이던스가 감소했습니다. 후반부에 **보폭 리듬**이 함께 불규칙해졌는지 확인해 보세요.

> **왼발에서 오른발로 넘어가는 시간과 오른발에서 왼발로 넘어가는 시간에 차이가 관찰**되었습니다. 같은 조건에서 반복 측정하는 것이 좋습니다.

사용하면 안 되는 피드백:

> 180spm 미만이므로 나쁜 자세입니다.

> 케이던스를 무조건 10% 높이면 부상을 예방할 수 있습니다.

> 리듬 비대칭이 5%이므로 신체에 문제가 있습니다.

> 케이던스가 감소했으므로 피로가 확실합니다.

## 7. F3 데이터 후보

| 자료 | 활용도 | 용도 |
|---|---|---|
| Van Hooren OSF 데이터 | 매우 높음 | 선호 케이던스와 ±10spm 조건, 속도·경사 비교 |
| Fukuchi 데이터 | 높음 | 여러 속도에서 케이던스와 보행주기 검증 |
| Running Injury Clinic Dataset | 후보 | 대규모 착지 간격 분포 검토 |
| 자체 RTMW 영상 | 필수 | 실제 착지 검출 정확도 검증 |

## 8. F3 최종 분류

| 변수 | 최종 상태 |
|---|---|
| `cadence_spm` | 핵심 피처 |
| `left_step_count` | 채택, QC에도 사용 |
| `right_step_count` | 채택, QC에도 사용 |
| `mean_step_time_sec` | 핵심 파생 피처 |
| `left_step_interval_sec` | `left_stride_time_sec`로 변경 권장 |
| `right_step_interval_sec` | `right_stride_time_sec`로 변경 권장 |
| `rhythm_asymmetry_pct` | 탐색적 좌우 리듬 지표 |
| `cadence_variability_cv_pct` | 개인 일관성 지표 |
| `baseline_delta_spm` | 개인 변화 지표 |
| `baseline_delta_pct` | 개인 상대 변화 지표 |
| `cadence_trend_spm` | 영상 내 변화 지표 |

핵심 원칙:

> 절대적인 180spm 기준보다 동일한 속도에서 측정한 개인 기준선, 착지 리듬의 일관성, 영상 전후 변화에 초점을 맞춘다.

# 첫 사용자는 어떻게 하나요?

개인 기록이 없는 신규 사용자는 세 가지 방법이 있습니다.

1. **기준 측정 세션 진행 — 가장 권장**
    - 일정 속도로 20~30초씩 3회 달리게 합니다.
    - 이 결과를 개인 baseline으로 저장합니다.
2. **현재 영상의 안정 구간 사용**
    - 중간의 안정적인 10~20초를 임시 기준으로 설정합니다.
    - 단, 이는 개인의 장기 baseline이 아니라 `session_baseline`입니다.
3. **집단 평균 사용**
    - 개인 baseline이 없을 때만 임시로 사용합니다.
    - 자세 이상 판정보다는 참고값으로만 표시해야 합니다.

개인 기준이 없는 상태에서 집단값을 사용했다면 변수도 구분하는 것이 안전합니다.

---

# FEAT-08. 골반 중심 대비 착지거리(측면 적합, 정후면 부적합)
논문1. [![](https://cdn.ncbi.nlm.nih.gov/pmc/pd-medc-pmc-cloudpmc-viewer/production/bb3d0162/var/data/static/img/favicons/apple-touch-icon.png)PubMed Central (PMC)An Evidence-Based Videotaped Running Biomechanics Analysis](https://pmc.ncbi.nlm.nih.gov/articles/PMC4714754/)​

논문2. **Men's and Women's World Championship Marathon Performances and Changes With Fatigue Are Not Explained by Kinematic Differences Between Footstrike Patterns**

[https://www.frontiersin.org/journals/sports-and-active-living/articles/10.3389/fspor.2020.00102/](https://www.frontiersin.org/journals/sports-and-active-living/articles/10.3389/fspor.2020.00102/)

![[Pasted image 20260819143906.png|432]]
![[Pasted image 20260819143808.png|374]]

## 1. 명칭 변경

RTMW는 실제 신체 질량중심(COM)을 출력하지 않으므로 `COM–발 거리`를 그대로 사용하지 않는다.

권장 명칭:

```text
골반 중심 대비 발목 착지거리
Pelvis-relative Foot Placement at Initial Contact
```

### 해석

- 양수: 착지 발이 골반보다 진행 방향 앞에 있음
- `0` 근처: 착지 발이 골반 바로 아래에 가까움
- 음수: 착지 발이 골반보다 뒤에 있음
```text
pelvis_center = (left_hip + right_hip) / 2
```

## 2. FEAT-08A

기존 `com_to_foot_distance_px`의 권장 변수명:

```text
pelvis_to_ankle_ap_distance_px
```

**정의:** Initial Contact 순간 착지측 발목과 골반 중심 사이의 부호 있는 수평거리
![[Pasted image 20260819120015.png|616]]
 ![[Pasted image 20260819120411.png|446]]<< 다리 길이로 정규화
```python
pelvis_to_ankle_ap_distance_px = (
    running_direction(오른쪽이면 +1, 왼쪽이면 -1)
    * (landing_ankle_x - pelvis_center_x)
)
```

- 양수: 발목이 골반보다 진행 방향 앞쪽
- 0에 가까움: 발목과 골반 중심이 수직으로 가까움
- 음수: 발목이 골반보다 뒤쪽

절댓값만 저장하면 앞과 뒤를 구분할 수 없으므로 부호 있는 거리로 저장한다.

## 3. 발목을 기본 기준점으로 사용하는 이유

착지 유형에 따라 실제 최초 접촉점이 다르다.

- Rearfoot strike: heel 우선
- Midfoot strike: heel과 앞발이 유사한 시점
- Forefoot strike: toe 계열 우선
- ![[Pasted image 20260819120606.png|328]]

Lieberman et al.은 IC에서 lateral malleolus와 knee center 또는 greater trochanter 사이의 투영된 전후거리를 overstride로 정의했다. 
따라서 RTMW ankle을 기본 착지 위치로 사용하는 것이 해당 연구 정의와 가깝다.

- [Lieberman et al., PLOS ONE, 2015](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0131354)

내부 검증용 보조 변수:

```text
pelvis_to_heel_ap_distance_px
pelvis_to_big_toe_ap_distance_px
pelvis_to_small_toe_ap_distance_px
```

## 4. FEAT-08B

기존 `knee_to_ankle_distance_px`의 권장 이름:

```text
knee_to_ankle_ap_distance_px
```

**정의:** IC 순간 착지측 무릎과 같은 쪽 발목 사이의 부호 있는 수평거리
참고: anterior–posterior, 즉 **진행 방향 앞뒤 축**
![[Pasted image 20260819120937.png|595]]

```python
knee_to_ankle_ap_distance_px = (
    running_direction
    * (landing_ankle_x - landing_knee_x)
)
```

- 양수: 발목이 무릎보다 앞쪽
- 0에 가까움: 발목과 무릎이 수직으로 가까움
- 음수: 발목이 무릎보다 뒤쪽

무릎 굴곡의 영향을 크게 받으므로 다음 값과 함께 해석한다.

```text
knee_flexion_at_ic_deg
pelvis_to_ankle_ap_distance
knee_to_ankle_ap_distance
```

## 5. 거리 단위와 정규화

### `pelvis_to_ankle_ap_distance_px`

같은 영상 안의 프레임 및 좌우 비교에 사용할 수 있으나 카메라 거리와 해상도가 다른 사용자끼리 직접 비교하지 않는다.

### 기존 `com_to_foot_distance_cm`

권장 이름:

```text
pelvis_to_ankle_ap_distance_cm
```

센티미터 변환에는 기준 물체, 카메라 보정, 촬영 거리 및 투시 왜곡 보정이 필요하다. MVP 핵심 피처로는 보류한다.

### 기존 `com_to_foot_height_ratio`

권장 이름과 계산식:

```text
pelvis_to_ankle_height_ratio = pelvis_to_ankle_ap_distance_px / body_height_px
```

러닝 프레임의 머리–발 수직거리를 실제 신장으로 사용하지 않는다. 정지 자세 보정 또는 별도의 영상 스케일이 필요하다.

### 기존 `com_to_foot_leg_ratio`

권장 이름:

```text
pelvis_to_ankle_leg_length_ratio
```

```text
thigh_length_px = distance(hip, knee)
shank_length_px = distance(knee, ankle)

reference_leg_length_px = median(thigh_length_px + shank_length_px)

pelvis_to_ankle_leg_length_ratio = pelvis_to_ankle_ap_distance_px / reference_leg_length_px
```

### `knee_to_ankle_height_ratio`

```text
knee_to_ankle_ap_distance_px / body_height_px
```

## 6. 좌우 파생변수

### `left_overstride_ratio`

왼발 IC에서 계산한 정규화 착지거리의 평균이다.

### `right_overstride_ratio`

오른발 IC에서 계산한 정규화 착지거리의 평균이다.

권장 정규화 기준은 다리 길이다. 신장 기준과 다리 길이 기준을 혼합하지 않는다.

### `overstride_asymmetry_pct`

```text
abs(left_overstride_ratio - right_overstride_ratio)
/
((abs(left_overstride_ratio) + abs(right_overstride_ratio)) / 2)
× 100
```
입력값은 다리 길이로 정규화 해야 합니다.
방향 저장:

```text
overstride_lr_difference_ratio
= left_overstride_ratio - right_overstride_ratio
```

## 7. 변동성과 개인 기준선

### 기존 `overstride_variability_cv_pct`

부호 있는 평균이 0에 가까우면 CV가 폭증하므로 다음 SD 기반 변수로 변경을 권장한다.

```text
overstride_variability_sd_ratio = SD(all pelvis_to_ankle_leg_length_ratio)
```

좌우 분리:

```text
left_overstride_variability_sd_ratio
right_overstride_variability_sd_ratio
```

기존 CV를 유지한다면:

```text
SD(distance_ratio)
/
Mean(abs(distance_ratio))
× 100
```

### 기존 `baseline_change_ratio`

권장 이름:

```text
baseline_overstride_change_ratio
```

```text
현재 mean_pelvis_to_ankle_leg_length_ratio - 개인 baseline_mean_overstride_ratio
```

동일한 속도, 카메라 방향, 촬영 조건, 정규화 방식, 신발, 경사도 및 IC 알고리즘에서 비교한다.

## 8. 범주형 파생변수

기존 `foot_position_relative_to_com`의 권장 이름:

```text
foot_position_relative_to_pelvis
```

범주:

```text
behind_pelvis
near_pelvis
ahead_of_pelvis
```

```python
if distance_ratio < -tolerance:
    category = "behind_pelvis"
elif distance_ratio > tolerance:
    category = "ahead_of_pelvis"
else:
    category = "near_pelvis"
```

`tolerance`는 RTMW 측정 오차를 검증한 후 결정한다. 현재 `near_pelvis` 허용범위나 overstriding 임계값을 확정할 수 없다.

## 9. RTMW 품질관리

- 정확한 측면 촬영 필요
- 진행 방향 저장
- F2·F3·FEAT-08에서 동일한 공통 gait event detector 사용
- IC 프레임의 양쪽 hip, 착지측 knee·ankle·heel·toe confidence 검사
- 사선 촬영, 원근 왜곡 및 가까운 다리·먼 다리 크기 차이 경고

## 10. FEAT-08 근거 해석

Souza의 근거 기반 러닝 영상 분석 논문에서는 **overstriding을 IC에서 heel과 신체 COM 사이의 거리로 설명하며 이 거리가 무릎 신전 모멘트와 관련된다고 정리했다.**

- [Souza, 2015](https://pmc.ncbi.nlm.nih.gov/articles/PMC4714754/)

Lieberman et al.은 실제 COM 대신 IC에서 발목–무릎 중심과 발목–고관절의 전후거리를 사용했다. 해당 연구에서 habitually barefoot 그룹은 habitually shod 그룹보다 무릎 대비 overstride가 약 50% 작았지만, 신발 습관, 경험, 케이던스, 속도와 노면 조건이 달랐으므로 50%를 정상 기준으로 사용할 수 없다.

과거 골 스트레스 부상 이력이 있는 남성 러너에게서 발이 COM보다 더 앞쪽에 위치하는 특징이 관찰됐지만 과거 부상 이력 그룹의 특성 비교이므로 미래 부상의 인과관계나 임계값으로 사용할 수 없다.

- [Bramah et al., 2021](https://pmc.ncbi.nlm.nih.gov/articles/PMC8169031/)

## 11. 사용자 피드백 설계

가능한 피드백:

> **착지 순간 발목이 골반 중심보다 진행 방향 앞쪽에 위치**했습니다. 이 값은 실제 질량중심이 아니라 RTMW 골반 중심을 이용한 영상 기반 추정값입니다.

> **같은 속도의 개인 기준선보다 착지 위치가 앞쪽으로 이동**했습니다. 보폭이 함께 길어졌거나 케이던스가 변했는지 확인해 보세요.

> **오른발 착지 위치가 왼발보다 상대적으로 앞쪽에 있었습니다.** 촬영 각도와 키포인트 검출 영향을 받을 수 있으므로 반복 영상에서 같은 경향이 나타나는지 확인하는 것이 좋습니다.

사용하면 안 되는 피드백:

> 발이 골반보다 1픽셀 앞에 있으므로 오버스트라이드입니다.

> 착지거리가 신장의 5%를 넘었으므로 위험합니다.

> 오버스트라이드 때문에 무릎 부상이 발생합니다.

> 발은 반드시 골반 바로 아래에 착지해야 합니다.

> 뒤꿈치로 착지하면 모두 오버스트라이드입니다.

## 12. FEAT-08 데이터 후보

| 자료 | 활용도 | 용도 |
|---|---|---|
| Lieberman PLOS 공개 데이터 | 높음 | 무릎·고관절 대비 overstride 정의와 조건별 비교 |
| Van Hooren OSF 데이터 | 매우 높음 | 3D 마커와 지면반력으로 IC 착지거리 계산 |
| Running Injury Clinic Dataset | 후보 | 대규모 3D 마커 착지 위치 분포 |
| Fukuchi 데이터 | 높음 | 속도별 착지 위치와 하지 운동학 비교 |
| 자체 RTMW 측면 영상 | 필수 | RTMW 거리 오차와 IC 검출 검증 |

## 13. FEAT-08 최종 분류

| 기존 변수 | 권장 변수 또는 상태 |
|---|---|
| FEAT-08A COM–발 거리 | 골반 중심–발목 착지거리로 변경 |
| FEAT-08B 무릎–발목 거리 | 채택 가능 |
| `com_to_foot_distance_px` | `pelvis_to_ankle_ap_distance_px` |
| `com_to_foot_distance_cm` | 보류, 카메라 보정 필요 |
| `com_to_foot_height_ratio` | `pelvis_to_ankle_height_ratio` |
| `com_to_foot_leg_ratio` | `pelvis_to_ankle_leg_length_ratio` |
| `knee_to_ankle_distance_px` | `knee_to_ankle_ap_distance_px` |
| `knee_to_ankle_height_ratio` | 채택 가능 |
| `left_overstride_ratio` | 채택 가능 |
| `right_overstride_ratio` | 채택 가능 |
| `overstride_asymmetry_pct` | 탐색적 좌우 비교 |
| `overstride_variability_cv_pct` | SD 기반 지표로 변경 권장 |
| `baseline_change_ratio` | `baseline_overstride_change_ratio` |
| `foot_position_relative_to_com` | `foot_position_relative_to_pelvis` |

핵심 원칙:

> RTMW에서 실제 COM을 측정했다고 표현하지 않고, Initial Contact 순간의 골반 중심 대비 발목 수평거리를 부호와 체격 정규화를 포함해 측정한다.

---

# 네 피처 공통 구현 원칙

## 공통 Gait Event Detector

F2, F3 및 FEAT-08은 동일한 IC와 toe-off 이벤트를 공유한다.

```text
RTMW frame keypoints
→ smoothing and confidence filtering
→ left/right foot event detection
→ common gait events
→ F2, F3, FEAT-08 calculations
```

## 공통 개인 기준선 조건

- 속도
- 경사도
- 신발
- 트레드밀 또는 야외
- 카메라 방향·높이·거리
- 영상 프레임레이트
- 워밍업과 피로 상태

## 근거 표시 방식

사용자 화면에서는 관찰, 의미, 추천 및 근거를 분리한다.

```text
관찰
평소보다 측정값이 변화했습니다.

의미
일부 연구에서 관련된 생체역학적 변화가 보고됐습니다.

추천
같은 조건에서 반복 측정하고 함께 변한 피처를 확인하세요.

근거
논문명, 저자, 학술지, 연도

자세히 보기
연구 대상, 속도, 장비, 측정 정의와 한계
```

수치 하나로 부상, 통증, 피로 또는 잘못된 자세를 확정하지 않는다.

# 추가 상체 피쳐 정리

RTMW 좌표로 측정 가능하고 무료 원문 근거가 확인되는 후보를 검토한 결과, 우선순위는 다음과 같습니다.

| 우선순위 | 상체 피처 후보          | 촬영 방향       | 판단       |
| ---- | ----------------- | ----------- | -------- |
| 1    | 팔 스윙 가동범위         | 측면          | 채택 권장    |
| 2    | 좌우 팔 스윙 비대칭       | 측면          | 채택 권장    |
| 3    | 몸통 측면(좌우) 기울기·흔들림 | 정면·후면       | 정면,후면확장  |
| 4    | 팔–다리 협응           | 측면          | 연구용 파생지표 |
| 5    | 팔꿈치 굴곡각           | 측면          | 보조지표     |
| 6    | 어깨–골반 회전          | 정면·후면 단일 영상 | 보류       |

## 1. 팔 스윙 가동범위(arm_swing_rom_deg)

### 기본 정의

달리는 동안 상완이 몸통을 기준으로 앞뒤로 움직이는 각도 범위입니다.

RTMW 키포인트:

- 왼쪽: 어깨 5, 팔꿈치 7, 손목 9
    
- 오른쪽: 어깨 6, 팔꿈치 8, 손목 10
    
- 몸통 기준선: 어깨 중심과 골반 중심을 연결한 선
    

상완 각도는 다음 두 벡터 사이의 부호 있는 각도로 정의할 수 있습니다.
![[Pasted image 20260819122012.png]]
![[Pasted image 20260819131728.png|332]]

|변수명|정의|
|---|---|
|`left_arm_swing_rom_deg`|왼팔 상완각의 최댓값−최솟값|
|`right_arm_swing_rom_deg`|오른팔 상완각의 최댓값−최솟값|
|`mean_arm_swing_rom_deg`|좌우 팔 스윙 ROM 평균|
|`arm_swing_variability_deg`|보폭별 팔 스윙 ROM의 표준편차|
|`baseline_arm_swing_change_deg`|개인 기준선 대비 현재 ROM 변화|

### 근거

팔 스윙은 단순한 장식 동작이 아니라 다리 움직임으로 생기는 각운동량을 상쇄하고, 상체의 회전 안정성을 유지하는 역할을 합니다. 능동적인 팔 스윙을 제한했을 때 상체 회전 안정성과 대사 효율이 달라질 수 있다는 실험 결과도 있습니다.

- [Active Arm Swing During Running Improves Rotational Stability and Metabolic Energy Efficiency, 2025](https://pmc.ncbi.nlm.nih.gov/articles/PMC11929735/)
    
- [Is There an Economical Running Technique? A Review, 2016](https://pmc.ncbi.nlm.nih.gov/articles/PMC4887549/)
    
- [Running Economy from a Muscle Energetics Perspective, 2017](https://pmc.ncbi.nlm.nih.gov/articles/PMC5479897/)
    

다만 현재 문헌만으로 모든 사람에게 적용할 수 있는 “이상적인 팔 스윙 각도”를 제시하기는 어렵습니다. 따라서 앱에서는 절대 합격 기준보다 개인 기준선과 좌우 균형을 이용해야 합니다.

논문이나 다른 자료 중 기준 지표를 찾아봐야 할 것 
## 2. 좌우 팔 스윙 비대칭

### 기본 정의

왼팔과 오른팔의 스윙 가동범위 차이를 평균 가동범위로 정규화한 값입니다.
```
arm_swing_asymmetry_pct =
abs(left_arm_swing_rom_deg - right_arm_swing_rom_deg)
/
((left_arm_swing_rom_deg + right_arm_swing_rom_deg) / 2)
× 100
```


![[Pasted image 20260819131307.png]]x`

|변수명|정의|
|---|---|
|`arm_swing_asymmetry_pct`|좌우 팔 스윙 ROM의 상대적 차이|
|`arm_swing_difference_deg`|왼팔 ROM−오른팔 ROM|
|`left_arm_swing_cv_pct`|왼팔 보폭별 ROM 변동계수|
|`right_arm_swing_cv_pct`|오른팔 보폭별 ROM 변동계수|
|`baseline_asymmetry_change_pct`|개인 기준선 대비 비대칭 변화|
![[Pasted image 20260819133544.png|400]]
https://geeksonfeet.com/run/arm-swing-elbow-form/ (디지털 러닝 커뮤니티 기사)
![[Pasted image 20260819133721.png]]
과도한 팔꿈치 회전 / 과도한 팔꿈치 벌어짐 
팔이 앞으로 나갈 때 팔꿈치가 가슴 중앙을 넘어 반대편까지 교차하는 과도한 회전 움직임/
발이 땅에 닿아 체중을 지지하는 중간 입각기(midstance) 단계에서 골반이 과도하게 떨어지거나(pelvic drop) 코어의 안정성이 부족하다는 것을 의미


### 근거와 주의점


주행 중 팔 동작을 영상으로 분석하는 방법의 타당도와 신뢰도를 검토한 연구가 있어, 상지 러닝 동작을 영상 기반으로 정량화하는 접근 자체에는 근거가 있습니다.

- [Validity and Reliability of Video-Based Analysis of Upper Extremity Running Gait, 2020](https://pmc.ncbi.nlm.nih.gov/articles/PMC7727438/)
    

하지만 건강한 사람에게도 자연스러운 좌우 차이가 나타날 수 있습니다. 따라서 비대칭 수치 하나만으로 부상이나 잘못된 자세라고 판정하면 안 됩니다.

- [Whole Body Kinematic Sex Differences Persist Across Non-Dimensional Walking and Running Speeds, 2020](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0237449)
    

권장 피드백은 다음과 같습니다.

> 이번 영상에서 오른팔과 왼팔의 스윙 범위 차이가 평소보다 크게 나타났습니다. 자연스러운 좌우 차이일 수 **있으므로** 반복 측정 결과를 함께 확인해 보세요.

## 3. 몸통 측면 기울기와 좌우 흔들림(trunk_lateral_lean_deg)
# 기존 피쳐1과 겹치는 부분이 있습니다.

### 기본 정의

정면 또는 후면 영상에서 골반 중심과 어깨 중심을 연결한 몸통 축이 영상의 수직선에서 좌우로 기울어진 각도입니다.

![[Pasted image 20260819131413.png]]

|변수명|정의|
|---|---|
|`trunk_lateral_lean_deg`|각 프레임의 부호 있는 몸통 측면 기울기|
|`mean_abs_trunk_lateral_lean_deg`|절댓값 기준 평균 측면 기울기|
|`trunk_lateral_sway_rom_deg`|좌우 기울기 최댓값−최솟값|
|`left_peak_trunk_lean_deg`|왼쪽 방향 최대 기울기|
|`right_peak_trunk_lean_deg`|오른쪽 방향 최대 기울기|
|`trunk_sway_asymmetry_pct`|좌우 최대 기울기의 상대적 차이|
|`trunk_sway_variability_deg`|보폭별 흔들림 범위의 표준편차|
|`baseline_trunk_sway_change_deg`|개인 기준선 대비 흔들림 변화|

### 근거

러닝 중 상부 몸통 움직임은 하지 움직임 및 골반 운동과 연결되어 있으며, 몸통과 골반의 운동 협응은 임상적 러닝 분석에서도 관찰해야 할 요소로 제안됩니다.

- [Relationship Between Lower Limb Kinematics and Upper Trunk Movement in Recreational Runners, 2020](https://pmc.ncbi.nlm.nih.gov/articles/PMC6988689/)
    
- [Setting Standards for Medically-Based Running Analysis, 2014](https://pmc.ncbi.nlm.nih.gov/articles/PMC4469466/)
    

다만 몸통 좌우 흔들림 역시 속도, 피로, 촬영 각도와 개인 체형의 영향을 받습니다. **초기 앱에서는 “정상/비정상”보다 평소 대비 증가와 반복적인 방향 편향을 알려주는 편**이 타당합니다.


## 4. 팔꿈치 굴곡각

![[Pasted image 20260819131511.png]]

|변수명|정의|
|---|---|
|`mean_elbow_flexion_deg`|분석 구간 평균 팔꿈치 굴곡각|
|`elbow_flexion_rom_deg`|팔꿈치 굴곡각의 최대−최소 범위|
|`elbow_flexion_asymmetry_pct`|좌우 굴곡 ROM의 상대적 차이|
|`elbow_flexion_variability_deg`|보폭별 굴곡각 변동성|

RTMW로 계산하기 쉽고 영상 분석 연구에서도 사용할 수 있지만, “팔꿈치는 반드시 90도여야 한다” 같은 숫자는 강한 과학적 기준으로 보기 어렵습니다. 따라서 독립적인 자세 점수보다는 팔 스윙을 설명하는 보조지표로 두는 것이 적절합니다.
```
피처명: elbow_flexion_deg
분류: 권고형 보조 피처
근거 수준: 낮음
권고 범위: 90–110°
근거 유형: 의료기관·코칭 권고
진단 임계값: 아님
부상 위험 판정: 사용 금지
```

Wilk 등(2024), University of Lausanne 및 Lausanne University Hospital:

[Impact of Elbow Stiffness on Running Economy in Trained Athletes](https://pmc.ncbi.nlm.nih.gov/articles/PMC11656458/?utm_source=chatgpt.com)

논문을 보면 Running economy was measured at 180 ± 10.6 mlO2·km−1·kg−1 with a full ROM, and 180.2 ± 12.3 mlO2·km−1·kg−1 with the limited ROM showing a non-significant 0.1% difference (_p_ = 0.871).
차이는 약 0.1%였으며 통계적으로 유의하지 않았습니다.
즉, 팔꿈치 움직임을 상당히 제한해도 시속 12km의 지구성 러닝 이코노미에는 유의한 차이가 없었습니다.

미국 의료기관 Intermountain Health는 팔꿈치를 약 90~110° 굽히도록 안내합니다. [Intermountain Health 러닝 가이드](https://intermountainhealthcare.org/blogs/article/efficient-running-mechanics?utm_source=chatgpt.com)
공신력 있는 기관의 일반적인 코칭 권고로는 인용할 수 있지만, 앱의 과학적 판정 기준으로 사용하기에는 부족합니다.

개인적인 생각으로 팔꿈치 각도는 단순 권고 정도로만 피드백해도 무관하지 않을까 생각합니다.

- 팔이 완전히 펴진 상태: 약 0°
- 팔꿈치가 직각인 상태: 약 90°

RTMW 키포인트:

- 왼쪽: 어깨 5, 팔꿈치 7, 손목 9
- 오른쪽: 어깨 6, 팔꿈치 8, 손목 10

의료기관 및 러닝 코칭 자료에서는 약 90～110°의 팔꿈치 굴곡을 일반적인 러닝 자세로 안내합니다. 식으로 피드백

2024년 체계적 문헌고찰에서도 개별 생체역학 변수 하나가 러닝 이코노미를 설명하는 정도는 제한적이며, 사람마다 경제적인 동작이 다를 가능성을 강조합니다. 따라서 상체 지표 역시 개인 기준선, 속도, 촬영 방향을 함께 기록해야 합니다. [2024 체계적 문헌고찰 및 메타분석](https://pmc.ncbi.nlm.nih.gov/articles/PMC11127892/)


Part A. 최종 핵심 피처
- FEAT-01 전방 기울기
- FEAT-02 IC 무릎 굴곡각
- FEAT-03 케이던스
- FEAT-08 골반 중심 대비 착지거리

Part B. 확장 피처
- FEAT-01F 몸통 좌우 기울기

Part C. 권고형 보조 피처
- 팔꿈치 굴곡각

Part D. 연구·검증 후보
- 팔 스윙 ROM
- 팔 스윙 비대칭
- 팔–다리 협응

Part E. 제외
- 머리·목·얼굴 각도
- 단일 2D 영상 기반 실제 어깨–골반 축회전
- 건강한 일반 러너를 대상으로 러닝 중 머리·목·얼굴 각도의 정상범위나 교정 임계값을 제시한 근거가 부족하다. 기존 연구는 주로 3D 모션캡처나 센서를 이용한 머리 가속도·시선 안정성을 측정하므로, RTMW의 2D 얼굴 키포인트로 동일하게 재현하기 어렵다. 따라서 근거 없는 정상·비정상 판정을 방지하기 위해 최종 피처에서 제외하였습니다.
