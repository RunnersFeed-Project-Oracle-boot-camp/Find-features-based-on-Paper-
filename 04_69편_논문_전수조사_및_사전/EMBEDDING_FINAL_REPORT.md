# 📘 EMBEDDING FINAL REPORT (논문 벡터 유사도 분석 최종 보고서)

> **총 수집 논문**: 69편 | **추출 성공**: 69편 | **생성된 Chunk 수**: 1727개
> **임베딩 모델**: `sentence-transformers/all-MiniLM-L6-v2` (Sentence Transformers, CUDA)

---

## 📊 1. 요약 결과
- **FAISS Vector Index**: 1727개 벡터 인덱싱 완료 (`papers.faiss`).
- **Paper-level Vector**: 69편의 L2-normalized 384d 평균 풀링 벡터 저장 완료 (`paper_embeddings.npy`).
- **최적 K-Means 군집 수**: **K=3** (Silhouette Score: 0.0932).

---

## 🏆 2. 가장 유사한 논문 Pair TOP 20
1. **[P02] 2025_J_Biomechanical_insights_into_carbo** ↔ **[P66] 2026_S_Carbon_plates_in_running_shoes_bi** (Cosine Sim: **0.9211**)
2. **[P42] 2026_JCC_Effect_of_high_time_under_tensi** ↔ **[P55] 2025_S_Effect_of_complex_training_on_low** (Cosine Sim: **0.9111**)
3. **[P09] 2025_J_Alterations_in_pelvic_kinematics_** ↔ **[P49] 2026_W_Effects_of_running_distance_on_pe** (Cosine Sim: **0.9087**)
4. **[P09] 2025_J_Alterations_in_pelvic_kinematics_** ↔ **[P10] 2025_N_Influence_of_running_speed_inclin** (Cosine Sim: **0.907**)
5. **[P61] 2026_F_Effects_of_high-intensity_interva** ↔ **[P65] 2026_M_Effect_of_high-intensity_interval** (Cosine Sim: **0.9021**)
6. **[P49] 2026_W_Effects_of_running_distance_on_pe** ↔ **[P62] 2026_H_Biomechanical_differences_between** (Cosine Sim: **0.899**)
7. **[P58] 2025_Z_A_review_of_uphill_and_downhill_r** ↔ **[P59] 2025_Z_Footwear_technology_and_biomechan** (Cosine Sim: **0.8984**)
8. **[P10] 2025_N_Influence_of_running_speed_inclin** ↔ **[P49] 2026_W_Effects_of_running_distance_on_pe** (Cosine Sim: **0.8965**)
9. **[P57] 2025_Y_A_mini-review_of_mathematical_met** ↔ **[P63] 2026_J_Pioneers_and_paradigms_in_sprint_** (Cosine Sim: **0.8911**)
10. **[P56] 2025_EN_Metabolic_effects_of_carbon-plat** ↔ **[P66] 2026_S_Carbon_plates_in_running_shoes_bi** (Cosine Sim: **0.8902**)
11. **[P09] 2025_J_Alterations_in_pelvic_kinematics_** ↔ **[P62] 2026_H_Biomechanical_differences_between** (Cosine Sim: **0.8894**)
12. **[P17] 2025_Y_Effects_of_the_difference_foot_st** ↔ **[P28] 2026_Y_A_preliminary_study_on_the_effect** (Cosine Sim: **0.885**)
13. **[P49] 2026_W_Effects_of_running_distance_on_pe** ↔ **[P58] 2025_Z_A_review_of_uphill_and_downhill_r** (Cosine Sim: **0.8843**)
14. **[P27] 2025_B_Differences_in_lower_extremity_bi** ↔ **[P49] 2026_W_Effects_of_running_distance_on_pe** (Cosine Sim: **0.8842**)
15. **[P10] 2025_N_Influence_of_running_speed_inclin** ↔ **[P27] 2025_B_Differences_in_lower_extremity_bi** (Cosine Sim: **0.883**)
16. **[P05] 2026_W_Physiological_associations_with_h** ↔ **[P30] 2025_E_The_effect_of_XC-running_race_Lid** (Cosine Sim: **0.8824**)
17. **[P10] 2025_N_Influence_of_running_speed_inclin** ↔ **[P25] 2026_S_Running_gait_kinematics_are_repro** (Cosine Sim: **0.8821**)
18. **[P25] 2026_S_Running_gait_kinematics_are_repro** ↔ **[P26] 2022_Z_Validity_and_reliability_of_inert** (Cosine Sim: **0.8817**)
19. **[P01] 2025_B_Advanced_footwear_technology_in_w** ↔ **[P51] 2026_Y_Effects_of_a_wrapping_closure_lac** (Cosine Sim: **0.88**)
20. **[P02] 2025_J_Biomechanical_insights_into_carbo** ↔ **[P59] 2025_Z_Footwear_technology_and_biomechan** (Cosine Sim: **0.8798**)


---

## 🏷️ 3. 연구 Cluster 결과 (K=3)
- 자세한 군집 키워드 및 대표 논문 분석은 [`embeddings/CLUSTER_ANALYSIS.md`](file:///home/jwp/dataset-agent/running_papers/embeddings/CLUSTER_ANALYSIS.md)를 참고하세요.

---

## ⚠️ 4. 한계점 및 주의사항 (Caution)
1. **유사도 ≠ 과학적 근거 수준**: Cosine Similarity가 높다는 것은 단지 문맥/주제(Semantic Content)가 유사하다는 의미일 뿐, 실험 증거의 강도나 논문 품질 점수를 의미하지 않습니다.
2. **원문 사실과의 분리**: 임베딩 유사도로 새로운 인과관계를 추론하지 않으며, 원문 논문에 수재된 측정 수치를 고유 보존합니다.
