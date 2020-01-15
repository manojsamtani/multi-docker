# use an existing docker image as a base
FROM node:alpine as builder

# download and install a dependency
#RUN apk update
#RUN apk add --update nodejs npm
# if directory does not exist, will get created by workdir
###WORKDIR /mnt/node
#COPY package.json ./
#RUN npm install
#COPY index.js ./
#RUN npm run build
#

WORKDIR '/app'
COPY ./package.json ./
RUN npm install
COPY . .
RUN npm run build
#CMD ["npm", "run", "start"]

###RUN mkdir /mnt/node/build
##RUN echo "multicontainer - testing builder" > /mnt/node/build/index.html

FROM nginx
EXPOSE 3000
COPY ./nginx/default.conf /etc/nginx/conf.d/default.conf
COPY --from=builder /app/build /usr/share/nginx/html
