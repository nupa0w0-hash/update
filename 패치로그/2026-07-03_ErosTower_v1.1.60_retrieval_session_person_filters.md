# Eros Tower v1.1.60 - Retrieval / Session Person Filters

## Status
- Deployment requested after local validation.
- Main update files prepared for GitHub raw deployment.

## Changed
- Updated local plugin display/internal version to `1.1.60`.
- Prevented lore heading and institution labels from being treated as character candidates:
  - `주인공들`
  - `인물정보`
  - `조정`
  - multilingual equivalents such as `Character Information`, `朝廷`, `人物情報`
- Existing polluted character-state entries are now filtered out during retrieval.
- Psyche units marked as `character` are retyped to `lore` when their name is clearly a heading, aggregate title, or institution label.
- Blocked session-born person extraction fragments from becoming memory/person entries:
  - possessive sect/institution names such as `청허문의`
  - pronoun-particle fragments such as `그분은`
  - descriptive CJK clauses such as `同一年入门的`
- Kept concrete person names valid in Korean, Chinese, Japanese, and English.

## Verification
- `node --check ErosTower.v1.update.js`
- `node --check ErosTower.update.js`
- `node --check "☸에로스 타워.js"`
- `git diff --check`
- `Select-String -Path .\ErosTower.v1.update.js,.\ErosTower.update.js,".\☸에로스 타워.js" -Pattern '^//@(display-name|version|update-url)'`
- compat body sync check: `BODY_SYNC_OK`
- UTF-8 BOM check: first bytes start with `2F 2F 40`
- direct install file SHA256 matches v1 update file SHA256
- VM reproduction:
  - blocked `주인공들`, `인물정보`, `조정`, `Character Information`, `朝廷`, `人物情報`
  - allowed `연수`, `묵야`, `南宫墨夜`, `李霜`, `アヤ`, `Alice`
  - blocked `청허문의`, `그분은`, `同一年入门的`
  - allowed `현진`, `명선`, `明善`, `Allen`

## Backups
- `D:\리수작업\에로스 타워\백업\ErosTower_before_lore_heading_character_filter_20260703-082333`
- `D:\리수작업\에로스 타워\백업\ErosTower_before_session_person_fragment_filter_20260703-085821`
- `D:\리수작업\에로스 타워\백업\ErosTower_before_version_1_1_60_20260703-090000`

## Raw URL confirmation
- Checked after push at `2026-07-03 09:13:56 +09:00`.
- `https://raw.githubusercontent.com/nupa0w0-hash/update/main/ErosTower.v1.update.js`
  - `//@display-name ☸Eros Tower 1.1.60`
  - `//@version 1.1.60`
- `https://raw.githubusercontent.com/nupa0w0-hash/update/main/ErosTower.update.js`
  - `//@display-name ☸Eros Tower 1.1.60`
  - `//@version 4.0.12`
