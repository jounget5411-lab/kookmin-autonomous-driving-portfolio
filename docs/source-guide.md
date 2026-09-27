# 구현 설명과 근거 코드

[프로젝트 소개로 돌아가기](../README.md)

기준 커밋: `8036461664402a54821b0665a4434e961f9a2e76`

| 공개 문서에서 사용할 내용 | 직접 확인한 근거 |
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

