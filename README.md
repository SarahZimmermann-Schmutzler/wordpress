# WordPress Containerization

This repository serves as a guide for containerizing **WordPress** using **Docker Compose**.  
  
This Repository was created as part of my training at the **Developer Academy**.  

## Table of Contents
1. <a href="#technologies">Technologies</a>  
2. <a href="#description">Description</a>  
3. <a href="#getting-started">Getting Started</a>  
4. <a href="#usage">Usage</a>  

## Technologies
* **Docker** 24.0.7
    * **Compose** v2.32.4 (module to install, <a href="https://docs.docker.com/compose/">More Information</a>)

## Description
### WordPress
<a href="https://wordpress.com/de/">WordPress</a> is a content management system (CMS) that allows users to easily create, manage and publish websites and blogs - without extensive programming knowledge. It is one of the most popular CMS worldwide and powers over 40% of all websites on the Internet (as of 2023).

### Containerize WordPress
Docker offers a possible <a href="https://hub.docker.com/_/wordpress">compose.yaml</a> file for the WordPress containerization. It shows that **two containers are needed**: one for the WordPress application and one for the database.  

- The compose file uses the `wordpress base-image` which is provided by the official WordPress community and already contains all the essential components to run the WordPress application:
    - slim operating system
    - Apache (web server)
    - PHP (including necessary extensions for WordPress)
    - WordPress files

- For the database-container a MySQL or **MariaDB** database is recommended. In this repository the latter is used here - the open source focused and independent alternative to MySQL. Provided and maintained by the MariaDB community there is a pre-build base-image. It contains, in addition to the necessary operating system:
    - MariaDB server
    - Basic configurations required for operation

## Getting Started
0) <a href="https://docs.github.com/de/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo">Fork</a> the project to your namespace, if you want to make changes or open a <a href="https://docs.github.com/de/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests">Pull Request</a>.

1) <a href="https://docs.github.com/en/repositories/creating-and-managing-repositories/cloning-a-repository">Clone</a> the project to your platform if you just want to use it:
    - <ins>Example</ins>: Clone the repo e.g. using an SSH-Key:  
    ```bash
    git clone git@github.com:SarahZimmermann-Schmutzler/wordpress.git
    ```

2) Create an **.enf file** in the project directory. The required variables are provided in <a href="https://github.com/SarahZimmermann-Schmutzler/wordpress/blob/main/example.env">example.env</a>.

3) **Build and start the container** in the background (detached mode):
    ```bash
    docker compose up --build -d
    ```

4) Check whether the **server is running** correctly:
- WordPress can be reached under the *IP address of your cloud VM on port 8080*:
    - http://IP_Address_VM:8080

- When you open the page you will be redirected to the **installation page** and can now start creating your account. After the setting, a sample blog page appears if you have not yet written your own post.

## Usage
### Installation and Preparation
1) <a href="https://docs.github.com/en/repositories/creating-and-managing-repositories/cloning-a-repository">Clone</a> the project to your platform if you just want to use it.
    - <ins>Example</ins>: Clone the repo e.g. using an SSH-Key:  
    ```bash
    git clone git@github.com:SarahZimmermann-Schmutzler/wordpress.git
    ```
2) Create an **.enf file** in the project directory. The required variables are provided in <a href="https://github.com/SarahZimmermann-Schmutzler/wordpress/blob/main/example.env">example.env</a>.  
    
    ```bash
    DB_USER=
    DB_NAME=
    DB_PASSWORD=
    MYSQL_ROOT_PASSWORD=
    ```

### Containerization with Docker Compose
1) The `compose.yaml` is responsible for managing and orchestrating the WordPress and the Database container. It defines what configurations they should have:
    ```bash
    services:

        wordpress:
            env_file: .env
            container_name: wordpress
            image: wordpress:6.3
            restart: on-failure
            ports:
                - 8080:80
            environment:
                # name of the database container (db)
                WORDPRESS_DB_HOST: db
                # username for database access
                WORDPRESS_DB_USER: ${DB_USER}
                # password for database access
                WORDPRESS_DB_PASSWORD: ${DB_PASSWORD}
                # name of the database used by WordPress
                WORDPRESS_DB_NAME: ${DB_NAME}
            volumes:
                - wordpress_volume:/var/www/html

        db:
            env_file: .env
            container_name: wordpress_mariadb
            image: mariadb:10.5
            restart: always
            environment:
                # same variable as WORDPRESS_DB_NAME
                MYSQL_DATABASE: ${DB_NAME}
                # same variable as WORDPRESS_DB_USER
                MYSQL_USER: ${DB_USER}
                # same variable as WORDPRESS_DB_PASSWORD
                MYSQL_PASSWORD: ${DB_PASSWORD}
                # password for the root user of the MariaDB database; required for administrative tasks
                MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
            volumes:
                - db_volume:/var/lib/mysql

    volumes:
        wordpress_volume:
        db_volume:
    ```

2) **Build and start the container** in the background (detached mode):
    ```bash
    docker compose up --build -d
    ```
    - To view the log files:
        ```bash
        docker compose logs -f
        ```
    - To stop the container:
        ```bash
        docker compose stop <container-name>
        ```
    - To delete the container:
        ```bash
        docker compose down <container-name>
        ```
    - To list all containers that are operatet by Docker Compose:
        ```bash
        docker compose ps
        ```
    - If no network is defined in the compose file, docker automatically creates one. To check which networks exist:
        ```bash
        docker network ls
        ```
        - To list which containers are on a network:
            ```bash
            docker network inspect <network-name>
            ```
            - To check if the wordpress and mariadb container are in the same network:
                ```bash
                docker network inspect wordpress_default
                ```  
                <img alt="network" src="https://github.com/SarahZimmermann-Schmutzler/wordpress/blob/main/network.png"></img>

3) Check whether the **server is running** correctly:
- WordPress can be reached under the *IP address of your cloud VM on port 8080*:
    - http://IP_Address_VM:8080

- When you open the page you will be redirected to the **installation page** and can now start creating your account:  

    <img alt="install.php" src="https://github.com/SarahZimmermann-Schmutzler/wordpress/blob/main/install.png"></img>

- After the setting, a sample blog page appears:  

    <img alt="example blog entry" src="https://github.com/SarahZimmermann-Schmutzler/wordpress/blob/main/example.png"></img>

- Unless you have already written your first entry and chose a layout:  

    <img alt="first entry" src="https://github.com/SarahZimmermann-Schmutzler/wordpress/blob/main/entry.png"></img>