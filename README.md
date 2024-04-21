# Language Output Generator (LOG)

See [documentation](https://www.figma.com/file/tJYSeV3GA4ECbXn6oVzAUV/LOG-Design-Documentation?type=whiteboard&node-id=0%3A1&t=M0PDdGPnckSUL8Ky-1) for details on what this is about!

---------------

## Set Up
**High-Level Summary**
This project is a web and mobile application using a MERN stack with Docker. 

**Regarding Docker and Git**
We'll have three Docker containers: a frontend, a backend, and one for MongoDB. The frontend and backend will be housed in their own repositories, and to make sure everything can be deployed nicely, we have another repository for the configuration.

To summarize:
- Repositories
    - "frontend": the frontend
    - "backend": the backend
    - "config": the config; houses deployment details
- Docker Containers
    - "frontend": the frontend, using the same name as the repository for simplicity
    - "backend": the backend, using the same name as the repository for simplicity
    - "mongo": the MongoDB server

**Regarding MERN**
- M | The database of choice is MongoDB
- E | The backend uses ExpressJS (JavaScript routing framework build on NodeJS)
- R | The frontend uses ReactJS (JavaScript UI framework)
- N | Both the frontend and backend are built on NodeJS (JavaScript runtime environment framework)

## Dockerfile Setup
In both the frontend and backend, there is a `Dockerfile` looking like so, where the **\<port number\>** depends on the app.

```Dockerfile
# Start your image with a node base image
FROM node:18-alpine

# The /app directory should act as the main application directory
WORKDIR /app

# Copy the app package and package-lock.json file
COPY package.json .

# Copy local directories to the current local directory of our docker image (/app)
COPY . .

# Install node packages, install serve, build the app, and remove dependencies at the end
RUN npm install

EXPOSE <port number>

# Start the app using serve command
CMD ["npm", "start"]
```

We also have a simple `.dockerignore` the frontend and backend:

```
./node_modules
Dockerfile
.dockerignore
.git;
```

Lastly, we have a `compose.yaml` file right here in the `log-config` repository, which calls the creation and states the dependencies, volumes, and so on. This is what will create the images and start the containers.

## Running Docker
To actually run docker, it's as simple as running `docker compose up --detach`. That last keyword is what allows the command to run in the background.

## Accessing MongoDV in Docker Container
To get to db in terminal:
- sh into container: `docker exec -it log-config-mongo-1 bash`, where `log-config-mongo-1` is the container name.
- sh into mongodb: `mongosh -u root -p example` to actually get in mongo

## Troubleshooting Notes
[From the NodeJS GitHub](https://github.com/nodejs/docker-node/blob/main/README.md#how-to-use-this-image):

>Docker Compose example mounts your current directory (including node_modules) to the container. It assumes that your application has a file named package.json defining start script.

Be sure to include a `start` script in the `package.json` to be safe.