Genere 4 clusters en 8082, 8083, 8084 y 8085

console

┬─[valentin@valeng:~/Desktop/Backend/Entrega-15]
╰─>$ pm2 start ./src/server.js --name="ClusterEn8082" --watch -i 2  -- -p 8082

┬─[valentin@valeng:~/Desktop/Backend/Entrega-15]
╰─>$ pm2 start ./src/server.js --name="ClusterEn8083" --watch -i 2  -- -p 8083

┬─[valentin@valeng:~/Desktop/Backend/Entrega-15]
╰─>$ pm2 start ./src/server.js --name="ClusterEn8084" --watch -i 2  -- -p 8084

┬─[valentin@valeng:~/Desktop/Backend/Entrega-15]
╰─>$ pm2 start ./src/server.js --name="ClusterEn8085" --watch -i 2  -- -p 8085


Compruebo la lista:

console

┬─[valentin@valeng:~/Desktop/Backend/Entrega-15]
╰─>$ pm2 list
┌─────┬──────────────────┬─────────────┬─────────┬─────────┬──────────┬────────┬──────┬───────────┬──────────┬──────────┬──────────┬──────────┐
│ id  │ name             │ namespace   │ version │ mode    │ pid      │ uptime │ ↺    │ status    │ cpu      │ mem      │ user     │ watching │
├─────┼──────────────────┼─────────────┼─────────┼─────────┼──────────┼────────┼──────┼───────────┼──────────┼──────────┼──────────┼──────────┤
│ 0   │ ClusterEn8082    │ default     │ 1.0.0   │ cluster │ 17066    │ 48s    │ 1    │ online    │ 0%       │ 70.0mb   │ valentin  │ enabled  │
│ 1   │ ClusterEn8082    │ default     │ 1.0.0   │ cluster │ 17072    │ 48s    │ 1    │ online    │ 0%       │ 70.2mb   │ valentin  │ enabled  │
│ 2   │ ClusterEn8083    │ default     │ 1.0.0   │ cluster │ 17084    │ 48s    │ 1    │ online    │ 0%       │ 69.5mb   │ valentin  │ enabled  │
│ 3   │ ClusterEn8083    │ default     │ 1.0.0   │ cluster │ 17078    │ 48s    │ 1    │ online    │ 0%       │ 69.6mb   │ valentin  │ enabled  │
│ 4   │ ClusterEn8084    │ default     │ 1.0.0   │ cluster │ 17259    │ 37s    │ 0    │ online    │ 0%       │ 70.6mb   │ valentin  │ enabled  │
│ 5   │ ClusterEn8084    │ default     │ 1.0.0   │ cluster │ 17266    │ 37s    │ 0    │ online    │ 0%       │ 70.7mb   │ valentin  │ enabled  │
│ 6   │ ClusterEn8085    │ default     │ 1.0.0   │ cluster │ 17368    │ 30s    │ 0    │ online    │ 0%       │ 70.5mb   │ valentin  │ enabled  │
│ 7   │ ClusterEn8085    │ default     │ 1.0.0   │ cluster │ 17375    │ 30s    │ 0    │ online    │ 0%       │ 71.1mb   │ valentin  │ enabled  │
└─────┴──────────────────┴─────────────┴─────────┴─────────┴──────────┴────────┴──────┴───────────┴──────────┴──────────┴──────────┴──────────┘


Nos movemos donde tenemos el archivo de configuración y lo editamos:

console

┬─[valentin@valeng:/etc/nginx]
╰─>$ sudo nvim nginx.conf



console

events {
}

http {

    upstream backend {
        server 127.0.0.1:8082;
        server 127.0.0.1:8083;
        server 127.0.0.1:8084;
        server 127.0.0.1:8085;
    }
    server {
        listen 8080;
        location /test/random/ {
            proxy_pass http://backend/test/randoms/;
        }
    }

}


Compruebo que la sintáxis sea correcta:

console

─[valentin@valeng:/etc/nginx]
╰─>$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

----

Frenamos y volvemos a prender el servicio

console

┬─[valentin@valeng:/etc/nginx]
╰─>$ sudo service nginx stop
┬─[valentin@valeng:/etc/nginx]
╰─>$ sudo service nginx start


Agregamos un log cuando se genere la lista y se puede comprobar a través de **pm2 monit**

console

┬─[valentin@valeng:/etc/nginx]
╰─>$ pm2 monit


┌─ Process List ───────────────────────┐┌──  ClusterEn8084 Logs  ────────────────────────────────────────────────────────────────────┐
│[ 0] ClusterEn8082     Mem:  69 MB    ││ ClusterEn8084 > Lista generada                                                             │
│[ 1] ClusterEn8082     Mem:  71 MB    ││                                                                                            │
│[ 2] ClusterEn8083     Mem:  69 MB    ││                                                                                            │
│[ 3] ClusterEn8083     Mem:  70 MB    ││                                                                                            │
│[ 4] ClusterEn8084     Mem:  71 MB    ││                                                                                            │
│[ 5] ClusterEn8084     Mem:  70 MB    ││                                                                                            │
│[ 6] ClusterEn8085     Mem:  69 MB    ││                                                                                            │
│[ 7] ClusterEn8085     Mem:  72 MB    ││                                                                                            │
│                                      ││                                                                                            │
                                            [...]

```