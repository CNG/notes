# Git and Github cheat sheet

This is currently pretty basic. I'll try to add information in here as I need to look it up. So this is not an exhaustive [reference](http://gitref.org/basic/); it's just a way for me to stop looking the same things up over and over. ;-)

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

### Delete

    $ git rm README
    $ git commit -m "Removed README**"

### Move

    $ git mv "Current name.md" "New name.md"

### Commit
    
    $ git commit -m 'description'
    
### Amend Commit

    $ git commit --amend -m 'description'
    
### Push

    $ git remote add origin https://github.com/username/Hello-World.git
    $ git push origin master

Or if amending last commit:

    $ git push -f origin master 

### List branches

`git branch` show current branch?  
`git branch -a` show all branches  
`git branch -r` show all remote branches  
`git remote` info about remote in general  
`git ls-remote` list all references to branches and tags known  

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


## To Organize

    git checkout -b NAME

short for

    git branch NAME;
    git checkout NAME;

more

    git checkout master; 
    git cehckout -b "hotfix"; 
    git commit -a -m 'msg'; 
    git checkout master; 
    git merge hotfix; 
    git branch -d hotfix

