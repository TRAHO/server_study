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

제어판 앱스토어/소프트웨어에서 클릭 설치:

```
Nginx (또는 Apache) + MySQL/MariaDB + PHP 8.x
```

워드프레스·그누보드가 바로 동작할 수 있는 조합이다.

## 다음 단계

제어판·스택 준비 후 → [Phase 4](./phase-4-backup-dns.md)
