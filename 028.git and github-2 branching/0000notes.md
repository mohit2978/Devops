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

![alt text](image-11.png)

now one more commit on devlop branch

![alt text](image-12.png)

![alt text](image-13.png)

![alt text](image-14.png)

see above on master we have another commit

![alt text](image-15.png)

![alt text](image-16.png)

### Merge vs Rebase

![alt text](image-17.png)

![alt text](image-18.png)

![alt text](image-19.png)

![alt text](image-20.png)

![alt text](image-21.png)

![alt text](image-22.png)