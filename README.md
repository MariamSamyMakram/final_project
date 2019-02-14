# Udacity - Linux Server Configuration

## Description
This up to date guide is designed to help other Udacity students with the final project for the FSND. This will give a step by step description into deploying a Flask application using Ubuntu and Apache with Amazon Lightsail.

## IP
- IP Address: 35.177.251.231
- PORT : 2200

## Amazon Lightsail Set Up

 1. Go to the [Amazon Lightsail](https://aws.amazon.com/lightsail/) website
 2. Click get started for free
 3. Create your first instance

![enter image description here](http://mulligandev.com/assets/lsc/lsc_01.JPG)

4. Select OS Only and Ubuntu
![enter image description here](http://mulligandev.com/assets/lsc/lsc_02.JPG)
5. Scroll down and name your instance whatever you'd like and click create.
6. It might take a minute or two for the instance to set up. Once running click on it and click "Account Page" at the bottom so you can download your private SSH key.
![enter image description here](http://mulligandev.com/assets/lsc/lsc_03.JPG)
7. Now click download to get your private key. The file type is .pem and will be used to SSH into the server.
![enter image description here](http://mulligandev.com/assets/lsc/lsc_04.JPG)
8. The last thing we will need to do is configure the ports Amazon Lightsail will allow. By default the firewall is set to only allow connects from port 22 and port 80. We need to set up port 2200 and 123. 
9. Click the networking tab
![enter image description here](http://mulligandev.com/assets/lsc/lsc_05.JPG) 
10. From this tab click add another under "Firewall" and choose **Custom** for application, **TCP** for protocol, and the port number under **Port Range**. Then click save.
![enter image description here](http://mulligandev.com/assets/lsc/lsc_06.JPG)
11. That should be all that needs to be done with the Lightsail website.

# Linux Configuration
 1. On Mac you will want to store all of your SSH Keys in the .ssh folder which is located in a folder called `.ssh` at the root of your user directory. For example `Macintosh HD/Users/[Your username]/.ssh/`
 2. If you cannot see that folder in the finder open the terminal and type: `$ killall Finder` then type `$ defaults write com.apple.finder AppleShowAllFiles TRUE` 
 3. Now move your downloaded `.pem` key into that folder.
 4. To make our key secure type `$ chmod 600 ~/.ssh/udacity.pem` into the terminal.
 5. From here we will log into the server as the user `ubuntu` with our key. From the terminal type `$ ssh -i ~/.ssh/udacity.pem ubuntu@18.188.169.39`
 6. Once logged in you will see the command line change to `root@[ip-your-private-ip]:$ `
 7. Lets switch to the root user by typing `sudo su -`
 8. As Udacity requires we need to create a user called `grader`. From the command line type `$ sudo adduser grader`. It will ask for 2 passwords and then a few other fields which you can leave blank.
 9. We must create a file to give the user grader superuser privileges. To do this type `$ sudo nano /etc/sudoers.d/grader`. This will create a new file that will be the superuser configuration for grader. When nano opens type `grader ALL=(ALL:ALL)`, to save the file hit Ctrl-X on your keyboard, type 'Y' to save, and return to save the filename.
 10. One of the first things you should always do when configuring a Linux server is updating it's package list, upgrading the current packages, and install new updates with these three commands:
 ```
 $ sudo apt-get update
 $ sudo apt-get upgrade
 $ sudo apt-get dist-upgrade
 
 ```
 11. We will also install a useful tool called **Finger** with the command  `$ sudo apt-get install finger`. This tool will allow us to see the users on this server.
 12. Now we must create an SSH Key for our new user **grader**. From a new terminal run the command: `$ ssh-keygen -f ~/.ssh/udacity.rsa`
 13. In the same terminal we need to read and copy the public key using the command: `$ cat ~/.ssh/udacity.rsa.pub`. Copy the key from the terminal.
 14. Back in the server terminal locate the folder for the user **grader**, it should be `/home/grader`. Run the command `$ cd /home/grader` to move to the folder.
 15. Create a directory called .ssh with the command `$ mkdir .ssh`
 16. Create a file to store the public key with the command `$ touch .ssh/authorized_keys`
 17. Edit that file using `$ nano .ssh/authorized_keys`
 18. Now paste in the public key
 19. We must change the permissions of the file and its folder by running
 ```
 $ sudo chmod 700 /home/grader/.ssh
 $ sudo chmod 644 /home/grader/.ssh/authorized_keys 
 ```
 20. Change the owner of the `.ssh` directory from **root** to **grader** by using the command `$ sudo chown -R grader:grader /home/grader/.ssh`
 21. The last thing we need to do for the SSH configuration is restart its service with `$ sudo service ssh restart`
 22. **Disconnect from the server**
 23. Now we need to login with the grader account using ssh. From your local terminal type `$ ssh -i ~/.ssh/udacity.rsa grader@35.177.251.231`
 24. You should now be logged into your server via SSH
 25. Lets enforce key authentication from the ssh configuration file by editing `$ sudo nano /etc/ssh/sshd_config`. Find the line that says **PasswordAuthentication** and change it to no. Also find the line that says **Port 22** and change it to **Port 2200**. Lastly change **PermitRootLogin** to no.
 26. Restart ssh again: `$ sudo service ssh restart`
 27. Disconnect from the server and try step "**23.**" again BUT also adding **-p 2200** at the end this time. You should be connected.
 28. From here we need to configure the firewall using these commands:
 ```
 $ sudo ufw allow 2200/tcp
 $ sudo ufw allow 80/tcp
 $ sudo ufw allow 123/udp
 $ sudo ufw enable
 ```
 29. Running `$ sudo ufw status` should show all of the allowed ports with the firewall configuration.
 30. That pretty much wraps up the Linux configuration, now onto the app deployment.

### References:

 - https://github.com/callforsky/udacity-linux-configuration
 - https://github.com/iliketomatoes/linux_server_configuration

For any issues or suggestions for this guide please **open an issue**
