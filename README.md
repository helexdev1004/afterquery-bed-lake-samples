# Attribute lake sediment samples to their source beds

A harbor-format benchmark task, authored for the AfterQuery Experts programme as data
annotation for AI evaluation. Task slug `afterquery/bed-lake-samples` — **Science / Geology**.

An AI agent gets a container, this brief and nothing else. It works alone for
4 hours. Then a sealed verifier it never sees scores the result `0` or `1`.

## What it asks for

The lake-core project has forty geochemistry samples whose results are in, and the paper needs each one placed in the bed it was cut from, by the bed's number down the hole, with the core scanner's grain reading at the sample. The beds were logged when the core sections were split; the samples were cut later, from the archive halves, by people who only wrote down the section and how far along it they cut.

The full brief is in [`instruction.md`](instruction.md).

| | |
| --- | --- |
| **Deliverable** | `/app/beds_for_paper.md` |
| **Agent budget** | 4 hours (14400s) |
| **Expert estimate** | 5 hours |
| **Machine** | 1 CPU, 2048 MB RAM, 0 GPU |
| **Verifier budget** | 300s |
| **Tags** | `sedimentology`, `core-curation`, `record-forensics`, `stratigraphy`, `python` |

## Why it is hard

Placing samples in the beds they were cut from is real and ordinary work. Sedimentologists and the core curators of lake- and ocean-drilling projects do it whenever geochemistry comes back years after a core was split, and they do it from exactly these leavings: the section list, the bed log, the scanner's track, the sample book and the project's curation note. The awkward parts of it here - a curator who books in millimetres, another in inches, a third who books depth down the hole instead of place along the section, a fourth who writes the volume first - are the ones a sample book actually has in it rather than invented ones.

<details>
<summary>Full note</summary>

The data is synthetic and was built truth first. 396 flood layers were laid down in 5 holes before anything was cored, each with its number down the hole; the holes were then cored in 61 sections, the sections labelled and logged, and 440 samples cut and booked the way five curators would have done it. Every answer is therefore exact by construction rather than a judgement made later, and the records are static files, so nothing is computed at marking time. Forty samples are each to be given the number of their bed down the hole, marked exactly, and at most 4 may be wrong. Naming one bed for all forty gets 2. The arithmetic is not withheld. CORING.md gives all of it: how a hole is cored in sections, how a section is labelled and measured, how its beds were logged, how beds are numbered down a hole, how a sample is booked, which bed takes a sample on a boundary, and the length of an inch. The work is in the doing. Each sample has to be found among the beds logged in its own section and that bed's place turned into a number down the hole, and the answer is a bed number rather than a quantity, so there is nothing to fit and nothing to regress. Taking the records exactly as they stand places 5 of 40. Five things about the records have to be got from the records themselves. None is written down anywhere, and each is settled by something in the records that cannot be right otherwise. The first is that one curator books a sample's place in millimetres, so that his samples lie ten sections' length along a section. The second is that one curator books it in inches, so that none of his samples lies beyond two fifths of the way along any section. The third is that one curator books the sample's depth down the hole in metres and not its place along the section, so that his figures never exceed the depth of the hole in metres and mean nothing as centimetres; the depth of every section's top is on record and turns them back into places. The fourth is that one curator writes the volume before the place, so that his places are 2, 5, 8 and 10 and his volumes run the length of a section. The fifth is not any curator's habit, and it is the one that decides the task. A section is pushed out of the barrel, capped and labelled top and bottom at the rig, and 20 of the 61 sections were labelled the wrong way up - four in every hole, so it is nobody's habit and no curator's sections can be checked against another's. Nothing in the records says which. A section labelled upside down was logged upside down, from its labelled top, and its samples were booked from the same labelled top, so a sample is found among its section's beds exactly as well as in any other section; but the beds logged in it run up the hole, not down, and every bed number that depends on it is counted from the wrong end of the section. CORING.md says how a section is labelled and logged and says nothing of a label being wrong; what settles it is the beds themselves. They are flood layers, each laid down by one waning flood, coarsest at its base and fining upward, and the core scanner ran a grain track down every section before it was split. In a section labelled upside down the beds read coarsest at their labelled tops: in every one of the 61 sections the beds read coarser in their lower third than in their upper, or the other way about, by at least 8 points of the scanner's index a bed, against a scatter of six points a reading. The bed log carries no grain sizes, so no listing of it shows which sections are which; it takes the scanner's track, set against the bed boundaries, to see it. Taking every section to lie as it is labelled costs 20 of the 40 samples - and nothing goes visibly wrong for a solver who does, since every sample falls in a bed of its own section either way. Unlike the four curators' habits, which can each be checked against the hole of the curator who kept to the note, there is no clean hole for this one. tests/gates.py scores all 32 ways of reading those five. Every figure quoted here comes from its output. The whole account places all 40. The best account that is not the whole one places 32, so missing any single one of them fails. At most 4 may be wrong, which leaves room for a slip and still sits 4 clear of every partial account. It also refuses to pass unless the grading really does settle which way up every section lies, since a reading the records did not settle would be a guess and not a finding; and unless no wanted sample stands within twelve millimetres of a bed boundary, so that neither the rule for a sample on a boundary nor an inch's rounding decides an answer.

</details>

## How it is verified

The deliverable is a data file. The verifier reads /app/beds_for_paper.md as text, takes the first two cells of every table row, and never executes, imports or loads anything the submission wrote, so there is nothing to sandbox and no way for a submission to influence its own score. 40 answers are marked, one for each sample in paper.txt, each a bed number matched exactly as text.

<details>
<summary>Full note</summary>

At most 4 may be wrong. The sealed numbers live only in the verifier image at /tests/truth.json; the agent's container carries the note, the section list, the bed log, the scanner's track, the sample book and the list of the paper's samples, and nothing else. /tests is mode 700 in the verifier image, set at build time so it holds under a read-only root, and the agent's container has no mount of it at all; the verifier's log directory is likewise unreachable from the agent's side. There is nothing in the agent's container against which a solver could check an answer. No sample's bed number is attested anywhere, no total has to come out and nothing has to balance, so a wrong set of numbers is exactly as self-consistent as the right one. The numbers have to be worked out; they cannot be searched for. tests/gates.py is shipped and can be re-run. It scores all 32 readings of the records against the sealed numbers and refuses to pass if the whole account does not place every sample, if any other account comes within the allowance, or if guessing does. It reports the whole account at 40, the best account that is not it at 32, the records taken as they stand at 5, and one bed named for everything at 2. The records are static files from a seeded generator, so nothing is computed at marking time and the answer cannot drift between runs. The reference was run against the shipped bundle in the task's own container and placed 40 of 40; an empty container scores 0.

</details>

<details>
<summary>Reference approach (spoiler)</summary>

solution/place.py reads only what the agent's container carries and writes /app/beds_for_paper.md. With solution/coring.py and solution/ref.py it is under two hundred and fifty lines and imports nothing outside the standard library. It reads the section list, the bed log and the sample book, and finds the curators' habits from the records rather than assuming them: the curator whose places are all 2, 5, 8 or 10 while his volumes run the length of a section, the one whose places lie beyond the end of their sections, the one whose places never pass two fifths of a section's length, and the one whose places are all under twenty five. It puts each right, working in exact fractions and rounding to the millimetre only at the end. For every section it sets the scanner's grain index in the lower third of each logged bed against the upper third, adds the differences up over the section, and takes the section to have been labelled upside down if the beds read coarser at their labelled tops. It finds each sample among the beds logged in its section, a sample on a boundary going to the bed beyond it, and numbers that bed down the hole: the beds in the sections above, plus the bed's place in its own section counted from the labelled top where the section lies the right way up, and from the labelled bottom where it does not. Every quantity is a whole number of millimetres once converted and every comparison against a bed boundary is made exactly, so nothing turns on floating point and two correct implementations must agree to the bed. Run against the shipped records it places 40 of 40.

</details>

## Layout

```
instruction.md          the brief - the only thing the agent is told
task.toml               artifacts, timeouts, resources, metadata
environment/            Dockerfile and data for the agent's container
solution/solve.sh       reference solution; scores 1 by construction
tests/                  verifier image, grading logic and ground truth
```

## Running it

With [harbor](https://github.com/laude-institute/harbor) installed and Docker running:

```bash
uv tool install harbor

harbor run -p . -a oracle -e docker   # reference solution - must score 1
harbor run -p . -a nop    -e docker   # nothing happens    - must score 0
```

Those two runs are the calibration the bundle has to satisfy: the oracle run substitutes
`solution/solve.sh` for the agent and must score `1`, the nop run does nothing and must
score `0`. Anything in between means the verifier is measuring the wrong thing.

## Reading this

`tests/` and `solution/` hold the answers. To try the task honestly, read only
`instruction.md` and `environment/`.

---

Part of a set of 38 tasks indexed at
[afterquery-data-annotation](https://github.com/helexdev1004/afterquery-data-annotation).
