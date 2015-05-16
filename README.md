# GIT PROCESS CHEAT SHEET

## Note for Master Branch
  Use the master branch to create staging branch (as throw away branch) or feature or working branch. Also use the master branch in updating the staging or working branch.

#### Creating a Staging or Working Branch
  Always update the master branch before creating a new working branch or rebasing it to a working branch or re-creating the staging branch.

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

  Fix merge conflicts. If you don't want to do merge conflicts then delete the staging branch and get the lates update instead by doing:
  
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
  
  All branches that are merged to master shall be deleted.
  
#### Bug Fixes
  If there are bugs found in the UAT, simply fix the changes in your working branch and repeat the process in merging the working branch to the staging branch. Push the updated working branch. Pull request for this branch shall be updated.
  
#### Hot Fixes
  If there bugs found in the production, branch out from the master branch and do the fixes. Push the fix bug branch and create a pull request. Immediately do the merging via Bitbucket/Github to apply the changes to the master branch and deploy it to production. UAT shall be done then in the production server instead.
  
#### Updating the Local Branches
