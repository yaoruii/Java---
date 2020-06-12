# [超详细 Spring @RequestMapping 注解使用技巧](https://juejin.im/entry/59bb7a8f5188256bd871dc15)

## 基本用法
可以在控制器的类级别或者在方法级别使用。

在类的级别上的注解会将一个特定请求或者请求模式映射到一个控制器之上。之后你还可以另外添加方法级别的注解来进一步指定到处理方法的映射关系。
```java
@RequestMapping("/home ")
public class IndexController {
    @RequestMapping("/ ")
    String get() {
        //mapped to hostname:port/home/
        return "Hello from get ";
    }
    @RequestMapping("/index ")
    String index() {
        //mapped to hostname:port/home/index/
        return "Hello from index ";
    }
}
```

**如果要处理多个URL，使用value属性，等于一个{}集合，`@RequestMapping(value = {
        " ",
        "/page ",
        "page* ",
        "view/*,**/msg "
    })`**
   
   
## 带有 @RequestParam 的 @RequestMapping
** @RequestParam有值：将值和方法的参数绑定**

id 这个请求参数被映射到了 thegetIdByValue() 这个处理方法的参数 personId 上。
```java
String getIdByValue(@RequestParam("id ") String personId) {
        System.out.println("ID is  " + personId);
        return "Get ID from query string of URL with value element ";
    }
```
**如果请求参数和方法参数名字相同，，@RequestParam 注解的 value 这个参数就可省掉了**
```java
 @RequestMapping(value = "/personId ")
    String getId(@RequestParam String personId) {
        System.out.println("ID is  " + personId);
        return "Get ID from query string of URL without value element ";
    }
```
**@RequestParam 注解的 required 这个参数定义了参数值是否是必须要传的**

```java
@RequestMapping("/home ")
public class IndexController {
    @RequestMapping(value = "/name ")
    String getName(@RequestParam(value = "person ", required = false) String personName) {
        return "Required element of request param ";
    }
}
```
如果是false，不要求必须传，所以，可以不传，这个时候以下两个URL都是可以的：`/home/name?person=xyz`和 `/home/name`

**如果，指定了defaultValue,那么当没传参数的时候，会获取默认的这个数**

## 用RequestMapping处理各种HTTP的方法：method属性
Spring MVC 的 @RequestMapping 注解能够处理 HTTP 请求的方法, 比如 GET, PUT, POST, DELETE 以及 PATCH。默认是GET方法。

为了能将一个请求映射到一个特定的方法，使用`method = RequestMethod.POST`语句。

```java
@RequestMapping("/home ")
public class IndexController {
    @RequestMapping(method = RequestMethod.GET)
    String get() {
        return "Hello from get ";
    }
    @RequestMapping(method = RequestMethod.DELETE)
    String delete() {
        return "Hello from delete ";
    }
    @RequestMapping(method = RequestMethod.POST)
    String post() {
        return "Hello from post ";
    }
    @RequestMapping(method = RequestMethod.PUT)
    String put() {
        return "Hello from put ";
    }
    @RequestMapping(method = RequestMethod.PATCH)
    String patch() {
        return "Hello from patch ";
    }
}
```
所有的处理方法会处理从这同一个 URL( /home)进来的请求, 但要看指定的 HTTP 方法是什么来决定用哪个方法来处理。

例如，一个 POST 类型的请求 /home 会交给 post() 方法来处理，而一个 DELETE 类型的请求 /home 则会由 delete() 方法来处理。

## 用 @RequestMapping 来处理生产和消费对象:Product/consume属性

## 使用 @RequestMapping 来处理消息头:header属性
`headers = {"content-type=text/plain " })`, 方法就只会处理到 /home/head 并且 content-typeheader 被指定为 text/plain 这个值的请求。

## 使用 @RequestMapping 来处理请求参数：params属性
```java
@RequestMapping("/home ")
public class IndexController {
    @RequestMapping(value = "/fetch ", params = {
        "personId=10 "
    })
    String getParams(@RequestParam("personId ") String id) {
        return "Fetched parameter using params attribute =  " + id;
    }
    @RequestMapping(value = "/fetch ", params = {
        "personId=20 "
    })
    String getParamsDifferent(@RequestParam("personId ") String id) {
        return "Fetched parameter using params attribute =  " + id;
    }
}
```
getParams() 和 getParamsDifferent() 两个方法都能处理相同的一个 URL (/home/fetch) ，但是会根据 params 元素的配置不同而决定具体来执行哪一个方法。

对于 localhost:8080/home/fetch?personId=20 这个URL, getParamsDifferent() 处理方法会得到执行，因为 id 值是 20。

## 使用 @RequestMapping 处理动态 URI：同 @PathVaraible 注解一起使用
用来处理动态的 URI，URI 的值可以作为控制器中处理方法的参数。按名匹配。

第一个方法：会在发起到 localhost:8080/home/fetch/10 的请求时执行。**这里 getDynamicUriValue() 方法 id 参数也会动态地被填充为 10 这个值。**
```java
@RestController
@RequestMapping("/home ")
public class IndexController {
    @RequestMapping(value = "/fetch/{id} ", method = RequestMethod.GET)
    String getDynamicUriValue(@PathVariable String id) {
        System.out.println("ID is  " + id);
        return "Dynamic URI parameter fetched ";
    }
    @RequestMapping(value = "/fetch/{id:[a-z]+}/{name} ", method = RequestMethod.GET)
    String getDynamicUriValueRegex(@PathVariable("name ") String name) {
        System.out.println("Name is  " + name);
        return "Dynamic URI parameter fetched using regex ";
    }
}
你也可以使用正则表达式来只处理可以匹配到正则表达式的动态 URI。方法 getDynamicUriValueRegex() 会在发起到 localhost:8080/home/fetch/category/shirt 的请求时执行。不过，如果发起的请求是 /home/fetch/10/shirt 的话，会抛出异常，因为这个URI并不能匹配正则表达式。
