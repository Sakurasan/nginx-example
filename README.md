```
docker run --name docker_nginx -d -p 8080:80 \
  -p 12345:12345 \
  -v $CODEPATH/docker/nginx/nginxdata/log:/var/log/nginx  \
  -v $CODEPATH/docker/nginx/nginxdata/conf/nginx.conf:/etc/nginx/nginx.conf \
  -v $CODEPATH/docker/nginx/nginxdata/conf.d:/etc/nginx/conf.d  \
  -v $CODEPATH/docker/nginx/nginxdata/html:/usr/share/nginx/html \
  nginx

```