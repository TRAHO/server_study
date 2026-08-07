# 웹에이전시 자체 호스팅 인프라 로드맵

스마일서브 클라우드(**iwinv**)를 활용해 웹에이전시 자체 호스팅 인프라를 구축하고, 고객사를 입주시키기까지의 실전 로드맵입니다.

**흐름:** 준비 → 인프라 구매 → 제어판 세팅 → 보안/백업 → 고객 입주

---

## Phase 1. 준비 단계 — 마스터 도메인 확보

에이전시 브랜드를 대표할 **마스터 도메인**이 필요합니다.  
호스팅 제어판 주소와 고객사가 바라볼 네임서버 주소의 기준이 됩니다.

| 할 일 | 상세 |
|--------|------|
| 도메인 구매 | 가비아, 호스팅KR 등에서 `내에이전시.com` 형태의 도메인 1개 구매 |

---

## Phase 2. 인프라 구축 — 스마일서브 클라우드(iwinv) 구매

[iwinv](https://www.iwinv.kr/) 콘솔에 가입하고 서버를 생성합니다.

### 2-1. 클라우드 인스턴스(서버) 생성

| 항목 | 권장 |
|------|------|
| 상품 | **General Purpose (GP)** — High Performance / Storage Optimized는 초기 불필요 |
| OS | Ubuntu Server **22.04 LTS** 또는 **24.04 LTS** |
| 사양 | 초기(~10개) **2Core / 4~8GB** → 규모가 커지면 스펙업·서버 분산 ([상세](./docs/phase-2-iwinv.md)) |

> 스펙은 나중에 콘솔에서 언제든 올릴 수 있습니다. 고객 ~100개·~300개 규모 가이드는 Phase 2 실습 노트 참고.

### 2-2. 블록 스토리지(디스크) 분리 추가

- OS용 기본 디스크(보통 25~50GB)와 **별도**로 추가 SSD를 연결(마운트)
- 용도: 고객사 웹 데이터 + DB 저장
- 용량 예시: **50GB ~ 100GB**

이렇게 분리해야 용량이 부족할 때 **서버를 끄지 않고 디스크만 확장**할 수 있습니다.

### 2-3. iwinv 방화벽(보안 그룹) 설정

| 방향 | 설정 |
|------|------|
| 아웃바운드 | 모두 허용 |
| 인바운드 | 아래 포트만 허용 |

**인바운드 허용 포트**

| 포트 | 용도 | 비고 |
|------|------|------|
| 80 | HTTP | |
| 443 | HTTPS | |
| 22 | SSH | **사무실 IP만 허용** 권장 |
| 8888 | aaPanel | 제어판 선택 시 |
| 8443 | Plesk | 제어판 선택 시 |

---

## Phase 3. 웹호스팅 제어판 설치 및 환경 최적화

서버에 접속해 마우스로 조작할 수 있는 관리자 화면을 올리는 단계입니다.

### 3-1. SSH 접속 및 제어판 설치

1. PuTTY / 터미널로 서버 SSH 접속
2. **Plesk**(유료·권장) 또는 **aaPanel**(무료) 원클릭 설치 명령 실행
3. 약 3~5분 후 브라우저 접속용 제어판 URL + 초기 계정 정보가 출력됨

### 3-2. 마스터 도메인 연결

- 제어판을 IP가 아닌 `https://panel.내에이전시.com` 형태로 접속하도록 DNS 설정
- 제어판에 **Let's Encrypt** 무료 SSL 적용

### 3-3. 웹 스택 패키지 설치

제어판 앱스토어/소프트웨어 메뉴에서 클릭 설치합니다.

**추천 조합** (현재 운영 서버와 호환 기준 → [docs/current-stack.md](./docs/current-stack.md))

```
Apache 2.4.x + MariaDB 10.6+ + PHP 7.4 / 8.x
```

기존이 Apache라 Nginx보다 이전이 수월합니다. OS는 CentOS 7(EOL) 대신 Ubuntu LTS를 씁니다.

---

## Phase 4. 백업 및 네임서버 세팅 (안전장치)

고객사를 받기 **전**, 장애 시 빠르게 복구할 수 있는 백업을 먼저 만듭니다.

### 4-1. 클라우드 외부 3차 백업 연동

1. 제어판(Plesk / aaPanel) 백업 메뉴로 이동
2. Google Drive 또는 AWS S3를 API로 연동
3. 스케줄 예시:
   - **매일 새벽 3시**
   - 모든 계정의 소스코드 + DB 압축
   - 외부 스토리지로 자동 업로드

### 4-2. 자체 네임서버(DNS) 설정

마스터 도메인 관리 페이지에서 호스팅용 네임서버 호스트를 등록합니다.

```
ns1.내에이전시.com  →  스마일서브 서버 IP
ns2.내에이전시.com  →  스마일서브 서버 IP
```

이후 고객사에게는 도메인 네임서버를 위 주소로 변경하라고 안내하면 됩니다.

---

## Phase 5. 고객사 홈페이지 입주 및 관리

프로젝트가 끝난 뒤 고객 사이트를 서버에 올리는 실무 루틴입니다.

### 5-1. 고객사 독립 계정 생성

1. 제어판에서 **Add Site** / **Create Subscription**
2. 고객사 도메인(`client.com`) 입력
3. 디스크 용량 할당 (예: 5GB) → 독립 공간이 즉시 생성됨

### 5-2. 소스 업로드 및 DB 이전

- 파일 관리자 또는 FTP로 홈페이지 소스 업로드
- DB 임포트

### 5-3. SSL 적용

- 제어판 Let's Encrypt 메뉴로 고객 도메인에 무료 SSL 발급
- 이후 자동 갱신

### 5-4. 모니터링 및 정기 청구

- 대시보드에서 고객사별 CPU/메모리 사용량 점검
- 계약 주기에 맞춰 유지보수·호스팅 비용 청구

---

## 체크리스트 요약

- [ ] Phase 1: 마스터 도메인 구매
- [ ] Phase 2: iwinv 서버 + 블록스토리지 + 방화벽
- [ ] Phase 3: 제어판 설치 · 도메인/SSL · 웹 스택
- [ ] Phase 4: 외부 백업 · 자체 네임서버
- [ ] Phase 5: 고객 입주 · SSL · 모니터링/청구

## 보고 자료

| 문서 | 내용 |
|------|------|
| **[호스팅 방식 비교 보고](./docs/report-hosting-comparison.md)** | 카페24 리셀러 vs iwinv 장단점·구조·비용·권고 (보고용) |

## Phase별 실습 노트

| Phase | 문서 |
|-------|------|
| 비용·왜 필요한가 | [docs/costs.md](./docs/costs.md) |
| 카페24 리셀러 vs iwinv | [docs/cafe24-reseller-vs-iwinv.md](./docs/cafe24-reseller-vs-iwinv.md) |
| 현재 스택 | [docs/current-stack.md](./docs/current-stack.md) |
| 1. 도메인 | [docs/phase-1-domain.md](./docs/phase-1-domain.md) |
| 2. iwinv | [docs/phase-2-iwinv.md](./docs/phase-2-iwinv.md) |
| 3. 제어판 | [docs/phase-3-panel.md](./docs/phase-3-panel.md) |
| 4. 백업·DNS | [docs/phase-4-backup-dns.md](./docs/phase-4-backup-dns.md) |
| 5. 고객 입주 | [docs/phase-5-onboarding.md](./docs/phase-5-onboarding.md) |

---

## 참고 링크

- [iwinv 클라우드](https://www.iwinv.kr/)
- [Plesk](https://www.plesk.com/)
- [aaPanel](https://www.aapanel.com/)
