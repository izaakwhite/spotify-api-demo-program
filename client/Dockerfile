# This is the nodejs docker image. In very simplified terms we are downloading nodejs 
# into our docker environment and using nodejs to run our project like we would normally
FROM node:current AS base

# This is to set the current working directory in our docker environment. What we choose our name for the WORKDIR to be really does not matter.
# Just know when we set the workdir to something like /app. whenever we do something like "COPY file.txt ./" we would copy the file.txt file from the working directory
# that the physical Dockerfile sits in. To the /app directory in our docker environment
WORKDIR /app

# This copy command will download package.json and package-lock.json into our currently selected WORKDIR /app
COPY package.json package-lock.json* ./

# The package-lock.json file is a file that holds all of our npm packages that we use for our node website. We can use this file to download our packages using "npm ci". 
# Running "npm ci" is basically the same as running "npm install" but it is more optimized to be used in a production environment like a ci pipeline. 
RUN \
    if [ -f package-lock.json ]; then npm ci; \ 
    else echo "Warning: Lockfile not found. It is recommended to commit lockfiles to version control." && npm install; \
    fi

# This here will copy our actual code for our website into our docker environment
COPY ./public ./public
COPY ./src ./src

# like we typicaly would in any other environment. We will simply build our project in our docker environment
RUN npm run build; 

# This command is what is actually ran when we run our docker container. 
# For example we can use the command "docker build -t website /path/to/Dockerfile" to build our container. 
# To break down these arguments
# arg[0] "docker"  - executing the docker binary
# arg[1] "build"   - specifying that we are building a docker image
# arg[2] "-t"      - this flag is used whenever we want to name our docker image
# arg[3] "website" - the name that will be assigned to our docker image when it is built
# arg[4] "."       - the path to our Dockerfile (we are assuming that we are executing this command in the same directory as our Dockerfile)
#
# We can then (after a successful image build) run the command "docker run -p 3000:3000 website"
# To break down these arguments
# arg[0] "docker"    - executing the docker binary
# arg[1] "run"       - specifying that we will be running an image
# arg[2] "-p"        - This specifies that we want to open a port from our internal docker network to expose to our host network. I will later describe in more detail what this means
# arg[3] "3000:3000" - We are specifying what host port we want to bind our internal docker host port onto
# arg[4] "website"   - the name of the image that we are running
#
# The docker container will run these commands to actually start our website in the docker environment
CMD ["npm", "start"]

# [ A little explination on docker networking ]
# As the whole premice of docker is to run a task in a containerized environment, we need to decide what permissions our docker container has on our host machine.

# Initally whenever we run a docker container, our docker daemon will assign a network interface in which our running docker container will run in. In this example We will just call this network name "website_network" that has the network ip 172.17.0.3.

# Lets say that we would normally bind our website to port 3000 and connect to it on http://localhost:3000 ( or http://127.0.0.1:3000 they are the exact same thing) whenever we just ran it directly on our host machine. 
# Unfortunatley now, because our container is running in "website_network" it has no access to bind to our host ports like http://localhost:3000 because our website isnt binding to localhost:3000 anymore, its binding to 172.17.0.3:3000. 
# We need some way to bridge these two networks together so we can access our website on http://localhost:3000.

# This is where '-p 3000:3000' comes in. All this does when you run your container 127.0.0.1:3000 will direct traffic to 172.17.0.3:3000, therefore we can now access your website on http://localhost:3000.