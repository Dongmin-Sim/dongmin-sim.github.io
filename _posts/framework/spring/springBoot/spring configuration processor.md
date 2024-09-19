---
title: spring configuration processor
author: codongmin
date: 2024-03-17T22:16:00
categories: 
tags:
  - build
  - ci/cd
image: 
path: /assets/img/thumbnail/gradle.png
lqip: 
alt: Gradle이란?
---

  
## 들어가며


- application.properties/.yml 파일에 넣는 커스텀 설정의 자동 완성, 도움말등을 지원해주는 서비스 
- spring 설정에는 없는 사용자 설정에 대한 자동완성, 도움말

```properties
spring.application.name=springbootpractice  
server.port=9000
management.server.port=9001  
management.server.address=127.0.0.1  
my.height=170
```

- 커스텀 설정에 대한 내용을 추가하기 위해선 정해진  path와 Json 형식을 따른다. 
	- META_INF 디렉토리에 
	- `additional-spring-configuration-metadata.json`

```json
{  
  "properties": [{  
    "name": "my.height",  
    "type": "java.lang.Integer"  
  }],  
  "hints": [  
    {  
      "name": "my.height",  
      "values": [{  
        "value": 190,  
        "description": "큰 키."  
      }]  
    }  
  ]  
}
```


build.gradle에는 다음을 추가해주어야 한다. 
```gradle
compileJava {  
    inputs.files(processResources)  
}
```