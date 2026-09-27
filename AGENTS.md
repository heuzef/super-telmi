# Instructions for AI agents working on this repository

## What this project is

Super Telmi is an Open-Source storytelling box for children, based on the free
[Telmi](https://telmi.fr) system and built on a Miyoo Mini Plus handheld.

This repository contains **documentation and 3D files only**. There is no source
code, no build step, no test suite. The deliverable is prose that people follow
while soldering and printing real hardware, so accuracy matters more than speed.

## Layout

| Path | Role |
| --- | --- |
| `diy/DIY_FR.md` | Build guide in French — **the source of truth** |
| `diy/DIY_EN.md`, `diy/DIY_ES.md`, `diy/DIY_ZH.md` | Translations of the French guide |
| `diy/assets/` | Images shared by all four guides |
| `files/` | 3D files: CAD sources, `.3mf` models, ready-to-print exports |
| `README.md` | Project presentation, one collapsible section per language |

## Language rules

**French is the source.** Write or fix content in `diy/DIY_FR.md` first, then
mirror it into the other three guides **within the same change** — never leave a
guide behind. The same applies to the four language sections of `README.md`.

**Keep the four guides structurally identical.** Same number of lines, and
headings, images, list items and block quotes on the same line numbers in every
file. This makes a change comparable across languages at a glance, and it is
verified by the checks below. Translate the text of a paragraph; never merge,
split, reorder or drop one.

**Never translate:** URLs (AliExpress, telmi.fr, Discord, YouTube), image paths
under `assets/`, the silkscreen label `SPK1`, screw sizes `M2` / `M3`, the
material names `TPU` / `3MF`, units (`mm`, `g`, `W`, `ohm`), and the product
names `Miyoo`, `Miyoo Mini Plus`, `Telmi`, `Super Telmi`.

**Units are normalised** across all languages: `40 mm`, `8 ohms`, `3 W`, `250 g`,
`10x3 mm`, `20 cm`. A space between number and unit; `%` follows each language's
own typography (`25 %` in French and Spanish, `25%` in English and Chinese).

**Register per language:** French uses *vouvoiement*, Spanish uses *tú*, Chinese
uses 您, English stays neutral. Chinese is Simplified (zh-Hans).

**The HTML comment at the top of each guide** is written in that guide's own
language and lists the other three files. Update it when adding a language.

## README structure

Four `<details>` blocks in the order FR, EN, ES, ZH. Only the French one carries
the `open` attribute. The flag lives in the `<summary>` as an `<h3>`; the flag
alone signals the language, so do not add "choose your language" prose. Each
section is a full translation of the same content, with the same headings.

## Adding a new language

1. Copy the structure of `diy/DIY_FR.md` into `diy/DIY_<CODE>.md` and translate
   it line for line, keeping the layout identical.
2. Add a `<details>` block to `README.md`, after the existing ones.
3. Update the header comment and the `diy/` table row of **every** existing
   guide and README section to list the new language.
4. Run the checks below.

Translations produced by an agent have not been reviewed by a native speaker.
Say so when handing the work over, and suggest a review on the community Discord
before the change is advertised.

## Checks to run before committing

```sh
# Every referenced image exists
for f in diy/DIY_*.md; do
  grep -o '(assets/[^)]*)' "$f" | tr -d '()' | while read -r i; do
    [ -f "diy/$i" ] || echo "MISSING image: $i (in $f)"
  done
done

# The four guides stay structurally aligned with the French source
for f in diy/DIY_EN.md diy/DIY_ES.md diy/DIY_ZH.md; do
  diff -q <(grep -n '^#\|!\[\]\|^> \|^\* ' diy/DIY_FR.md | cut -d: -f1) \
          <(grep -n '^#\|!\[\]\|^> \|^\* ' "$f"          | cut -d: -f1) \
    >/dev/null || echo "STRUCTURE drift: $f vs diy/DIY_FR.md"
done

# Every relative link in the README points at something real
grep -oE '\]\(\./[^)]*\)' README.md | tr -d '](' | sed 's/)$//' | sort -u |
  while read -r p; do [ -e "$p" ] || echo "MISSING path: $p"; done

# The README's collapsible sections are balanced
[ "$(grep -c '<details' README.md)" = "$(grep -c '</details>' README.md)" ] ||
  echo "UNBALANCED <details> in README.md"
```

## Conventions

* Commit messages are written in English, one commit per coherent change.
* Do not push unless asked.
* Prose in a guide is written in that guide's language; so is its HTML header
  comment. This file and commit messages are the exceptions — English.
* Images live in `diy/assets/` and are shared; `assets/nopreview.png` is the
  placeholder for a step that still has no photograph.
