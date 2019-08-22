FROM nginx:stable-alpine

# This expects the static content of your SPA in the folder ./dist in your build context during your docker build
COPY ./dist /usr/share/nginx/html

# Copy the specific nginx.conf we need for ng...
COPY nginx.conf /etc/nginx/nginx.conf

# see http://pjdietz.com/2016/08/28/nginx-in-docker-without-root.html
RUN touch /var/run/nginx.pid && \
  chown -R nginx:nginx /var/run/nginx.pid && \
  chown -R nginx:nginx /var/cache/nginx

# run as non-root
USER nginx

EXPOSE 8080