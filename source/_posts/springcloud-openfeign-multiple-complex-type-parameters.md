在使用Spring Cloud OpenFeign时，遇到了一个很烦人的问题，就是Feign无法支持多个复杂对象作为参数生成查询字符串的场景，而这样的场景无法避免，并且短期内看不到官方有意愿解决这一问题，因此，我研究一下为什么支持不了。
<!-- more -->

# 多个复杂参数接收场景

在Spring MVC中，我们采用多个复杂参数接收客户端请求是很常见的场景，尤其在查询分页场景中，这一需求尤为常见：

```java
@GetMapping("queryXXXInfo")
public IPage<XXX> queryXXXInfo(@ModelAttribute QueryDto query ,@ModelAttribute  PageCondition page){
    //TODO
} 
```

# 现有解决方案

但是很可惜，在`Spring Cloud OpenFeign`中，如果你直接使用这一方法签名，将无法按预期生成QueryString。首先，`Spring Cloud OpenFeign`不支持`@ModelAttribute`注解，但是Spring Cloud提供了`@SpringQueryMap`注解，`OpenFeign`提供了`@QueryMap`注解。可惜，这两个注解都不支持多个复杂参数的场景，如果你使用了这个注解，仅仅会将第一个打了该注解的参数编码为对应的QueryString。

`Spring Cloud OpenFeign`针对这一场景做了一个补丁，通过引入`spring data`中的`spring-data-commons`组件，引入`Pageable`接口用以支持分页场景。

# 现有方案的问题
但是这一方案适应范围极端狭窄，仅仅增加了分页场景支持，并且需要分页参数实现`Pageable`接口。如果需要增加其他复杂类型则该方案完全无法支持。另外，如果使用`Pageable`接口添加分页相关参数，需要着重注意：page参数 **不能** 放在前方并加上`@SpringQueryMap`注解。否则只会将分页参数进行编码。

# 解决方案
通过查看`Spring Cloud OpenFeign`相关代码，在以下文件中找到了`Pageable`相关实现

> org.springframework.cloud.openfeign.support.PageableSpringEncoder

如果跟踪相关代码，可以发现在Feign的相关处理流程中，是把Pageable参数当作了body参数进行处理的，再在body编码中，做了花活将page相关参数放到了QueryString当中。

# 吐槽时间
## 为什么难以扩展

我们看一下QueryString具体是如何生成的？

在OpenFeign中，我们需要关注描述方法元数据的类`feign.MethodMetadata`。
```java
public final class MethodMetadata implements Serializable {
    //...
    private Integer queryMapIndex;
    //...
    public Integer queryMapIndex() {
        return queryMapIndex;
    }
    //...
    public MethodMetadata queryMapIndex(Integer queryMapIndex) {
        this.queryMapIndex = queryMapIndex;
        return this;
    }
    //...
}
```
这个类中，`queryMapIndex`记录了QueryMap参数所在的方法参数索引，问题的根子就在这，用以储存索引的类型是`Integer`，Feign认为，表示查询字符串的参数至多只能有一个~让人更加绝望的是，这个类是一个`final`类，无法扩展修改。

同时，进行具体解析操作的类：`feign.RequestTemplateFactoryResolver`我们看一下是怎么操作的：
```java
final class RequestTemplateFactoryResolver{
    //...
        private static class BuildTemplateByResolvingArgs implements RequestTemplate.Factory {
            //...
            @Override
            public RequestTemplate create(Object[] argv) {
                RequestTemplate template = resolve(argv, mutable, varBuilder);
                if (metadata.queryMapIndex() != null) {
                    // add query map parameters after initial resolve so that they take
                    // precedence over any predefined values
                    Object value = argv[metadata.queryMapIndex()];
                    Map<String, Object> queryMap = toQueryMap(value);
                    template = addQueryMapQueryParameters(queryMap, template);
                }
            }
            //...
        }
      
      //...
}
```

可以看到这个私有内部类通过`queryMapIndex`获取到QueryString参数，进行解析为`Map<String, Object>`对象，然后提供给后续操作。很明显，这个类也无法扩展。
至此，我们已经断定，OpenFeign在设计上，就拒绝了多个复杂参数共同生成QueryString的行为。

## 社区的声音

通过在SpringCloud-OpenFeign项目的issue和OpenFeign项目的issue中可以看到，有这个困惑的人不止我一个。尤其作为Spring Cloud的一等RPC框架，这个设计和Spring MVC不统一，是其一个巨大的槽点。

[例如这里](https://github.com/spring-cloud/spring-cloud-openfeign/issues/753)

## 补丁真丑

为了解决这一问题，Spring Cloud引入了`Pageable`进行参数传递，但是这一方式带来了几个显而易见的问题：

1、本质上来说，它占用了Body的位置，当然，你可以说需要分页查询的都应该用GET请求，GET请求是不应该有body的。但是如果我用的使用就是POST，并且POST请求里既有复杂查询字符串又有body怎么办？所有API都用POST的团队不要太多哦，很多人还当作了最佳实践。

2、其表现与Spring MVC不同，尤其是使用了Spring MVC协定的开发而言，带来了无穷无尽的困惑。

3、`@SpringQueryMap`注解的实现让人困惑，我上文说到，这个注解不能打在`Pageable`参数上，如果你将`Pageable`参数放在前面，并且打上了这个注解，它会占用另外参数的index，造成另外参数无法被序列化为QueryString，而这个限制的描述在文档中我没找到，并且很反直觉，至少我看到这个注解我的第一反应就是所有需要序列化的参数都打上。

4、我真的不想用`Spring data`的`Pageable`啊，我有我自己团队约定的Page参数定义啊~

## Feign的扩展性问题

我个人其实很喜欢Feign这个库，当年第一次接触到就让我感到眼前一亮。优雅清晰的HTTP调用构造，让我一眼就爱上了他。但是在长期的使用过程中，Feign的扩展性问题却多次影响到了我。

上一次映像深刻的是在进行响应Decode时我期望获取到方法元数据，但是无法获取`MethodMetadata`。我当时的需求是，在对接一个三方API时，它返回了一个层级很深的JSON，但是我只需要其中很少的一点点信息，我希望在定义方法时，同时通过自定义注解定义一个JSONPATH，可惜当时我实在找不到在哪里能够取到我这个自定义注解及JSONPATH。

我那时一度想扩展Decode的上层方法，将MetaData存储在`ThreadLocal`中也行，然后就找到了
```java

final class SynchronousMethodHandler implements MethodHandler {
    ...
}
```
真的..哪哪都是`final`。

一直到2019年12月22日才终于在

```java

public Object decode(final Response response, Type type) throws IOException, FeignException {
		 response.request().requestTemplate().methodMetadata()
}

```

如此深渊般的地方将`MethodMetadata`暴露出来。至截稿日期（2023年11月27日），该属性还有`@Experimental`注解，姑且认为OpenFeign团队的实验很严谨吧。

# 总结
至截稿日期为止（2023年11月27日）我只能分析出为什么Feign无法支持将多个复杂参数解析为查询字符串的原因，实在是**无法提供很好的解决方案用以解决这个问题**。但是如果你真的遇到这个需求，又实在很想解决的漂亮那么一丢丢，那么我给你两个也不咋样的方案：

1、使用Map传参吧，想怎么传就怎么传，学习PHP大法。

2、学习Pageable的解决方法，定义一个自己的接口或者注解，自定义一个feign encoder，再在里面向进行处理，向queryMap中加内容。参考代码如下：

```java
    @Override
	public Map<String, Object> encode(Object object) {
		if (supports(object)) {
			Map<String, Object> queryMap = new HashMap<>();

			if (object instanceof Pageable pageable) {

				if (pageable.isPaged()) {
					queryMap.put(pageParameter, pageable.getPageNumber());
					queryMap.put(sizeParameter, pageable.getPageSize());
				}

				if (pageable.getSort() != null) {
					applySort(queryMap, pageable.getSort());
				}
			}
			else if (object instanceof Sort sort) {
				applySort(queryMap, sort);
			}
			return queryMap;
		}
		else {
			return super.encode(object);
		}
	}
```