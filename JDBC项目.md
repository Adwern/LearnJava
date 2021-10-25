# 第一讲

工具包里先写Dbutil类，数据库连接，关闭方法，getCon，closeCon

lass.forName(xxx.xx.xx);的作用是要求JVM查找并加载指定的类，也就是说JVM会执行该类的静态代码段

model或者pojo包，只用来存放User类，getset

dao包，login方法，sql语句，select User的getpassword等，最后return一个resultUser，如果失败就return的是null。导入的user参数可能是错的，所以返回的user是再new出来的

view层，可以说是业务层？实例化con，getCon，实例化userDao，调用login

# 第二讲

