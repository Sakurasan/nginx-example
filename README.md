```
docker run --name tengine -d -p 8080:80 \
  -p 12345:12345 \
  -v $CODEPATH/docker/nginx/nginxdata/log:/var/log/nginx  \
  -v $CODEPATH/docker/nginx/nginxdata/conf/nginx.conf:/etc/nginx/nginx.conf \
  -v $CODEPATH/docker/nginx/nginxdata/conf.d:/etc/nginx/conf.d  \
  -v $CODEPATH/docker/nginx/nginxdata/html:/usr/share/nginx/html \
  goenv/tengines:latest

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
example
```
server {
    resolver 119.29.29.29;
    resolver_timeout 10s;
 
    listen 12345;
 
    access_log  /var/log/nginx/proxy.access.log;
    error_log   /var/log/nginx/proxy.error.log;

    proxy_connect;
    proxy_connect_allow            443 563;
    proxy_connect_connect_timeout  10s;
    proxy_connect_read_timeout     10s;
    proxy_connect_send_timeout     10s;
 
    # location / {
    #     proxy_pass $scheme://$host$request_uri;
    #     proxy_set_header Host $http_host;
 
    #     proxy_buffers 256 4k;
    #     proxy_max_temp_file_size 0;
 
    #     proxy_connect_timeout 30;
 
    #     proxy_cache_valid 200 302 10m;
    #     proxy_cache_valid 301 1h;
    #     proxy_cache_valid any 1m;
    # }
    location / {
        if ($http_host ~ "(:(\d){4})$"){ 
            set_by_lua $port "
            local host = ngx.req.get_headers()[\"host\"]; 
            local port = string.sub(host,-4);
            return port ";
            set $cookie_passport $port ;
            proxy_pass http://$host:$cookie_passport$request_uri; 
        }

        if ($http_host ~ "(:(\d){3})$"){ 
            set_by_lua $port "
                local host = ngx.req.get_headers()[\"host\"]; 
                local port = string.sub(host,-3);
                return port ";
            set $cookie_passport $port ;
            proxy_pass http://$host:$cookie_passport$request_uri; 
        }

        if ($http_host ~ "(:(\d){2})$"){
            set_by_lua $port "
                local host = ngx.req.get_headers()[\"host\"]; 
                local port = string.sub(host,-2);
                return port ";
            set $cookie_passport $port ;
            proxy_pass http://$host:$cookie_passport$request_uri; 
        }

        if ($http_host !~ :){
            proxy_pass http://$host$request_uri; 
        }
		 
	}

}
```

```
    location / {
        #proxy_pass $scheme://$host$request_uri;
        #proxy_set_header Host $http_host;
 
        #proxy_buffers 256 4k;
        #proxy_max_temp_file_size 0;
 
        #proxy_connect_timeout 30;
 
        #proxy_cache_valid 200 302 10m;
        #proxy_cache_valid 301 1h;
        #proxy_cache_valid any 1m;

        set $passport 80;
        if ($http_host ~ "(:(\d){4})$"){
          set_by_lua $port "
            local host = ngx.req.get_headers()[\"host\"];
            local port = string.sub(host,-4);
            return port ";
          set $passport $port ;
        }
    
        if ($http_host ~ "(:(\d){3})$"){
          set_by_lua $port "
            local host = ngx.req.get_headers()[\"host\"];
            local port = string.sub(host,-3);
            return port ";
          set $passport $port ;
        }
    
        if ($http_host ~ "(:(\d){2})$"){
          set_by_lua $port "
            local host = ngx.req.get_headers()[\"host\"];
            local port = string.sub(host,-2);
            return port ";
          set $passport $port ;
        }
        if ($scheme = 'http') {
          proxy_pass http://$host:$passport$request_uri;
        }
        if ($scheme = 'https') {
          proxy_pass https://$host$request_uri;
        }
        proxy_set_header Host $host;		 
        #  proxy_buffers 256 4k;
        #  proxy_max_temp_file_size 0k; 


    }
```