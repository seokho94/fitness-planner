# PC 웹 → Android 대응 기술 스택 및 라이브러리 조사

> **기준 시점: 2026-10-01 (문서 작성일)**
> - 이 문서는 웹 검색 없이 작성자의 사전 지식만으로 정리했다. 실제 최신 정보와 차이가 있을 수 있다.
> - 라이브러리 버전과 유지보수 상태는 모두 **확인 필요**로 표시했다. 도입 직전에 npm / pub.dev / GitHub 저장소에서 최신 릴리스, 최근 커밋 시점, 이슈 대응 현황을 다시 확인한다.
> - 관련 작업: FP-9 (상위: FP-6 헬스 플래너 앱 사전조사)

---

## 1. 조사 목적과 전제

| 항목 | 내용 |
| --- | --- |
| 제품 | 개인용 헬스(운동) 플래너: 루틴 계획, 운동 기록, 진행 상황 차트 |
| 1차 타깃 | PC 웹 브라우저 |
| 2차 타깃 | Android 앱 (같은 코드베이스 최대 재사용) |
| 데이터 | 처음에는 로컬 전용(오프라인 우선). 이후 클라우드 동기화로 확장할 수 있음 |
| 개발 인원 | 1인 |

조사의 핵심 질문은 "웹으로 먼저 만들고, 적은 비용으로 Android 앱까지 낼 수 있는 스택은 무엇인가"이다.

---

## 2. 후보 스택 개요

| ID | 스택 | 한 줄 요약 |
| --- | --- | --- |
| A | **React + Vite + TypeScript (PWA) → Capacitor** | 표준 웹앱을 만들고 Capacitor의 WebView 셸로 Android APK/AAB를 패키징 |
| B | **Flutter (Web + Android)** | Dart 하나로 위젯을 직접 렌더링. Android 품질은 좋지만 Web은 Canvas/Wasm 렌더링 |
| C | **React Native + Expo (Web 지원)** | 네이티브 UI 컴포넌트 기반. 웹은 react-native-web으로 변환 |
| D | **Kotlin Multiplatform / Compose Multiplatform** (참고용) | Kotlin으로 Android 네이티브 + Web(Wasm). Web 타깃 성숙도가 낮음 |

---

## 3. 후보 스택 비교표

평가: ◎ 매우 좋음 / ○ 좋음 / △ 보통 / × 부족

| 비교 기준 | A. React+Vite PWA → Capacitor | B. Flutter | C. React Native + Expo | D. KMP / Compose MP (참고) |
| --- | --- | --- | --- | --- |
| **웹 개발 편의성** | ◎ 표준 DOM/CSS, 브라우저 DevTools, HMR 그대로 사용 | △ Web은 Canvas 렌더링이라 SEO·텍스트 선택·초기 로딩(번들 크기)이 불리 | ○ react-native-web으로 동작하지만 CSS 대신 StyleSheet, 웹 전용 라이브러리 일부 사용 불가 | × Compose for Web(Wasm)은 실험/베타 단계 (확인 필요) |
| **Android 이식 비용** | ○ Capacitor 추가 + 플러그인 연결. UI는 반응형 대응만 하면 됨 | ◎ 같은 코드가 네이티브 수준으로 동작 | ◎ 네이티브 컴포넌트로 동작. 웹보다 모바일이 주력 | ◎ Android는 1급 지원 |
| **오프라인 로컬 DB** | ○ 웹: IndexedDB(Dexie) / Android: 같은 IndexedDB 또는 SQLite 플러그인 | ◎ Drift(SQLite), Isar, Hive. 단 Web에서는 sql.js/IndexedDB 기반으로 제약 있음 | ○ expo-sqlite (Web 지원 수준 확인 필요), WatermelonDB | ○ SQLDelight, Room(KMP 지원 확인 필요). Web 지원 제한 |
| **차트·UI 생태계** | ◎ npm 생태계 전체 (Recharts, Chart.js, ECharts, shadcn/ui, MUI 등) | ○ fl_chart, syncfusion_flutter_charts, Material 3 기본 내장 | △ 차트는 victory-native, react-native-gifted-charts 등. 웹/네이티브 동시 지원 라이브러리가 적음 | △ 라이브러리 수가 적음 |
| **학습 곡선** | ◎ 웹 표준 지식만으로 시작 (React/TS 경험 있으면 거의 0) | △ Dart + 위젯 트리 + 상태관리 패턴 학습 필요 | ○ React 경험이 있으면 무난. 네이티브 빌드/Expo 개념은 추가 학습 | × Kotlin, Gradle, Compose 학습 필요 |
| **1인 개발 생산성** | ◎ 웹에서 대부분 개발·테스트, Android는 마지막 단계에서 확인 | ○ 한 언어로 끝나지만 Web 품질 이슈 대응에 시간 소요 | ○ Expo가 빌드를 많이 대신해 주지만 웹/네이티브 차이 디버깅 필요 | △ Web 타깃 리스크 |
| **빌드·배포 방식** | 웹: `vite build` → 정적 호스팅(Vercel/Netlify/GitHub Pages) / Android: `npx cap sync` → Android Studio 또는 Gradle로 AAB 빌드 | 웹: `flutter build web` / Android: `flutter build appbundle` | 웹: `npx expo export -p web` / Android: EAS Build(클라우드) 또는 로컬 prebuild | Gradle 기반 각 타깃 빌드 |
| **웹 → Android 코드 재사용률(추정)** | 90% 이상 (네이티브 기능 부분만 분기) | 95% 이상 | 80~90% (웹 전용 분기 발생) | Android 위주, Web은 불확실 |
| **주요 리스크** | WebView 성능(복잡한 애니메이션), 네이티브 느낌 부족 | Web 결과물 품질, Dart 생태계 의존 | 웹 지원이 부차적, 라이브러리 호환성 | Web 타깃 미성숙 |

### 비교 요약

- **PC 웹이 1차 타깃**이라는 조건에서 웹 품질은 A > C > B > D 순이다.
- **Android 품질**만 보면 B, C, D가 A보다 낫지만, 헬스 플래너는 폼 입력·리스트·차트 위주라 WebView 성능으로도 충분하다.
- **1인 개발**에서는 개발 대부분을 브라우저에서 끝낼 수 있는 A가 가장 효율적이다.

---

## 4. 카테고리별 라이브러리 후보

> 모든 라이브러리의 버전과 유지보수 상태는 **확인 필요**. 아래 "상태" 열은 작성자 지식 기준의 인상이다.

### 4.1 로컬 저장소

| 후보 | 대상 스택 | 장점 | 단점 | 상태 |
| --- | --- | --- | --- | --- |
| **Dexie.js** (IndexedDB 래퍼) | A, (C-web) | API가 단순하고 TS 타입 지원, `liveQuery`로 반응형 조회, 브라우저와 Capacitor WebView 모두 동작. Dexie Cloud로 동기화 확장 가능 | 관계형 쿼리·집계가 약함(통계는 JS로 계산). Android WebView 저장소는 OS가 정리할 가능성이 있어 `navigator.storage.persist()` 필요 | 활발 (확인 필요) |
| **idb** (Jake Archibald) | A | 아주 가벼운 Promise 래퍼 | 스키마 마이그레이션·조회 편의 기능이 없음 | 유지 (확인 필요) |
| **sql.js / wa-sqlite** | A | 브라우저에서 SQLite 사용, SQL 집계 가능. wa-sqlite는 OPFS/IndexedDB VFS로 영속화 | Wasm 번들 크기(~1MB 내외), 영속화 직접 구성 필요, 설정 난이도 높음 | sql.js 유지 / wa-sqlite 활발 (확인 필요) |
| **SQLite 공식 Wasm + OPFS** | A | 공식 빌드, OPFS로 성능 좋음 | OPFS 동기 API는 Worker 필요, 브라우저별 지원 차이 | 활발 (확인 필요) |
| **@capacitor-community/sqlite** | A(Android) | Android에서 네이티브 SQLite 사용, 데이터 안정성 높음. Web은 jeep-sqlite(sql.js 기반)로 대체 | 웹/네이티브 구현이 달라 테스트 범위 증가 | 커뮤니티 유지 (확인 필요) |
| **Drift** | B | 타입 안전 SQL, 마이그레이션 지원, Web(sql.js/wasm) 지원 | 코드 생성(build_runner) 필요 | 활발 (확인 필요) |
| **Isar** | B | 빠른 NoSQL, 쿼리 API 편리 | 원 개발자 유지보수 중단 이슈가 있었음, 커뮤니티 포크 존재 | **확인 필요 (리스크)** |
| **expo-sqlite / WatermelonDB** | C | Expo 기본 지원 / 대용량·동기화 설계 | Web 지원 수준 제한 (확인 필요) | 확인 필요 |

**권장(스택 A 기준)**: Dexie.js를 기본으로 사용하고, 저장소 접근은 Repository 인터페이스 뒤로 숨긴다. Android에서 데이터 유실 문제가 확인되면 `@capacitor-community/sqlite` 구현체로 교체할 수 있게 한다.

### 4.2 상태 관리

| 후보 | 장점 | 단점 | 상태 |
| --- | --- | --- | --- |
| **Zustand** | 보일러플레이트가 거의 없음, 작은 번들, persist 미들웨어 | 대규모 구조화 규칙은 직접 정해야 함 | 활발 (확인 필요) |
| **Redux Toolkit** | 표준화된 패턴, DevTools 강력, RTK Query 포함 | 소규모 앱에는 코드량이 많음 | 활발 (확인 필요) |
| **Jotai** | 원자 단위 상태, 파생 상태 표현이 쉬움 | 상태가 흩어지기 쉬움 | 활발 (확인 필요) |
| **TanStack Query** | 비동기(서버/DB) 데이터 캐싱, 동기화 단계에서 유용 | 클라이언트 UI 상태 관리용은 아님 | 활발 (확인 필요) |
| (B) Riverpod / Bloc | Flutter 표준급 | Flutter 전용 | 확인 필요 |

**권장**: UI 상태는 Zustand, DB 데이터는 Dexie `useLiveQuery`로 직접 구독한다. 백엔드 동기화가 붙으면 TanStack Query를 추가로 검토한다.

### 4.3 라우팅

| 후보 | 장점 | 단점 | 상태 |
| --- | --- | --- | --- |
| **React Router** | 사실상 표준, 자료가 많음 | 메이저 버전마다 API 변경이 컸음 | 활발 (확인 필요) |
| **TanStack Router** | 경로·검색 파라미터 타입 안전 | 상대적으로 신생, 학습 필요 | 활발 (확인 필요) |
| (B) go_router / (C) Expo Router | 각 스택 표준 | 스택 전용 | 확인 필요 |

**권장**: React Router. Capacitor 환경에서는 `HashRouter`가 아니어도 동작하지만, 딥링크 설정 시 경로 처리를 함께 확인한다.

### 4.4 UI 컴포넌트

| 후보 | 장점 | 단점 | 상태 |
| --- | --- | --- | --- |
| **shadcn/ui (Radix + Tailwind CSS)** | 코드를 프로젝트에 복사해 완전히 수정 가능, 접근성 좋음, 번들 최소 | 컴포넌트를 직접 관리해야 함, Tailwind 학습 필요 | 활발 (확인 필요) |
| **MUI (Material UI)** | 컴포넌트가 풍부, Android Material 느낌과 잘 맞음 | 번들 크기 큼, 커스터마이징 시 스타일 시스템 학습 필요 | 활발 (확인 필요) |
| **Mantine** | 컴포넌트·훅 풍부(날짜 선택기, 폼 포함), 문서 좋음 | 디자인 개성이 강함 | 활발 (확인 필요) |
| **Ionic Framework (React)** | Capacitor와 같은 팀, 모바일 네이티브 느낌(전환 애니메이션, 탭) | PC 웹에서는 모바일 느낌이 어색할 수 있음 | 유지 (확인 필요) |
| (B) Material 3 기본 위젯 / (C) Tamagui, React Native Paper | 스택 전용 | 스택 전용 | 확인 필요 |

**권장**: shadcn/ui + Tailwind CSS. PC와 모바일 반응형 레이아웃을 같은 코드로 만들기 쉽다.

### 4.5 차트

| 후보 | 대상 | 장점 | 단점 | 상태 |
| --- | --- | --- | --- | --- |
| **Recharts** | A | React 컴포넌트 방식, 선/막대/영역 차트 구현이 쉬움 | SVG 기반이라 데이터가 매우 많으면 느림, 터치 상호작용 제한적 | 활발 (확인 필요) |
| **Chart.js (+ react-chartjs-2)** | A | Canvas 기반으로 가벼움, 플러그인 많음 | React 선언형과는 다소 어긋남 | 활발 (확인 필요) |
| **Apache ECharts (echarts-for-react)** | A | 기능이 가장 풍부, 대용량·모바일 터치 대응 좋음 | 번들 큼(트리셰이킹 필요), 설정이 장황 | 활발 (확인 필요) |
| **visx / Nivo** | A | visx: 저수준 자유도 / Nivo: 예쁜 기본값 | visx 학습 비용, Nivo 번들 | 확인 필요 |
| **fl_chart** | B | Flutter 대표 차트, 애니메이션 좋음 | Flutter 전용 | 활발 (확인 필요) |
| **victory-native / react-native-gifted-charts** | C | RN 네이티브 차트 | 웹 동시 지원은 제한적 | 확인 필요 |

**권장**: Recharts로 시작(운동 볼륨·1RM 추이 정도의 데이터량이면 충분). 성능 문제가 생기면 ECharts로 교체한다.

### 4.6 폼·검증

| 후보 | 장점 | 단점 | 상태 |
| --- | --- | --- | --- |
| **React Hook Form** | 비제어 입력으로 리렌더 최소, 대중적 | 동적 필드(세트 추가/삭제)는 `useFieldArray` 이해 필요 | 활발 (확인 필요) |
| **TanStack Form** | 타입 안전 | 상대적으로 신생 | 확인 필요 |
| **Zod** | 스키마로 TS 타입 추론, DB 저장 데이터·가져오기(import) 검증에도 재사용 | 번들이 다소 큼 | 활발 (확인 필요) |
| **Valibot** | Zod와 유사, 더 작은 번들 | 생태계가 작음 | 확인 필요 |

**권장**: React Hook Form + Zod (`@hookform/resolvers`).

### 4.7 날짜 처리

| 후보 | 장점 | 단점 | 상태 |
| --- | --- | --- | --- |
| **date-fns** | 함수 단위 import, 트리셰이킹 좋음, 한국어 locale | 시간대 처리는 별도 패키지 필요 | 활발 (확인 필요) |
| **Day.js** | 2KB 수준, Moment와 비슷한 API | 플러그인 방식이라 기능별 설정 필요 | 유지 (확인 필요) |
| **Luxon** | 시간대·Intl 처리 강함 | 상대적으로 큼 | 유지 (확인 필요) |
| **Temporal API** | 표준 API | 브라우저/WebView 지원 상황 **확인 필요**, 폴리필 필요 가능성 | 확인 필요 |

**권장**: date-fns. 저장은 ISO 문자열(UTC) 또는 로컬 날짜 문자열(`YYYY-MM-DD`)로 규칙을 정한다.

### 4.8 테스트 도구

| 후보 | 용도 | 장점 | 단점 | 상태 |
| --- | --- | --- | --- | --- |
| **Vitest** | 단위/통합 | Vite 설정 공유, 빠름, Jest 호환 API | — | 활발 (확인 필요) |
| **Jest** | 단위/통합 | 가장 대중적 | Vite/ESM 프로젝트에서 설정이 번거로움 | 활발 (확인 필요) |
| **React Testing Library** | 컴포넌트 | 사용자 관점 테스트 | — | 활발 (확인 필요) |
| **fake-indexeddb** | DB 테스트 | Node에서 Dexie/IndexedDB 테스트 가능 | 실제 브라우저와 미세 차이 | 유지 (확인 필요) |
| **Playwright** | E2E | 크로스 브라우저, 모바일 뷰포트 에뮬레이션 | 실제 Android WebView 테스트는 별도 | 활발 (확인 필요) |
| **Cypress** | E2E | 디버깅 UI 좋음 | 멀티 탭/브라우저 제약 | 활발 (확인 필요) |
| **MSW** | API 모킹 | 동기화 단계에서 백엔드 모킹 | 로컬 전용 단계에선 불필요 | 활발 (확인 필요) |
| (B) flutter_test, integration_test / (C) Jest + Detox, Maestro | 스택 전용 | — | — | 확인 필요 |

**권장**: Vitest + React Testing Library + fake-indexeddb, E2E는 Playwright. Android는 실기기/에뮬레이터에서 수동 스모크 테스트로 시작한다.

### 4.9 운동 데이터셋 (공개 운동 DB)

> 라이선스 조건은 데이터셋마다 다르고 변경될 수 있으므로 **모두 확인 필요**. 상업적 이용 여부, 이미지 포함 여부를 따로 확인한다.

| 데이터셋 | 내용 | 라이선스(작성자 기억 기준) | 장점 | 단점 |
| --- | --- | --- | --- | --- |
| **free-exercise-db** (GitHub: yuhonas/free-exercise-db) | 약 800개 운동, JSON, 근육군·장비·설명·이미지 | Unlicense(퍼블릭 도메인) — **확인 필요** | 앱에 JSON을 번들해 오프라인 사용 가능, 제약 적음 | 영어만 제공, 한국어 번역 필요 |
| **wger** (wger.de, 오픈소스 피트니스 앱) | 운동·근육·장비 DB, REST API, 다국어 | 코드 AGPL-3.0, 운동 데이터 CC-BY-SA 계열 — **확인 필요** | 다국어, API 제공 | CC-BY-SA는 출처 표시·동일조건 변경허락 의무, 이미지별 라이선스 상이 |
| **ExerciseDB** (RapidAPI 등) | 1,000개 이상 운동, GIF | 상용 API 약관 — **확인 필요** | GIF 시각 자료 풍부 | 유료/요청 제한, 오프라인 번들 재배포 불가 가능성 |
| **exercemus/exercises** 등 기타 GitHub JSON | 운동 목록 JSON | MIT 등 저장소별 상이 — **확인 필요** | 가벼움 | 유지보수·품질 불확실 |

**권장**: free-exercise-db JSON을 초기 시드 데이터로 번들하고, 한국어 이름은 자체 매핑 테이블로 추가한다. 사용자 정의 운동 추가 기능을 함께 제공한다. 라이선스 원문과 출처는 앱 내 "오픈소스 라이선스" 화면에 표시한다.

---

## 5. 추천 스택 기준 PC 웹 → Android 이전 경로

추천 스택(A: React + Vite + TS + PWA → Capacitor) 기준 단계별 절차.

### 1단계: 웹 우선 개발 (Android를 고려한 설계)

1. Vite React-TS 템플릿으로 프로젝트 생성.
2. **반응형 레이아웃**: 모바일 폭(360px)부터 설계하고 PC는 넓은 레이아웃으로 확장 (Tailwind breakpoint 활용).
3. **플랫폼 의존 코드 격리**: 저장소·파일 내보내기·알림·진동 등은 `src/platform/` 아래 인터페이스로 정의하고 웹 구현만 먼저 작성.
4. **데이터 계층 추상화**: `WorkoutRepository` 같은 인터페이스 뒤에 Dexie 구현을 둔다. 모든 레코드에 `id(UUID)`, `updatedAt`, `deletedAt`(소프트 삭제) 필드를 넣어 이후 동기화에 대비.
5. 터치 대상 크기(최소 44~48px), 호버 의존 UI 지양.

### 2단계: PWA 적용

1. `vite-plugin-pwa`(버전 확인 필요)로 Web App Manifest와 Service Worker 생성.
2. 정적 자원 precache로 오프라인 실행 보장.
3. `navigator.storage.persist()` 요청으로 IndexedDB 영구 저장 요청.
4. 이 단계에서 Android Chrome의 "홈 화면에 추가"로 앱처럼 사용 가능 → 스토어 출시 전 실사용 검증.

### 3단계: Capacitor 도입

1. `@capacitor/core`, `@capacitor/cli`, `@capacitor/android` 설치 (버전 확인 필요).
2. `npx cap init` → `capacitor.config.ts`에 `appId`(예: `com.example.fitnessplanner`), `webDir: 'dist'` 설정.
3. `npm run build` → `npx cap add android` → `npx cap sync android`.
4. Android Studio에서 에뮬레이터/실기기 실행. 개발 중에는 `server.url`로 Vite dev server를 연결해 라이브 리로드.
5. Capacitor 환경에서는 Service Worker가 필요 없거나 충돌할 수 있으므로, `Capacitor.isNativePlatform()`으로 SW 등록을 분기.

### 4단계: 네이티브 기능 연결

1. 1단계의 `src/platform/` 인터페이스에 Capacitor 구현 추가:
   - 뒤로가기 버튼: `@capacitor/app` (`backButton` 이벤트로 라우터 뒤로가기)
   - 상태바/스플래시: `@capacitor/status-bar`, `@capacitor/splash-screen`
   - 휴식 타이머 알림: `@capacitor/local-notifications`
   - 진동: `@capacitor/haptics`
   - 백업 파일 내보내기/공유: `@capacitor/filesystem`, `@capacitor/share`
   - 화면 꺼짐 방지(운동 중): 커뮤니티 keep-awake 플러그인 (확인 필요)
2. 필요 시 저장소를 `@capacitor-community/sqlite`로 교체(Repository 구현체만 교체).
3. 안전 영역(`env(safe-area-inset-*)`), 소프트 키보드에 가려지는 입력 필드 처리.

### 5단계: 빌드·배포

1. 앱 아이콘·스플래시 생성: `@capacitor/assets` (확인 필요).
2. 서명 키(keystore) 생성 및 안전한 보관.
3. Android Studio 또는 `./gradlew bundleRelease`로 AAB 생성.
4. Google Play Console 내부 테스트 트랙 → 비공개 테스트 → 프로덕션 순으로 출시 (신규 개인 개발자 계정의 테스트 요건은 확인 필요).
5. 웹 버전은 같은 빌드 결과물을 정적 호스팅에 배포.
6. (선택) 웹 자산 OTA 업데이트: Capgo, Appflow 등 — 스토어 정책 준수 여부 확인 필요.

### 6단계: 운영

- 웹과 Android 버전 동시 릴리스를 위해 `package.json` 버전과 Android `versionCode/versionName`을 함께 관리.
- 데이터 백업/복원(JSON 내보내기·가져오기)을 동기화 기능 전까지의 안전장치로 제공.

---

## 6. 클라우드 동기화 확장 시 백엔드 후보

### 6.1 백엔드 후보 비교

| 기준 | Supabase | Firebase (Firestore) | 자체 Spring Boot 서버 |
| --- | --- | --- | --- |
| 데이터 모델 | PostgreSQL (관계형, SQL) | 문서형 NoSQL | 자유 (보통 PostgreSQL/MySQL) |
| 인증 | Supabase Auth (이메일, OAuth) | Firebase Auth (Google 로그인 연동 쉬움) | Spring Security 직접 구현 |
| 오프라인 지원 | 클라이언트 오프라인 캐시 기본 제공 안 함 → 직접 동기화 또는 PowerSync/RxDB 등 연동 (확인 필요) | Firestore SDK 오프라인 지속성 내장 (웹은 IndexedDB 사용) | 직접 구현 |
| 실시간 | Realtime(Postgres 변경 구독) | 실시간 리스너 내장 | WebSocket 직접 구현 |
| 권한 제어 | Row Level Security(RLS) | Security Rules | 코드로 구현 |
| 비용 | 무료 티어 있음, 비활성 프로젝트 일시정지 정책 확인 필요 | 무료(Spark) 티어, 읽기/쓰기 횟수 과금 | 서버 호스팅 비용 + 운영 시간 |
| 1인 개발 생산성 | ◎ | ◎ | △ |
| 종속(lock-in) | 낮음(오픈소스, 셀프호스팅 가능) | 높음 | 없음 |
| 통계/집계 쿼리 | ◎ SQL로 쉬움 | △ 집계 제한적 | ◎ |

### 6.2 오프라인 우선(Offline-first) 동기화 패턴

1. **로컬 DB가 진실의 원천(Source of truth)**: UI는 항상 로컬 DB만 읽고 쓴다. 네트워크는 백그라운드 동기화에만 사용.
2. **아웃박스(Outbox) 패턴**: 로컬 변경 시 `pending_changes` 테이블에 작업을 기록 → 온라인일 때 순서대로 서버에 전송 → 성공 시 삭제.
3. **증분 풀(Pull) 동기화**: 마지막 동기화 시점(`lastSyncedAt` 또는 서버 시퀀스 번호) 이후 변경분만 받아 로컬에 병합.
4. **충돌 해결 전략**
   - **LWW(Last-Write-Wins)**: `updatedAt` 비교. 단순하고 개인용 앱(사용자 1명, 기기 2~3대)에는 대부분 충분. 기기 시계 차이를 줄이려면 서버 시각 또는 HLC(Hybrid Logical Clock) 사용.
   - **필드 단위 병합**: 레코드 전체가 아니라 변경된 필드만 덮어씀.
   - **CRDT**(Yjs, Automerge): 동시 편집이 많을 때. 이 앱에는 과함.
5. **식별자/삭제 규칙**: 클라이언트 생성 UUID(v7 권장, 정렬 가능) 사용, 삭제는 `deletedAt` 톰스톤으로 표시 후 일정 기간 뒤 정리.
6. **도구 선택지**
   - Dexie Cloud: Dexie 사용 시 가장 적은 코드로 동기화 (유료 요금제, 확인 필요)
   - RxDB: Supabase/Firestore/커스텀 HTTP 복제 플러그인 (일부 플러그인 유료, 확인 필요)
   - PowerSync / ElectricSQL: Postgres(Supabase) ↔ 클라이언트 SQLite 동기화 (확인 필요)
   - 직접 구현: 위 1~5 패턴으로 REST 엔드포인트 2개(`/sync/push`, `/sync/pull`)

**권장**: 동기화가 필요해지는 시점에 **Supabase + 직접 구현한 Outbox/증분 Pull + LWW**로 시작한다. 1단계부터 UUID·`updatedAt`·`deletedAt` 필드를 넣어 두면 마이그레이션 비용이 거의 없다. 자체 Spring Boot 서버는 다중 사용자 기능(코치-회원 공유 등)이나 복잡한 비즈니스 로직이 필요할 때 검토한다.

---

## 7. 결론

### 7.1 추천 스택 (1안)

| 영역 | 선택 |
| --- | --- |
| 기반 | React + Vite + TypeScript |
| 배포 형태 | PWA(웹) + Capacitor(Android) |
| 로컬 저장소 | Dexie.js (필요 시 @capacitor-community/sqlite로 교체 가능하도록 Repository 추상화) |
| 상태 관리 | Zustand + Dexie `useLiveQuery` |
| 라우팅 | React Router |
| UI | shadcn/ui + Tailwind CSS |
| 차트 | Recharts (대안 ECharts) |
| 폼·검증 | React Hook Form + Zod |
| 날짜 | date-fns |
| 테스트 | Vitest + React Testing Library + fake-indexeddb, Playwright |
| 운동 데이터 | free-exercise-db 시드 + 한국어 매핑 (라이선스 확인 필요) |
| 동기화(확장) | Supabase + Outbox/증분 Pull + LWW |

**선택 근거**
1. **1차 타깃이 PC 웹**: 표준 DOM 기반이라 웹 품질(로딩 속도, 텍스트 선택, 접근성, 반응형)이 가장 좋다.
2. **Android 이식 비용이 낮음**: 같은 빌드 결과물을 Capacitor로 감싸기만 하면 되고, 코드 재사용률이 90% 이상이다.
3. **1인 개발 생산성**: 개발·디버깅 대부분을 브라우저 DevTools와 HMR로 처리. 별도 언어(Dart/Kotlin) 학습이 필요 없다.
4. **생태계**: 차트·UI·폼·테스트 모두 npm에서 성숙한 선택지가 2개 이상 있어 교체가 쉽다.
5. **앱 특성 적합**: 헬스 플래너는 폼·리스트·차트 중심이라 WebView 성능 한계에 걸릴 가능성이 낮다.
6. **단계적 출시**: PWA만으로도 Android에서 먼저 사용해 볼 수 있어 스토어 출시 전 검증이 가능하다.

### 7.2 대안 (2안): Flutter (Web + Android)

| 영역 | 선택 |
| --- | --- |
| 기반 | Flutter (Dart) |
| 로컬 저장소 | Drift (Web: wasm/sql.js) |
| 상태 관리 | Riverpod |
| 라우팅 | go_router |
| UI | Material 3 기본 위젯 |
| 차트 | fl_chart |
| 테스트 | flutter_test, integration_test |
| 동기화(확장) | Supabase(supabase_flutter) 또는 Firebase |

**대안으로 둔 근거**
- Android 앱 품질(애니메이션, 네이티브 느낌)이 중요해지거나, 우선순위가 **모바일 > 웹**으로 바뀌면 가장 유리하다.
- 하나의 언어·위젯 체계로 Web/Android/iOS까지 확장할 수 있다.
- Drift + fl_chart 조합으로 로컬 DB와 차트 요구사항을 충족한다.

**1안 대비 채택하지 않은 이유**: PC 웹 품질(초기 로딩, Canvas 렌더링의 텍스트/접근성 이슈)과 Dart 학습 비용이 웹 우선 조건에 불리하다.

### 7.3 제외 사유 요약

- **React Native + Expo**: React 지식은 재사용되지만 웹 지원이 부차적이고, 웹/네이티브 동시 지원 차트·UI 라이브러리가 적다. 모바일이 1차 타깃이 되면 재검토.
- **Kotlin/Compose Multiplatform**: Web 타깃 성숙도가 낮아 PC 웹 우선 조건에 맞지 않음. 참고용으로만 유지.

### 7.4 재검토 조건

- Android WebView에서 차트·리스트 성능 문제가 실제로 측정되는 경우 → 2안(Flutter) 또는 성능 구간만 네이티브 플러그인으로 분리.
- iOS 출시가 결정되는 경우 → Capacitor iOS 추가로 1안 유지 가능 (Apple 심사 기준 확인 필요).
- 도입 직전 각 라이브러리의 최신 버전·유지보수 상태 재확인 (본 문서 기준 시점 이후 변경 가능).
