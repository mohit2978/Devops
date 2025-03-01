## Notes

![alt text](image.png)

Branching in other VCS is expensive as they copy whole chnanges to new branch but in git it only cpies chnages !! here creating ,moving from brnach to other is very easy!!

We have a head pointer which points to current branch!!

![alt text](image-1.png)

`git branch -v`--> tells last commit of all branches!!

![alt text](image-2.png)

Renaming a branch

![alt text](image-23.png)

Renaming a branch without going to it

![alt text](image-24.png)

deleting a branch

![alt text](image-25.png)

Let us suppose we created a brnach devlop from master

![alt text](image-3.png)

 we move to devlop branch

![alt text](image-4.png)

and then make a commit

![alt text](image-5.png)

now we move to master

![alt text](image-6.png)

and make a commit

![alt text](image-7.png)

`git log`--> tells commit history (canot see logs of diverging branch), can see brnaches which are a part of current brnach

`git log --oneline`--> tells in oneline

`git log --oneline --graph`--> tells in oneline in graph

`git log --oneline --graph --all`--> tells in oneline in graph in all brnaches

![alt text](image-8.png)

### Merge

Let us suppose from here

![alt text](image-9.png)

we create a branch impfix from master and created a commit on that

![alt text](image-10.png)

we commited to infix!! we want master to point to impfix!!

we want impfix to be merged with master!! so we need to move master so for that do checkout master first!!

Now we do `git merge impfix` on master branch

![alt text](image-11.png)

This is fast-forward merge!!we just changed the pointer so this is called as fast-forwarding!!

we have merged impfix now we can delete that branch!!

now on devlop if we do `git merge master` it will put all chnages of master to devlop but our main branch is master we do not want other branch chnages in master without testing!!

now one more commit on devlop branch

![alt text](image-12.png)

now we want two chnages of develop branch to master branch !!

we first need to go to master as we need to merge develop to master!!

after going to master we do `git merge devlop`

it ask for commit message !! give it

you see merge make but recursive strategy!!

![alt text](image-13.png)

see how master is updated!!

- first git find common ancestor as on that commit both brnaches have same changes

- after that git reads devlop branch and see all changes made by both branches and git will merge them in a new commit

- this is called as 3-way merge!!

- the new commit we made is called as merge commit!!

![alt text](image-14.png)

see above on master we have another commit

if we had impfix here it still be poiniting to 28bd1!!

![alt text](image-15.png)

![alt text](image-16.png)

### Merge vs Rebase

![alt text](image-17.png)

![alt text](image-18.png)

![alt text](image-19.png)

![alt text](image-20.png)

![alt text](image-21.png)

![alt text](image-22.png)