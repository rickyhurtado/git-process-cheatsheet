# GIT PROCESS CHEAT SHEET

#### Note for Master Branch
  The master branch shall be deployed to the production server. Use the master branch to create staging branch (as throw away branch) or feature or working branch. Also use the master branch in updating the staging or working branch.

#### Note for Staging Branch
  The staging branch shall be deployed to the staging or UAT server. Pull requests to the master branch shall be merged if the features are already accepted in the UAT. If some of the pull requests are merged to the master branch (and there are few shall be left on the pull requests page) and happens weekly, the staging branch shall be deleted and re-created. By doing this, we keep the the staging branch synced with the master branch as much as possible to avoid possible more merge conflicts.
  
  The following shall be done weekly in re-creating the staging branch given that there were pull requests merged to the master branch:
  
    git checkout master
    git pull origin master
    git branch -D staging (force delete local)
    git push origin :staging (force delete remote)
    git checkout -b staging (re-created the staging branch)
    git push origin staging (then deploy the staging branch immediately to UAT server)
  
  The processes above do not include the working branches on the pull requests page. These branches shall be rebased again with the master branch befire merging them to the staging for UAT. Let's say that the working_branch is one of the branches in the pull requests, do the following:
  
    git checkout working_branch
    git rebase master (fix all conflicts if there's any)
    git push working_branch
    git checkout staging
    git pull --rebase origin staging (fix all conflicts if there's any)
    git merge working_branch (fix all conflicts if there's any)
    git push origin staging (if unable to push, just repeat the git pull --rebase origin staging process)
  
  Notify the person responsible for UAT and notify the person responsible to deploy the master branch to production server.

#### Creating a Staging or Working Branch
  Always update the master labranch before creating a new working branch or rebasing it to a working branch or re-creating the staging branch.

  Creating a new staging or working branch:

    git checkout master
    git pull origin master
    git checkout -b new_working_branch (use underscore as separator for consistency)

#### Rebasing a Working Branch
  If there are changes that are not included in the commits, stash them.
  
    git stash
  
  Then update the master branch before rebasing it to a working branch:
  
    git checkout master
    git pull origin master
    git chekout working_branch
    git rebase master

### Fixing Conflicts
  If there are merge conflicts during rebase process, fix all the conflicts of each files. Once done with the fixing of the conflicts of the current file, always add the file before continuing the rebase process:
  
    git add path_of_the_file
  
  Then continue the rebase process:
  
    git rebase --continue

  If --continue didn't proceed despite the conflicts are merged, do this instead:
  
    git rebase --skip

  Just incase you would like to repeat the rebasing process, abort the rebase process first:
  
    git rebase --abort
  
#### Working Branch for User Acceptance Test (UAT - staging server)
  Working branch shall be merged to staging branch manually on local computer and not by pull request then merge. Make sure that your working branch is synced (rebased) with the master branch before merging it to the staging branch. Before doing the merging process, update the staging branch first by doing:
  
    git pull --rebase origin staging

  Fix merge conflicts. If you don't want to do merge conflicts then delete the staging branch and get the latest update instead by doing:
  
    git checkout master
    git branch -D staging
    git fetch origin fetch
    git checkout staging
  
  After updating the staging, manually merge the working branch for UAT:
  
    git checkout staging (if not yet on the staging branch)
    git merge working_branch (fix merge conflicts if there's any)
    git push origin staging
  
  If unable to push the staging branch despite it is updated (usually happens when someone updated the staging branch), update again the staging branch by doing:
  
    git pull --rebase origin staging (fix merge conflicts if there's any)

#### Creating Pull Request
  Pull request happens if there is a working branch ready for production. Pull requests can be merged only if they pass in the UAT. For some reasons, the working branch might be behind with the recent commits of the master branch and merging is not possible despite that the working branch was previously synced with the maste branch. If this happens, rebase again master branch to working branch by repeating the Rebasing a Working Branch section. The owner of the working branch shall be responsible in syncing with the master branch or merging it to master branch. Those who has permission can deploy the master or staging branch to the server.
  
  All branches that are merged to master shall be deleted. After the merging of pull request, see the Updating the Local Branches section.

#### Updating the Local Branches
  Sync the remote branches (origin/branches) in your local machine with the branches in the GIT server.
  
    git fetch --prune
  
  The --prune option shall include the deletion of the branches (branches deleted inthe GIT server).
  
  Delete the working branches from your local machine if there is a GIT message for those branches deleted from the GIT server but doing:
  
    git branch -d working_branch

If unable to delete the branch, this means that the master branch is not updated so it must be updated and deleted the branch:

    git checkout master
    git pull origin master
    git branch -d working_branch
  
  Force delete branch if needed by doing:
  
    git branch -D working_branch
  
#### Display the Local and Remote Braches
  Local:
  
    git branch

  Remote:
  
    git branch -r

#### Bug Fixes
  If there are bugs found in the UAT, simply fix the changes in your working branch and repeat the process in merging the working branch to the staging branch. Push the updated working branch. Pull request for this branch shall be updated.
  
#### Hot Fixes
  If there bugs found in the production, branch out from the master branch and do the fixes. Push the fix bug branch and create a pull request. Immediately do the merging via Bitbucket/Github to apply the changes to the master branch and deploy it to production. UAT shall be done then in the production server instead.
