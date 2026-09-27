# Section 1 — Build TokenScope twice

This is your ungraded Module 1 project. Build the same small command-line tool
once with Codex and once with Claude Code, from the written specification. Use
an independent repository for each build, or two branches from the same clean
starting commit. Start the second build from the original handoff, not the first
implementation. Your code may look different in each build.

## What you have

- [spec.md](spec.md): the complete shared behavior contract.
- [sample-project](sample-project/): small input folder with text, Unicode, empty,
  ignored and binary files. Binary files are deliberate test inputs; do not edit them.
- [SAMPLE_OUTPUT.md](SAMPLE_OUTPUT.md): expected counts and example outputs.
- [expected-table.txt](expected-table.txt) and [expected-report.json](expected-report.json): concrete reference outputs.
- [REFLECTION.md](REFLECTION.md): prompts for your own observations.

There is no application code or supplied test suite in this starter. It is ready
to build from, not a broken application to repair. Write your own product tests.
Work from the specification without the previous video open beside you.

## Start each build

1. Download the repository using **Code → Download ZIP** and extract it into a fresh folder.
   Make two clean copies, for example `tokenscope-codex` and `tokenscope-claude`, before
   implementing either build. Work on each independently.
   Check `python3 --version`: use Python 3.10 or newer. If your system's python3 is
   older, use your installed newer interpreter for every command below.
2. Open only that folder in the chosen coding tool. Read spec.md yourself first.
3. Initialize a local Git repository and commit the handoff before implementation:

```sh
git init
git add .
git commit -m "add TokenScope specification and sample inputs"
```

4. Ask the agent for a small-step plan, review it, and implement one step at a time.
   Give each step the relevant requirements, constraints and a definition of done.
   Run tests, read the changes and commit each working state before continuing.
5. Complete steps 1–2 (core, walker and table) and step 3 (`--json` and `--top`).
   Step 3 is the extension left for you by the teaching video; complete it in both builds.

These commands should work once you finish:

```sh
python3 -m unittest discover -s tests -v
python3 -m tokenscope sample-project
python3 -m tokenscope sample-project --json
python3 -m tokenscope sample-project --top 1
python3 -m tokenscope sample-project --json --top 1
python3 -m tokenscope .
git log --oneline
```

The `tokenscope` shorthand in the video means the same module command; a global
installation is unnecessary. Different internal architectures are welcome if the
published command and behavior match spec.md.

## When you are finished

Keep two working implementations, passing tests, and more than one meaningful
commit in each build. Verify a real folder as well as the sample. Check invalid
inputs and skipped files, not just a successful table. Write your real observations
in REFLECTION.md. If a correction loop stalls, return to the spec, isolate the
smallest failure, and give the agent the relevant context again.

This is practice, with no grade, time threshold or weekly capstone submission.
Keep the repositories and notes locally; graded PromptLab work begins in Section 2.
The point is to practice making and reviewing decisions yourself.
