# Phase 2. iwinv 인프라 구축

## 목표

스마일서브 클라우드(iwinv)에 서버·디스크·방화벽을 준비한다.

## 2-1. 인스턴스 생성

1. [iwinv 콘솔](https://www.iwinv.kr/) 가입·로그인
2. 클라우드 서버 생성
   - OS: Ubuntu Server 22.04 LTS 또는 24.04 LTS
   - 사양: vCPU 2 / RAM 4GB 또는 8GB (고객 10개 내외 기준)

### 기록

| 항목 | 값 |
|------|-----|
| 서버 IP | |
| OS | |
| 스펙 | |
| root/SSH 키 위치 | |

## 2-2. 블록 스토리지 추가·마운트

OS 디스크와 분리해 웹/DB용 SSD를 붙인다. (예: 50~100GB)

SSH 접속 후 예시 (디스크 장치명은 콘솔/환경에 맞게 확인):

```bash
# 디스크 확인
lsblk

# 파티션·파일시스템 (최초 1회, 장치명 확인 후)
sudo mkfs.ext4 /dev/vdb

# 마운트 포인트
sudo mkdir -p /data
sudo mount /dev/vdb /data

# 재부팅 후에도 유지 (UUID 확인 후 fstab)
sudo blkid /dev/vdb
# /etc/fstab 에 UUID=.... /data ext4 defaults 0 2 추가
```

> 용량 부족 시 서버 전원 없이 디스크만 확장하는 것이 목적.

## 2-3. 방화벽(보안 그룹)

| 방향 | 규칙 |
|------|------|
| 아웃바운드 | 전체 허용 |
| 인바운드 | 아래만 |

| 포트 | 용도 | 비고 |
|------|------|------|
| 80 | HTTP | |
| 443 | HTTPS | |
| 22 | SSH | 사무실 IP만 허용 권장 |
| 8888 | aaPanel | 선택 시 |
| 8443 | Plesk | 선택 시 |

## 다음 단계

인프라 준비 후 → [Phase 3](./phase-3-panel.md)
