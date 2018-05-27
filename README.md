# docker-nexus-oss

Docker compose ready for Sonatype Nexus OSS. This will run Sonatype Nexus OSS on ___:8081___ and the compatible [Docker Registry V2 API](https://docs.docker.com/registry/spec/api/) on ___:5000___.

Extended from the official image:

- https://github.com/sonatype/docker-nexus3
- https://hub.docker.com/r/sonatype/nexus3

```
git clone https://github.com/victorskl/docker-nexus-oss.git
cd docker-nexus-oss

(Only required on Linux host)
mkdir nexus-data && sudo chown -R 200 ./nexus-data

docker-compose -p dev build
docker-compose -p dev up -d

(need to wait awhile on first time bootstrap)
docker logs -f nexus

      -------------------------------------------------
      
      Started Sonatype Nexus OSS 3.7.1-02
      
      -------------------------------------------------


curl -u admin:admin123 http://localhost:8081/service/metrics/ping

open -a "Google Chrome" http://localhost:8081
    admin:admin123
```

### Persistent Data

- Nexus data persist into `./nexus-data`. Create this directory if not present on Linux host. It will auto-create on macOS if absent.

### Make it Private Repo

- Admin > Security > Anonymous > Disable _Allow anonymous users to access the server_

### OSS Limitation

- Allow ___most___ component/package formats
- https://help.sonatype.com/repomanager3/repository-manager-feature-matrix
- https://help.sonatype.com/repomanager3/system-requirements

---

### Hosting as Maven Repository

- https://help.sonatype.com/display/NXRM3/Repository+Manager+3
- http://www.sonatype.org/nexus/2017/02/08/using-nexus-3-as-your-repository-part-1-maven-artifacts

### Hosting as Docker Registry

- create Blob store `docker-images`

- create repository > docker (hosted) > `test` > tick `HTTP` with port `5000` > select Blob store to `docker-images`
  
- Extended Registry UI - _This is optional_

  ```
  docker-compose -p dev down
  touch .env
  docker-compose -f docker-compose.yml -f registry-ui.yml -p dev up -d
  open -a "Google Chrome" http://localhost:8888
  ```

#### Working with Registry

- First, let's [Login](https://docs.docker.com/engine/reference/commandline/login/)
  ```
  docker login -u admin -p admin123 localhost:5000
  ```
  
- Pull image from docker hub
  ```
  docker pull alpine:latest
  ```

- Tag to make our custom `my-alpine` image published at `localhost:5000` - Read [Understanding image naming](https://docs.docker.com/registry/introduction/#understanding-image-naming)
  ```
  docker tag alpine:latest localhost:5000/my-alpine
  ```

- Now push this image to our registry
  ```
  docker push localhost:5000/my-alpine
  ```

- Delete local and test whether we can pull this image from our registry
  ```
  docker rmi localhost:5000/my-alpine
  docker pull localhost:5000/my-alpine
  ```
  
- Try [Docker Registry V2 API](https://docs.docker.com/registry/spec/api/)
  ```
  curl -u admin:admin123 localhost:5000/v2/_catalog
  curl -u admin:admin123 localhost:5000/v2/my-alpine/tags/list
  ```

#### Nexus Maintenance with Docker Registry

The following Nexus schedule tasks would take care blob store growth:
- Admin > System > Tasks > Create task > Purge incomplete docker uploads
- Admin > System > Tasks > Create task > Purge unused docker manifests and images
- Admin > System > Tasks > Create task > Purge unused components and assets
- Admin > System > Tasks > Create task > Compact blob store

For more custom maintenance:
- Admin > System > Tasks > Create task > [Execute script](https://stackoverflow.com/questions/40742766/purge-old-release-from-nexus-3)
- Nexus CLI for Docker Registry - https://github.com/mlabouardy/nexus-cli


#### Using HAProxy, Let's Encrypt, Basic Auth, SSL Termination

- Install HAProxy and adapt [haproxy.cfg](haproxy.cfg) to your environment; configure for 443 and cert location.

- Install [Certbot](https://letsencrypt.org); adjust haproxy config for TLS.

  ```
  mkdir -p /etc/haproxy/certs
  chmod 600 /etc/haproxy/certs
  
  certbot certonly --standalone --rsa-key-size 4096 --preferred-challenges http --http-01-port 10000 --noninteractive --agree-tos --email victorsankho.lin@email.address -d reg.my-domain.com
  certbot certificates
  cat /etc/letsencrypt/live/reg.my-domain.com/fullchain.pem /etc/letsencrypt/live/reg.my-domain.com/privkey.pem > /etc/haproxy/certs/reg.my-domain.com.pem
  ```

- Additional SSL env config for the extended Registry UI _if used_
  
  ```
  rm .env
  cp env.sample .env
  vi .env [change reg.my-domain.com to your FQDN]
  ``` 
  
#### REF:

  - https://stefanprodan.com/2016/docker-private-registry-nexus-nginx/
  - http://codeheaven.io/using-nexus-3-as-your-repository-part-1-maven-artifacts
  - http://codeheaven.io/using-nexus-3-as-your-repository-part-2-npm-packages
  - http://codeheaven.io/using-nexus-3-as-your-repository-part-3-docker-images

### Related

For hosting Docker Private Registry, you may just want the Docker Registry:

- https://github.com/victorskl/docker-private-registry