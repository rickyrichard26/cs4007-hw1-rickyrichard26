# HW1 submission

**Name:Ricky Richard Takahindangen**
**Student ID: 26078831**
**Group:**
**Repository:* https://github.com/rickyrichard26/cs4007-hw1-rickyrichard26.git *

## AI tool disclosure
I used ChatGPT as an AI assistance tool during this assignment.

The assistance was used mainly for:
- understanding the assignment requirements;
- troubleshooting Python environment and dependency issues;
- explaining code and experiment outputs;
- helping organize and present my own experiment results in `SUBMISSION.md`.

The implementation, execution of the experiments, API calls, and interpretation of the actual results were performed and verified by me. API keys were kept in `.env` and were not committed to the repository.

>

---

## Sublab Easy — the registration bot and its bill
**How I laid the catalogue out inside the system prompt, and why:**

I placed the course catalogue directly inside the system prompt as structured course information, including the course code, course name, credits, schedule, capacity, and registration-related details. This gives the model the catalogue as its reference data and keeps the user messages focused on registration requests. Putting the catalogue in the system prompt also makes the rules and available course information persist across all conversation turns.

>

**My turn 5 (Kazakh or Russian):**
My fifth user-written turn was:

> Я студент третьего курса. На какие курсы я всё ещё могу зарегистрироваться?

This asks the chatbot which courses are still available for a third-year student.

>

### Run 1 — OpenAI, `gpt-5.6-luna`

| Turn | Input tokens | Output tokens | Cost $ |
|---|---|---|---|
| 1 | 736 | 506 | $0.000754 |
| 2 | 968 | 80 | $0.000296 |
| 3 | 1040 | 73 | $0.000300 |
| 4 | 1123 | 25 | $0.000261 |
| 5 | 1174 | 320 | $0.000625 |
| **total** | 5160 | 1004 | $0.002237 |

The input token count increased across the conversation because the chatbot sends the growing conversation history together with each new user message.


### Run 2 — OpenRouter, `google/gemma-4-26b-a4b-it:free`

The five-turn conversation was attempted using `google/gemma-4-26b-a4b-it:free`.

Turn 1 completed successfully:

| Turn | Input tokens | Output tokens | Cost |
|---|---:|---:|---:|
| 1 | 824 | 123 | $0.000000 |

The run then stopped at Turn 2 because OpenRouter returned HTTP 429. The error reported that the Google AI Studio upstream provider was temporarily rate-limited through the shared pool.

Therefore, the Gemma run could not produce a complete five-turn conversation, and I did not fabricate results for the missing turns.

### Turn 4, verbatim

Registration refused: **CSS-4090 does not exist in the 2026-FALL course catalogue**.

**OpenAI:**

```

```

**OpenRouter:**

```

```

### Written answers

**1. The two providers used almost identical code. What actually changed, and
what did not?**

The same chatbot implementation and conversation flow were used for both providers. The main difference was the model/provider used to generate the responses. The OpenAI run used GPT-5.6 Luna, while the OpenRouter run used Gemma. The system prompt, course catalogue, conversation logic, and five-turn sequence did not change. The OpenAI run completed all five turns, while the Gemma run was interrupted by an HTTP 429 upstream rate-limit error.

>

**2. Why did the input token count climb on every turn when your questions
stayed roughly the same length? Use the numbers from your own table. What
happens to the bill at fifty turns?**

The input token count increased because the chatbot sends the growing conversation history with every new request, not only the latest user question. In my Luna run, the input token count increased from 736 tokens on Turn 1 to 999 on Turn 2, 1,064 on Turn 3, 1,156 on Turn 4, and 1,205 on Turn 5. Therefore, even if the new questions are short, the previous messages continue to be included in the prompt. Over fifty turns, the amount of input processed can become much larger because the conversation history keeps growing, so the total input-token cost also increases substantially.

>

**3. Turn 4: did the bot refuse, or did it invent CSS-4090?** If it refused, what
in your system prompt held the line? If it invented, what did it make up —
credits, a room, an instructor?

The bot refused to register CSS-4090. It responded: “CSS-4090 does not exist in the 2026-FALL course catalogue.” The system prompt instructed the model to use the provided course catalogue as the source of truth and not invent course information. This helped prevent the model from making up details such as credits, rooms, or instructors for a non-existent course.

>

**4. Where else was either bot wrong?** Turn 2 asks for two courses that meet at
the same hour; two courses in the catalogue are full. Did the bots notice?

On Turn 2, the Luna bot correctly noticed that CSS-4007 and CSS-4102 overlap on Tuesday from 09:00–10:50, and it refused to register both courses together. It also identified that CSS-4400 was full. In the Gemma run, only Turn 1 completed successfully before the provider returned an HTTP 429 rate-limit error, so there is not enough evidence to evaluate its Turn 2 response.

Another issue in the Luna responses was that some answers added punctuation or wording that was not exactly present in the catalogue, although the main registration information remained consistent.

>

---

## Sublab Medium — one task, six models

Paste the per-model summary printed by `correct_kazakh.py`:

| Model | Exact | Failed | Tokens | Cost $ |
|---|---|---|---|---|
| google/gemma-4-26b-a4b-it:free | 0 | 8 | 0 | 0.00000 |
| qwen/qwen3.8-27b | 0 | 8 | 0 | 0.00000 |
| deepseek/deepseek-v4-flash-0731 | 7 | 0 | 21882 | 0.00590 |
| gpt-5.6-luna | 3 | 0 | 2991 | 0.00255 |
| gpt-5.6-terra | 0 | 8 | 0 | 0.00000 |
| gpt-5.6-sol | 0 | 8 | 0 | 0.00000 |

### Which error types did each model repair?

Rows are error labels, columns are models. Write "yes", "no" or "partial".

| Error type | gemma | qwen | deepseek | luna | terra | sol |
|---|---|---|---|---|---|---|
| kaz_to_rus | no | no | yes | yes | no | no |
| latin_homoglyph | no | no | yes | yes | no | no |
| drop_hyphen | no | no | yes | yes | no | no |
| join_words | no | no | yes | yes | no | no |
| double_letter | no | no | yes | yes | no | no |

**The `latin_homoglyph` row: what happened?** 
The models were able to recognize and correct the Latin homoglyphs in the corrupted Kazakh text. DeepSeek corrected the Latin A, a, and t characters in KZ-03, while Luna also identified the Latin characters and replaced them with Cyrillic characters. In KZ-08, both models corrected the Latin characters and also removed the doubled letter. However, some models were unavailable during the run because of API errors, so no correction could be observed from them.

>

**Where a model returned good Kazakh that was not identical to the original,
say so here.** Exact match is not correctness.

Several outputs were linguistically good even though they were not exact
matches. For example, Luna correctly repaired KZ-02 but added a final
period, and correctly repaired KZ-03 but also added punctuation. In KZ-05,
Luna correctly separated the joined words but added a question mark.
DeepSeek had a similar case in KZ-05, where it correctly separated the
joined words but added a question mark. Therefore, an exact-match score
can mark a linguistically correct correction as non-exact when punctuation
differs.

>

**Cheapest model that was good enough, and why:**
Using exact-match accuracy as the main criterion, DeepSeek was the only model in this run with a high exact-match result (7/8). Its recorded cost was $0.00590. Luna was cheaper at $0.00255 and successfully produced corrections for all eight sentences, but only 3/8 were exact matches. Therefore, whether Luna is considered “good enough” depends on whether linguistic correction or exact reproduction is the requirement.
>

---

## Sublab Harder — open the tokenizer

### A. What a language costs

**`cl100k_base`:**

| Language | Tokens | Chars | Tok/char | × English | $ per 1,000 sentences |
|---|---|---|---|---|---|
| kk | 200 | 263 | 0.760 | 3.75 | $0.9985 |
| ru | 129 | 277 | 0.466 | 2.30 | $0.6450 |
| en | | 59 | 291 | 1.00 | $0.2950 |

**`o200k_base`:**

| Language | Tokens | Chars | Tok/char | × English | $ per 1,000 sentences |
|---|---|---|---|---|---|
| kk | 84 | 263 | 0.319 | 1.58 |  $0.4180|
| ru | 74 | 277 | 0.267 | 1.32 | $0.3699 |
| en | 59 | 291 | 0.203 | 1.00 |  $0.2950|

### B. What a homoglyph does

One row per `latin_homoglyph` sentence in the dataset. Paste the actual decoded
token strings around the divergence point, not a description of them.

| Sentence id | Foreign char (index, name) | Tokens correct | Tokens corrupted | Δ | Diverges at |
|---|---|---|---|---|---|
| KZ-03 | A (0, LATIN CAPITAL LETTER A); a (2, LATIN SMALL LETTER A); t (5, LATIN SMALL LETTER T) |16 |20 | +4 | 0 |
| KZ-08 | o (1, LATIN SMALL LETTER O); a (3, LATIN SMALL LETTER A); T (9, LATIN CAPITAL LETTER T) | 21 | 24 | +3 | 1 |

**Token pieces around the divergence:**

```
KZ-03:
correct  : ['А', 'лая', 'қ', 'тарға', ' ақша']
corrupted: ['A', 'л', 'a', 'я', 'қ']

KZ-08:
correct:   ['Д', 'он', 'аль', 'д', ' Т', 'рамп']
corrupted: ['Д', 'o', 'н', 'a', 'л', 'ль']
```

### C. Did it get better?

| Language | cl100k_base | o200k_base | Change |
|---|---|---|---|
| kk | 0.760 | 0.319 | -0.441 |
| ru | 0.466 | 0.267 | -0.199 |
| en | 0.203 | 0.203 | 0.000 |

### Written answers

**1. What is the Kazakh tax?** 
With `cl100k_base`, Kazakh used 0.760 tokens per character compared with 0.203 for English, or 3.75× the English rate. With `o200k_base`, Kazakh decreased to 0.319 tokens per character, or 1.58× the English rate.

Therefore, the newer tokenizer reduced the Kazakh tokenization gap substantially. The tok/char ratio decreased by 0.441, from 0.760 to 0.319. This also reduced the estimated input-token cost for the same character volume.

>

**2. Why did the models repair `kaz_to_rus` but struggle with
`latin_homoglyph`?** 
The `kaz_to_rus` and `latin_homoglyph` cases may look visually similar to a human reader, but the tokenizer receives different character sequences. The homoglyph examples show that replacing Cyrillic characters with visually similar Latin characters changed the token stream and increased the token count.

For KZ-03, the corrupted version increased from 16 to 20 tokens and diverged at index 0. For KZ-08, it increased from 21 to 24 tokens and diverged at index 1. The decoded token pieces also became more fragmented, such as `['А', 'лая', 'қ', 'тарға', ' ақша']` becoming `['A', 'л', 'a', 'я', 'қ']` in KZ-03.

This shows that visually similar characters are not necessarily represented similarly at the tokenizer level.

>

**3. Name one thing this measurement does not explain about your Sublab Medium
results.** 
The tokenizer measurement does not directly explain the performance of all six models because the experiment uses OpenAI tokenizers (`cl100k_base` and `o200k_base`), while three of the six models in Sublab Medium are OpenRouter models. Different model providers and model families may use different tokenization schemes.

To close this gap, I would need to obtain and analyze the actual tokenizer used by each model, where available, and repeat the same measurements for those tokenizers.

>
