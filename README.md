# github_mr_creator

`ghmr` is a small macOS shell helper for creating GitHub pull requests from the current git branch.

It is meant to be run from inside any project:

```sh
ghmr
```

## What It Does

- Finds the git repository root from any subdirectory.
- Reads the GitHub `origin` repository.
- Detects the current source branch.
- Checks that the branch exists on `origin`; if not, offers to push it.
- Suggests the target branch interactively.
- Uses the GitHub repository default branch as option `1`.
- Adds `test` as an extra target option when `origin/test` exists.
- Uses the last commit subject as the PR title by default.
- Creates the PR through GitHub CLI.
- Does not assign users or reviewers.
- Caches the default branch per repository.

## Requirements

- macOS or another POSIX shell environment.
- `git`
- GitHub CLI:

```sh
brew install gh
gh auth login
```

Check auth:

```sh
gh auth status
```

## Installation

Put the script into a directory that is already in `PATH`, for example:

```sh
mkdir -p ~/.local/bin
cp ghmr ~/.local/bin/ghmr
chmod +x ~/.local/bin/ghmr
```

Make sure `~/.local/bin` is in `PATH`:

```sh
echo "$PATH"
```

If it is missing, add it to your shell config:

```sh
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

## Usage

Run from any directory inside a git repository:

```sh
ghmr
```

The target prompt is ordered like this:

```text
Выбери target branch. По умолчанию: release
 [1] release
 [2] test
Номер [1]:
```

Press Enter to use the default branch. Enter `2` to select `test`.

## Options

```sh
ghmr --target release
ghmr --title "P-2081: fix vacancy status"
ghmr --draft
ghmr --web
ghmr --dry-run
ghmr --refresh-cache
ghmr --no-cache
ghmr --clear-cache
```

Options:

- `-t, --target BRANCH`: use a target branch without the interactive prompt.
- `-T, --title TITLE`: set the PR title manually.
- `--draft`: create a draft PR.
- `--web`: open the created PR in the browser.
- `--dry-run`: show what would be created without creating a PR.
- `--refresh-cache`: fetch the default branch from GitHub and update the cache.
- `--no-cache`: do not read or write the default branch cache.
- `--clear-cache`: delete the local `ghmr` cache and exit.

## Default Branch Cache

The script stores default branches here:

```text
~/.cache/ghmr/default-branches.tsv
```

Cache format:

```text
owner/repo	default-branch	updated-at
```

Example:

```text
sobes-dev/nestjs-api-sobes-kz	release	2026-05-20T05:00:46Z
```

Use this after changing a repository default branch in GitHub:

```sh
ghmr --refresh-cache
```

## Typical Flow

```sh
git checkout -b P-2081
git commit -m "P-2081: fix vacancy status"
ghmr
```

If the branch has no upstream, `ghmr` will ask:

```text
Запушить сейчас? (git push -u origin P-2081) [y/N]
```

After confirmation, it creates the PR and prints the PR URL.
