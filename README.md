# TakeMeter

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

TakeMeter sorts comments from r/college, a public subreddit where students ask about classes, professors, majors, and money. It labels each comment as one of three types. A `reasoned` comment builds an argument for a general point. An `experience` comment is mostly the writer's own story or how things work at their school. A `take` is a verdict, advice, or agreement with little or no reason behind it.

---

## Label Taxonomy

### `reasoned`

**Definition:** The comment makes a general point and builds an argument for it, with more than one step or a concrete example tied to the point.

**Example 1:**
> "Honestly college should be harder and not just a pass for everyone. If you want the brightest to succeed then you need to push them for success and show that not everyone can just do the bear minimum. Sure when you get to higher levels like the 3000 and 4000 its an area you actually have to work, however those 1000 and 2000 level classes are super easy the majority of the time. This should be true with Community College as well. College isn't high school where you just have to show up."

**Example 2:**
> "Take what you want unless you plan on entering a field where foreign language is really necessary. If you're going into engineering for example, there's not a whole work related reason to learn a specific language. If you're going into a field where you need to interact with the public though and employers either don't have access to or want to save money on interpreters and language lines, that's another story. Spanish really only makes you stand out in healthcare, social services, and sometimes customer service roles in areas with higher proportion of Spanish speakers."

### `experience`

**Definition:** The comment is mostly the writer's own story, or how things work at their own school, even if it ends with a lesson.

**Example 1:**
> "I was a second year CS and simply decided to drop because I completely lost touch with it all. It was no longer for me. I gave myself a few years to breathe and went to work and then jumped back into a new major unrelated to CS. That's my experience. Happier now for it."

**Example 2:**
> "In secondary school, I took French because I loved ths language, no other reason. My fluency then exempted me from the undergraduate language requirement. But in grad school, starting to think about my future career and where I was planning to work, I switched to studying Spanish for mainly practical reasons, plys a little bit because I'd started to become much more aware, and embarrassed by, the racial and linguistic tensions that had quietly divided the town I grew up in."

### `take`

**Definition:** A verdict, advice, or agreement with no reason, or with only one short reason.

**Example 1:**
> "If you don't think an A+ is better than an A, you should just drop it. You're not the type of person this was designed for."

**Example 2:**
> "I think this is odd. I wouldn't do this."

### The hardest boundary

**Which two labels:** `reasoned` and `take`

**The decision rule I used every time:**
First, if the comment gives no reason, or only one short reason, it is `take`. The exception is a concrete number or finding, which counts as an argument even in a short comment. Someone stating their own preferences or rules is also `take`, even if they explain them, because a preference is a verdict, not a reason. Next, if most of the comment is the writer's own story or a description of how things work at their own school, it is `experience`, even if it ends with advice. Otherwise, if it builds an argument for a general point, it is `reasoned`. When two labels still fit, I pick the one the comment spends most of its words on. The check is whether the reason is stated in the comment, not whether it is true.

---

## The Dataset

**Where the posts came from:**
All 200 posts are comments from 10 threads on r/college, a public subreddit. I picked threads with a mix of advice, personal stories, and debates. These threads were about various topics such as RateMyProfessor, emailing professors before class, choosing a foreign language, honors programs, Harvard's cap on A grades, a CS student feeling stuck, taking the same professor again, asking a professor about research, women's scholarships, and work-study pay. 

**How I labelled them:**
I labeled 20 comments cold first, with no help, using two from each thread. Then I used AI to pre-label the other 180 using my definitions and decision rule and I checked every one and revised any that I did not agree with. 

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `take` | 89 | 44.5% |
| `experience` | 69 | 34.5% |
| `reasoned` | 42 | 21% |
| **Total** | 200 | 100% |

**Three hard cases**

**1.**
> *The post:* "Stick with the professor you already know and like. You don't know what you're getting into with a new professor. You already have a good rapport, stay with it."
>
> *Could have been:* `take` or `reasoned`
>
> *I chose `take`, because:* it gives advice with only short reasons and never builds an argument. My cold label was `reasoned`. But I labeled a very similar comment ("Definitely don't do that. You'll be categorized as an annoying student before they even meet you.") as `take`. That showed me I wasn't drawing the line between one short reason and a real argument the same way every time.

**2.**
> *The post:* "I took English 1 and 2 with the same professor. Loved him so much I took literature with him too. I now have to take other literature classes with other professors and I'm nervous. I am familiar with his teaching style so now I have to get familiar with another one. I would stick with a familiar professor."
>
> *Could have been:* `experience` or `reasoned`
>
> *I chose `experience`, because:* almost the whole comment is the writer's own story, and it only ends with a short lesson. My cold label was `reasoned`, but I had labeled another story that ended with a verdict as `experience`. My final rule says to pick the label the comment spends most of its words on, which is the story.

**3.**
> *The post:* "There no taxes you file exempt."
>
> *Could have been:* `take` or `reasoned`
>
> *I chose `take`, because:* it is one short statement with nothing behind it. It states a rule, but it doesn't explain it or connect it to a bigger point. I went back and forth on this one, along with two other short work-study comments, and decided all three should be `take` so the rule stays consistent.

---

## The Training Run

**Base model:** `distilbert-base-uncased`

**Settings:** 3 epochs, learning rate 2e-5, batch size 16, seed 42, and 128 tokens per post. I trained on `mps` (Apple Silicon GPU) with PyTorch 2.14.1. The training took about 1 minute.

**Anything I changed from the defaults, and why:** Nothing. I only changed `LABELS` to match my three labels: `reasoned`, `experience`, and `take`.

**Split sizes:** train 139 / val 31 / test 30.

| Label | Train | Val | Test |
|---|---|---|---|
| `reasoned` | 30 | 6 | 6 |
| `experience` | 47 | 11 | 11 |
| `take` | 62 | 14 | 13 |

`reasoned` has only 6 examples in the test split, which is fewer than 8. A score computed on that few examples will move a lot between seeds, so I expect its F1 to be jumpy next unit.

---

## How I Used AI

**Moment 1**

- *What I asked for:* I asked AI to compare my 20 cold labels against the decision rule I had written in my README, and say whether my labels actually followed it.
- *What came back:* It showed that 11 of my 20 labels did not follow my rule. My rule said a comment was `reasoned` only if it had a checkable fact, like a number, a named policy, or a study. But I was calling comments `reasoned` whenever they gave any reason at all, including logic or a personal story used as evidence.
- *What I changed:* I realized my decision rule did not match how I really labeled. I revised it so `reasoned` meant any comment that gives a reason to argue a general point, whether that reason is a fact, logic, or the writer's own story.

**Moment 2**

- *What I asked for:* After revising my decision rule and labels, I asked AI to label the remaining 180 comments under it and check the result against my criteria.
- *What came back:* The labels were lopsided. `reasoned` grew to 56% of my data, which broke my balance criterion. AI suggested a different rule based on the patterns in my cold labels: `reasoned` only when a comment builds a real argument, `experience` when it is mostly the writer's own story, and `take` when it gives no reason or one short reason. 
- *What I changed:* I switched to that rule and revised all three label definitions and my decision rule in the README. I also rewrote the reasons for criteria 3 and 5 so they describe my new hardest boundary: whether a comment gives one short reason or builds a real argument. 

**Pre-labelling disclosure:**

I labeled 20 comments cold with no help. After this, I used to AI to then then pre-labeled the other 180 using my updated label definitions and decision rule. I reviewed every one and revised any that I did not agree with.  

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy |  |  |  |
| Macro F1 |  |  |  |
| F1 — `label_one` |  |  |  |
| F1 — `label_two` |  |  |  |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

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

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



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
