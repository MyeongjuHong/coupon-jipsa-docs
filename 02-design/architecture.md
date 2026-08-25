# 시스템 아키텍처

## 1. 전체 아키텍처 개요

### 시스템 다이어그램

(텍스트로 아스키 다이어그램 또는 이미지 링크)

### 설명

- **클라이언트 (프레젠테이션 계층)**: Next.js, React, TypeScript, Tanstack Query, Zustand, Zod, Tailwind CSS, Cloudflare Pages
- **백엔드 (비즈니스 로직 계층)**: NestJS, TypeScript, TSX, Helmet, CORS, JWT, Argon2, Dayjs, Nanoid, Render, fly.io
- **데이터베이스 (데이터 접근 계층)**: Prisma, PostgreSQL, Supabase
- **외부 서비스**: Kakao OAuth, SendGrid(이메일), Sentry(모니터링)

> NginX, Redis, Docker, WebSocket, RabbitMQ/Kafka 등은 v0.1 스코프에서 제외. 이유는 각 항목 하단 참고
> 

---

## 2. 각 계층별 기술 스택 (quot. 모든 글은 formal하게 다듬어줄 것)

### 2.1 프레젠테이션 계층 (클라이언트)

| 기술 | 선택 이유 | 트레이드오프  | 비고 |
| --- | --- | --- | --- |
| **Next.js** | SSR로 초기 로딩 개선, 파일 기반 라우팅 + API Route 통합으로 개발 속도 향상 | React 단독보다 SSR/ISR 등 개념적 러닝커브 존재, 순수 SPA보다 무거움 | 로그인 후 사용하는 서비스라 SEO 필요성은 낮아 개발 속도 고려시 Vite+React로 축소 가능, 다만 Next.js가 채용시장 평균이라 유지 |
| **React** | Next.js 사용 위해 필수 (컴포넌트 UI 구축) | Vue, Svelte 대비 보일러 플레이트 多 | 채용 시장 평준이라 대체 불필요 |
| **TypeScript** | 정적 타입으로 실행 전 빌드타임에서 오류 검출. 1인 개발 고려 | 초기 설정 및 타입 정의 비용, 타입 정교히 만들수록 개발 시간 증가 |  |
| **TanStack Query** | 서버 상태(비동기 API 데이터)의 캐싱/재검증/로딩/에러 상태 자동 관리로 개발 속도 ↑ | 없을 시 useEfffect + useState로 캐싱, 로딩, 에러처리 전부 수동 구현 (캐시 무효화 버그 발생 쉬움) | SWR (더 가볍지만 기능 적음) |
| **Zustand** | Redux 대비 적은 보일러 플레이트로 UI 전역 상태 관리 가능 | Redux 대비 미들웨어 생태계 얕음 | 복잡도 증가시 Redux 검토 |
| **Zod** | 런타임 데이터 검증, TypeScript 타입 자동 추론, RHF과 함께 사용 예정 | Yup 대비 TS 통합이 낫지만 생태계는 작음 |  |
| **Tailwind CSS** | 유틸리티 클래스 기반이라 별도 CSS 파일 관리 불필요, 반응형 문법이 모바일 웹 우선에 적합 | 클래스명 길어지면 JSX 가독성 저하 | 컴포넌트 단위로 재사용성 관리해서 완화 |
| **Cloudflare Pages** | 무료티어에서도 상업적 이용 제한X(Vercel Hobby 플랜은 상업적 사용 금지) | Vercel 대비 Next.js 특화 기능(ISR 등) 일부 제약 | 트래픽 증가시 Vercel Pro 재검토 |
| ~~NginX~~ | (v0.1에서 제외) Cloudflare가 이미 CDN 엣지에서 정적 자산 캐싱 |  | v2.0 트래픽 증가 or 서버 분산 시 재검토 |

### 2.2 백엔드 계층 (비즈니스 로직)

| 기술 | 선택 이유 | 트레이드오프 | 비고 |
| --- | --- | --- | --- |
| **NestJS** | Next.js와 유사한 구조(모듈화)라 프레임워크 전환 경험 비교 | Express보다 초기 개념 러닝 커브 존재, 소규모 프로젝트엔 Express/Fastify가 더 적합 | - |
| **TypeScript** | 프론트와 동일 (위 참고) | “ | “ |
| **TSX** | 컴파일 없이 TS를 node 위에서 즉시 실행해 개발 속도 향상 | ts-node보다 빠르나 신생 도구라 일부 엣지케이스 정보 적음 |  |
| **Helmet** | 보안 헤더(CSP, X-Frame-Options 등)를 한 줄로 세팅, 1인 개발 고려해 직접 관리X | 없을 시 각 헤더를 직접 설정 | - |
| **CORS** | 리버스 프록시 도입 전까진 로컬 개발 및 테스트 서버가 다른 origin이라 필수 | - | 설정 비용 거의 없어 프록시 도입 여부와 무관하게 기본 유지 |
| **JWT** | 서버가 로그인 상태를 별도 저장할 필요X(stateless), Render 재시작해도 로그인 유지 가능 | 세션 방식보다 토큰 강제 만료가 더 번거로움(블랙리스트 별도 구현 필요?) | JWT 위조 공격 우려시 Paesto 검토 |
| **Argon2** | 메모리 사용 방식이라 GPU/ASIC 병렬 브루트포스 공격에 강함, 금융성 아이템(쿠폰) 다루므로 보안 우선순위 최상 | CPU 위주 사용 방식의 bcrypt보다 서버 자원(메모리) 소모 큼 | 무료 서버 자원 부족 시 bcrypt로 폴백 가능 |
| **Dayjs** | 만료일 계산/포맷팅에 필요, 네이티브 Date의 불변성(원본 객체 직접 변경) 문제 회피 | moment.js보다 기능은 적으나 프로젝트에 충분 | 프론트에서도 만료이 표시에 공통 사용 |
| **Nanoid** | Enumeration 방지용 PK 및 초대 링크 생성용, UUID보다 짧아 초대 링크에 넣기 좋음 | UUID보다 충돌 방지 이론적 안정성 낮으나 실사용 문제 없는 수준 | - |
| **Render** (배포) | 무료, 자동 배포 지원 | 콜드스타트 이슈 | cron으로 주기적 핑을 보내 완화 |
| Fly.io | 콜드스타트 없는 무료/저가 티어 제공 | Render보다 설정이 복잡(Dockerfile 필요) | v0.1 출시 후 사용자 확보 시 마이그레이션 검토 |

### 2.3. 테스트 및 로깅 도구 (v0.1 보류, v0.2 도입)

| 기술 | 선택 이유 | 트레이드오프 | 비고 |
| --- | --- | --- | --- |
| Vitest | vite 기반으로 빠르고 ESM/TS 네이티브 지원, Jest보다 설정 간단 | Jest보다 생태계/자료 적음 | v0.2에서 도입 |
| Supertest | 서버 없이 API 호출 테스트 가능 | - | v0.2에서 Vitest와 함께 도입 |
| Playwright | 실제 브라우저로 전체 사용자 흐름(E2E) 테스트 가능 | SuperTest보다 느리고 무거움 | v1.0 이후, 핵심 흐름 E2E 테스트 필요시 |
| Pino | Json 구조화 로그 빠르게 생성, Sentry와 연동 쉬움, 소규모 API 서버라 오버헤드 최소화가 중요 | Winston보다 포매팅 커스터마이징 자유도 낮음 | v0.2 도입 시 채택, 파일 저장이나 복잡한 로그 분기 필요시 Winston 검토 |
| tsx watch | nodemon + ts-node 조합 없이 한 도구로 해결돼 설정 단순 | Node 외 다른 언어/명령어 감시 불가 (TS 전용) | - |
| lighthouse | 성능/접근성/SEO 자동 진단 도구 | - | 배포 후 성능 체크용, v0.1 완성 후 1회 실행 권장 |