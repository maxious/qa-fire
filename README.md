# README

## Why QA Fire?

QA Fire enables a continous delivery model using feature branches.

### Limitations of Gitflow

Gitflow, a common branching and merging strategy for git typically implements a stable/production branch (usually master)
and a forthcoming release branch (usually develop).

In this model pull requests are usually done in feature branches off develop or hotfix branches of master.
QA or functional sign off is typically done after the feature or hotfix branch has been merged.
Releases are cut from develop to master (via a release branch) once all of the merged features have been signed off by a QA or product owner.

The issue here is that QA becomes the bottleneck. If a feature cannot be QA'd then it holds up the release because the relevant code
is now in the to-b-deployed branch.

### Feature Branch Model

In the feature branch model code review and QA/sign-off both occur in the feature branch _before_ merging.
This way once a feature branch has been merged it is automatically ready for release.

In fact, because merged feature branches are inherently deployable there is little need for a separate develop branch and features
can be branched off master instead.

### Environment per Feature

A key requirement to allow sign-off on feature branches before merging is that the code is runnable/visitable by the QA(s) or the PO.
This is where QA Fire comes in.

QA Fire listens for Github open pull request webhooks. When received, QA Fire will:

TODO: Insert GH doco link

  1. clone the repo
  1. switch to the branch associated with the PR
  1. Spin up a dedicated cloud foundry environment
  1. Deploy the code to the dedicated environment

QA Fire also listens for Github close pull request webhooks. In these cases, when received QA Fire will destroy the environment.

At this stage QA fire doesn't update and redeploy if additional commits are made to the PR (see LIMITATIONS).

## Webhooks

QA Fire uses the [github_webook](https://github.com/ssaunier/github_webhook) gem to listen to incoming webhooks from Github.

### Setting up a webhook

TODO: Explain how to set up a webhook

### Webhook Secret

The webhook secret should be set via the `GITHUB_WEBHOOK_SECRET` environment variable.

## Cloud Foundry

QA Fire makes all deployments to cloud foundry (see app/services/server.rb) and follows these basic steps:

    git clone <repo-in-pr>
    git checkout <branch-in-pr>
    cf push <app-name> -f manifest-qa.yml --no-start # app must have a manifest-qa.yml file
    cf create-service dto-shared-pgsql shared-psql <app-name>-db
    cf bind-service <app_name> <app-name>-db
    cf start <app-name>

### QA Manifest

TODO: Explain the QA Manifest

### Setting of environment variables

TODO: Explain that env vars are set of QA fire and then cloned to the app

TODO:
Feature switches
Deployment vs Release

## Limitations

* doesn't handle updates to the PR yet
* would be nice if it updated the PR with a comment containing the URL
* would be great to do a LGTM style check for QAPASS or something
* doesn't handle auth to CF yet!
* env vars must be set on QA fire (we can't copy vars from apps or read from a DB at this point)

