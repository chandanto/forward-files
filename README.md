# forward-files
General forwarded solutions

As per the requirement, i have tried .. 

1) Provision two servers -- > Ansible or python (Enable port 8080/8443) 
2) Install docker after servers provisioned --- > yum install docker -- > include ansible
3) Below steps i followed to run the docker containers & self signed SSL.

Note:-  Below i tried on docker alone. Please replace all with your docker-compose. As per there requirement, all script needs to be dynamic. if you write in docker-compose. Required meets
         
       a) docker pull tomcat 
       b) attached server.xml & self signed certificate created. 
              Below two added on server.xml & created self-signed cert
                 i)     <Connector port="8443" protocol="org.apache.coyote.http11.Http11NioProtocol"
               maxThreads="150" SSLEnabled="true"
               compression="on" scheme="https" secure="true"
               keystoreFile="conf/ssl-keystore.jks"
               keystorePass="password123"
               SSLVerifyClient="none" SSLProtocol="TLSv1.2"
    />
                ii) keytool -genkey -keyalg RSA -alias selfsigned -keystore ssl-keystore.jks -storepass password123 -validity 365 -keysize 2048
        
       c) Follow below command to include this two in running container 
                docker container run -dit --name appss -p 8081:8443 docker.io/tomcat
                docker container cp server.xml appss:/usr/local/tomcat/conf/.
                docker container cp companyNews.war appss:/usr/local/tomcat/webapps/
                docker container cp ssl-keystore.jks appss:/usr/local/tomcat/conf/.
                docker container  stop  appss
                docker container start  appss
                docker container logs appss

Above all the steps you can write in docker compose -- > so that you can run docker swarm like below -- > it is easy

1) Copy all the static content >> htdocs & run the below 
docker service create --name webserver -p 8080:80 --mode global --constraint 'node.role == worker' httpd

2) Run the tomcat container >> companyNews >> Here make a note you have to write a docker-compose & include the above. So that it can run exactly same. Here as per there required & problem says fault tolerance, scaling applications
docker service create --name webapp-p 8081:8443 --mode global --constraint 'node.role == worker' tomcat

Hope this help you to setup. 

I can understand, you have to do few stuff alone. Like writting ansible script to provisioning ec2 instances (Public > enable_ports : 8081, 8080, 80, 8443), install docker. Write docker-compose to create a images using httpd or tomcat. & upload the files as attached to setup "companyNews". 
