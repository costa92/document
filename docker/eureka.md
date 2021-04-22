# eureka部署

> https://www.jianshu.com/p/db6296643759





```shell
docker pull sovandocker/eureka-server:2.0.0

docker run --rm --name eureka  -p 8761:8761 sovandocker/eureka-server:2.0.0
```

```yaml
version: "3.5"

services:
  eureka:
    container_name: docker-eureka
    image: sovandocker/eureka-server:2.0.0
    restart: always
    privileged: true
    environment:
      TZ: ${TZ}
    ports:
      - "8761:8761"
    networks:
      - default

networks:
  default:
    driver: bridge


```

访问 

http://192.168.33.50:8761/

![image-20210324114910820](../asset/euerka/image-20210324114910820.png)