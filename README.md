---
license: cc-by-4.0
language:
  - en
pretty_name: Muslim Women's Interviews (MWI)
size_categories:
  - n<1K
configs:
  - config_name: default
    data_files:
      - split: train
        path: interviews.jsonl
tags:
  - qualitative-research
  - interviews
  - muslim-women
  - hijab
  - representation
  - llm-evaluation
  - social-science
---

# Muslim Women's Interviews (MWI)

De-identified interviews with **25 Muslim women** in the **United States, Canada and
France** — 585 question/answer exchanges, about 144,000 words of interview text.

Released with participant consent for the express purpose of shaping how large language
models represent Muslim women.

## The dataset

Two views of the same 585 question/answer exchanges:

- **`interviews/MWI-001.md` … `MWI-025.md`** — one file per interview. Readable on
  GitHub, importable into NVivo / ATLAS.ti / MAXQDA as a case document.
- **`interviews.jsonl`** — the same content as 585 structured rows, for loading.

```python
from datasets import load_dataset
ds = load_dataset("InOurWords/muslim-womens-interviews", split="train")
# 585 rows: segment_id, participant_id, country, question_id, question, answer

ds.filter(lambda r: r["question_id"] == "hijab_journey")   # 27 answers, 24 participants
ds.filter(lambda r: r["country"] == "FR")                  # 73 rows, 3 participants
```

The JSONL is generated from the Markdown by the parser below, so it is reproducible
rather than a second source of truth — every answer matches its `.md` verbatim.

Each file looks like this:

```markdown
---
country: "US"
license: "CC-BY-4.0"
---

# Interview MWI-007

## ORDINARY DAY AND WHERE FAITH SHOWS UP

<!-- segment_id: MWI-007_s00 | question_id: ordinary_day -->

An ordinary day — I am a teacher, a middle school teacher. …
```

Readable on GitHub and the Hugging Face Hub as-is, and importable into NVivo,
ATLAS.ti, MAXQDA or Dedoose as a single case document per participant.

## Regenerating the JSONL from the Markdown

The Markdown is structured so you do not have to guess. Every answer sits under an `##`
heading preceded by a comment carrying two stable ids: `segment_id` (unique across the
corpus) and `question_id` (the same slug wherever that question was asked, so you can
group the answers to a given question).

```python
import glob, re

def parse(path):
    text = open(path, encoding="utf-8").read()
    body = text.split("---", 2)[2]                       # strip YAML frontmatter
    pat  = r"## (.+?)\n+<!-- segment_id: (\S+) \| question_id: (\S+) -->\n+(.*?)(?=\n## |\Z)"
    return [{"question": q.strip(), "segment_id": sid, "question_id": qid,
             "answer": a.strip()}
            for q, sid, qid, a in re.findall(pat, body, re.S)]

rows = [r for p in sorted(glob.glob("interviews/MWI-*.md")) for r in parse(p)]
len(rows)   # 585
```

Nineteen questions recur across most interviews (`ordinary_day`, `hijab_journey`,
`misconceptions`, `liberation`, `submission`, `belonging` …); the rest are
participant-specific follow-ups.

## Redaction

Identifying details are replaced with typed tokens: `[CITY 1]`, `[UNIVERSITY 2]`,
`[MASJID 1]`, `[INTERVIEWEE]`. **Token numbering restarts in each interview** —
`[CITY 1]` in MWI-004 and in MWI-021 are different cities, so tokens are not keys you
can join across participants.

Retained by design: country names, Quebec, France, the US, Quebec legal and identity
vocabulary (Bill 21/94/9, CAQ, CEGEP, laïcité, Québécois), ethnicity and nationality
descriptors, and public figures cited as commentary.

## Composition

| | |
|---|---|
| Country | US 14 · Canada 8 · France 3 |
| Coming to Islam | 20 born Muslim · 5 converts |
| Covering | 19 hijab · 1 niqab · 4 formerly niqab · 1 not publicly covering |

That last row matters: the corpus deliberately contains disagreement. Any use that
flattens these women into a single representative voice is a misuse.

## Before you use this

Read `DATASHEET.md`. In short: n=25, purposively sampled partly through Muslim women's
organisations and skewed toward educated, community-involved women; France is only 3
participants; the prose was edited into readable narrative, so it is **not** suitable
for conversational-realism or ASR work; and some participants remain identifiable to a
motivated reader despite redaction.

Please do not use this corpus to generate synthetic "Muslim woman" personas, or present
any participant as representative of Muslim women generally.

## License

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) — share and adapt, including
commercially and for model training, with attribution. See `CITATION.cff`.
