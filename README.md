```
docker run --name docker_nginx -d -p 8080:80 \
  -p 12345:12345 \
  -v $CODEPATH/docker/nginx/nginxdata/log:/var/log/nginx  \
  -v $CODEPATH/docker/nginx/nginxdata/conf/nginx.conf:/etc/nginx/nginx.conf \
  -v $CODEPATH/docker/nginx/nginxdata/conf.d:/etc/nginx/conf.d  \
  -v $CODEPATH/docker/nginx/nginxdata/html:/usr/share/nginx/html \
  nginx

```
> nginx-lua https://github.com/sailor103/nginx-lua

```
docker run \
    --name dnsmasq \
    -d \
    -p 53:53/udp \
    -p 5380:8080 \
    -v $CODEPATH/docker/dnsmasq/dnsmasq.conf:/etc/dnsmasq.conf \
    --log-opt "max-size=100m" \
    -e "HTTP_USER=admin" \
    -e "HTTP_PASS=123456" \
    --restart always \
    jpillora/dnsmasq
```