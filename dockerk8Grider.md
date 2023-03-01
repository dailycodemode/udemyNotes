<h1>Docker and Kubernetes: The Complete Guide</h1>
Stepehn Grider.
https://www.udemy.com/course/docker-and-kubernetes-the-complete-guide

### Sec 1 - intro

13. Commands
```
docker run busybox echo hi there
podman run busybox echo hi there
podman run busybox ls
```
14. Running images
```
docker ps
podman ps
```
All containers ever ran
```
podman ps --all
```
17. Removing stopped containers
```
docker system prune
podman system prune
```
18. To start with logs
```
docker start -a sadfgsadgasdffadfgsdfahjsdfajsdhkf 
```
18. To get logs
```
docker logs sadfgsadgasdffadfgsdfahjsdfajsdhkf
```
19. Stopping containers
```
docker stop dfgsdffasdfsda
docker kill dfgsdffasdfsda
```
Stop will give it a shutdown time. 

Kill stops immediatly. 
'Stop' is preferable. 

20. Multi command containers

```
docker run redis

```
21. Running commands in running container

```
docker exec -it <continaer> <command>
docker exec -it asdfasdfdsaf redis-cli

--->
set myvalue 5
get myvalue
```

22. Purpose of the -it flag
```
stdin
stdout
stderr
```
-i means that we want to attach our terminal.
-t makes sure that output text appears in a pretty manner.

23. Getting a command prompt. ie Getting shell access
```
docker exec -it asdfasdfasdf sh
```

Command shells ---> bash powershell zsh sh.

To exit
```
Ctrl+d
```

24. Starting with a shell
```
docker run -it busybox sh
```

### Sec 2 - Custom Image with Docker

28. Dockerfile

```
touch Dockerfile
```
```
FROM alpine
# Step 2: Download and install dependency
RUN apk add --update redis
# Step 3: Tell the image what to do when it starts as container
CMD ["redis-server"]
```

30. apk = apache package manager

31. docker build
```
docker build .
```
34. Tagging an image
```
docker build -t stephengrider/redis:latest .
docker run stephengrider/redis:latest
```
36. Manual image generation. 
```
docker run -it alpine sh
docker ps
docker commit -c "CMD 'redis-server'" asdasfsdafasdfsd
>will give a hash e.g. aaaaaabbbbbbb
docker run aaaaaabbbbbbb
```
### Sec 4 - basic node js express project

38. Add package.json to tell node.js app to run
```
{
  "dependencies": {
    "express": "*"
  },
  "scripts": {
    "start": "node index.js"
  }
}
```
Add index.js
```
const express = require('express');

const app = express();

app.get('/', (req, res) => {
  res.send('How are you doing');
});

app.listen(8080, () => {
  console.log('Listening on port 8080');
});

```
39. Dockerfile build of basic node.js
```
# Specify a base image
FROM node:14-alpine

WORKDIR /usr/app

# Install some depenendencies
COPY ./package.json ./
RUN npm install
COPY ./ ./

# Default command
CMD ["npm", "start"]
```
40. Exposing ports
```
docker run -p 8087:8080 <image-name>
-p <our-port>:<port inside the container>

localhost:8087
```
44. Safe copy loation
```
WORKDIR /usr/app
```

### Sec 5 - dockerCompose - express + redis

46. Add package.json to tell node.js app to run
```
{
  "dependencies": {
    "express": "*",
    "redis": "2.8.0"
  },
  "scripts": {
    "start": "node index.js"
  }
}
```

47. Add index.js
```
const express = require('express');
const redis = require('redis');
const process = require('process');

const app = express();
const client = redis.createClient({
  host: 'redis-server',
  port: 6379,
});
client.set('visits', 0);

app.get('/', (req, res) => {
  process.exit(0);
  client.get('visits', (err, visits) => {
    res.send('Number of visits ' + visits);
    client.set('visits', parseInt(visits) + 1);
  });
});

app.listen(8081, () => {
  console.log('listening on port 8081');
});

```

48. Create Dockerfile
```
FROM node:alpine

WORKDIR '/app'

COPY package.json .
RUN npm install
COPY . .

CMD ["npm","start"]
```
49. Run a seperate redis container 
```
docker run redis
```

```
docker run nodeGrider
```
Will fail

Set up networking infrastructure by using DockerCompose files

50. Docker Compose
```
touch docker-compose.yml
```
```
version: '3'
services:
  redis-server:
    image: 'redis'
  node-app:
    restart: on-failure
    build: .
    ports:
      - '4001:8081'

```
52. Networking

Containers contained within same docker-compose.yml will have access to eachother.
```
const client = redis.createClient({
  host: 'redis-server',
  port: 6379,
});
```
The host will make a best-effort attempt to connect to something called 'redis-server'

53. Docker compose
run docker image 
```
docker-compose up
```
launch in background  <--- preferred
```
docker-compose up
```

docker build and then run
```
docker-compose up --build
```

Can view running containers
'''
docker ps
'''

54. Stopping docker compose
```
docker-compose down
```

55. Dealing with crashes / Container maintenance



Status codes
```
0               We existed and everything is okay
1,2,3, etc      We exited because something went wrong
```

<ins>Restart policies </ins>

no  
always  
on-failure  
unless-stopped

```
  node-app:
    restart: on-failure
```

### Sec 5 - dev workflow

62.  basic react app
```
npx create-react-app frontend
```
63. react commands
```
npm run start
npm run test
npm run build
```

npm run build will build /build which will include an index.js and main.asdfgsad.js

64. Dev Dockerfile
```
touch Dockerfile.dev
```

```
FROM node:16-alpine
WORKDIR '/app'
COPY package.json .
RUN npm install
COPY . .
CMD ["npm", "run", "start"]
```

```
docker build -f Dockerfile.dev
docker build -f Dockerfile.dev -t steedman:frontend .
```

'''
docker build -t steedman:frontendTest .
docker run -p 8080:80 steedman:frontendTest
'''
65. Starting the container
```
docker run -p 3000:3000 adsfgasdffg
```
66. Docker volumes

Put a bookmark on the node_modules folder 

Add map reference to local folder from the Docker container

git bash
```
docker run -p 3000:3000 -v /app/node_modules -v $(pwd):/app <image_id>

docker run -it -p 3000:3000 -v /home/node/app/node_modules -v ~/frontend:/home/node/app USERNAME:frontend
docker run -it -p 3000:3000 -v /home/node/app/node_modules -v /home/YOURUSERNAME/frontend:/home/node/app USERNAME:frontend
```

ubuntu
* move source folder to ubuntu home location
* have the Dockerfile.dev to include change ownership
* build and run. changing the App.js file should now be reflected.
```
docker run -it -p 3000:3000 -v /home/node/app/node_modules -v /home/ubu/repos/frontend-wsl:/home/node/app steedman:frontend
OR
docker run -it -p 3000:3000 -v /home/node/app/node_modules -v /home/ubu/frontend:/home/node/app USERNAME:frontend
```

70. Shorthand docker compose for the above
```
version: "3"
services:
  web:
    build:
      context: .
      dockerfile: Dockerfile.dev
    ports:
      - "3000:3000"
    volumes:
      - /home/node/app/node_modules
      - .:/home/node/app
```
71. executing tests from Docker 

```
docker run -it fgsdfgtsdfhkjg npm run test
```
Will allow you to run your own manual testing.

72. Live updating tests
2 options
* Open 2nd terminal. Find running container and run test against existing container.
Problem: have to rememner process.
```
docker exec -it sdfgfdsg npm run test
```
* Attach a 2nd service in docker-compose, allows for statard input testing and overwrite starting command.
Problem: can't add any manual inputs.
```
version: "3"
services:
  web:
    build:
      context: .
      dockerfile: Dockerfile.dev
    ports:
      - "3000:3000"
    volumes:
      - /home/node/app/node_modules
      - .:/home/node/app
  tests:
    stdin_open: true
    build:
      context: .
      dockerfile: Dockerfile.dev
    volumes:
      - /home/node/app/node_modules
      - .:/home/node/app
    command: ["npm", "run", "test"]
```
```
docker-compose up --build
```
--build is used when another service is added.

77. Nginx

To be used in producation as web server used in Dev which falls away after build.

78. Multistep build process for Prod Dockerfile 
```
FROM node:16-alpine as builder
WORKDIR '/app'
COPY package.json .
RUN npm install
COPY . .
RUN npm run build

FROM nginx
COPY --from=builder /app/build /usr/share/nginx/html
```

79. Running nginx
```
docker build .
docker run -p 8080:80 asdfasdfsda
```
### Section 7 - Workflow 
80. Azure Devops CI
* Set up new service connection to Docker Registry. 
* Add pipeline file
```
touch azure-pipelines.yml
```
```
trigger:
- master

resources:
- repo: self

variables:
  tag: 'TestV2' 
  
stages:
- stage: Build
  displayName: Build image
  jobs:
  - job: Build
    displayName: Build
    pool:
      vmImage: ubuntu-latest
    steps:       
    - task: Docker@2
      displayName: Build an image
      inputs:
        command: build
        dockerfile: '$(Build.SourcesDirectory)/Dockerfile'
        repository: 'steedman/testazdo'
        tags: |
          $(tag)

    - task: Docker@2
      displayName: Login to Docker Hub
      inputs:
        command: login
        containerRegistry: steedman_dockerHub

    - task: Docker@2
      displayName: 'Push the Docker image to Dockerhub'
      inputs:
        repository: 'steedman/testazdo'
        command: 'push'
        tags: |
          $(tag)

    - task: Docker@2
      displayName: Logout of ACR
      inputs:
        command: logout
        containerRegistry: steedman_dockerHub

- stage: Deploy
  displayName: Deploy to AWS beanstalk
  jobs:
  - job: Build
    displayName: Build
    pool:
      vmImage: windows-latest
    steps:       
    - task: Bash@3
      displayName: TempFile
      inputs:
        targetType: 'inline'
        script: echo "hi there" > testFile.txt

    - task: AWSCLI@1
      displayName: AddTempFileToS3
      inputs:
        awsCredentials: 'aws4'
        regionName: 'eu-west-1'
        awsCommand: 's3'
        awsSubCommand: 'cp'
        awsArguments: 'testFile.txt s3://elasticbeanstalk-eu-west-1-498738601876'

    - task: AWSCLI@1
      displayName: AddTempFileToS3
      inputs:
        awsCredentials: 'aws4'
        regionName: 'eu-west-1'
        awsCommand: 'elasticbeanstalk'
        awsSubCommand: 'create-application-version'
        awsArguments: '--application-name "docker-react" --version-label 1 --source-bundle S3Bucket="elasticbeanstalk-eu-west-1-498738601876",S3Key=Dockerrun.aws.json'

    - task: AWSCLI@1
      displayName: AddTempFileToS3
      inputs:
        awsCredentials: 'aws4'
        regionName: 'eu-west-1'
        awsCommand: 'elasticbeanstalk'
        awsSubCommand: 'update-environment'
        awsArguments: '--application-name "docker-react" --environment-name "Dockerreact-env" --version-label=1'

```
* Add new pipeline. Point to azurepipeline file above. 
* Turn on CI
* Build stage builds the Docker image. Deploy, adds a config file to an S3 bucket, creates a version of that in the EBS and finally updates to that version.

```
aws elasticbeanstalk create-application-version --application-name "docker-react" --version-label 1 --source-bundle S3Bucket="elasticbeanstalk-eu-west-1-498738601876",S3Key=Dockerrun.aws.json
aws elasticbeanstalk update-environment --application-name "docker-react" --environment-name "Dockerreact-env" --version-label=1
```

### Section 8 - Multi Containers 

104. Set up worker folders and code 
worker/ => where work is done and which will connect to redis
server/ => express 



### Section 9 - Dockerizing Multi Containers 

117. Setup
It's a good idea to point the running Docker iamge back to the existing files. This will speed up the work process as the image doesn't need to be rebuilt every time.
Should set up volumes.

118. Dockerfile.dev
Setup Dockerfile.dev for client 
```
FROM node:16-alpine
WORKDIR '/app'
COPY ./package.json ./
RUN npm install
COPY . .
CMD ["npm", "run", "start"]
```
Setup Dockerfile.dev for server 
```
FROM node:16-alpine
WORKDIR "/app"
COPY ./package.json ./
RUN npm install
COPY . .
CMD ["npm", "run", "dev"]
```
Setup Dockerfile.dev for worker 
```
FROM node:16-alpine
WORKDIR "/app"
COPY ./package.json ./
RUN npm install
COPY . .
CMD ["npm", "run", "dev"]
```
```
docker build -f Dockerfile.dev .
```

120. DockerCompose file
```
toucn docker-compose.yml
```
```
version: '3'
services:
  postgres:
    image: 'postgres:latest'
    environment:
      - POSTGRES_PASSWORD=postgres_password
  redis:
    image: 'redis:latest'
  nginx:
    depends_on:
      - api
      - client
    restart: always
    build:
      dockerfile: Dockerfile.dev
      context: ./nginx
    ports:
      - '3050:80'
```


121. Add postgres to docker-compose. Add redis to docker-compose.
Add server. Add volumes for the server.
```
  api:
    build:
      dockerfile: Dockerfile.dev
      context: ./server
    volumes:
      - /app/node_modules
      - ./server:/app
```
122. Set up environment variables for different services. Default redis and postgress variables are available on the DockerHub page/
```
  api:
    build:
      dockerfile: Dockerfile.dev
      context: ./server
    volumes:
      - /app/node_modules
      - ./server:/app
    environment:
      - REDIS_HOST=redis
      - REDIS_PORT=6379
      - PGUSER=postgres
      - PGHOST=postgres
      - PGDATABASE=postgres
      - PGPASSWORD=postgres_password
      - PGPORT=5432
```
123. Add client service and worker service.
```
  client:
    environment:
      - WDS_SOCKET_PORT=0
    build:
      dockerfile: Dockerfile.dev
      context: ./client
    volumes:
      - /app/node_modules
      - ./client:/app
  worker:
    build:
      dockerfile: Dockerfile.dev
      context: ./worker
    volumes:
      - /app/node_modules
      - ./worker:/app
    environment:
      - REDIS_HOST=redis
      - REDIS_PORT=6379
```
125. Nginx Path Routing
Development will usee Nginx for multcontainer as there will be multiple routes (client or express server). Nginx will decide how routes get handled.

Nginx will redirect traffic based on 
* /
* /api/
 
126. ngnx default.conf
```
mkdir nginx
touch nginx/default.conf
```
```
upstream client {
  server client:3000;
}

upstream api {
  server api:5000;
}

server {
  listen 80;

  location / {
    proxy_pass http://client;
  }

  location /ws {
      proxy_pass http://client;
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "Upgrade";
  }

  location /api {
    rewrite /api/(.*) /$1 break;
    proxy_pass http://api;
  }
}
```
Qu. Upstream server. Server api and client. 
Changing the name 'server' to api to prevent confusion.
Note: the line 'rewrite /api/(.*) /$1 break;' says to remove the /api/ bit and leave the rest alone. The break says to apply no further rules.

126. nginx Dockerfile
```
FROM nginx
COPY ./default.conf /etc/nginx/conf.d/default.conf
```
Add nginx server to docker-compose.yml
```
  nginx:
    depends_on:
      - api
      - client
    restart: always
    build:
      dockerfile: Dockerfile.dev
      context: ./nginx
    ports:
      - '3050:80'
```
127. startup 
```
docker-compose up --build
```
Will likely fail first time.

Visit server on localhost:3050


130. websocket issue
Websockets allow the back and forth communication between the react app and server which will notify it of changes. The nginx config file can be updated to allow it through.

```
  location /ws {
      proxy_pass http://client;
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "Upgrade";
  }
```


### Section 9 - CI workflow for multicontainer app

134. Make producation versions of Dockerfiles.
Copy Dockefile and updaete the start up script to.
```
CMD ["npm", "run", "start"]
```

135. Multiple nginx

One for Routing and one for the client.

137. nginx listedning port for client server
```
touch client/nginx/default.conf
```
```
server {
  listen 3000;

  location / {
    root /usr/share/nginx/html;
    index index.html index.htm;
    try_files $uri $uri/ /index.html;
  }
}
```
```
touch Dockerfile
```
```
FROM node:16-alpine as builder
WORKDIR '/app'
COPY ./package.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx
EXPOSE 3000
COPY ./nginx/default.conf /etc/nginx/conf.d/default.conf
COPY --from=builder /app/build /usr/share/nginx/html
```
Expose it on 3000, copy over the nginx conf, copy over the build directory.


139. Deployment file
* Build production versions for all projects. Use dockerfile.dev as tests need to be run. 
* Then run tests
* If success, build production version of all projects, using all Dockefile's . Include them all in the same build step.

 ```
trigger:
- master

resources:
- repo: self

variables:
  tag: $(Build.BuildId)
  
stages:
- stage: Build
  displayName: Build image
  jobs:
  - job: Build
    displayName: Build
    pool:
      vmImage: ubuntu-latest
    steps:     
    # - task: Bash@3
    #   displayName: docker tests
    #   inputs:
    #     targetType: 'inline'
    #     script: echo steedman/react-test:$(tag)

### Image Test
    - task: Docker@2
      displayName: Build a test client image
      inputs:
        command: build
        dockerfile: '$(Build.SourcesDirectory)/client/Dockerfile.dev'
        repository: 'steedman/react-test'
        buildContext: '$(Build.SourcesDirectory)/client'
        tags: |
          $(tag)

    - task: Bash@3
      displayName: grep docker images
      inputs:
        targetType: 'inline'
        script: docker images | grep steedman

    - task: Bash@3
      displayName: docker tests
      inputs:
        targetType: 'inline'
        script: docker run -e CI=true steedman/react-test:$(tag) npm test
        tags: |
          $(tag)

### Image Build
    - task: Docker@2
      displayName: docker multi-client image
      inputs:
        command: build
        dockerfile: '$(Build.SourcesDirectory)/client/Dockerfile'
        repository: 'steedman/multi-client'
        buildContext: '$(Build.SourcesDirectory)/client'
        tags: |
          $(tag)

    - task: Docker@2
      displayName: docker multi-nginx image
      inputs:
        command: build
        dockerfile: '$(Build.SourcesDirectory)/nginx/Dockerfile'
        repository: 'steedman/multi-nginx'
        buildContext: '$(Build.SourcesDirectory)/nginx'
        tags: |
          $(tag)

    - task: Docker@2
      displayName: docker multi-server image
      inputs:
        command: build
        dockerfile: '$(Build.SourcesDirectory)/server/Dockerfile'
        repository: 'steedman/multi-server'
        buildContext: '$(Build.SourcesDirectory)/server'
        tags: |
          $(tag)

    - task: Docker@2
      displayName: docker multi-worker image
      inputs:
        command: build
        dockerfile: '$(Build.SourcesDirectory)/worker/Dockerfile'
        repository: 'steedman/multi-worker'
        buildContext: '$(Build.SourcesDirectory)/worker'
        tags: |
          $(tag)

    - task: Bash@3
      displayName: grep docker images
      inputs:
        targetType: 'inline'
        script: docker images | grep steedman

### DockerHub
    - task: Docker@2
      displayName: Login to Docker Hub
      inputs:
        command: login
        containerRegistry: steedman_dockerHub

    - task: Docker@2
      displayName: 'Push multi-client to Dockerhub'
      inputs:
        repository: 'steedman/multi-client'
        command: 'push'
        tags: |
          $(tag)

    - task: Docker@2
      displayName: 'Push multi-nginx to Dockerhub'
      inputs:
        repository: 'steedman/multi-nginx'
        command: 'push'
        tags: |
          $(tag)

    - task: Docker@2
      displayName: 'Push multi-server to Dockerhub'
      inputs:
        repository: 'steedman/multi-server'
        command: 'push'
        tags: |
          $(tag)

    - task: Docker@2
      displayName: 'Push multi-worker to Dockerhub'
      inputs:
        repository: 'steedman/multi-worker'
        command: 'push'
        tags: |
          $(tag)

    - task: Docker@2
      displayName: Logout of ACR
      inputs:
        command: logout
        containerRegistry: steedman_dockerHub
 ```

### Section 10 - CI workflow for multicontainer app

146. Container definition file

NOTE: THIS SECTION MAY BE OUT OF DATE
```
touch Docxkerrun.aws.json
```
```

```
Create a Elastic Beanstalk instance.

154. VPCs and security groups
Create security group and allow any traffic from any other AWS service that has this security group.
Attach security group to;
* EB Instance
* RDS (Postgres)
* EX (Redis)

155. DB Creation
* Search RBS. Create postgressql database.
* Micro class. 
* Name: multi-docker-postgres Master username:postgres Password: postgrespassword
* Add it to  default VPC.
* Not publically available.
* DB name: fibvalues
* Port: 5432

Create

156. Redis Creation
* search elasticache. redis
* name: multi-docker-redis
* node type: t2 micro
* Replicas: 0
* Subnet group: new, default group.
Create

157. Custome Security Group
* Create security group
* name: multi-docker
* group: multi-docker

Rules - Inbound role
* Custome TCP Rule, TCP (6), 5432-6379, allow traffic from 'multi-docker' Sec.Grp.

158. Applying new security group to redis, db and elb

* Cache. Modify, change modify sec grp.
* RDS: click DB. Details 'modify'. Change security group. Apply.
* ELB: Configuration, Instances, EC2 security groups-> click 'multi-docker'

159. Setting env vars
ELB -> Configuration -> Software -> Set variables here.
* REDIS_HOST: elasticache -> get primary endpooint. Leave out port.
* REDIS_PORT: 6379
* PGUSER: postgres
* PGPASSWORD: postgrespassword
* PGHOST: db-> instances-> Connect section -> copy endpoint e.g. multi-docker-postgres.cozmjl6qojkt.eu-west-1.rds.amazonaws.com
* PGDATABASE: fibvalues
* PGPORT: 5432


160. Notes on deployment

This deployment wasn't verified as the redis cache would not display it's endpoint. All other steps appear to work however.  
ADO deployment
```
- stage: Deploy
  displayName: Deploy to AWS beanstalk
  jobs:
  - job: Build
    displayName: Build
    pool:
      vmImage: windows-latest
    steps:       
    - task: AWSCLI@1
      displayName: Copy DockerCompose to S3
      inputs:
        awsCredentials: 'aws4'
        regionName: 'eu-west-1'
        awsCommand: 's3'
        awsSubCommand: 'cp'
        awsArguments: 'docker-compose.yml s3://elasticbeanstalk-eu-west-1-498738601876'
    - task: AWSCLI@1
      displayName: Create ELB app version
      inputs:
        awsCredentials: 'aws4'
        regionName: 'eu-west-1'
        awsCommand: 'elasticbeanstalk'
        awsSubCommand: 'create-application-version'
        awsArguments: '--application-name "multi-docker" --version-label 1 --source-bundle S3Bucket="elasticbeanstalk-eu-west-1-498738601876",S3Key=docker-compose.yml'
    - task: AWSCLI@1
      displayName: Update ELB to app version
      inputs:
        awsCredentials: 'aws4'
        regionName: 'eu-west-1'
        awsCommand: 'elasticbeanstalk'
        awsSubCommand: 'update-environment'
        awsArguments: '--application-name "multi-docker" --environment-name "Multidocker-env" --version-label=1'

```


# Sec 14 - Multi container app in Kubernetes

208. Routing
Ingress will handle routing in k8s. Nginx was used in docker.

215. Worker Cluster IP
Worker does not need a cluster IP as nothing needs to communicate to it. The worker pod is subscribed to the redis pod so it should see the ingestion and be able to do its work. 

231. k8 secrets
kc create secret generic postgres_password --from-literal POSTGRES_PASSWORD=12345asdf

# Sec 15 - Ingress Controllers in Kubernetes


