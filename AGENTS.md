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
- **Tier list drag & drop** for previously tried distros — bangers inject their signals into your profile then get excluded (find me something similar), trash gets excluded entirely and penalizes similar distros
- Follow-up like/dislike questions after Q2
- **Tier list summary** shown on podium, pick screen, and distro detail (🔥 Bangers + 🗑️ Trash)
- "Why this distro?" explainer on result page
- Comparison table on podium
- Easter egg: typing Linux distro names shows toast notification
- AI questions (Q20-C, Q24-A) trigger fullscreen red jumpscare
- Share result (clipboard copy)
- **Copy answers button** — copies `Q1A;Q2D;Q3C… | 🔥Bangers:… 🗑️Trash:…` format to clipboard
- Random distro button
- Badge wall UI
- "Tweak answers" replays quiz keeping previous answers
- Glitch effect on title hover
- Mobile-responsive with media queries
- **Cookie consent banner** with localStorage opt-in
- **Privacy policy modal** (accessible from footer and cookie banner)
- **Skip-to-content link** for keyboard users
- `aria-expanded` on settings gear, `aria-live` on card region, `tabindex` and `role="button"` on clickable spans
- Escape key closes settings panel and privacy modal
- Inline anti-flash script for cookie banner (prevents FOUC)
- **Ubuntu variants screen removed** — clicking Ubuntu now goes straight to its detail page
- **Focus management after `swap()`** — card gets focus and scrolls into view for screen readers
- **Result button row optimized** — smaller buttons with `white-space:nowrap` to prevent ugly wrapping

### Known Bugs / Edge Cases
1. **Q1 has only one option (A):** "Never used it" — no options for experienced users. The "Already Tried" input compensates.
2. **Brand tag has trailing space:** `'// I.Love.Linux '` — intentional or oversight?
3. **`shareAnswers` uses normal QS array in Arch mode:** Question IDs won't match the absurd Arch questions. Minor, Arch mode is a joke anyway.
4. **Cookie banner inline script uses `document.write`:** Works but flagged by linters. Acceptable for a single-file project.
5. **Some interactive elements still lack `tabindex`:** The main start-page links are covered, but dynamic elements inside `swap()` (like the distro detail back-link) may lose `tabindex` after re-render. Low priority.
6. **`swap()` focus management works but `preventScroll: true` may not be respected in all browsers:** Safari sometimes ignores it. Minor UX quirk.
7. **Tier list bangers excluded AND tried penalty:** Bangers get both injected signals AND the -25 `triedDistros` penalty in the dimension algo, which slightly contradicts the "find similar" intent. Working as designed but could be refined.

### Design Decisions
- **Single file by design:** No build step, no dependencies beyond Google Fonts and SimpleIcons CDN. Easy to deploy, share, and fork.
- **Dual-algorithm scoring:** The direct `boost`/`exclude` system gives precise per-question distro targeting (you answer like an Arch user → Arch skyrockets). The old dimension system catches broader personality matches. Merged 5:3 in favour of boosts.
- **Tier list philosophy:** Bangers = "I loved these, find me something similar" (signals injected, distros excluded). Trash = "Never again" (excluded + penalized). This forces the algo to explore new ground instead of re-recommending what you already know.
- **`Math.max` → `+=` for dimensions:** Originally used `Math.max()` which capped dimensions at 3. Changed to `+=` so scores accumulate across questions. Badge thresholds updated accordingly (Terminal Lover: 40, Gamer: 8, Privacy: 18).
- **Arch mode replaces QS entirely:** Uses a parallel `ARCH_QUESTIONS` array with its own inline translations (m/m_fr, s/s_fr). Doesn't use the `t()` system. Always forces `['arch','arch','arch']` on the podium.
- **Both beta features (locks, arch mode) default OFF:** Set in initialization block, overwriting any saved localStorage.
- **Theme system uses CSS variables:** 4 themes defined in `THEMES` object, applied via `setProperty()` on `:root`. Light mode adds `.light-mode` class for hardcoded overrides (because some elements need `!important`).
- **Badge secrets:** 4 badges hidden until earned (linux-detective, ai-abuser, loyalist, indecisive). Their names/descriptions show as `???` on the badge wall.
- **localStorage prefix unified:** All keys now use `ilovelinux-` prefix (badges, theme, lang, locks, archmode, quiz-count, easteregg-count, last-top3, cookies-accepted).
- **Cookie/privacy code wrapped in `DOMContentLoaded`:** Prevents null element errors when script runs before HTML is parsed.
- **Ubuntu variants removed:** The extra DE-choosing screen was unnecessary friction. Users go straight to the Ubuntu detail page.

## What To Work On Next
1. **`shareAnswers` Arch mode support** — use `ARCH_QUESTIONS` IDs when arch mode is active
2. **Persist tier list across retakes** — currently resets on `beginQuiz()`, maybe allow carry-over
3. **Consider reducing the tried penalty for bangers** — the -25 conflicts with signal injection, could be -10 or removed entirely
4. **Add more dynamic `tabindex` injection** — elements inside `swap()` lose their `tabindex` on re-render
5. **French translation audit** — some newer strings may be missing French equivalents

## Communication Style
- The project owner is called **babe**. Address him that way.
- Update this AGENTS.md whenever you make meaningful code changes.
- Use FIND/REPLACE blocks for all code changes.
- Be concise. Don't over-explain.