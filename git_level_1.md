# Git Level 1 (Tasks 1.1-1.5) 

## 1.1 Set Up Git Repository on Storage Server

The Nautilus development team shared with the DevOps team requirements for new application development, setting up a Git repository for that project. Create a Git repository on Storage server in Stratos DC as per details given below:

Install git package using yum on Storage server.

After that create/init a git repository /opt/cluster.git (use the exact name as asked and make sure not to create a bare repository).

---

```bash
$ sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15

$ sudo su -

$ yum install git -y

$ git init --bare /opt/cluster.git
hint: Using 'master' as the name for the initial branch. This default branch name
hint: is subject to change. To configure the initial branch name to use in all
hint: of your new repositories, which will suppress this warning, call:
hint:
hint:   git config --global init.defaultBranch <name>
hint:
hint: Names commonly chosen instead of 'master' are 'main', 'trunk' and
hint: 'development'. The just-created branch can be renamed via this command:
hint:
hint:   git branch -m <name>
Initialized empty Git repository in /opt/cluster.git/

$ ls -la /opt/cluster.git
total 40
drwxr-xr-x 7 root root 4096 May 20 08:37 .
drwxr-xr-x 1 root root 4096 May 20 08:37 ..
-rw-r--r-- 1 root root   23 May 20 08:37 HEAD
drwxr-xr-x 2 root root 4096 May 20 08:37 branches
-rw-r--r-- 1 root root   66 May 20 08:37 config
-rw-r--r-- 1 root root   73 May 20 08:37 description
drwxr-xr-x 2 root root 4096 May 20 08:37 hooks
drwxr-xr-x 2 root root 4096 May 20 08:37 info
drwxr-xr-x 4 root root 4096 May 20 08:37 objects
drwxr-xr-x 4 root root 4096 May 20 08:37 refs
```
✅

## 1.2 Clone Git Repository on Storage Server

The DevOps team established a new Git repository last week, which remains unused at present. However, the Nautilus application development team now requires a copy of this repository on the `Storage Server` in the Stratos DC. Follow the provided details to clone the repository:

The repository to be cloned is located at `/opt/cluster.git`

Clone this Git repository to the `/usr/src/kodekloudrepos` directory. Ensure no modifications are made to the repository during the cloning process.

---

```bash
$ sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15

sudo su -

$ cd /usr/src/kodekloudrepos/

$ git clone /opt/cluster.git
Cloning into 'cluster'...
warning: You appear to have cloned an empty repository.
done.
```
✅

## 1.3 Fork a Git Repository

There is a Git server utilized by the Nautilus project teams. Recently, a new developer named Jon joined the team and needs to begin working on a project. To begin, he must fork an existing Git repository. Follow the steps below:

Click on the `Gitea UI` button located on the top bar to access the Gitea page.

Login to `Gitea` server using username `jon` and password `Jon_pass123`.

Once logged in, locate the Git repository named `sarah/story-blog` and `fork` it under the `jon` user.

`Note:` For tasks requiring web UI changes, screenshots are necessary for review purposes. Additionally, consider utilizing screen recording software such as loom.com to record and share your task completion process.

---

![Screenshot_13](https://github.com/user-attachments/assets/639ea47a-d1fe-4c2a-9ded-e6a136979079)
![Screenshot_14](https://github.com/user-attachments/assets/2c65d938-ad0b-4931-8f8f-cfce3a669899)
✅

## 1.4 Update Git Repository with Sample HTML File

The Nautilus development team has initiated a new project development, establishing various Git repositories to manage each project's source code. Recently, a repository named `/opt/news.git` was created. The team has provided a sample `index.html` file located on the `jump host` under the `/tmp` directory. This repository has been cloned to `/usr/src/kodekloudrepos` on the `storage server` in the `Stratos DC`.
1. Copy the sample `index.html` file from the `jump host` to the `storage server` placing it within the cloned repository at `/usr/src/kodekloudrepos/news`.
2. Add and commit the file to the repository.
3. Push the changes to the `master` branch.

---

```bash
$ scp /tmp/index.html natasha@172.16.238.15:/tmp/

$ sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15

$ mv /tmp/index.html /usr/src/kodekloudrepos/news/

$ sudo su -

$ cd /usr/src/kodekloudrepos/news

$ git branch
* master

$ git add index.html
$ git commit -m "add index.html"
[master d9ea652] add index.html
 1 file changed, 1 insertion(+)
 create mode 100644 index.html
$ git push
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 36 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 331 bytes | 331.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To /opt/news.git
   607954e..d9ea652  master -> master
```
✅

### 1.5 Delete Git Branch

The Nautilus developers are engaged in active development on one of the project repositories located at `/usr/src/kodekloudrepos/bet`a. During testing, several test branches were created, and now they require cleanup. Here are the requirements provided to the DevOps team:

On the `Storage server` in Stratos DC, delete a branch named `xfusioncorp_beta` from the `/usr/src/kodekloudrepos/beta` Git repository.

---

```bash
$ sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no natasha@172.16.238.15

cd /usr/src/kodekloudrepos/beta

git config --global --add safe.directory /usr/src/kodekloudrepos/beta

$ git branch
  master
* xfusioncorp_beta

$ sudo git checkout master

$ sudo git branch -d xfusioncorp_beta
Deleted branch xfusioncorp_beta (was 8ee35d2).

$ git branch
* master

$ git push origin --delete xfusioncorp_beta
error: unable to delete 'xfusioncorp_beta': remote ref does not exist
error: failed to push some refs to '/opt/beta.git'
```
✅
![Screenshot_16](https://github.com/user-attachments/assets/d0a19712-e01b-49bd-9d2f-4fd0b1f14bc9)

