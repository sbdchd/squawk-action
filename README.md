# squawk-action ![GitHub release (latest SemVer)](https://img.shields.io/github/v/release/sbdchd/squawk-action?display_name=tag&sort=semver)

A GitHub Action for [Squawk](https://github.com/sbdchd/squawk).

Lint Postgres migrations and report violations as a comment in a GitHub Pull Request ([example PR](https://github.com/sbdchd/squawk/pull/14#issuecomment-647009446)).

For more information on Squawk, see the [Squawk GitHub repository](https://github.com/sbdchd/squawk) or [website](https://squawkhq.com).

## basic usage

Lint every .sql file in `migrations/` on every pull request.

```yml
# .github/workflows/lint-migrations.yml
name: Lint Migrations

on: pull_request

jobs:
  lint_migrations:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v1
      - uses: sbdchd/squawk-action@v1
        with:
          pattern: "migrations/*.sql"
          version: "latest"
```

## advanced usage

Only lint modified .sql files in the `migrations/`.

```yml
# .github/workflows/lint-migrations.yml
name: Lint Migrations

on: pull_request

jobs:
  lint_migrations:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v1
      - name: Find modified migrations
        run: |
          modified_migrations=$(git diff --name-only origin/$GITHUB_BASE_REF...origin/$GITHUB_HEAD_REF 'migrations/*.sql')
          echo "$modified_migrations"
          echo "::set-output name=file_names::$modified_migrations"
        id: modified-migrations
      - uses: sbdchd/squawk-action@v1
        with:
          pattern: ${{ steps.modified-migrations.outputs.file_names }}
```

## example report

[![Example Squawk PR Comment](./squawk-pr-comment.png)](https://github.com/sbdchd/squawk/pull/14#issuecomment-647009446)

## development

### releasing a new version

1. [Release a new version](https://github.com/sbdchd/squawk-action/releases) (e.g. v5.2.1)
2. Update major version tag to point to commit.
   ```bash
   git tag v5
   git push --tags
   ```
