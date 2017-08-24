# Linux-Server-Configuration
I will take a baseline installation of a Linux distribution on a virtual machine and prepare it to host web applications, to include installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.

Step-by-step:

1) Create User grader
sudo adduser grader

2) Add grader to sudo Group
# Open directory of sudoers group
sudo ls /etc/sudoers.d/
# Copy file from an existing user and change the name of the file for the User Grader
sudo cp /etc/sudoers.d/90-cloud-init-users /etc/sudoers.d/grader
# Edit file garder to Add the name of the user
sudo nano /etc/sudoers.d/grader


