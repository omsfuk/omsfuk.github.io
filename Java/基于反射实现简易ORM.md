-----------------------
title: 基于反射实现简易ORM
tags: 反射,java
-----------------------
# 简易ORM
## 简介
利用java反射，可以实现一个简易的orm，从此再也不用傻傻的写dao了。。。

## 目标
实现查询和插入方法。其中查询方法需要接受一个POJO对象，DBUtil会自动根据POJO中已经提供的信息补全sql。例如User对象中已经设置了`username`属性为`omsfuk`，则sql会变成：`select * from user where username = 'omsfuk'`。插入方法类似。


## 代码
Talk is chaper， show me the code
注释还是很详细的

先上一段建表语句
```
drop database ormtest;

create database ormtest;

use ormtest;

create table novel
(
    novelid char(36) PRIMARY  KEY ,
    novelName varchar(20) UNIQUE,
    currChap varchar(50),
    rectChap varchar(50),
    url varchar(100) NOT NULL,
    encoding varchar(10) DEFAULT 'UTF-8'
);

create table user
(
    userid char(36) PRIMARY KEY,
    password varchar(20),
    username varchar(20) UNIQUE,
    nickname varchar(20) NOT NULL ,
    email varchar(30) NOT NULL
);
```

再来一个User类
```
package cn.omsfuk.domain;

/**
 * Created by Administrator on 2017/4/8.
 */
public class User {

    private String userid;

    private String username;

    private String nickname;

    private String password;

    private String email;

    public String getUserid() {
        return userid;
    }

    public void setUserid(String userid) {
        this.userid = userid;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getNickname() {
        return nickname;
    }

    public void setNickname(String nickname) {
        this.nickname = nickname;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    @Override
    public String toString() {
        return userid + " " + username + " " + password + " " + nickname + " " + email;
    }
}
```

正主DBUtil类
```
package cn.omsfuk.simpleorm;

import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;
import java.sql.*;
import java.util.LinkedList;
import java.util.List;

/**
 * Created by omsfuk on 2017/4/8.
 */
public class DBUtil {

    private static Connection getConn() {
        String url = "jdbc:mysql://localhost:3306/ormtest?useUnicode=true&characterEncoding=utf-8&useSSL=false&user=root&password=root";
        Connection conn = null;
        try {
            Class.forName("com.mysql.jdbc.Driver");
            conn = DriverManager.getConnection(url);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return conn;
    }

    private static void close(AutoCloseable closeable) {
        try {
            if(closeable != null) {
                closeable.close();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        closeable = null;
    }

    /**
     * 查询。默认返回结果集合。需要用List接收。会自动根据这个对象里已有的信息自动补全sql语句。
     * @param obj 要查询的对象。
     * @return 结果List
     */
    public static List<Object> getObject(Object obj) {
        Class cls = obj.getClass();
        Method[] methods = cls.getMethods();

        String sql = "select * from " + cls.getSimpleName() + " where ";
        Field[] fields = cls.getDeclaredFields();

        for(int i = 0; i < fields.length; i++) {
            // 方法返回值类型
            Class returnType = null;
            // 返回值
            Object returnValue = null;
            try {
                // 获得get方法
                Method method = cls.getMethod("get" + (char)(fields[i].getName().charAt(0) - 32) + fields[i].getName().substring(1));
                returnType = method.getReturnType();
                // 调用方法，得到属性值
                returnValue = method.invoke(obj);

                // 补充sql语句
                if(returnValue != null) {
                    if(returnType == String.class) {
                        sql += fields[i].getName() + "='" + returnType.cast(returnValue) + "' and ";
                    } else {
                        sql += fields[i].getName() + "=" + returnType.cast(returnValue) + " and ";
                    }
                }
            } catch (NoSuchMethodException e) {
                e.printStackTrace();
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            } catch (InvocationTargetException e) {
                e.printStackTrace();
            }
        }
        // 把最后一个and去掉
        if(sql.endsWith("and ")) {
            sql = sql.substring(0, sql.length() - 4);
        }

        // 处理查询
        List<Object> result = new LinkedList<Object>();
        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        try {
            conn = getConn();
            stmt = conn.createStatement();
            rs = stmt.executeQuery(sql);

            while(rs.next()) {
                // 先创建一个实例对象，用来保存这一条记录
                Object res = cls.newInstance();
                for(int i = 0; i < fields.length; i++) {
                    Field field = fields[i];
                    try {
                        // 获取set方法，用来给属性赋值
                        Method method = cls.getMethod("set" + (char)(field.getName().charAt(0) - 32) + field.getName().substring(1),
                                field.getType());

                        // 调用方法，赋值
                        if(fields[i].getType() == String.class) {
                            method.invoke(res, rs.getString(field.getName()));
                        } else {
                            method.invoke(res, rs.getInt(field.getName()));
                        }
                    } catch (NoSuchMethodException e) {
                        e.printStackTrace();
                    } catch (InvocationTargetException e) {
                        e.printStackTrace();
                    }
                }

                // 将本条记录对应的对象添加到结果集中
                result.add(res);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } finally {
            close(rs);
            close(stmt);
            close(conn);
        }

        return result;

    }

    /**
     * 插入新记录
     * @param obj 要插入的对象，如果是uuid，请将setUserid之类的设置主键id的方法的内容设置为UUID()。如user.setUserid("UUID()");
     * @return 是否插入成功
     */
    public static boolean insertObject(Object obj) {
        Class cls = obj.getClass();
        Method[] methods = cls.getMethods();

        String sql = "insert " + cls.getSimpleName() + "(";


        Field[] fields = cls.getDeclaredFields();

        for (int i = 0; i < fields.length; i++) {
            sql += fields[i].getName() + ", ";
        }

        sql = sql.substring(0, sql.length() - 2) + ") values(";

        for (int i = 0; i < fields.length; i++) {
            // 方法返回值类型
            Class returnType = null;
            // 返回值
            Object returnValue = null;
            try {
                Method method = cls.getMethod("get" + (char)(fields[i].getName().charAt(0) - 32) + fields[i].getName().substring(1));
                returnType = method.getReturnType();
                returnValue = method.invoke(obj);
                if(returnValue != null) {
                    if(returnType.cast(returnValue).toString().toLowerCase().indexOf("uuid") != -1) {
                        sql += returnType.cast(returnValue) + ", ";
                    } else if(returnType == String.class) {
                        sql += "'" + returnType.cast(returnValue) + "', ";
                    } else {
                        sql += returnType.cast(returnValue) + ", ";
                    }
                }

            } catch (NoSuchMethodException e) {
                e.printStackTrace();
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            } catch (InvocationTargetException e) {
                e.printStackTrace();
            }
        }
        sql = sql.substring(0, sql.length() - 2) + ")";

        Connection conn = null;
        Statement stmt = null;

        boolean result = false;
        try {
            conn = getConn();
            stmt = conn.createStatement();
            if(stmt.executeUpdate(sql) == 1) {
                result = true;
            }
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            close(stmt);
            close(conn);
        }

        return result;
    }

}

```

再来一个测试类
```
package cn.omsfuk;

import cn.omsfuk.domain.User;
import cn.omsfuk.simpleorm.DBUtil;
import org.junit.Test;

import java.util.List;

/**
 * Created by Administrator on 2017/4/8.
 */
public class DBUtilTest {

    @Test
    public void insertObject() {
        User user = new User();
        user.setUserid("UUID()");
        user.setPassword("admin");
        user.setUsername("omsfuk");
        user.setEmail("11111111@qq.com");
        user.setNickname("逗逼");
        DBUtil.insertObject(user);
    }

    @Test
    public void getObject() {
        User user = new User();
        user.setUsername("omsfuk");
        List<Object> result = DBUtil.getObject(user);
        for(Object obj : result) {
            User usr = (User) obj;
            System.out.println(usr);
        }
    }
}
```
运行测试，输出
```
2152589c-1c73-11e7-8ca6-afc3c17f389b omsfuk admin 逗逼 11111111@qq.com
```

OK，成功。
