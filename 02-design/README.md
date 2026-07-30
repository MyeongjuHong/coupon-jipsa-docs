# 설계 단계

## 📌 단계 소개

기획을 바탕으로 **"어떻게 만들지"** 를 기술적으로 설계하는 단계입니다.

시스템 아키텍처, 데이터베이스 설계, API 명세, UI/UX 설계 등을 
상세히 정의하여 개발팀이 바로 코딩할 수 있도록 준비합니다.

---

## 📋 산출물 목록

| 문서 | 파일명 | 설명 |
| --- | --- | --- |
| 시스템 아키텍처 | [architecture.md](./architecture.md) | 전체 시스템 구조 및 기술 스택 |
| DB 설계 | [database-schema.md](./database-schema.md) | ERD, 테이블 정의, 인덱스 |
| API 명세 | [api-design.md](./api-design.md) | REST API 엔드포인트 및 요청/응답 스펙 |
| UI 설계 | [ui-design.md](./ui-design.md) | 화면정의서, 와이어프레임, 컴포넌트 |

---

## 🏗️ 아키텍처 방향

### 기술 스택 (확정)
- **프론트엔드**: Next.js + React + TypeScript
- **백엔드**: NestJS + Node.js
- **데이터베이스**: PostgreSQL (Supabase)
- **인증**: 카카오 OAuth
- **알림**: SendGrid (이메일)
- **모니터링**: Sentry
- **배포**: Vercel (프론트) / Render (백엔드)

### 아키텍처 패턴
- **레이어드 아키텍처**: 프레젠테이션 → 비즈니스 로직 → 데이터 접근
- **REST API**: 클라이언트-서버 통신
- **마이크로서비스**: v0.2 이후 검토

---

## 🔐 보안 및 비기능 요구사항

자세한 내용은 [기획 단계 NFR](../01-planning/nfr.md) 참고

### 주요 보안 고려사항
- 카카오 OAuth로 인증
- 기프티콘 코드는 AES-256 암호화
- 스페이스별 멤버 기반 권한 제어
- HTTPS/TLS 암호화 전송

---

## ✅ 체크리스트

- [ ] 시스템 아키텍처 완성
- [ ] DB ERD 작성
- [ ] API 명세 확정
- [ ] UI/UX 디자인 완성
- [ ] 다음 단계: 개발 시작

---

## 📚 참고 자료

- [기획 단계](../01-planning/) - 요구사항 분석 결과
- [프로젝트 개요](../project-overview.md) - 전체 프로젝트 정보

---
