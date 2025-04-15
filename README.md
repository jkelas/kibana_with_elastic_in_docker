# Kibana with Elastic in Docker

This project sets up an Elastic Stack in Docker environment, using Docker-Compose.
It also spins a Real Elastic Package Registry (EPR) and also a fake EPR (using an http-echo server). 

It makes it very easy to test different version of Kibana/Elastic, simply by changing the version in the docker-compose.yml file. 

It enables testing of communication between Kibana and EPR.

## Project Structure

## Services

The following services are defined in the `docker-compose.yml` file:

- **elasticsearch**: Elasticsearch 9.0.0 with security enabled.
- **kibana**: Kibana 9.0.0 configured to connect to Elasticsearch and the fake EPR.
- **real-epr**: A real Elastic Package Registry (EPR) using official Elastic EPR image
- **fake-epr**: A fake EPR using the `hashicorp/http-echo` image.

## Prerequisites

- Docker
- Docker Compose

## Usage

1. Clone this repository.
2. Start the services using Docker Compose:

   Start everything with one command:
   ```sh
   docker-compose up -d
   ```
   or
   ```
   docker-compose up
   ```


   or start some service individually:
   ```
   docker-compose up <service_name> -d
   ```

3. Shut down completely the services when done (this removes the container):

   ```sh
   docker-compose down
   ```

4. Restart the services if needed (keeping existing container):

   ```sh
   docker-compose restart
   ```
   or 
   ```
   docker-compose down && docker compose up -d
   ```

   4.1. Restart an individual service if needed:

      ```sh
      docker-compose restart <service_name>
      ```

   4.2. Alternatively, restart an individual service using the `docker` command:

      ```sh
      docker restart <container_name>
      ```

5. Embedded `service_token` for secure communication:

   - In Docker the authentication must be done with the service_token. The service_token is already stored in es-config/service_token file. It is mounted inside the elasticsearch container, so that ElasticSearch can see it and use it. The same value of a token is stored also in kibana.yml file. 
   - If you wish, you can cenerate a new service token using this command inside elasticsearch container: `bin/elasticsearch-service-tokens create elastic/kibana kibana-new-token`. Then add the token to the `kibana.yml` configuration file under the `elasticsearch.serviceAccountToken` field (only the value).

6. For testing EPR: ensure connectivity between Kibana and the EPR:

   - The `real-epr` and `fake-epr` services have preconfigured IP addresses.
   - Chose which service you want to use and set the appropriate IP in `kibana.yml` in `xpack.fleet.registryUrl` field.
   - If you want to change the IP to some different value then remember to update the `kibana.yml` file to use the same IP under the `xpack.fleet.registryUrl` field.

7. It is possible to further configure behavior of the hashicorp/http-echo. You can set the response message or the status code. Then restart the fake-epr service.