# Serving an SPA (its static files) from an NGINX based container image

The most important thing here is the following setting in `nginx.conf`:

```sh
    location / {
        try_files $uri$args $uri$args/ /index.html;
    }
```

This makes sure that any url paths in the SPA will not be handled by nginx but forwarded to the SPA that lives in index.html.

## Single stage build (easier entry)

The general process to get this running:

1. Put `Dockerfile` and `nginx.conf` into your project folder.
1. Build your SPA as per your requirements (for Angular this might e.g. be executing `ng build --aot --prod --env=prod`).
1. Run a docker build with your project folder as build context.
1. Running the resulting image will serve the static content on port 8080.

## Multi stage build 

The build could also be a [multi stage build](https://docs.docker.com/develop/develop-images/multistage-build/) similar to this:

```Dockerfile
# ===== Build stage =====
FROM node AS buildandtest 
# Install chrome and ng ---
RUN apt-get update; apt-get install -y gconf-service libasound2 libatk1.0-0 libcairo2 libcups2 libfontconfig1 libgdk-pixbuf2.0-0 libgtk-3-0 libnspr4 libpango-1.0-0 libxss1 fonts-liberation libappindicator1 libnss3 lsb-release xdg-utils
RUN wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb; dpkg -i google-chrome-stable_current_amd64.deb; apt-get -fy install
RUN npm install @angular/cli -g --unsafe-perm
# Build -------------------
WORKDIR /src
# First only copy package.json and npm install, so that this can be cached in a separate layer
COPY package.json .
RUN npm install
# Now copy all the rest and do the build
COPY . .
RUN ng build --aot --prod --env=prod

# ===== Production stage =====
FROM nginx:1.13.12-alpine AS production 
COPY --from=buildandtest /src/dist /usr/share/nginx/html

# Copy the specific nginx.conf we need for ng specifics...
COPY nginx.conf /etc/nginx/nginx.conf

# see http://pjdietz.com/2016/08/28/nginx-in-docker-without-root.html
RUN touch /var/run/nginx.pid && \
  chown -R nginx:nginx /var/run/nginx.pid && \
  chown -R nginx:nginx /var/cache/nginx

# run as non-root
USER nginx

```