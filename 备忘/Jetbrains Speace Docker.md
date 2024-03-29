## Document

https://www.jetbrains.com/help/space/proof-of-concept-installation.html

## Jetbrains Speace Docker

https://assets.on-premises.service.jetbrains.space/

## 2022-12-15

```yml
version: '3.8'

services:Jetbrains Speace Docker
  init-configs:
    image: 'public.registry.jetbrains.space/p/space-on-premises/docker/init-configs:2022.1.0-BETA'
    volumes:
      - config:/home/init-config/config
    environment:
      AUTOMATION_TAG: '2022.1.0-BETA.1596'
  space:
    image: 'public.registry.jetbrains.space/p/space-on-premises/docker/space:2022.1.0-BETA'
    volumes:
      - config:/home/space/circlet-server-onprem/config
    environment:
      JAVA_OPTS: '-Dconfig.file=/home/space/circlet-server-onprem/config/space.on-premises.conf'
    depends_on:
      init-configs:
        condition: service_completed_successfully
      vcs:
        condition: service_started
      redis:
        condition: service_started
      postgres:
        condition: service_started
      minio:
        condition: service_started
      elasticsearch:
        condition: service_healthy
    ports:
      - '8084:8084'
    networks:
      - 'frontend'
      - 'backend-apps'
      - 'backend-data'

  vcs:
    image: 'public.registry.jetbrains.space/p/space-on-premises/docker/vcs-hosting:2022.1.0-BETA'
    volumes:
      - config:/home/space/git/vcs-hosting/config
    environment:
      JAVA_OPTS: '-Dproperties.file=config/vcs.on-premises.properties'
    depends_on:
      init-configs:
        condition: service_completed_successfully
      redis:
        condition: service_started
      postgres:
        condition: service_started
      minio:
        condition: service_started
      elasticsearch:
        condition: service_healthy
    ports:
      - '2222:2222'
      - '8080:8080'
    networks:
      - 'frontend'
      - 'backend-apps'
      - 'backend-data'

  packages:
    image: 'public.registry.jetbrains.space/p/space-on-premises/docker/packages:2022.1.0-BETA'
    volumes:
      - config:/home/space/packages-server/config
    environment:
      JAVA_OPTS: '-Dconfig.file=/home/space/packages-server/config/packages.on-premises.conf'
    depends_on:
      init-configs:
        condition: service_completed_successfully
      redis:
        condition: service_started
      postgres:
        condition: service_started
      minio:
        condition: service_started
      elasticsearch:
        condition: service_healthy
    ports:
      - '8390:8390'
      - '9390:9390'
    networks:
      - 'frontend'
      - 'backend-apps'
      - 'backend-data'

  langservice:
    image: 'public.registry.jetbrains.space/p/space-on-premises/docker/langservice:2022.1.0-BETA'
    volumes:
      - config:/home/space/langservice-server/config
    environment:
      JAVA_OPTS: '-Dconfig.file=/home/space/langservice-server/config/langservice.on-premises.conf'
    depends_on:
      init-configs:
        condition: service_completed_successfully
    ports:
      - '8095'
    networks:
      - 'backend-apps'

  postgres:
    image: 'postgres:12.2'
    volumes:
      - db_data:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: space
      POSTGRES_PASSWORD: spacepassword
      POSTGRES_DB: spacedb
    ports:
      - '5432'
    networks:
      - 'backend-data'

  elasticsearch:
    image: 'elasticsearch:7.17.4'
    volumes:
      - elasticsearch_data:/usr/share/elasticsearch/data
    environment:
      ES_JAVA_OPTS: -Xms512m -Xmx1024m
      discovery.type: single-node
      xpack.security.enabled: 'false'
    ports:
      - '9200'
      - '9300'
    healthcheck:
      test: ['CMD', 'curl', '-f', 'http://localhost:9200/_cat/health']
      interval: 5s
      timeout: 5s
      retries: 10
      start_period: 20s
    networks:
      - 'backend-data'

  redis:
    image: 'redis:7.0.2-alpine'
    ports:
      - '6379'
    networks:
      - 'backend-data'

  minio:
    image: minio/minio:RELEASE.2021-09-09T21-37-07Z
    volumes:
      - minio_data:/data
    environment:
      MINIO_ROOT_USER: space-access-key
      MINIO_ROOT_PASSWORD: space-secret-key
    command: server --address :9000 --console-address :9001 --compat /data
    ports:
      - '9000:9000'
      - '9001'
    networks:
      - 'backend-data'

volumes:
  elasticsearch_data:
    driver_opts:
      devices: './elasticsearch_data'
  db_data:
    driver_opts:
      devices: './db_data'
  minio_data:
    driver_opts:
      devices: './minio_data'
  config:
    driver_opts:
      devices: './config'

networks:
  frontend:
    enable_ipv6: true
    driver: bridge
    driver_opts:
      com.docker.network.enable_ipv6: 'true'
    ipam:
      driver: default
      config:
        - subnet: 2002:8000:8000::/64
          gateway: 2002:8000:8000::1
  backend-apps:
    enable_ipv6: true
    driver: bridge
    driver_opts:
      com.docker.network.enable_ipv6: 'true'
    ipam:
      driver: default
      config:
        - subnet: 2002:8000:8001::/64
          gateway: 2002:8000:8001::1
  backend-data:
    enable_ipv6: true
    driver: bridge
    driver_opts:
      com.docker.network.enable_ipv6: 'true'
    ipam:
      driver: default
      config:
        - subnet: 2002:8000:8002::/64
          gateway: 2002:8000:8002::1
```

Open your Space On-Premises instance in a browser. The default URL is http://127.0.0.1:8084. The default administrator username is admin, the password is admin.

Space components are accessible on the following URLs:

http://127.0.0.1:8084: Space user interface.

http://127.0.0.1:8080: VCS API.

ssh://127.0.0.1:2222: VCS SSH.

http://127.0.0.1:8390: Packages API.