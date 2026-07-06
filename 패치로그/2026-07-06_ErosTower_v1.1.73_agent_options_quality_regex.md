# Eros Tower v1.1.73 agent options and quality regex patch

## Scope
- Made the `Eros pre-agent 실행 옵션` settings block collapsible and collapsed by default.
- Changed the `Synthesis 보조 실행` disabled option label to `꺼짐(추천)`.
- Strengthened the built-in quality regex fallback for parenthetical cleanup.

## Details
- The pre-agent execution option controls are now inside a closed `<details class="et-section-toggle">`.
- Only the Synthesis option receives the recommended-off label; other boolean controls keep their existing labels.
- Added a fallback rule for empty parentheses `()` / `（）`.
- Expanded Korean-output parenthetical cleanup from single-character Korean prefixes to Korean token prefixes and added Japanese kana support.

## Notes
- This patch does not change runtime agent prompts, provider calls, source retrieval, or state commit.
- Deployment metadata and raw URL verification are tracked in `패치로그/2026-07-06_ErosTower_v1.1.73_release.md`.
- Backup before edit: `update_repo/작업메모/backup_before_agent_options_quality_regex_20260706_170249`.
