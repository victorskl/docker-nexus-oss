# docker-nexus-oss

Docker compose ready for Sonatype Nexus OSS. Extended from:

- https://github.com/sonatype/docker-nexus3
- https://hub.docker.com/r/sonatype/nexus3

```
docker-compose --project-name=dev build
docker-compose --project-name=dev up -d

(need to wait awhile on first time bootstrap)
curl -u admin:admin123 http://localhost:8082/service/metrics/ping
open -a Safari http://localhost:8082
admin:admin123
```

OSS Limitation

- Allow ___all___ component/package formats
- https://www.sonatype.com/nexus-repository-oss


Documentation

- https://help.sonatype.com/display/NXRM3/Repository+Manager+3
- http://www.sonatype.org/nexus/2017/02/08/using-nexus-3-as-your-repository-part-1-maven-artifacts
- http://codeheaven.io/using-nexus-3-as-your-repository-part-1-maven-artifacts
- http://codeheaven.io/using-nexus-3-as-your-repository-part-2-npm-packages
- http://codeheaven.io/using-nexus-3-as-your-repository-part-3-docker-images