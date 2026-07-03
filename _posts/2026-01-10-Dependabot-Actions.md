---
layout: post
title:  "Keeping your Github actions up to date with Dependabot"
date:   2026-01-10 12:58:37 +0100
categories: how-to github
---

You can use Dependabot to keep the actions you use updated to the latest versions.

## Enabling Dependabot for actions

When you enable Dependabot version updates for GitHub Actions, Dependabot will help ensure that references to actions in a repository's workflow.yml files and reusable workflows used inside workflows are kept up to date.

1. Create or open `dependabot.yml` in the `.github` directory of your repository.
2. Specify `"github-actions"` as a `package-ecosystem`.
3. Set the directory to `"/"` to check for workflow files in `.github/workflows`.
4. Set a `schedule.interval` to specify how often to check for new versions.

An example `dependabot.yml` for github actions

```yaml
# Set update schedule for GitHub Actions

version: 2
updates:

  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      # Check for updates to GitHub Actions every week
      interval: "weekly"
```

### Make it more fancy

#### Group actions into single PR

The example already is enough to automatical let dependabot update your github actions but it will create for every action his own Pull Request, which can be quite annoying when there are multiple updates all at once.
To counter this we can add a group section to our `"github-actions"` `package-ecosystem`.

```yaml
  - package-ecosystem: "github-actions"
    (...)
    groups:
      # Group all GitHub Actions updates into a single pull request
      github-actions:
        patterns:
          - "*"
```

#### Setting a cooldown

You can use `cooldown` with a combination of options to control when Dependabot creates pull requests for version updates.

```yaml
  - package-ecosystem: "github-actions"
    (...)
    cooldown:
      # Skip updates for 10 days after the last update
      default-days: 10
```

#### Dependabot and CODEOWNERS file

The `CODEOWNERS` file can be used to assign a team member or a complete team to a Pull request.
To set up a `CODEOWNERS` file, create a new file named `CODEOWNERS` in the `.github/`, root, or `docs/` directory of your repository.

Example `CODEOWNERS` file:

```text
# This is a comment.
# Each line is a file pattern followed by one or more owners.

# These owners will be the default owners for everything in
# the repo. Unless a later match takes precedence,
# @global-owner1 and @global-owner2 will be requested for
# review when someone opens a pull request.
*       @global-owner1 @global-owner2

# Order is important; the last matching pattern takes the most
# precedence. When someone opens a pull request that only
# modifies JS files, only @js-owner and not the global
# owner(s) will be requested for a review.
*.js    @js-owner #This is an inline comment.

# You can also use email addresses if you prefer. They'll be
# used to look up users just like we do for commit author
# emails.
*.go docs@example.com

# Teams can be specified as code owners as well. Teams should
# be identified in the format @org/team-name. Teams must have
# explicit write access to the repository. In this example,
# the octocats team in the octo-org organization owns all .txt files.
*.txt @octo-org/octocats

# In this example, @doctocat owns any files in the build/logs
# directory at the root of the repository and any of its
# subdirectories.
/build/logs/ @doctocat

# The `docs/*` pattern will match files like
# `docs/getting-started.md` but not further nested files like
# `docs/build-app/troubleshooting.md`.
docs/* docs@example.com

# In this example, @octocat owns any file in an apps directory
# anywhere in your repository.
apps/ @octocat

# In this example, @doctocat owns any file in the `/docs`
# directory in the root of your repository and any of its
# subdirectories.
/docs/ @doctocat

# In this example, any change inside the `/scripts` directory
# will require approval from @doctocat or @octocat.
/scripts/ @doctocat @octocat

# In this example, @octocat owns any file in a `/logs` directory such as
# `/build/logs`, `/scripts/logs`, and `/deeply/nested/logs`. Any changes
# in a `/logs` directory will require approval from @octocat.
**/logs @octocat

# In this example, @octocat owns any file in the `/apps`
# directory in the root of your repository except for the `/apps/github`
# subdirectory, as its owners are left empty. Without an owner, changes
# to `apps/github` can be made with the approval of any user who has
# write access to the repository.
/apps/ @octocat
/apps/github

# In this example, @octocat owns any file in the `/apps`
# directory in the root of your repository except for the `/apps/github`
# subdirectory, as this subdirectory has its own owner @doctocat
/apps/ @octocat
/apps/github @doctocat
```
