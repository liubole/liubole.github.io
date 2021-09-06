
---
title: Docker环境变量
tags: [docker]
categories: [容器,docker]
toc: false
date: 2017-07-17 10:46:05
---

1. 通过执行docker-compose所在的shell环境注入
	```
	web:
	  image: "webapp:${TAG}"
	```
	
2. 通过文件注入
  2.1 默认使用.env中的变量
    ```
    $ cat .env
	TAG=v1.5
    ```
    
    ```
	$ cat docker-compose.yml
	version: '2.0'
	services:
	  web:
	    image: "webapp:${TAG}"
	```
	
  2.2 指定环境变量文件
	```
	//同docker run –env-file=web-variables.env
	//docker-compose -f web-variables.env
	web:
	  env_file:
		- web-variables.env
	```

3. 通过docker-compose.yml注入
	```
	//同docker run -e DEBUG=1
	web:
	  environment:
		- DEBUG=1
	```
	
	```
	//同docker run -e DEBUG
	web:
	  environment:
		- DEBUG //无赋值的环境变量
	```

4. 通过启动命令注入
	```
	docker run
		docker run -e DEBUG=1
		docker run -e DEBUG // VARIABLE从shell环境变量中获取，同第1条
	```
	
	```
	docker-compose run
		docker-compose run -e DEBUG=1 
		docker-compose run -e DEBUG // VARIABLE从shell环境变量中获取，同第1条
	```

5. 优先级从大到小
	```
	Shell 
	Compose file
	Environment file
	Dockerfile
	Variable is not defined
	```
