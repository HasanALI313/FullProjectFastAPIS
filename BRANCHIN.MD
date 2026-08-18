flowchart LR
  A["feature/<ticket>-<description>"] -->|Pull request| B["dev"]
  B -->|Release pull request| C["main"]

feature/* branches are short-lived branches for individual changes. Do not use one shared branch named feature.

dev is the integration branch. It accepts pull requests from feature/* branches.

main is the production-ready branch. It accepts pull requests only from dev.

Direct pushes, force pushes, and branch deletion are prohibited on dev and main.

Every change requires review and passing automated checks.

Naming

Use lowercase words separated by hyphens:

feature/MED-123-add-patient-search
feature/MED-148-redshift-load-audit

Keep each branch focused on one work item. Delete the remote feature branch after its pull request is merged.

Developer workflow

Start from the latest dev:

git switch dev
git pull --ff-only origin dev
git switch -c feature/MED-123-short-description

Commit and publish the feature:

git add .
git commit -m "feat: short description"
git push -u origin feature/MED-123-short-description

Open a pull request from feature/MED-123-short-description into dev. After review and successful checks, squash-merge it.

Release workflow

When dev is tested and ready for release, open a pull request from dev into main. Do not create a release pull request from a feature branch. After the pull request passes its checks and approvals, merge it and create a version tag:

git switch main
git pull --ff-only origin main
git tag -a v1.0.0 -m "Release v1.0.0"
git push origin v1.0.0

GitHub rulesets

Create two active branch rulesets under Settings → Rules → Rulesets.

Protect dev

Target branch: dev

Restrict deletions

Block force pushes

Require a pull request before merging

Require at least one approval

Dismiss stale approvals when new commits are pushed

Require approval of the most recent reviewable push

Require conversation resolution

Require status checks to pass; select branch-direction

Require the branch to be up to date before merging

Do not add bypass users unless an audited emergency process requires them

Protect main

Target branch: main

Apply every dev rule above

Require at least two approvals where team size permits

Require branch-direction and all test/security checks

Restrict deployments to an approved production environment

GitHub's pull-request rule prevents direct changes to protected branches. The checked-in workflow adds the source-branch restriction: only feature/* can target dev, and only dev can target main.

Emergency changes

Do not bypass main. Create a feature/hotfix-<description> branch from dev, merge it back into dev, validate it, and promote dev to main through the normal pull request. If an organizational incident process allows a bypass, document the approver, reason, incident number, and follow-up reconciliation.