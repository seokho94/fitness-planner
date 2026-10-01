# 기본 운동 시드 목록 v1

- 작업: FP-15 (상위: FP-11 헬스 플래너 MVP 기능 상세 명세)
- 작성일: 2026-10-01
- 기준 시점: **2026-10** (이 시점의 국내 헬스장 운동 구성 기준)
- 상태: 초안
- 개정 이력
  - 2026-10-01 (FP-25): I-01 — `BAND` 운동 3개(139~141번) 추가, 1장 요약에 장비별 개수 표와 합계 추가 / I-03 — 6장 기본 운동 `createdAt`·`updatedAt`을 시드 작성 시각 고정값으로 변경 / I-12 — 4.1절에 #0 `SETTINGS_ID` 예약 명시
- 기준 문서
  - [사전조사 종합 요약 및 의사결정](../pre-research-summary.md) ADR-006(자체 큐레이션), R-3(라이선스), Q-4(이미지), C-4(시드 범위)
  - [데이터 모델·도메인 규칙 v1](./data-model-v1.md) 1.4절(열거값), 2.1절(Exercise), 4장(기록 유형)
  - [운동 라이브러리(F-EX) 상세 기능 명세](../../2.feature/mvp-spec/exercise-library.md) 3.3절(시드 규칙)

이 문서는 앱 첫 실행 시 넣는 **기본 운동(`isCustom=false`) 141개**의 목록과 분류 기준을 확정한다.
작성은 웹 조회 없이 기존 지식으로 했으며, 외부 데이터셋을 복사하지 않았다(7장).

---

## 1. 요약

| 항목 | 값 |
| --- | --- |
| 전체 운동 수 | 141 |
| 기록 유형 | `WEIGHT_REPS` 95, `REPS_ONLY` 29, `DURATION` 17 (합계 141) |
| 유산소(`CARDIO`) | 11개, 모두 `DURATION` |
| 등척성 운동 | 데드 행, 월 싯, 플랭크, 사이드 플랭크, 할로우 바디 홀드 (모두 `DURATION`) |
| 이미지 | 넣지 않는다(Q-4 기본안) |

주 부위별 개수 (13개 부위 모두 2개 이상)

| 주 부위 | 개수 | 주 부위 | 개수 |
| --- | --- | --- | --- |
| `CHEST` | 17 | `QUADRICEPS` | 16 |
| `BACK` | 18 | `HAMSTRINGS` | 6 |
| `SHOULDERS` | 16 | `GLUTES` | 10 |
| `BICEPS` | 10 | `CALVES` | 4 |
| `TRICEPS` | 9 | `ABS` | 15 |
| `FOREARMS` | 3 | `FULL_BODY` | 6 |
| | | `CARDIO` | 11 |
| | | **합계** | **141** |

장비별 개수 (8개 장비 모두 1개 이상)

| 장비 | 개수 | 장비 | 개수 |
| --- | --- | --- | --- |
| `BARBELL` | 27 | `KETTLEBELL` | 2 |
| `DUMBBELL` | 24 | `BODYWEIGHT` | 31 |
| `MACHINE` | 36 | `BAND` | 3 |
| `CABLE` | 13 | `OTHER` | 5 |
| | | **합계** | **141** |

---

## 2. 선정 기준

기준 시점 2026-10의 국내 일반 헬스장(프랜차이즈·동네 헬스장)을 기준으로 골랐다.

1. **국내 헬스장에서 흔히 하는 운동을 우선한다.** 분할 루틴(가슴·등·어깨·팔·하체)과 PT 수업에서 자주 쓰는 종목, 국내 헬스장에 대부분 있는 머신(체스트 프레스, 랫풀다운, 레그 프레스, 핵 스쿼트, 힙 어브덕션, 천국의 계단 등)을 넣었다.
2. **부위마다 장비별 대표 종목을 둔다.** 바벨·덤벨·머신·케이블·맨몸 중 실제로 많이 하는 조합만 넣고, 그립·각도만 다른 세부 변형은 대표 1~2개로 줄였다(예: 랫풀다운은 와이드·클로즈그립 2개).
3. **기록 유형이 하나로 정해지는 운동만 넣는다.** 중량과 시간(또는 거리)을 함께 기록해야 의미가 있는 운동(파머스 워크, 슬레드 푸시 등)은 v1 기록 유형(`WEIGHT_REPS`/`REPS_ONLY`/`DURATION`)으로 표현이 어려워 뺐다. 보조 중량을 기록하는 어시스티드 풀업 머신도 볼륨·1RM 계산이 거꾸로 되므로 뺐다.
4. **유산소는 헬스장 기구 위주로 5개 이상 넣는다.** 트레드밀(러닝·걷기·인클라인 걷기), 실내 사이클, 좌식 사이클, 에어 바이크, 로잉머신, 스텝밀, 일립티컬과 야외 러닝, 줄넘기를 넣었다.
5. **가정·야외에서도 쓸 수 있도록 맨몸 운동을 일부 넣는다.** 푸시업, 풀업, 스쿼트, 런지, 크런치, 플랭크, 버피 등.
6. **개수는 100~150개 안에서 정한다(ADR-006).** 빠진 운동은 사용자 정의 운동(F-EX-05)으로 추가한다.

---

## 3. 분류 규칙

코드값은 [데이터 모델 v1 1.4절](./data-model-v1.md#14-열거값)과 같다.

| 열거값 | 사용 값 |
| --- | --- |
| `MuscleGroup` | `CHEST`, `BACK`, `SHOULDERS`, `BICEPS`, `TRICEPS`, `FOREARMS`, `ABS`, `QUADRICEPS`, `HAMSTRINGS`, `GLUTES`, `CALVES`, `FULL_BODY`, `CARDIO` |
| `Equipment` | `BARBELL`, `DUMBBELL`, `MACHINE`, `CABLE`, `KETTLEBELL`, `BODYWEIGHT`, `BAND`, `OTHER` |
| `TrackingType` | `WEIGHT_REPS`, `REPS_ONLY`, `DURATION` |

### 3.1 부위

- `primaryMuscle`은 국내 분할 루틴에서 그 운동을 하는 날의 부위를 따른다. 부위별 빈도 통계는 주 부위만 센다(데이터 모델 5.3절, Q-1).
- `secondaryMuscles`는 0~3개, `primaryMuscle`과 겹치지 않게 적는다(데이터 모델 상한 5개).
- 1.4절에 없는 근육은 다음과 같이 묶는다.

| 근육 | 분류 | 예 |
| --- | --- | --- |
| 승모근 | `BACK` | 바벨·덤벨 슈러그 |
| 후면 삼각근 | `SHOULDERS` (보조 `BACK`) | 리버스 펙덱, 페이스 풀 |
| 척추기립근 | `BACK` | 백 익스텐션, 데드리프트 |
| 내전근 | `QUADRICEPS` | 힙 어덕션 머신 |
| 외전근(중둔근) | `GLUTES` | 힙 어브덕션 머신 |
| 복사근 | `ABS` | 러시안 트위스트, 사이드 플랭크 |

- 데드리프트는 국내 헬스장에서 등 운동으로 많이 하므로 컨벤셔널은 `BACK`, 스모는 `GLUTES`, 루마니안·스티프 레그는 `HAMSTRINGS`로 둔다.
- 여러 부위를 고르게 쓰는 복합 동작(버피, 파워 클린, 쓰러스터 등)은 `FULL_BODY`로 둔다.
- 유산소 기구·러닝·줄넘기는 `CARDIO`로 두고, 주로 쓰는 다리 근육을 보조 부위에 적는다.

### 3.2 장비

- 스미스 머신, 플레이트 로드 머신, 핵 스쿼트·레그 프레스, 유산소 기구는 `MACHINE`이다.
- 랫풀다운·케이블 로우처럼 케이블 스택을 쓰는 운동은 `CABLE`이다.
- EZ바, 티바 로우(랜드마인), 랜드마인 프레스는 `BARBELL`이다.
- 딥스·풀업 바, 매트, 벤치만 쓰는 운동과 야외 러닝은 `BODYWEIGHT`이다.
- 가중 벨트를 쓰는 가중 딥스·가중 풀업, 앱 롤러, 배틀 로프, 줄넘기는 `OTHER`이다.
- 저항 밴드(루프 밴드·튜빙 밴드)를 쓰는 운동은 `BAND`이다.

### 3.3 기록 유형(trackingType)

아래 순서로 처음 맞는 규칙을 적용한다.

| 순서 | 조건 | trackingType |
| --- | --- | --- |
| 1 | 유산소(`primaryMuscle = CARDIO`) | `DURATION` (장비와 관계없음. 거리는 기록하지 않는다, 데이터 모델 4.1절) |
| 2 | 등척성 운동(자세를 버티는 운동) | `DURATION` |
| 3 | 시간 단위로 하는 컨디셔닝(배틀 로프) | `DURATION` |
| 4 | 맨몸 운동(외부 중량 없음), 밴드 운동(kg 중량 없음) | `REPS_ONLY` |
| 5 | 그 외(바벨·덤벨·머신·케이블·케틀벨, 가중 맨몸 운동) | `WEIGHT_REPS` |

- 가중 딥스·가중 풀업은 맨몸 버전과 별도 운동으로 두고 `WEIGHT_REPS`로 기록한다. `weight`에는 추가 중량만 넣는다(데이터 모델 4.1절).
- 앱 롤아웃은 도구(`OTHER`)를 쓰지만 외부 중량이 없으므로 `REPS_ONLY`다.
- 밴드 운동은 저항을 kg으로 적을 수 없으므로 `REPS_ONLY`다. 밴드 강도는 세트·세션 메모에 적는다.

---

## 4. 식별자 규칙

### 4.1 고정 UUID

- 기본 운동 `id`는 미리 정한 고정 UUID를 쓴다(데이터 모델 1.1절, C-2). 기기·설치마다 같은 값이어야 JSON 가져오기·병합과 확장 시 동기화에서 같은 운동으로 인식된다.
- 형식: `0192a000-0000-7000-8000-{순번 12자리}`
  - UUID v7 형식(버전 `7`, variant `8`)을 지키는 소문자 문자열이다. 타임스탬프 부분은 실제 생성 시각이 아니라 고정값이다.
  - 마지막 12자리는 표의 `#` 번호를 0으로 채운 값이다(예: 1번 → `000000000001`, 141번 → `000000000141`).
  - **#0(`0192a000-0000-7000-8000-000000000000`)은 Settings 고정 id(`SETTINGS_ID`, 데이터 모델 2.8절)로 예약한다.** 기본 운동 번호는 1부터 쓴다.
- **한번 배포한 UUID는 바꾸거나 다른 운동에 다시 쓰지 않는다.** 운동을 빼야 하면 해당 번호를 비워 두고, 새 운동은 마지막 번호 다음(현재 142번)부터 붙인다.
- 사용자 정의 운동은 Repository가 생성 시각 기반 UUID v7을 부여하므로 이 범위와 겹치지 않는다.

### 4.2 코드

- `code`는 대문자 스네이크 영어 식별자이며, 시드 JSON 관리·테스트·향후 다국어 이름 키로 쓴다. 중복되지 않는다.
- 데이터 모델 v1의 Exercise 엔티티에는 `code`, 영어 이름 필드가 없다. 시드 JSON에는 두 값을 함께 두되, DB에는 `id`, `name`(한국어 이름), `primaryMuscle`, `secondaryMuscles`, `equipment`, `trackingType`, `isCustom=false`만 저장한다. DB 저장이 필요해지면 데이터 모델 개정으로 다룬다.
- 한국어 이름은 50자 이하이고 서로 겹치지 않는다(데이터 모델 2.1절 이름 중복 규칙).

---

## 5. 시드 표

보조 부위가 없으면 `—`로 적는다(저장 값은 `[]`).
139번 이후는 추가한 순서대로 번호를 붙이고(4.1절), 주 부위에 해당하는 표의 끝에 둔다.

### 5.1 가슴 (`CHEST`)

| # | id | code | 한국어 이름 | 영어 이름 | 주 부위 | 보조 부위 | 장비 | trackingType |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | `0192a000-0000-7000-8000-000000000001` | `BARBELL_BENCH_PRESS` | 바벨 벤치프레스 | Barbell Bench Press | `CHEST` | `TRICEPS`, `SHOULDERS` | `BARBELL` | `WEIGHT_REPS` |
| 2 | `0192a000-0000-7000-8000-000000000002` | `INCLINE_BARBELL_BENCH_PRESS` | 인클라인 바벨 벤치프레스 | Incline Barbell Bench Press | `CHEST` | `SHOULDERS`, `TRICEPS` | `BARBELL` | `WEIGHT_REPS` |
| 3 | `0192a000-0000-7000-8000-000000000003` | `DECLINE_BARBELL_BENCH_PRESS` | 디클라인 바벨 벤치프레스 | Decline Barbell Bench Press | `CHEST` | `TRICEPS` | `BARBELL` | `WEIGHT_REPS` |
| 4 | `0192a000-0000-7000-8000-000000000004` | `DUMBBELL_BENCH_PRESS` | 덤벨 벤치프레스 | Dumbbell Bench Press | `CHEST` | `TRICEPS`, `SHOULDERS` | `DUMBBELL` | `WEIGHT_REPS` |
| 5 | `0192a000-0000-7000-8000-000000000005` | `INCLINE_DUMBBELL_BENCH_PRESS` | 인클라인 덤벨 벤치프레스 | Incline Dumbbell Bench Press | `CHEST` | `SHOULDERS`, `TRICEPS` | `DUMBBELL` | `WEIGHT_REPS` |
| 6 | `0192a000-0000-7000-8000-000000000006` | `DUMBBELL_FLY` | 덤벨 플라이 | Dumbbell Fly | `CHEST` | `SHOULDERS` | `DUMBBELL` | `WEIGHT_REPS` |
| 7 | `0192a000-0000-7000-8000-000000000007` | `INCLINE_DUMBBELL_FLY` | 인클라인 덤벨 플라이 | Incline Dumbbell Fly | `CHEST` | `SHOULDERS` | `DUMBBELL` | `WEIGHT_REPS` |
| 8 | `0192a000-0000-7000-8000-000000000008` | `MACHINE_CHEST_PRESS` | 체스트 프레스 머신 | Machine Chest Press | `CHEST` | `TRICEPS`, `SHOULDERS` | `MACHINE` | `WEIGHT_REPS` |
| 9 | `0192a000-0000-7000-8000-000000000009` | `INCLINE_MACHINE_CHEST_PRESS` | 인클라인 체스트 프레스 머신 | Incline Machine Chest Press | `CHEST` | `SHOULDERS`, `TRICEPS` | `MACHINE` | `WEIGHT_REPS` |
| 10 | `0192a000-0000-7000-8000-000000000010` | `PEC_DECK_FLY` | 펙덱 플라이 | Pec Deck Fly | `CHEST` | `SHOULDERS` | `MACHINE` | `WEIGHT_REPS` |
| 11 | `0192a000-0000-7000-8000-000000000011` | `CABLE_CROSSOVER` | 케이블 크로스오버 | Cable Crossover | `CHEST` | `SHOULDERS` | `CABLE` | `WEIGHT_REPS` |
| 12 | `0192a000-0000-7000-8000-000000000012` | `SMITH_MACHINE_BENCH_PRESS` | 스미스 머신 벤치프레스 | Smith Machine Bench Press | `CHEST` | `TRICEPS`, `SHOULDERS` | `MACHINE` | `WEIGHT_REPS` |
| 13 | `0192a000-0000-7000-8000-000000000013` | `SMITH_MACHINE_INCLINE_BENCH_PRESS` | 스미스 머신 인클라인 벤치프레스 | Smith Machine Incline Bench Press | `CHEST` | `SHOULDERS`, `TRICEPS` | `MACHINE` | `WEIGHT_REPS` |
| 14 | `0192a000-0000-7000-8000-000000000014` | `DUMBBELL_PULLOVER` | 덤벨 풀오버 | Dumbbell Pullover | `CHEST` | `BACK`, `TRICEPS` | `DUMBBELL` | `WEIGHT_REPS` |
| 15 | `0192a000-0000-7000-8000-000000000015` | `PUSH_UP` | 푸시업 | Push-up | `CHEST` | `TRICEPS`, `SHOULDERS`, `ABS` | `BODYWEIGHT` | `REPS_ONLY` |
| 16 | `0192a000-0000-7000-8000-000000000016` | `CHEST_DIP` | 딥스 | Chest Dip | `CHEST` | `TRICEPS`, `SHOULDERS` | `BODYWEIGHT` | `REPS_ONLY` |
| 17 | `0192a000-0000-7000-8000-000000000017` | `WEIGHTED_DIP` | 가중 딥스 | Weighted Dip | `CHEST` | `TRICEPS`, `SHOULDERS` | `OTHER` | `WEIGHT_REPS` |

### 5.2 등 (`BACK`)

| # | id | code | 한국어 이름 | 영어 이름 | 주 부위 | 보조 부위 | 장비 | trackingType |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 18 | `0192a000-0000-7000-8000-000000000018` | `CONVENTIONAL_DEADLIFT` | 컨벤셔널 데드리프트 | Conventional Deadlift | `BACK` | `HAMSTRINGS`, `GLUTES`, `FOREARMS` | `BARBELL` | `WEIGHT_REPS` |
| 19 | `0192a000-0000-7000-8000-000000000019` | `BARBELL_ROW` | 바벨 로우 | Bent-over Barbell Row | `BACK` | `BICEPS`, `FOREARMS` | `BARBELL` | `WEIGHT_REPS` |
| 20 | `0192a000-0000-7000-8000-000000000020` | `ONE_ARM_DUMBBELL_ROW` | 원암 덤벨 로우 | One-arm Dumbbell Row | `BACK` | `BICEPS` | `DUMBBELL` | `WEIGHT_REPS` |
| 21 | `0192a000-0000-7000-8000-000000000021` | `LAT_PULLDOWN` | 랫풀다운 | Lat Pulldown | `BACK` | `BICEPS` | `CABLE` | `WEIGHT_REPS` |
| 22 | `0192a000-0000-7000-8000-000000000022` | `CLOSE_GRIP_LAT_PULLDOWN` | 클로즈그립 랫풀다운 | Close-grip Lat Pulldown | `BACK` | `BICEPS` | `CABLE` | `WEIGHT_REPS` |
| 23 | `0192a000-0000-7000-8000-000000000023` | `SEATED_CABLE_ROW` | 시티드 케이블 로우 | Seated Cable Row | `BACK` | `BICEPS` | `CABLE` | `WEIGHT_REPS` |
| 24 | `0192a000-0000-7000-8000-000000000024` | `T_BAR_ROW` | 티바 로우 | T-Bar Row | `BACK` | `BICEPS` | `BARBELL` | `WEIGHT_REPS` |
| 25 | `0192a000-0000-7000-8000-000000000025` | `SEATED_ROW_MACHINE` | 시티드 로우 머신 | Seated Row Machine | `BACK` | `BICEPS` | `MACHINE` | `WEIGHT_REPS` |
| 26 | `0192a000-0000-7000-8000-000000000026` | `HIGH_ROW_MACHINE` | 하이 로우 머신 | Machine High Row | `BACK` | `BICEPS` | `MACHINE` | `WEIGHT_REPS` |
| 27 | `0192a000-0000-7000-8000-000000000027` | `STRAIGHT_ARM_PULLDOWN` | 스트레이트 암 풀다운 | Straight-arm Pulldown | `BACK` | — | `CABLE` | `WEIGHT_REPS` |
| 28 | `0192a000-0000-7000-8000-000000000028` | `PULL_UP` | 풀업 | Pull-up | `BACK` | `BICEPS`, `FOREARMS` | `BODYWEIGHT` | `REPS_ONLY` |
| 29 | `0192a000-0000-7000-8000-000000000029` | `CHIN_UP` | 친업 | Chin-up | `BACK` | `BICEPS` | `BODYWEIGHT` | `REPS_ONLY` |
| 30 | `0192a000-0000-7000-8000-000000000030` | `WEIGHTED_PULL_UP` | 가중 풀업 | Weighted Pull-up | `BACK` | `BICEPS`, `FOREARMS` | `OTHER` | `WEIGHT_REPS` |
| 31 | `0192a000-0000-7000-8000-000000000031` | `INVERTED_ROW` | 인버티드 로우 | Inverted Row | `BACK` | `BICEPS` | `BODYWEIGHT` | `REPS_ONLY` |
| 32 | `0192a000-0000-7000-8000-000000000032` | `BACK_EXTENSION` | 백 익스텐션 | Back Extension | `BACK` | `GLUTES`, `HAMSTRINGS` | `BODYWEIGHT` | `REPS_ONLY` |
| 33 | `0192a000-0000-7000-8000-000000000033` | `RACK_PULL` | 랙 풀 | Rack Pull | `BACK` | `GLUTES`, `FOREARMS` | `BARBELL` | `WEIGHT_REPS` |
| 34 | `0192a000-0000-7000-8000-000000000034` | `BARBELL_SHRUG` | 바벨 슈러그 | Barbell Shrug | `BACK` | `FOREARMS` | `BARBELL` | `WEIGHT_REPS` |
| 35 | `0192a000-0000-7000-8000-000000000035` | `DUMBBELL_SHRUG` | 덤벨 슈러그 | Dumbbell Shrug | `BACK` | `FOREARMS` | `DUMBBELL` | `WEIGHT_REPS` |

### 5.3 어깨 (`SHOULDERS`)

| # | id | code | 한국어 이름 | 영어 이름 | 주 부위 | 보조 부위 | 장비 | trackingType |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 36 | `0192a000-0000-7000-8000-000000000036` | `BARBELL_OVERHEAD_PRESS` | 바벨 오버헤드 프레스 | Barbell Overhead Press | `SHOULDERS` | `TRICEPS` | `BARBELL` | `WEIGHT_REPS` |
| 37 | `0192a000-0000-7000-8000-000000000037` | `DUMBBELL_SHOULDER_PRESS` | 덤벨 숄더 프레스 | Dumbbell Shoulder Press | `SHOULDERS` | `TRICEPS` | `DUMBBELL` | `WEIGHT_REPS` |
| 38 | `0192a000-0000-7000-8000-000000000038` | `MACHINE_SHOULDER_PRESS` | 숄더 프레스 머신 | Machine Shoulder Press | `SHOULDERS` | `TRICEPS` | `MACHINE` | `WEIGHT_REPS` |
| 39 | `0192a000-0000-7000-8000-000000000039` | `SMITH_MACHINE_SHOULDER_PRESS` | 스미스 머신 숄더 프레스 | Smith Machine Shoulder Press | `SHOULDERS` | `TRICEPS` | `MACHINE` | `WEIGHT_REPS` |
| 40 | `0192a000-0000-7000-8000-000000000040` | `ARNOLD_PRESS` | 아놀드 프레스 | Arnold Press | `SHOULDERS` | `TRICEPS` | `DUMBBELL` | `WEIGHT_REPS` |
| 41 | `0192a000-0000-7000-8000-000000000041` | `DUMBBELL_LATERAL_RAISE` | 덤벨 사이드 레터럴 레이즈 | Dumbbell Lateral Raise | `SHOULDERS` | — | `DUMBBELL` | `WEIGHT_REPS` |
| 42 | `0192a000-0000-7000-8000-000000000042` | `CABLE_LATERAL_RAISE` | 케이블 사이드 레터럴 레이즈 | Cable Lateral Raise | `SHOULDERS` | — | `CABLE` | `WEIGHT_REPS` |
| 43 | `0192a000-0000-7000-8000-000000000043` | `MACHINE_LATERAL_RAISE` | 사이드 레터럴 레이즈 머신 | Machine Lateral Raise | `SHOULDERS` | — | `MACHINE` | `WEIGHT_REPS` |
| 44 | `0192a000-0000-7000-8000-000000000044` | `DUMBBELL_FRONT_RAISE` | 덤벨 프론트 레이즈 | Dumbbell Front Raise | `SHOULDERS` | `CHEST` | `DUMBBELL` | `WEIGHT_REPS` |
| 45 | `0192a000-0000-7000-8000-000000000045` | `BENT_OVER_LATERAL_RAISE` | 벤트오버 레터럴 레이즈 | Bent-over Lateral Raise | `SHOULDERS` | `BACK` | `DUMBBELL` | `WEIGHT_REPS` |
| 46 | `0192a000-0000-7000-8000-000000000046` | `REVERSE_PEC_DECK` | 리버스 펙덱 플라이 | Reverse Pec Deck Fly | `SHOULDERS` | `BACK` | `MACHINE` | `WEIGHT_REPS` |
| 47 | `0192a000-0000-7000-8000-000000000047` | `FACE_PULL` | 페이스 풀 | Face Pull | `SHOULDERS` | `BACK` | `CABLE` | `WEIGHT_REPS` |
| 48 | `0192a000-0000-7000-8000-000000000048` | `BARBELL_UPRIGHT_ROW` | 바벨 업라이트 로우 | Barbell Upright Row | `SHOULDERS` | `BACK`, `BICEPS` | `BARBELL` | `WEIGHT_REPS` |
| 49 | `0192a000-0000-7000-8000-000000000049` | `LANDMINE_PRESS` | 랜드마인 프레스 | Landmine Press | `SHOULDERS` | `CHEST`, `TRICEPS` | `BARBELL` | `WEIGHT_REPS` |
| 50 | `0192a000-0000-7000-8000-000000000050` | `PIKE_PUSH_UP` | 파이크 푸시업 | Pike Push-up | `SHOULDERS` | `TRICEPS` | `BODYWEIGHT` | `REPS_ONLY` |
| 139 | `0192a000-0000-7000-8000-000000000139` | `BAND_PULL_APART` | 밴드 풀어파트 | Band Pull-apart | `SHOULDERS` | `BACK` | `BAND` | `REPS_ONLY` |

### 5.4 이두 (`BICEPS`)

| # | id | code | 한국어 이름 | 영어 이름 | 주 부위 | 보조 부위 | 장비 | trackingType |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 51 | `0192a000-0000-7000-8000-000000000051` | `BARBELL_CURL` | 바벨 컬 | Barbell Curl | `BICEPS` | `FOREARMS` | `BARBELL` | `WEIGHT_REPS` |
| 52 | `0192a000-0000-7000-8000-000000000052` | `EZ_BAR_CURL` | 이지바 컬 | EZ-Bar Curl | `BICEPS` | `FOREARMS` | `BARBELL` | `WEIGHT_REPS` |
| 53 | `0192a000-0000-7000-8000-000000000053` | `DUMBBELL_CURL` | 덤벨 컬 | Dumbbell Curl | `BICEPS` | `FOREARMS` | `DUMBBELL` | `WEIGHT_REPS` |
| 54 | `0192a000-0000-7000-8000-000000000054` | `HAMMER_CURL` | 해머 컬 | Hammer Curl | `BICEPS` | `FOREARMS` | `DUMBBELL` | `WEIGHT_REPS` |
| 55 | `0192a000-0000-7000-8000-000000000055` | `INCLINE_DUMBBELL_CURL` | 인클라인 덤벨 컬 | Incline Dumbbell Curl | `BICEPS` | — | `DUMBBELL` | `WEIGHT_REPS` |
| 56 | `0192a000-0000-7000-8000-000000000056` | `CONCENTRATION_CURL` | 컨센트레이션 컬 | Concentration Curl | `BICEPS` | — | `DUMBBELL` | `WEIGHT_REPS` |
| 57 | `0192a000-0000-7000-8000-000000000057` | `EZ_BAR_PREACHER_CURL` | 이지바 프리처 컬 | EZ-Bar Preacher Curl | `BICEPS` | `FOREARMS` | `BARBELL` | `WEIGHT_REPS` |
| 58 | `0192a000-0000-7000-8000-000000000058` | `MACHINE_BICEPS_CURL` | 바이셉스 컬 머신 | Machine Biceps Curl | `BICEPS` | — | `MACHINE` | `WEIGHT_REPS` |
| 59 | `0192a000-0000-7000-8000-000000000059` | `CABLE_CURL` | 케이블 컬 | Cable Curl | `BICEPS` | `FOREARMS` | `CABLE` | `WEIGHT_REPS` |
| 140 | `0192a000-0000-7000-8000-000000000140` | `BAND_CURL` | 밴드 컬 | Band Curl | `BICEPS` | `FOREARMS` | `BAND` | `REPS_ONLY` |

### 5.5 삼두 (`TRICEPS`)

| # | id | code | 한국어 이름 | 영어 이름 | 주 부위 | 보조 부위 | 장비 | trackingType |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 60 | `0192a000-0000-7000-8000-000000000060` | `CABLE_TRICEPS_PUSHDOWN` | 트라이셉스 푸시다운 | Cable Triceps Pushdown | `TRICEPS` | — | `CABLE` | `WEIGHT_REPS` |
| 61 | `0192a000-0000-7000-8000-000000000061` | `ROPE_TRICEPS_PUSHDOWN` | 로프 푸시다운 | Rope Triceps Pushdown | `TRICEPS` | — | `CABLE` | `WEIGHT_REPS` |
| 62 | `0192a000-0000-7000-8000-000000000062` | `CABLE_OVERHEAD_TRICEPS_EXTENSION` | 케이블 오버헤드 트라이셉스 익스텐션 | Cable Overhead Triceps Extension | `TRICEPS` | — | `CABLE` | `WEIGHT_REPS` |
| 63 | `0192a000-0000-7000-8000-000000000063` | `LYING_TRICEPS_EXTENSION` | 라잉 트라이셉스 익스텐션 | Lying Triceps Extension (Skull Crusher) | `TRICEPS` | — | `BARBELL` | `WEIGHT_REPS` |
| 64 | `0192a000-0000-7000-8000-000000000064` | `DUMBBELL_OVERHEAD_TRICEPS_EXTENSION` | 덤벨 오버헤드 트라이셉스 익스텐션 | Dumbbell Overhead Triceps Extension | `TRICEPS` | — | `DUMBBELL` | `WEIGHT_REPS` |
| 65 | `0192a000-0000-7000-8000-000000000065` | `DUMBBELL_KICKBACK` | 덤벨 킥백 | Dumbbell Triceps Kickback | `TRICEPS` | — | `DUMBBELL` | `WEIGHT_REPS` |
| 66 | `0192a000-0000-7000-8000-000000000066` | `CLOSE_GRIP_BENCH_PRESS` | 클로즈그립 벤치프레스 | Close-grip Bench Press | `TRICEPS` | `CHEST`, `SHOULDERS` | `BARBELL` | `WEIGHT_REPS` |
| 67 | `0192a000-0000-7000-8000-000000000067` | `BENCH_DIP` | 벤치 딥스 | Bench Dip | `TRICEPS` | `CHEST`, `SHOULDERS` | `BODYWEIGHT` | `REPS_ONLY` |
| 68 | `0192a000-0000-7000-8000-000000000068` | `MACHINE_DIP` | 딥스 머신 | Machine Dip | `TRICEPS` | `CHEST`, `SHOULDERS` | `MACHINE` | `WEIGHT_REPS` |

### 5.6 전완 (`FOREARMS`)

| # | id | code | 한국어 이름 | 영어 이름 | 주 부위 | 보조 부위 | 장비 | trackingType |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 69 | `0192a000-0000-7000-8000-000000000069` | `BARBELL_WRIST_CURL` | 바벨 리스트 컬 | Barbell Wrist Curl | `FOREARMS` | — | `BARBELL` | `WEIGHT_REPS` |
| 70 | `0192a000-0000-7000-8000-000000000070` | `REVERSE_BARBELL_CURL` | 리버스 바벨 컬 | Reverse Barbell Curl | `FOREARMS` | `BICEPS` | `BARBELL` | `WEIGHT_REPS` |
| 71 | `0192a000-0000-7000-8000-000000000071` | `DEAD_HANG` | 데드 행 | Dead Hang | `FOREARMS` | `BACK` | `BODYWEIGHT` | `DURATION` |

### 5.7 대퇴사두 (`QUADRICEPS`)

| # | id | code | 한국어 이름 | 영어 이름 | 주 부위 | 보조 부위 | 장비 | trackingType |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 72 | `0192a000-0000-7000-8000-000000000072` | `BARBELL_BACK_SQUAT` | 바벨 백 스쿼트 | Barbell Back Squat | `QUADRICEPS` | `GLUTES`, `HAMSTRINGS`, `ABS` | `BARBELL` | `WEIGHT_REPS` |
| 73 | `0192a000-0000-7000-8000-000000000073` | `BARBELL_FRONT_SQUAT` | 바벨 프론트 스쿼트 | Barbell Front Squat | `QUADRICEPS` | `GLUTES`, `ABS` | `BARBELL` | `WEIGHT_REPS` |
| 74 | `0192a000-0000-7000-8000-000000000074` | `SMITH_MACHINE_SQUAT` | 스미스 머신 스쿼트 | Smith Machine Squat | `QUADRICEPS` | `GLUTES`, `HAMSTRINGS` | `MACHINE` | `WEIGHT_REPS` |
| 75 | `0192a000-0000-7000-8000-000000000075` | `LEG_PRESS` | 레그 프레스 | Leg Press | `QUADRICEPS` | `GLUTES`, `HAMSTRINGS` | `MACHINE` | `WEIGHT_REPS` |
| 76 | `0192a000-0000-7000-8000-000000000076` | `HACK_SQUAT_MACHINE` | 핵 스쿼트 머신 | Machine Hack Squat | `QUADRICEPS` | `GLUTES` | `MACHINE` | `WEIGHT_REPS` |
| 77 | `0192a000-0000-7000-8000-000000000077` | `V_SQUAT_MACHINE` | 브이 스쿼트 머신 | V-Squat Machine | `QUADRICEPS` | `GLUTES` | `MACHINE` | `WEIGHT_REPS` |
| 78 | `0192a000-0000-7000-8000-000000000078` | `LEG_EXTENSION` | 레그 익스텐션 | Leg Extension | `QUADRICEPS` | — | `MACHINE` | `WEIGHT_REPS` |
| 79 | `0192a000-0000-7000-8000-000000000079` | `GOBLET_SQUAT` | 고블릿 스쿼트 | Goblet Squat | `QUADRICEPS` | `GLUTES`, `ABS` | `DUMBBELL` | `WEIGHT_REPS` |
| 80 | `0192a000-0000-7000-8000-000000000080` | `BULGARIAN_SPLIT_SQUAT` | 덤벨 불가리안 스플릿 스쿼트 | Dumbbell Bulgarian Split Squat | `QUADRICEPS` | `GLUTES`, `HAMSTRINGS` | `DUMBBELL` | `WEIGHT_REPS` |
| 81 | `0192a000-0000-7000-8000-000000000081` | `DUMBBELL_LUNGE` | 덤벨 런지 | Dumbbell Lunge | `QUADRICEPS` | `GLUTES`, `HAMSTRINGS` | `DUMBBELL` | `WEIGHT_REPS` |
| 82 | `0192a000-0000-7000-8000-000000000082` | `DUMBBELL_STEP_UP` | 덤벨 스텝업 | Dumbbell Step-up | `QUADRICEPS` | `GLUTES` | `DUMBBELL` | `WEIGHT_REPS` |
| 83 | `0192a000-0000-7000-8000-000000000083` | `BODYWEIGHT_SQUAT` | 맨몸 스쿼트 | Bodyweight Squat | `QUADRICEPS` | `GLUTES` | `BODYWEIGHT` | `REPS_ONLY` |
| 84 | `0192a000-0000-7000-8000-000000000084` | `BODYWEIGHT_LUNGE` | 맨몸 런지 | Bodyweight Lunge | `QUADRICEPS` | `GLUTES`, `HAMSTRINGS` | `BODYWEIGHT` | `REPS_ONLY` |
| 85 | `0192a000-0000-7000-8000-000000000085` | `JUMP_SQUAT` | 점프 스쿼트 | Jump Squat | `QUADRICEPS` | `GLUTES`, `CALVES` | `BODYWEIGHT` | `REPS_ONLY` |
| 86 | `0192a000-0000-7000-8000-000000000086` | `WALL_SIT` | 월 싯 | Wall Sit | `QUADRICEPS` | `GLUTES` | `BODYWEIGHT` | `DURATION` |
| 87 | `0192a000-0000-7000-8000-000000000087` | `HIP_ADDUCTION_MACHINE` | 힙 어덕션 머신 | Hip Adduction Machine | `QUADRICEPS` | — | `MACHINE` | `WEIGHT_REPS` |

### 5.8 햄스트링 (`HAMSTRINGS`)

| # | id | code | 한국어 이름 | 영어 이름 | 주 부위 | 보조 부위 | 장비 | trackingType |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 88 | `0192a000-0000-7000-8000-000000000088` | `BARBELL_ROMANIAN_DEADLIFT` | 루마니안 데드리프트 | Barbell Romanian Deadlift | `HAMSTRINGS` | `GLUTES`, `BACK` | `BARBELL` | `WEIGHT_REPS` |
| 89 | `0192a000-0000-7000-8000-000000000089` | `DUMBBELL_ROMANIAN_DEADLIFT` | 덤벨 루마니안 데드리프트 | Dumbbell Romanian Deadlift | `HAMSTRINGS` | `GLUTES`, `BACK` | `DUMBBELL` | `WEIGHT_REPS` |
| 90 | `0192a000-0000-7000-8000-000000000090` | `STIFF_LEG_DEADLIFT` | 스티프 레그 데드리프트 | Stiff-leg Deadlift | `HAMSTRINGS` | `GLUTES`, `BACK` | `BARBELL` | `WEIGHT_REPS` |
| 91 | `0192a000-0000-7000-8000-000000000091` | `LYING_LEG_CURL` | 라잉 레그 컬 | Lying Leg Curl | `HAMSTRINGS` | `CALVES` | `MACHINE` | `WEIGHT_REPS` |
| 92 | `0192a000-0000-7000-8000-000000000092` | `SEATED_LEG_CURL` | 시티드 레그 컬 | Seated Leg Curl | `HAMSTRINGS` | — | `MACHINE` | `WEIGHT_REPS` |
| 93 | `0192a000-0000-7000-8000-000000000093` | `GOOD_MORNING` | 굿모닝 | Good Morning | `HAMSTRINGS` | `GLUTES`, `BACK` | `BARBELL` | `WEIGHT_REPS` |

### 5.9 둔근 (`GLUTES`)

| # | id | code | 한국어 이름 | 영어 이름 | 주 부위 | 보조 부위 | 장비 | trackingType |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 94 | `0192a000-0000-7000-8000-000000000094` | `BARBELL_HIP_THRUST` | 바벨 힙 쓰러스트 | Barbell Hip Thrust | `GLUTES` | `HAMSTRINGS` | `BARBELL` | `WEIGHT_REPS` |
| 95 | `0192a000-0000-7000-8000-000000000095` | `HIP_THRUST_MACHINE` | 힙 쓰러스트 머신 | Hip Thrust Machine | `GLUTES` | `HAMSTRINGS` | `MACHINE` | `WEIGHT_REPS` |
| 96 | `0192a000-0000-7000-8000-000000000096` | `GLUTE_BRIDGE` | 글루트 브릿지 | Glute Bridge | `GLUTES` | `HAMSTRINGS` | `BODYWEIGHT` | `REPS_ONLY` |
| 97 | `0192a000-0000-7000-8000-000000000097` | `CABLE_GLUTE_KICKBACK` | 케이블 킥백 | Cable Glute Kickback | `GLUTES` | `HAMSTRINGS` | `CABLE` | `WEIGHT_REPS` |
| 98 | `0192a000-0000-7000-8000-000000000098` | `HIP_ABDUCTION_MACHINE` | 힙 어브덕션 머신 | Hip Abduction Machine | `GLUTES` | — | `MACHINE` | `WEIGHT_REPS` |
| 99 | `0192a000-0000-7000-8000-000000000099` | `SUMO_DEADLIFT` | 스모 데드리프트 | Sumo Deadlift | `GLUTES` | `QUADRICEPS`, `HAMSTRINGS`, `BACK` | `BARBELL` | `WEIGHT_REPS` |
| 100 | `0192a000-0000-7000-8000-000000000100` | `DUMBBELL_SUMO_SQUAT` | 덤벨 와이드 스쿼트 | Dumbbell Sumo Squat | `GLUTES` | `QUADRICEPS` | `DUMBBELL` | `WEIGHT_REPS` |
| 101 | `0192a000-0000-7000-8000-000000000101` | `KETTLEBELL_SWING` | 케틀벨 스윙 | Kettlebell Swing | `GLUTES` | `HAMSTRINGS`, `BACK`, `SHOULDERS` | `KETTLEBELL` | `WEIGHT_REPS` |
| 102 | `0192a000-0000-7000-8000-000000000102` | `DONKEY_KICK` | 동키 킥 | Donkey Kick | `GLUTES` | `HAMSTRINGS` | `BODYWEIGHT` | `REPS_ONLY` |
| 141 | `0192a000-0000-7000-8000-000000000141` | `BAND_LATERAL_WALK` | 밴드 래터럴 워크 | Band Lateral Walk | `GLUTES` | — | `BAND` | `REPS_ONLY` |

### 5.10 종아리 (`CALVES`)

| # | id | code | 한국어 이름 | 영어 이름 | 주 부위 | 보조 부위 | 장비 | trackingType |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 103 | `0192a000-0000-7000-8000-000000000103` | `STANDING_CALF_RAISE_MACHINE` | 스탠딩 카프 레이즈 머신 | Standing Calf Raise Machine | `CALVES` | — | `MACHINE` | `WEIGHT_REPS` |
| 104 | `0192a000-0000-7000-8000-000000000104` | `SEATED_CALF_RAISE_MACHINE` | 시티드 카프 레이즈 머신 | Seated Calf Raise Machine | `CALVES` | — | `MACHINE` | `WEIGHT_REPS` |
| 105 | `0192a000-0000-7000-8000-000000000105` | `LEG_PRESS_CALF_RAISE` | 레그 프레스 카프 레이즈 | Leg Press Calf Raise | `CALVES` | — | `MACHINE` | `WEIGHT_REPS` |
| 106 | `0192a000-0000-7000-8000-000000000106` | `BODYWEIGHT_CALF_RAISE` | 맨몸 카프 레이즈 | Bodyweight Calf Raise | `CALVES` | — | `BODYWEIGHT` | `REPS_ONLY` |

### 5.11 복근 (`ABS`)

| # | id | code | 한국어 이름 | 영어 이름 | 주 부위 | 보조 부위 | 장비 | trackingType |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 107 | `0192a000-0000-7000-8000-000000000107` | `CRUNCH` | 크런치 | Crunch | `ABS` | — | `BODYWEIGHT` | `REPS_ONLY` |
| 108 | `0192a000-0000-7000-8000-000000000108` | `SIT_UP` | 싯업 | Sit-up | `ABS` | — | `BODYWEIGHT` | `REPS_ONLY` |
| 109 | `0192a000-0000-7000-8000-000000000109` | `DECLINE_SIT_UP` | 디클라인 싯업 | Decline Sit-up | `ABS` | — | `BODYWEIGHT` | `REPS_ONLY` |
| 110 | `0192a000-0000-7000-8000-000000000110` | `BICYCLE_CRUNCH` | 바이시클 크런치 | Bicycle Crunch | `ABS` | — | `BODYWEIGHT` | `REPS_ONLY` |
| 111 | `0192a000-0000-7000-8000-000000000111` | `LYING_LEG_RAISE` | 라잉 레그 레이즈 | Lying Leg Raise | `ABS` | — | `BODYWEIGHT` | `REPS_ONLY` |
| 112 | `0192a000-0000-7000-8000-000000000112` | `HANGING_LEG_RAISE` | 행잉 레그 레이즈 | Hanging Leg Raise | `ABS` | `FOREARMS` | `BODYWEIGHT` | `REPS_ONLY` |
| 113 | `0192a000-0000-7000-8000-000000000113` | `CAPTAINS_CHAIR_KNEE_RAISE` | 캡틴스 체어 니 레이즈 | Captain's Chair Knee Raise | `ABS` | — | `BODYWEIGHT` | `REPS_ONLY` |
| 114 | `0192a000-0000-7000-8000-000000000114` | `RUSSIAN_TWIST` | 러시안 트위스트 | Russian Twist | `ABS` | — | `BODYWEIGHT` | `REPS_ONLY` |
| 115 | `0192a000-0000-7000-8000-000000000115` | `MOUNTAIN_CLIMBER` | 마운틴 클라이머 | Mountain Climber | `ABS` | `SHOULDERS`, `QUADRICEPS` | `BODYWEIGHT` | `REPS_ONLY` |
| 116 | `0192a000-0000-7000-8000-000000000116` | `AB_WHEEL_ROLLOUT` | 앱 롤아웃 | Ab Wheel Rollout | `ABS` | `SHOULDERS`, `BACK` | `OTHER` | `REPS_ONLY` |
| 117 | `0192a000-0000-7000-8000-000000000117` | `CABLE_CRUNCH` | 케이블 크런치 | Cable Crunch | `ABS` | — | `CABLE` | `WEIGHT_REPS` |
| 118 | `0192a000-0000-7000-8000-000000000118` | `AB_CRUNCH_MACHINE` | 앱 크런치 머신 | Ab Crunch Machine | `ABS` | — | `MACHINE` | `WEIGHT_REPS` |
| 119 | `0192a000-0000-7000-8000-000000000119` | `PLANK` | 플랭크 | Plank | `ABS` | `SHOULDERS` | `BODYWEIGHT` | `DURATION` |
| 120 | `0192a000-0000-7000-8000-000000000120` | `SIDE_PLANK` | 사이드 플랭크 | Side Plank | `ABS` | `SHOULDERS` | `BODYWEIGHT` | `DURATION` |
| 121 | `0192a000-0000-7000-8000-000000000121` | `HOLLOW_BODY_HOLD` | 할로우 바디 홀드 | Hollow Body Hold | `ABS` | — | `BODYWEIGHT` | `DURATION` |

### 5.12 전신 (`FULL_BODY`)

| # | id | code | 한국어 이름 | 영어 이름 | 주 부위 | 보조 부위 | 장비 | trackingType |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 122 | `0192a000-0000-7000-8000-000000000122` | `BURPEE` | 버피 | Burpee | `FULL_BODY` | `CHEST`, `QUADRICEPS`, `SHOULDERS` | `BODYWEIGHT` | `REPS_ONLY` |
| 123 | `0192a000-0000-7000-8000-000000000123` | `JUMPING_JACK` | 점핑 잭 | Jumping Jack | `FULL_BODY` | `CALVES`, `SHOULDERS` | `BODYWEIGHT` | `REPS_ONLY` |
| 124 | `0192a000-0000-7000-8000-000000000124` | `POWER_CLEAN` | 파워 클린 | Power Clean | `FULL_BODY` | `BACK`, `QUADRICEPS`, `SHOULDERS` | `BARBELL` | `WEIGHT_REPS` |
| 125 | `0192a000-0000-7000-8000-000000000125` | `BARBELL_THRUSTER` | 바벨 쓰러스터 | Barbell Thruster | `FULL_BODY` | `QUADRICEPS`, `SHOULDERS`, `GLUTES` | `BARBELL` | `WEIGHT_REPS` |
| 126 | `0192a000-0000-7000-8000-000000000126` | `KETTLEBELL_TURKISH_GET_UP` | 케틀벨 터키시 겟업 | Kettlebell Turkish Get-up | `FULL_BODY` | `SHOULDERS`, `ABS`, `GLUTES` | `KETTLEBELL` | `WEIGHT_REPS` |
| 127 | `0192a000-0000-7000-8000-000000000127` | `BATTLE_ROPE` | 배틀 로프 | Battle Rope | `FULL_BODY` | `SHOULDERS`, `ABS` | `OTHER` | `DURATION` |

### 5.13 유산소 (`CARDIO`)

모두 `DURATION`이다(3.3절 규칙 1).

| # | id | code | 한국어 이름 | 영어 이름 | 주 부위 | 보조 부위 | 장비 | trackingType |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 128 | `0192a000-0000-7000-8000-000000000128` | `TREADMILL_RUN` | 트레드밀 러닝 | Treadmill Running | `CARDIO` | `QUADRICEPS`, `HAMSTRINGS`, `CALVES` | `MACHINE` | `DURATION` |
| 129 | `0192a000-0000-7000-8000-000000000129` | `TREADMILL_WALK` | 트레드밀 걷기 | Treadmill Walking | `CARDIO` | `QUADRICEPS`, `CALVES` | `MACHINE` | `DURATION` |
| 130 | `0192a000-0000-7000-8000-000000000130` | `TREADMILL_INCLINE_WALK` | 트레드밀 인클라인 걷기 | Treadmill Incline Walking | `CARDIO` | `GLUTES`, `HAMSTRINGS`, `CALVES` | `MACHINE` | `DURATION` |
| 131 | `0192a000-0000-7000-8000-000000000131` | `OUTDOOR_RUN` | 야외 러닝 | Outdoor Running | `CARDIO` | `QUADRICEPS`, `HAMSTRINGS`, `CALVES` | `BODYWEIGHT` | `DURATION` |
| 132 | `0192a000-0000-7000-8000-000000000132` | `STATIONARY_BIKE` | 실내 사이클 | Stationary Bike | `CARDIO` | `QUADRICEPS`, `HAMSTRINGS` | `MACHINE` | `DURATION` |
| 133 | `0192a000-0000-7000-8000-000000000133` | `RECUMBENT_BIKE` | 좌식 사이클 | Recumbent Bike | `CARDIO` | `QUADRICEPS`, `GLUTES` | `MACHINE` | `DURATION` |
| 134 | `0192a000-0000-7000-8000-000000000134` | `AIR_BIKE` | 에어 바이크 | Air Bike (Assault Bike) | `CARDIO` | `QUADRICEPS`, `SHOULDERS` | `MACHINE` | `DURATION` |
| 135 | `0192a000-0000-7000-8000-000000000135` | `ROWING_MACHINE` | 로잉머신 | Rowing Machine | `CARDIO` | `BACK`, `QUADRICEPS`, `BICEPS` | `MACHINE` | `DURATION` |
| 136 | `0192a000-0000-7000-8000-000000000136` | `STAIRMILL` | 스텝밀 (천국의 계단) | Stair Climber (Stepmill) | `CARDIO` | `GLUTES`, `QUADRICEPS`, `CALVES` | `MACHINE` | `DURATION` |
| 137 | `0192a000-0000-7000-8000-000000000137` | `ELLIPTICAL` | 일립티컬 | Elliptical Trainer | `CARDIO` | `QUADRICEPS`, `GLUTES` | `MACHINE` | `DURATION` |
| 138 | `0192a000-0000-7000-8000-000000000138` | `JUMP_ROPE` | 줄넘기 | Jump Rope | `CARDIO` | `CALVES` | `OTHER` | `DURATION` |

---

## 6. 시드 JSON과 검증

시드 파일(예: `src/data/seed/exercises.v1.json`)은 이 표를 그대로 옮긴다. 레코드 예시는 다음과 같다.

```json
{
  "id": "0192a000-0000-7000-8000-000000000001",
  "code": "BARBELL_BENCH_PRESS",
  "name": "바벨 벤치프레스",
  "nameEn": "Barbell Bench Press",
  "primaryMuscle": "CHEST",
  "secondaryMuscles": ["TRICEPS", "SHOULDERS"],
  "equipment": "BARBELL",
  "trackingType": "WEIGHT_REPS"
}
```

- 앱 첫 실행 시 `isCustom=false`, `createdAt`·`updatedAt`=**시드 작성 시각 고정값**, `deletedAt=null`을 붙여 저장한다. `code`, `nameEn`은 DB에 넣지 않는다(4.2절).
  - 고정값은 시드 버전마다 하나의 UTC ISO 8601 문자열(예: v1은 `2026-10-01T00:00:00.000Z`)을 시드 파일에 상수로 두고, 그 버전의 모든 기본 운동에 같은 값을 쓴다. 삽입 시각을 쓰지 않는다.
  - 이유: 기기·설치마다 같은 기본 운동 레코드가 같은 `updatedAt`을 가져야 가져오기 병합의 LWW 비교(데이터 모델 1.1절)가 흔들리지 않는다. [운동 라이브러리 명세](../../2.feature/mvp-spec/exercise-library.md) 3.3절과 같은 규칙이다.
- 시드 파일 단위 테스트에서 아래를 검사한다.
  - 행 수가 100~150개
  - 13개 부위마다 2개 이상, 8개 장비마다 1개 이상(운동 라이브러리 명세 3.3절)
  - 모든 행의 `createdAt`·`updatedAt`이 시드 버전의 고정값과 같음
  - `id`, `code`, `name`(공백·대소문자 무시)에 중복 없음
  - `id`가 UUID v7 형식(소문자)
  - 부위·장비·trackingType이 1.4절 열거값 안에 있음
  - `secondaryMuscles`에 중복이 없고 `primaryMuscle`을 포함하지 않음
  - `primaryMuscle = CARDIO`인 행은 모두 `trackingType = DURATION`
- 시드 개정(운동 추가·이름 수정)은 새 시드 버전으로 내고, 이미 설치된 앱에는 `id` 기준으로 없는 운동만 추가한다. 기존 기본 운동의 `id`는 바꾸지 않는다(4.1절). 새 시드 버전에서 추가한 운동의 `createdAt`·`updatedAt`은 그 버전의 고정값이다.

---

## 7. 참고 데이터셋 라이선스 주의 (R-3)

- 이 목록은 **외부 데이터셋을 복사하지 않고** 일반적인 운동 이름과 분류 지식으로 직접 작성했다. 운동 이름 자체는 일반 용어지만, 데이터셋의 설명문·이미지·분류 구조를 그대로 가져오면 라이선스 의무가 생길 수 있다.
- ADR-006에서 참고 자료로 언급한 데이터셋은 아래처럼 다룬다. 라이선스 내용은 작성자의 기억에 기반하며 **원문을 확인하지 않았다.** 데이터셋에서 무엇이든 가져오기 전에 반드시 저장소의 LICENSE 원문을 확인한다.

| 데이터셋 | 알려진 라이선스(미확인) | 이 프로젝트에서의 사용 |
| --- | --- | --- |
| free-exercise-db | 퍼블릭 도메인(Unlicense)으로 알려짐 | 운동 선정 시 참고만 함. 설명문·이미지는 가져오지 않음 |
| wger | CC-BY-SA 계열로 알려짐(데이터), 코드 AGPL | 사용하지 않음. 가져오면 출처 표시·동일 조건 배포 의무 |
| ExerciseDB | 상용 API 약관 | 사용하지 않음. 오프라인 재배포가 금지될 수 있음 |

- 나중에 설명문·이미지(Q-4)를 추가할 때 외부 데이터를 쓰면, 해당 라이선스를 확인하고 앱의 라이선스 화면(R-3 대응)에 출처를 표시한다.

---

## 8. 넣지 않은 운동과 후속 작업

| 운동 | 제외 이유 | 대안 |
| --- | --- | --- |
| 어시스티드 풀업 머신 | 보조 중량을 `weight`로 기록하면 볼륨·1RM이 거꾸로 계산됨 | 사용자 정의 운동(`REPS_ONLY`)으로 추가 |
| 파머스 워크, 슬레드 푸시·풀 | 중량과 거리(시간)를 함께 기록해야 함 | 기록 유형 확장 시 검토 |
| 수영, 등산, 사이클(야외) | 헬스장 운동이 아니고 거리 기록이 중요함 | 사용자 정의 운동(`DURATION`) |
| 요가, 필라테스, 스트레칭 | 근력·유산소 기록 앱 범위 밖 | 사용자 정의 운동(`DURATION`) |
| 세부 그립·각도 변형(와이드 바벨 로우, 리버스 그립 벤치 등) | 대표 종목으로 충분, 목록이 지나치게 길어짐 | 사용자 정의 운동 |

- 기본 운동에 이미지·설명을 넣을지(Q-4)는 이 문서에서 다루지 않는다. 현재 기본안대로 이미지 없이 시작한다.
- 다국어 지원 시 `code`를 번역 키로 쓰는 방안은 확장 단계에서 결정한다.
