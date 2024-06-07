# Installing MKdocs to store notes firstly

## Installation

There are 3 ways of creating an mkdocs.

[TOC]

### 1- Manual creation

This is the slowest method but usefull if only needed once.

#### 1- I have logged into my server from my windows pc using

This step is only needed if you want to create the mkdocs on a selfhosted server.

~~~ bash
ssh guigoch@guigoch.ddns.net
~~~

#### 2- Then update Ubuntu

~~~ bash
sudo apt update

sudo apt upgrade
~~~

#### 3- Make sure to have Python 3 installed and pip3

~~~ bash
pip --version
python3 --version
~~~

#### 4- Then install Mkdocs

~~~ bash
sudo apt install mkdocs
~~~

Check installation using

~~~ bash
mkdocs --version
#Ouput
mkdocs, version 1.5.3 from /home/guigoch/.local/lib/python3.10/site-packages/mkdocs (Python 3.10)
~~~

#### 5- Create new mkdocs project and build site

~~~ bash
sudo mkdocs new mkdocs

cd mkdocs

sudo mkdocs build
~~~

#### 6- Verify files creates succesfully

~~~bash
ls -la /mkdocs
docs  mkdocs.yml  site  ssl

ls -la /mkdocs/site
404.html  css  fonts  img  index.html  js  search  sitemap.xml  sitemap.xml.gz
~~~

#### 7 - Install mkdocs-material theme and setup

~~~ bash
pip3 install mkdocs-material

sudo vim /mkdocs/mkdocs.yml
~~~

An expected 'mkdocs.yml' file could be this:

~~~ yml
    site_name: Doc

    nav: 
    - Home: index.md
    - Server: server-settings.md
    - MkDocs: setting-mkdocs.md

    theme: 
    name: 'material'
~~~

#### 8 - Start mkdocs

Specify ip address and port

~~~ bash
mkdocs serve -a 0.0.0.0:8000
~~~

### 2- Using a Dockerfile

| Note: Make sure to have docker installed

This method is to automate the manual process and will create a docker image.
**It won't allow you to run docker compose!**

Create a Dockerfile

Go to the folder of the mkdocs project

~~~ bash
cd /mkdocs
~~~

Create a Dockerfile

~~~ bash
sudo vim Dockerfile
~~~

And put this inside of the content

~~~ Dockerfile
FROM python:3

RUN pip install mkdocs
RUN mkdocs new mkdocs

EXPOSE 8000

WORKDIR /mkdocs

ENTRYPOINT ["mkdocs"]

CMD ["serve", "--dev-addr=0.0.0.0:8000"]
~~~

Then create the container and build
Execute the commands from inside the folder where the Dockerfile is located

~~~ bash
sudo docker build -t mkdocs .

sudo docker run -itd -p 8000:8000 --rm mkdocs
~~~

The docker build will automatically build all the process done manually
It is only necessary to run once.

Once it is done you can run the second command to run the server

### 3- Docker composer

Setting up docker composer to update the project automatically and deploy

#### 1- Create docker-compose.yml

Create a folder to store your Mkdocs files

~~~ bash
    mkdir mkdocs
    nvim docker-compose.yml
~~~

Put this inside the created file

~~~ bash
    version: '3'
    services:
    mkdocs:
        image: squidfunk/mkdocs-material
        ports:
        - "8005:8000"
        volumes:
        - ./:/docs
        stdin_open: true
        tty: true
~~~

Then make sure to have the /docs directory created

~~~bash
    mkdir ./docs
~~~

Then just run the following command and it's ready to go

~~~ bash
sudo docker compose up -d
~~~

To stop it from running run:

~~~ bash
sudo docker compose stop
~~~

## Building the doc

Check documentation here [[https://www.mkdocs.org]]

Here is a great place to look about documenting the mkdocs [[https://github.com/mkdocs/catalog#-theming]]

I try and add a new theme:
Dracula

~~~ bash
pip install mkdocs-dracula-theme
~~~
