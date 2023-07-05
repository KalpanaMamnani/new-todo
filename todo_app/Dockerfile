# Stage 1: Build the application
FROM nginx:alpine AS build

WORKDIR /app

# Copy the necessary files
COPY . .

# Stage 2: Serve the application using Nginx
FROM nginx:alpine

# Copy the built application from the previous stage
COPY --from=build /app /usr/share/nginx/html