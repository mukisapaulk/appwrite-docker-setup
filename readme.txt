This is one of the hardest and most expeience that i have had to do in my life
The issue with docker is that when the internet is slow it crahes the downlaod
so i have a tweak that am using.
    first the ports in the docker-compose.yml should be
        - 20080:80
        - 20443:443

The images that we want are 
 - Image appwrite/appwrite:1.9.5 
 - Image traefik:3.6
 - Image mariadb:10.11
 - Image appwrite/browser:0.3.2
 - Image openruntimes/executor:0.25.1
 - Image appwrite/console:8.7.5 
 - Image redis:7.4.7-alpine          

upon a successfull install we should find our server here
http://localhost:20080/console/

so the important thing is to install one by one image this is because of the internet connectio issue
    docker compose pull traefik
    docker compose pull mariadb
    docker compose pull redis
    docker compose pull appwrite
start with those four i havent found out a way that i can work with them one by one
so if you have mbps of 5mbps and above that is consistent then run
    docker compose up -d --remove-orphans
If things crash run it again
after all is done appwrite will start

docker compose stop   to stop
docker compose start  to  start
