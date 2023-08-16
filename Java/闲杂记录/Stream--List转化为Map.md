# 1、**JDK1.8流方式**
**用户类**
```
import lombok.Data;

@Data
public class User {

    private String id;
    private String name;

    public User(String id, String name) {
        this.id = id;
        this.name = name;
    }
}
```

## 1. **收集成key为id，value为name：**
```
        List<User> userList = new ArrayList<>();
        User user1 = new User("1", "name1");
        User user2 = new User("2", "name2");
        User user3 = new User("3", "name3");
        userList.add(user1);
        userList.add(user2);
        userList.add(user3);

        Map<String, String> collect = userList.stream().collect(Collectors.toMap(User::getId, User::getName));

        for (Map.Entry<String, String> next : collect.entrySet()) {
            System.out.println("key: " + next.getKey() + " , " + "value: " + next.getValue());
        }
```
![2022-03-31_181249.png](https://img-blog.csdnimg.cn/img_convert/b6bfd935743315c30109af7268b95524.png)


## **2. 收集成实体本身**
```
        List<User> userList = new ArrayList<>();
        User user1 = new User("1", "name1");
        User user2 = new User("2", "name2");
        User user3 = new User("3", "name3");
        userList.add(user1);
        userList.add(user2);
        userList.add(user3);

        Map<String, User> collect = userList.stream().collect(Collectors.toMap(User::getId, user -> user));

        for (Map.Entry<String, User> next : collect.entrySet()) {
            System.out.println("key: " + next.getKey() + " , " + "value: " + next.getValue());
        }
```
![222.png](https://img-blog.csdnimg.cn/img_convert/379493d2a6b6980d723237f0da4e2dab.png)

user -> user是一个返回本身的lambda表达式，其实还可以使用Function接口中的一个默认方法代替，使整个方法更简洁优雅：
```
Map<String, User> collect1 = userList.stream().collect(Collectors.toMap(User::getId, Function.identity()));
```

**重复key的情况：**
代码如下：
```
Map<String, User> collect2 = userList.stream().collect(Collectors.toMap(User::getName, Function.identity());
```
这个方法可能报错（java.lang.IllegalStateException: Duplicate key），因为name是有可能重复的。
![333.png](https://img-blog.csdnimg.cn/img_convert/6de2c3f0320d1e111654b22be3d8e094.png)

**toMap有个重载方法，可以传入一个合并的函数来解决key冲突问题：**
```
        List<User> userList = new ArrayList<>();
        User user1 = new User("1", "name2");
        User user2 = new User("2", "name2");
        User user3 = new User("3", "name3");
        userList.add(user1);
        userList.add(user2);
        userList.add(user3);

        Map<String, User> collect = userList.stream().collect(Collectors.toMap(User::getName, Function.identity(), (key1, key2) -> key2));

        for (Map.Entry<String, User> next : collect.entrySet()) {
            System.out.println("key: " + next.getKey() + " , " + "value: " + next.getValue());
        }
```
![44444.png](https://img-blog.csdnimg.cn/img_convert/177e1138116a3889c58050b30a72eeef.png)

**这里只是简单的使用后者覆盖前者来解决key重复问题。还有一种分组的方法：**
```
        List<User> userList = new ArrayList<>();
        User user1 = new User("1", "name2");
        User user2 = new User("2", "name2");
        User user3 = new User("3", "name3");
        userList.add(user1);
        userList.add(user2);
        userList.add(user3);

        Map<String, List<User>> collect3 = userList.stream().collect(Collectors.groupingBy(User::getName));

        for (Map.Entry<String, List<User>> next : collect3.entrySet()) {
            System.out.println("key: " + next.getKey() + " , " + "value: " + next.getValue());
        }
```
![555.png](https://img-blog.csdnimg.cn/img_convert/73bc96c4c083d317cf0485935109704d.png)

## **3. 指定具体收集的map**
toMap还有另一个重载方法，可以指定一个Map的具体实现，来收集数据：
```
        List<User> userList = new ArrayList<>();
        User user1 = new User("1", "name2");
        User user2 = new User("2", "name2");
        User user3 = new User("3", "name3");
        userList.add(user1);
        userList.add(user2);
        userList.add(user3);

        Map<String, User> collect3 = userList.stream().collect(Collectors.toMap(User::getName, Function.identity(), (key1, key2) -> key2, LinkedHashMap::new));

        for (Map.Entry<String, User> next : collect3.entrySet()) {
            System.out.println("key: " + next.getKey() + " , " + "value: " + next.getValue());
        }
```
![666.png](https://img-blog.csdnimg.cn/img_convert/de7ae048a0860d8bf24a6ac6c2d068f2.png)

