<h1>Docker and Kubernetes: The Complete Guide</h1>
Stepehn Grider.
https://www.udemy.com/course/docker-and-kubernetes-the-complete-guide

<h3>Sec 1 - intro</h3>

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

<h3>Sec 2 - Custom Image with Docker</h3>

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
<h3>Sec 4 - basic node js express project</h3>

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

<h3>Sec 5 - dockerCompose - express + redis</h3>

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

<h3>Sec 5 - dev workflow</h3>

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
