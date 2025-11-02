# Git Level 2 (Tasks 2.1-2.5) 

## 2.1 Git Install and Create Repository

The Nautilus development team shared with the DevOps team requirements for new application development, setting up a Git repository for that project. Create a Git repository on `Storage server` in Stratos DC as per details given below:

Install `git` package using `yum` on `Storage server`.

After that, create/init a git repository named `/opt/demo.git` (use the exact name as asked and make sure not to create a bare repository).

---

```bash
$ sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15

$ sudo su -

$ yum install git -y

$ git -v
git version 2.47.1

$ git init /opt/demo.git

$ ls -la /opt/demo.git/
total 12
drwxr-xr-x 3 root root 4096 May 28 21:19 .
drwxr-xr-x 1 root root 4096 May 28 21:19 ..
drwxr-xr-x 7 root root 4096 May 28 21:19 .git
```
✅

## 2.2 Git Create Branches

Nautilus developers are actively working on one of the project repositories, `/usr/src/kodekloudrepos/ecommerce`. Recently, they decided to implement some new features in the application, and they want to maintain those new changes in a separate branch. Below are the requirements that have been shared with the DevOps team:
- On `Storage server` in Stratos DC create a new branch `xfusioncorp_ecommerce` from `master` branch in `/usr/src/kodekloudrepos/ecommerce` git repo.
- Please do not try to make any changes in the code.

---

```bash
$ sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15

$ cd /usr/src/kodekloudrepos/ecommerce/

$ git config --global --add safe.directory /usr/src/kodekloudrepos/ecommerce

$ git branch
* kodekloud_ecommerce
  master

$ sudo git checkout -b xfusioncorp_ecommerce master 
Switched to a new branch 'xfusioncorp_ecommerce'

$ git branch
  kodekloud_ecommerce
  master
* xfusioncorp_ecommerce
```
✅

## 2.3 Git Merge Branches

The Nautilus application development team has been working on a project repository `/opt/games.git`. This repo is cloned at `/usr/src/kodekloudrepos` on `storage server` in `Stratos DC`. They recently shared the following requirements with DevOps team:

Create a new branch `nautilus` in `/usr/src/kodekloudrepos/games` repo from `master` and copy the `/tmp/index.html` file (present on `storage server` itself) into the repo. Further, `add/commit` this file in the new branch and merge back that branch into `master` branch. Finally, push the changes to the origin for both of the branches.

---

```bash
$ sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15

$ sudo su -

$ cd /usr/src/kodekloudrepos/games

$ git branch
* master

$ git checkout -b nautilus master 
Switched to a new branch 'nautilus'
$ git branch
  master
* nautilus

$ cp /tmp/index.html .
$ ls
index.html  info.txt  welcome.txt

$ git status
On branch nautilus
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        index.html

$ git add index.html 
$ git status
On branch nautilus
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   index.html

$ git commit -m "Add index.html from /tmp"
[nautilus 19fe3ea] Add index.html from /tmp
 1 file changed, 1 insertion(+)
 create mode 100644 index.html

$ git push origin nautilus
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 16 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 338 bytes | 338.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To /opt/games.git
 * [new branch]      nautilus -> nautilus

$ git checkout master
$ git merge nautilus
Updating 8a52c43..19fe3ea
Fast-forward
 index.html | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 index.html

$ git push origin master
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0
To /opt/games.git
   8a52c43..19fe3ea  master -> master
```
✅

## 2.4 Git Manage Remotes

The xFusionCorp development team added updates to the project that is maintained under `/opt/news.git` repo and cloned under `/usr/src/kodekloudrepos/news`. Recently some changes were made on Git server that is hosted on `Storage server` in `Stratos DC`. The DevOps team added some new Git remotes, so we need to update remote on `/usr/src/kodekloudrepos/news` repository as per details mentioned below:
- In `/usr/src/kodekloudrepos/news` repo add a new remote `dev_news` and point it to `/opt/xfusioncorp_news.git` repository.
- There is a file `/tmp/index.html` on same server; copy this file to the repo and add/commit to master branch.
- Finally push `master` branch to this new remote origin.

---

```bash
sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15

sudo su -

$ cd /usr/src/kodekloudrepos/news

$ git remote add dev_news /opt/xfusioncorp_news.git

$ cp /tmp/index.html .

$ git checkout master
Already on 'master'
Your branch is up to date with 'origin/master'.

$ git add index.html

$ git commit -m "Add index.html file from /tmp"
[master 7201d01] Add index.html file from /tmp
 1 file changed, 10 insertions(+)
 create mode 100644 index.html

$ git push dev_news master
Enumerating objects: 6, done.
Counting objects: 100% (6/6), done.
Delta compression using up to 16 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (6/6), 593 bytes | 593.00 KiB/s, done.
Total 6 (delta 0), reused 0 (delta 0), pack-reused 0
To /opt/xfusioncorp_news.git
 * [new branch]      master -> master
```
✅

### 2.5 Git Revert Some Changes

The Nautilus application development team was working on a git repository `/usr/src/kodekloudrepos/official` present on `Storage server` in `Stratos DC`. However, they reported an issue with the recent commits being pushed to this repo. They have asked the DevOps team to revert repo HEAD to last commit. Below are more details about the task:

In `/usr/src/kodekloudrepos/official` git repository, revert the latest commit `( HEAD )` to the previous commit (JFYI the previous commit hash should be with `initial commit` message ).

Use `revert official` message (please use all small letters for commit message) for the new revert commit.

---

```bash
$ sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15

$ sudo su -

$ cd /usr/src/kodekloudrepos/official

$ git log --oneline
bbb8149 (HEAD -> master, origin/master) add data.txt file
f19cb9c initial commit

$ git revert HEAD
[master 9d026d7] revert official
 1 file changed, 1 insertion(+)
 create mode 100644 info.txt

$ git log --oneline
9d026d7 (HEAD -> master) revert official
bbb8149 (origin/master) add data.txt file
f19cb9c initial commit
```
✅

<img width="1119" height="437" alt="2" src="https://github.com/user-attachments/assets/d9376742-41ba-4a13-b3f6-a9a192906109" />

✅
