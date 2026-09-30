# The Unofficial Guide

<!-- Replace this line with your name and which corpus you picked. -->

> **This file is your submission.** Fill it in as you go — most sections get
> written during the milestone that produces them, not at the end.
>
> How the starter works, and every command you'll need, is in `RUNNING.md`.
> Leave that file alone.
>
> **Paste everything as text.** No screenshots, no video. A typed table gets
> full credit; a picture of the same table gets none.
>
> Delete these instruction blocks as you replace them. The `<!-- -->` comments
> are notes to you and don't show up when the page renders — you can leave them
> or remove them.

---

# Unit 1

<!-- Three or four sentences. Which corpus you picked, and the kinds of
     questions your system answers. Write it for someone who has never seen
     this repo.

     Milestone 5. -->
## What This Does

This project is a retrieval-augmented generation (RAG) system that answers questions using information from campus life, student advice threads, and local city guides. Users can ask questions about topics such as registration, commuting, internships, and places to visit. The system retrieves the most relevant information from the selected corpus and uses it to generate a grounded answer with its source. If the retrieved information is not relevant enough, the system refuses to answer rather than making up information.
## Chunking Strategy

**Chunk size:** 800 characters (default; structure-aware chunking is used when possible)

**Overlap:** 120 characters (default)

<!-- What about YOUR documents made you pick these numbers? Short posts and
     long sectioned guides don't want the same chunking, and "800 seemed
     reasonable" earns nothing. Point at something you noticed when you read
     the documents in Milestone 1.

     If you changed your mind partway through, say so and say why. That's worth
     more than pretending you got it right first time.

     Milestone 3. -->
I realize if we just chunk them based on sizing alone, the sentences and structure of the thoughts would be cut off. Since advice, city_guides and campus already have defined structures to them. I extracted the chunk based on what makes a complete reply, or answer. For example in advice after "---" is a reply.
## Sample Chunks

<!-- Five chunks, pasted as text. Label each one and name the file it came from
     AND the function that produced it — the grader checks your code against
     what you claim here.

     `python app.py chunks -n 5` prints all three for you. Copy them straight
     across.

     Milestone 3. -->
**Chunk 1** — source: `guide_accessibility.md#0` — produced by: `chunker.py::split_documents`

```
# Getting around the region with limited mobility

An honest assessment rather than a promotional one. Some of these places are
difficult and it is better to know in advance.

## Straightforward

**Thornby Wells** is the easiest town in the region. It is flat, compact, and
everything is within three minutes of everything else. Parking is free for two
hours anywhere in town and the station is central. The pump room and gardens
are level throughout.

**Marchwood** has a modern tram network with level boarding on all four lines,
running every 8 minutes on weekdays. The city museum and covered market are both
step-free. The distances between districts are the main consideration.

**Brightwater** is level along the river and through the centre. The mill museum
is step-free. The station is a 15-minute walk from campus on flat ground, or the
shuttle meets the four busiest arrivals.
```

**Chunk 2** — source: `guide_corry_vale.md#5` — produced by: `chunker.py::split_documents`

```
# Corry Vale

Corry Vale is not a town but a valley containing four villages strung along eleven miles of road. Visitors treat it as one destination and locals emphatically do not. The largest village has 900 people and the smallest has 140.

## When to go

May to September. Outside those months the pub in the third village closes, the farm shop reduces its hours, and several footpaths become genuinely boggy rather than merely wet. The road is not gritted above the second village and is impassable in snow.
```

**Chunk 3** — source: `guide_givens_mill.md#2` — produced by: `chunker.py::split_documents`

```
# Givens Mill

Givens Mill is a village of 700 built around a working watermill that still grinds flour commercially. It is the sort of place people visit for an afternoon and then talk about for longer than the visit lasted.

## Eat and drink

A tearoom attached to the mill, open 10 to 4 daily except Tuesdays, which sells bread made from the flour ground twenty metres away and is the reason most people come. One pub, food served lunchtimes and Thursday to Saturday evenings.
```

**Chunk 4** — source: `guide_kestrelford.md#4` — produced by: `chunker.py::split_documents`

```
# Kestrelford

Kestrelford is a hill town of 12,000, an hour inland from Brightwater. It has been a market town since the 1200s and the street plan has not meaningfully changed since. This is charming on foot and difficult in a car.

## Where to stay

Two inns on the square and a handful of rooms above the pubs. Booking ahead matters between May and September and not at all otherwise. There is no accommodation of any kind within four miles of the town in either direction.
```

**Chunk 5** — source: `guide_pellew_sands.md#6` — produced by: `chunker.py::split_documents`

```
# Pellew Sands

Pellew Sands is a Victorian seaside resort that has been through three distinct lives: fashionable, then neglected, and now something in between. The architecture is from the first period and much of the infrastructure from the second.

## Practical notes

Cash is still useful at the market and in smaller places, though cards are
accepted almost everywhere now. Mobile coverage is good in the centre and
patchy on the outskirts. The nearest full hospital is in Brightwater; there is
a minor injuries unit locally with limited hours.
```

## Sample Answer

<!-- One complete question and answer, pasted as text, with the source line
     visible. Milestone 4. -->

**Question:** How should I set up my courses if I commute everyday

**Answer:** According to `thread_commuting.txt`, you should stack your courses so that you have three long days instead of five short ones.

```
```

**My relevance cutoff:**

I used a relevance cutoff of **0.6**.

For the five in-scope test questions, the best retrieval distances were:

- 0.2409
- 0.4038
- 0.3477
- 0.2767
- 0.4510

For the five out-of-scope questions tested against the city guides corpus, the best distances were:

- 0.8896
- 0.9032
- 1.0423
- 0.8497
- 0.8469

The highest in-scope distance was 0.4510, while the lowest
out-of-scope distance was 0.8469. This left a clear gap between
relevant and unrelated questions. I kept the cutoff at 0.6 because
it falls within this gap: all five in-scope questions passed the
gate, while all five out-of-scope questions were rejected.

A cutoff that is too low could reject questions that the documents
can answer, while a cutoff that is too high could allow unrelated
questions through and cause the system to generate unsupported
answers.
<!-- The number you set in config.py, and how you got there.

     You ran five questions your corpus covers and the five in OUT_OF_SCOPE
     that it clearly doesn't, and wrote down the best distance for each. What
     did those two groups look like? Where was the gap? Put the actual numbers
     here — the table below wants all ten rows.

     Milestone 4. -->

| Question | In corpus? | Best distance |
|---|---|---|
|  |  |  |

## How I Used AI
I used AI to help me understand how to replace the starter's fixed-size chunking with corpus-specific chunking. I showed AI the different document structures, and it suggested splitting advice threads by replies and city guides by Markdown sections while keeping the short campus documents together. After testing the output, I noticed that the city guide chunks were repeating too much introductory text, so I changed the approach to preserve only the guide title with each section.

I also used AI while evaluating my retrieval results and choosing a relevance cutoff. I gave it the distances returned by my in-scope and out-of-scope test questions, and it helped me understand what the distances meant and identify the gap between the two groups. My in-scope questions had best distances from 0.2409 to 0.4510, while my out-of-scope questions tested against the city guides ranged from 0.8469 to 1.0423. Based on those results, I decided to keep the 0.6 cutoff because it accepted all of my test questions while rejecting the unrelated questions.


**1.**

**2.**

<!-- ── Stretch features ─────────────────────────────────────────────────────
     Doing one? Say so here BEFORE you start. A feature this README never
     claims earns nothing.
     ───────────────────────────────────────────────────────────────────────── -->

---

# Unit 2

<!-- These sections get ADDED to what's already above. Don't delete or rewrite
     unit 1 — the point is that someone can see what you said before you knew
     how it went. -->

## Run Log — Before

<!-- Your five criteria, three runs each. `python run_eval.py --label before`
     runs the questions, puts the OUT_OF_SCOPE ones through the gate, and
     writes it all into results/ for you. Targets come from criteria.md; the
     verdict column is your call.

     Criterion 3 is measured in one deterministic pass rather than three, so
     the same number goes in all three run columns. That's correct, not lazy.

     Milestone 1. -->

|| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunks contain the answer | 4 of 5 | 2 of 5 | 2 of 5 | 2 of 5 | **MISSED** |
| 2. Every answer names a source | 5 of 5 | 2 of 5 | 2 of 5 | 2 of 5 | **MISSED** |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | **MET** |
| 4. Chunks preserve complete sentences and thoughts | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | **MET** |
| 5. Answers contain expected information | 3–4 of 5 | 2 of 5 | 2 of 5 | 2 of 5 | **MISSED** |

<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->

## Real output

This is what the system actually produced. Paste the relevant parts
into your README underneath the table — the rubric asks for real
output as text, not a description of it.

### What do students say about summer internship timing to apply? — run 1

- Best distance: 0.2409 (passed the gate)
- Sources retrieved: thread_first_gen.txt, thread_first_year_regret.txt, thread_internship_timing.txt

```
According to the documents, large employers close their summer internship applications in October and November of the previous year. Meanwhile, smaller and local places typically hire in February and March. 

Source: `thread_internship_timing.txt`
```

### What do students say about summer internship timing to apply? — run 2

- Best distance: 0.2409 (passed the gate)
- Sources retrieved: thread_first_gen.txt, thread_first_year_regret.txt, thread_internship_timing.txt

```
According to the documents, large employers close their applications for summer internships in October and November of the preceding year, while smaller and local places tend to hire in February and March. 

Source: `thread_internship_timing.txt`
```

### What do students say about summer internship timing to apply? — run 3

- Best distance: 0.2409 (passed the gate)
- Sources retrieved: thread_first_gen.txt, thread_first_year_regret.txt, thread_internship_timing.txt

```
Based on the documents provided, large employers close their internship applications in October and November for the following summer, while smaller and local places hire in February and March. 

Source: thread_internship_timing.txt
```

### When should I make an appointment with advisor to lift registration hold? — run 1

- Best distance: 0.6467 (refused by the gate)
- Sources retrieved: thread_first_year_regret.txt, thread_internship_timing.txt

```
I don't have enough information about that.
```

### When should I make an appointment with advisor to lift registration hold? — run 2

- Best distance: 0.6467 (refused by the gate)
- Sources retrieved: thread_first_year_regret.txt, thread_internship_timing.txt

```
I don't have enough information about that.
```

### When should I make an appointment with advisor to lift registration hold? — run 3

- Best distance: 0.6467 (refused by the gate)
- Sources retrieved: thread_first_year_regret.txt, thread_internship_timing.txt

```
I don't have enough information about that.
```

### Where do people stay when they visit Givens Mill? — run 1

- Best distance: 0.7084 (refused by the gate)
- Sources retrieved: thread_commuting.txt, thread_laundry_timing.txt, thread_study_spots.txt, thread_winter_advice.txt

```
I don't have enough information about that.
```

### Where do people stay when they visit Givens Mill? — run 2

- Best distance: 0.7084 (refused by the gate)
- Sources retrieved: thread_commuting.txt, thread_laundry_timing.txt, thread_study_spots.txt, thread_winter_advice.txt

```
I don't have enough information about that.
```

### Where do people stay when they visit Givens Mill? — run 3

- Best distance: 0.7084 (refused by the gate)
- Sources retrieved: thread_commuting.txt, thread_laundry_timing.txt, thread_study_spots.txt, thread_winter_advice.txt

```
I don't have enough information about that.
```

### When do students go to Pellew Sands? — run 1

- Best distance: 0.5916 (passed the gate)
- Sources retrieved: thread_first_gen.txt, thread_first_year_regret.txt, thread_late_work.txt, thread_study_spots.txt

```
I do not have enough information to answer this question.
```

### When do students go to Pellew Sands? — run 2

- Best distance: 0.5916 (passed the gate)
- Sources retrieved: thread_first_gen.txt, thread_first_year_regret.txt, thread_late_work.txt, thread_study_spots.txt

```
I do not have enough information to answer this question.
```

### When do students go to Pellew Sands? — run 3

- Best distance: 0.5916 (passed the gate)
- Sources retrieved: thread_first_gen.txt, thread_first_year_regret.txt, thread_late_work.txt, thread_study_spots.txt

```
I do not have enough information to answer this question.
```

### How should I set up my courses if I commute everyday — run 1

- Best distance: 0.4510 (passed the gate)
- Sources retrieved: thread_bike_commute.txt, thread_commuting.txt

```
If you have a long commute, you should stack your courses so that you have three long days instead of five short ones. (Source: thread_commuting.txt)
```

### How should I set up my courses if I commute everyday — run 2

- Best distance: 0.4510 (passed the gate)
- Sources retrieved: thread_bike_commute.txt, thread_commuting.txt

```
According to `thread_commuting.txt`, you should stack your courses so that you have three long days instead of five short ones.
```

### How should I set up my courses if I commute everyday — run 3

- Best distance: 0.4510 (passed the gate)
- Sources retrieved: thread_bike_commute.txt, thread_commuting.txt

```
According to the document `thread_commuting.txt`, you should stack your courses because three long days beats five short ones by a wide margin.
```
## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 | Retrieved chunks contain the answer | **MISSED** | Only 2 of the 5 test questions retrieved chunks containing the expected answer. This is below my target of 4 out of 5. |
| 2 | Every answer names a source | **MISSED** | Only 2 of the 5 questions produced grounded answers that named a source document. The other questions returned insufficient-information responses, so this did not meet my target of 5 out of 5. |
| 3 | The relevance gate stops out-of-corpus questions | **MET** | The relevance gate correctly refused all 5 out-of-scope questions. This exceeded my target of at least 4 out of 5. |
| 4 | Chunks preserve complete sentences and thoughts | **MET** | All 5 sampled chunks preserved complete sentences and understandable thoughts, meeting my target of at least 4 out of 5. |
| 5 | Answers contain expected information | **MISSED** | 2 of the 5 questions produced answers containing the expected information from `questions.py`. This is below my target of 3–4 out of 5. |


## Diagnoses


### Criterion 1 — Retrieved chunks contain the answer

**Stage: Retrieval**

The main failure occurred during retrieval. Only 2 of the 5 questions retrieved chunks containing the expected information. The internship and commuting questions retrieved relevant documents and were answered correctly, but the advisor, Givens Mill, and Pellew Sands questions did not retrieve chunks containing their expected answers. Because the correct evidence was missing before generation, the model did not have enough information to answer those questions correctly.

### Criterion 2 — Every answer names a source

**Stage: Retrieval / Generation**

This criterion was missed as a consequence of the retrieval failures. When relevant evidence was retrieved, such as `thread_internship_timing.txt` and `thread_commuting.txt`, the generated answers named their sources. For the questions where the needed evidence was not retrieved, the system instead returned that it did not have enough information. This suggests that source attribution itself works when the system has useful retrieved context, but failed retrieval prevented the system from producing a grounded, sourced answer for all five questions.

### Criterion 5 — Answers contain expected information

**Stage: Retrieval**

Only 2 of the 5 questions produced answers containing the expected information. This follows the same pattern as Criterion 1: the internship and commuting questions retrieved useful evidence and generated answers containing the expected information, while the other three questions did not retrieve the evidence needed for their expected answers. The problem therefore appears earlier in the pipeline at retrieval rather than generation, because the generator performed correctly when it received relevant chunks.

### Overall pattern

The misses point to retrieval as the main weakness in the current system. The generator appears capable of producing grounded answers and citing sources when the correct chunks are available. Therefore, my improvement should focus on increasing the chance that retrieval finds the relevant chunks rather than changing the generation prompt.


## The Improvement

**What I changed:**

I increased the number of chunks returned by retrieval (`TOP_K`) from 5 to 8 while keeping the relevance cutoff, chunking strategy, embedding model, and generation process unchanged.

**Why I picked it:**

My diagnoses showed that retrieval was the main source of failure. When the correct evidence was retrieved, the system was able to generate an appropriate answer and cite its source. I increased `TOP_K` to test whether retrieving more candidate chunks would allow relevant information that was previously outside the top five results to reach the generation stage.


### Run Log — After

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunks contain the answer | 4 of 5 | 2 of 5 | 2 of 5 | 2 of 5 | **MISSED** |
| 2. Every answer names a source | 5 of 5 | 2 of 5 | 2 of 5 | 2 of 5 | **MISSED** |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | **MET** |
| 4. Chunks preserve complete sentences and thoughts | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | **MET** |
| 5. Answers contain expected information | 3–4 of 5 | 2 of 5 | 2 of 5 | 2 of 5 | **MISSED** |

**Real output:** Produced by `run_eval.py::main` using `store.py::search` for retrieval and `chunker.py::split_documents` for chunks.

For the internship question, the system continued to retrieve `thread_internship_timing.txt` and correctly answered that large employers close applications in October and November while smaller and local employers hire in February and March.

For the commuting question, the system continued to retrieve `thread_commuting.txt` and correctly recommended stacking courses into fewer days.

The advisor and Givens Mill questions were still refused by the relevance gate, while the Pellew Sands question passed the gate but did not retrieve enough relevant information to answer.

**Did it help?**

Increasing `TOP_K` from 5 to 8 did not improve the measured acceptance criteria. The system still met Criteria 3 and 4 and missed Criteria 1, 2, and 5. Although retrieving more chunks added additional source documents for some questions, it did not retrieve the missing evidence needed to answer the three failing questions. This showed that simply increasing the number of semantic retrieval results was not enough to solve the underlying retrieval problem.



## What's Still Broken

Criteria 1, 2, and 5 are still missed after increasing `TOP_K` from 5 to 8. The system still correctly answers the internship and commuting questions, but it cannot answer the advisor, Givens Mill, and Pellew Sands questions.

Increasing the number of retrieved chunks did not solve the problem because the additional results still did not contain the missing information. For example, the Pellew Sands question retrieved more documents after the change, but those documents were still unrelated to the expected answer. If I continued improving the system, I would focus on the retrieval strategy rather than simply retrieving more semantic results. I would consider using hybrid retrieval that combines semantic similarity with keyword matching so that specific names and phrases have another way of finding relevant documents.

I stopped after this change because the assignment asks for one measured improvement. Even though the change did not improve my acceptance criteria, the experiment showed that increasing `TOP_K` alone is not enough to solve the retrieval failures.

## What I'd Do Differently

Knowing what I know now, I would make my acceptance criteria more precise about which corpus each question should be evaluated against. My test questions include questions about student advice, campus life, and city guides, while an evaluation run searches a selected corpus. Making the corpus associated with each question explicit would make retrieval failures easier to interpret.

I would also rewrite Criterion 5 to use a clearer numerical target. Instead of saying that answers should contain expected words and aiming for "3–4/5," I would define it as: **"At least 4 of 5 answers contain the expected information specified in `questions.py`."** This would make the criterion easier to score consistently as either MET or MISSED.

Finally, I would keep the relevance-gate criterion because it gave me a clear and repeatable measurement. The gate rejected all five out-of-scope questions consistently, which made it easy to determine whether that part of the system was working.


### Unit 2 AI Use

I used ChatGPT (GPT-5.6 Sol) to help me analyze my evaluation results and identify where the failed questions were breaking in the RAG pipeline. I compared the retrieved sources with the expected answers in `questions.py`. From this, I found that retrieval was the main issue because the system produced correct, sourced answers when relevant chunks were retrieved, while the failed questions did not retrieve the information needed for generation.

I also used ChatGPT to help me choose and evaluate my improvement. I increased `TOP_K` from 5 to 8 to test whether retrieving more candidate chunks would improve the results. After rerunning the evaluation, the acceptance criteria did not improve. Instead of making another change, I kept the result and concluded that increasing the number of semantic search results alone was not enough to fix the retrieval problem.