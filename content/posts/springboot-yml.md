---
date: 2017-03-23
title: SpringBoot에서 외부 yml 파일 맵핑하여 사용하기
description: Springboot의 기본 application.yml 이외에 다른 yml파일을 생성하고 객체에 맵핑하여 사용해보자.
categories: ["springboot"]
tags: ["springboot", "yml"]
comments: true
---
**1. Bean 등록**
---
```
import org.springframework.beans.factory.config.YamlPropertiesFactoryBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.support.PropertySourcesPlaceholderConfigurer;
import org.springframework.core.io.ClassPathResource;
import org.springframework.stereotype.Component;

@Component
public class Config {
    @Bean
    public static PropertySourcesPlaceholderConfigurer properties() {
        PropertySourcesPlaceholderConfigurer propertySourcesPlaceholderConfigurer = new PropertySourcesPlaceholderConfigurer();
        YamlPropertiesFactoryBean yaml = new YamlPropertiesFactoryBean();
        yaml.setResources(new ClassPathResource("external-config.yml"));
        propertySourcesPlaceholderConfigurer.setProperties(yaml.getObject());
        return propertySourcesPlaceholderConfigurer;
    }
}
```
**2. POJO object**
---
```
import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.List;
import java.util.Map;

@Component
@ConfigurationProperties(prefix = "enterprise-info")
@Data
public class ExternalEnterprise {
    private String stringProp1;
    private String stringProp2;
    private Integer intProp1;
    private List<String> listProp;
    private Map<String, String> mapProp;
}
```
**3. external-config.yml**
---
```
enterprise-info:
  stringProp1: propValue1
  stringProp2: propValue2
  intProp1: 10
  listProp:
    - listValue1
    - listValue2
  mapProp:
    key1: mapValue1
    key2: mapValue2
```   
***Result***
```
ExternalEnterprise(stringProp1=propValue1, stringProp2=propValue2, intProp1=10, listProp=[listValue1, listValue2], mapProp={key2=mapValue2, key1=mapValue1})
```
위와 같이 오브젝트에 맵핑하여 프로퍼티 정보를 사용 할 수 있다.  
