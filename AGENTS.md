# AGENTS.md — I Love Linux (find-your-linux)

## Project Overview
Single-file HTML quiz app (~4000+ lines) that matches users to Linux distributions. Originally called DistroMatch, renamed to "I Love Linux" / Find Your Linux. Lives at `slyazer/find-your-linux` on GitHub.

**Tech stack:** Vanilla HTML/CSS/JS, no frameworks, no build step. 4 themes (Space, Light, Catppuccin, Dracula), EN/FR translations, localStorage persistence.

## How to make changes
- **Always use FIND/REPLACE blocks.** Never rewrite the full file.
- Use enough context in FIND to uniquely match (5-10 lines minimum).
- Test both English and French after any translation change.
- Test both normal mode and Arch mode after any QS/logic change.
- Hard refresh (Ctrl+Shift+R) after every change — browser caches aggressively.

## Current State (as of June 2026)

### Working
- 25-question quiz across 5 themed parts (Ease & Stability, Setup & Workflow, Privacy & Security, Custom & CLI, Gaming & Beauty)
- 30-distro database with scoring profiles, logos, descriptions, tags
- **Dual-algorithm scoring engine** — `computeDimTopThree` (dimension dot product + signal matching) and `computeBoostTopThree` (direct `boost`/`exclude`/`penalise` per option), merged via `mergePodiums` (boost weighted 5:3 over dimensions)
- Every question option has `boost` (comma-separated distro keys), `exclude` (hard gate removing distros from candidates), and `penalise` (negative scoring) fields for precise per-answer targeting
- Q14 systemd hard gate: option D excludes all systemd distros and boosts only non-systemd ones (Artix, Void, Gentoo, Alpine, Slackware)
- Podium reveal with scan bar, confetti, particle effects, animated cards
- 28 achievement badges with localStorage persistence (4 secret)
- Profile Locks system (gaming/security/lightweight) — toggleable in settings
- "I use Arch btw" meme mode — replaces all 25 questions with absurd ones, always matches Arch, changes brand/footer text
- Tier list drag & drop for previously tried distros
- Follow-up like/dislike questions after Q2
- Ubuntu variants screen
- "Why this distro?" explainer on result page
- Comparison table on podium
- Easter egg: typing Linux distro names shows toast notification
- AI questions (Q20-C, Q24-A) trigger fullscreen red jumpscare
- Share result (clipboard copy)
- **Copy answers button** — copies `Q1A;Q2D;Q3C…` format to clipboard
- Random distro button
- Badge wall UI
- "Tweak answers" replays quiz keeping previous answers
- Glitch effect on title hover
- Mobile-responsive with media queries
- **Cookie consent banner** with localStorage opt-in
- **Privacy policy modal**
- **Skip-to-content link** for keyboard users
- `aria-expanded` on settings gear, `aria-live` on card region
- Escape key closes settings panel and privacy modal
- Inline anti-flash script for cookie banner (prevents FOUC)

### Known Bugs / Edge Cases
1. **Q1 has only one option (A):** "Never used it" — no options for experienced users. The "Already Tried" input compensates.
2. **Brand tag has trailing space:** `'// I.Love.Linux '` — intentional or oversight?
3. **`shareAnswers` uses normal QS array in Arch mode:** Question IDs won't match the absurd Arch questions. Minor, Arch mode is a joke anyway.
4. **Cookie banner inline script uses `document.write`:** Works but flagged by linters. Acceptable for a single-file project.
5. **Some clickable `<span>`/`<div>` elements lack `tabindex`:** Full keyboard navigation not yet complete (badge wall links, random distro, etc.).
6. **`swap()` doesn't call `scrollTo` or announce changes:** Screen readers may not automatically pick up new question content without manual navigation.
7. **Tier list bangers are excluded from results:** `_excludedDistros` includes both trash and bangers, so loved distros are hidden. Intentional? Probably not.
8. **`_excludedDistros` persists across lock overrides:** Clicking "Show with all distros" doesn't reset it, so tier list exclusions remain permanent until quiz restart.
9. **`recomputeWithoutLightweight()` uses old engine only:** Calls `computeBoostTopThree` but not `computeDimTopThree`, missing the dual-algorithm merge.

### Design Decisions
- **Single file by design:** No build step, no dependencies beyond Google Fonts and SimpleIcons CDN. Easy to deploy, share, and fork.
- **Dual-algorithm scoring:** The direct `boost`/`exclude` system gives precise per-question distro targeting (you answer like an Arch user → Arch skyrockets). The old dimension system catches broader personality matches. Merged 5:3 in favour of boosts.
- **`Math.max` → `+=` for dimensions:** Originally used `Math.max()` which capped dimensions at 3. Changed to `+=` so scores accumulate across questions. Badge thresholds updated accordingly (Terminal Lover: 40, Gamer: 8, Privacy: 18).
- **Arch mode replaces QS entirely:** Uses a parallel `ARCH_QUESTIONS` array with its own inline translations (m/m_fr, s/s_fr). Doesn't use the `t()` system. Always forces `['arch','arch','arch']` on the podium.
- **Both beta features (locks, arch mode) default OFF:** Set in initialization block, overwriting any saved localStorage.
- **Theme system uses CSS variables:** 4 themes defined in `THEMES` object, applied via `setProperty()` on `:root`. Light mode adds `.light-mode` class for hardcoded overrides (because some elements need `!important`).
- **Badge secrets:** 4 badges hidden until earned (linux-detective, ai-abuser, loyalist, indecisive). Their names/descriptions show as `???` on the badge wall.
- **localStorage prefix unified:** All keys now use `ilovelinux-` prefix (badges, theme, lang, locks, archmode, quiz-count, easteregg-count, last-top3, cookies-accepted).
- **Cookie/privacy code wrapped in `DOMContentLoaded`:** Prevents null element errors when script runs before HTML is parsed.

## What To Work On Next
1. **Fix tier list bangers being excluded** — only trash should be excluded, bangers should stay in candidate pool
2. **Reset `_excludedDistros` on lock overrides** — so "Show with all distros" actually shows all
3. **Add `tabindex="0"` to clickable spans/divs** — badge wall, random distro, privacy link, etc.
4. **Make `recomputeWithoutLightweight()` use dual-algorithm merge** — currently only calls boost engine
5. **Add `scrollTo` or focus management after `swap()`** — screen reader UX improvement
6. **Consider adding `shareAnswers` support for Arch mode** — use `ARCH_QUESTIONS` when active

## Communication Style
- The project owner is called **babe**. Address him that way.
- Update this AGENTS.md whenever you make meaningful code changes.
- Use FIND/REPLACE blocks for all code changes.
- Be concise. Don't over-explain.
