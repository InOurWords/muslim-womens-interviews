# MWI — Muslim Women's Interviews Dataset

A machine-readable corpus of 25 de-identified qualitative interviews with Muslim
women in the US, Canada and France, structured for use in LLM evaluation.

**Version** 1.0 · **Built** 2026-08-27 · Derived from `Cleaned, Redacted Transcripts/`

---

## Files

| File | Count | What it is |
|---|---|---|
| `interviews/MWI-###.md` | 25 | One Markdown file per interview. The readable view. |
| `interviews.jsonl` | 585 | The same content as structured rows: `segment_id`, `participant_id`, `country`, `question_id`, `question`, `answer`. Generated from the Markdown; every answer matches verbatim. |
| `README.md` | — | What it is, how to parse it, how to cite it. |
| `DATASHEET.md` | — | This file: provenance, limitations, ethics. |
| `LICENSE.md` / `CITATION.cff` | — | CC BY 4.0 terms and citation metadata. |

585 question/answer exchanges, ~144,000 words. Each answer carries a `segment_id`
(unique corpus-wide) and a `question_id` (shared across interviews for the 19 recurring
protocol questions) in an HTML comment, so the files parse without guesswork — see the
README for a parser.

## Composition

- **585 segments**, **143,074 words**, **25 participants**
- **19 core questions** answered by 15–25 participants each (437 segments)
- **89 follow-up questions**, mostly participant-specific (148 segments)
- Countries: **US 14 · Canada 8 · France 3**; 6 Canadian participants are in Quebec

## Segment schema

```json
{
  "segment_id": "MWI-024_s07",
  "participant_id": "MWI-024",
  "segment_index": 7,
  "question_id": "misconceptions",
  "question_text": "WHAT DO PEOPLE GET WRONG ABOUT MUSLIM WOMEN OR HIJAB?",
  "is_core_question": true,
  "answer_text": "…",
  "n_words": 191,
  "n_paragraphs": 2,
  "redactions": [{"token": "[CITY 1]", "category": "CITY", "index": 1, "compound": false}],
  "n_redactions": 1,
  "redaction_categories": ["CITY"],
  "question_redactions": []
}
```

Note: a question_id can repeat within a participant — interviewers returned to some
topics. Use `segment_id` as the key, not `(participant_id, question_id)`.

## Redaction tokens

Identifying details are replaced by typed tokens such as `[CITY 1]`, `[UNIVERSITY 2]`,
`[MASJID 1]`, `[INTERVIEWEE]`. **Numbering restarts per participant** — `[CITY 1]` in
MWI-004 and in MWI-021 are different cities. Tokens are not a cross-participant key.

Compound tokens (`[CITY 1, STATE 1]`) are decomposed into components in `redactions`
while `token` preserves the original span.

Retained by design: country names, **Quebec**, **France**, the **US**, Quebec legal and
identity vocabulary (Bill 21/94/9, CAQ, CEGEP, laïcité, Québécois), ethnicity and
nationality descriptors, and public figures cited as commentary. Full policy and the
complete change log are in `../Cleaned, Redacted Transcripts/REDACTION-NOTES.md`.

## Intended uses

This is a corpus release, not a benchmark. Three evaluation designs it supports well:

1. **Flattening / heterogeneity** — does a model reproduce the range of views 25 real
   women hold, or collapse them into one voice? The corpus's internal disagreement is
   the ground truth. Best-supported design.
2. **Misconception rejection** — grounded in the 22 `misconceptions` and 15
   `responding_to_portrayals` segments, where participants state directly what is false
   about them. Ground truth is participant-authored, not researcher-assumed.
3. **Perspective faithfulness** — compare model answers against the thematic
   distribution of real answers to the same question.

Of direct relevance: the follow-up question **"WHO GETS TO REPRESENT MUSLIM WOMEN IN
RESEARCH, MEDIA, OR AI?"** (`followup__who_gets_to_represent_muslim_women_in_research`)
was asked of 4 participants — their own views on this dataset's use case.

## Uses to avoid

- **Treating any participant as representative.** n=25, purposively sampled, three
  countries. The corpus documents variation; it does not estimate population parameters.
- **Reading tokens as linkable IDs** across participants (see above).

## Limitations and biases

- **Not a probability sample.** Recruitment ran partly through Muslim women's
  organisations, which over-represents community-involved and civically active women.
  Two participants hold public community roles.
- **Highly educated skew** — several physicians, PhD candidates, teachers and graduate
  students; few low-income or non-English-speaking participants.
- **Interviewer effects** — follow-up questions differ by participant, so absence of a
  theme in a transcript does not mean the participant lacked a view on it.
- **Prose is edited.** Source transcripts were cleaned into readable narrative before
  redaction; these are not verbatim disfluent speech and should not be used for
  conversational-realism or ASR work.
- **English throughout**, including for the French participants; some phrasing is
  translated.

## Ethics and consent

Participants consented to public release for the purpose of shaping LLM training,
outputs and analysis. De-identification was performed and independently audited before
this dataset was built; the audit and every content-level edit are logged in
`REDACTION-NOTES.md`.

**Content.** Individual interviews touch on bereavement, pregnancy loss, mental-health
disclosure, workplace discrimination, street harassment, and war and political violence.

## Contamination caveat

Public release means this text will enter future training corpora. Any benchmark built
directly on these verbatim segments will decay as models memorise it. If you need a
durable benchmark, derive paraphrased probes and hold the verbatim set back as a
contamination check, or version and date each release.

## Provenance

Raw interviews → cleaned into readable narrative → redaction and independent audit →
these files. Segmentation into question/answer units was verified lossless: every word
of the redacted prose appears in exactly one answer.

A fuller redaction log — the policy applied, every content-level edit, and the
re-identification audit — is held by the research team and is available on request.
