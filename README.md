OpenStack-Docker-Team-4
=======================

Configure OpenStack with docker and deploy a 3 tier application

Steps:
  1.  Install OpenStack
      Link - git clone https://github.com/openstack-dev/devstack.git 

      Note - Whenever you restart the PC you need to run ./rejoin-stack.sh of devstack to run the openstack. But this may not start the keystone service. To start it manually run following command
      /opt/stack/keystone/bin/keystone-all --config-file /etc/keystone/keystone.conf -d
  
  2.  Install Docker
      Link - https://docs.docker.com/installation/ubuntulinux/ 
      configure HTTP_PROXY in /etc/default/docker file
  
  3.  Install Docker-Heat plugin using link https://github.com/openstack/heat/tree/stable/icehouse/contrib/docker 
      a. Copy contents of above directory to /usr/heat diretory
      b. update PATH_DIRS variable in heat.conf file to include the path /usr/heat/docker
      c. run ./rejoin-stack.sh      

      Note - after installing Docker-Heat plugin, python 'six' library may break. To fix the issue, update 'six' library using command - sudo pip install -U six
      
      To test if the plugin is integrated successfully, run the following command -
      heat resource-type-list | grep -i Docker
      Output should contain - DockerInc::Docker::Container

  4.  Pull following images from docker registry 
      a. https://registry.hub.docker.com/u/rishineo/wordpress_file2
      b. https://registry.hub.docker.com/u/rishineo/mysql_dbpass
      
  4. Start Docker deamon to listen at some specific port number. We need specify this port number in Heat template as 'docker_endpoint' to which HEAT can contact Docker API.
     Steps:
      1. Stop docker service - sudo service docker stop
      2. sudo docker -H :<port> -d
      
    Note - Before starting the deamon, you need to stop the docker service, if already running.
  
  5. Create a Heat Orchestration Template file 
     1. Heat template specifies resources
     2. Each resource is s container
     3. Specify the image of the container
     4. Specify the environment variables that it may need
     e.g. two-tier-app.yaml in the repository. 
  
  6. Go to Project->Orchestration->Stacks
      a. Click on Create Stack
      b. Upload the above mentioned yaml file
      c. Launch

  7. Once the stack has been created successfully, go to the overview page and note down the IP of 'blog' resource.
      This is the private IP where our presentation layer is hosted and this is been connected to a database layer in another container.
      
  8. List the docker processes running. For that run 'sudo docker ps'
     Note - If docker is running as deamon - you need to specify port number e.g. docker -H :5555 <command>
  
  9. Go into the mysqldb container.
      a. Type mysql
      b. Execute following commands
        1. create database wordpress;
  
  10. Access the IP of the 'blog' container in the browser. You will see the UI for wordpress.
  
  11. Install wordpress through UI in browser.
  
  12. Once installation is done, we can see the required tables have been genearted in the 'wordpress' database. 
  
