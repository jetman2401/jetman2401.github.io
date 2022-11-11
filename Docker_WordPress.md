INSTRUCTIONS FOR INSTALLATION FOUND AT: 
https://www.hostinger.com/tutorials/run-docker-wordpress

## How to install Docker on Ubuntu

### 1. Update the package list with the following command:
'sudo apt-get update'
### 2. Install the required packages with the following command:
'sudo apt-get install ca-certificates curl gnupg lsb-release'
### 3. Create a directory for the Docker GPG key with the following command:
'sudo mkdir -p /etc/apt/keyrings'
Note: you will have to put in the root password
### 4. Add Docker’s GPG key with the following command:
'curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg'
### 5. Set up the repository with the following command:
'echo \ "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \ $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null'
### 6. Update Docker’s repository by running the follwing command again:
'sudo apt-get update'
### 7. Install the latest version of Docker Engine, containerd, and Docker Compose with the following command:
'sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin'

## How to get WordPress on Ubuntu Docker

### 1. To make sure that Docker Compose is working correctly, enter the following command:
'docker compose -version'
This should tell you the version of Docker you are running
### 2. Next, we will create a new directory for the WordPress application and change to that directory using:
'mkdir wordpress'
and
'cd wordpress'
### 3. Once that's done, do the following:
Run the command 'touch docker-compose.yml' while cd'ed into the wordpress directory
Next, run the command 'nano docker-compose.yml (assuming you are using nano as your chosen text editor application)'
### 4. You have now created a .yml file for your WordPress and have that file open in a text editor.
Copy the entire selection below and paste it exactly as it is into the .yml file:
~~~
version: "3" 
# Defines which compose version to use 
services: 
	# Services line define which Docker images to run. In this case, it will be MySQL server and WordPress image. 
	db: 
		image: mysql:5.7 
		# image: mysql:5.7 indicates the MySQL database container image from Docker Hub used in this installation. 
		restart: always 
		environment: 
			MYSQL_ROOT_PASSWORD: MyR00tMySQLPa$$5w0rD 
			MYSQL_DATABASE: MyWordPressDatabaseName 
			MYSQL_USER: MyWordPressUser 
			MYSQL_PASSWORD: Pa$$5w0rD 
			# Previous four lines define the main variables needed for the MySQL container to work: database, database username, database user password, and the MySQL root password. 
			wordpress: 
				depends_on: 
					- db 
				image: wordpress:latest 
				restart: always 
				# Restart line controls the restart mode, meaning if the container stops running for any reason, it will restart the process immediately. 
				ports: 
					- "8000:80" 
					# The previous line defines the port that the WordPress container will use. After successful installation, the full path will look like this: http://localhost:8000 
				environment: 
					WORDPRESS_DB_HOST: db:3306 
					WORDPRESS_DB_USER: MyWordPressUser 
					WORDPRESS_DB_PASSWORD: Pa$$5w0rD 
					WORDPRESS_DB_NAME: MyWordPressDatabaseName 
# Similar to MySQL image variables, the last four lines define the main variables needed for the WordPress container to work properly with the MySQL container. 
				volumes: 
					["./:/var/www/html"] 
volumes: 
	mysql: {}
~~~
### 5. Run the command below to start up the Docker container. It will not only start up the container, but the -d modifier will keep it running
'docker compose up -d'
### 6. Normally, you would be hosting WordPress on the same machine that you use it on and go to this link: 'http://localhost:8000/'. However, given that I am doing this in an Ubuntu VM, you should instead go to the following link which includes the IP address of my VM and the port number of the Docker container:
'10.10.1.104:8000'
Note: only works with the given University of Tulsa VPN

## How to set up your WordPress page:
### You will be shown a webpage asking what you want the title of the site to be callled. On this same page, you will also be asked to create a Username, a Password, and to enter your email address (each in their own labeled box). Once you have done this, and click the Install button at the bottom of the page, your WordPress site will be ready for use.