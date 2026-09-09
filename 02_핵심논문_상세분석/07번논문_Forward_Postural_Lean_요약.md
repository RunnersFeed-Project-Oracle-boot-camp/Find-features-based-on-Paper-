# The effect of forward postural lean on running economy, kinematics, and muscle activation

# Fit: Running Economy 중심

# 피처값:
- 1. **전방 자세 기울기(Postural lean angle)**: c7 마커까지 연결한 절대각
전방 자세 기울기= 지지측 발목–C7 선과 수직축 사이 각도
- 2. **몸통 굴곡각(torso flexion angle):** torso flexion angle (sacral marker to c7 marker relative to the vertical axis) across the gait cycle.)
- 천골마커에서 C7마커까지 연결한 분절과 수직축 사이의 각도

# outcome:

# 근거:
![[Pasted image 20260831130630.png|241]]
그래프 해석이 결론에 있습니다.

# 결론: 
- Postural lean angle이 커질수록 net metabolic cost가 증가(밑은 원문)
- Howerver, our seconde hypothesis, increases in forward postural lean signifi cantly increased net metabolic cost by 8% (worsened running economy) F(1, 64) = 11.592, p < .001 (Fig 2).
- net metabolic cost(power)(순 대사 비용): 사람이 걷거나 달리는 등 특정 신체 활동을 할 때 추가로 소모되는 순수 에너지량을 의미(출처: 위키피디아)
- 즉 순 대사 비용이 증가했다는 것은 동작을 하는데 힘을 더 사용하였고 이는 에너지 효율일 떨어졌다는 의미입니다.
-  큰 전방 기울기보다 직립 또는 중간 정도의 전방 기울기(more upright or moderate forward postural lean)가 에너지 측면에서 더 적절할 가능성이 있다고 결론을 내렸습니다. 하지만 여기서도 이거 따라서 해석하면 안된다고 하긴 합니다.
- 

# 대상: 
- 건강한 젊은 성인 러너 16명(남성8명, 여성8명)

# 측정방법: 
- 처음 5분 동안 트레드밀 적응 실시 후 5가지 조건을 수행(5분 마다)
1. 직립 자세(upright/minimal lean)
2. 발목에서 중간 기울기(moderate ankle lean)
3. 발목에서 최대 기울기(maximal ankle lean)
4. 몸통에서 중간 기울기(moderate torso lean)
5. 몸통에서 최대 기울기(maximal torso lean
- 러닝 경제성은 TrueOne2400 장비 사용
- Posturl , torso 각도는 전체 보행 주기(gait cycle)에 걸쳐 평균을 계산

# 구현방법  :
- 전방 1m 설치된 모니터에서 60HZ 실시간 2D 측면 영상 피드백 확인
- 발목을 앞으로 기울이라는 지시, 엉덩이 또는 몸통을 기울이라는 지시를 참가자에게 하였고 그것에 맞춰 참가자들이 달림
- 러닝 경제성은 True One 2400 장비를 사용해 간접열량측정법으로 구했다네요
- ![[Pasted image 20260831121606.png|342]]
# 피처측정(**Halpe26로)**:  
- 1. postural_lean_angle : 지지측 발목관절(stance-side ankle) - neck- 영상 수직축(vertical axis)
- 2. 몸통 굴곡각(torso flexion angle)  
= 엉덩이 중심–목(neck) 선과 영상 수직축 사이의 각
# 기준 설정:
- 전방 자세 기울기/ 몸통 굴곡각 실제 측정된 평균 각도
-![[Pasted image 20260831132503.png|402]]
Upright/Moderate Ankle, Moderate Torso/ Large Ankle, Torso 순
(논문 원문: 순 대사 비용까지 포함)
![[Pasted image 20260831132637.png]]
	
# 추가의견:
앱에서는 “전방 기울기가 클수록 반드시 비효율적이다”보다, **사용자의 평소 자세보다 전방 기울기가 크게 증가했을 때 러닝 경제성이 저하될 가능성이 있다**는 식으로 제한적으로 해석해야 합니다