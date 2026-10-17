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

## 1. Overall accuracy

Overall accuracy is at least 0.60 on the held-out set, on all three seeds.

**Why this target:** `reasoned` is my biggest label, at about 45% of my data. A
model that always guesses `reasoned` would get about 0.45. To be useful, my
model has to beat that by a clear amount. 0.60 means getting about 5 more
posts right out of 30.

---

## 2. The two big labels

`reasoned` and `take` each have an F1 of at least 0.60, on all three seeds.

**Why this target:** These are my two biggest labels. Each has about 11 to 14
posts in the test split, which is enough for a steady score. I didn't set it
higher because the line between them is my hardest boundary. Many takes give
something that looks like a reason but is really the claim said again.

---

## 3. The small label

`experience` has an F1 of at least 0.40, on all three seeds.

**Why this target:** `experience` is my smallest label, at about 18%. Only
about 5 or 6 posts land in the test split, so one wrong guess moves the score
a lot. It is also easy to mix up with `reasoned`, because many `reasoned`
comments are personal stories too. The only difference is whether the story
argues a general point. So this target is lower than the other two.

---

## 4. Balance

No label is more than 50% of labels.csv, and `experience` has at least 30 rows.

**Why this target:** After I changed my rule, `reasoned` grew to almost half
my data. Above 50%, the model could score well just by guessing `reasoned`. 30
`experience` rows is 15% of 200. That keeps about 5 in the test split. With
fewer, criterion 3 can't really be measured.

---

## 5. Consistency

My agreement with the course staff's labels on their 30-post practice set is
at least 70% (21 of 30).

**Why this target:** My labels depend on a judgment call: whether a comment
gives a real reason or just says its claim again. If I can't apply staff's
written rules at 70%, my own 200 labels probably aren't consistent either.
Staff say a rate in the 70s is normal, so 70% is fair but not easy.

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
