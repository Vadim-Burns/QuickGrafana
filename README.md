# Infra host

Quick start to grafana server monitoring!

## What you get?

- Ready to use grafana node exporter dashboard
TODO: add dashboard image

- Ready to use dozzle for checking docker logs
TODO: add dozzle image


## Requirements

1. [Docker and docker-compose](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)

## How-to

1. Download this repository `git clone ...`
2. Start services `docker-compose up -d`
3. Import node-exporter [dashboard](https://grafana.com/grafana/dashboards/1860-node-exporter-full/)
3. (Optional) Open prometheus to network using nginx
```
map $http_upgrade $connection_upgrade {
  default upgrade;
  '' close;
}

upstream grafana {
  server localhost:3000;
}

upstream dozzle {
  server localhost:8888;
}

server {
        root /var/www/html;

        listen 80 default_server;

        location / {
                index infra.html;
        }


        # Dozzle
        location ^~ /dozzle/ {
                #include /config/nginx/proxy.conf;
                #include /config/nginx/resolver.conf;
                proxy_pass http://dozzle;

                chunked_transfer_encoding off;
                proxy_buffering off;
                proxy_cache off;
        }

        # Grafana
        location /grafana/ {
                proxy_set_header Host $http_host;
                proxy_pass http://grafana;
        }

        # Proxy Grafana Live WebSocket connections.
        location /grafana/api/live/ {
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection $connection_upgrade;
                proxy_set_header Host $http_host;
                proxy_pass http://grafana;
        }
}
```
