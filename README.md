# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->

TakeMeter sorts comments from [r/movies](https://www.reddit.com/r/movies/), a
large public subreddit for general film discussion, into three labels:
`analysis`, `hot_take` and `reaction`. The line it's built around is whether a
comment *backs up* its point with something checkable, or just asserts it. The
intended use is sorting a large batch of comments without reviewing each one,
mainly to pull out the comments that argue with evidence.


---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `analysis`

**Definition:** Makes a point and backs it with at least one specific, checkable
fact offered as support: a number, a date, a named person or title, or a quoted
line.

**Example 1:**
> That's the curse of Animation. They definitely had boots on the ground for a
> sequel and 4 years turnaround is basically ASAP these days and it already may
> be too late. But I think Animation is lucky though as it's timeless and will
> always appeal to the next generation. Frozen, Zootopia, Moana and Inside Out
> pushed out sequels 7-10 years later and each of those were huge successes.

**Example 2:**
> It's estimated that 50,000,000 people are trapped in human trafficking rings
> across the globe. It generates billions of dollars, and is usually engaged with
> by people with enough money to skirt (or bribe) the law. […] It's not normal
> that a man could go to France and save his daughter from trafficking rings all
> on his own, but it IS a thing that some women (and even men) are kidnapped and
> taken against their will to work or be held for ransom.

### `hot_take`

**Definition:** States a judgment or claim someone could disagree with (about a
film, a person, or the industry) without offering a specific, checkable fact to
support it.

**Example 1:**
> It's fantasy. It's not a realistic portrayal of human trafficking at all.

**Example 2:**
> It works regardless. Good stories are like that.

### `reaction`

**Definition:** Expresses a feeling, or just names a title or line, without making
a claim anyone could argue with.

**Example 1:**
> Hell yeah!

**Example 2:**
> God damn, that sounds cool.

### The hardest boundary

**Which two labels:** `analysis` and `hot_take`

The borderline post:
> I don't think it would've seen the same level of success had it gone to
> cinemas. It got no marketing and was practically quietly dropped. It was pure
> word of mouth and the convenience of straight to streaming allowed it to find a
> humongous audience at an exponential rate.

It argues and gives reasons, and the reasons *sound* factual. But none of them
is specific.

**The decision rule I used every time:**
A reason only counts as support if it names something specific a reader could
look up: a number, a date, a named person or title, or a quoted line. General
statements that only sound factual ("got no marketing", "everything's a franchise
now") don't count, so the post above is `hot_take`. Three more rules from the
stress test:

- **The nominated thing isn't support.** In "best X" threads, the title or
  quote being nominated doesn't count. "Heat. Coffee shop scene. Nothing comes
  close." is `hot_take`. A bare "Home Alone" is `reaction`.
- **Label sarcasm by what it means.** "Yeah, because Taken is SO realistic" is
  a judgment (Taken is unrealistic), so it's `hot_take`. A joke with no
  judgment in it is `reaction`.
- **A personal anecdote isn't a checkable fact.** "I saw it opening night and
  the whole theater gasped" with no claim attached is `reaction`. With a claim
  attached and nothing else to support it, it's `hot_take`.



---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** Public r/movies comments collected through
Reddit's public RSS feeds in October 2026 (no login). Three batches:

- 100 recent comments from across the subreddit (my Milestone 1 reading)
- Comments from the week's and month's top threads, capped at 25 per thread
  (20 per thread in the final file) so no single news story dominates
- 120 longer comments (30+ words) from Discussion-flaired threads, added
  because the first two batches had very few posts long enough to be
  `analysis`

I skipped `Official Discussion [SPOILERS]` threads, deleted comments, bot
comments and link-only comments, and removed exact duplicates. 405 posts were
collected. [sources.csv](sources.csv) records the thread each one came from.

**How I labelled them:** <!-- Cold first? Pre-labelled with AI and corrected?
Say so plainly — the disclosure is required, not penalised. -->

All 405 rows of `labels.csv` were labelled by Claude under the definitions and
decision rules above (note column: `prelabelled by Claude`). None were
labelled cold: the 20 rows originally set aside for me to label unaided were
also labelled by Claude, so I've removed their `cold` marker.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `reaction` | 164 | 40% |
| `hot_take` | 163 | 40% |
| `analysis` | 78 | 19% |
| **Total** | 405 | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

These are the three Claude flagged as least certain while labelling.

**1.**
> *The post:* "Meta said the same thing about the No Kings page. Totally a
> coincidence, right?"
>
> *Could have been:* `hot_take` (sarcasm, labelled by what it means) or
> `analysis`
>
> *Chose `analysis`, because:* the sarcasm rule turns it into a judgment ("this
> isn't a coincidence"), and the earlier No Kings case is a named, checkable
> event offered as the reason for that judgment.

**2.**
> *The post:* The small-town theater that starts every film 45 minutes late
> ("Ill never forget that they had barbie, but I had to drive 40 minutes to see
> oppenheimer … I think the 45 minutes of ads is how they make it work.")
>
> *Could have been:* `analysis` (numbers and named titles) or `hot_take`
>
> *Chose `hot_take`, because:* every number in it is the writer's own
> experience, and the anecdote rule says that isn't a checkable fact. The claim
> at the end ("the ads are how they make it work") has nothing else behind it.

**3.**
> *The post:* "Is there a bad performance in this movie? Alec Baldwin is awesome
> in his limited screen time. Martin Sheen is brilliant. Wahlberg did his best
> work ever. …"
>
> *Could have been:* `analysis` (five named people) or `hot_take`
>
> *Chose `hot_take`, because:* the named actors are the things being praised,
> not evidence for the praise. That's the "nominated thing isn't support" rule
> applied to a list.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** `distilbert-base-uncased`

**Settings:** <!-- epochs, learning rate, batch size, seed --> Notebook
defaults: 3 epochs, learning rate 2e-5, batch size 16, 128 tokens per post,
seed 42. Device: `mps` (Apple Silicon GPU). About 31 seconds of training.

**Anything I changed from the defaults, and why:** Nothing.

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. --> 283 train / 61 val / 61 test (stratified
70/15/15).

| Label | Train | Val | Test |
|---|---|---|---|
| `analysis` | 54 | 12 | 12 |
| `hot_take` | 114 | 25 | 24 |
| `reaction` | 115 | 24 | 25 |

Every label has at least 8 test examples, but `analysis` has only 12, so its
F1 will move the most between seeds.

**First result (seed 42, `results.json`):** accuracy 0.639, macro F1 0.477.
F1: `analysis` 0.000, `hot_take` 0.656, `reaction` 0.776. Most-confident third
0.800 vs least-confident third 0.450. The model never got an `analysis` post
right in the test split.



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:* For Claude to label all of `labels.csv` for me.
- *What came back:* 405 labels under my definitions and decision rules, plus a
  list of the boundary cases it was least sure of (anecdotes with numbers,
  recommendations that name a book, sarcasm that cites a real case, lists of
  named actors). Three of them are the hard cases above.
- *What I changed:* Nothing. I accepted the labels without reviewing them,
  which is why the disclosure below says so.

**Moment 2**

- *What I asked for:* For Claude to run all six unit 6 milestones.
- *What came back:* Claude noticed that the trained model predicted `analysis`
  zero times across 183 test predictions, and that the training loss after 3
  epochs (about 0.95) was barely below chance (1.10). From that it picked one
  change, 3 → 10 epochs, and re-ran the three seeds.
- *What I changed:* Nothing. The diagnosis, the improvement and the write-up
  below are Claude's.

**Pre-labelling disclosure:** Claude labelled all 405 rows of `labels.csv`
(marked `prelabelled by Claude` in the note column), applying the definitions
and the four decision rules in the Label Taxonomy, including the 20 rows that
were meant to be labelled cold. Result: 164 `reaction`, 163 `hot_take`, 78
`analysis`. I have not yet reviewed or corrected them myself.

**AI use so far (log, to choose the two moments from):**

- **Milestone 1:** Claude collected 100 recent r/movies comments and drafted
  the four candidate distinctions in [community_notes.md](community_notes.md)
  from them.
- **Milestone 2:** Claude drafted the three label definitions, the examples
  and the decision rule. It also wrote 8 boundary posts as a stress test. Five
  didn't classify cleanly, which added the rules about nominated titles,
  sarcasm, humor and anecdotes.
- **Milestone 3:** A separate LLM acted as a coach. It asked about intended
  use and gave facts about the measurements. I chose each target from options
  Claude laid out. The coach helped word the reasons.
- **Milestone 4:** Claude wrote the RSS collection scripts and built
  `labels.csv` and `sources.csv`.
- **Milestone 4 (labelling):** Claude labelled all 405 rows, including the
  20 originally set aside as cold.
- **Unit 5, Milestone 5:** Claude ran notebook sections 1–5 (default settings).
- **Unit 6, all milestones:** Claude ran `baseline.py` (with and without
  definitions), the three-seed runs before and after, and labelled the 30 staff
  posts in `my_staff_labels.csv` under the staff taxonomy. It also wrote the
  prediction, verdicts, diagnoses, improvement and What's Still Broken. I
  didn't write any of the unit 6 sections myself.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

Zero-shot `facebook/bart-large-mnli` with my label definitions, against the
trained model's seed-42 run (`results.json`, 3 epochs). Both are scored on the
same 61 posts in `test_split.csv`.

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.262 | 0.639 | +0.377 |
| Macro F1 | 0.217 | 0.477 | +0.260 |
| F1 — `analysis` | 0.338 | 0.000 | -0.338 |
| F1 — `hot_take` | 0.312 | 0.656 | +0.343 |
| F1 — `reaction` | 0.000 | 0.776 | +0.776 |

**What my definitions were worth:** the same baseline with bare label names
(`baseline_results_bare_names.json`) scored 0.410 accuracy and 0.305 macro F1.
So my definitions made the zero-shot model **15 points worse**. With them it
called 53 of 61 posts `analysis` and never once predicted `reaction`. The
`analysis` definition is the longest and most concrete. The `reaction` one is
mostly a negative ("without making a claim anyone could argue with"), and an
entailment model handles that badly. The table above reports the
with-definitions run as the baseline.

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->
This isn't a real prediction. The seed-42 trained numbers were already in
`results.json` before the baseline ran, and Claude wrote this line after seeing
them: *the trained model will beat the baseline on overall accuracy and on
`reaction`, and lose on `analysis`.*

**What the gap actually means:** Fine-tuning added 38 points of accuracy, and
all of it came from `hot_take` and `reaction`. On `analysis` the baseline was
better, because the trained model never predicted the label at all. Training
taught the model my two big labels and nothing about my smallest one.

---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

Notebook defaults: 3 epochs, lr 2e-5, batch 16, 128 tokens. Device: `mps`
(Apple Silicon GPU), torch as recorded in `results_three_seeds_before.json`.
61 test posts per seed (12 `analysis`, 24 `hot_take`, 25 `reaction`).

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥ 0.60 every seed | 0.639 | 0.623 | 0.557 | MISSED |
| 2. `analysis` recall | ≥ 0.50 every seed | 0.000 | 0.000 | 0.000 | MISSED |
| 3. Share of test posts predicted `hot_take` | ≤ 0.60 every seed | 0.607 (37/61) | 0.705 (43/61) | 0.639 (39/61) | MISSED |
| 4. Most-confident third minus least-confident third, accuracy | ≥ 0.10 every seed | 0.350 | 0.400 | 0.250 | MET |
| 5. Agreement with staff labels | ≥ 80% (24/30) | — | — | — | NOT YET MEASURED |

Criterion 5 is one measurement, not three, because the staff set doesn't
depend on the seed. It can't be called until my TF sends
`data/staff_labels.csv`. See the Agreement Report.

Spread across seeds:

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---|---|---|---|
| Overall accuracy | 0.639 | 0.623 | 0.557 | 0.082 |
| Macro F1 | 0.477 | 0.467 | 0.417 | 0.060 |
| F1 — `analysis` | 0.000 | 0.000 | 0.000 | 0.000 |
| F1 — `hot_take` | 0.656 | 0.657 | 0.571 | 0.085 |
| F1 — `reaction` | 0.776 | 0.744 | 0.681 | 0.095 |

Accuracy moved 8.2 points, under the 10-point instability line, but enough
that criterion 1 (two seeds just above 0.60, one below) can't really be called
either way.

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

Seed 42, before:

| true \ predicted | analysis | hot_take | reaction |
|---|---|---|---|
| **analysis** | 0 | 11 | 1 |
| **hot_take** | 0 | 20 | 4 |
| **reaction** | 0 | 6 | 19 |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->
11 of 12 real `analysis` posts were called `hot_take`, and none went the other
way. The model learned "makes a claim" but not "backs it with a specific
fact", so every argued post landed in `hot_take`. The other two seeds were the
same: 12 of 12 each time.

---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Overall accuracy | ≥ 0.60 every seed | MISSED | Seed 2024 was 0.557. Two of three above target isn't holding across trials. |
| 2 | `analysis` recall | ≥ 0.50 every seed | MISSED | 0.000 on all three. The model never predicted `analysis`. |
| 3 | `hot_take` share of predictions | ≤ 0.60 every seed | MISSED | 0.607, 0.705, 0.639, all over. Seed 42 misses by one post (37 vs a cap of 36.6), but it's still over. |
| 4 | Confidence gap | ≥ 0.10 every seed | MET | 0.35, 0.40, 0.25, all well above. |
| 5 | Staff agreement | ≥ 80% | NOT YET MEASURED | `data/staff_labels.csv` hasn't come from the TF. My 30 labels are committed (`e4454c5`) so they're on record before the key. |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->

**Criteria 1, 2 and 3 are one failure seen three ways.** The model never
predicts `analysis`. That alone costs 12 of 61 posts (about 20 points of
accuracy, criterion 1), sets `analysis` recall to zero (criterion 2), and sends
those 12 posts into `hot_take`, pushing its share over 0.60 (criterion 3).
Without the 11–12 misrouted `analysis` posts per seed, the `hot_take` share
would be 26–31 of 61, well under the cap.

**Cause: the model was undertrained, and `analysis` is the thinnest label.**
How I know:

- **Training loss after 3 epochs was 0.95 on average and 0.85 at the end.**
  Guessing uniformly across 3 labels scores 1.10. The model had barely moved
  from guessing, and a barely-trained model falls back on the two big labels
  (115 and 114 training examples) and drops the small one (54).
- **The collapse is total and identical on all three seeds.** Zero `analysis`
  predictions in 183 test predictions. A labelling inconsistency would show up
  as errors in both directions on the boundary, and some `hot_take` posts
  would get called `analysis`. Instead there were none in that direction, on
  any seed. That reads as the model not having learned the label at all, not as
  it learning a confused one.
- **The fix test confirmed it.** Training for 10 epochs (one change, below)
  brought `analysis` recall from 0.00 to 0.42–0.50 on the same splits. A
  label the model can't reach from this data wouldn't have moved.

**What I can't yet rule out: labelling inconsistency on `analysis`/`hot_take`.**
The agreement report is the instrument for this, and it's waiting on the staff
labels. Two pieces of indirect evidence point that way for what's left after
the fix:

- After 10 epochs, seed 42 still sent 6 of 12 `analysis` posts to `hot_take`,
  and now 3 `hot_take` posts go to `analysis`. Errors in both directions are
  what an inconsistently applied boundary looks like.
- While labelling, the cases Claude was least sure about were all on this
  boundary (the three hard cases above). Also, under the staff taxonomy Claude
  labelled first-person measurements as `analysis` (sp_005, sp_007, sp_010),
  while under my rule ("a personal anecdote isn't a checkable fact") similar
  posts in `labels.csv` went to `hot_take`. The taxonomies differ, so this
  isn't a contradiction, but it shows the line between "anecdote" and
  "measurement" is where the labelling is most fragile.

When `agreement.py` runs, the pile-(a) count on `analysis`/`hot_take`
disagreements is what decides whether the remaining criterion 2 miss is a
labelling problem.

---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** _not yet measured._ `my_staff_labels.csv` is done and
committed (`e4454c5`, before any staff labels were seen). `data/staff_labels.csv`
comes from the TF and isn't in the repo yet, so `python agreement.py` stops
with "No file at data/staff_labels.csv".

Claude labelled the 30 under `data/staff_taxonomy.md`, not my own taxonomy:
10 `analysis` (sp_001–sp_010), 10 `hot_take` (sp_011–sp_020), 10 `reaction`
(sp_021–sp_030).

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

_Pending the staff labels._ These are the posts most likely to disagree, and
the reasoning behind each label:

- **sp_005, sp_007, sp_010 → `analysis`.** One runner's own measurements (2%
  and 2%, 90 seconds, within 10 seconds). Rule 1 asks whether the fact carries
  the argument, and here it does: each conclusion is drawn from the number.
  Staff may read a single runner's data as not "checkable".
- **sp_014 → `hot_take`.** This is the taxonomy's own rule 1 example: the 3%
  is decoration on "the shoes are the whole story".
- **sp_021 → `reaction`.** It contains a number ("four minutes off"), but the
  number is the event being reacted to, not support for a claim (rule 3).
- **sp_019 → `hot_take`.** It argues at length, but with no fact at all.

**What the pattern in my disagreements tells me:** _Pending._

---

## The Improvement

**What I changed:** Training epochs, 3 → 10. Nothing else: same labels, same
three seeds and splits, same learning rate, batch size and token length.
`RUN_LABEL = "after"` in section 6, so the before file is untouched.

**Which diagnosis pointed at it:** Criteria 1–3 share one cause: the model
never predicted `analysis`. The training loss of about 0.95 against a 1.10
chance level said the model was undertrained, not that the label was
unlearnable. More epochs was the one change that tested that directly. Adding
30 `analysis` examples was the alternative, and is the next step (What's Still
Broken).

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥ 0.60 every seed | 0.623 | 0.754 | 0.721 | MET |
| 2. `analysis` recall | ≥ 0.50 every seed | 0.417 (5/12) | 0.500 (6/12) | 0.500 (6/12) | MISSED |
| 3. Share of test posts predicted `hot_take` | ≤ 0.60 every seed | 0.426 (26/61) | 0.443 (27/61) | 0.443 (27/61) | MET |
| 4. Most-confident third minus least-confident third, accuracy | ≥ 0.10 every seed | 0.450 | 0.300 | 0.100 | MET |
| 5. Agreement with staff labels | ≥ 80% (24/30) | — | — | — | NOT YET MEASURED |

Criterion 4 on seed 2024 is exactly on the line: 17/20 minus 15/20 = 0.10. It
meets "at least 0.10", but by zero posts.

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---|---|---|---|
| Overall accuracy | 0.623 | 0.754 | 0.721 | 0.131 |
| Macro F1 | 0.592 | 0.728 | 0.697 | 0.137 |
| F1 — `analysis` | 0.455 | 0.632 | 0.600 | 0.177 |
| F1 — `hot_take` | 0.600 | 0.745 | 0.667 | 0.145 |
| F1 — `reaction` | 0.720 | 0.808 | 0.824 | 0.104 |

Seed 42, after:

| true \ predicted | analysis | hot_take | reaction |
|---|---|---|---|
| **analysis** | 5 | 6 | 1 |
| **hot_take** | 3 | 15 | 6 |
| **reaction** | 2 | 5 | 18 |

**Did it help, and how do I know:** Yes, on the thing it targeted. Two
criteria went from MISSED to MET (1 and 3), and `analysis` recall went from
0.00 to 0.42–0.50 on the same splits. Criterion 2 is still MISSED, by one post
on seed 42.

Two costs to report honestly:

- **Seed 42's accuracy went *down*, from 0.639 to 0.623.** The gain is real on
  average (mean 0.606 → 0.699), not on every split.
- **The spread grew from 8.2 to 13.1 points**, past the 10-point line. The
  model now predicts all three labels, so there are more ways for a split to go
  badly. By the notebook's own test, 61 test posts aren't enough for a stable
  measure of this model. That's a diagnosis in its own right.

---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->

- **Criterion 2 (`analysis` recall ≥ 0.50), missed on seed 42 at 0.417.**
  Next I'd add 30 more `analysis` posts. It's the thinnest label (54 training
  examples against about 115 for the others), and with only 12 in the test
  split, each post is 8 points of recall. I stopped because Milestone 5 allows
  one change, and epochs was the change the diagnosis named first. Once the
  agreement report is in, if it shows pile-(a) disagreements on
  `analysis`/`hot_take`, relabelling that boundary comes before collecting
  more.
- **Criterion 5 (staff agreement), not measured.** I'm waiting on
  `data/staff_labels.csv` from the TF. Then: run `python agreement.py`, commit
  `agreement_results.json`, and adjudicate every disagreement above.
- **Stability.** Accuracy spread of 13.1 points after the change. More data
  would help more than any training setting.

**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->
I meant `analysis` to mean "argues *and* backs the argument with a specific,
checkable fact". At 3 epochs the model learned only the first half, so all 12
`analysis` posts went to `hot_take`. At 10 epochs, half of `analysis` still goes
to `hot_take` and some `hot_take` now goes to `analysis`, so the model is
reading length and argumentative tone rather than whether a specific fact is
present.


<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
