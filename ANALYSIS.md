# Project Analysis: کاروان اسلام (Karavane Eslam)

## Overview

An **mdBook** (Rust-based static book generator) project containing **"کاروان اسلام" (Karavan-e Eslam / The Caravan of Islam)** by **صادق هدایت (Sadegh Hedayat)**, one of Iran's most prominent modern writers.

The book is a **satirical short story** written around 1312–1313 SH (1933–1934), presented as translated reports from a journalist accompanying a group of Islamic missionaries sent to convert Europeans.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Build tool | **mdBook v0.5.2** |
| Language | Persian (Farsi), RTL layout |
| Theme | Custom CSS (Vazir font, RTL fixes, footnote fixes) |
| CI/CD | GitHub Actions → GitHub Pages |
| Version control | Git, single branch `main`, 1 commit |
| Editor | Obsidian (`.obsidian/` config present) |

---

## Project Structure

```
karavan/
├── .github/workflows/deploy.yml   # GitHub Actions: mdBook build → GitHub Pages
├── .obsidian/                      # Obsidian vault config
├── book.toml                       # mdBook config (title, RTL, fa, rust theme, custom CSS)
├── book/                           # Built output (HTML, JS, CSS) — gitignored but committed
├── src/
│   ├── SUMMARY.md                  # Book table of contents
│   ├── title.md                    # Title page: کاروان اسلام
│   ├── about.md                    # Publisher's preface (2025 internet edition)
│   ├── intro.md                    # Story introduction
│   ├── 1.md                        # نامه اول (First Letter) — ~240 lines
│   ├── 2.md                        # نامه دوم (Second Letter) — ~165 lines
│   ├── 3.md                        # نامه سوم (Third Letter) — ~169 lines
│   ├── hashiye.md                  # حاشیه (Marginalia/publisher notes) — ~124 lines
│   ├── image.md                    # Image gallery (references images/ dir)
│   ├── notes.md                    # یادداشت‌ها (Annotations/glossary) — ~265 lines
│   └── afsaneye_afarinesh.md       # افسانه آفرینش (Creation Myth) — ~468 lines, 3-act play
├── theme/
│   └── custom.css                  # RTL + Vazir font + footnote styling
├── .gitignore                      # Ignores book/ and .obsidian/
└── README.md
```

---

## Content Summary

**Main work** — *The Caravan of Islam*: A satirical story in 3 "letters" by reporter الجرجیس یافث بن اسحق الیسوعی, documenting a group of so-called Islamic missionaries departing from Samarra to convert "infidel" Europeans. The missionaries (Taj al-Mutakallimin, Andleb al-Islam, Sekan al-Shari'a, Sant al-Aqtab) are hypocritical, greedy, and absurd. By Letter 3 (set in Paris), the caravan has dissolved — Sekan stole the funds, and the others are running bars and croupier jobs. The satire targets religious hypocrisy and fanaticism.

**附录** — *افسانه آفرینش (The Legend of Creation)*: A 3-pact puppet play (خیمه شب بازی) satirizing the Genesis creation story with Ottoman-style characters (خالق اف, جبراییل پاشا, etc.).

**Notes** — Detailed Persian/Arabic glossary and cultural annotations.

---

## Issues Found

1. **`theme/fonts/` directory missing** — `custom.css` references `fonts/Vazir-Regular.woff2` and `fonts/Vazir-Bold.woff2` but no `theme/fonts/` directory exists. Font won't load → falls back to system sans-serif.

2. **`book/` is gitignored but committed** — `.gitignore` lists `book` but the `book/` directory with built HTML is tracked (committed). The ignore was likely added after the initial commit. This means built artifacts are version-controlled alongside source.

3. **Broken image path in `image.md`** — Line 36: `![vajebi](vajebi.png)` is missing the `images/` prefix (all other images use `images/filename.png`). This image will fail to render.

4. **GitHub token exposed in git remote** — The remote URL contains a plaintext PAT (`ghp_...`). This is a security issue — should use credential helpers or SSH instead.

5. **`notes.md` not linked in SUMMARY.md** — The `notes.md` (یادداشت‌ها/annotations) file exists but is not included in the book's table of contents, making it invisible to readers.

6. **Single git commit** — Only 1 commit ("Initial commit") with all content. No development history.

7. **mdBook version pinned to v0.5.2** in CI — The latest mdBook release may have newer features or fixes. Worth checking if an upgrade is desired.

---

## Recommendations

### Priority Fixes

| # | Issue | Fix |
|---|---|---|
| 1 | Missing Vazir fonts | Add `theme/fonts/Vazir-Regular.woff2` and `Vazir-Bold.woff2` files |
| 2 | Exposed GitHub token | Switch remote to SSH or use `git credential-helper`; rotate the token |
| 3 | Broken image path | Change `vajebi.png` → `images/vajebi.png` in `image.md` line 36 |
| 4 | Orphaned `notes.md` | Add `[یادداشت‌ها](./notes.md)` entry to `SUMMARY.md` |

### Improvements

- Remove `book/` from git tracking: `git rm -r --cached book/`
- Consider adding the `images/` directory to the `src/` tree if not already present
- Add a `CHANGELOG.md` for tracking editions
- Pin mdBook version in CI or use a setup action for easier upgrades
