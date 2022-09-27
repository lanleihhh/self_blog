# Java中如何让一个集合不可变？

## Java8及之前：Collections工具类中提供的方法：unmodifiableXXX()


|            方法            |                      参数列表                       | 功能                       |
| :------------------------: | :-------------------------------------------------: | -------------------------- |
|    `unmodifiableList()`    |     `unmodifiableList(List<? extends T> list)`      | 返回指定list的不可修改视图 |
|    `unmodifiableMap()`     |  `unmodifiableMap(Map<? extends K,? extends V> m)`  | 返回指定map的不可修改视图  |
|    `unmodifiableSet()`     |        `unmodifiableSet(Set<? extends T> s)`        | 返回指定set的不可修改视图  |
| `unmodifiableCollection()` | `unmodifiableCollection(Collection<? extends T> c)` | 返回指定集合的不可修改视图 |

## 示例：
![在这里插入图片描述](https://img-blog.csdnimg.cn/3ece670f979b4dcb896c24ad47189b08.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/0b75588d6b794500a423e815f65aec59.png)

## 源码：

`unmodifiableCollection()`源码

```java
public static <T> Collection<T> unmodifiableCollection(Collection<? extends T> c) {
    return new UnmodifiableCollection<>(c);
}

static class UnmodifiableCollection<E> implements Collection<E>, Serializable {
	//fianl修饰的集合，不可改变
	final Collection<? extends E> c;

	//将传入的集合参数指向UnmodifiableCollection类中的c
	UnmodifiableCollection(Collection<? extends E> c) {
		if (c==null)
		    throw new NullPointerException();
		this.c = c;
	}
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/c7238e12364a4630b3afe378c843962a.png)

`unmodifiableMap()`源码类似
```java
public static <K,V> Map<K,V> unmodifiableMap(Map<? extends K, ? extends V> m) {
        return new UnmodifiableMap<>(m);
}

private static class UnmodifiableMap<K,V> implements Map<K,V>, Serializable {

    private final Map<? extends K, ? extends V> m;

    UnmodifiableMap(Map<? extends K, ? extends V> m) {
        if (m==null)
            throw new NullPointerException();
        this.m = m;
    }
}
```
修改Map的方法也都抛出了异常UnsupportedOperationException
![在这里插入图片描述](https://img-blog.csdnimg.cn/366ffba5a1134f468e2400bcff6b316d.png)
## 	Java9 XXX.of()
List、Set、Map接口新增了静态的of()方法，可以给集合一次性添加多个元素
`static List of​(E... elements)`

- List.of(); `List.of(1,2,3)`
- Set.of(); `Set.of(1,2,3)`
- Map.of(); `Map.of(k1,v1,k2,v2,k3,v3......)`

1. of()只能`接口.of()`,不能在实现类中使用
2. 返回的集合不可改变，进行add、put、remove等修改操作会抛出异常
3. Set和Map调用of()时，添加重复元素/键会抛出异常
