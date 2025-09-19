# Poetry Lambda Builder Taskfile

This is a Taskfile which can be included remotely.

It allows you to bundle up your Python Lambdas with Poetry.

## Features

- Deterministic zip file hashes for optimized CDK caching
  - Well, deterministic _enough_ - we are still at the mercy of PyPI after all
  - Allows you to skip re-deploying your dependencies layer if an editable package was modified!
- Includes all filesystem packages in the code artifact rather than the layer, allowing you to view/modify them from the aws console
  - Also avoids re-deploying the entire layer when one of these change
- Builds dependencies in the AWS SAM image of your choice, making sure any native compiled dependencies run properly in your environment.
  - Docker image and platform are variables
- Customizable compression level (zip)
- Optionally pre-compile `.pyc` files for code, layer, or both - to speed up Lambda cold starts

## Usage

### Configuration

The possible options are documented within the Taskfile.yml itself.

>[!WARNING]
>When using this taskfile, please ensure you are using a permalink (with the commit hash in the URL) as I may make breaking changes at any point.

### Example

**Good**: `https://raw.githubusercontent.com/xaviergmail/go-task-poetry-lambda-build/9ca9bf307fc0955fa1ac38cd2202c287a82c06b4/Taskfile.yml`
**Bad**: `https://github.com/xaviergmail/go-task-poetry-lambda-build/blob/develop/Taskfile.yml`

```yaml
vars:
  POETRY_BUILD_TASKFILE: https://raw.githubusercontent.com/xaviergmail/go-task-poetry-lambda-build/9ca9bf307fc0955fa1ac38cd2202c287a82c06b4/Taskfile.yml
includes:
  bundle-api:
    taskfile: "{{.POETRY_BUILD_TASKFILE}}"
    vars:
      DIR: ../api
  bundle-workers:
    taskfile: "{{.POETRY_BUILD_TASKFILE}}"
    vars:
      DIR: ../workers

 tasks:
   bundle:
     deps: [bundle-api, bundle-workers]
```

## Development

If you make changes, run `task -d test test-all` to avoid regressions. You may add tests there as needed.
