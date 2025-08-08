# Tempo Installation with TLS configuration

To install Tempo, clone the git repository by executing the below command
```sh
git clone https://github.com/grafana/tempo.git
```

Then navigate into the directory and edit the docker compose file so that we are only using memcache and Tempo for now. 

(Please refer to the attached docker-compose.yaml file in XMon Target folder)

```sh
cd tempo/example/docker-compose/local
```

After saving the yaml file, now let’s create certificate and key to make tempo secure.

To Configure TLS for GRPC

Navigate to /home/ubuntu/tempo/example/docker-compose/local/tempo-data, Create a config file with the below content and pass the IP address in the configuration.

```sh
cd /home/ubuntu/tempo/example/docker-compose/local/tempo-data
```

```sh
nano san.cnf
```

san.cnf:

[req]
default_bits  = 2048
distinguished_name = req_distinguished_name
req_extensions = req_ext
x509_extensions = v3_req
prompt = no[req_distinguished_name]
countryName = XX
stateOrProvinceName = N/A
localityName = N/A
organizationName = Self-signed certificate
commonName =<ip-addr>: Self-signed certificate[req_ext]
subjectAltName = @alt_names[v3_req]
subjectAltName = @alt_names[alt_names]
IP.1 = <ip-addr>

Save this file as san.cnf and run the following command.

```sh
openssl req -x509 -nodes -days 730 -newkey rsa:2048 -keyout tempo.key -out tempo.crt -config san.cnf
```

This will create a tempo.crt and tempo.key file which is to be passed in the tempo.yaml and the otel-config.yaml to establish a secure connection.


Next change the permission of the crt and key file by executing the below commands

```sh
chmod 755 tempo.key tempo.crt

chown 10001 tempo.key tempo.crt

chgrp 10001 tempo.key tempo.crt
```

Add volumes to the docker-compose file and add tls configuration under receiver/otlp/grpc. 

(Check the attached docker-compose.yaml and tempo.yaml for the above configurations.)

Now execute the below command to create the tempo container.

```sh
docker-compose up –d
```


# In X-Mon target

Copy the tempo.crt file into the local otelcol-contrib directory and change permissions if needed.

Add tempo exporter, pipeline to the otel config file and restart the otel service. 

(Please check otel-config.yaml file in X-Mon target folder for the exporter configuration).

Finally add the tls certificate in the grafana tempo data source section and save & test the connection.

Finally start the demo application and check if the traces are being sent to tempo.


Reference links:

https://grafana.com/docs/tempo/latest/setup/operator/quickstart/

https://grafana.com/docs/tempo/latest/configuration/network/tls/

https://github.com/open-telemetry/opentelemetry-collector/tree/main/config/configtls








 
