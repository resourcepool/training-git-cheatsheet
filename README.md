#Git Cheatsheet  

##1. Git Configuration
###1.1. GitConfig
>`$ git config --global user.name "your name"`  

>`$ git config --global user.email "your@email.com"`  

Edit the .gitconfig file via the command:  

>`$ git config --global -e`  

To activate the syntactic color and the "tree" alias, add the following lines to the .gitconfig: 
>  [alias]  
   tree = log --graph --decorate --pretty=oneline --abbrev-commit --all  
  
>  [color]  
   branch = auto  
   diff = auto  
   interactive = auto  
   status = auto  

###1.2. End of Line management
**Windows** : The end of lines are modeled by a CRLF (Carriage Return Line Feed <=> \r\n)  
**Linux** : modeled by LF (Line Feed <=> \n)  
**Git** : all is stored as LF by convention  

**Windows Client** : must always process a **CRLF → LF** conversion when writing in the database, and must always process a **LF →CRLF** conversion when retrieving data from the base.
>`$ git config --global -e core.autocrlf true`

**Linux Client** : must always process a **CRLF → LF** conversion when writing in the database (but not needed normaly) and does not need any conversion when retrieving data.
>`$ git config --global -e core.autocrlf input`

##2. Initialization or Cloning a repository
###2.1. Initialization of a new central repo (i.e. origin)
For a new project, you have to initialize a central repository on your server. From there, other clients will connect and push theirs modifications. To initialize a myrepo project:
>`$ mkdir myrepo.git`  
>`$ cd myrepo.git`  
>`$ git –-bare init`  

###2.2. Initialization of a new local repo
Go to your project root, and run the following command:
>`$ git init`

If the file has a central remote repo (i.e. remote), it has to be declared. By convention, « origin » should be the name for the main repo.  
*N.B.: You can have any number of remote repos as git is fully distributed*  
The deposit could be an URI:  
>`$ git remote add origin https://github.com/me/myrepo.git`  

or something else (e.g. a remote repo via ssh):  
>`$ git remote add origin git@myserver:~/git/myrepo.git`  
or even:  
>`$ git remote add origin /home/git/myrepo.git`  

###2.3. Cloning an existing repo
If the project already exists (i.e.: already has a remote repo), you only need to clone it:
>`$ git clone git@myserver:~/git/myrepo.git`  

A myrepo folder will be created in the directory you run the command at.

##3. Main Commands

###3.5. Must-Have Commands  
####Show state of the Staging area
>`git status`

####Show main tree with all the branches (warning: it is an alias created with your gitconfig!)
>`git tree`

####Stash your non-committed changes to temporarily switch to another branch
>`git stash`

####Redo (i.e. pop) your non-committed changes when back to the branch
>`git stash pop`

###3.2. Branches
(+) Create a new branch at the actual location (<=> position in the tree) without selecting it:
>`$ git branch branch_name`  

(+) Go on a branch and create it at the actual location if it does not exist yet:
>`$ git checkout -b branch_name`  

(x) Delete a branch from the local repo:
>`$ git branch -D branch_name`

(x) Delete a branch from the origin repo: (two solutions)  
>`$ git push origin --delete branch_name` (git v1.7.0+)  
or  
>`$ git push origin :branch_name` (older versions but also works with newer)

(>) Push a branch to the origin repo:
>`$ git push origin localbranch:remotebranch`  
or if both branches have the same name:  
>`$ git push origin branch .`  
use `-f` to force the update (WARNING: will override the remote branch with yours)  
>`$ git push origin -f branch`  

(T) Create an annoted tag on a commit:
>`$ git tag -a tag_name sha_of_commit -m "tag for version vX.X"`  

(T) Create a lightweight tag on a commit:
>`$ git tag tag_name sha_of_commit`  

(<) Get last version of branches from the origin repo:
>`$ git fetch origin`  



###3.3. Index
(+) Add new files or modified files to the index: (3 examples)  
>`$ git add .`  
>>add all files of the current folder and its subfolder  

>`$ git add -A`
>>add all files (including deleted and modified files) to the index

>`$ git add file2.txt folder1/* folder2/*.txt`  
>> add file2.txt, all files of folder1 folder , and all text files of folder2 folder


(x) Remove files from the index (will be marked as unstaged or untracked files) (2 examples):
>`$ git reset HEAD`  
>>remove all non-commited files from the index

>`$ git reset HEAD file1.txt folder1/*`  
>>remove file1.txt and all files of folder1 from the index  

###3.4. Commit
(+) Commit changes added to the index:
>`$ git commit -m "message of your commit" .`  

(+) Add other files to the index to the last commit (e.g. in case of omission), if you did not push on the remote repo yet:  
>`$ git commit --amend -m "new message of commit which will replace the old one"`  

(x) Properly cancel a commit (will create a new commit which contains the reverted changes from the previous one):
>`$ git revert HEAD~numberOfCommitToBeCancelled`  
>>e.g.: Cancel the previous commit:  
>`$ git revert HEAD~1`  

###3.5. Rebase and interactive rebase
(^) Apply commits of the remote branch which differs from the local branch:
>`$ git rebase origin/branchname`  

(e) Start an interactive rebase of the 4 last commits:  
>`$ git rebase -i HEAD~3`  

For example, something like this is shown:  
>pick 4995a59 my second commit  
>pick 531b54f my third commit  
>pick 0e9c8cc my forth commit  

One can now modifiy the content to process the following tasks:

####(e) Inversing order of commits 531b54f and 0e9c8cc:  
>pick 4995a59 my second commit  
>pick 0e9c8cc my forth commit  
>pick 531b54f my third commit  

####(e) Fusionning commit 531b54f with its previous 4995a59:  
>pick 4995a59 my second commit  
>squash 531b54f my third commit  
>pick 0e9c8cc my forth commit  

####(x) Deleting commit 531b54f:  
>pick 4995a59 my second commit  
>\#Delete this line: pick 531b54f my third commit   
>pick 0e9c8cc my forth commit  

####(e) Editing commit 531b54f:  
>pick 4995a59 my second commit  
>**edit** 531b54f my third commit  
>pick 0e9c8cc my forth commit  

Then perform your modifications, commit, and execute  
>`$ git rebase --continue`  

###3.6. Merge and mergetool
(c) Configure a mergetool (e.g. replace mymt by your mergetool: meld,kdiff3,...)  
>`git config --global merge.tool mymt"`  

(m) Launch a merge of the mybranchy branch on the actual branch (mybranchx)  
>`git merge mybranchy -m "merge of mybranchy on mybranchx"`  


##Bibliography
A very easy-to-follow and complete git training
>[http://git-scm.com/book/en](http://git-scm.com/book/en)  

A very complete book about git  
>[https://github.com/blog/1640-git-internals-pdf-open-sourced](https://github.com/blog/1640-git-internals-pdf-open-sourced)  

A cheatsheet of the git commands (listed in layers)
>[http://ndpsoftware.com/git-cheatsheet.html](http://ndpsoftware.com/git-cheatsheet.html)

A very good document to understand and wisely choose the most adapted workflow for your project
>[https://www.atlassian.com/git/workflows#!workflow-release-cycle](https://www.atlassian.com/git/workflows#!workflow-release-cycle)

An article about the use of gitflow
>[http://blogs.endjin.com/2013/04/a-step-by-step-guide-to-using-gitflow-with-teamcity-part-3-gitflow-commands](http://blogs.endjin.com/2013/04/a-step-by-step-guide-to-using-gitflow-with-teamcity-part-3-gitflow-commands/)  

##Contributors

@Loicortola for the CHEATSHEET redaction  
@VinceFoixCable for the review and english translation  
