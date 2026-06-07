# Help

## Running the tests

The Factor track uses **Factor 0.101**. Each exercise ships with a bundled `exercism-tools` vocabulary that defines `STOP-HERE` and `TASK:` parsing words plus a test runner. From the exercise's directory, run:

```
factor -roots=. -run=exercism-tools <exercise-slug>
```

For example, for the `hello-world` exercise:

```
factor -roots=. -run=exercism-tools hello-world
```

The runner exits with status 0 when all tests pass, and non-zero with diagnostic output when any test fails.

## Skipped tests

Solving an exercise means making all its tests pass.
By default, only one test (the first one) is executed when you run the tests.
This is intentional, as it allows you to focus on just making that one test pass.

The mechanism is the `STOP-HERE` line in the test file: at parse time it tells Factor to ignore everything below it, so the runner only sees the tests above. Once your first test passes, you can enable more tests in two ways:

- **Delete the `STOP-HERE` line** to enable every remaining test at once.
- **Move the `STOP-HERE` line further down** to enable tests one at a time, or one task at a time. Concept exercises group tests under `TASK:` markers — moving `STOP-HERE` just before the next `TASK:` is a natural step.

When all tests have been enabled and your implementation makes them all pass, you'll have solved the exercise!

## Submitting your solution

You can submit your solution using the `exercism submit cargo-shuffle/cargo-shuffle.factor` command.
This command will upload your solution to the Exercism website and print the solution page's URL.

It's possible to submit an incomplete solution which allows you to:

- See how others have completed the exercise
- Request help from a mentor

## Need to get help?

If you'd like help solving the exercise, check the following pages:

- The [Factor track's documentation](https://exercism.org/docs/tracks/factor)
- The [Factor track's programming category on the forum](https://forum.exercism.org/c/programming/factor)
- [Exercism's programming category on the forum](https://forum.exercism.org/c/programming/5)
- The [Frequently Asked Questions](https://exercism.org/docs/using/faqs)

Should those resources not suffice, you could submit your (incomplete) solution to request mentoring.

## Running the tests

The Factor track uses **Factor 0.101**. After
[installing it](https://exercism.org/docs/tracks/factor/installation),
run from this exercise's directory:

```
factor -roots=. -run=exercism-tools <exercise-slug>
```

For example, for `hello-world`:

```
factor -roots=. -run=exercism-tools hello-world
```

Each exercise ships a small bundled `exercism-tools` vocabulary
that drives the test run. The runner exits with status 0 when
every test passes, and with non-zero status plus diagnostic
output if any test fails.

### Skipped tests

By default, only the first test runs. The mechanism is the
`STOP-HERE` line in the test file: at parse time it tells
Factor's lexer to ignore everything below it, so the runner
only sees the tests above. Once your first test passes, you can
enable more tests in two ways:

- **Delete the `STOP-HERE` line** to enable every remaining
  test at once.
- **Move the `STOP-HERE` line further down** to enable tests
  one at a time, or one task at a time. Concept exercises group
  their tests under `TASK:` markers — moving `STOP-HERE` to
  just before the next `TASK:` is a natural step.

When all tests pass, you've solved the exercise.

## Submitting your solution

From the exercise's directory:

```
exercism submit <exercise-slug>/<exercise-slug>.factor
```

For example:

```
exercism submit hello-world/hello-world.factor
```

If the exercise's `.meta/config.json` lists more than one
solution file (some concept exercises split a solution across a
helper sub-vocabulary), pass every file you edited to
`exercism submit`.

## Submitting incomplete solutions

You can submit a solution even with failing tests — that lets
you see how others have approached the exercise, and ask for
mentor feedback on whatever you have so far.

## Getting more help

- The [Factor category on the Exercism forum](https://forum.exercism.org/c/programming/factor/)
  is the best place for track-specific questions and feedback.
- The [Exercism Discord](https://exercism.org/r/discord) has
  channels for both general help and individual tracks.
- Factor's official documentation is searchable and exhaustive. Two good entry points:
  - [The Factor handbook](https://docs.factorcode.org/content/article-handbook.html)
    — reference-style, organised by vocabulary.
  - [The Factor cookbook](https://docs.factorcode.org/content/article-cookbook.html)
    — short, idiomatic examples by topic.
- Andrea Ferretti's
  [Factor tutorial](https://andreaferretti.github.io/factor-tutorial/)
  is a fast, friendly introduction to the language and the
  development environment.