# `TEXTTEST_PATH` Issue

This repository demonstrates an issue with running `texttest` tests when
test-related files are stored in separate directories.

## Problem Description

The `TEXTTEST_PATH` environment variable (or the `-d` option) is expected to
allow `texttest` to locate test-related files spread across multiple
directories. However, this does not seem to work as expected.

### Expected Folder Structure

The following directory structure should be supported:

```bash
$ tree
.
├── dir1
│   ├── config.test_path
│   └── testsuite.test_path
├── dir2
│   └── TestPath
│       ├── errors.test_path
│       └── output.test_path
```

Here:

- `config.test_path` and `testsuite.test_path` are located in `dir1`
- `output.test_path` is located in `dir2`

## Prerequisites

1. Create a Python virtual environment, activate it, and install `texttest`:

```bash
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

2. Set the `HELLO_WORLD_PATH` environment variable:

```bash
export HELLO_WORLD_PATH=$(pwd)
```

## Steps to Reproduce

1. Set `TEXTTEST_PATH` to include both `dir1` and `dir2`:

```bash
export TEXTTEST_PATH=$(realpath dir1)
export TEXTTEST_PATH=$TEXTTEST_PATH:$(realpath dir2)
```

2. Verify the `TEXTTEST_PATH` value:

```bash
$ printenv TEXTTEST_PATH
<repo_dir>/dir1:<repo_dir>/dir2
```

3. Run `texttest`:

```bash
texttest -con
```

## Expected Behavior

```bash
$ texttest -con
Using local queues for Application TEST_PATH
Q: Submitting TEST_PATH test-case TestPath to default local queue
S: TEST_PATH test-case TestPath succeeded on <hostname>
```

## Current Behavior

```bash
$ texttest -con
Using local queues for Application TEST_PATH
WARNING: The test TestPath could not be found.
Please check the file at <repo_dir>/dir1/testsuite.test_path
Rejected Application TEST_PATH - no tests matching the selection criteria found.
```

## Notes

This issue suggests that `texttest` is not correctly recognizing `TEXTTEST_PATH`
when tests are split across multiple directories. Further investigation may be
needed to determine whether this is a bug or a configuration issue.
