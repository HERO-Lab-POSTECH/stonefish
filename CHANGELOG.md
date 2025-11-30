# CHANGELOG

## [Unreleased]

### Added
- Runtime wind velocity control via `Atmosphere::SetWindVelocity()` method
  - 목적: 실행 중 대기 바람 속도 동적 제어
  - 좌표계: NED (North, East, Down) in m/s
  - 검증:
    - 수직 성분(Down) > 0.1 m/s 시 경고
    - 풍속 > 25 m/s 시 경고
    - 풍속 > 50 m/s 시 거부 (안전 제한)
  - 제약: Uniform 바람 타입만 지원
  - 활용: ROS2 `SetWindVelocity` 서비스와 연동
