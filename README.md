# Transcriptomcs 2017
Instructions and notes for creating the VM for the [Harvard](http://evomics.org/workshops/2017-workshop-on-transcriptomics-harvard-university/) Workshop on Transcriptomics. It is based on the instruction/methods to prepare the AMIs for the [evomics.org](https://github.com/guyleonard/evomics_2017/) workshops.

Not much here yet other than Genomics 2017 - Coming Soon!

----

We will be using the latest Ubuntu Linux as our initial AMI, in this case: [ami-cf68e0d8](https://console.aws.amazon.com/ec2/home?region=us-east-1#LaunchInstanceWizard:ami=ami-cf68e0d8) which is the 'us-east-1'	copy of Ubuntu Xenial Xerus 16.04 LTS. But you should be able to use any Debian based version of Linux, ansible will stop on errors but it can also be resumed from that point. I am assuming that you have an AWS account and have already initialised the above AMI for this README.

## Automation

For this we are going to use a series of shell scripts, [Ansible]() and a few other package managers such as apt-get, pip, conda, and gem. You may also wish to read [ami_changes.md](https://github.com/guyleonard/evomics_2017/blob/master/ami_changes.md). Ansible is a simple piece of automation software you can use to install programs and manage systems. You can see what is happening in each stage by looking in the 'main\_\*.yaml' file (or playbook) for each section (e.g. base, genomics, phylogenomics), it is a plain text file with a list of instructions to install programs, change files, download data etc. Some of these instructions are broken into smaller components called taks (e.g. the Assembly Tutorial from the Workshop on Genomics is a series of programs and files to be installed) and they can be found in the sub-folder "tasks" in each workshop.

# Using This Repository
To build the AMI for either workshop, you will need to run at least the [setup.sh](https://github.com/guyleonard/evomics_2017/blob/master/base.sh) script on your remote Virtual Machine (this can be on an Amazon VM (AMI) or from another provider), and then both sets of "main\_\*.yaml" playbooks from either or both workshops.

## Base AMI Setup

Log in to your virtual machine and run this code:

    wget -O- https://raw.githubusercontent.com/guyleonard/evomics_2017/master/setup.sh | bash

Typically, I would now make an [Amazon Machine Image](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html) of this system and then use that as your "base AMI" for the next steps (this will save time if you run in to any issues).

If you already have a base AMI that you wish to use you can do this instead:

    git clone https://github.com/guyleonard/evomics_2017.git
    ansible-playbook /home/ubuntu/evomics_2017/base/main.yaml -b -K -c local -i "localhost,"

## NB

If you are logged in to your AMI using a key pair then you do not need to enter a password when you are asked for SUDO access. Just press 'enter' and continue.

## Transcriptomics AMI Setup:

### Software

Run the genomics software playbook fully:

    ANSIBLE_NOCOWS=1 ansible-playbook /home/ubuntu/evomics_2017/genomics/main_software.yaml -b -K -c local -i "localhost,"

or for just two tools, e.g. samtools & bwa:

    ANSIBLE_NOCOWS=1 ansible-playbook /home/ubuntu/evomics_2017/genomics/main_software.yaml -b -K -c local -i "localhost," --tags samtools,bwa
    
or for just one tutorial you could do:

    ANSIBLE_NOCOWS=1 ansible-playbook /home/ubuntu/evomics_2017/genomics/main_software.yaml -b -K -c local -i "localhost," --tags assembly

Tags can be mixed and matched as you like, they *should* install their dependencies where I have remembered ;).

### Data

There is only one playbook for the data section, but you can run it with tags as before.

    ANSIBLE_NOCOWS=1 ansible-playbook /home/ubuntu/evomics_2017/genomics/main_data.yaml -b -K -c local -i "localhost,"

# FAQs

* Hey Guy, why does it say "ANSIBLE_NOCOWS=1" above?
```
 _______________________________________
/ Do you really want to see cows in all \
| the output messages? Like this? Why?  |
\ Moooooooo.                            /
 ---------------------------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
