# student-list 
This repo is a simple application to list student with a webserver (PHP) and API (Flask)

![project](https://user-images.githubusercontent.com/18481009/84582395-ba230b00-adeb-11ea-9453-22ed1be7e268.jpg)


------------


## Context


*POZOS*  is an IT company located in France and develops software for High School.

The innovation department want to disrupt the existing infrastructure to ensure that

it can be scalable, easily deployed with a maximum of automation.

POZOS wants to build a **POC** to show how docker can help you and how much this technology is efficient.

For this POC, POZOS will give you an application and want you to build a "decouple" infrastructure based on **Docker**.

Currently, the application is running on a single server with any scalability and any high availability.

When POZOS needs to deploy a new release, every time some goes wrong.

In conclusion, POZOS needs agility on its software farm.

## Infrastructure

POZOS recommends to use centos7.6 OS because it's the most used in the company.

As recommended by the customer,a Centos 7 machine was provisioned using the Vagrant tool.


## Application


The application is named "*student_list*", this application is very basic and enables POZOS to show the list of the student with their age.

student_list has two modules:

- the first module is a REST API (with basic authentication needed) who send the desire list of the student based on JSON file
- The second module is a web app written in HTML + PHP who enable end-user to get a list of students

Now it is time to explain you each file's role:

- docker-compose.yml: to launch the application (API and web app)
- Dockerfile: the file that will be used to build the API image (details will be given)
- requirements.txt: contains all the packages to be installed to run the application
- student_age.json: contain student name with age on JSON format
- student_age.py: contains the source code of the API in python
- index.php: PHP  page where end-user will be connected to interact with the service to - list students with age. 

## Build and test 

1- create images docker:

 docker build -t student-list-img:v1 .
 
 docker images
 
![docker_images](https://github.com/ravelonanosy/mini-projet-docker-03/assets/138290448/28bf0dcd-78d5-4e9f-8582-d26a5937dbe6)


2-create network "bridge type"

docker network create student-list-network

docker network ls


![docker_network](https://github.com/ravelonanosy/mini-projet-docker-03/assets/138290448/d090e255-9fc0-4c6d-865c-a716fae4767e)



3-create container running the API flask python using persistent volume, network brige type on the port 5000

docker run --rm -d --name=student_list_api -p 5000:5000 -v ./simple_api:/data --network=student-list-network student-list-img:v1

docker ps

![run_API_flask](https://github.com/ravelonanosy/mini-projet-docker-03/assets/138290448/d2dc6af1-58e9-4255-bab4-c09cdc779893)


3bis- run curl command to make sure that the API correctly responding

curl -u toto:python -X GET http://192.168.56.141:5000/pozos/api/v1.0/get_student_ages

![docker_curl](https://github.com/ravelonanosy/mini-projet-docker-03/assets/138290448/84338fe0-ce51-447a-8e9c-1612c7ea84ee)



4-create web container on the same network and using  persistent volume

Before running this container, we must modify the index.php file to enter the URL of the flask python API as well as the login and password for the connection

![image](https://github.com/ravelonanosy/mini-projet-docker-03/assets/138290448/a8e223aa-7100-4189-9634-0d6822497afc)


Now we can create and execute the container

docker run --rm -d --name=student_list_web -p 8000:80 -v ./website:/var/www/html --network=student-list-network -e USERNAME=toto -e PASSWORD=python php:apache

docker ps 

![docker_web](https://github.com/ravelonanosy/mini-projet-docker-03/assets/138290448/6ee52622-c959-4478-970e-b3f5caec2cbd)



5-test web URL:

From the internet browser, enter the URL composed of the IP address(192.168.56.141) of the host and the port(8000)

![image](https://github.com/ravelonanosy/mini-projet-docker-03/assets/138290448/e4884038-d2f1-4f90-b9b0-5fc733fead8c)





## Infrastructure As Code 


The ***docker-compose.yml*** file will deploy two services :

- website: the end-user interface with the following characteristics
   - image: php:apache
   - environment: you will provide the USERNAME and PASSWORD to enable the web app to access the API through authentication
   - volumes: to avoid php:apache image run with the default website, we will bind the website given by POZOS to use. You must have something like
`./website:/var/www/html`
   - depend on: you need to make sure that the API will start first before the website
   - port: do not forget to expose the port
- API: the image builded before should be used with the following specification
   - image: the name of the image builded previously
   - volumes: You will mount student_age.json file in /data/student_age.json
   - port: don't forget to expose the port
   - networks: don't forget to add specific network for your project

After deleting the containers created previously.

Run docker-compose.yml

![image](https://github.com/ravelonanosy/mini-projet-docker-03/assets/138290448/3a4c7c10-1b47-48b7-8a17-8e1bef6c6107)


Finally, reach website and click on the bouton "List Student"

![image](https://github.com/ravelonanosy/mini-projet-docker-03/assets/138290448/cc010aa6-9310-4a23-9883-a936ab1d830c)



**If the list of the student appears, you are successfully dockerizing the POZOS application! Congratulation (make a screenshot)**

## Docker Registry 

Previously I already created a local registry on another machine(IP: 192.168.56.142:8090), so I will use it to push the student-list-img image used for this project.

This application was created with docker compose using the file docker-compose-registry.yml

docker-compose-registry.yml will be added to the project directory.

We now tag and send the image to the remote registry.

![docker_remore_registry](https://github.com/ravelonanosy/mini-projet-docker-03/assets/138290448/53c4d561-e794-4c2d-b691-2d5b9199b396)

It can be seen on the remote registry portal.


![image](https://github.com/ravelonanosy/mini-projet-docker-03/assets/138290448/e8d61f8f-3bde-44cf-8346-237607d19f26)




## Delivery 

The ravelonanosy.zip file was sent to the address: eazytrainingfr@gmail.com 
It contains :
- all files used for the project

- the capture of the README.md file, explaining the entire deployment process.
