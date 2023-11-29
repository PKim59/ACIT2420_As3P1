Write a tutorial that would guide the reader through the following steps.

- Starting from a Fresh Debian 12 server on digitalocean.

They will already have an ssh key, have set themselves up to connect using that key. Basically, they've done everything in week1! you can check what it looks like in the github page of the file.

Look at week 4 notes for how to create a new user and prevent a user from logging in as root.

- Create a new regular user:
	- User can perform administrative tasks
	- User has bash as login shell
	- User can access the server via SSH
- Prevent the root user from connecting to the server via SSH
- Install nginx
- Configure nginx to serve a sample website

# Assignment 3

In this assignment, we will be creating a web server using digitalocean, creating a new user (as using the root user isn't good practice) with the necessary permissions, configuring a Secure Shell (ssh) connection, and configuring the web server to serve a website!

## Making a new user, enabling ssh for that user and blocking root access.

1. To create a new user in your web server, enter the following code:

```
useradd -ms /bin/bash <the username you want>
```

>Note:
>The sections with < username > just mean to enter the username you want, without the <>. so the command
>useradd -ms /bin/bash < username > would become:

>```
>useradd -ms /bin/bash username
>```

2. Add a password for your new user by entering the following command:

```
passwd <user-name>
```

3. The system will then ask you to enter the password. Enter the password and then hit the enter key. It will ask you to enter it again to confirm the password.

>Note:
>The password won't display the characters you're entering, but it IS being entered and caught properly. 
>This is a security feature to ensure someone looking over your shoulder or a screen-viewer can't know the password by watching you enter it here.

4. Now that the user has been created, we need to add the user to the sudo group. Enter the following command:

```
usermod -aG sudo <user-name>
```

5. Now we need to copy the root user's ssh configurations to the new user as well. We can do this by copying the root users entire ssh folder to the new user's home directory. To do this, enter the following command:

```
cp -r /home/root/.ssh /home/<username>
```

6. Now that it is copied over, we need to change the ownership of these files to the new user and the new user's default group. Enter the following command:

```
chown -R <username>:<username> /home/<username>/.ssh
```

7. Now, let's test that the new user's ssh works. Exit the connection to the debian server and re-enter the command you use to initially connect to the server, but using the username instead of root. 

>Note that your windows username and IP address ***WILL*** be different from the example below:

```
# Old command you used to connect as the root user:
ssh -i "C:\Users\paulk\.ssh\do-key" root@24.199.123.248

# New command you will use to connect as the user you created:
ssh -i "C:\Users\paulk\.ssh\do-key" <username>@24.199.123.248
```

>Tip: To exit your connection, enter the following command:
>```
>exit
>```

It may ask you to confirm that you want to connect as it is the first time connecting. Enter Yes and then it should connect to the web server as your new username.

8. Now that you're connected as the new user, let's make our server safe by removing the ability to log in as the root user. This is particularly helpful in preventing hackers from using our server for possibly nefarious means.

Enter the following command:

```
sudo vim /etc/ssh/sshd_config
```

You should now see a text file with a lot of commented text.

9. In the vim viewer, find the line where it says **PermitRootLogin yes** and change it to **PermitRootLogin no**. Save and exit the file. It should look like this:

```
PermitRootLogin no
```

>Note: Make sure that the PermitRootLogin line does **NOT** have a "#" in front of it. Simply delete it to ensure the configuration change is made and looks identical to the command above.

10. Finally, we're going to make sure the new settings get applied. Enter the command below:

```
sudo systemctl restart ssh.service
```

11. To confirm all of the above worked, let's check the root login method you used before. Exit your connection, and then in your terminal, enter the old root access command. In the previous example, it looked ***similar*** to this:

```
ssh -i "C:\Users\paulk\.ssh\do-key" root@24.199.123.248
```

You should get a lovely "Permission Denied" error message. Congratulations! You have successfully created a user, enabled ssh for that user, and prevented remote root access for your web server.

## Setting Up a Web Server

1. Connect to your Debian server using the ssh connection as the new user you created above.

2. Once connected, enter the following command:

```
sudo apt install nginx
```

>Note: Depending on your settings, you may have safeguards for sudo commands. It may ask you to enter your password to confirm the action.

3. 

command to run to create symbolic link:
 sudo ln -s /etc/nginx/sites-available/my-site.conf /etc/nginx/sites-enabled

