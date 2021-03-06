# Docker images for the Graylog

Graylog 2.0.3

###  Date
2016-08-22

# How to use


## Build the Docker images

    docker build -t "graylog" .

## Docker run command

    docker run --name some-mongo -d mongo
    docker run --name some-elasticsearch -d elasticsearch elasticsearch -Des.cluster.name="graylog"
    docker run --link some-mongo:mongo --link some-elasticsearch:elasticsearch -d nguoianphu/docker-graylog
    
Login to Graylog (http://127.0.0.1:9000/) with the username ```admin``` and password ```admin```

To access Graylog web on other machines, you can change the value

     rest_listen_uri = http://0.0.0.0:12900/
     
in the graylog server.conf to your real  Graylog web IP address (i.e 192.168.1.123). You can override it by Docker run command:

     docker run -e "GRAYLOG_REST_TRANSPORT_URI=http://192.168.1.123:12900" --link some-mongo:mongo --link some-elasticsearch:elasticsearch -d nguoianphu/docker-graylog
     
---      
    
## Docker-compose

    version: '2'
    services:
     my-mongo:
      image: "mongo:3"

     my-elasticsearch:
      image: "elasticsearch:2"
      command: "elasticsearch -Des.cluster.name='graylog'"
      ports:
       - "9200:9200"

     graylog:
      image: graylog
      environment:
       GRAYLOG_PASSWORD_SECRET: somepasswordpepper
       GRAYLOG_ROOT_PASSWORD_SHA2: 8c6976e5b5410415bde908bd4dee15dfb167a9c873fc4bb8a81f6f2ab448a918
       GRAYLOG_REST_TRANSPORT_URI: http://127.0.0.1:12900
      links:
       - my-mongo:mongo
       - my-elasticsearch:elasticsearch
      ports:
       - "9000:9000"
       - "12900:12900"
       - "12201/udp:12201/udp"
       - "1514/udp:1514/udp"

### Generate your own password with this command (i.e admin1):

    echo -n admin1 | sha256sum
    
Change ```GRAYLOG_ROOT_PASSWORD_SHA2``` with your new password hash string.

### Access Graylog web on other machines

You must change

     GRAYLOG_REST_TRANSPORT_URI: http://127.0.0.1:12900
     
to your real  Graylog web IP address (i.e 192.168.1.123)

     GRAYLOG_REST_TRANSPORT_URI: http://192.168.1.123:12900
