# 사이트 이전

브리핑 → [report-hosting-comparison.md](../report-hosting-comparison.md)

## 무엇이 달라지는가

현행 예: CentOS 7 + Apache 2.4 + MariaDB 10 + PHP 7.3  
신규 권장: Ubuntu LTS + Apache 2.4 + MariaDB 10.6+ + PHP 7.4/8.x

| 이슈 | 영향 |
|------|------|
| OS 교체 (CentOS 7 EOL) | 경로·패키지·권한 관행 차이 |
| PHP 7.3 → 7.4/8.x | deprecated·문법·확장 모듈로 사이트 오류 |
| DB 버전 상향 | 대체로 호환, 문자셋·sql_mode 확인 필요 |
| `.htaccess` / vhost | Apache 유지 시 수월, Nginx면 재작성 부담 |

## 대응 방향

1. 신규 서버 스택을 현행과 **호환 방향**으로 맞춤 (Apache 우선)
2. 제어판에서 **사이트별 PHP 버전** 지정 (레거시는 7.4, 신규는 8.x 등)
3. 이전 전: 스테이징(또는 임시 도메인)에서 소스+DB 올려 보고 확인
4. 체크: PHP 확장(mysqli, gd, mbstring, zip, curl), cron, 업로드 경로, 메일
5. DNS 전환은 검증 후, 가능하면 TTL을 미리 낮춰 두기