# Use Node.js official image as a base
FROM node:14

# Set the working directory inside the container
WORKDIR /app

#ENV Variables for runtime
ENV ME_CONFIG_MONGODB_ADMINUSERNAME=admin
ENV ME_CONFIG_MONGODB_ADMINPASSWORD=password
ENV ME_CONFIG_MONGODB_SERVER=mongo-container:27017
ENV ME_CONFIG_MONGODB_DATABASE=PoolsApp

# Copy package.json and package-lock.json to the working directory
COPY package*.json ./

# Install backend dependencies
RUN npm install

RUN npm install -g nodemon

# Copy all the application files to the container
COPY . .

# Expose the backend port (default: 1234)
EXPOSE 1234

# Start the backend server
CMD ["npm", "start"]
