# 클로덱스 루프 PC 설치 가이드 (클로드 + GPT 한 팀)

출처: github.com/chaseai-yt/claudex-loop (무료, MIT, 제작 Chase AI)
하는 일: 클로드가 계획과 코드를 쓰고, GPT(코덱스)가 검토합니다. 수정과 재검토를 반복한 뒤 통과하면 구현하고, 최종 검사까지 합니다.
비용: 스킬은 무료입니다. 두 모델의 사용량은 각 계정(클로드 구독, ChatGPT 구독)에서 빠집니다.

> 명령어가 바뀌었을 수 있습니다. 안 되면 먼저 저장소의 README를 확인하세요.

---

## 0단계. 터미널 여는 법
- **맥**: `Cmd + Space` → "터미널" 입력 → Enter
- **윈도우**: 시작 메뉴 → "PowerShell" 입력 → Enter

## 1단계. 준비물 확인 (터미널에서 한 줄씩)

| 확인 | 명령어 | 정상 결과 |
|---|---|---|
| Node.js | `node --version` | v18 이상 |
| 파이썬 | 맥 `python3 --version` / 윈도우 `python --version` | 3.10 이상 |
| 클로드코드 | `claude --version` → `claude auth status` | 버전 표시, 로그인됨 |

**없는 것이 있으면 설치하세요.**
- Node.js: https://nodejs.org 에서 **LTS** 버전을 받아 설치합니다.
- 파이썬: https://python.org/downloads 에서 받습니다. 윈도우는 설치 첫 화면에서 **"Add python.exe to PATH" 체크 필수**입니다.
- 클로드코드: `npm install -g @anthropic-ai/claude-code` → `claude` 실행 후 로그인합니다.

설치한 뒤에는 **터미널을 껐다 켜야** 명령어가 인식됩니다.

## 2단계. 코덱스(GPT) 설치 + 로그인 (클로드코드 밖, 일반 터미널)

```
npm install -g @openai/codex
codex --version        # 0.130 이상이어야 함
codex login            # 브라우저가 열리면 ChatGPT 계정으로 로그인
codex login status     # 로그인 확인
```

"Successfully logged in"이 뜨면 성공입니다.
- 맥에서 `permission denied`가 나오면 `sudo npm install -g @openai/codex`로 다시 설치합니다(맥 로그인 비밀번호 입력).

## 3단계. 플러그인 설치 (클로드코드 안)

`claude`를 실행한 뒤 아래 두 줄을 **한 줄씩 따로** 입력합니다.

```
/plugin marketplace add chaseai-yt/claudex-loop
```
```
/plugin install claudex-loop@claudex-loop
```

**설치 확인**: `/plugin`을 입력하고 Marketplaces 탭을 엽니다. `claudex-loop` 줄에 **"1 installed"**가 보이면 성공입니다.
"1 available"만 보이면 Discover 탭에서 `claudex-loop`를 찾아 Install을 직접 누르세요.

**반드시 재시작**: `/exit` → `claude`. 재시작하지 않으면 "Unknown command"가 나옵니다.

## 4단계. 첫 테스트

1. **빈 폴더에서 시작합니다.** 폴더 안의 파일을 전부 읽기 때문입니다.
   ```
   mkdir claudex-test
   cd claudex-test
   claude
   ```
2. **auto mode를 끕니다.** 화면 아래에 "auto mode on"이 보이면 `Shift+Tab`으로 끕니다. 켜져 있으면 코덱스 실행이 막힙니다.
3. **실행합니다.**
   ```
   /claudex-loop:claudex-loop 할 일 적고 체크할 수 있는 투두 웹페이지 만들어줘. 새로고침해도 목록이 안 사라지게 rounds=2
   ```
4. **권한 질문이 나오면** `Yes` 또는 "Yes, and always allow access to (폴더)"를 고릅니다.
   **"switch to auto mode"는 고르지 마세요.** 고르면 다시 막힙니다.
5. 끝나면 결과 파일 두 개가 남습니다.
   - `PLAN.md`: 무엇을 만들기로 했는지
   - `PLAN-REVIEW-LOG.md`: 라운드별 지적 사항과 처리 내용, 실제로 돌아간 모델

처음에는 `rounds=2`로 작게 돌려 보세요. 붙이지 않으면 기본 5회 반복이라 시간과 사용량이 많이 듭니다. 예시 작업은 약 25분 걸렸습니다.

## 자주 쓰는 옵션 (작업 내용 뒤에 띄어쓰기로)

| 옵션 | 효과 |
|---|---|
| `rounds=2` | 검토 왕복을 2번까지만 |
| `builder=codex` | GPT가 만들고 클로드가 검사 |
| `mode=review plan=docs/plan.md` | 이미 써 둔 계획 파일만 검토 |
| `reviewer_model=...` / `builder_model=...` / `inspector_model=...` | 역할별 모델 지정 |

기본 모델은 클로드 쪽은 `/model`로, 코덱스 쪽은 `~/.codex/config.toml`의 `model =` 줄로 정해집니다.

## 문제 해결

| 증상 | 해결 |
|---|---|
| `npm: command not found` | Node.js 설치 → 터미널 재시작 |
| "Unknown command" | `/exit` 후 `claude`로 재시작 |
| 코덱스 실행이 막힘 | auto mode 끄기(`Shift+Tab`) |
| 윈도우에서 "blocked by policy" | 클로드에게 "우회책 찾아서 계속 진행해"라고 요청. 그래도 안 되면 `C:\Users\내이름\.codex\config.toml`에 아래 두 줄 추가 |
| 빌드 중 다른 플러그인 훅과 충돌 | 그 플러그인을 잠시 끄기 |

```toml
[windows]
sandbox = "elevated"
```

## 알아둘 것
- 지적이 0개여도 정상입니다. 지적 개수는 품질 점수가 아닙니다.
- `PLAN.md`를 손으로 고치면 이전 승인은 무효가 됩니다. 검토를 다시 돌려야 합니다.
- 다른 회사 AI가 검토하면 놓친 부분을 더 잘 잡아내지만, 전부 잡아 주지는 않습니다.

## 더 가벼운 대안: OpenAI 공식 코덱스 플러그인
코드 검토나 개별 작업만 코덱스에 맡기고 싶을 때 씁니다.
```
/plugin marketplace add openai/codex-plugin-cc
/plugin install codex@openai-codex
/reload-plugins
/codex:setup
```
