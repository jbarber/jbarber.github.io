+++
title = 'GitLab CI First Impressions'
date = 2024-10-03T11:15:16+01:00
draft = false
+++
I've predominately used CircleCI for CI/CD for the last ~8 years (with some
GitHub Actions sprinkled in). I've recently started a project with GitLab and
I'm implementing a CI workflow using GitLab's CI/CD offering - which is
straightforwardly called "GitLab CI/CD".

Here's some of my biggest first impressions. As I say, I've got a lot of
experience with CircleCI, and not a lot of experience with GitLab - so I'm not
making any claims that these impressions are correct.

# No SSH'ing into workers in GitLab

This is a fantastic feature I loved having in CircleCI - if any job failed, you
could click a button to restart the job with SSH and it would automatically
spawn an SSH daemon so you could shell into the container that was running the
job.

This was amazing for when you were making changes to a job and needed to
debugging why it was failing, and more importantly, helping you to work out
what you should have done instead. Just log into the failing job, work out what
the command or configuration should have been, check it works (in the
container), then update the `.circleci/config.yml`, commit, push, and away you
go.

In comparison, I found fixing failing gitlab jobs a much more laborious process
involving multiple rounds of printf debugging and trial and error.

It seems this is
[something](https://forum.gitlab.com/t/debugging-ci-by-ssh-ing-into-the-running-container/43937)
[other](https://forum.gitlab.com/t/ssh-into-ci-server/36435)
[people](https://forum.gitlab.com/t/ssh-into-a-shared-runners-docker-container/14977)
have been looking to do as well, but there's no simple (or even any?) way to do it.

# GitLab rules and `changes`

GitLab has a feature called
[rules](https://docs.gitlab.com/ee/ci/jobs/job_rules.html) which allows you to
determine when a job should run. CircleCI has [branch filters](https://support.circleci.com/hc/en-us/articles/115015953868-Filter-workflows-by-branch) and [conditionals](https://circleci.com/docs/configuration-reference/#using-when-in-workflows) which gives you similar controls, but they aren't as good IMO.

Why?

CircleCI's config has limited information available to it when it's evaluated -
and one important thing that's missing is what GitLab provides in the `changes`
stanza. This lets you specify to run a job only when a particular file has
changed in the changeset. This is really important when your job is expensive
(i.e. integration / system tests) but doesn't need to be invoked (e.g.
documentation updates, unit test changes). This is something you can do in
CircleCI by having a step in the job that does a `git diff` and only executes the
expensive steps if the change is relevant - but if you're using
[Orbs](https://circleci.com/orbs/) (CircleCI's library mechanism) you can't do
this.

This might sound like a small issue - but having to run system tests (and deal
with failures and flakiness) adds friction to making improvements in those
parts of the system that aren't tested, which leads to less work being done in
updating documentation and refactoring tests - which will decrease your team's
performance.

# GitLab is batteries included

Part of the project I'm working on creates docker images for deployment, and
GitLab has a container registry included that you can push them to.

It's really nice just having this included so I don't have to use
DockerHub/GCP's Artifact Registry/whatever.

Even better, the CI jobs have credentials injected into them for the registry,
so I didn't have to mess about with setting them up.

# Differences in ordering jobs

CircleCI is very much based on DAGs - you explicitly state job B requires job A,
and it runs the jobs to satisfy these requirements. This is great because you
can have independent checks run in parallel, but them have a deployment job
which requires that all of those independent jobs pass before it'll kick off.

I think you can do this in GitLab, but it seems more focused on build stages.
In the course of writing this, I discovered this
[article](https://docs.gitlab.com/ee/ci/pipelines/pipeline_architectures.html)
which suggests a more CircleCI-like configuration is possible in GitLab. 
