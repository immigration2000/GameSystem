# GameSystem

언리얼 엔진 5.7 기반 **그리드 인벤토리(Grid Inventory) 시스템** 구현 프로젝트입니다.

Third Person 템플릿을 베이스로, 칸(슬롯) 단위로 아이템을 배치·이동·정렬하는
인벤토리 UI와 데이터 구조를 블루프린트로 만들어 나가는 것이 목표입니다.

이 README는 **작업 기록용 문서**입니다.
커밋할 때마다 그 커밋에서 실제로 적용된 내용을 아래 [변경 이력](#변경-이력)에 남깁니다.

---

## 개발 환경

| 항목 | 값 |
| --- | --- |
| 엔진 | Unreal Engine 5.7 |
| 프로젝트 타입 | 블루프린트 전용 (C++ `Source` 없음) |
| 템플릿 | Third Person BP |
| 렌더링 | DX12 / SM6, Lumen, Virtual Shadow Map, Substrate, Ray Tracing |
| 입력 | Enhanced Input |
| 플러그인 | ModelingToolsEditorMode(에디터), GameplayStateTree |
| 시작 레벨 | `/Game/ThirdPerson/Lvl_ThirdPerson` |

### 실행 방법

1. `GameSystem.uproject` 를 더블클릭해 에디터를 엽니다.
2. `Lvl_ThirdPerson` 레벨에서 **Play(PIE)** 를 실행합니다.
3. `Tab` 키로 인벤토리를 열고 닫습니다.

---

## 프로젝트 구조

```
Content/
├─ Asset/                          아이템 아이콘 텍스처 (T_item__1_ ~ T_item__8_)
├─ Input/
│  ├─ IMC_Default                  기본 입력 매핑 컨텍스트
│  ├─ IMC_MouseLook                마우스 시점 매핑 컨텍스트
│  └─ Actions/                     IA_Move, IA_Look, IA_MouseLook, IA_Jump, IA_OpenInventory
├─ ThirdPerson/
│  ├─ Lvl_ThirdPerson              테스트용 플레이 레벨
│  └─ Blueprints/
│     ├─ BP_ThirdPersonGameMode
│     ├─ BP_ThirdPersonCharacter
│     ├─ BP_ThirdPersonPlayerController   입력 처리 + 인벤토리 UI 토글
│     └─ UMG/
│        ├─ WBP_Inventory          인벤토리 창 (슬롯 컨테이너)
│        └─ WBP_ItemSlot           개별 아이템 슬롯 위젯
├─ Characters/Mannequins/          기본 마네킹 메시 · 애님 (템플릿 에셋)
└─ LevelPrototyping/               프로토타입용 메시 · 머티리얼 (템플릿 에셋)
```

---

## 입력 매핑 (IMC_Default)

| 액션 | 키보드 | 게임패드 |
| --- | --- | --- |
| `IA_Move` | W / A / S / D | 왼쪽 스틱 |
| `IA_Look` | — | 오른쪽 스틱 |
| `IA_MouseLook` | 마우스 | — |
| `IA_Jump` | Space | A 버튼 |
| `IA_OpenInventory` | **Tab** | — |

---

## 구현 현황

### 완료

- [x] Enhanced Input 기반 이동 / 점프 / 시점 조작 (플레이어 컨트롤러에서 처리)
- [x] 모바일 플랫폼 감지 시 터치 컨트롤 위젯(`UI_TouchSimple`) 자동 부착
- [x] `IA_OpenInventory`(Tab) 입력으로 인벤토리 UI 열기 / 닫기 토글
- [x] 인벤토리 열림 상태에 따른 입력 모드 전환 (Game Only ↔ Game And UI) 및 마우스 커서 표시
- [x] `WBP_Inventory` — 인벤토리 창 레이아웃, 슬롯 위젯을 런타임에 생성해 Add Child 로 붙이는 구조
- [x] `WBP_Inventory` — 생성한 슬롯을 `AllWidgets` 배열에 보관
- [x] `WBP_ItemSlot` — 아이콘(Image) + 수량/이름(TextBlock) 구성의 슬롯 위젯
- [x] `WBP_ItemSlot` — 버튼 Pressed / Released 이벤트로 `bIsDrag` 플래그 갱신 (드래그 입력 감지의 기반)
- [x] 아이템 아이콘 텍스처 8종 임포트

### 진행 중 / 예정

- [ ] 아이템 데이터 구조 정의 (Struct / DataTable — ID, 이름, 아이콘, 스택 최대치, 차지 칸 수)
- [ ] 그리드 좌표계 (Row / Column) 기반 슬롯 인덱싱
- [ ] 1×1 을 넘는 **다중 칸 아이템** 배치 및 충돌 판정
- [ ] 슬롯 간 드래그 앤 드롭으로 아이템 이동 / 교환
- [ ] 아이템 스택(수량 합치기 · 나누기)
- [ ] 인벤토리 컴포넌트 분리 (UI와 데이터 로직 분리)
- [ ] 월드 아이템 픽업 액터 및 획득 처리
- [ ] 아이템 버리기(Drop) 처리
- [ ] 인벤토리 저장 / 불러오기

---

## 변경 이력

> 커밋할 때마다, 그 커밋에서 **실제로 적용한 내용**만 여기에 추가합니다.
> 형식: `### YYYY-MM-DD — <커밋 요약> (<커밋 해시>)` 아래에 변경 항목을 나열합니다.
> 최신 항목이 위로 오도록 작성합니다.

### 2026-07-29 — 초기 설정 (`2ef30d7`)

- Unreal Engine 5.7 Third Person 템플릿으로 프로젝트 생성 및 Git 저장소 초기화
- 언리얼용 `.gitignore` 추가 (`Saved/`, `Intermediate/`, `DerivedDataCache/`, `Binaries/` 제외)
- 인벤토리 UI 위젯 2종 추가
  - `WBP_Inventory` — 인벤토리 창. 슬롯 위젯을 런타임 생성해 컨테이너에 붙이고 `AllWidgets` 배열에 보관
  - `WBP_ItemSlot` — 아이콘 + 텍스트로 구성된 슬롯 위젯. 버튼 Pressed/Released 로 `bIsDrag` 갱신
- `IA_OpenInventory` 인풋 액션 추가, `IMC_Default` 에 **Tab** 키로 매핑
- `BP_ThirdPersonPlayerController` 에 인벤토리 토글 로직 추가
  - BeginPlay 에서 `WBP_Inventory` 생성 후 플레이어 스크린에 추가
  - Tab 입력 시 UI 표시/숨김 전환, 입력 모드(Game Only ↔ Game And UI)와 마우스 커서 상태 전환
- 아이템 아이콘 텍스처 8종(`T_item__1_` ~ `T_item__8_`) 임포트

---

## 커밋 규칙

- 한 커밋에는 하나의 기능 단위만 담습니다.
- 커밋 메시지는 한국어로, 무엇을 했는지 한 줄로 적습니다. (예: `슬롯 드래그 앤 드롭 이동 구현`)
- 커밋 직후 위 [변경 이력](#변경-이력)에 해당 항목을 추가하고, [구현 현황](#구현-현황)의 체크박스를 갱신합니다.

### 블루프린트 프로젝트 주의사항

`.uasset` / `.umap` 은 **바이너리 파일이라 Git 이 자동 병합하지 못합니다.**
같은 에셋을 여러 곳에서 동시에 수정하면 한쪽 작업을 버려야 하므로,
작업 전 `git pull` 로 최신 상태를 받고 작업 후 바로 커밋·푸시하는 것을 권장합니다.

### Git LFS

에셋 파일은 **Git LFS** 로 관리합니다. 대상 확장자는 `.gitattributes` 에 정의돼 있습니다
(`.uasset`, `.umap`, 이미지 · 모델 · 오디오 · 영상 등).

저장소를 새로 클론하는 PC에서는 **처음 한 번** 아래를 실행해야 에셋이 정상적으로 받아집니다.

```bash
git lfs install
```

설치하지 않고 클론하면 `.uasset` 파일이 실제 데이터 대신 몇 줄짜리 포인터 텍스트로 받아져
에디터에서 에셋이 깨져 보입니다. 이 경우 `git lfs install` 후 `git lfs pull` 로 복구합니다.

추적할 확장자를 추가하려면:

```bash
git lfs track "*.확장자"
```
