# Git and Github cheat sheet

## Basic workflow

### Create repo

Create repo on [Github](https://github.com/repositories/new)

    $ cd ~/Documents/Work/Projects
    $ mkdir Hello-World
    $ cd Hello-World
    $ git init
    $ touch README

### Stage

Recursively add all files in `cwd`:

    $ git add .

Or see which files need to be added and add individually:

    $ git status -s
    $ git add file1 file2

### Commit
    
    $ git commit -m 'description'
    
### Amend Commit

    $ git commit --amend -m 'description'
    
### Push

    $ git remote add origin https://github.com/username/Hello-World.git
    $ git push origin master

Or if amending last commit:

    $ git push -f origin master 

### Revert Master branch to upstream

    $ git remote update
    $ git reset --hard upstream/master

Then push this new branch-head to your origin repository, ignoring that it won't be a fast forward:

    $ git push origin +master


    
## Fork repo and copy to local

    $ cd ~/Documents/Work/Projects
    $ git clone https://github.com/CNG/forecast.io-php-api.git
    $ cd forecast.io-php-api
    $ git remote add upstream https://github.com/tobias-redmann/forecast.io-php-api.git

### Push commits to Github

    $ git push origin master
    
### Pull in upstream changes and automatically merge

    $ git pull upstream

### Pull in upstream changes and prompt on merge conflicts

    $ git fetch upstream
    $ git merge upstream/master
    
### Branches

Details at <https://help.github.com/articles/fork-a-repo>
    
### Contribute back to original with pull request

<https://help.github.com/articles/using-pull-requests>

## Git resources

<http://gitref.org/basic/>

## To Organize

git checkout -b NAME
short for git branch NAME; git checkout NAME;

git checkout master
git cehckout -b "hotfix"
git commit -a -m 'msg'
git checkout master
git merge hotfix
git branch -d hotfix

