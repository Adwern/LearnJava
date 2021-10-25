# Spring

## IoC概念

- UserDao接口
- UserDao实现类
- UserService业务接口
- UserServiceImpl业务实现类

```java
@Test //用户只能调用业务层，通过set方法，即可掌握所有权
public void test(){ 
    UserServiceImpl service = new UserServiceImpl(); 			   
    service.setUserDao( new UserDaoMySqlImpl() ); 
    service.getUser(); //那我们现在又想用Oracle去实现呢
    service.setUserDao( new UserDaoOracleImpl() );   
    service.getUser();
}
```

获得依赖的方式反转了，不用在serviceImpl写死

IoC是Spring框架的核心内容，使用xml或者注解