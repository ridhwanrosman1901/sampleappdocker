# Sample React Application with Docker

This project demonstrates how to build and run a React application using Docker and Docker Compose. It includes configurations for both development and production environments.

## Project Structure

- `docker-compose.yaml`: Configuration for Docker Compose to manage multi-container Docker applications.
- `Dockerfile.dev`: Dockerfile for the development environment, using Node.js and Nginx.
- `Dockerfile`: Dockerfile for the production environment, using Node.js.
- `App.js`: The main React component for the sample application.

## Docker Compose Configuration

The `docker-compose.yaml` file defines the setup for running the React application with Docker Compose. It includes:

- **Service Name**: `react-app`
- **Build Context**: The current directory, using `Dockerfile.dev` for building the image.
- **Ports**: Maps port 80 in the container to port 4000 on the host.
- **Volumes**: Mounts the current directory to `/sampleapp` in the container and ensures `node_modules` is managed correctly.
- **Environment Variables**: Sets `CHOKIDAR_USEPOLLING` to `true` for file change detection in development.

```yaml
version: '3'
services:
  react-app:
    build:
      context: .
      dockerfile: ./Dockerfile.dev
    ports:
      - "4000:80"
    volumes:
      - .:/sampleapp
      - /sampleapp/node_modules
    environment:
      - CHOKIDAR_USEPOLLING=true
```

## Dockerfile for Development

The `Dockerfile.dev` is used to build the development image. It:

1. **Uses Node.js**: Starts from the latest Node.js image.
2. **Sets Working Directory**: Creates and uses `/sampleapp` as the working directory.
3. **Installs Dependencies**: Copies `package*.json` and runs `npm install`.
4. **Builds the Application**: Copies the application code and builds it.
5. **Uses Nginx**: Copies the build artifacts to Nginx's default HTML directory for serving.

```dockerfile
FROM node:latest AS build

WORKDIR /sampleapp

COPY package*.json ./
RUN npm install

COPY . .

RUN npm run build

FROM nginx

COPY --from=build /sampleapp/build /usr/share/nginx/html
```

## Dockerfile for Production

The `Dockerfile` is used for the production environment. It:

1. **Uses Node.js**: Starts from the latest Node.js image.
2. **Sets Working Directory**: Creates and uses `/sampleapp` as the working directory.
3. **Installs Dependencies**: Copies `package*.json` and runs `npm install`.
4. **Copies Application Code**: Includes the application code and exposes port 3000.
5. **Sets the Default Command**: Runs `npm start` to start the application.

```dockerfile
FROM node:latest

WORKDIR /sampleapp

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

## Running the Application

1. **Build and Start the Application**:
   ```bash
   docker-compose up --build
   ```

2. **Access the Application**: Open your browser and navigate to `http://localhost:4000`.

## Development

For development purposes, Docker will watch for changes in the codebase and reload the application accordingly. Ensure you have Docker and Docker Compose installed on your machine.

## Additional Resources

- [Docker Documentation](https://docs.docker.com/)
- [React Documentation](https://reactjs.org/)