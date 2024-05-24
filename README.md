# pip-tools-actions

Github actions for pip-tools.

The following actions are available.

## setup-pip-tools

This action installs pip-tools.

Example usage:

```
jobs:
  test-setup-pip-tools:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code Repository
        uses: actions/checkout@v4

      - name: Set up Python 3.9
        uses: actions/setup-python@v5
        with:
          python-version: 3.9

      - name: Set up pip tools
        uses: UW-GAC/pip-tools-actions/setup-pip-tools@v1
```

You will need to set up python before running this action.

## run-pip-compile

This action sets up pip-tools and runs pip-compile.

Example usage:

```
jobs:
  test-check-requirements-files:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code Repository
        uses: actions/checkout@v4
        with:
          ref: ${{ github.head_ref }}

      - name: Set up Python 3.9
        uses: actions/setup-python@v5
        with:
          python-version: 3.9

      - name: Run pip-compile
        uses: UW-GAC/pip-tools-actions/run-pip-compile@v1
        with:
          requirements_files:  |-
            requirements/requirements.in
            requirements/dev-requirements.in
```

You will need to set up python before running this action.

You can specify the requirements files to check by providing a list of files separated by newlines.
The action will run `pip-compile` on each file in the order they are listed.

### Inputs

```
inputs:
  requirements_files:
    description: |-
      The paths to the requirements files to compile. Files will be compiled
      in the order they are listed. To specify multiple files, use the following
      syntax:

      requirements_files: |-
        path/to/requirements1.txt
        path/to/requirements2.txt
    required: true
  pip-compile-args:
    description: "Additional arguments to pass to pip-compile."
    required: false
    default: ""
```

## check-requirements-files

This action sets up pip-tools, runs pip-compile, and verifies that there are no changes detected to the requirements files.
If there are changes, the action will fail.

Example usage:

```
jobs:
  test-check-requirements-files:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code Repository
        uses: actions/checkout@v4
        with:
          ref: ${{ github.head_ref }}

      - name: Set up Python 3.9
        uses: actions/setup-python@v5
        with:
          python-version: 3.9

      - name: Check requirements files
        uses: UW-GAC/pip-tools-actions/check-requirements-files@main
        with:
          requirements_files:  |-
            requirements/requirements.in
            requirements/dev-requirements.in
```

You will need to set up python before running this action.

You can specify the requirements files to check by providing a list of files separated by newlines.
The action will run `pip-compile` on each file in the order they are listed.

### Inputs

```
inputs:
  requirements_files:
    description: |-
      The paths to the requirements files to compile. Files will be compiled
      in the order they are listed. To specify multiple files, use the following
      syntax:

      requirements_files: |-
        path/to/requirements1.txt
        path/to/requirements2.txt
    required: true
  pip-compile-args:
    description: "Additional arguments to pass to pip-compile."
    required: false
    default: ""
```

## update-requirements-files

This action sets up pip-tools and runs pip-compile.
If there are any changes detected to the requirements files, the job will open a pull request into the branch from which it is run and then fail with a message about merging in the new pull request.

It is intended to be used in a workflow that runs on a pull request event.

The name of the branch that is opened is of the format: `pip-tools-actions/update-requirements-files/<pr-branch-suffix>`.

Example usage:

```
jobs:
  test-update-requirements-files:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code Repository
        uses: actions/checkout@v4
        with:
          ref: ${{ github.head_ref }}

      - name: Set up Python 3.9
        uses: actions/setup-python@v5
        with:
          python-version: 3.9

      - name: Update requirements files
        uses: UW-GAC/pip-tools-actions/update-requirements-files@main
        with:
          requirements_files:  |-
            requirements/requirements.in
            requirements/dev-requirements.in

          pr-commit-message: "Test update requirements files commit message"
          pr-title: "Test update requirements files title"
          pr-body: "Test update requirements files body"
          pr-branch-suffix: "${{ github.event.number}}"
```

You will need to set up python before running this action.
You will also need to make sure that checkout step checks out the `head_ref` so that the pull request base can be detected correctly.

You can specify the requirements files to check by providing a list of files separated by newlines.
The action will run `pip-compile` on each file in the order they are listed.

### Inputs

```
inputs:
  requirements_files:
    description: |-
      The paths to the requirements files to compile. Files will be compiled
      in the order they are listed. To specify multiple files, use the following
      syntax:

      requirements_files: |-
        path/to/requirements1.txt
        path/to/requirements2.txt
    required: true
  pip-compile-args:
    description: "Additional arguments to pass to pip-compile."
    required: false
    default: ""
  pr-commit-message:
    description: "The commit message to use for the pull request."
    required: false
    default: "Compile requirements files"
  pr-title:
    description: "The title to use for the pull request."
    required: false
    default: "Update requirements files"
  pr-body:
    description: "The body to use for the pull request."
    required: false
    default: "Compile requirements files"
  pr-labels:
    description: "The labels to apply to the pull request."
    required: false
    default: "automated-pr, update-requirements-files"
  pr-branch-suffix:
    description: "A suffix to append to the branch name."
    required: true
```
