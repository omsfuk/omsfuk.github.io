# getDeclaredXXXX与getXXXX区别

今天造轮子的时候，发现有个点老是实现不了了，最后看了看API文档，终于关键点不在那，错误是在前边。
原来是被一只不懂还瞎写博客的傻逼误导了。。。。我想骂娘。。。吐槽完毕。。  
所以特此来纠正下这个错误。

其实api文档写的很清楚了，平时总是懒得查，习惯于谷歌，所以引发了一些问题。

## 我是搬运工。。。
搬运自官方文档（https://docs.oracle.com/javase/8/docs/api/, jdk1.8）  ，翻译了下，方便E文不好的童鞋

```
Field    getField(String name)
Returns a Field object that reflects the specified public member field of the class or interface represented by this Class object.

Field[]    getFields()
Returns an array containing Field objects reflecting all the accessible public fields of the class or interface represented by this Class object.
```
返回`public`属性，**包括继承来的属性**

```
Field getDeclaredField(String name)
Returns a Field object that reflects the specified declared field of the class or interface represented by this Class object.

Field[]	getDeclaredFields()
Returns an array of Field objects reflecting all the fields declared by the class or interface represented by this Class object.
```
返回`本类`所有属性，包括`private`,`public`,`default`, `protected`,**不包括继承来的属性**。
另外私有属性要想修改，需要`field.setAccessible(true)`

```
Method	getMethod(String name, Class<?>... parameterTypes)
Returns a Method object that reflects the specified public member method of the class or interface represented by this Class object.

Method[]	getMethods()
Returns an array containing Method objects reflecting all the public methods of the class or interface represented by this Class object, including those declared by the class or interface and those inherited from superclasses and superinterfaces.
```
获得`public`成员方法，**包括继承来的方法**

```
Method	getDeclaredMethod(String name, Class<?>... parameterTypes)
Returns a Method object that reflects the specified declared method of the class or interface represented by this Class object.

Method[]	getDeclaredMethods()
Returns an array containing Method objects reflecting all the declared methods of the class or interface represented by this Class object, including public, protected, default (package) access, and private methods, but excluding inherited methods.
```
获得所有成员方法，包括`public`, `protected`, `default`, `private`, **不包括继承来的方法**

综上，不论是方法还是属性，private和继承无法共存（就是说无法获得继承来的私有东西）

```
Constructor<T>	getConstructor(Class<?>... parameterTypes)
Returns a Constructor object that reflects the specified public constructor of the class represented by this Class object.
Constructor<?>[]	getConstructors()
Returns an array containing Constructor objects reflecting all the public constructors of the class represented by this Class object.
```
返回所有构造器，**不包括私有**（继承呢？开玩笑，构造器可以继承嘛。。）

```
Constructor<T>	getDeclaredConstructor(Class<?>... parameterTypes)
Returns a Constructor object that reflects the specified constructor of the class or interface represented by this Class object.

Constructor<?>[]	getDeclaredConstructors()
Returns an array of Constructor objects reflecting all the constructors declared by the class represented by this Class object.
```
返回所有构造器，**包括私有**（继承呢？开玩笑，构造器可以继承嘛。。）

```
<A extends Annotation> A  getAnnotation(Class<A> annotationClass)
Returns this element's annotation for the specified type if such an annotation is present, else null.
Annotation[]	getAnnotations()
Returns all annotations present on this element.
```
获得所有注解，**包括继承来的**（关于注解的继承，一定要小心：方法级别的注解无条件继承；类级别的注解，需要加@Inherited注解才能继承）

```
Annotation[]	getDeclaredAnnotations()
```
获得所有“直接的”注解，即 **不包括继承来的**（关于注解的继承，一定要小心：方法级别的注解无条件继承；类级别的注解，需要加@Inherited注解才能继承）
注意，不存在getDeclaredAnnotation方法。
