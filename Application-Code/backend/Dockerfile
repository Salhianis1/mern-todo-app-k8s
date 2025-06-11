# FROM node:14
# WORKDIR /usr/src/app
# COPY package*.json ./
# RUN npm install
# COPY . .
# CMD ["node", "index.js"]
####################################################
# Stage 1: Build
FROM node:14 AS build
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .

# Stage 2: Production
FROM node:14-slim
WORKDIR /usr/src/app

# Copy only needed files from the build stage
COPY --from=build /usr/src/app ./

# Run the application
CMD ["node", "index.js"]