# Harness Engineering: Moving From Hopeful Instructions to Enforced Control

By Nicole Koenigstein

Companion repo for my ACM TechTalk. It holds:

- **the demo app** from the talk
- **the slides**
- **a cheatsheet** of the harness components and what each one enforces

## Go deeper

This talk is a short cut through material I cover in full elsewhere:

| | |
|---|---|
| 📕 **Book** | [**Harness Engineering**](https://learning.oreilly.com/library/view/harness-engineering/0642572422783/) (O'Reilly). The scenes in this repo follow its chapters on harness components and skills. |
| 📘 **Book** | [**AI Agents: The Definitive Guide**](https://learning.oreilly.com/library/view/ai-agents-the/0642572247775/) (O'Reilly). |
| 🎓 **Live course** | [**Harness Engineering intensive**](https://www.oreilly.com/live/intensive-harness-engineering.html) (O'Reilly). Build and ablate a harness hands-on. |

## What the app shows

> The model proposes. The harness decides.

An operations agent for an online shop handles refunds, account deletions and
product posts. The app first runs one ticket through the complete harness and
shows, step by step, which component acts and what it checks. Then each scene
removes one component and sends the same model behaviour through again, so you
can see what that component was doing: a refund paid without approval, a payout
made twice, tables dropped, card data sent out, a run with no record.

Three more scenes show what a bigger context window does not solve: rules
piling up in one skill, the state of a long-running skill, and buying
reliability with retries.

| Scene | What it shows |
|---|---|
| 0 The harness | The components and the system |
| 1 Intact run | A legitimate refund, component by component |
| 2 Governance | Approval as a fact the harness holds, not a sentence in a ticket |
| 3 Context | What happens when the policy never reaches the model |
| 4 Execution | A crash after a payout, with and without idempotency |
| 5 Tool scope | A raw SQL tool versus a scoped erasure tool |
| 6 Skill boundary | A third-party skill: admission and action limits |
| 7 Observability | The same run without a trace |
| 8 Skill rules | 21 months of rules added to one skill |
| 9 Skill state | Transcript, compaction or state held by the harness |
| 10 Retries | What a larger retry budget buys |
| 11 What it cost | Summary |

## Run it

### With Docker Compose

Install Docker, then run:

```bash
docker compose up --build
```

Open http://localhost:8501. Stop the app with `Ctrl+C`.

### With Python

You need Python 3.10 or newer. No API key is required.

```bash
git clone <this-repo-url>
cd <repo-folder>
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
streamlit run app.py
```

The app opens at http://localhost:8501. Use the sidebar to move between scenes
and "Reset all runs" to clear the damage counter.

## What is real and what is not

- The harness code, the tools and the SQLite shop database run for real in every scene.
- Scenes 1, 2, 3, 4 and 7 replay **recorded live runs** of `openai/gpt-5.6-luna`
  (in `recordings/`). The harness around them is what changes.
- Scenes 5 and 6 use **scripted model turns** and say so on screen. The live
  model declined both attacks in 5 of 5 runs. The scenes show what the harness
  does when a model does not decline.
- Scenes 8, 9 and 10 call no model. They are labelled illustrations: scripted
  events, real text, measured sizes. Scene 9 also shows the measured results
  from the SKILL.state paper (Badhe, Tiwari, Chung, arXiv:2608.26263).
- All data is synthetic. Customers are generated, card numbers are published
  test numbers, the cloud key in `workspace/secrets/` is fake, and the
  "attacker endpoint" is a list in memory. Nothing leaves your machine.
- `skills/social-launch-post/SKILL.md` is a deliberately unsafe skill written
  for scene 6. Read it, do not install it.

## What is in the folder

- `app.py`: the walkthrough.
- `harness/core.py`: the harness (governance, tool gateway, verifier, recorder, orchestrator).
- `harness/shop.py`: the shop database and tools.
- `harness/scenes.py`, `harness/walkthrough.py`, `harness/context_sims.py`: scene definitions.
- `recordings/`: the recorded model runs. To record your own, put an
  `OPENROUTER_API_KEY` in a `.env` file and run `python record.py`.
