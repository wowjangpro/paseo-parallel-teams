# paseo-parallel-teams — 설치와 첫 사용 안내

Paseo 에서 병렬 작업을 3계층(메인 세션 = 창구 → 트랙 워커 → 워커 안 이름 있는 agent team)으로
운영하는 절차를 담은 Claude Code **사용자 전역 스킬**입니다. 프로젝트와 무관합니다.

원래 Orca(병렬 작업 도구)용으로 쓰던 `orca-parallel-teams` 스킬의 원칙 · 역할 · 기준 · 흐름을 그대로 두고,
도구 명령(워커 띄우기 · 교신 · 상태 보기 · 종료 · 사용 한도)만 Paseo 의 CLI · MCP 도구로 옮긴 판입니다.

> **시험 운영 전입니다.** 옮긴 뒤 한 번도 돌려 보지 않았습니다. Paseo 도움말과 공개 문서에서 직접 확인한
> 명령만 단정해 적었고, 확인하지 못한 것은 SKILL.md 「미측정」 절(§7)에 모았습니다.
> 특히 「Paseo 가 띄운 Claude 워커가 이름 있는 팀원을 만들고 팀원끼리 SendMessage 를 주고받을 수 있는가」 는
> 문서에 없어서 아직 확인하지 못한 핵심 전제입니다.

## 필요한 것

- Paseo(데스크톱 앱 또는 `paseo` CLI)와 실행 중인 데몬. `paseo --version`, `paseo status` 로 확인합니다.
- 데몬이 도는 기계에 설치되고 로그인된 Claude Code. `paseo provider diagnostic claude` 로 확인합니다.
- 창구 세션이 **Paseo 안에서 연 Claude 에이전트**여야 합니다(워커가 창구의 에이전트 ID 로 메시지를 보냅니다).
- agent team 기능 — 환경변수 `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` 이 워커 세션에 전달돼야 합니다
  (`paseo run --env ...` 또는 공급자 설정의 `env`).
- 선택: Paseo 의 MCP 도구(Settings → Host → Tools & prompts → Enable Paseo tools, 기본 꺼짐).
  셸 접근이 있으면 CLI 만으로도 모든 기능을 쓸 수 있습니다.

## 설치

`paseo-parallel-teams` 폴더를 통째로 대상 PC 의 사용자 전역 스킬 디렉토리에 넣습니다.

- macOS · Linux: `~/.claude/skills/paseo-parallel-teams/`
- 결과 경로: `~/.claude/skills/paseo-parallel-teams/SKILL.md`

특정 프로젝트에서만 쓰려면 그 리포의 `.claude/skills/` 아래에 같은 이름으로 둡니다.
Claude Code 를 새로 띄우면 목록에 잡힙니다. 폴더 안 세 파일은 서로 참조하므로 함께 옮깁니다.

## 처음 쓰기 전에 — 읽기 전용 시험 1회

실제 작업에 쓰기 전에 파일을 바꾸지 않는 시험을 한 번 돌립니다(SKILL.md §0 6번).

1. 창구에서 SKILL.md §0 의 점검(데몬 · 공급자 · 창구 ID · 모드 · 환경변수)을 마칩니다.
2. 연습용 저장소 하나에 worktree 작업 공간 1개와 워커 1명을 만듭니다. 워커 명세는 「파일을 바꾸지 않는다」 로 둡니다.
3. 워커가 이름 있는 팀원 2명(예: `probe-a`, `probe-b`)을 `model: sonnet` 으로 spawn 하게 합니다.
4. 합격 기준을 확인합니다.
   - 팀원 2명이 각자 고유 토큰에 답하고 자기 모델명이 Sonnet 으로 나온다.
   - 팀원끼리 직접 메시지를 한 번 주고받는다.
   - 워커가 창구에 `status` · `ask` 헤더 메시지를 보내면 창구에 도착하고, 창구의 답이 워커에게 돌아간다.
   - 워커가 `worker_done` 헤더 메시지를 보내고, 창구가 종료 절차(SKILL.md §5)로 워커와 작업 공간을 정리한다.
   - 워커의 완료 · 오류 · 권한 요청 알림이 창구에 오는지(CLI 경로 · MCP 경로 각각) 기록한다.
5. 확인된 것은 SKILL.md §7 에서 지우고 본문에 반영합니다. 안 되는 것이 나오면 그 항목의 「규칙으로 메우는 방법」 을 고칩니다.

## 미측정 목록(요약, 정본은 SKILL.md §7)

- 워커 안 이름 있는 팀원 생성과 SendMessage 동작, agent team 환경변수 전달
- `paseo run` 으로 만든 워커의 완료 · 오류 · 권한 요청 알림이 창구에 가는지(MCP 경로만 문서에서 확인)
- 워커 메시지와 알림이 창구 대화에 들어오는 모양, 창구 idle 시 도착 동작, `--no-wait` 없는 `send` 의 동작
- `steer` 지원, `paseo wait` 시간 초과 종료 코드, 첫 지시 길이 한도
- `paseo usage` 가 Claude 의 5시간 · 주간 창을 보여 주는지
- 모델 별칭(`sonnet`) 해석, 창구가 Paseo 밖 터미널일 때의 동작, 팀원 수에 따른 부하와 동시 워커 수 한도

## 함께 볼 것

- 보안 검수 팀원(security)의 역할 · 단계 · 판정은 SKILL.md 「보안 검수」 절이 정본입니다.
  점검 항목은 두 층입니다 — 공통은 이 폴더의 `security-checklist.md`(폴더째 복사하면 함께 옮겨집니다),
  프로젝트마다 다른 것은 각 프로젝트 루트의 `docs/security-review/checklist.md`.
  공통 체크리스트의 근거 문서(법령 · 인증기준)는 국내 서비스 기준이고 조문 대조 전이라,
  다른 조직은 프로젝트 체크리스트에서 근거를 바꿔 쓰고 심사 증적에 조문 번호를 바로 인용하지 않습니다.
- 브라우저 검증 도구는 **사용자·프로젝트가 지정한 것**을 씁니다(SKILL.md §2 마지막에서 두 번째 행).
  대상 PC 의 사용자 전역 `CLAUDE.md` 에 그 지정이 있는지 먼저 확인하세요. Paseo 내장 브라우저는 기본 꺼져 있고,
  지정 도구가 있으면 그것을 대신하지 않습니다.
- 판정 술어(§6)는 도구별 문자열에 의존합니다. 다른 빌드 도구를 쓰면 「강제 실행의 증거 문자열」을
  그 도구 기준으로 먼저 확인한 뒤 세십시오.
- Paseo 자체 사용법은 `paseo --help`, 공개 문서 색인 `https://paseo.sh/llms.txt`, 설치돼 있으면 `paseo` · `paseo-help` 스킬을 봅니다.
