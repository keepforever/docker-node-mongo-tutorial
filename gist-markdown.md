## Docker Compose notes from Traversy Media video: 
#### https://www.youtube.com/watch?v=hP77Rua1E0c&t=208s

### Note, when you create a Dockerfile, you do not specify a file extension.  No dot at the beginning either. 
### Should be in your root directory, same level as your package .json.

### Main Dockerfile

```
# My personal notes begin with "#", but this is not 
actually how you note things in a Dockerfile. And they 
# must be removed prior to running. 

# we could do 'FROM node: latest' but we run the risk of breaking chanes
# down the line with updates.  Instead we specify a stable version. This might 
# be the docker image.  
FROM node:10

# specify where our app will live on the container
# I think this is arbitrary..
WORKDIR /usr/src/app

# we need to move the package.json and package-lock.json to the working 
# adding a " * " to the end of package in the COPY command acts as way to say: 
# grab every file that starts with 'package' of the '.json' file extension
COPY package*.json ./

# to install dependencies listed in the package.json files
RUN npm install

# copy everything else in the root directory... 
COPY . .

# we expose 3000 because that's the port the application runs on. 
# and is specified on line 36 of the index.js file
EXPOSE 3000

# start command is in array form, same as "npm start" in when you 
# run an app locally.  Specified in the scripts property from package.json
# file. 
CMD ["npm", "start"]

```
# Docker Compose
### this file serves the same purpouse as running:
```
$ docker run -p [other options... ] -v ${pwd}:site some/image
```
### can, and will, run this command multiple times for the various 
### containers our application is intended to be COMPOSED of.

## docker-compose.yml
```
# must remove notes before running, all lines prefixed with " # ". 

# docker version
version: '3'

# these are all the containers we would otherwise be using 'docker run ... '
# to instanciate the containers that compose our app. 
services:
  # indentation is important. 2 spaces.
  # 'app' is arbitrary. 
  app:
    # 'docker-node-mongo' is the arbitrary name of the node app's container. 
    container_name: docker-node-mongo
    # if container fails, 'always' try to restart. 
    restart: always
    # we want to build from our dockerfile, we put " . " and that will look 
    # in the current dir for a Dockerfile 
    build: .
    # in:  - '80:3000', ' - ' is a way to go to a new line for readability. 
    ports:
      # since our app runs on 3000 we map 80(local machine's default) to 3000
      - '80:3000'
    links:
      - mongo
  # here, 'mongo' is arbitrary.  Sometimes labled 'db'.
  mongo:
    # name is arbitrary
    container_name: mongo
    # this image stands in place of " build: . " in the app config above. 
    # the difference being that we are building this container from the image on
    # docker hub and the app we are building according to the config in Dockerfile, 
    image: mongo
    # the dafault port and the port we want exposed (and other apps to access via) 
    # are the standard mongo 21017
    ports:
      - '27017:27017'
```


## index.js (node app)
#### an example of how the app container would interact with the mongo 
#### container. 'mongo:27017' is the container_name and port# as specified 
#### in the docker-compose file.  

```
// Connect to MongoDB
mongoose
  .connect(
    'mongodb://mongo:27017/docker-node-mongo',
    { useNewUrlParser: true }
  )
  .then(() => console.log('MongoDB Connected'))
  .catch(err => console.log(err));

```

# Eveything below here is boiler markdown for use above.
### Docker version info

```
$ docker version
```