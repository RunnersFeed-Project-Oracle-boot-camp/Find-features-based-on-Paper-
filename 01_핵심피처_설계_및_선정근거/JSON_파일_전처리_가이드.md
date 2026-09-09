우선 세현님이 주신 json 파일의 구조는 다음과 같다.
문제가 있다 내가 선정한 피쳐들은 RTMW 기준이었는데 `RTMW COCO-WholeBody 133`이 아니라
Halpe-26이다.
```
원본 JSON 확인
→ 모델·키포인트 형식 확인
→ 프레임별 좌표를 표 형태로 변환
→ 주 러너만 선택
→ 관측값·보간값·결측값 구분
→ 좌표 스무딩
→ 착지 이벤트 검출
→ F1·F2·F3·FEAT-08 계산
```

```
Detector: RTMDet-nano
Pose model: RTMPose-M
Keypoint format: Halpe-26
Keypoints: 26개
Frames: 500개
```

```
pose_predictions.json
├── model
│   ├── detector
│   ├── pose
│   ├── keypoints
│   ├── keypoint_format
│   └── device
└── frames
    ├── image_path
    └── people
        ├── track_id
        ├── bbox
        ├── bbox_score
        ├── keypoints
        ├── keypoint_scores
        ├── observed
        └── imputed_keypoints
```
각 배열 구조는 다음과 같다.
```
keypoints:          (26, 2)  # x, y
keypoint_scores:    (26,)
observed:           (26,)
imputed_keypoints:  (26,)
```
공식 MMPose 문서의 Halpe 데이터셋 안내에 따르면
[공식 MMPose 문서의 Halpe 데이터셋 안내](https://mmpose.readthedocs.io/)
MMPose 일반 문서에서는 Halpe 데이터셋을 지원한다는 설명만 나오고, 26개 관절 인덱스 표는 바로 보이지 않습니다. 실제 인덱스는 공식 GitHub 저장소의 데이터셋 설정 파일에서 확인합니다.

공식 자료:

- [MMPose Halpe 데이터셋 문서](https://mmpose.readthedocs.io/en/latest/dataset_zoo/2d_wholebody_keypoint.html#halpe)
    
- [MMPose Halpe-26 설정 파일](https://github.com/open-mmlab/mmpose/blob/main/configs/_base_/datasets/halpe26.py)
    

두 번째 링크를 연 다음 `Ctrl + F`를 눌러 다음을 검색하세요.

```text
keypoint_info
```

그러면 다음과 비슷한 Python 딕셔너리가 나옵니다.

```python
keypoint_info = {
    0: dict(name="nose", id=0, ...),
    1: dict(name="left_eye", id=1, ...),
    2: dict(name="right_eye", id=2, ...),
    ...
    20: dict(name="left_big_toe", id=20, ...),
    21: dict(name="right_big_toe", id=21, ...),
    22: dict(name="left_small_toe", id=22, ...),
    23: dict(name="right_small_toe", id=23, ...),
    24: dict(name="left_heel", id=24, ...),
    25: dict(name="right_heel", id=25, ...),
}
```

여기에서:

- 딕셔너리 왼쪽 숫자: JSON 배열의 인덱스
    
- `name`: 관절 이름
    
- `id`: 키포인트 ID
    
- `swap`: 좌우 반전 시 대응 관절
    

예를 들어 다음 설정은:

```python
20: dict(
    name="left_big_toe",
    id=20,
    swap="right_big_toe"
)
```

JSON에서 다음을 의미합니다.

```python
person["keypoints"][20]        # 왼쪽 엄지발가락 좌표
person["keypoint_scores"][20]  # 왼쪽 엄지발가락 신뢰도
person["observed"][20]         # 직접 관측 여부
```

Python에서 확인하면:

```python
left_big_toe_xy = first_person["keypoints"][20]
left_big_toe_score = first_person["keypoint_scores"][20]

print("왼쪽 엄지발가락 좌표:", left_big_toe_xy)
print("신뢰도:", left_big_toe_score)
```

우리 JSON 자체에도 다음 메타데이터가 들어 있습니다.

```python
pose_data["model"]
```

결과:

```text
pose: RTMPose-M Halpe-26
keypoints: 26
keypoint_format: Halpe-26
```

따라서 `halpe26.py`의 순서를 연결한 것입니다. 다만 팀원이 JSON을 생성하면서 키포인트 순서를 임의로 변경했을 가능성까지 완전히 배제하려면, JSON 생성 코드에서도 `keypoints`를 저장하는 부분을 한 번 확인해야 합니다. 표준 MMPose 출력을 순서 변경 없이 저장했다면 공식 `halpe26.py` 인덱스를 그대로 사용하면 됩니다.

| 인덱스 | 키포인트           | 인덱스 | 키포인트            |
| --: | -------------- | --: | --------------- |
|   0 | Nose           |  13 | Left knee       |
|   1 | Left eye       |  14 | Right knee      |
|   2 | Right eye      |  15 | Left ankle      |
|   3 | Left ear       |  16 | Right ankle     |
|   4 | Right ear      |  17 | Head            |
|   5 | Left shoulder  |  18 | Neck            |
|   6 | Right shoulder |  19 | Hip center      |
|   7 | Left elbow     |  20 | Left big toe    |
|   8 | Right elbow    |  21 | Right big toe   |
|   9 | Left wrist     |  22 | Left small toe  |
|  10 | Right wrist    |  23 | Right small toe |
|  11 | Left hip       |  24 | Left heel       |
|  12 | Right hip      |  25 | Right heel      |
다시 맵핑해야한다.
우선 깃허브 가서 키포인트 보면 0~25 keypoint가 있다. 총 26개 이것을 하나하나 맵핑해줘야 할 것 같다.
