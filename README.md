# TantilloLab
## Welcome
Hello there!😊

This repository is for Tantillo Lab general information on:

[1. Comet account set up and common questions.](#A)

[2. Campus MPS cluster access.](#B)

<a name="A"><a/>
## 1. Comet new users guide

This section is meant to help you set up comet supercomputer account and help you learn how to submit gaussian jobs. 

### Table of content 
***
1. [Set up XSEDE account / Obtain Gaussian permission](#1)
2. [Login through terminal](#2)
3. [Set up Job Script](#3)
4. [Run Gaussian jobs](#4)
5. [Keep track of your usage](#5)
6. [Common questions and errors](#6)
0. [Questions about Comet?](#6)

<a name="1"><a/>
#### 1. Set up XSEDE account / Obtain Gaussian permission
1.	Go to [XSEDE portal](http://portal.xsede.org/#/guest), then go to Create Account.
2.	Remember your username/password.
3.	Email Dean and ask to be added to XSEDE group account.
![](images/Picture1.png)
4.	Obtain Gaussian permission: Hover your mouse over “HELP” in the menu bar, and click on “Help Desk”. Submit a ticket asking for Gaussian permissions for your comet account.
![](images/Picture2.png)
![](images/Picture3.png)

#### 2. Login through terminal
<a name="2"><a/>

1. To log in to your account through the terminal: Use the command (they’re the same) in terminal (if you are not familiar with bash, see lab manual first chapter):
`ssh username@comet.sdsc.xsede.org` or `ssh username@comet.sdsc.edu`

2. Your password should be the same as on the XSEDE website. Sometimes it’ll take a while for your account to be ready. If you can’t access it, wait a day and try again. If you still can’t log in, you can try to reset your XSEDE password.

#### 3. Set up Job Script
<a name="3"><a/>
To run a Gaussian.com file, you need to set up .job file first. The job file tells the computer how many processors/how much memory you will use, and calls your gaussian input.

Here’s how to make a .job file:

1. Download “cometg09” in the github repository. Sync it to your Comet account home.
2. Make a folder called "bin" in your Comet account home (if it's not there already):  `mkdir ~/bin/` and place the script ("cometg09", see file ) in the bin folder `mv cometg09 ~/bin/`
3. Do `chmod 755 cometg09` to make it executable. For gaussian 16, please use  `module load gaussian/16.B.01` in the script instead.
4. In cometg09 file, **replace the existing email address with your address**.
5. MAKE SURE THAT **nodes=1** AND **ntasks-per-node=8**!


#### 4. Run Gaussian jobs
<a name="4"><a/>
1.	Set up your Gaussian input file as usual (Please note that the script we have is for job input with ".com" file type so don’t forget to make the respective changes, if you run other input types).
Here’s a sample .com file:
```
    %nprocshared=8
    %mem=16GB
    %chk=7_mEH.chk
    %nosave
    # opt freq b3lyp/6-31+g(d,p) 
 
    Title Card Required

    0 1
    C                  1.22871037    0.08515815    0.00000000
    C                  2.62387037    0.08515815    0.00000000
    ……
```
2.	Upload the .com from your computer to Comet, in specific project folders as how you prefer. 

3.	In the folder, do `cometg09 [molecule1].com`. A job file should appear as [molecule1].job. Edit the run time of the .job file. For example, #SBATCH -t 1:00:00 means one hour long job. Importantly, we are for time based on the processors you indicate in your job file! Again, **make sure your com file has**:
```
    %nprocshared=8
    %mem=16GB
```

4.	Do `sbatch [molecule1].job` to submit your calculation. This job script then waits in a queue, and when it reaches the top will run your job.

    (Note, the queue is controlled by the "Partition" variable as described above. Other than that, you can't control which one run first or second. It all depend on their system calculation based on what processors are available and how much the job is asking for.)

5.	Job monitoring:

    The program used for this queue is called Slurm. Here are a few basic commands for slurm:

    `squeue -u username` shows what jobs you’ve submitted, you can create an alias in your ~/.bash_profile so you won't have to type this all the time.

    `squeue | wc -l` shows the total number of jobs submitted to the supercomputer.

    `sbatch [molecule1].job` runs your job file.

    `scancel #######` cancels the job by number – you can get this from the queue.

    Note: Each job you run will produce a slurm#####.out file, where the number is the same as the job number. These are generally blank, and not important unless you need to troubleshoot a problem with the SLURM system.

6. Once the job finishes or terminates with error, the system should email you about the status. 


#### 5. Keep track of your usage
<a name="5"><a/>
    Log into your XSEDE web account/user portal, and under “allocations/usage” it should have the recommended burn rate so you can keep a good pace. 

#### 6. Common questions and errors
<a name="6"><a/>
1.  Storage

    If you encounter “disk space exceed” error when you submit job, means you run out of space in HOME (~100GB, check with du -h /home/username). You can either transfer files to local or somewhere else to save space or:
- Save files in `/oasis/projects/nsf/ucd114/username`
- Save files in temporary storage `/oasis/scratch/comet/username/temp_project`  
    For more information:  
    https://www.sdsc.edu/support/user_guides/comet.html#storage
    https://www.sdsc.edu/support/user_guides/oasis.html 

2.  Run CCSD

    Large jobs like CCSD can be run in here:`/oasis/scratch/comet/userName/temp_project/`, use the large-shared partition with 700G and then also use `/oasis/scratch/comet/$USER/temp_project/gscratch` for the scratch location. This location is not ideal for such IO so use it **only** if the normal approach fails.
  
#### 0. Questions about Comet?
<a name="6"><a/>
1.	Email Tiffany (yuezh@ucdavis.edu)
2.	Go to XSEDE user portal and [submit a ticket](https://portal.xsede.org/group/xup/tickets).


<a name="B"><a/>
## 2. Campus MPS cluster access 
1.  How to apply an account on peloton
    Fill out [this form](https://wiki.cse.ucdavis.edu/cgi-bin/mps.pl). Then you'll receive a email which give you your username and instruction.
2.  Both gaussian09 and 16 modules are available. 
3.  Set up job submission file as follow (**Don't forget to change email**):

```shell
input_file="#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=8
#SBATCH --export=ALL
#SBATCH -t 10:00:00
#SBATCH --mail-type=END,FAIL
#SBATCH --mail-user=yuezh@ucdavis.edu

export my_file=in_file
module load gaussian/16
g16 < in_file > id_name.log"

root_name=$(basename $1 .com)
input_file=$(echo "$input_file" | sed -e s/in_file/$1/g -e s/id_name/$root_name/g)
echo "$input_file" > $root_name.job
```
0.  Check out this page if you'd like to know more about the cluster: 
https://mps-cluster.ucdavis.edu/




#### Edited: 09-17-19
<!--

Syntax for myself

1. hyperlink
[this is the description](https://support.squarespace.com/hc/en-us/articles/206543587-Markdown-cheat-sheet)

2. code
This paragraph has some `variable` inline code.
```html
<p>A paragraph example<p/>
```
```javascript
let num= Math.random();
```
3. picture

![alt text](http://picsum.photos/200/200)

4. Some paragraph with text
> blockquote text below the paragraph

5. Tables

| heading | header | head |
| --- | --- | --- |  
| content | more content | text |
| more | more  | more |

6. Text

This is being *created* on a **Friday** ~~Satruday~~

7. List
- first
    - second
        - third

-->

