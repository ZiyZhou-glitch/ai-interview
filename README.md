# AI-led interview pilot on saving and investment

Earlier this month, I built a small AI-led interview about how people handle money they don't need right away. It runs on the open-source interview platform by Geiecke and Jaravel and uses the OpenAI API. My part is the interview outline (the `INTERVIEW_OUTLINE` in `config.py`) and testing it. This is a first try, not a finished study.

## Motivation

My bachelor's thesis looked at how euro-area households shift deposits between overnight and fixed-term accounts when interest rates change. I wanted to see whether an open-ended interview can capture the reasons people give in their own words.

## Interview Outline

| Part | Topics |
|---|---|
| I | What respondents do with money they don't need right away, why, and whether anyone helped them decide |
| II | What interest rate they think they get on their bank account and how sure they are; whether they have compared options; whether they would move to an equally safe, equally accessible account with higher interest |
| III | Where they get information about saving and investing; what people currently say is a good way to make money; how often they talk about money with friends or family; bank advertisements |
| IV | Student or working, field of study, which financial products they hold, how familiar they are with these topics |

## Testing and changes

I tested the interview several times myself, answering as different personas, and once with another person. Two of my own test transcripts are in `pilot_self_tests/`; the first one used an earlier version of the outline. I did not include the other person's transcript.

After each test I changed the outline:

| What happened | What I changed |
|---|---|
| "How did you start doing it this way?" sounded like a question about opening an account, not about reasons | Asked about the reason instead, with a separate question for people who only keep money in their bank account |
| A yes/no question only got "yes", so I never learned what the respondent had heard | Rephrased it as an open question |
| The AI combined two follow-up questions into one, and only the first was answered | Wrote each follow-up as its own question |
| The AI asked follow-ups that had already been answered | Added a rule to skip them |
| "22" was accepted on a 1–10 scale | Added a rule to ask again once, but to accept typos and short answers like "somewhat" |
| I rejected the premise "just as safe" and assumed higher-interest banks are riskier | Kept the question. This could be a real reason not to switch, so I want to code it as its own category |

## Problems that remain

The changes helped, but they did not fix everything. In the test with another person, which used the revised outline:

- The respondent had already named an option, and the AI still asked "What options did you look at?"
- The confidence question has three answer options, but "80%" was accepted.
- The open question "what, if anything, have you heard…" still got only "yes".

Respondents' answering styles are hard to anticipate. Every new version of the prompt needs to be tested again, which matches the point in Haaland et al. that interview quality depends on carefully tested prompts.

## Limitations and open questions

- **Deployment.** The interview only runs on my own computer, so respondents have to answer on my machine. The platform can be hosted online, but I have not tried this yet.
- **Analysis.** I have only a few test transcripts and have not analysed them yet.
- **Sample.** Many students have little savings and no investment experience. I am not sure how much meaningful data a student sample can give on depositor behaviour.

## How to run

Download the platform from https://github.com/friedrichgeiecke/interviews, replace its `config.py` with the one here, add your own API key in `.streamlit/secrets.toml`, and run `streamlit run interview.py`. Model: `gpt-4.1-2025-04-14`, text input only.

## Credits and licence

- The interview platform, the `GENERAL_INSTRUCTIONS`, the opening role paragraph and the outline structure are by Friedrich Geiecke and Xavier Jaravel: https://github.com/friedrichgeiecke/interviews (paper: https://ssrn.com/abstract=4974382). Their code is licensed under the PolyForm Noncommercial License 1.0.0 (https://polyformproject.org/licenses/noncommercial/1.0.0). The modified `config.py` here is shared under the same terms, for non-commercial use only.
- The interview content, branching and added rules are my own design. I used an AI assistant (Claude) to discuss design choices and to draft the English wording.
