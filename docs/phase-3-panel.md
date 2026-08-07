# Phase 3. 제어판 설치 및 환경 최적화

## 목표

SSH로 서버에 접속해 제어판을 올리고, 마스터 도메인·SSL·웹 스택을 준비한다.

## 3-1. SSH 접속

```bash
ssh root@서버IP
# 또는
ssh -i ~/.ssh/키파일 ubuntu@서버IP
```

Windows: PuTTY / Windows Terminal / PowerShell

## 제어판 선택

| 제품 | 비용 | 비고 |
|------|------|------|
| Plesk | 유료 | 에이전시·다중 고객 관리에 권장 |
| aaPanel | 무료 | 비용 절감·학습용에 적합 |

### 설치 (공식 문서의 최신 원클릭 명령 사용)

설치 후 터미널에 출력되는 **URL / 아이디 / 비밀번호**를 반드시 저장한다.

| 항목 | 값 |
|------|-----|
| 제어판 URL | |
| 관리자 ID | |
| 초기 비밀번호 | |
| 사용 포트 | 8888 (aaPanel) / 8443 (Plesk) |

## 3-2. 마스터 도메인·SSL

1. DNS에서 `panel.내에이전시.com` → 서버 IP (A 레코드)
2. 브라우저로 `https://panel.내에이전시.com` 접속 가능하도록 설정
3. 제어판에 Let's Encrypt SSL 적용

## 3-3. 웹 스택 설치

현재 운영 서버와 맞추려면 **Nginx보다 Apache**를 우선한다.  
(기존: CentOS7 + Apache 2.4 + MariaDB 10 + PHP 7.3 → [current-stack.md](./current-stack.md))

제어판 앱스토어/소프트웨어에서 클릭 설치:

```
Apache 2.4.x + MariaDB 10.6+ + PHP 7.4 / 8.x (다중 버전)
```

| 현재 | 신규 목표 | 메모 |
|------|-----------|------|
| Apache 2.4.6 | Apache 2.4.x | `.htaccess` 호환 유지 |
| MariaDB 10.0.21 | MariaDB 10.6+ | 요구(10+) 충족 |
| PHP 7.3.33 | 7.4 이상 (가능하면 8.x) | 7.3 EOL → 사이트별 버전 지정 |
| CentOS 7 | (OS는 Ubuntu) | 스택만 호환, OS는 교체 |

## 다음 단계

제어판·스택 준비 후 → [Phase 4](./phase-4-backup-dns.md)
