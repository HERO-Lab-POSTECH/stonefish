# CHANGELOG

## [Unreleased]

### Added
- FLS semantic segmentation output — 강도 이미지와 픽셀 정렬된 클래스 라벨(beam x bin, R16UI)
  - 라벨 소스: `.scn`의 `<segmentation class="N"/>` (`<static>`/`<dynamic>`/`<animated>` 자식, 없으면 0=배경)
  - `Entity::setSegmentationClassId()` / `getSegmentationClassId()`, `Renderable.classId`
  - `FLS::getSegmentationDataPointer()` — beam x bin, `GLushort`, 강도 이미지와 같은 격자
  - 동작 근거: `sonarOutput.comp`가 bin마다 샘플을 하나만 고르므로(가까운 range 우선)
    "이 픽셀을 만든 표면"이 단일하게 정의된다. 승자의 클래스를 함께 나른다
  - 라벨은 postprocess Gaussian blur와 노이즈를 타지 않는다 — 보간된 클래스 id는 무의미
  - 입력 텍스처 `GL_RG32F` -> `GL_RGBA32F` (`GL_RGB32F`는 image load/store 비호환)
  - 제약: FLS 전용. SSS/MSIS는 `sonarOutput2.comp`가 bin을 누적하는 구조라 별도 설계 필요
  - 검증: RTX 4070 실기. 5개 클래스가 각자 기하학적 slant range에 ±1 bin 착지,
    물체 평균 강도 38~70 (해저면 8.6·배경 0.9), occlusion 그림자 확인
- Runtime wind velocity control via `Atmosphere::SetWindVelocity()` method
  - 목적: 실행 중 대기 바람 속도 동적 제어
  - 좌표계: NED (North, East, Down) in m/s
  - 검증:
    - 수직 성분(Down) > 0.1 m/s 시 경고
    - 풍속 > 25 m/s 시 경고
    - 풍속 > 50 m/s 시 거부 (안전 제한)
  - 제약: Uniform 바람 타입만 지원
  - 활용: ROS2 `SetWindVelocity` 서비스와 연동
