# Assignment 3

In this assignment, we will be creating a web server using digitalocean, creating a new user (as using the root user isn't good practice) with the necessary permissions, configuring a Secure Shell (ssh) connection, and configuring the web server to serve a website!

## Making a new user, enabling ssh for that user and blocking root access.

1. Assuming you have already connected to your debian server, create a new user in your Debian server by entering the following command:

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

5. Now we need to copy the root user's ssh configurations for the new user. We can do this by copying the root user's entire ssh folder to the new user's home directory. To do this, enter the following command:

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

2. Once connected, enter the following command to install nginx, a package that allows us to create a web server:

```
sudo apt install nginx
```

>Note: Depending on your settings, you may have safeguards set for sudo where it may ask you to enter your password to confirm the action.
>Enter the password to proceed as necessary.

3. Create a folder in the /var/www directory named my-site. Enter the following command:

```
sudo mkdir /var/www/my-site
```

4. Now, we need to create a base index.html file that will serve as a landing page for anyone who goes to your web server. Enter the following command:

```
sudo vim /var/www/my-site/index.html
```

5. In Vim, enter the following content into the file (index.html) and then save and quit.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>2420</title>
    <style>
        body {
            display: flex;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
        }
        h1 {
            text-align: center;
        }
    </style>
</head>
<body>
    <h1>Hello, World</h1>
</body>
</html>
```

6. We're now going to create the server block file. First, enter the following command: 

```
sudo vim /etc/nginx/sites-available/my-site.conf
```

7. In Vim, enter the following content into the file and then save and quit.

```
server {
	listen 80 default_server;
	listen [::]:80 default_server;
	
	root /var/www/my-site;
	
	index index.html index.htm index.nginx-debian.html;
	
	server_name _;
	
	location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		try_files $uri $uri/ =404;
	}
}
```

8. Now, we need to create a symbolic link to the file so that it can be activated. Do this by entering the following command:

```
sudo ln -s /etc/nginx/sites-available/my-site.conf /etc/nginx/sites-enabled
```

9. Now that it is linked, we need to ensure that the default setting does not get in the way by unlinking the default settings. Enter the following command:

```
sudo unlink /etc/nginx/sites-enabled/default
```

10. Now that the link to the default settings is removed, we can test our nginx configuration file to ensure there are no issues. Enter the following command to check:

```
sudo nginx -t
```

Ideally, you will see the following messages after entering the command:

>nginx: the configuration file /etc/nginx/nginx.conf syntax is ok

>nginx: configuration file /etc/nginx/nginx.conf test is successful

11. Now, we need to restart the service to ensure these new settings are applied. To play it safe, let's reboot the server entirely using the following command:

```
sudo reboot
```

12. After a few minutes, open your internet browser and enter the ip address of your web server, i.e. the ip address of the debian server you use for your ssh connection command. You should see the "Hello World" message (from index.html) in the middle of the page!

