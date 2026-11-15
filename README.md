# 차량용 앱

> BMS 데이터로 배터리 건강·예측 정비·V2L/V2G 전력을 관리하고, 장거리 여행에서는 운전자 피로와 아이·어르신의 휴식 시점을 판단해 차량이 먼저 휴게소를 제안하며 음성으로 가족을 돌보는 전기차 통합 차량 앱입니다.

현대자동차그룹 차량용 앱 개발 공모전 출품작 · Pleos Connect (Android Automotive) 앱 · v0.6.0

| 화면 | 주요 기능 |
|---|---|
| 🧭 내비 · 🛡 피로도 | 목적지 검색 → 경로·도착 배터리·경로상 휴게소/충전소 사전 점검 → Pleos 내비 안내, 주행 시간·속도 기반 피로도 단계와 휴게소 추천 |
| ⚡ Energy Hub | V2L 스마트 방전(출근용 최소 전력 잠금), V2G 전력 거래 대시보드(캔들·거래량·RSI) |
| 🩺 Predictive Care | 차량 건강 점수, 96셀 전압/온도 히트맵, 3개월 내 부품 다운타임 확률 예측 리포트 |
| 👨‍👩‍👧 Family Care | 나이별 음성 콘텐츠, 여정 진행 지도, 음성 놀이(끝말잇기·스무고개·지역 퀴즈), 동승자 맞춤 휴식 추천 → 대답하면 내비에 경유지 추가 |

---

## 1. 실행 방법

### APK로 바로 실행
1. [Releases](https://github.com/minjongg7/car-application/releases/tag/naviya)에서 `차량용 앱_v0.6.0.apk`를 받습니다.
2. Pleos Connect 에뮬레이터를 켠 뒤 APK 파일을 에뮬레이터 창에 끌어다 놓거나 아래 명령으로 설치합니다.
   ```bash
   adb install "차량용 앱_v0.6.0.apk"
   ```
3. 앱 목록에서 **FatigueGuard Pro**를 실행합니다.

### 소스로 빌드해서 실행
1. 저장소를 내려받습니다.
   ```bash
   git clone https://github.com/minjongg7/car-application.git
   ```
2. Android Studio → **File › Open** → 내려받은 폴더 선택 → Gradle Sync
3. 실행 구성 `app`, 기기 **Pleos Connect v2.1.0** 선택 → ▶ Run

### 시연 순서 (에뮬레이터)
에뮬레이터에는 실제 주행이 없으므로 아래 시연 기능을 씁니다.
- **내비**: 목적지(예: 수원 화성행궁) 선택 → `시뮬레이션` 버튼으로 경로를 따라 가상 주행
- **Family Care › 🗺 여정 지도**: `🎬 시연 여정 (서울역 → 부산역)` → 약 3분 동안 남은 거리 감소·휴게소 스탬프·휴식 질문 재현
- **Family Care › ☕ 휴식 추천**: `시연: 주행 +30분` → "다음 휴게소에서 쉬어갈까요?" 바로 확인

---

## 2. 테스트 환경 및 필요한 설정

### 개발 환경
| 항목 | 버전 |
|---|---|
| Android Studio | Rabbit 2 (2026.2.2 Patch 1) |
| Gradle / AGP | 8.10.2 / 8.8.2 |
| Kotlin | 2.0.0 (Jetpack Compose) |
| Gradle JDK | JDK 21 (Android Studio가 물어보면 **Use JVM 21** 선택) |
| minSdk / targetSdk / compileSdk | 28 / 35 / 35 |

### 사용한 Pleos SDK와 권한
| SDK | 버전 | 권한 (모두 normal) |
|---|---|---|
| Vehicle | 2.3.2 | `CAR_ENERGY` `CAR_INFO` `CAR_DRIVE` `CAR_SAFETY` |
| NaviHelper | 3.0.5 | `NAVI_ROUTE` `NAVI_ROUTE_SEARCH` |
| TextToSpeech | 3.3.1 (ON_DEVICE) | `TTS_SERVICE` |
| SpeechToText | 3.3.1 (ON_DEVICE) | `STT_SERVICE` |

SDK는 `settings.gradle.kts`에 등록된 Pleos Maven 저장소(`https://nexus-playground.pleos.ai/repository/maven-releases/`)에서 받습니다.

### 필요한 설정
1. **CRN 주입**: Pleos Playground › My Project › Test Info의 CRN을 에뮬레이터에 주입해야 앱이 실행됩니다.
2. **차량 속도 권한**(피로도 계산용, 런타임 권한): 앱 첫 실행 때 팝업에서 허용하거나 아래 명령을 실행합니다.
   ```bash
   adb shell pm grant --user 10 com.fatigueguard.pro android.car.permission.CAR_SPEED
   ```
3. **마이크**(음성 놀이·휴식 질문 대답): 에뮬레이터 Extended controls › Microphone에서 호스트 오디오 입력을 켭니다.
4. **위치**: 첫 실행 때 위치 권한을 허용합니다. 허용하지 않으면 시뮬레이션 주행으로 안내합니다.
5. **인터넷**: 내장 지도·경로·검색(OpenStreetMap·OSRM·Photon)에 필요합니다.
6. **한글 폴더 경로**: 프로젝트 폴더 이름에 한글이 있으면 Windows에서 빌드가 막힐 수 있어 `gradle.properties`에 `android.overridePathCheck=true`를 넣어 두었습니다. 영문 경로라면 지워도 됩니다.

> Pleos 차량이 아닌 기기(휴대폰·일반 에뮬레이터)에서는 차량 값 대신 데모 데이터로 동작하며, 화면 오른쪽 위에 `데모 데이터`로 표시됩니다.
> 셀 단위 BMS·V2G 시세·휴게소 편의시설(수유실·놀이방)은 공개 SDK에 없어 시연용 데이터입니다.

---

## 3. 개발에 사용한 Pleos Connect 에뮬레이터 버전과 화면 규격

| 항목 | 값 |
|---|---|
| 에뮬레이터 | **Pleos Connect Emulator v2.1.0** (Android Automotive, API 34) |
| 화면 해상도 | **2560 × 1440** (가로형) |
| 앱 표시 영역 | 왼쪽 클러스터와 아래 독을 뺀 오른쪽 영역, 약 1700 × 1300 |
| 화면 방향 | 가로(Landscape) 고정 기준으로 설계 |

---

## 4. APK 파일 위치

- **GitHub Release**: https://github.com/minjongg7/car-application/releases/tag/naviya
- **직접 빌드 시**: `app/build/outputs/apk/debug/app-debug.apk`
  (Android Studio › Build › Assemble 'app' Run Configuration)

---

## 프로젝트 구조
| 모듈 | 내용 |
|---|---|
| `app` | 내비게이션 · 피로도 가드 |
| `core` | 차량 신호 허브 · 공용 UI · 여정 공유(TripBridge) |
| `feature-family` | Family Care |
| `feature-energy` | Energy Hub |
| `feature-care` | Predictive Care |
