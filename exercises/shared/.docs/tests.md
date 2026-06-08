# Tests

The Factor track uses **Factor 0.101**. Each exercise ships with a bundled `exercism-tools` vocabulary that defines `STOP-HERE` and `TASK:` parsing words plus a test runner. From the exercise's directory, run:

```
factor -roots=. -run=exercism-tools <exercise>
```

For example, for the `hello-world` exercise:

```
factor -roots=. -run=exercism-tools hello-world
```

The runner exits with status 0 when all tests pass, and non-zero with diagnostic output when any test fails.

## Running the tests in the listener

If you prefer Factor's interactive [listener][listener], you can load an exercise there and run its tests as you develop, reloading after each change — the same edit-and-rerun loop you would use for any Factor vocabulary.

Start the listener from the top of the exercise folder, with that folder on the vocabulary roots:

```
factor -roots=. -run=listener
```

Load your solution together with its tests and run them:

```factor
"<exercise>" [ require ] [ test ] bi
```

For example, for the `hello-world` exercise:

```factor
"hello-world" [ require ] [ test ] bi
```

This loads `hello-world/hello-world.factor`, its test file, and the bundled `exercism-tools`, then runs every active test — it is exactly what `-run=exercism-tools` does for you. A passing test prints the test it ran; a failure prints `--> test failed!`. To see the details — what the test expected versus what your code produced — inspect the failures with:

```factor
:test-failures
```

After editing your solution, reload the changed files and run the tests again:

```factor
refresh-all
"<exercise>" test
```

The `STOP-HERE` mechanism described below works the same way in the listener: only the tests above it are run.

[listener]: https://docs.factorcode.org/content/article-listener.html

## Skipped tests

Solving an exercise means making all its tests pass.
By default, only one test (the first one) is executed when you run the tests.
This is intentional, as it allows you to focus on just making that one test pass.

The mechanism is the `STOP-HERE` line in the test file: at parse time it tells Factor to ignore everything below it, so the runner only sees the tests above. Once your first test passes, you can enable more tests in two ways:

- **Delete the `STOP-HERE` line** to enable every remaining test at once.
- **Move the `STOP-HERE` line further down** to enable tests one at a time, or one task at a time. Concept exercises group tests under `TASK:` markers — moving `STOP-HERE` just before the next `TASK:` is a natural step.

When all tests have been enabled and your implementation makes them all pass, you'll have solved the exercise!
