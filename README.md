# RADAR, offline HTML tool

One file, `index.html`, that a group opens from a stick or a mail attachment and works
through in the 35 minutes of the Breakout Sessions. It holds the six fields of the
paper sheet on five screens, keeps the June vocabulary as chips rather than free text,
and produces the Interventionsbrief as PDF and as JSON at the end.

Nothing here reaches the network. Nothing is written to the device. See `PRIVACY.md`,
which is the long form of the note the tool itself carries at the foot of every screen.

## V1.14 status: three decluttering rounds, a full-tool audit at three widths, and an overseer gate

Three rounds on 2026-09-18, all after Eugen's escalating verdicts on the screens
("crowded", "un-readable", "not professional", then "un-clutter the screens" and "one
hyper thorough round"). The tool bumped to `DATA.build.version = "1.14"` in round 3.
Full record in `04 internal/02 qa/RELEASE_NOTE_V1.14.md`; the short version:

- **Carry band** ("Bisher entschieden") is a `<details>` disclosure, collapsed by
  default past six items, single column, no truncation.
- **Screen 3**: a 150px dead gap in the link map (SVG height never set in the
  nothing-chosen state) is fixed at the root; the three chiprow headers are two lines
  each instead of three.
- **Brief chain**: arrows travel with the node they point at, so no wrapped line ends
  on a dangling arrow at any width; nodes on a row share a height; on paper a key never
  separates from its value.
- **Progress line** carries only the open-field count; the step number lives on the
  eyebrow and the `aria-current` nav item.
- Screen 1 now promises "ein eigener Fall", which is what the brief prints; Feld 4's
  help no longer repeats the Massnahme header's question; the prediction label no
  longer wraps beside its bar on a laptop.
- **Participant handout**: spellcheck squiggles, a shifted element crop and a focus
  ring were found by an independent overseer (86, then 78, then 91/100 across three
  rounds, per-page evidence each time) and fixed in
  `generate_participant_screenshots.py`, which now has a red-pixel gate and a
  scroll-position assertion.
- **Tier on the final run:** 812 checks over 12 suites, 0 failed, 0 skipped.

## V1.13 status: an APEX two-evaluator gate, a new hypothesis toggle, and every finding fixed

Two rounds, both dated 2026-09-17, both against the tool this round bumped to
`DATA.build.version = "1.13"`. Full record in `04 internal/02 qa/archive/RELEASE_NOTE_V1.13.md`.

**Round 1 (the APEX gate Eugen asked for):** two independent evaluator/fixer agents
drove the live tool with their own from-scratch Playwright scripts. New feature: a
voluntary working-hypothesis toggle (Erklärung A / Erklärung B) under screen 2, so the
group's own answer to "welche Beobachtung würde sie trennen?" can actually change the
headline sentence, the Diagnose node and the carry band, instead of always echoing
Erklärung A. Three bugs fixed: a link-map pairing could resurrect unconfirmed after an
unrelated re-check; importing `keine_massnahme: true` alongside a non-empty
`massnahmen_ids` left the screen self-contradictory; the printed brief could spill to a
second page under ordinary (not extreme) field lengths, fixed by retuning every
free-text `maxlength` down together with the print padding. One visual fix: a status
toast could linger over the next screen's own title. Covered by a dedicated 17-check
suite at the time; folded into `regression_v29_test.py`'s H-checks since.

**Round 2/3 (this round's own APEX re-scoring, in direct response to "not done,
disclosed rather than hidden -- do it"):** two fresh, independent adversarial
evaluators, again with zero prior context, scored the tool 71/100 and 80/100 across
ten dimensions each with concrete repro steps. Every one of their findings was fixed,
not deferred: accented and non-Latin export filenames (JS's `\w` is ASCII-only, unlike
Python's; fixed with a Unicode property escape), phone-width horizontal overflow from
an unbroken German compound word (the carry band, the brief sheet, and `.brief__lead`,
found while verifying the other two), stale link pairings surviving a JSON export in
BOTH tools (a symmetric `pruneLinks()`/`prune_links()` gap, not the Streamlit-only bug
it looked like at first), the Startfall button label and the "case loaded" toast/case-
meta block having drifted from a Streamlit redesign that was never ported, a stale
Startfall `titel` traced to a missed field in an earlier merge (fixed at the JSON
source, with a new field-by-field cross-file static check added to catch a recurrence),
a missing `aria-current` on the progress nav and a heading-level skip in the lookup
panel, and a non-array JSON type-handling gap on every one of the four id-list fields a
hand-edited or corrupted import can carry (`asArray()` / `_as_list()` guards added to
both tools; JS threw on a truthy non-array via `.filter()`, Python crashed on an
explicit `null` under an existing key -- opposite failure modes on the same underlying
gap). All 7 suites in this folder plus the Streamlit suite: 622 checks, 0 failed, 0
skipped (see "Running the gates" below for the per-suite breakdown). Not done this
round: the ~30-gate project-level conformance suite was not re-run (this round's
changes were confined to `03 tools/`, already covered here and in the Streamlit
suite). ("Porting the Kundenperspektive/Gruppe print-export gates to Streamlit" used
to be listed here too; that line is stale as of round 4 below -- see the round 4
paragraph, and `04 internal/02 qa/archive/RELEASE_NOTE_V1.13.md`, for why.)

**V1.13 round 4 (2026-09-17), two further adversarial passes, every finding fixed
and live-fired:** a first pass (Evaluators I, J) found and fixed a missing
accessibility announcement on the link map (H4), a Streamlit timer display that
went stale without a widget click (H5) and a related click-ordering bug in the
same function (I2), a cross-tool Unicode-truncation mismatch plus a deeper
surrogate-pair-splitting bug it uncovered (I1), the Streamlit brief's missing
diagnosis-chain/prediction-chart rendering (G4), and a corrupted-JSON crash risk in
the Streamlit data loader. A SECOND, independent pass (Evaluators M, N), run
specifically to catch defects the first pass's own fixes might have introduced,
found and fixed three more: the corrupted-JSON fix raised `SystemExit`, which
Streamlit's own script runner silently discards, so the facilitator would have seen
a blank page with no error at all (I3/I4); a regression gate for an earlier D7 fix
was a source-text substring search that a docstring's own narration satisfied even
with the real code deleted (F3); and a syntactically-valid-but-structurally-broken
data file (a deleted key) still raised a raw `KeyError` (F4). Full record, including
the round's own numeric scorecard, in `04 internal/02 qa/archive/RELEASE_NOTE_V1.13.md`.

## V1.12 status: the Kundenperspektive and Gruppe gates are now enforced, not just displayed

Six changes, chosen by an LLM council (5 advisors, anonymized peer review, chairman
synthesis; full record in `04 internal/02 qa/LLM-COUNCIL_RECORD_V1.12.md`) and recorded in
full in `04 internal/02 qa/archive/RELEASE_NOTE_V1.12.md` (superseded by V1.13; moved to
`archive/` when V1.13's release note was written):

1. Printing or exporting a brief is now held until all five Kundenperspektive-Prüfung
   boxes are ticked. The checklist's own on-screen copy always said this was required; the
   two gate sites (`btnPrint`, `exportJson`) never actually checked it until this round.
2. Printing or exporting is now also held until Gruppe (screen 1) is chosen, by the same
   mechanism, so a brief can no longer print or export anonymously.
3. The **Eigener Fall** control and opening a case from JSON now ask before discarding
   unsaved work, matching the confirm the tool already used when swapping Startfälle.
4. The file-import control no longer shows English browser chrome ("Choose File") inside
   an otherwise all-German tool; a German-labelled button now triggers a hidden native
   input, with the picked filename shown beside it.
5. A chip disabled by its row's selection cap now says why, in a tooltip naming the cap.
6. Version moved to 1.5 (`index.html`, `build_radar.py`, and the Streamlit companion's
   `../streamlit-app/radar_data.py`, label-only there).

The five-screen structure, the six worksheet fields, the report-back sentence format, the
V1.10 theme grouping and coaching banners, the lab clock, and the Streamlit companion's
own gating behavior did not change. Gating Gruppe and Kundenperspektive on `index.html`
without a matching change to the Streamlit companion is a deliberate, disclosed fork
between the two tools (see the release note); porting the same two gates to Streamlit is
open for a future round. Full 458-check tier green after the edits, live Chromium,
non-quick.

## V1.11 status: every June/Juni reference removed from both tools

Built 2026-09-16, same day as V1.10, on Eugen's instruction: he introduces the June
material himself, once, at the start of his own presentation, and never again after
that, so neither RADAR tool may say "Juni" anywhere a participant can see it. This
round did not remove or rewrite any substantive content; it only removed the two-part
"Im Juni: ...? Heute: ..." framing from the three theme-coach bridge sentences added
in V1.10, keeping the already-verified "Heute" half verbatim as a single sentence, and
renamed labels that named June explicitly. No new prose was written for this round.

What changed in `index.html`:

- The three theme-coach bridge sentences (T-WARTEN, T-STILLE, T-UMFELD) dropped their
  "Im Juni: [question]? Heute: " opening; the sentence now states only the already-
  verified "Heute" insight. T-BERATER still shows no banner, unchanged (no fabricated
  content to fill the gap it never had).
- The banner label above each bridge sentence changed from "Juni-Anschluss" to
  "Hinweis".
- The brief's footer stamp changed from "Stand Juni-Material: Liste und
  Zusammenfassung, <date>" to "Stand Konzepteliste: <date>" (same date, same source).
- `DATA.build.version` and the `radar-version` meta tag moved to `1.4`.

What changed elsewhere:

- `build_radar.py`: `TOOL_VERSION` to `"1.4"`; `FOOTER_STAND_PREFIX` to match
  `index.html`'s new stamp (this constant is read as canonical ground truth by
  `static_checks.py`, `regression_v29_test.py` and `stress.py`, so it had to move even
  though the generator itself stays dormant); `BOX_TITLES_FALLBACK`'s "Feld 2 ·
  Juni-Konzepte" to "Feld 2 · Konzepte und Hebel" (dead fallback code, confirmed the
  real worksheet file takes precedence; fixed anyway for anyone who reads this file).
- `../streamlit-app/radar_data.py`: `APP_VERSION` to `"1.4"`; every "Juni"-prefixed
  user-facing string relabelled (`HINT_WIRK`, `CAP_WIRK`, `CAP_MASS`, `CAP_TOOL`,
  `STARTFALL_SUGGEST`, `BOX2_TITLE`, `BRIEF_BOXES_FALLBACK`, `BRIEF_FOOTER`,
  `REPLACE_WARNING`, `STARTFALL_LOADED`). Fixing `BOX2_TITLE` and
  `BRIEF_BOXES_FALLBACK` also fixed a genuine pre-existing cross-tool inconsistency:
  the Streamlit app had been calling Feld 2 "Juni-Konzepte" for several rounds while
  `index.html` already called it "Konzepte und Hebel"; both tools now agree.
- `../streamlit-app/radar_data.py`'s JSON export/import schema: `stand_juni_material`
  to `stand_material`, `keine_juni_massnahme` to `keine_massnahme`, in both
  `export_payload()` and `apply_payload()`. This was a genuine, previously-undetected
  bug: a case exported from the Streamlit app and reopened in `index.html` (or vice
  versa) would silently fail to restore its "keine passende Massnahme" state and its
  footer stamp, because the two tools were writing and reading different key names.
  `../streamlit-app/app_regression_test.py`'s matching assertion was updated for the rename.
- `04 internal/00 data/june_concepts.json`: the three `theme_anchors[*].bridge` values
  reduced to match `index.html`'s new wording exactly (this file is not read at
  runtime by either tool, but is the documented original source of this text, so it
  is kept in sync for any future maintainer who regenerates from it).
- the former lab/STARTFALLKARTEN_DE.md (quarantined in V1.13, when SF2 was merged
  into SF1 and printed Startfallkarten were retired; it sits in the package's
  quarantine folder, outside every shipped path), `02 for-participants/lab/
  TRANSFERKARTE_DE.md`, `02 for-participants/FUER_IHR_TEAM_DE.md`,
  `02 for-participants/radar-screenshots/README.md`: "Juni-Anker" / "Juni-Konzept"
  labels renamed to "Konzepte" / "Konzept", consistent with the software. These are
  printed participant handouts, not "the software" literally, but they are read by
  the same participants at the same table as the RADAR tool, so the same rule
  applies to them.
- `02 for-participants/radar-screenshots/`: all 7 screenshots and the combined PDF
  regenerated against the fixed tool, using a new script kept in this folder,
  `generate_participant_screenshots.py` (see the file table below), so the images no
  longer show the retired "Juni-Anschluss" banner or footer.
- `../00 Essentials/Tag X (Day-Of)/RADAR_Tool.html`: re-synced from the fixed
  `index.html`.

**Deliberately left alone, and why:** internal Python/JS identifiers, function names,
JSON schema keys and code comments that use a `june_`/`Juni`-prefixed name purely as
internal architecture naming and are never rendered to a participant (for example the
`JUNE_STAND` variable, the `_GERMAN_MONTHS` helper, `04 internal/00 data/june_concepts.json`'s
own filename, or a code comment explaining provenance). Renaming these was not part of
what was asked, touches nothing a participant ever sees, and would be a large,
disproportionately risky change for zero participant-facing benefit. Facilitator-only
and internal documents (`01 facilitator/*`, `00 Essentials/Vorbereitung (Prep)/
VORBEREITUNGSDOSSIER_DE.md`, the call transcript, the build/QA archive) also still
reference June, because Eugen himself is the reader of those, and he is the one who
introduces the June material in the first place; nothing in them reaches a
participant.

All 6 suites in this folder, plus the Streamlit suite, pass clean against a live
Chromium run, full tier, no skips: `static_checks.py` 167/167, `regression_v29_test.py`
101/101, `button_regression_test.py` 35/35, `stress.py` 28/28, `theme_coaching_test.py`
24/24, `manual_qa_supplement.py` 13/13, `../streamlit-app/app_regression_test.py`
90/90. 458 checks total, 0 failed. See `04 internal/02 qa/archive/RELEASE_NOTE_V1.11.md`
(archived; V1.12 is now current, see the section above).

## V1.10 status: 3 main survey themes + 1 wildcard theme, real-time coaching added

Built 2026-09-16 in a separate version folder (`Version 1.10`, not overwriting the
shipped V1.9) by council process (see `04 internal/02 qa/LLM-COUNCIL_RECORD_V1.10.md`). Screen 1's flat five-button "Startfall laden" list is now grouped
by theme: the three patterns the pre-survey names most (`T-WARTEN` n=4, `T-STILLE` n=4,
`T-UMFELD` n=3, all from `04 internal/00 data/survey_facts.json`) sit front and center, and a fourth,
visually distinct wildcard group (`T-BERATER`, n=3) sits last with a pointer to the
pre-existing "Eigener Fall" free-text path for anything the four do not cover. The five
Startfall buttons themselves, their ids, their order and their printed text are
byte-identical to V1.9; only the grouping and headings around them are new, so every
`#startfallRow button[data-case]` selector the four pre-existing suites already used
still finds the same buttons in the same order (see `theme_coaching_test.py`, which
asserts this explicitly).

Three of the four themes carry a verified "Juni-Anschluss" bridge sentence, taken
verbatim and unmodified from `04 internal/00 data/june_concepts.json`'s `theme_anchors[*].bridge` (frozen
data that was already in the package but never surfaced in the tool before this round).
Once a group loads a themed Startfall, that sentence appears as a real-time coaching
banner on screens 2 and 3 (`#themeCoach2` / `#themeCoach3`). `T-BERATER` (the wildcard)
and any `Eigener Fall` case show no banner, because `theme_anchors` carries no bridge
for `beraterseite`: the tool never fabricates a connection the source data does not
state. Two further live aids: a similarity nudge on screen 2 (`#hint-gegenprobe-similar`)
that flags, without blocking, when Erklärung B reads as a reworded Erklärung A rather
than a genuine alternative; and a progress line under the step nav
(`#progressCount`) that always reads off the same field list the brief's own "Noch
offen" line already tracks, so it cannot drift from what the brief actually requires.
The Toolbox chip disclosure (screen 3) was also repointed from `german_short` (always a
substring of the chip's own label, so it never actually showed) to `print_questions`,
June's own reflection questions for that tool, verbatim.

`DATA.build.version` and the `radar-version` meta tag moved to `1.3`; `build_radar.TOOL_VERSION`
(retired as a generator, kept as the suites' canonical version literal) and the
Streamlit companion's `APP_VERSION` were bumped to match. All 5 suites in this folder
pass clean against a live Chromium run (167 + 101 + 35 + 28 + 24 = 355 checks, 0 failed);
see "Running the gates" below.

## V1.8 status: report-back format corrected to the BCG-approved deck, all suites green

As of V1.7 (2026-09-09), `index.html`'s report-back sentence moved from the flowing-prose
format ("Unsere Situation ist X. Im Juni hiess das Y. Wir vermuten Z. Wir ändern A. Wir
erwarten B. Das Signal: D.") to a labelled-line format. The retired "Y" (June-concept
header) and "B" (Vorhersage) clauses are gone from the header; Vorhersage is still
collected in Feld 6 but is no longer repeated in the report-back sentence.

As of V1.8 (2026-09-09), the labelled-line format itself was corrected against the
BCG-approved deck (then shipped as "Behavioral_Science_Deep_Dive_V1.8_2026-09-24.pptx";
the same panel is unchanged in the current build, deck_v11_client), seen for the first
time this round: its own "Brief in einem Satz" panel (slide 26) carries exactly four
labels, not five: "Problem: X. Hypothese: Z.
Änderung: A. Test: D." V1.7 had shipped a fifth "Alternative: G." clause with no shipped
artifact to check it against; nothing caught it because the deck comparison this
package's own gate runs reads a stale generator dump that never carried the sentence at
all (see `04 internal/04 build/package_gate.py`'s comment on `_SENTENCE_RX`). Erklärung B
(Gegenprobe) is unaffected as a worksheet field: it stays Feld 3, box 3 of the
Interventionsbrief, and is still shown in the full six-box brief on screen and in the
PDF export; it is simply not echoed in this compressed headline any more. All dead
old-format construction code (`deCapitalise`, `closeSentence`, `isVerbPhrase`,
`joinUnd`, `conceptHeaderList`, `situationIsOwnCase`, and the leading-function-word list)
was removed from `index.html`, not just left unused.
`../../02 for-participants/lab/BERICHTSKARTE_DE.md` was rewritten to match this format
word for word.

`../streamlit-app/radar_data.py` was rewritten this round to match `index.html` field for
field: labels, help text, the Gegenprobe wording, and `reportback_sentence()`'s
construction logic all match. **The Streamlit companion is no longer stale** and is safe
to present alongside `index.html`; see its own README for the reconciliation detail.

**`index.html` is hand-authored, not generated.** This has been true since V1.6
(2026-09-09): ChatGPT independently hand-wrote a new version of the tool from scratch (no
generator, no companion), and it was adopted as canonical because it carried post-review
field renames the generator and the frozen JSON data had not yet been updated for. **Do
not run `python3 build_radar.py` and copy its output over `index.html`.** Doing so would
silently overwrite the shipped tool with the retired V1.5 field names and structure.
`build_radar.py` (and the "no June concept name may be a string literal in code"
architecture described further down) is kept in this folder as historical/future
reference only, clearly flagged at the top of the file itself, and its `TOOL_VERSION`,
`ROUTINE_HELP`, `FOOTER_STAND_PREFIX` and `GEGENPROBE_HELP` constants are kept in sync
with `index.html`'s actual values even though the generator itself is dormant, because
`static_checks.py` still reads them as its canonical source for those strings.
Reconciling the generator so it produces the current `index.html` byte for byte again
remains out of scope, unchanged from V1.6.

A consequence: several claims below that describe how the *build* enforces things
("static_checks.py fails the build if...", "the two cannot drift because...") describe
the build-time architecture as it stood through V1.5. They are historically accurate
and remain true of `build_radar.py` itself, but they no longer describe a guarantee
that holds for the shipped `index.html`, because that file is no longer produced by
the build. Treat this whole README, past this notice, as a description of the tool's
behaviour and of the (currently dormant) build architecture, not as a live guarantee
about how `index.html` was produced.

## What is in this folder

Paths in this table are given from the package root. Inline references elsewhere in this
file are relative to this folder, `03 tools/radar-tool/`.

| File | What it is |
|:-|:-|
| `03 tools/radar-tool/index.html` | **The shipped tool, current as of V1.14.** Hand-authored (see notice above), not generated. Edit it directly; `build_radar.py` no longer produces it. |
| `03 tools/radar-tool/build_radar.py` | Superseded generator. Reads the frozen JSON and writes an `index.html` that reflects the pre-V1.6 field names and flowing-prose report-back format, not the shipped one. Kept for reference; see the banner at the top of the file. |
| `03 tools/radar-tool/PRIVACY.md` | Data-handling statement for the IT and compliance read, in German. |
| `03 tools/radar-tool/static_checks.py` | Static gate: vocabulary, orthography, offline integrity, data parity. Checks `index.html`'s content and structure directly (201 checks, all passing as of V1.14; 197 as of V1.13 round 4; 167 as of V1.7); it does not compare `index.html` against `build_radar.py`'s output (see notice above). |
| `03 tools/radar-tool/regression_v29_test.py` | Browser gate: the four assertions the build spec names (chips, brief, vocabulary, checkpoints). |
| `03 tools/radar-tool/button_regression_test.py` | Browser gate: every button, including a real JSON download read back off disk. |
| `03 tools/radar-tool/stress.py` | Browser gate: one whole case end to end, the printed PDF read back, layout under a long entry. |
| `03 tools/radar-tool/manual_qa_supplement.py` | Browser gate: the clock driven to each checkpoint, the chip caps under a forced DOM edit, storage, focus, one screenshot per screen. |
| `03 tools/radar-tool/theme_coaching_test.py` | Browser gate for the V1.10 round: the 3-main-plus-1-wildcard theme grouping on screen 1, the theme-coach bridge banner on screens 2 and 3, the live Erklärung-similarity nudge, the progress counter, and the Toolbox chip disclosure now showing `print_questions`. Also writes one screenshot per screen to its temp output folder. |
| `03 tools/radar-tool/generate_participant_screenshots.py` | Not a gate (no PASS/FAIL). Regenerates the 7 shipped screenshots and the combined PDF in `02 for-participants/radar-screenshots/`. Reuses `stress.py`'s own case choice and fill text, so the illustrative content on the screenshots always matches what the stress suite already exercises rather than a second, hand-maintained copy. |

`regression_v29_test.py` and `button_regression_test.py` keep names inherited from the
reference package because the package gates in `04 internal/04 build/` call them by
name. The names say nothing about what is in them; their contents are the expectations
of this package.

## Building (historical; do not use to regenerate the shipped tool)

```
python3 build_radar.py            # writes index.html next to this file (SUPERSEDED, see notice above)
python3 build_radar.py --check    # builds in memory and verifies, writes nothing
```

The build reads four files, all in the package's frozen data folder:

- `04 internal/00 data/june_concepts.json`: the `concepts` array and `theme_anchors` (never `_schema` and
  never `stand`, which name the client), plus the `toolbox` array.
- `04 internal/00 data/startfaelle.json`: the cases behind the "Startfall laden" buttons. The file also
  holds the worked example printed on the Berichtskarte and, live in the deck, on slide 26.
  The BCG-approved V1.8 deck has no appendix, so this is the example's only home in the
  deck; it is never distributed to a group, both tools read that exclusion out of the file and
  offer only the distributed cases.
- `04 internal/00 data/session.json`: the lab clock, the RADAR checkpoints, the Signal help line, the
  Teammeeting question label, the check-date label, and the date the June material
  arrived, from which the brief's footer stamp is derived.
- `04 internal/00 data/survey_facts.json`: the survey pattern labels, so a Startfall button can print the
  pattern short label and the channel string verbatim.

Set `RADAR_DATA_DIR` to run the build or the suites from a copy of this folder outside
the package.

### Why there was a build at all (V1.5 architecture, dormant in V1.6)

No June concept name may be a string literal in code. Every June label in the tool is
resolved in the browser from an id in one generated data block, and `build_radar.py`
fails its own build if any of those labels turns up outside that block. `static_checks.py`
repeated the check on the shipped file, on the build script and on itself. The shipped
V1.6 `index.html` was not produced under this discipline; it was hand-written, so this
guarantee should be treated as not currently in force for the file participants use.

The tool version lived in `build_radar.py` (`TOOL_VERSION`); the shipped `index.html`
now carries its own version independently (`radar-version` meta tag and the embedded
`DATA.build.version`, currently `1.2`), since it is no longer produced by this script.

## The five screens and the six fields (V1.6; SUPERSEDED by V1.13, not fully reconciled)

**V1.13 (2026-09-17):** the field NAME below ("Konzepte und Hebel") is stale:
`index.html`'s own `BOX_TITLES` now reads "Feld 2 · Konzepte und Massnahmen", and V1.13
also added several screen elements this table and the two paragraphs after it do not
mention at all: the link map on screen 3 (click a concept, then a Massnahme or Werkzeug,
to confirm a pairing), the ten-dot prediction grid and carry band, and the removal of the
Kundenperspektive-Prüfung block from screen 5 (replaced by the quantified prediction).
`index.html`'s own `BOX_TITLES` array and its inline comments are the authoritative
current structure until this section gets a full V1.13 rewrite; treat everything below
as historically accurate for V1.6 only, not as the tool's current shape.

| Screen | RADAR step | Field of the sheet |
|:-|:-|:-|
| 1 | Reibung erkennen | Feld 1 Beobachtung (Situation, Beobachtetes Verhalten), plus the Konzepte chips (part of Feld 2) |
| 2 | Auslöser analysieren | Feld 3 Diagnose (Erklärung A, Erklärung B) |
| 3 | Design anpassen | Feld 4 Die eine Änderung und Frage fürs Teammeeting, plus the Massnahmen and Toolbox chips (the rest of Feld 2) |
| 4 | Aktion einleiten | Feld 5 Umsetzung (Nächster Schritt, Verantwortlich, Datum) |
| 5 | Routine verankern | Feld 6 Vorhersage, Signal, Risiko oder Nebenwirkung, and the Kundenperspektive-Prüfung |

Feld 2, "Konzepte und Hebel", is split across screens 1 and 3 because that is where the
two decisions fall in the lab; the Interventionsbrief prints both halves together as one
field, under the sub-rows Konzepte, Massnahmen, Toolbox.

Six box titles, byte for byte as `index.html`'s own `BOX_TITLES` array carries them:

1. Feld 1 · Beobachtung
2. Feld 2 · Konzepte und Hebel
3. Feld 3 · Diagnose
4. Feld 4 · Die eine Änderung und Frage fürs Teammeeting
5. Feld 5 · Umsetzung
6. Feld 6 · Vorhersage, Signal, Risiko

### The glossary

Two lookup surfaces, both driven from the embedded arrays and neither carrying a
word that is not already in `04 internal/00 data/june_concepts.json`:

- a disclosure on every concept chip, showing that concept's `german_gloss` (a Toolbox
  tool's `german_short`) verbatim. The control is a sibling of the chip's label, never
  a child of it, so the click that opens an explanation can never reach the checkbox.
  Which explanations are open is deliberately not part of the case state and never
  reaches the brief or the export.
- a standing panel, **Konzepte nachschlagen**, in its own bar above the screens, so all
  five reach it. It lists the ten Wirkmechanismen, the eight Massnahmen and the five
  Toolbox tools under June's own three headers, with a foundation entry under its own
  heading rather than folded into the mechanism row.

Chip rules (unchanged from V1.5): Wirkmechanismen minimum one, maximum two. Massnahmen
none to two. Toolbox none or one. "Keine passende Massnahme" clears and disables the
Massnahmen chips, and the brief then prints "Keine passende Massnahme" under Massnahmen,
so a group whose case has no Massnahme is not forced into one. No chip row takes free
text. "Startfall laden" fills Feld 1 only and preselects no chip; the card's own June
suggestion is printed as text beside the field, so the group can overrule it.

The Gegenprobe (Erklärung B) field's help text in the shipped tool is now "Eine echte
Alternative, nicht dieselbe Erklärung anders formuliert." This differs from the wording
the V1.5 build carried; it is one of the strings that changed in the hand-authored file
without a corresponding change to `build_radar.py` or the frozen JSON (see the V1.6
status notice above).

## The Interventionsbrief

The brief prints the six fields in order under the report-back sentence, and closes
with the June stamp and the attribution. Its box titles are the six box names of the
worksheet, byte for byte, because the group reads the brief next to the worksheet and
the slide-26 worked example. Box 6 also carries the quantified Vorhersage (the group's
own two 0-10 estimates, heute/nach der Änderung, and the delta sentence between them),
which replaced the earlier Kundenperspektive-Prüfung checklist in a round before
V1.13; there is no separate advisory line for it, since the two numbers are already
part of box 6's own fields (`erwartung`/`signal`).

Through V1.5 the box titles were read from the worksheet at build time, so the two could
not drift; that mechanism does not run for the hand-authored V1.6 `index.html`, so a
change to the worksheet's field names or to the tool's `BOX_TITLES` array now has to be
kept in sync by hand until the two are reconciled.

## What the tool requires before a step is finished

"Aktion einleiten" is not complete without Nächster Schritt, Verantwortlich and Datum, and
"Routine verankern" is not complete without Vorhersage and Signal; the export is held until
those fields are there. Both gates name the fields they are waiting for rather than
leaving a button looking inert.

As of V1.12, printing or exporting is also held until Gruppe (screen 1) is chosen.
`missingGruppe()` joins the existing `missingOf(REQUIRED_STEP_5)` list at both gate
sites (`btnPrint` and `exportJson`), so a brief can no longer print or export
anonymously. V1.12 also added a matching `missingKosten()` gate for the
Kundenperspektive-Prüfung checklist that then existed; a later round removed that
checklist in favor of the quantified Vorhersage described above, and
`missingKosten()` was removed with it, undocumented at the time -- the current gate
is Gruppe plus Vorhersage/Signal, not a five-box checklist. (index.html's own
`missingGruppe()` comment carries the fuller history.)

The brief carries one line at its head naming every field still open, next to the
per-field markers.

## Own case, and the group

A group may replace its Startfall with a case of its own, which every printed surface
promises. Two ways out of the binding: the **Eigener Fall** control, and simply rewriting
Feld 1, after which the loaded case is no longer what is on the screen and the header
sentence says "ein eigener Fall". Loading a different Startfall over work the group has
already done asks first, and does not carry the previous case's June selection into the
new one.

As of V1.12, the **Eigener Fall** control and opening a case from JSON both ask first too,
the same way, whenever unsaved work is at risk (`ownWorkAtRisk()`): before this round,
only the Startfall switch had that guard, and either control could silently wipe Feld 1,
the chip selections, or a whole in-progress case.

Screen 1 asks for the group. It prints on the brief, travels in the JSON and appears in
the export file name, because the runbook collects one brief per group and the follow-up
mail attributes them by group. As of V1.12, a brief also cannot print or export until a
group is chosen, so the export file name can no longer silently fall back to the generic
"Interventionsbrief.json". A fully filled case fits one A4 page.

The JSON export carries both the ids and the resolved labels, so the same case reopens
in this tool ("Bestehenden Fall aus JSON öffnen" on screen 1). As of V1.7 the Streamlit
companion's field names and structure were reconciled to match (see the companion's own
README), and as of V1.13 both tools guard the same four id-list fields against a
malformed (non-array) value on import, so a case exported from either tool reopens
cleanly in the other, including a hand-edited or corrupted file; the round trip is
checked from both directions in `../streamlit-app/app_regression_test.py` and
`regression_v29_test.py`.

## The timer

The lab clock and RADAR checkpoints are embedded in the shipped `index.html` directly
(it is no longer generated from `04 internal/00 data/session.json` at build time). A
change to that data file will not move the shipped tool at all now; any change to the
lab clock or checkpoints has to be made by hand in `index.html` until the build is
reconciled.

## The Streamlit companion (reconciled as of V1.7)

`../streamlit-app/` carries the same five steps and field names as `index.html`, the
same chip rules, glossary and brief, and reads the same frozen JSON through its own
`03 tools/streamlit-app/radar_data.py`. As of V1.7 its field labels, help text and
`reportback_sentence()` construction were rewritten to match `index.html` field for
field; `../streamlit-app/app_regression_test.py` (221 checks as of V1.13 round 4, 156
as of round 2/3, up from the 90 at V1.7) confirms this at the level of shared source
data and construction logic. See
`../streamlit-app/README.md` for detail.

## Running the gates

```
python3 static_checks.py
PLAYWRIGHT_BROWSERS_PATH=/opt/pw-browsers python3 regression_v29_test.py
PLAYWRIGHT_BROWSERS_PATH=/opt/pw-browsers python3 button_regression_test.py
PLAYWRIGHT_BROWSERS_PATH=/opt/pw-browsers python3 stress.py
PLAYWRIGHT_BROWSERS_PATH=/opt/pw-browsers python3 manual_qa_supplement.py
PLAYWRIGHT_BROWSERS_PATH=/opt/pw-browsers python3 theme_coaching_test.py
```

As of V1.7, all four pre-existing suites are fully rewritten for `index.html`'s current
labelled-line report-back format and structure, and all pass clean against a live
Chromium run: `static_checks.py` 167/167, `regression_v29_test.py` 101/101,
`button_regression_test.py` 35/35, `stress.py` 28/28. `../streamlit-app/app_regression_test.py`
(90 checks) is reconciled the same way. None of the four suites here are weakened or
left honestly-failing; every check that used to assert the retired flowing-prose format
was rewritten to assert the current one, not skipped or deleted. `theme_coaching_test.py`,
added in V1.10, covers this round's own new surface and passes 24/24.

As of V1.11, all six suites in this folder plus the Streamlit suite were run live,
full tier, no skips, after the June-removal and cross-tool schema fixes: `static_checks.py`
167/167, `regression_v29_test.py` 101/101, `button_regression_test.py` 35/35, `stress.py`
28/28, `theme_coaching_test.py` 24/24, `manual_qa_supplement.py` 13/13,
`../streamlit-app/app_regression_test.py` 90/90. 458 checks total, 0 failed.

**As of V1.13 round 2/3 (2026-09-17), all seven suites in this folder plus the
Streamlit suite were run live, full tier, no skips, after the APEX two-evaluator
round's fixes (accented/non-Latin export filenames, phone-width overflow-wrap on the
carry band and brief sheet, `pruneLinks()` guarding both tools' exports, the Startfall
button label and case-loaded notice ported to Streamlit, a stale Startfall titel
corrected at its JSON source, `aria-current` and the lookup panel's heading level, and
`asArray()`/`_as_list()` type guards on every JSON-imported id list in both tools):**
`static_checks.py` 191/191, `regression_v29_test.py` 131/131,
`button_regression_test.py` 39/39, `stress.py` 57/57, `theme_coaching_test.py` 30/30,
`manual_qa_supplement.py` 18/18, `../streamlit-app/app_regression_test.py` 156/156.
**622 checks total, 0 failed, 0 skipped**, at that point in the round.

**As of V1.13 round 4 (2026-09-17, current), after both further adversarial passes
above and their fixes, the suite has grown to 12 files and 767 checks, 0 failed, 0
skipped:** `static_checks.py` 197/197, `regression_v29_test.py` 149/149,
`button_regression_test.py` 39/39, `stress.py` 78/78, `theme_coaching_test.py` 30/30,
`manual_qa_supplement.py` 18/18, `../streamlit-app/app_regression_test.py` 221/221,
`../streamlit-app/brief_print_test.py` 7/7,
`../streamlit-app/brief_chain_parity_test.py` 13/13 (new this round),
`../streamlit-app/timer_autorefresh_test.py` 6/6 (new this round),
`../streamlit-app/unload_guard_test.py` 5/5 (new this round),
`../streamlit-app/data_error_visibility_test.py` 4/4 (new this round).
**767 checks total, 0 failed, 0 skipped.** Full itemized record, including the
round's numeric scorecard: `04 internal/02 qa/archive/RELEASE_NOTE_V1.13.md`. The counts two
paragraphs up (167/101/35/28, 458 total) are the V1.7/V1.11 snapshot, kept as dated
history, not the current figure; every suite has grown since, so none of these three
paragraphs' totals are meant to match each other -- each is dated to its own round.

`stress.py`, `manual_qa_supplement.py` and `theme_coaching_test.py` write their PDFs,
PNGs and screenshots to a temp directory, never into this folder.
