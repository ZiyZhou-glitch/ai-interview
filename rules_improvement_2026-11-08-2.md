# Rules improvement, 8 November 2026

On 8 November 2026, I tested the interview by answering "1" to every question, including the opening question. The interview went through all parts without stopping once, and many follow-up questions were skipped. Comparing the transcript with `config.py` showed that the AI was following the rules: the rules simply did not say what to do with an unclear or meaningless answer.

I then revised the rules and tested them again. The re-test did not cover every case, and some tests failed. Both are recorded below as they happened.

This file has three parts:

1. The rules before the revision (version 1)
2. The issues found, the changes, the test results, and what still needs to be fixed
3. The rules after the revision (version 2)

- Platform: Geiecke and Jaravel, open-source interview platform
- Model: `gpt-4.1-2025-04-14` (OpenAI API)
- Interview language: English

---

## Part 1: Rules before the revision (version 1)

The text is copied from `config.py` without changes. It covers only the parts that the revision deals with.

### 1.1 Rules that apply throughout the interview

Where these rules differ from the general instructions, these rules take precedence.

- Ask one question at a time and do not number your questions.
- Ask the questions given in double quotation marks below exactly as written.
- Apart from the follow-up questions specified below, ask at most one short follow-up question when an answer is unclear.
- Skip a follow-up question only if the respondent has already answered it clearly and specifically in an earlier answer. This applies only to follow-up questions that depend on an earlier answer; all other questions must always be asked.
- Never suggest possible answers, reasons or examples, unless the respondent asks for clarification.
- Do not mention interest rates before Part II. Do not name specific products such as savings accounts, fixed-term deposits, ETFs, stocks or cryptocurrencies unless the respondent mentions them first or they appear in a question given below.
- Never tell the respondent whether their estimates are correct, and never mention any actual interest rates or figures.
- Do not comment on whether the respondent's choices are good or bad, and do not give financial advice. If the respondent asks for advice, politely say that you cannot give advice in this interview.
- Accept "I don't know" or a skipped question. You may ask once for a best guess, but do not push further.
- For questions with a fixed scale or fixed answer options: accept any answer that clearly corresponds to one option, even with typos, abbreviations or partial wording (for example, "somewhat" for "somewhat sure"). If an answer is outside the scale, gives more than one value, or cannot be clearly matched to one option, briefly and neutrally repeat the allowed answers and ask once more. If the second answer is still unclear, accept it and move on.
- Keep your questions short and use everyday language.

### 1.2 Start of the interview

> Begin the interview with: "Hi, and thank you for taking part! In this short conversation, I'd like to learn how you handle money you don't need right away. It takes about 10 to 15 minutes. There are no right or wrong answers, and you can skip any question. Please don't share your name or other personal details. Are you ready to start?"
>
> When the respondent confirms that they are ready, begin Part I. If the respondent asks a question about the interview first, answer briefly without revealing the topics of later parts, then ask again whether they are ready.

### 1.3 Branching conditions

| Part | Condition in `config.py` | What happens |
|---|---|---|
| I | "If the respondent says that they rarely have money left over or do not save" | Asks the hypothetical question, then only the first two questions of Part II |
| I | "If the respondent says that they only keep the money in their bank account or that they do not invest" | Asks about their reasons for keeping it there (or for not investing) |
| I | "Otherwise" | Asks "What made you start doing that?" |
| II | "If the respondent said in Part I that they rarely have money left over or do not save" | Skips to Part III |
| II | "If they answer yes" (looked at other options) | Asks which options |
| II | "If they answer yes" / "If they answer no or that they do not know" (another account pays more) | Asks one of two versions of the switching question, then their reasons |
| III | "If they mention something" (heard about ways to make money) | Asks "What do you think about it?" |
| III | "If the answer is not never" (talks about money) | Asks the 1 to 10 influence question |
| III | "If they answer yes" (seen bank ads), then "After their answer, ask: 'What are your reasons for that?'" | Asks whether they opened an account because of an ad, then their reasons |
| IV | "If the respondent is a student" / "is working" | Asks about their field of study or training |
| IV | "If they are neither" | Continues with the next question |

### 1.4 Product question (Part IV)

> "Which of the following do you currently have, if any: a regular savings account such as Tagesgeld, a fixed-term deposit such as Festgeld, ETFs or investment funds, individual stocks, or cryptocurrencies? Is there anything else?"

### 1.5 Codes

> Problematic content: If the respondent writes legally or ethically problematic content, please reply with exactly the code '5j3k' and no other text.
>
> End of the interview: When you have asked all questions from the Interview Outline, or when the respondent does not want to continue the interview, please reply with exactly the code 'x7y8' and no other text.

### 1.6 General instructions that interact with these rules

These come from the platform's default `GENERAL_INSTRUCTIONS` and were not changed.

- "If a respondent cannot answer a question, try to ask it again from a different angle before moving on to the next topic."
- "Maintain forward momentum. Do not return to previously discussed topics; ensure the interview flows progressively."

---

## Part 2: Issues, changes, test results and next steps

### 2.1 Issues found in the first test

I answered "1" to every question. These are the issues in the order they appeared.

| # | Question | What the AI did | Type |
|---|---|---|---|
| 1 | Opening: "Are you ready to start?" | Treated "1" as yes and started Part I | Missing rule |
| 2 | I-1: "When you have money that you don't need right away, what do you usually do with it?" | Did not ask what "1" meant | Missing rule |
| 3 | I-1, continued | Treated "1" as "I don't save" and moved to the hypothetical question, so most of Part I and Part II was skipped | Missing rule, wrong branch |
| 4 | II-1: interest rate estimate | Accepted "1" | Data quality (1% is a valid answer) |
| 5 | II-2: "very sure, somewhat sure, or not sure at all?" | Accepted "1", possibly as the first option | Rule gap |
| 6 | III-1: where they get information | Did not ask what "1" meant | Missing rule |
| 7 | III-2: what people say is a good way to make money | Treated "1" as "nothing" and skipped "What do you think about it?" | Missing rule, wrong branch |
| 8 | III-3: "never, rarely, sometimes, or often?" | Read "1" as "never" and skipped the 1 to 10 influence question | Rule gap, wrong branch |
| 9 | III-5: seen bank ads (yes/no) | Treated "1" as no and skipped both follow-ups | Rule gap, wrong branch |
| 10 | IV-1: student or working | Treated "1" as "neither" and skipped the field-of-study question | Rule gap, wrong branch |
| 11 | IV-3: product list | Accepted "1", possibly recorded as the first product (Tagesgeld). The question also used German terms in an English interview | Rule gap, wording |
| 12 | IV-4: familiarity, 1 to 10 | Accepted "1" | Data quality (1 is a valid answer) |
| 13 | The rules as a whole | See 2.2 | Cause of issues 1 to 11 |

### 2.2 Why this happened (issue 13)

- **Every branch had a default path.** Each branch said what to do if the answer was X, but not what to do if the answer was unclear. The AI always took the path without follow-up questions.
- **Numbers were read as option positions.** The fixed-option rule said to accept answers that "clearly correspond to one option". For the AI, "1" corresponded to the first option.
- **Yes/no questions were not covered** by the fixed-option rule.
- **Several rules pushed the AI forward.** "Ask at most one short follow-up" allows zero follow-ups. "Accept … a skipped question" let the AI treat "1" as skipped. The general instruction to re-ask unanswered questions was overridden by the interview rules, which take precedence.
- **The opening rule covered only two cases**: the respondent confirms, or asks a question.

### 2.3 Changes

Changes 1 to 10 were made first. Change 11 was added after a first check showed that one of them was not followed (see 2.4).

#### Change 1: Opening rule (issue 1)

**Before**

> When the respondent confirms that they are ready, begin Part I. If the respondent asks a question about the interview first, answer briefly without revealing the topics of later parts, then ask again whether they are ready.

**After**

> Only begin Part I when the respondent clearly confirms that they are ready (for example "yes", "ready", "ok" or "let's start").
> - If the respondent asks a question about the interview first, answer briefly without revealing the topics of later parts, then ask again whether they are ready.
> - If the reply is unclear or unrelated (for example a single number or random characters), do not begin. Politely ask once more whether they are ready, and ask them to reply with "yes" or "no". If the next reply is still unclear, do not begin; say that the interview will start as soon as they reply "yes".
> - If the respondent says they are not ready yet, tell them to take their time and to reply "yes" whenever they want to start.
> - If the respondent says they do not want to take part, reply with exactly the code 'd4n1' and no other text.

**Why:** The rule now covers four cases instead of two. An unclear reply never starts the interview, and the AI does not keep asking forever.

#### Change 2: Separate code for declining (issue 1)

The existing end code `x7y8` shows "this was the last question", which does not fit someone who declines before the interview starts.

**Added to Codes**

> Declined to take part: If the respondent says at the start that they do not want to take part, please reply with exactly the code 'd4n1' and no other text.

**Added to `CLOSING_MESSAGES`**

```python
CLOSING_MESSAGES["d4n1"] = "No problem, thank you for your time. The interview ends here."
```

**Changed in Codes**, so that the two codes do not overlap:

| Before | After |
|---|---|
| … or when the respondent does not want to continue the interview … | … or when the respondent does not want to continue after the interview has started … |

#### Change 3: What counts as a skipped question (issues 2, 3, 6, 7, 10)

**Before**

> Accept "I don't know" or a skipped question. You may ask once for a best guess, but do not push further.

**After**

> Accept "I don't know" or a skipped question. Treat a question as skipped only if the respondent says so (for example "skip", "pass" or "I'd rather not say"). You may ask once for a best guess, but do not push further.

**Why:** The AI may have treated "1" as a skipped question.

#### Change 4: Meaningless answers to open questions (issues 2, 3, 6, 7)

**Before:** no rule.

**After (new)**

> - If an answer to an open question is meaningless or unrelated to the question (for example a single number or letter when the question does not ask for a number, random characters, or text that has nothing to do with the question), do not treat it as an answer. Do not use it to decide which question to ask next, and do not guess what the respondent meant. Instead, say briefly and neutrally that you did not understand the answer, and ask the same question again without suggesting possible answers. This does not count as a follow-up question. If the second answer is still meaningless, treat the question as skipped and continue with the next question that does not depend on this answer.
> - Short answers that make sense must be accepted as they are, for example "nothing", "none", "no", "not really" or "I don't know". Minor typos in an otherwise understandable answer are also fine.

**Why:**
- "When the question does not ask for a number" keeps "1" valid for the interest-rate question.
- "Do not use it to decide which question to ask next" is meant to prevent wrong branches such as issue 3.
- "This does not count as a follow-up question" stops the clarification from using up the one allowed follow-up.
- The second rule protects valid short answers, such as "nothing" for issue 7's question.

**Decision:** If the first question of Part I gets two meaningless answers, it should be treated as skipped and the respondent should go through the full Part II. The respondent has not said that they do not save, so they should not be placed in that branch. (The re-test showed that the AI does not do this yet; see T7.)

#### Change 5: Fixed answer options (issues 5, 8, 9, 10, 11)

**Before**

> For questions with a fixed scale or fixed answer options: accept any answer that clearly corresponds to one option, even with typos, abbreviations or partial wording (for example, "somewhat" for "somewhat sure"). If an answer is outside the scale, gives more than one value, or cannot be clearly matched to one option, briefly and neutrally repeat the allowed answers and ask once more. If the second answer is still unclear, accept it and move on.

**After**

> - For questions with a fixed scale or fixed answer options, including yes/no questions:
>   - Accept any answer that clearly corresponds to one option, even with typos, abbreviations, partial wording or everyday equivalents (for example "somewhat" for "somewhat sure", or "yeah" for "yes").
>   - If the options are words, do not interpret a number or letter as the position of an option in the list. For example, do not read "1" as the first option.
>   - If an answer is outside the scale, gives more than one value, or cannot be clearly matched to one option, briefly and neutrally repeat the allowed answers and ask once more.
>   - If the second answer is still unclear, treat the question as skipped. Do not guess an answer, and do not ask follow-up questions that depend on this answer.
> - In the question about which financial products the respondent currently has, more than one answer is allowed, and "none" is also a valid answer.

**Why:**
- Yes/no questions are now covered (issue 9).
- Numbers should no longer be read as option positions (issues 5, 8, 11). The 1 to 10 scales are not affected, because their options are numbers.
- "Accept it and move on" let the AI guess an answer, which caused issue 8. Unclear answers are now treated as skipped, in the same way as in Change 4.
- The old rule asked again whenever an answer gave "more than one value", which would have conflicted with the product question, where several products are allowed.

#### Change 6: Branches only on clear answers (issue 13)

**Before:** no rule.

**After (new)**

> Follow a branch below only when the respondent's answer clearly meets its condition. Do not assign the respondent to a branch based on a guess. If a question that decides a branch was skipped, do not ask the follow-up questions that depend on it, unless the outline below says otherwise.

**Why:** Changes 4 and 5 stop meaningless answers from deciding branches. This rule also covers answers that make sense but do not clearly meet a condition, and says what happens when a question is skipped.

#### Change 7: The "do not save" branch in Part I (issues 3, 13)

**Before**

> If the respondent says that they rarely have money left over or do not save, do not push. Instead ask: "If you had some money you didn't need for a few months, what would you do with it?" Then continue with Part II, where this respondent only answers the first two questions.

**After**

> If the respondent says that they rarely have money left over or do not save, and does not mention any place where they keep money, do not push. Instead ask: "If you had some money you didn't need for a few months, what would you do with it?" Then continue with Part II, where this respondent only answers the first two questions. If they mention any place where they keep money, even a small amount, follow the main path below.

**Why:** This branch skips most of Part II, so a wrong decision costs the most here. A respondent who keeps even a little money somewhere should follow the main path.

#### Change 8: Switching question in Part II when the previous question is skipped (issue 13)

**Before**

> If they answer no or that they do not know, ask: "If there were such an account, would you consider moving your money there?"

**After**

> If they answer no, that they do not know, or skip the question, ask: "If there were such an account, would you consider moving your money there?"

**Why:** Under Change 6, skipping "Do you think another bank account … would pay you more interest" would also skip the switching question and its reasons. These questions are central to the switching-cost mechanism from my bachelor's thesis, so they should always be asked.

#### Change 9: Bank ads follow-up in Part III (issue 13)

**Before**

> After their answer, ask: "What are your reasons for that?"

**After**

> After their answer to this question, ask: "What are your reasons for that?"

**Why:** "Their answer" could refer to whether they had seen ads, so respondents who had not seen any might have been asked for their reasons. The reasons question should now clearly follow only "Have you ever opened an account because of an ad like this?". The same wording in Part II was not changed, because there both paths should lead to the reasons question.

#### Change 10: English terms in the product question (issue 11)

**Before**

> "Which of the following do you currently have, if any: a regular savings account such as Tagesgeld, a fixed-term deposit such as Festgeld, ETFs or investment funds, individual stocks, or cryptocurrencies? Is there anything else?"

**After**

> "Which of the following do you currently have, if any: a savings account that you can access at any time, a savings account where your money is fixed for a set period, ETFs or investment funds, individual stocks, or cryptocurrencies? Is there anything else?"

**Why:** The interview is in English. I used plain descriptions instead of terms such as "instant-access savings account" or "fixed-term deposit", so that respondents who do not know English financial terms can still understand the question. "Regular savings account" was also removed, because a "regular saver" is a specific product in the UK.

#### Change 11: Checks next to each fixed-option question

**What happened:** After Changes 1 to 10, I ran one interview on the "do not save" path. On the question "How sure are you about that: very sure, somewhat sure, or not sure at all?", the AI still accepted "1" and moved on to Part III. The rule from Change 5 was in `config.py`, but it is written at the top of the outline, far from the question. Directly after this question comes the branch instruction "continue with Part III now", which may have pulled the AI forward. I am not sure whether I had restarted the program after the last edit, so an old version of `config.py` may also have been running.

**Change:** I added a short check directly after each fixed-option question, between the question and any branch instruction.

| Question | Added after the question |
|---|---|
| II: How sure are you | Only accept an answer that clearly matches one of these three options. If the answer is anything else, for example a number, repeat the three options and ask once more before continuing. |
| II: Looked at other options | If the answer is not clearly yes or no, ask once more before continuing. |
| II: Another account pays more | If the answer is not clearly yes, no or that they do not know, ask once more before continuing. |
| III: How often | Only accept an answer that clearly matches one of these four options. If the answer is anything else, for example a number, repeat the four options and ask once more before continuing. |
| III: Influence, 1 to 10 | If the answer is not a whole number from 1 to 10, repeat the scale and ask once more before continuing. |
| III: Seen bank ads | If the answer is not clearly yes or no, ask once more before continuing. |
| III: Opened an account because of an ad | If the answer is not clearly yes or no, ask once more before continuing. |
| IV: Student or working | If the answer does not clearly say that they are a student, working, both or neither, ask once more before continuing. |
| IV: Products | Accept one or more of these options, "none", or other products that the respondent names. If the answer is anything else, for example a number, repeat the options and ask once more before continuing. |
| IV: Familiarity, 1 to 10 | If the answer is not a whole number from 1 to 10, repeat the scale and ask once more before continuing. |

**Why:** A rule written next to the question is harder for the AI to overlook than a rule at the top of a long outline. "Before continuing" makes clear that the answer must be checked before any branch is followed.

### 2.4 Test results

After Change 11, I restarted the program and tested again on 8 November 2026. I did not run every test: some tests need a different path through the interview, and I ran out of time. Untested items are marked as such.

- **Pass**: the AI did what was expected
- **Not passed**: the AI did something else (described in the table)
- **Not tested**: I did not run this test

#### Opening

| # | Input | Expected result | Result |
|---|---|---|---|
| T1 | `1` | Does not start, asks for "yes" or "no" | Pass |
| T2 | `1` again | Does not start, says it will start after "yes" | Pass |
| T3 | `not yet` | Does not start, tells respondent to take their time | Pass |
| T4 | `I don't want to do this` | Shows the `d4n1` closing message | Pass |
| T5 | `yes` | Starts Part I | Pass |

T4 also confirms that the platform recognises the new code `d4n1`.

#### Part I

| # | Input | Expected result | Result |
|---|---|---|---|
| T6 | I-1: `1` | Says it did not understand, repeats the question | Pass |
| T7 | I-1: `1` again | Treats the question as skipped, continues with the full Part II | **Not passed.** The AI asked the hypothetical question, which puts the respondent in the "do not save" branch |
| T8 | I-1: `not much, a bit stays in my account` | Follows the main path | Pass |
| T9 | I-1: `I never have money left over` | Asks the hypothetical question | Pass (seen in two runs) |

#### Part II

This run followed the "do not save" path, so only the first two questions were asked.

| # | Input | Expected result | Result |
|---|---|---|---|
| T10 | Interest rate: `1` | Accepts it (valid number) | Pass |
| T11 | How sure: `1` ("do not save" path) | Repeats the three options and asks once more | **Not passed.** The AI no longer accepted "1", but it asked the question again twice before moving on, instead of once. It repeated the question without saying that the answer was not valid |
| T12 | How sure: `1` (main path) | Repeats the three options and asks once more | Not tested |
| T13 | How sure: `somewhat` | Accepts it | Not tested |
| T14 | Looked at other options: `1` | Asks for "yes" or "no" | Not tested |
| T15 | Another account pays more: `skip` | Still asks the switching question and the reasons | Not tested |
| — | Branch after the first two questions ("do not save" path) | Continues with Part III | Pass |

#### Part III

| # | Input | Expected result | Result |
|---|---|---|---|
| T16 | Information: `asdf` (also tested with `1`) | Says it did not understand, repeats the question | Pass |
| T17 | Ways to make money: `nothing` | Accepts it without a follow-up | Pass |
| T18 | How often: `1` | Repeats the four options and asks once more | Pass |
| T19 | How often: `1` twice | Treats as skipped, does not ask the influence question | **Not passed.** The end result was correct (the influence question was not asked), but the AI asked the question again twice instead of once |
| T20 | Influence: `22` | Repeats the scale and asks once more | Not tested (needs an answer other than "never" first) |
| T21 | Seen bank ads: `1` | Asks for "yes" or "no"; after a second `1`, skips both follow-ups | Pass |
| T22 | Seen bank ads: `yeah` | Treats as yes and asks the follow-up | Not tested |
| T23 | Seen bank ads: `no` | Asks neither the account question nor the reasons | Not tested |

#### Part IV

| # | Input | Expected result | Result |
|---|---|---|---|
| T24 | Student or working: `1` | Repeats the options and asks once more | Pass |
| T25 | Products: `1` | Repeats the options and asks once more | Pass |
| T26 | Products: `ETFs and Tagesgeld` | Accepts both | Pass |
| T27 | Products: `none` | Accepts it | Pass |
| T28 | Familiarity: `22` | Repeats the scale and asks once more | Pass |

#### Other observations

These cases were not on the test list but came up during testing.

| Situation | What the AI did | Result |
|---|---|---|
| Open question: `skip` | Treated the question as skipped and moved on | Pass |
| After the main path in Part I, I typed `go back` | Rewrote the first question in its own words and asked two questions in one message: "Could you tell me more about what you do when you have money you don't need right away? For example, what happens to that money?" | Not passed: breaks "ask exactly as written" and "one question at a time"; there is no rule for this situation |
| Then I answered `I never have money left over`, after having said earlier that "a bit stays in my account" | Followed the "do not save" branch, based on the latest answer | Not passed: under Change 7, the respondent had mentioned a place where they keep money. This was caused by my contradictory test answers and is unlikely in real interviews |
| On the hypothetical question, I typed `Lets go to part2` | Moved to Part II | Acceptable as a request to skip, but there is no rule for this |
| I typed `go to the first question of part3` | Moved to Part III | Same as above |
| As the second answer to "Where do you usually get information or tips about saving or investing?", I typed `heard people say` | Asked "What have you heard people say is a good way to make money?" instead of the exact question "In the last few months, what, if anything, have you heard people say is a good way to make money?" | Not passed: breaks "ask exactly as written". Dropping "if anything" matters, because it makes "nothing" a natural answer. The AI may have read my answer as a request to jump to that question |
| Number of repeated questions | Asked twice more on "How sure" and "How often", but only once more on "Seen bank ads" | Inconsistent behaviour |

### 2.5 Defects found and what I plan to change

| # | Defect | Planned change | Status |
|---|---|---|---|
| 1 | T7: after two meaningless answers to the first question, the AI asks the hypothetical question instead of treating the question as skipped | Add directly after the first question of Part I: "If this question is treated as skipped, do not ask the hypothetical question below. Continue with Part II and ask all of its questions." | Draft ready |
| 2 | Requests to move around the interview ("go back", "go to part 2", "go to the first question of part 3") are not covered by any rule. On "go back", the AI rewrote an earlier question | Add a rule such as: "If the respondent wants to go back to an earlier question or correct an earlier answer, let them add to or change their answer. Do not ask the earlier question again in different words. Then continue where you left off." Still to decide how to handle requests to jump forward | Draft for going back; jumping forward to be decided |
| 3 | T11, T19: after an invalid answer, the AI sometimes asks twice more instead of once, and does not always say that the answer was not valid | Probably caused by the local check ("ask once more") and the global rule ("ask once more", then treat as skipped) being counted separately. Plan: make the local checks refer to the global rule instead of repeating it, or state clearly that the question is asked again only once in total, and that the AI should say briefly why it is asking again | To be drafted |
| 4 | A question that must be asked exactly as written was shortened (the "heard people say" question) | Strengthen the rule on exact wording, for example by repeating it in the rule for move requests (defect 2): when moving to another question, still ask it exactly as written | To be drafted |
| 5 | Contradictory answers: the AI follows the latest answer | Low priority, because real respondents are unlikely to answer this way | Postponed |
| 6 | Tests not yet run: T12 to T15, T20, T22, T23 | Run these tests, mainly on the main path through Part II | To do |

### 2.6 Open questions for my professor

**Issues 4 and 12: careless answers that look valid.** For the interest-rate estimate and the 1 to 10 familiarity scale, "1" is a valid answer, so the AI cannot tell whether the respondent is answering seriously. This is a data quality question rather than a prompt problem. Haaland et al. discuss related approaches, such as screening questions, attention checks and motivational prompts. I plan to discuss this with my professor before changing anything.

---

## Part 3: Rules after the revision (version 2)

This is the state of the rules used in the re-test in 2.4: `config.py` as of 8 November 2026, including Changes 1 to 11. The defects listed in 2.5 are not fixed yet; they will go into version 3. Sections that are not listed here are the same as in version 1.

### 3.1 Rules that apply throughout the interview

Where these rules differ from the general instructions, these rules take precedence.

- Ask one question at a time and do not number your questions.
- Ask the questions given in double quotation marks below exactly as written.
- Apart from the follow-up questions specified below, ask at most one short follow-up question when an answer is unclear.
- Skip a follow-up question only if the respondent has already answered it clearly and specifically in an earlier answer. This applies only to follow-up questions that depend on an earlier answer; all other questions must always be asked.
- Never suggest possible answers, reasons or examples, unless the respondent asks for clarification.
- Do not mention interest rates before Part II. Do not name specific products such as savings accounts, fixed-term deposits, ETFs, stocks or cryptocurrencies unless the respondent mentions them first or they appear in a question given below.
- Never tell the respondent whether their estimates are correct, and never mention any actual interest rates or figures.
- Do not comment on whether the respondent's choices are good or bad, and do not give financial advice. If the respondent asks for advice, politely say that you cannot give advice in this interview.
- Accept "I don't know" or a skipped question. Treat a question as skipped only if the respondent says so (for example "skip", "pass" or "I'd rather not say"). You may ask once for a best guess, but do not push further.
- If an answer to an open question is meaningless or unrelated to the question (for example a single number or letter when the question does not ask for a number, random characters, or text that has nothing to do with the question), do not treat it as an answer. Do not use it to decide which question to ask next, and do not guess what the respondent meant. Instead, say briefly and neutrally that you did not understand the answer, and ask the same question again without suggesting possible answers. This does not count as a follow-up question. If the second answer is still meaningless, treat the question as skipped and continue with the next question that does not depend on this answer.
- Short answers that make sense must be accepted as they are, for example "nothing", "none", "no", "not really" or "I don't know". Minor typos in an otherwise understandable answer are also fine.
- For questions with a fixed scale or fixed answer options, including yes/no questions:
  - Accept any answer that clearly corresponds to one option, even with typos, abbreviations, partial wording or everyday equivalents (for example "somewhat" for "somewhat sure", or "yeah" for "yes").
  - If the options are words, do not interpret a number or letter as the position of an option in the list. For example, do not read "1" as the first option.
  - If an answer is outside the scale, gives more than one value, or cannot be clearly matched to one option, briefly and neutrally repeat the allowed answers and ask once more.
  - If the second answer is still unclear, treat the question as skipped. Do not guess an answer, and do not ask follow-up questions that depend on this answer.
- In the question about which financial products the respondent currently has, more than one answer is allowed, and "none" is also a valid answer.
- Follow a branch below only when the respondent's answer clearly meets its condition. Do not assign the respondent to a branch based on a guess. If a question that decides a branch was skipped, do not ask the follow-up questions that depend on it, unless the outline below says otherwise.
- Keep your questions short and use everyday language.

### 3.2 Start of the interview

> Begin the interview with: "Hi, and thank you for taking part! In this short conversation, I'd like to learn how you handle money you don't need right away. It takes about 10 to 15 minutes. There are no right or wrong answers, and you can skip any question. Please don't share your name or other personal details. Are you ready to start?"
>
> Only begin Part I when the respondent clearly confirms that they are ready (for example "yes", "ready", "ok" or "let's start").
> - If the respondent asks a question about the interview first, answer briefly without revealing the topics of later parts, then ask again whether they are ready.
> - If the reply is unclear or unrelated (for example a single number or random characters), do not begin. Politely ask once more whether they are ready, and ask them to reply with "yes" or "no". If the next reply is still unclear, do not begin; say that the interview will start as soon as they reply "yes".
> - If the respondent says they are not ready yet, tell them to take their time and to reply "yes" whenever they want to start.
> - If the respondent says they do not want to take part, reply with exactly the code 'd4n1' and no other text.

### 3.3 Interview parts

```text
Part I of the interview

Begin this part with: "When you have money that you don't need right away, what do you usually do with it?"

If the respondent says that they rarely have money left over or do not save, and does not mention any place where they keep money, do not push. Instead ask: "If you had some money you didn't need for a few months, what would you do with it?" Then continue with Part II, where this respondent only answers the first two questions. If they mention any place where they keep money, even a small amount, follow the main path below.

Otherwise, explore how the respondent came to keep their money where they do, and whether anyone else helped them decide. If they mentioned more than one place, focus on the one they mentioned first.
If the respondent says that they only keep the money in their bank account or that they do not invest, ask: "What are your reasons for keeping it there rather than doing something else with it?" If they have explicitly said that they do not invest, you may instead ask: "What are your reasons for not investing?"
Otherwise, ask: "What made you start doing that?" (You may replace "that" with the respondent's own words for that place.)
Then ask: "Did anyone else help you decide where to keep your money?" Do not ask this if they have already said who helped them.

Then continue with Part II.


Part II of the interview

Begin this part with: "Now I'd like to talk about what your money earns. What interest rate do you think you currently get on the money in your bank account? A best guess is fine."

Then ask: "How sure are you about that: very sure, somewhat sure, or not sure at all?"
Only accept an answer that clearly matches one of these three options. If the answer is anything else, for example a number, repeat the three options and ask once more before continuing.

If the respondent said in Part I that they rarely have money left over or do not save, continue with Part III now.

Otherwise ask: "Have you ever looked at other options for where to keep your money?"
If the answer is not clearly yes or no, ask once more before continuing.
If they answer yes, ask: "What options did you look at?"

Then ask: "Do you think another bank account, just as safe and just as easy to access, would pay you more interest than yours?"
If the answer is not clearly yes, no or that they do not know, ask once more before continuing.
If they answer yes, ask: "Would you consider moving your money there?"
If they answer no, that they do not know, or skip the question, ask: "If there were such an account, would you consider moving your money there?"
After their answer, ask: "What are your reasons for that?" Do not ask this if they already gave their reasons in that answer.

Then continue with Part III.


Part III of the interview

Begin this part with: "Where do you usually get information or tips about saving or investing?"

Then ask: "In the last few months, what, if anything, have you heard people say is a good way to make money?"
If they mention something, ask: "What do you think about it?"

Then ask: "How often do you talk with friends or family about saving or investing: never, rarely, sometimes, or often?"
Only accept an answer that clearly matches one of these four options. If the answer is anything else, for example a number, repeat the four options and ask once more before continuing.
If the answer is not never, ask: "On a scale from 1 to 10, where 1 means 'not at all' and 10 means 'very much', how much do these conversations influence what you do with your money?"
If the answer is not a whole number from 1 to 10, repeat the scale and ask once more before continuing.

Then ask: "Have you seen any ads from banks offering interest on savings?"
If the answer is not clearly yes or no, ask once more before continuing.
If they answer yes, ask: "Have you ever opened an account because of an ad like this?"
If the answer is not clearly yes or no, ask once more before continuing.
After their answer to this question, ask: "What are your reasons for that?" Do not ask this if they already gave their reasons in that answer.

Then continue with Part IV.


Part IV of the interview

Begin this part with: "To finish, I have a few short questions about you. You can skip any of them. Are you currently a student, or are you working?"
If the answer does not clearly say that they are a student, working, both or neither, ask once more before continuing.
If the respondent is a student, ask: "What do you study?"
If the respondent is working, ask: "What did you study or train in?"
If the respondent is both studying and working, treat them as a student. If they are neither, continue with the next question.

Then ask: "Which of the following do you currently have, if any: a savings account that you can access at any time, a savings account where your money is fixed for a set period, ETFs or investment funds, individual stocks, or cryptocurrencies? Is there anything else?"
Accept one or more of these options, "none", or other products that the respondent names. If the answer is anything else, for example a number, repeat the options and ask once more before continuing.

Then ask: "On a scale from 1 to 10, where 1 means 'not familiar at all' and 10 means 'very familiar', how familiar are you with topics like saving, interest rates and investing?"
If the answer is not a whole number from 1 to 10, repeat the scale and ask once more before continuing.
```

### 3.4 Codes

> Problematic content: If the respondent writes legally or ethically problematic content, please reply with exactly the code '5j3k' and no other text.
>
> Declined to take part: If the respondent says at the start that they do not want to take part, please reply with exactly the code 'd4n1' and no other text.
>
> End of the interview: When you have asked all questions from the Interview Outline, or when the respondent does not want to continue after the interview has started, please reply with exactly the code 'x7y8' and no other text.

```python
CLOSING_MESSAGES["5j3k"] = "Thank you for participating, the interview concludes here."
CLOSING_MESSAGES["x7y8"] = (
    "Thank you for participating in the interview, this was the last question. Many thanks for your time and help with this research project!"
)
CLOSING_MESSAGES["d4n1"] = "No problem, thank you for your time. The interview ends here."
```
