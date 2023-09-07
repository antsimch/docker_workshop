# what your image will be based on
FROM node:20

# create a directory and CD into the directory
# mkdir /app
# cd /app
WORKDIR /app

# copy the files from local into the image
COPY main.js .
COPY metrics.js .
COPY package-lock.json .
COPY package.json .

# copy the directories from local into the image
COPY public public
COPY views views

# install packages (clean install)
RUN npm ci

# define an environment variable and set it to 5000
ENV PORT=5000 METRICS_PORT=5100
ENV INSTANCE_NAME="This is my first containerized application"

# tell Docker what ports to expose
EXPOSE ${PORT}/tcp ${METRICS_PORT}/tcp

# run the application
ENTRYPOINT node main.js
