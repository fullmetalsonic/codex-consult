# 코덱스 협의 (codex-consult)

Claude Code용 스킬이에요. Claude와 OpenAI Codex가 같은 일을 놓고 **각자 독립적으로 의견을 내고, 협의해서 방향을 정한 뒤, Codex가 실행하고, 둘이 각자 결과를 검토**하는 과정을 일이 끝날 때까지 반복해요.

```
원자료 패키지 ─┬─> Claude 의견 ─┐
              └─> Codex 의견  ─┴─> 의견 원문 교환 → 합의 → 실행 계획
                                                           │
            ┌──────────────── Codex 실행 (전체 권한, 컴퓨터 유즈 포함) <┘
            │
            ├─> Claude 검토 ─┐
            └─> Codex 검토  ─┴─> 문제 장부 갱신 → 문제가 남으면 다음 라운드
```

## 특징

- **독립성:** 의견과 검토 모두 상대 것을 보기 전에 따로 써요. 진행자인 Claude의 안을 우대하지 않아요.
- **공정한 정보:** Codex에게는 Claude가 가공한 요약이 아니라 원자료를 그대로 넘겨요. 사용자 메시지 원문, 파일 경로, 명령 출력과 로그 원문이에요. Claude의 해석, 가설, 추천은 넣지 않아요.
- **자율 진행:** 사용자에게는 꼭 필요할 때만 물어요.
  - 금지 항목(결제, 외부 발송, 되돌릴 수 없는 삭제, 인증정보 입력, 보안 설정)이 필요할 때
  - 두 안의 차이가 사용자의 목표, 취향, 비용에 관한 것일 때
  - 접근을 바꿔도 계속 막힐 때
- **의견이 갈릴 때:** 둘이 스스로 정해요. 되돌리기 쉬운 안을 먼저 시도하거나, 둘 다 시도해서 결과로 비교하거나, 근거가 더 구체적인 안을 골라요.
- **발전 기반 반복:** 라운드 수가 아니라 "발전 없는 라운드"를 세요. 문제 장부로 재발이나 퇴보를 추적해서, 조금씩만 나아지며 끝없이 도는 것을 막아요.
- **기록:** 모든 자료, 의견, 실행 로그, 검토가 작업 폴더의 `.codex-consult/`에 남아요.

## 필요한 것

- [Claude Code](https://claude.com/claude-code)
- [Codex CLI](https://github.com/openai/codex) (로그인 완료 상태). 0.169에서 테스트했어요.
- 선택: [codex-plugin-cc](https://github.com/openai/codex-plugin-cc) 플러그인. 설치되어 있으면 `/codex:setup`으로 Codex 설정을 확인할 수 있어요.

## 설치

전역 스킬로 설치해요(모든 프로젝트에서 사용).

```bash
git clone https://github.com/fullmetalsonic/codex-consult.git
```

```bash
mkdir -p ~/.claude/skills && cp -r codex-consult/skills/codex-consult ~/.claude/skills/
```

새 Claude Code 세션부터 인식돼요.

## 사용법

- "코덱스 협의로 진행해줘", "코덱스랑 협의해서 이 버그 고쳐줘"
- 또는 `/codex-consult`

말로 진행 방식을 조절할 수 있어요.

| 말 | 효과 |
|---|---|
| "먼저 보고해", "계획부터 보여줘" | 실행 전에 계획을 보고하고 승인을 받아요 |
| "될 때까지", "계속 해" | 막혀도 멈추지 않고 끝날 때까지 진행해요 |

### 기본으로 쓰고 싶다면 (선택)

[examples/CLAUDE.md](examples/CLAUDE.md)의 내용을 `~/.claude/CLAUDE.md`에 추가하세요. 새 작업, 기획, 문제 해결을 시작할 때 자동으로 코덱스 협의를 쓰고, 작은 일은 제외해요.

## 주의

- 실행 단계는 Codex를 `--dangerously-bypass-approvals-and-sandbox`(샌드박스 없음, 승인 없음)로 실행해요. Codex가 PC에서 파일, 셸, 화면 조작을 자유롭게 할 수 있어요. 스킬이 금지 목록을 실행 계획에 넣어 막지만, 이는 지시일 뿐 기술적 차단은 아니에요. 믿을 수 있는 작업과 환경에서만 쓰세요.
- 컴퓨터 유즈는 Codex 쪽 `computer_use` 기능이 켜져 있어야 해요(`codex features list`로 확인). Windows 11에서 메모장 조작으로 동작을 확인했어요.
- 스킬 안의 일부 명령은 Windows Git Bash 기준(`cygpath`)이에요. macOS와 Linux에서는 그 부분을 생략하면 돼요.
