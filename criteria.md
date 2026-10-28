# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1. Overall accuracy is at least 0.60 on every seed

On the held-out test split, `accuracy` in the results is at least **0.60** for
each of seeds 42, 7 and 2024. With about 30 test posts, that's no more than 12
wrong per seed.

**Why this target:** The dataset is small, with approximately 30 test examples. The model must distinguish three labels, including a difficult `analysis`/`hot_take` boundary. A 60% threshold also exceeds random guessing, although it might not exceed the majority-class baseline.

---

## 2. `analysis` recall is at least 0.50 on every seed

`per_label.analysis.recall` is at least **0.50** for each of seeds 42, 7 and
2024. With about 8 `analysis` posts in the test split, that's at least 4 caught.

**Why this target:** `analysis` may be one of the smallest classes, giving the model relatively few examples to learn from. With approximately eight `analysis` examples in the test set, each missed example changes recall substantially.

---

## 3. The model predicts `hot_take` for no more than 60% of test posts

On every seed, the predictions in the confusion matrix's `hot_take` column,
summed across all true labels and divided by `n_test`, come to at most **0.60**.

**Why this target:** `hot_take` is expected to be the largest class. The model might learn to favor it, especially when comments contain vague supporting reasons that don't meet the definition of `analysis`.

---

## 4. Confident predictions beat unsure ones by at least 10 points

On every seed, `confidence.accuracy_most_confident_third` minus
`confidence.accuracy_least_confident_third` is at least **0.10**.

**Why this target:** The confidence groups contain approximately ten examples each. A 0.10 difference represents one additional correct prediction in the high-confidence group, making the measurement sensitive to individual examples.

---

## 5. I agree with the staff labels on at least 80% of the staff set

Labelling the 30 posts in `data/staff_posts.csv` under `data/staff_taxonomy.md`,
my agreement rate from `agreement.py` is at least **80%**, so at least 24 of 30.

**Why this target:** The staff dataset includes deliberately ambiguous comments, and its taxonomy differs from mine. These differences could cause disagreement even when both labelling approaches are applied consistently.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
