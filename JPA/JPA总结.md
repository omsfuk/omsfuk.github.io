> # JPA 总结
> ####  -------- 基于Spring Data JPA + Hibernate

## 概述

ORM决定了JPA的操作粒度是对象，这既是优势也是劣势。

优势在于，以面向对象的观点来解决持久化问题，简化了持久化API模型。

其劣势在于，也正因为面向对象的粗粒度的操作，导致了效率上的弱势。

## 应用场景
后台管理系统，OA等对效率要求不高的场景

## 坑
#### 双边关系中，mapedBy只能存在一边
即一对`ManyToMany`中，有且只有其中一方带有mappedBy（MDZZ，竟然要分主从，不科学）

#### 延迟抓取会利用同一个session，提早关闭session会出问题
具体的表现是调用实现JpaRepository的Bean的方法获取的对象，当延迟抓取集合属性时，回去出现`no session`异常
解决方案是为每一次请求都只分配一个EntityManager。已经由`openEntityManagerInViewFilter`实现，只需要将它声明到`web.xml`里就好了

## 最佳实践

#### 序列化对象时，禁止序列化任何`*ToMany`属性。
```
@Entity
class Human {

	@Id
	@GenerateValue
	public Integer id;
	...
	@OneToMany
	public List<Human> children;
}
```
当序列化**整个Human对象**时，一定要略过`children`属性。
对于需要序列化`children`的情况，可以单独的为它写一个接口，然后返回的数据只包括`children`。
例如构建Restful应用时，可以在`children`字段上加`@JsonIgnore`注解（以jackson为例），然后为获取children单独提供一个接口。
