# Stage-1 & specify a name 'builder'
FROM node:latest AS builder

WORKDIR /app

# Pass the build argument and set the environment variable for the build
ARG REACT_APP_SERVER_URL
ENV REACT_APP_SERVER_URL=$REACT_APP_SERVER_URL

# Copy package.json and install dependencies
COPY package.json .
RUN npm install

# Copy the remaining source code and build the app
COPY . .
RUN npm run build

# Stage-2: Use nginx to serve the build
FROM nginx:1.25.2-alpine-slim

# Copy the built files
COPY --from=builder /app/build /usr/share/nginx/html

# Expose port 80
EXPOSE 80

# Run nginx in foreground
CMD ["nginx", "-g", "daemon off;"]
