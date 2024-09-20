
## Windows OS

=> Developed by Microsoft (Bill Gates)

=> It is GUI based OS (Graphical User Interface)

=> It is single user based OS

=> It is commercial OS (paid)

=> Security features are less (Anti-virus is required)

=> Windows OS is recommended for personal use

Ex: Play Games, Watch Movies, Internet Browsing, Store data, Online Classes...


>Note: Windows is not recommended for business use (servers, application deployment..)	

### Linux Operating System


=> Linux is a community based OS

=> Linux is free & Open Source

=> Linux is Multi User OS

=> Linux Provides High Security

=> Linux is highly recommended for project operations

=> Linux is CLI based OS (Command Line Interface)

> Note: In real-time we will use Linux machines to setup our infrastructure such as server etc.

### History


=> Developed by Linus Torvalds

=> Initially Linus Torvalds was using Unix OS and found some challenges in that and informed to that company but they did not accept his suggestions.

=> Linus started doing research and he found Minux OS is similar to his ideas.

=> He has taken Minux OS and made few changes to that and released into market as Linux OS.

		(Lin)us   + Min(ux) = Linux

### Distributions


- Linus Tarvalds given Linux os as free and open source

- Many companies downloaded Linux OS code and modified according to their requirement and released into market with different names. Those are called as Linux Distributions.

    - Ex: Amazon Linux, Red Hat Linux, Ubuntu Linux, Cent OS Linux, SuSe linux....

> Note: We have 200+ linux distributions

till now we have seen how to connect to a linux machine using aws!!

### Linux Machine Setup


1) Login into AWS cloud account

2) Create Linux Virtual Machine using AWS Ec2 service

3) Connect with Linux VM using MobaXterm / Putty


Connection with MobaXterm : https://youtu.be/uI2iDk8iTps?si=ZuZs0lQTxoRpbRMk

Connection with putty : https://youtu.be/GXc_bxmP0AA?si=HgSydrP89mPxv23s

### Linux File System


=> Everything is represented as a file

=> 3 types of files

1) Ordinary file / Normal file (starts with -)

2) Directory file (Folder) (starts with d)

3) Link File (starts with l)

### Linux commands

ls : list content

- $ ls    (display files in present working directory)

- $ ls -l (display files in alphabetical order)
        
- $ ls -lr (display files in reverse of alphabetical order)

- $ ls -lt (display latest files on top)

- $ ls -ltr (display old files on top)

- $ ls -la (display hidden files)


mkdir : To create directory (folder)

rmdir : To delete empty directory

cd : change directory

- cd <dir-name> : To go inside directory

- cd .. : come out from directory

touch : To create empty files

- $ touch f1.txt f2.txt f3.txt		

rm : To delete file & directories

- $ rm <file-name>

- $ rm *.txt

- $ rm a*.txt

- $ rm -rf <dir-name>

mv : To rename & to move

- $ mv  <present-name> <new-name>

- $ mv  <present-location>  <new-location>

cat : To create file with data + append data to existing file + view file data

- $ cat > f1.txt 

- $ cat >> f1.txt

- $ cat f1.txt

- $ cat -n f1.txt