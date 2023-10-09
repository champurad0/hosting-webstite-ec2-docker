# hosting-webstite-ec2-docker

## Preface
---
I came across this project trying to get more hands on experience with what a DevOps engineer might be doing. The problem I found was trying to understand why use an EC2 instance to begin with. The utility made sense but I needed to understand for myself.

The project is simple.
1. Create an EC2 instance 
2. Connect to the EC2 
3. Download `Docker` 
4. Create a `Dockerfile`
5. Run the `Dockerfile` 
6. Verify everything is working properly
7. End the session

## Step-by-Step Guide

---
### 1. Creating the EC2 instance

Navigate over to the EC2 dashboard
![[Assets\Pasted image 20231008184611.png]]

From the dashboard select `Instances` > `Launch Instance` OR simply hit `Launch Instance`

![[Pasted image 20231008185720.png]]

Give the instance a name and select the Amazon Linux OS image

![[Pasted image 20231008190028.png]]

Verify the instance type, in this case I'm staying in the free tier

![[Pasted image 20231008190051.png]]

Now we will create a key pair to allow us to SSH in from our local machine 

![[Pasted image 20231008190225.png]]

You can name the `Key Pair` whatever. Since im using a Win 11 machine I'll be using `RSA` for the Key Pair type and `.pem` for the format 

![[Pasted image 20231008190526.png]]

Since this is a web site, usee the `Create security group` and All SSH from anywhere (since this is a test this should be fine) and Allow HTTP traffic

![[Pasted image 20231008191748.png]]

Skip past the remaining options and launch instance.

![[Pasted image 20231008191829.png]]


### 2.  Connecting to the instance 

Navigate to the instance running in the EC2 dashboard and select the instance before selecting `Connect`

![[Pasted image 20231008192438.png]]

Select the SSH Client and then follow the example given and replace the `.pem` with the file location of your file (most likely still in downloads)

![[Pasted image 20231008192623.png]]

This will change based on you machine (view my file path underlined by the green line)

![[Pasted image 20231008193052.png]]

### 3. Downloading Docker
**IF ANY COMMANDS GET PERMISSION DENIED RUN WITH `SUDO`

Before anything is installed it is good to install any updates on the instance 

```
yum update -y
```

Now install Docker and verify it was installed correctly 
```
yum install docker -y
```

![[Pasted image 20231008193629.png]]

Either create a website folder or find a template online to copy the download link address

![[Pasted image 20231008193821.png]]

Now back to the shell create a directory and download the assets to that folder
```
mkdir website
```
```
cd website
```
```
wget https://www.free-css.com/assets/files/free-css-templates/download/page296/finexo.zip
```

Once downloaded you can LS to verify and then unzip the folder 

![[Pasted image 20231008194233.png]]

![[Pasted image 20231008194438.png]]

Before moving on copy the files from the finexo-html to the parent 'website' folder. Then delete the zip and original folder

```
cd finexo-html
```

```
cp -R * ../.
```

```
rm -rf finexo-html finexo.zip
```

### 4. Creating the Dockerfile

---
*You must start the docker service using the following commands*

```
sudo systemctl start docker
```

---
Now that we have the assets we can create the `Dockerfile` using the following commands. Before entering the commands navigate back to the user directory.

![[Pasted image 20231008195113.png]]

```
vim Dockerfile 
```

Inside the vim editor add the following text and save / exit

```
FROM httpd
COPY ./website /usr/local/apache2/htdocs/
```

Verify the file is correct with `cat Dockerfile`

Now use the `build` command to create the image 
(the -t flag allows you to name the image and the '.' indicates there is a Dockerfile in the current directory)

```
docker build -t webserver .
```

![[Pasted image 20231008201548.png]]

### 5. Run the Dockerfile

Now use `docker run` to deploy the image

```
docker run -dit --name running-website -p 8080:80 webserver
```

![[Pasted image 20231008201820.png]]

![[Pasted image 20231008201835.png]]

### 6. Verifying the container is running

Now to verify navigate back to the EC2 instance and select the IPv4 DNS 

![[Pasted image 20231008202608.png]]

Since the website is not secure we have to delete the 'S' to access the site

![[Pasted image 20231008202724.png]]

Then we are able to access the site 

![[Pasted image 20231008202751.png]]

### 7. End the Session 

You can stop the project a few different ways.
1. Terminating the instance - deletes everything 
2. Stopping the instance - shuts down the ec2 instance 

---

## Where to go from here
Just some ideas on how this project can go further
1. configuring a VPC 
2. adding security roles 
3. adding a login and database 

There are many way to take this further. 

