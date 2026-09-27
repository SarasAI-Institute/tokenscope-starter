# Sample folder and expected results

The two expected-output files are illustrative fixtures, not a test runner. Do not
hard-code these answers: your program must work on any folder following spec.md.

| Included path | Characters | Estimated tokens |
|---|---:|---:|
|notes.txt|30|8|
|src/greet.py|15|4|
|unicode.txt|8|2|
|empty.txt|0|0|

notes.txt contains `Plan small. Review carefully.` plus one LF. src/greet.py contains
`print("Hello")` plus one LF. unicode.txt contains `Živjo 👋` plus one LF.
All counts include the newline. binary.dat contains a NUL; invalid-utf8.dat contains
invalid UTF-8. Both are skipped as binary. node_modules is pruned, and .env.example
is a harmless ignored fixture with no secret. Keep the sample folder unchanged.

At step 2, compare the table with expected-table.txt. At step 3, compare the parsed
JSON with expected-report.json. Whitespace and object-key order in JSON may differ.
`--top 1` shows only notes.txt; files_shown becomes 1. The full totals remain
4 files, 53 characters, 14 estimated tokens and 2 skips. An empty folder should
succeed with zero totals. Try a real folder as well; its output will vary.
