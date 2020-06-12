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

## 用RequestMapping处理各种HTTP的方法
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

## 用 @RequestMapping 来处理生产和消费对象 


