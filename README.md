# docker-nexus-oss

Docker compose ready for Sonatype Nexus OSS. Extended from:

- https://github.com/sonatype/docker-nexus3
- https://hub.docker.com/r/sonatype/nexus3

```
docker-compose --project-name=dev build
docker-compose --project-name=dev up -d

(need to wait awhile on first time bootstrap)
curl -u admin:admin123 http://localhost:8081/service/metrics/ping
open -a Safari http://localhost:8081
admin:admin123
```

Persistent Data

- Nexus data persist into `./nexus-data`. This directory will auto created if not present.
- On macOS, no special permission need to be set.
- However, on Linux, it is better to create this directory and give the ownership as follow:
    ```
    git clone https://github.com/victorskl/docker-nexus-oss.git
    cd docker-nexus-oss
    mkdir nexus-data && chown -R 200 ./nexus-data
    docker-compose --project-name=dev build
    docker-compose --project-name=dev up -d
    ```

OSS Limitation

- Allow ___all___ component/package formats
- https://www.sonatype.com/nexus-repository-oss

---

Hosting as Maven Repository

- https://help.sonatype.com/display/NXRM3/Repository+Manager+3
- http://www.sonatype.org/nexus/2017/02/08/using-nexus-3-as-your-repository-part-1-maven-artifacts

Hosting as Docker Registry

- https://stefanprodan.com/2016/docker-private-registry-nexus-nginx/
- http://codeheaven.io/using-nexus-3-as-your-repository-part-1-maven-artifacts
- http://codeheaven.io/using-nexus-3-as-your-repository-part-2-npm-packages
- http://codeheaven.io/using-nexus-3-as-your-repository-part-3-docker-images