# NWEA Data Agent
## Natural Language Interface for Standardized Test Data | Spring 2026

---

## Overview

This is a conversational data tool that lets school administrators ask plain-language
questions about NWEA MAP Growth scores and get answers drawn directly from the database.
The user types a question; the agent figures out what SQL to run; the answer comes back as
a formatted table or summary. No SQL knowledge required on the user's end.

The agent runs on OpenAI's `o4-mini` model with high reasoning effort, backed by a
three-tool loop that mirrors the process a data analyst would follow manually:

```
1. get_schema       → inspect what tables and columns exist
2. get_table_info   → examine the structure of relevant tables
3. sql_query        → write and execute a query against the database
```

The agent is instructed to never skip steps and never ask clarifying questions; it makes
reasonable assumptions and goes straight to the data. Results are returned as
GitHub-flavored Markdown tables where appropriate.

The database (`anon.db`) is a fully anonymized export of real MAP Growth assessment data.
Student identifiers have been removed. The database contains RIT scores, sub-goal
scores, growth metrics, teacher assignments, and grade-level information across subjects
including Mathematics, Language Arts, and Science.

---

## Architecture

```
app.py          Streamlit chat UI: renders conversation history, streams SQL and
                responses to the screen as the agent works

queryagent.py   The agent loop: sends messages to the model, checks for tool calls,
                dispatches them, feeds results back, repeats until final response.
                Maintains multi-turn conversation history across a session.

tools.py        Three callable functions (get_schema, get_table_info, sql_query) and
                their OpenAI function-call JSON descriptions

config.py       Database path constants

anon.db         Anonymized SQLite database of NWEA MAP Growth results
```

The agent loop in `queryagent.py` is implemented as a generator: it `yield`s each SQL
query as it runs so the UI can display it in real time, then `yield`s the final response
when the model is done. This means the administrator sees the agent's reasoning process,
including the SQL it chose to write, before they see the answer.

---

## How to Run

**Locally:**
```bash
pip install -r requirements.txt
# Add your OpenAI API key to a .env file: OPENAI_API_KEY=...
streamlit run app.py
```

**With Docker:**
```bash
docker build -t nwea-agent .
docker run -p 5000:5000 --env OPENAI_API_KEY=... nwea-agent
```

---

## Philosophy Alignment

This project sits at the intersection of two threads in the portfolio philosophy.

The first is about tools and operators. Principle 11 holds that *a teacher should be a
software's sole user just as an adult should be a vehicle's sole driver.* The NWEA agent
inverts that framing in a deliberate way: here, the teacher is the *builder*, and the
administrator is the operator. The teacher wrote every line of the agent loop, defined
the tool schemas, shaped the model's instructions, and chose what data to expose. The
administrator drives. But the teacher built the vehicle.

This project could not have been built without first having built tools that were
invisible to students. The skills and judgment developed in that context (knowing what one tool should do,
knowing where computation should be hidden, knowing how to write instructions that shape
model behavior without breaking it) transferred directly here.
Principle 7 applies: *tools amplify the mastery of skills and ethics gained in that
tool's absence.*

The second thread is about AI's role in protecting student potential. Principles 13–15
argue that AI amplifies whatever skills and ethics its user brings to it, and that the
right use of AI in education is invisible amplification of student development. This tool
does not touch students directly. It touches the administrators and teachers who make
decisions about students. By making it easier to see where a grade level is growing,
where sub-goal scores are lagging, and which teachers' students are outperforming
projections, the agent serves the same underlying goal: better decisions upstream
producing better outcomes downstream.

Principle 12 governs the interface design: *computation is excellent at being invisible.*
The administrator does not see an ORM, a query builder, or a schema browser. They see a
chat window. The SQL is shown (it streams to the screen as the agent works) but only because
transparency about the agent's reasoning is itself a form of trust-building with the
end user. The visibility is intentional and bounded.

---

## What This Is Not

This tool is not a replacement for a data analyst or an instructional coach. It does not
interpret results, recommend interventions, or make decisions. It answers questions about
what the data says. The skill of knowing which questions to ask, and what to do with the
answers, remains entirely with the human.

That constraint is consistent with principle 10: *a computer program should facilitate
one skill while its user facilitates its ethical use.* The skill this program facilitates
is access. The ethical use, turning access into action that serves students, belongs to
the people who asked the question.
