# 구현 근거와 개발 기록

[프로젝트 소개로 돌아가기](../README.md)

기준 커밋: `8036461664402a54821b0665a4434e961f9a2e76`

코드는 최종 구현을, 개발 기록은 그 구현을 선택하게 된 관찰과 실험을 설명합니다. 소스에 설정이 존재한다는 사실과 과거 실험에서 얻은 성능 수치를 구분했습니다.

## 소스로 확인할 수 있는 구조

| 설명 | 구현 근거 |
|---|---|
| 예선 시뮬레이터 최종 제출 / 본선 V7 / 초기 실차 이식 코드 구분 | [루트 README](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/README.md) |
| 본선 V7 원본은 팀 저장소의 고정 커밋 | [V7 안내](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/본선_최종_V7_안내.md) |
| 예선: 차선·객체·신호등 노드 → 융합 → 경로 → 제어 | [예선 launch](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/예선/src/track_drive/launch/drive.launch.py#L17-L27) |
| 예선: WAIT / CONE / LANE, 라바콘·차선 2차 피팅 | [예선 path planner](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/예선/src/track_drive/track_drive/path_planner_node.py#L1-L23) |
| 예선: 다점 pursuit와 heading 기반 조향 | [예선 motion](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/예선/src/track_drive/track_drive/motion_node.py#L1-L12) |
| 본선: YOLO segmentation + traffic-light crop classifier | [yolo_bev_node.py](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/본선/src/track_drive_cnn_gpt/track_drive_cnn_gpt/yolo_bev_node.py#L693-L750) |
| 본선: 최신 프레임만 남기는 입력 버퍼 | [yolo_bev_node.py](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/본선/src/track_drive_cnn_gpt/track_drive_cnn_gpt/yolo_bev_node.py#L60-L91) |
| BEV 128×120, 0.025m/cell, 중앙선·차선·LiDAR 3채널 | [bev_geometry.py](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/본선/src/track_drive_cnn_gpt/track_drive_cnn_gpt/bev_geometry.py#L22-L32) |
| 네 모델을 미리 로드하고 활성 모델 하나를 추론 | [모델 로드](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/본선/src/track_drive_cnn_gpt/track_drive_cnn_gpt/cnn_path_node.py#L329-L351), [활성 모델 추론](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/본선/src/track_drive_cnn_gpt/track_drive_cnn_gpt/cnn_path_node.py#L1062-L1095) |
| 일반 모드의 도로 밖 LiDAR 제거 / CONE 모드의 별도 처리 | [cnn_path_node.py](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/본선/src/track_drive_cnn_gpt/track_drive_cnn_gpt/cnn_path_node.py#L897-L933) |
| V7의 장애물 회피는 hardcoded_all | [perception_cnn.yaml](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/본선/src/track_drive_cnn_gpt/config/perception_cnn.yaml), [simple_motion.yaml](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/본선/src/track_drive_cnn_gpt/config/simple_motion.yaml) |
| 고정 조향 블록 실행 중 CNN 모드는 GENERAL로 유지 | [cnn_path_node.py](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/본선/src/track_drive_cnn_gpt/track_drive_cnn_gpt/cnn_path_node.py#L1022-L1046) |
| simple_motion은 전방 목표점의 bearing에 게인을 곱하고 IIR 평활화 | [simple_motion_node.py](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/본선/src/track_drive_cnn_gpt/track_drive_cnn_gpt/simple_motion_node.py#L62-L109) |
| 제어 주기는 20Hz 설정 | [simple_motion.yaml](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/본선/src/track_drive_cnn_gpt/config/simple_motion.yaml) |
| 차량 명령은 /xycar_motor로 발행 | [simple_motion_node.py](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/본선/src/track_drive_cnn_gpt/track_drive_cnn_gpt/simple_motion_node.py#L477-L500) |
| 배포 모델 6개 PT + OpenVINO 파일의 SHA256 기록 | [manifest.json](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/본선_배포모델/manifest.json) |

| 추가 설명 | 구현 근거 |
|---|---|
| 3채널 입력과 전방 0.3–3.0 m의 28점 경로 규격 | [path_contract.py](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/본선/src/track_drive_cnn_gpt/track_drive_cnn_gpt/path_contract.py#L20-L32) |
| 차선·LiDAR 특징을 별도로 인코딩한 경로 모델 | [path_model.py](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/본선/src/track_drive_cnn_gpt/track_drive_cnn_gpt/path_model.py#L188-L231) |
| 필요한 토픽만 연결하는 모터 브리지 | [motor_up_gpt.sh](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/본선/src/track_drive_cnn_gpt/tools/motor_up_gpt.sh) |
| 속도 변화량 제한과 명시적 정지 분리 | [simple_motion.yaml](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/본선/src/track_drive_cnn_gpt/config/simple_motion.yaml), [simple_motion_node.py](https://github.com/jounget5411-lab/2026-Legend-KHUSLA/blob/8036461664402a54821b0665a4434e961f9a2e76/본선/src/track_drive_cnn_gpt/track_drive_cnn_gpt/simple_motion_node.py) |

## 개발·대회 기록에 근거한 내용

| 내용 | 기록의 종류 | 공개 문서에서의 범위 |
|---|---|---|
| 예선 130팀 → 본선 22팀, 최종 8위·전체 코스 완주 | 대회 후 결과 기록 | 공식 결과표를 별도로 대조한 것으로 표현하지 않음 |
| 갈림길 정보가 대표점 축약 과정에서 사라진 사례 | 처리 단계 비교와 BEV 화면 | 해당 프레임의 표현 차이와 설계 변경 설명 |
| LiDAR ON/OFF와 점유 분포 비교 | 실차 디버깅 기록 | 학습·배포 입력 차이와 모드별 전처리의 근거 |
| OpenVINO 비교·CPU 경합·경로 발행률 | 개발 측정 기록 | 모델 단독 측정과 파이프라인 측정 조건 구분 |
| GENERAL 테스트 경로 MAE 8.045 cm | 모델 학습·평가 기록 | 분리한 테스트 321장의 경로 오차 |
| 경로 라벨 보정 화면 | 개발 도구 화면 | 초기 데이터 작업 과정의 예시 |
| VESC 전압 강하와 하위 블로킹 램프 | 실차 제어 디버깅 기록 | 관찰과 변경 이유, 최종 설정과의 연결 |

대회 결과와 과거 실험은 기록을 재구성한 내용입니다. 연결된 소스만으로 순위나 과거 측정 수치가 증명된다고 설명하지 않습니다. 모델 평가와 측정 조건은 [측정 조건과 구현 범위](technical-notes.md), 원인 분석 과정은 [문제 해결 사례](engineering-cases.md)에 정리했습니다.

