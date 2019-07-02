#### 对Objects类的一些方法进行介绍：

* _equals(Object, Object): boolean_
    
可以将*if(a != null && a.equals(b))*这样的代码简化为：_if(Objects.equals(a, b))_

* _deepEquals(Object, Object): boolean_

考虑这样的情景，我们要判断两个List中元素是否一致，我们可能会这样写：
```java
public boolean equalsList(List<String> list1, List<String> list2) {
    int length;
    if (list1 != null && list2 != null && (length = list1.size()) == list2.size()) {
        for (int i = 0; i < length; i++) {
            String l1ei = list1.get(i);
            String l2ei = list2.get(i);
            if (!Objects.equals(l1ei, l2ei)) {
                return false;
            }
        }
        return true;
    }
    return false;
}
```
可以考虑将代码简化为： _Objects.deepEquals(list1, list2)_, 同时，如果是list中的元素仍然是list的情况，这个方法也是可以迭代判断的。

* _hashCode(Object): int_

即: _return o != null ? o.hashCode() : 0;_

* _isNull(Object): boolean_

即： _return obj == null;_

* _nonNull(Object): boolean_

即： _return obj != null;_
isNull 和 nonNull在stream中可能会用到，考虑以下场景： 求 *List<String> list*中的非null 元素，可以用以下代码实现：

```java
List<String> collect = list1.stream().filter(Objects::nonNull).collect(Collectors.toList());
```

* _requireNonNull(T): T_

假如系统中有这样一个类：
```java
class XxxServiceClient {
    private String serviceUrl;
    private String password;

    public XxxServiceClient() {
    }

    public XxxServiceClient(String serviceUrl, String password) {
        this.serviceUrl = serviceUrl;
        this.password = password;
    }

    public String getServiceUrl() {
        return serviceUrl;
    }

    public void setServiceUrl(String serviceUrl) {
        this.serviceUrl = serviceUrl;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}
```
系统启动的时候会创建一个这样的对象提供服务， 我们需要在创建这个对象的时候 *serviceUrl*是应当是存在的。那可以这样调用构造器和set方法：
```java
String serviceUrl = getServiceUrlFromXxx();
String password = getPasswordFromXxx();
XxxServiceClient client = new XxxServiceClient(Objects.requireNonNull(serviceUrl), password);
client.setServiceUrl(Objects.requireNonNull(serviceUrl));
```
当然，另一种限制是直接在类的构造器和set方法上进行限制
