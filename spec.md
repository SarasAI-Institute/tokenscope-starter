# TokenScope specification — 1.0

Course-authored on September 13, 2026, with the course owner's authorization.
This supplies the details left open in the Module 1 recording outline. It is the
shared contract for the video and both independent student builds.

## Purpose and scope

Build a small, read-only command-line tool that recursively estimates the text
context size of a folder. Use Python 3.10+ and the standard library only, including
unittest for product tests. No API, tokenizer package, network, database or UI.
The estimate is a teaching heuristic: it is neither a model's exact token count
nor a price in money. Implementations may have different internal designs.

## Commands and milestones

Run from the project folder:

```sh
python3 -m tokenscope FOLDER
python3 -m tokenscope FOLDER --json
python3 -m tokenscope FOLDER --top 3
python3 -m tokenscope FOLDER --json --top 3
python3 -m tokenscope --help
```

FOLDER is required; relative and absolute paths are accepted. Quote paths with
spaces. `--top N` accepts a positive integer, including values larger than the
number of files. Unknown options, missing arguments, zero, negative or noninteger
N are errors. A terminal alias `tokenscope` may stand for `python3 -m tokenscope`
when presenting the shorter command in the recording; installation is not required.

Build in three working steps:

1. Pure estimation logic and tests, without file access or CLI.
2. Recursive scanning, skip rules, sorted table, CLI and tests. This is the video endpoint.
3. `--json` and `--top`, separately and together, with tests. This is homework in both builds.

The step-2 checkpoint rejects both extension flags until step 3 is implemented.

## Counting and ordering

Decode eligible files using strict UTF-8. Count every decoded Unicode code point
with `len(text)`, including whitespace, newlines and any UTF-8 BOM. Do not normalize
line endings: CRLF counts as two characters. A multibyte character counts as one
character; this does not mean one user-visible grapheme or one model token.

For each file, `estimated_tokens = (characters + 3) // 4`, equivalent to rounding
characters / 4 upward. Empty text has zero tokens. Examples: 1, 4, 5 characters
estimate 1, 1, 2 tokens. Sum these per-file estimates for the overall total; do not
estimate once from the combined character count.

Paths are relative to FOLDER, use `/` as separator, and do not start with `./`.
Sort files by estimated tokens descending, then by relative path ascending using
case-sensitive Unicode string order. `--top` is applied after scanning and sorting.
It limits displayed files only; totals and skipped entries always describe the full scan.

## Walking and skipping

Include regular files at all depths, including extensionless and hidden files,
except the exclusions below. Do not inspect content of excluded entries.

- Prune actual directories (not links) named exactly `.git`, `.venv`, `venv`, `node_modules`,
  `__pycache__`, `.pytest_cache`, `.ruff_cache`, `dist` or `build`, at any depth.
- Ignore entries named exactly `.env` or beginning `.env.`. Prune these if directories.
- These name exclusions are case-sensitive, apply to entries beneath FOLDER, and
  are not counted or listed as skipped. Do not interpret .gitignore patterns.
- Never traverse or read an encountered symbolic link. Record one `symlink` skip
  for that entry, even for a link to a directory; do not count its descendants.
- Record sockets, FIFOs and other nonregular, nondirectory entries as `non_regular`.
- Limit a file to 1,048,576 bytes (1 MiB), inclusive. Record larger files as
  `too_large`. Read at most the limit plus one byte, also detecting growth after stat.
- Record a file containing any NUL byte or invalid UTF-8 as `binary`.
- Record an entry that cannot be inspected/read, or a descendant directory that
  cannot be listed, as `unreadable`. Continue scanning other entries; for a directory,
  record it once without counting inaccessible descendants.

Evaluate name exclusions first, then symlink/type, size, read errors, binary and
UTF-8 validity. A successfully decoded empty file is included. Sort all recorded
skips by relative path ascending. Reason values are exactly the five names above.
No per-file warning is written to stderr; skips are part of the report.

A missing root, a root that is a file or symbolic link, or a root directory that
cannot be listed is a fatal input error. An empty readable root succeeds. The
teaching tool assumes a reasonably stable local folder; it does not promise an
atomic snapshot or protection against concurrent filesystem replacement.

## Table output

Write UTF-8 text to stdout, with one newline after every line. The first line is
`TOKENS` then `CHARACTERS` then `PATH`, separated by single tabs. Each displayed
file is one row in that order with decimal numbers and a JSON-quoted path
(`ensure_ascii=True`). Quoting keeps spaces, tabs, newlines and control characters
in filenames from breaking the report. There are no borders or ANSI colors.

Follow the rows with one blank line and this exact summary shape:

```text
Files: 4 | Shown: 4 | Characters: 53 | Estimated tokens: 14 | Skipped: 2
```

If there are skipped entries, follow the summary with one line per entry:
`SKIP`, the reason and the JSON-quoted relative path, separated by single tabs.
An empty scan prints the header, blank line and an all-zero summary.

## JSON output (step 3)

`--json` writes one valid JSON object and a trailing newline to stdout. No banner,
table or status text. Pretty printing is optional; key order is not significant.
Integer fields are nonnegative JSON integers. Object keys and meanings are:

```json
{
  "files": [
    {"path": "hello.txt", "characters": 5, "estimated_tokens": 2}
  ],
  "skipped": [
    {"path": "image.bin", "reason": "binary"}
  ],
  "summary": {
    "files_scanned": 1,
    "files_shown": 1,
    "total_characters": 5,
    "total_estimated_tokens": 2,
    "entries_skipped": 1
  }
}
```

Use exactly these keys; empty collections are arrays, not null. File and skip
ordering follows the rules above. `files_scanned` counts all included regular text
files; `files_shown` is the length of `files` after `--top`. Ignored entries are absent.

## Exit behavior

- 0: successful report, including empty folders and scans with skipped entries;
  also `--help`, which writes usage to stdout without scanning.
- 2: argument error or invalid/unreadable root. Print a concise diagnostic to
  stderr, no report to stdout and no traceback. Diagnostic wording is flexible.

## Acceptance examples

The supplied `sample-project` has four included files, two skipped binary files,
one ignored .env entry and one ignored node_modules subtree. Its exact expected
order and counts are in `SAMPLE_OUTPUT.md`. Also exercise empty roots, ties,
non-ASCII text, CRLF, the size boundary, links, invalid roots and invalid flags.
Write your own product tests; the sample is an example, not an exhaustive test suite.
