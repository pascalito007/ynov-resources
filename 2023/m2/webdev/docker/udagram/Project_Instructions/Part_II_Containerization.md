# Part 2 - Run the project locally in a multi-container environment

The objective of this part of the project is to:

- Set up each microservice to be run in its own Docker container

The Udagram application have the following services running internally:

1. Backend `/user/` service - allows users to register and log into a web client.
1. Backend `/feed/` service - allows users to post photos, and process photos using image filtering.
1. Frontend - It is a basic Ionic client web application that acts as an interface between the user and the backend services.
1. Nginx as a reverse proxy server - for resolving multiple services running on the same port in separate containers. When different backend services are running on the same port, then a reverse proxy server directs client requests to the appropriate backend server and retrieves resources on behalf of the client.

Navigate to the project root directory, and set up the environment variables again:

```bash
source set_env.sh
```

### Dockerfile for the Backend APIs

The Dockerfile for the above two backend services will be like:

```bash
FROM node:16
# Create app directory
WORKDIR /usr/src/app
# Install app dependencies

COPY package*.json ./
RUN npm ci
# Bundle app source
COPY . .
EXPOSE 8080
CMD [ "npm", "run", "dev" ]
```

> Note: A wildcard is used in the line `COPY package*.json ./` to ensure both package.json and package-lock.json are copied where available (npm@5+)

It's not a hard requirement to use the exact same Dockerfile above. Feel free to use other base images or optimize the commands.

### Frontend Application

Please make use of `pasciano007/udagram-frontend`. Pull it and put it in your private registry

### How would containers discover each other and communicate?

Use another container named _reverseproxy_ running the Nginx server. The _reverseproxy_ service will help add another layer between the frontend and backend APIs so that the frontend only uses a single endpoint and doesn't realize it's deployed separately. *This is one of the approaches and not necessarily the only way to deploy the services. *To set up the _reverseproxy_ container, follow the steps below:

1. Create a Dockerfile as inside _/udagram-reverseproxy/_ directory:

```bash
FROM nginx:alpine
COPY nginx.conf /etc/nginx/nginx.conf
```

2. Create the _nginx.conf_ file that will associate all the service endpoints as:

```bash
worker_processes 1;
events { worker_connections 1024; }
error_log /dev/stdout debug;
http {
    sendfile on;
    upstream user {
        server backend-user:8080;
    }
    upstream feed {
        server backend-feed:8080;
    }
    proxy_set_header   Host $host;
    proxy_set_header   X-Real-IP $remote_addr;
    proxy_set_header   X-NginX-Proxy true;
    proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header   X-Forwarded-Host $server_name;
    server {
        listen 8080;
        location /api/v0/feed {
            proxy_pass         http://feed;
        }
        location /api/v0/users {
            proxy_pass         http://user;
        }
    }
}
```

The Nginx container will expose 8080 port. The configuration file above, in the `server` section, it will route the _http://localhost:8080/api/v0/feed_ requests to the _backend-user:8080_ container. The same applies for the _http://localhost:8080/api/v0/users_ requests.

### Use Docker compose to build and run multiple Docker containers

> **Note**: The ultimate objective of this step is to have the Docker images for each microservice ready locally. This step can also be done manually by building and running containers one by one.

1. Once you have created the Dockerfile in each of the following services directories, you can use the `docker-compose` command to build and run multiple Docker containers at once.

   - _/udagram-api-feed/_
   - _/udagram-api-feed/_
   - _/udagram-frontend/_
   - _/udagram-reverseproxy/_

The `docker-compose` <a href="https://docs.docker.com/compose/" target="_blank">command</a> uses a YAML file to configure your application’s services in one go. Meaning, you create and start all the services from your configuration file, with a single command. Otherwise, you will have to individually build containers one-by-one for each of your services.

2. **Create Images** - In the project's parent directory, create a [docker-compose-build.yaml] with below content.

```bash
version: "3"
services:
  reverseproxy:
    build:
      context: ./udagram-reverseproxy
    image: reverseproxy
  backend_user:
    build:
      context: ./udagram-api-user
    image: udagram-api-user
  backend_feed:
    build:
      context: ./udagram-api-feed
    image: udagram-api-feed
```

It will create an image for each individual service. Then, you can run the following command to create images locally:

```bash
# Make sure the Docker are running in your local machine
# Remove unused and dangling images
docker image prune --all
# Run this command from the directory where you have the "docker-compose-build.yaml" file present
docker-compose -f docker-compose-build.yaml build --parallel
```

> **Note**: YAML files are extremely indentation sensitive, make sure you did not altered the content.

3. **Run containers** using the images created in the step above. Create another YAML file, [compose.yaml], in the project's parent directory with below requirements.

- make use of your own private registry images
- the containers need to be inside a network called `udagram`
- the database (postgres) container expect the environnement variables POSTGRES_PASSWORD, POSTGRES_USER, POSTGRES_DB
- The reverseproxy container export `8080` inside the container and need to expose `8080` outside
- The reverse proxy `depends_on` the containers `udagramd-db` , `backend-user` and `backend-feed`
- The backend-user and the backend-feed expect below environnement variables
  - POSTGRES_USERNAME, POSTGRES_PASSWORD,POSTGRES_DB,POSTGRES_HOST,AWS_REGION,AWS_PROFILE,AWS_BUCKET,JWT_SECRET and URL: `"http://localhost:8100"` and they `depends_on` the database.

Once you have the YAML file above ready in your project directory, you can start the application using:

```bash
docker-compose up -d
```

4. Do the port forwarding for the frontend app
5. Visit http://localhost:8100 in your web browser to verify that the application is running.
