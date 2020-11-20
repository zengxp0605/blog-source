---
title: Docker Compose
comments: true
date: 2020-09-31 22:36
categories: [Docker]
tags: [Docker]
---

## 简单实例：python+redis

app.py
```python
from flask import Flask
from redis import Redis

app = Flask(__name__)
redis = Redis(host='redis', port=6379)

@app.route('/')
def hello():
    count = redis.incr('hits')
    return 'Hello World! 该页面已被访问 {} 次。\n'.format(count)

if __name__ == "__main__":
    app.run(host="0.0.0.0", debug=True)
```

Dockerfile
```sh
FROM python:3.9.0-alpine
ADD . /code
WORKDIR /code
RUN pip install redis flask
CMD ["python", "app.py"]
```

docker-compose.yml
```yaml
version: '3'
services:

  web:
    build: .
    ports:
     - "5000:5000"

  redis:
    image: "redis:alpine"
```

启动容器
```sh
 docker-compose up -d
```

访问 `localhost:5000` 可以看到访问次数增加

<!-- more -->

停止并删除容器
```sh
 docker-compose down
```

## docker-compose.yml增加配置

docker-compose.yml
```yaml
version: '3'

networks:
  frontend:
  backend:

services:

  web:
    build: .
    networks:
        - frontend
        - backend
    ports:
     - "5000:5000"

  redis:
    image: "redis:alpine"
    networks:
      - backend
    volumes:
      - /opt/docker/redis/redis.conf:/etc/redis/redis.conf:ro
      - /opt/docker/redis/data:/data
    command: ["redis-server", "/etc/redis/redis.conf"]
    ports:
      - "6379:6379"
```



## 参考
> <https://yeasy.gitbook.io/docker_practice/compose/introduction>
> <https://juejin.cn/post/6844903976534540296>
