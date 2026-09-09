# 📖 69편 학술 논문 기반 러닝 생체역학 Feature 사전 (FEATURE DICTIONARY)

> **연구 논문 69편 심층 분석 기반 Biomechanical Features 사전 표 및 구체적 정의**

---

## 📌 Feature 사전 종합 요약표

| Feature ID  | 지표명 (한글 / 영문)                                              | 분류 (Category)               | 영상 Pose Estimation 측정 가능 여부                | 적정 권장 범위 / 임계값                                             | 핵심 생체역학적 의미                                                              | 대표 근거 논문                                                            |
| :---------: | :--------------------------------------------------------- | :-------------------------- | :----------------------------------------- | :--------------------------------------------------------- | :----------------------------------------------------------------------- | :------------------------------------------------------------------ |
| **FEAT-01** | **상체 전방 경사각**<br>`Trunk Forward Lean Angle`                | Kinematics (운동학)            | Group A (🟢 100% 비디오 직접 측정)                | 5° ~ 10° 전방 경사 (12° 초과 시 무릎 모멘트 악화)                        | 러닝 이코노미(VO2), 지면 충격 완화 및 허리/무릎 관절 부하 결정                                  | Carson et al. (2024), Kovše et al. (2025), Quan et al. (2026) 등 28편 |
| **FEAT-02** | **착지 시 무릎 굴곡각**<br>`Knee Flexion Angle at Initial Contact` | Kinematics (운동학)            | Group A (🟢 100% 비디오 직접 측정)                | 15° ~ 25° 굴곡 (10° 미만 시 Stiff Knee 착지로 부상 위험 상승)            | 착지 순간 충격 흡수(Shock Absorption) 능력 및 슬개대퇴관절(Patellofemoral) 충격 하중률(VLR) 제어 | Zhang et al. (2026), Carson et al. (2024), Yu et al. (2025) 등 34편   |
| **FEAT-03** | **분당 케이던스**<br>`Running Cadence (Step Rate)`               | Spatiotemporal (시공간)        | Group A (🟢 100% 프레임 카운팅 측정)               | 170 ~ 185 SPM (160 SPM 미만 시 과도한 충격 하중 발생)                  | 지면 제동력(Braking Force) 저감, 충격 분산 및 피로도 상태 추정                              | Quan et al. (2026), Wu et al. (2025), Yu et al. (2025) 등 42편        |
| **FEAT-04** | **지면 접지 시간**<br>`Ground Contact Time (GCT)`                | Spatiotemporal (시공간)        | Group A (🟢 100% 비디오 프레임 추적)               | 180 ~ 240 ms (피로 유발 시 평소 대비 5~15% 지속적 증가)                  | 하체 추진 수축력 상태, 신체 피로 누적(Fatigue)의 가장 대표적인 정량 지표                           | Wu et al. (2025), Quan et al. (2026), Yang et al. (2026) 등 36편      |
| **FEAT-05** | **공중 비행 시간**<br>`Flight Time (Airborne Time)`              | Spatiotemporal (시공간)        | Group A (🟢 100% 비디오 프레임 추적)               | 100 ~ 160 ms (속도 및 스프린트 강도에 비례)                            | 주행 탄성 및 Duty Factor(접지 비율) 계산의 기본 지표                                     | Wu et al. (2025), Breban et al. (2026) 등 15편                        |
| **FEAT-06** | **수직 동요**<br>`Vertical Oscillation of COM`                 | Kinematics / Spatiotemporal | Group A (🟢 100% 비디오 직접 측정)                | < 6 ~ 8 cm (10cm 초과 시 수직 에너지 손실 급증)                        | 수직 방향 에너지 손실량 및 러닝 이코노미(대사 효율) 결정                                        | Yang et al. (2026), Quan et al. (2026) 등 25편                        |
| **FEAT-07** | **착지 발 각도**<br>`Foot Strike Angle (FSA)`                   | Kinematics (운동학)            | Group A (🟢 100% 측면 비디오 측정)                | Midfoot / Light Rearfoot (-2° ~ 5°) (RFS > 8° 시 수직 충격률 상승) | 착지 유형 분류 (RFS: 뒤꿈치 착지, MFS: 미드풋, FFS: 전족부 착지)                            | Zhang et al. (2022), Jiang et al. (2025) 등 30편                      |
| **FEAT-08** | **질량중심 대비 착지거리**<br>`Overstriding Distance`                | Kinematics (운동학)            | Group A (🟢 100% 측면 비디오 측정)                | 최소화 (착지발이 질량중심 바로 아래 위치할수록 최적)                             | 지면 제동력(Braking Force) 발생 및 무릎 슬개골 충격력 유발 원인                              | Carson et al. (2024), Zhang et al. (2026) 등 18편                     |
| **FEAT-09** | **골반 측방 기울임**<br>`Pelvic Drop / Lateral Roll`              | Kinematics (운동학)            | Group B (🟡 정면/후면 칼리브레이션 필요)               | < 4° ~ 5° (5° 초과 Drop 발생 시 비정상 고관절 불안정성)                   | 중둔근(Gluteus Medius) 피로, 코어 약화 및 장경인대 증후군(ITBS) 연관                        | Kovše et al. (2025), Cenci et al. (2026) 등 22편                      |
| **FEAT-10** | **보행 비대칭 지수**<br>`Gait Asymmetry Index (ASI)`              | Spatiotemporal / Kinematics | Group B (🟡 연쇄 걸음 추적 및 비교 필요)              | < 3% ~ 5% (5% 초과 비대칭 지속 시 비정상 보행 패턴)                       | 좌우 근골격계 불균형, 국소 통증 보상 작용 및 신체 이상 징후 감지                                   | Cenci et al. (2026), Kovše et al. (2025) 등 20편                      |
| **FEAT-11** | **보폭 시간 변동성**<br>`Stride Time Variability (CV %)`          | Spatiotemporal (시공간)        | Group A/B (🟢 시공간 추적으로 프레임 산출)             | < 2.0% ~ 2.5% (피로 한계 도달 시 25~35% 급증)                       | 신경근조종(Neuromuscular Control) 붕괴 및 한계 피로도 도달 판정                           | Yang et al. (2026), Quan et al. (2026) 등 14편                        |
| **FEAT-12** | **지면 반발력 수직 충격률**<br>`Vertical Loading Rate (VLR)`         | Kinetics (운동역학)             | Group C (🔴 비디오 직접 측정 불가 - 가속도/역운동학 간접 추정) | < 60 ~ 70 BW/s (70 BW/s 초과 시 슬개골 및 피로골절 위험)                | 피부/뼈/관절에 가해지는 직접적인 수직 충격 스트레스 (부상 위험 요소)                                 | Quan et al. (2026), Carson et al. (2024), Zhang et al. (2026) 등 32편 |

---

## 🔍 각 Feature별 구체적 정의 및 계산 스펙

### 🔹 [FEAT-01] 상체 전방 경사각 (Trunk Forward Lean Angle)

- **분류 (Category)**: Kinematics (운동학)
- **정의**: 주행 중 수직선 대비 어깨-고관절 벡터가 전방으로 기울어진 내각(°)
- **수식 / 계산 로직**: `arctan2(Shoulder_X - Hip_X, Shoulder_Y - Hip_Y) * (180/pi)`
- **필요 Keypoint 및 시야각**: Shoulder (어깨), Hip (고관절) (Sagittal View (측면 뷰))
- **Pose Estimation 측정 그룹**: Group A (🟢 100% 비디오 직접 측정)
- **생체역학적 의미**: 러닝 이코노미(VO2), 지면 충격 완화 및 허리/무릎 관절 부하 결정
- **문헌 기준 권장 범위**: **5° ~ 10° 전방 경사 (12° 초과 시 무릎 모멘트 악화)**
- **자세 교정 / 개입(Intervention) 방법**: Conscious Forward Lean Cueing (상체 전방 기울임 인스트럭션)
- **관련 근거 논문**: Carson et al. (2024), Kovše et al. (2025), Quan et al. (2026) 등 28편

### 🔹 [FEAT-02] 착지 시 무릎 굴곡각 (Knee Flexion Angle at Initial Contact)

- **분류 (Category)**: Kinematics (운동학)
- **정의**: 발이 지면에 첫 닿는 순간(Initial Contact) 무릎 관절의 굽힘 내각(°)
- **수식 / 계산 로직**: `Angle between Thigh (Hip-Knee) & Shank (Knee-Ankle)`
- **필요 Keypoint 및 시야각**: Hip (고관절), Knee (무릎), Ankle (발목) (Sagittal View (측면 뷰))
- **Pose Estimation 측정 그룹**: Group A (🟢 100% 비디오 직접 측정)
- **생체역학적 의미**: 착지 순간 충격 흡수(Shock Absorption) 능력 및 슬개대퇴관절(Patellofemoral) 충격 하중률(VLR) 제어
- **문헌 기준 권장 범위**: **15° ~ 25° 굴곡 (10° 미만 시 Stiff Knee 착지로 부상 위험 상승)**
- **자세 교정 / 개입(Intervention) 방법**: Soft Landing Biofeedback (부드러운 착지 바이오피드백)
- **관련 근거 논문**: Zhang et al. (2026), Carson et al. (2024), Yu et al. (2025) 등 34편

### 🔹 [FEAT-03] 분당 케이던스 (Running Cadence (Step Rate))

- **분류 (Category)**: Spatiotemporal (시공간)
- **정의**: 1분(60초)당 양발의 총 걸음 수 (Steps Per Minute, SPM)
- **수식 / 계산 로직**: `60 * FrameRate / (Frame_IC2 - Frame_IC1)`
- **필요 Keypoint 및 시야각**: Ankle (발목), Heel (뒤꿈치), Toe (발가락) (Sagittal / Frontal / Rear (모든 시야각))
- **Pose Estimation 측정 그룹**: Group A (🟢 100% 프레임 카운팅 측정)
- **생체역학적 의미**: 지면 제동력(Braking Force) 저감, 충격 분산 및 피로도 상태 추정
- **문헌 기준 권장 범위**: **170 ~ 185 SPM (160 SPM 미만 시 과도한 충격 하중 발생)**
- **자세 교정 / 개입(Intervention) 방법**: Audio Metronome Cueing (+5% ~ +10% 케이던스 상향 훈련)
- **관련 근거 논문**: Quan et al. (2026), Wu et al. (2025), Yu et al. (2025) 등 42편

### 🔹 [FEAT-04] 지면 접지 시간 (Ground Contact Time (GCT))

- **분류 (Category)**: Spatiotemporal (시공간)
- **정의**: 발이 지면에 닿아 있는 순간(IC)부터 지면에서 떨어지는 순간(TO)까지의 지속 시간(ms)
- **수식 / 계산 로직**: `(Frame_ToeOff - Frame_InitialContact) / FrameRate * 1000`
- **필요 Keypoint 및 시야각**: Heel (뒤꿈치), Toe (발가락), Ankle (발목) (Sagittal View (측면 뷰, 60fps+ 권장))
- **Pose Estimation 측정 그룹**: Group A (🟢 100% 비디오 프레임 추적)
- **생체역학적 의미**: 하체 추진 수축력 상태, 신체 피로 누적(Fatigue)의 가장 대표적인 정량 지표
- **문헌 기준 권장 범위**: **180 ~ 240 ms (피로 유발 시 평소 대비 5~15% 지속적 증가)**
- **자세 교정 / 개입(Intervention) 방법**: Quick Feet / High Knee Drill (빠른 발 떼기 인스트럭션)
- **관련 근거 논문**: Wu et al. (2025), Quan et al. (2026), Yang et al. (2026) 등 36편

### 🔹 [FEAT-05] 공중 비행 시간 (Flight Time (Airborne Time))

- **분류 (Category)**: Spatiotemporal (시공간)
- **정의**: 양발이 모두 지면에서 떠 있는 체공 시간(ms)
- **수식 / 계산 로직**: `(Frame_NextIC - Frame_CurrentTO) / FrameRate * 1000`
- **필요 Keypoint 및 시야각**: Left/Right Ankle, Toe (Sagittal View (측면 뷰))
- **Pose Estimation 측정 그룹**: Group A (🟢 100% 비디오 프레임 추적)
- **생체역학적 의미**: 주행 탄성 및 Duty Factor(접지 비율) 계산의 기본 지표
- **문헌 기준 권장 범위**: **100 ~ 160 ms (속도 및 스프린트 강도에 비례)**
- **자세 교정 / 개입(Intervention) 방법**: Plyometric Stiffness Training (플라이오메트릭 강성 훈련)
- **관련 근거 논문**: Wu et al. (2025), Breban et al. (2026) 등 15편

### 🔹 [FEAT-06] 수직 동요 (Vertical Oscillation of COM)

- **분류 (Category)**: Kinematics / Spatiotemporal
- **정의**: 달리는 동안 신체 질량중심(고관절/골반)이 위아래로 이동하는 수직 변위 폭 (cm)
- **수식 / 계산 로직**: `(Max(Hip_Y) - Min(Hip_Y)) * Pixel_to_cm_Scale`
- **필요 Keypoint 및 시야각**: Hip (고관절), Pelvis (골반) (Sagittal View (측면 뷰))
- **Pose Estimation 측정 그룹**: Group A (🟢 100% 비디오 직접 측정)
- **생체역학적 의미**: 수직 방향 에너지 손실량 및 러닝 이코노미(대사 효율) 결정
- **문헌 기준 권장 범위**: **< 6 ~ 8 cm (10cm 초과 시 수직 에너지 손실 급증)**
- **자세 교정 / 개입(Intervention) 방법**: Smooth Level Running Cueing (위아래 튐 방지 큐잉)
- **관련 근거 논문**: Yang et al. (2026), Quan et al. (2026) 등 25편

### 🔹 [FEAT-07] 착지 발 각도 (Foot Strike Angle (FSA))

- **분류 (Category)**: Kinematics (운동학)
- **정의**: 착지 순간(IC) 지평선과 발바닥(Heel-Toe) 벡터가 이루는 내각(°)
- **수식 / 계산 로직**: `Angle of Foot Vector (Heel-Toe) relative to horizontal`
- **필요 Keypoint 및 시야각**: Heel (뒤꿈치), Ankle (발목), Toe (발가락) (Sagittal View (측면 뷰))
- **Pose Estimation 측정 그룹**: Group A (🟢 100% 측면 비디오 측정)
- **생체역학적 의미**: 착지 유형 분류 (RFS: 뒤꿈치 착지, MFS: 미드풋, FFS: 전족부 착지)
- **문헌 기준 권장 범위**: **Midfoot / Light Rearfoot (-2° ~ 5°) (RFS > 8° 시 수직 충격률 상승)**
- **자세 교정 / 개입(Intervention) 방법**: Foot Strike Retraining (미드풋 착지 전환 바이오피드백)
- **관련 근거 논문**: Zhang et al. (2022), Jiang et al. (2025) 등 30편

### 🔹 [FEAT-08] 질량중심 대비 착지거리 (Overstriding Distance)

- **분류 (Category)**: Kinematics (운동학)
- **정의**: 착지 순간 고관절(Hip) X좌표와 발목(Ankle) X좌표 간의 수평 전방 전위 거리(cm)
- **수식 / 계산 로직**: `(Ankle_X - Hip_X) at Initial Contact * Scale_Factor`
- **필요 Keypoint 및 시야각**: Hip (고관절), Ankle (발목) (Sagittal View (측면 뷰))
- **Pose Estimation 측정 그룹**: Group A (🟢 100% 측면 비디오 측정)
- **생체역학적 의미**: 지면 제동력(Braking Force) 발생 및 무릎 슬개골 충격력 유발 원인
- **문헌 기준 권장 범위**: **최소화 (착지발이 질량중심 바로 아래 위치할수록 최적)**
- **자세 교정 / 개입(Intervention) 방법**: Shorten Stride / Increased Cadence Cueing (보폭 줄이기 큐잉)
- **관련 근거 논문**: Carson et al. (2024), Zhang et al. (2026) 등 18편

### 🔹 [FEAT-09] 골반 측방 기울임 (Pelvic Drop / Lateral Roll)

- **분류 (Category)**: Kinematics (운동학)
- **정의**: 외발 지지기(Single Leg Stance) 동안 좌우 골반 키포인트의 높이차 각도(°)
- **수식 / 계산 로직**: `arctan2(Right_Hip_Y - Left_Hip_Y, Right_Hip_X - Left_Hip_X)`
- **필요 Keypoint 및 시야각**: Left Hip (좌 고관절), Right Hip (우 고관절) (Frontal / Rear View (정면/후면 뷰))
- **Pose Estimation 측정 그룹**: Group B (🟡 정면/후면 칼리브레이션 필요)
- **생체역학적 의미**: 중둔근(Gluteus Medius) 피로, 코어 약화 및 장경인대 증후군(ITBS) 연관
- **문헌 기준 권장 범위**: **< 4° ~ 5° (5° 초과 Drop 발생 시 비정상 고관절 불안정성)**
- **자세 교정 / 개입(Intervention) 방법**: Glute Strengthening & Level Pelvis Cueing (둔근 강화 훈련)
- **관련 근거 논문**: Kovše et al. (2025), Cenci et al. (2026) 등 22편

### 🔹 [FEAT-10] 보행 비대칭 지수 (Gait Asymmetry Index (ASI))

- **분류 (Category)**: Spatiotemporal / Kinematics
- **정의**: 좌측 및 우측 하체의 지면 접지 시간, 보폭, 각도의 불균형 비율 (%)
- **수식 / 계산 로직**: `|Left_Val - Right_Val| / (0.5 * (Left_Val + Right_Val)) * 100`
- **필요 Keypoint 및 시야각**: Left/Right Hips, Knees, Ankles (Frontal / Sagittal View (다중 프레임 연쇄 비교))
- **Pose Estimation 측정 그룹**: Group B (🟡 연쇄 걸음 추적 및 비교 필요)
- **생체역학적 의미**: 좌우 근골격계 불균형, 국소 통증 보상 작용 및 신체 이상 징후 감지
- **문헌 기준 권장 범위**: **< 3% ~ 5% (5% 초과 비대칭 지속 시 비정상 보행 패턴)**
- **자세 교정 / 개입(Intervention) 방법**: Personalized Biofeedback Asymmetry Retraining
- **관련 근거 논문**: Cenci et al. (2026), Kovše et al. (2025) 등 20편

### 🔹 [FEAT-11] 보폭 시간 변동성 (Stride Time Variability (CV %))

- **분류 (Category)**: Spatiotemporal (시공간)
- **정의**: 연속적인 걸음 주기 시간(Stride Time)의 표준편차를 평균으로 나눈 변동계수 (%)
- **수식 / 계산 로직**: `(SD_StrideTime / Mean_StrideTime) * 100`
- **필요 Keypoint 및 시야각**: Ankle, Foot Keypoints (Sagittal / Frontal View (최소 20걸음 이상 추적))
- **Pose Estimation 측정 그룹**: Group A/B (🟢 시공간 추적으로 프레임 산출)
- **생체역학적 의미**: 신경근조종(Neuromuscular Control) 붕괴 및 한계 피로도 도달 판정
- **문헌 기준 권장 범위**: **< 2.0% ~ 2.5% (피로 한계 도달 시 25~35% 급증)**
- **자세 교정 / 개입(Intervention) 방법**: Rhythmic Pace Maintenance (리듬 유지 주행 훈련)
- **관련 근거 논문**: Yang et al. (2026), Quan et al. (2026) 등 14편

### 🔹 [FEAT-12] 지면 반발력 수직 충격률 (Vertical Loading Rate (VLR))

- **분류 (Category)**: Kinetics (운동역학)
- **정의**: 착지 직후 지면 반발력이 최고 충격 피크까지 도달하는 시간당 힘의 상승 비율 (BW/s)
- **수식 / 계산 로직**: `Delta_GRF / Delta_Time at Impact Phase`
- **필요 Keypoint 및 시야각**: N/A (Force Plate 필요) (Kinetics Laboratory Equipment)
- **Pose Estimation 측정 그룹**: Group C (🔴 비디오 직접 측정 불가 - 가속도/역운동학 간접 추정)
- **생체역학적 의미**: 피부/뼈/관절에 가해지는 직접적인 수직 충격 스트레스 (부상 위험 요소)
- **문헌 기준 권장 범위**: **< 60 ~ 70 BW/s (70 BW/s 초과 시 슬개골 및 피로골절 위험)**
- **자세 교정 / 개입(Intervention) 방법**: Cadence Increase & Soft Landing Retraining
- **관련 근거 논문**: Quan et al. (2026), Carson et al. (2024), Zhang et al. (2026) 등 32편
