# The Triage Bot

(last updated 21 April 2016)

## Overview

The [Ansibull PR Triage Bot](https://github.com/ansible/ansibullbot/blob/master/triage.py) serves many functions:
* Responds quickly to PR submitters to thank them for submitting their PR;
* Identifies the community maintainer responsible for reviewing PRs for any files affected;
* Tracks the current status of PRs;
* Pings responsible parties to remind them of any PR actions that they may be responsible for;
* Provides maintainers with the ability to move PRs through our workflow;
* Identifies PRs abandoned by their submitters so that we can close them;
* Identifies modules abandoned by their maintainers so that we can find new maintainers.

## Community Maintainers

Each module in Core and Extras has at least one assigned maintainer, listed in two maintainers files: one for [Core](https://github.com/ansible/ansibullbot/blob/master/MAINTAINERS-CORE.txt) and one for [Extras](https://github.com/ansible/ansibullbot/blob/master/MAINTAINERS-CORE.txt). 

Some modules have no community maintainers assigned. In this case, the maintainer is listed as "ansible". Ultimately, it's our goal to have at least one community maintainer for every module.

The maintainer's job is to review PRs and decide whether that PR should be merged ("shipit!") or revised ("needs_revision"). 

The ultimate goal of any Pull Request is to reach "shipit" status, where the Core team then decides whether the PR is ready to be merged. Not every PR that reaches the "shipit" label is actually ready to be merged, but the better our reviewers are, and the better our guidelines are, the more likely it will be that a PR that reaches "shipit" will be mergeable.

## Workflow 

The triage bot runs every six hours and examines every open PR in both core and extras repositories, and enforces state roughly according to the following workflow:

* If a PR has no workflow labels, it's considered "new".  Files in the PR are identified, and the maintainers of those files are pinged by the bot, along with instructions on how to review the PR.  (Note: sometimes we strip labels from a PR to "reboot" this process.)
  * If the module maintainer is not "ansible", the PR then goes into the "community_review" state.
  * If the module maintainer is "ansible", the PR then goes into the "core_review" state (and probably sits for a while).
* If the PR is in "community_review" and has received comments from the maintainer:
  * If the maintainer says "shipit", the PR is labeled "shipit", whereupon the Core team assesses it for final merge.
  * If the maintainer says "needs_info", the PR is labeled "needs_info" and the submitter is asked for more info.
  * If the maintainer says "needs_revision", the PR is labeled "needs_revision" and the submitter is asked to fix some things.
* If the PR is in "needs_revision/needs_info" and has received comments from the submitter:
  * If the submitter says "ready_for_review", the PR is put back into community_review/core_review and the maintainer is notified that the PR is ready to be reviewed again.
* If the PR is in "needs_revision/needs_info" and the submitter has not responded lately:
  * The submitter is first politely pinged after two weeks, pinged again after two more weeks and labeled "pending action", and then may be closed two weeks after tha.
  * If the submitter responds at all, the clock is reset.
* If the PR is in "community_review" and the reviewer has not responded lately:
  * The reviewer is first politely pinged after two weeks, pinged again after two more weeks and labeled "pending_action", and then may be reassigned to "ansible" / core_review, or often the submitter of the PR is asked to step up as a maintainer.
* If Travis fails, or if the code is not mergable, the PR is automatically put into "needs_revision" along with a message to the submitter explaining why.

There are corner cases and frequent refinements, but this is the workflow in general. 

## PR Labels

There are two types of PR Labels generally: _workflow labels_ and _information labels_.

### Workflow Labels
* __community_review__: Pull requests for modules that are currently awaiting review by their maintainers in the Ansible community.
* __core_review__: Pull requests for modules that are currently awaiting review by their maintainers on the Ansible Core team.
* __needs_info__: Waiting on info from the submitter.
* __needs_rebase__: Waiting on the submitter to rebase. (Note: no longer used by the bot.)
* __needs_revision__: Waiting on the submitter to make changes.
* __shipit__: Waiting for final review by the core team for potential merge. 

### Informational Labels
* __backport__: this is applied automatically if the PR is requested against any branch that is not devel. The bot immediately assigns the labels "backport" and "core_review".
* __bugfix_pull_request__: applied by the bot based on the templatized description of the PR.
* __cloud__: applied by the bot based on the paths of the modified files.
* __docs_pull_request__: applied by the bot based on the templatized description of the PR.
* __easyfix__: applied manually, inconsistently used but sometimes useful.
* __feature_pull_request__: applied by the bot based on the templatized description of the PR.
* __networking__: applied by the bot based on the paths of the modified files.
* __owner_pr__: largely deprecated. Formerly workflow, now informational. Originally, PRs submitted by the maintainer would automatically go to "shipit" based on this label; now, if the submitter is also a maintainer, we notify the other maintainers and still require one of the maintainers (including the submitter) to give a "shipit".
* __P1 - P5__: deprecated for modules because they were wildly inconsistent and not useful. The bot now strips these.
* __pending_action__: applied by the bot to PRs that are not moving. Reviewed every couple of weeks by the community team, who tries to figure out the appropriate action (closure, asking for new maintainers, etc).

### Special Labels
* __new_plugin__: this is for new modules or plugins that are not yet in Ansible. __Note: this kicks off a completely separate process, and frankly it doesn't work very well at present. We're working our best to improve this process.__

