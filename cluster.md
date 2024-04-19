---
title: Computing Cluster
layout: default
nav_order: 4
---

# Computing Cluster

(Last updated: Apr 19, 2024)

This document is created by Yen-Chia Hsu when supervising students who need to have access to computing resources for their projects.
This document explains how to work with computer clusters in Dutch academia.
This is a live document that will periodically be updated.

### Table of Content

- [Available Computer Clusters](#clusters)
- [SURF Snellius Cluster](#snellius)
  - [How to apply for a Snellius account?](#snellius-account)
  - [How to estimate computing hours in SBUs?](#snellius-sbu)
  - [How to run scripts on the Snellius cluster?](#snellius-run-script)
  - [How to manage your code and data?](#snellius-code-data)

## <a name="clusters"></a>Available Computer Clusters

Computing resources are important for projects that involve heavy computing (e.g., running computational simulations and conducting computer vision experiments). In this case, you can apply for an account to use the [SURF Snellius cluster](https://www.surf.nl/en/services/snellius-the-national-supercomputer) with shared CPUs and GPUs. Or you can request virtual machines (with GPUs) from the [SURF HPC Cloud](https://www.surf.nl/en/services/hpc-cloud). Besides these Dutch national computing infrastructure by SURF, there are also others, such as the [DAS5](https://www.cs.vu.nl/das5/), [DAS6](https://www.cs.vu.nl/das/), and internal IVI cluster. These ones are only accessible to staff members (and not students).

## <a name="snellius"></a>SURF Snellius Cluster

### <a name="snellius-account"></a> How to apply for a Snellius account?

To apply for the Snellius cluster access, you can go straight to this [SURF service desk link](https://servicedesk.surf.nl/) (which requires logging in using your UvA credential). After logging in, click on the "Direct institute contract" button under the "Apply for access" section.

You will be directed to a page with a form. Then, following the steps below:
- First, choose UvA as your organization.
- Then, include the information about your project (e.g., a part of your thesis proposal/design document), why you need the computing hours (e.g., I am a student in the Master Information Studies program at the Informatics Institute, working on my thesis project under the supervision of Yen-Chia Hsu), and what you are going to do for the computing hours (e.g., conducting experiments of computer vision models to recognize industrial smoke emissions).
- Next, tick `Snellius CPU` and `Snellius project space` under `Type of request` to request CPU computing hours and storage spaces. If your project needs GPU resources (e.g., for computer vision experiments), you also need to tick `Snellius GPU`. Provide also an [estimation of computing hours in SBUs](https://servicedesk.surf.nl/wiki/display/WIKI/Estimating+SBUs).
- Finally, accept the terms and conditions and provide your contact details. You do not need to include the project leader's information.

Then, you should receive information about how to access the cluster using a terminal. If you are using a MAC, it comes with a terminal (search for "terminal" by clicking on the magnifying glass on the top-right of your MAC). Normally, you should log into the cluster [using the SSH command](https://www.geeksforgeeks.org/ssh-command-in-linux-with-examples/) on your terminal. You can also set up an SSH remote development environment (e.g., check [this website for Visual Studio Code](https://code.visualstudio.com/docs/remote/ssh)). In this way, you can use Visual Studio Code on your local computer to write code on the remote server (which is on Snellius). Besides, you can also [use Jupyter Notebook on the remote server](https://medium.com/@apbetahouse45/how-to-run-jupyter-notebooks-on-remote-server-part-1-ssh-a2be0232c533).

### <a name="snellius-sbu"></a>How to estimate computing hours in SBUs?

To estimate the computing hours, you can use 4-10 CPU cores per GPU. If your computer vision pipeline requires a lot of data augmentation and preprocessing on the CPU, it is better to get more cores, such as 8. Sometimes, data augmentation can be done on GPU, but you need to check if the Python package that you want to use supports data preprocessing and augmentation on GPU. A lot of the time, the data augmentation is done on the CPU, which means if you do not have enough CPU cores, the GPU will idle periodically (because there is not enough preprocessed data to feed the GPU). So, if you are thinking about an estimation, maybe something like 4 GPUs with 24 or 32 CPU cores would be a good estimate.

If you want to have a more precise estimation of SBUs, check the documentation (e.g., GitHub README file or their published papers) of the models that you plan to modify, implement, or extend. Usually, the documentation describes the resources that the researchers used to implement the model, including computing resources and time.

Besides estimating the precise SBUs, please also consider the unexpected situation by multiplying your precise estimation with a safety factor. This factor allows some extra room for unexpected errors. For example, you may accidentally set the wrong parameters and end up wasting computing time on the cluster, and thus, you may need extra time to rerun the experiments.

Remember that you also need to consider the node that you want to request on the cluster. Some clusters do not allow you to request an arbitrary number of CPUs or GPUs, so you may end up needing to request more SBUs. If you are using Snellius, [check here](https://servicedesk.surf.nl/wiki/display/WIKI/Snellius+partitions+and+accounting) for the allowed partitions of each type of cluster node.

Below is an example of estimating SBUs. You can also find [another example on this page](https://servicedesk.surf.nl/wiki/display/WIKI/Small+Compute+applications).
- You plan to use three AI models in your experiment, and one complete experiment that uses the three models needs to use 4 GPUs and 32 CPUs for 24 hours.
- You check the [Snellius partition accounting table](https://servicedesk.surf.nl/wiki/display/WIKI/Snellius+partitions+and+accounting) and plan to use half of the `gcn (MIG)` node, which has 36 CPUs and 4 GPUs. The accounting table says it is 512 SBUs per hour, but you only need half of the node, so it is 256 SBUs for you.
- You need to run the experiment for your model 6 times because there are 6 splits of training/validation/testing. This means the precise estimation is 256 SBUs times 24 hours and times 6 experiments, which should be 256*24*6=36864 SBUs
- You want a safety factor of 1.1 to allow some room for errors, so it will be 36864*1.1=40550 SBUs in total.

### <a name="snellius-run-script"></a>How to run scripts on the Snellius cluster?

The Snellius cluster uses the [Slurm manager](https://slurm.schedmd.com/overview.html). When you log into the cluster, you are typically on the head node. You will need to request a compute node to run scripts. DO NOT run scripts on the head node, as you will slow down the head node and cause trouble to other users when they log in.

The easiest way to run scripts on a cluster is to use interactive sessions, which is like a normal terminal where you can type commands. The following two links contain more information about running Slurm interactive sessions:
- [Interactive jobs (by SURF)](https://servicedesk.surf.nl/wiki/display/WIKI/Interactive+jobs)
- [Slurm Interactive Sessions (by the New Zealand eScience Infrastructure)](https://docs.nesi.org.nz/Scientific_Computing/Running_Jobs_on_Maui_and_Mahuika/Slurm_Interactive_Sessions/)

On the Snellius cluster, the following terminal command (according to [this tutorial](https://uvadlc-notebooks.readthedocs.io/en/latest/tutorial_notebooks/tutorial1/Lisa_Cluster.html)) is an example to request one task with 2 GPUs, 60G memory, and 20 CPUs for 1 hour and 30 minutes of usage time. This is for running an interactive session.
```sh
srun --partition=gpu --gpus=2 --ntasks=1 --mem=60G --cpus-per-task=20 --time=01:30:00 --pty bash -i
```

Notice that leaving the interactive session (e.g., using `Ctrl+C`) will cancel all the scripts that you are currently running. This is problematic and can also happen if you lose your internet connection (or accidentally disconnect from your VPN). If you want to detach from the interactive session, you should use the [screen](https://www.gnu.org/software/screen/manual/screen.html) or [tmux](https://github.com/tmux/tmux/wiki) tool. For example, the following commands will open a screen, which will bring you to a clean terminal.
```sh
screen -mSL job1
```

After that, you can run the `srun` command to request the interactive session. Once you are in the interactive session, you can then detach from the screen using `Ctrl+A+D`.

After that, you can use the following command to check the list of screens that you have.
```sh
screen -ls
```

Then, you will see a list of screen IDs (someting like `56826.job1`) on the returned message. You can enter one specific screen by using the command below. Replace `SCREEN_ID` with your screen ID, such as `56826.job1`.
```sh
screen -x SCREEN_ID
```

If you no longer need the screen, you can type the following when you are in the screen to terminate it:
```sh
exit
```

Usually, a good practice is to use the `srun` command with interactive sessions to test if your script can run on the cluster and then use the `sbatch` command to submit the actual job that needs to run for a long time. For how to use `sbatch`, refer to [this tutorial developed by a UvA course](https://uvadlc-notebooks.readthedocs.io/en/latest/tutorial_notebooks/tutorial1/Lisa_Cluster.html). It also contains information about how to set up your development environment (e.g., PyTorch and Anaconda).

### <a name="snellius-code-data"></a> How to manage your code and data?

You can use the [Git](https://git-scm.com/) tool to manage code. There are many online Git services for hosting code, such as [GitHub](https://github.com/), [GitLab](https://about.gitlab.com/), [Bitbucket](https://bitbucket.org/), etc. In this way, you can sync the code on both your local machine (e.g., your personal MAC) and remote server (e.g., your Snellius space). If you are new to Git, check the [Git guide](https://github.com/git-guides). Also, here is a [15-minute Git tutorial video](https://www.youtube.com/watch?v=USjZcfj8yxE).

For data management, you may need to move your data from your local machine to the remote server. In this case, you can use the [rsync tool](https://linux.die.net/man/1/rsync). Here is [a tutorial](https://www.digitalocean.com/community/tutorials/how-to-use-rsync-to-sync-local-and-remote-directories) about how to use it to sync the local and remote data. For example, if you are on the terminal of your local machine, you can use the following command to sync a file with path `/data/project-potions/healing_potion.py` to the `/var/www/project-potions/` folder on your remote server `snellius.surf.nl` with user name `hpotter`:
```sh
rsync -av /data/project-potions/healing_potion.py hpotter@snellius.surf.nl:/var/www/project-potions/
```

You can also use regular expressions with rsync. The example below will sync all files under the `/data/project-potions/` folder on your local machine to the remote server:
```sh
rsync -av /data/project-potions/* hpotter@snellius.surf.nl:/var/www/project-potions/
```

Another example is to sync all files with `_potion.py` as the suffix to the remote server:
```sh
rsync -av /data/project-potions/*_potion.py hpotter@snellius.surf.nl:/var/www/project-potions/
```

You can also specify a list of file names. The example below will sync files `healing_potion.py`, `invisibility_potion.py`, and `speed_potion.py` to the remote server:
```sh
rsync -av /data/project-potions/{healing,invisibility,speed}_potion.py hpotter@snellius.surf.nl:/var/www/project-potions/
```