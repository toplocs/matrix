# matrix
Repo for deployment of Matrix / Keycloak  behind traefik. 


Prerequisites: 
- docker and docker-compose installed. 

Deployment steps: 

1. Traefik Reverse Proxy.    

  ```console
   docker network create traefik-network 
  ```

  Inside the traefik folder use docker-compose up. 
2. Keycloak
Change Values in the docker-compose file in keycloak folder: 

```console
    KC_HOSTNAME: id.kawohl.net
    POSTGRES_PASSWORD: 
    KEYCLOAK_ADMIN_PASSWORD: thee3eeS
    - "traefik.http.routers.keycloak.rule=Host(`id.kawohl.net`)"
  ```

After successfully starting keycloak create a realm e.g. tribelike and in the realm create an oidc client "matrix"
copy the client secret and provide it later to matrix

3. Matrix
Change Values in the docker-compose file in Matrix folder:

```console
    POSTGRES_PASSWORD:
    - SYNAPSE_SERVER_NAME=matrix.kawohl.net
    - "traefik.http.routers.synapse.rule=Host(`matrix.kawohl.net`)"
    - "traefik.http.routers.element.rule=Host(`element.kawohl.net`)"
  ```

Then run:

```console
docker run -it --rm \
    --mount type=volume,src=synapse-data,dst=/data \
    -e SYNAPSE_SERVER_NAME=matrix.kawohl.net \
    -e SYNAPSE_REPORT_STATS=yes \
    matrixdotorg/synapse:latest generate
  ```

Make sure to set the hostname correct.

After that change the config file and add the contents of: 

