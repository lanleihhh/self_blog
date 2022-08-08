# 新增方法
- ``compute(K key,  BiFunction remappingFunction)``
	-  **不存在**key-value的映射关系，**返回null**
	-  **存在**key-value的映射关系，通过remappingFunction**计算newValue**
	   1. newValue**不为null**：**返回**newValue，将newValue**更新**到映射中
	   2. newValue**为null**：**删除**当前key
- `computeIfAbsent(K key,  Function remappingFunction)`
	-  **存在**key-value的映射关系，**返回value**
	-  **不存在**key-value的映射关系，通过remappingFunction**计算newValue**
	1. newValue**不为null**：将newValue**更新**到映射中
	 2. newValue**为null**：不会更新到映射
- ``computeIfPresent(K key,  BiFunction remappingFunction)``
	-  **不存在**key-value的映射关系，**返回null**
	-  **存在**key-value的映射关系，通过remappingFunction**计算newValue**
		1. newValue**不为null**：将newValue**更新**到映射中
		2. newValue**为null**：**删除**当前key
- ``putIfAbsent(K key,  V value)``
	-  key对应的**value不为null**，**返回value**
	- key对应的**value为null**，将value**更新**到映射中，**返回null**
- ``getOrDefault(Object key, V defaultValue)``
	- key对应
- ``merge(K key,  V value, BiFunction remappingFunction)``
	- **不存在**key-value的映射关系，将value更新到映射中，返回value
	- 存在时，执行一个参数为`(oldValue,newValue)`的自定义方法，返回计算后的新值
# 测试
## compute 
模拟订单与售价的映射

存在key对应的value，返回重新计算后的value，不存在，返回null


```java
HashMap<String,Integer> map = new HashMap<>();
map.put("订单1", 100);
System.out.println("订单1打折后 :"+map.compute("订单1", (key,value)->{
    if(value == null){
        return value;
    }
    return (int)(value * 0.8);
}));//输出80
System.out.println("订单2打折后 :"+map.compute("订单2", (key,value)->{
    if(value == null){
        return value;
    }
    return (int)(value * 0.8);
}));//输出null
```
## computeIfAbsent
leetcode249

```java
	public List<List<String>> groupStrings(String[] strings) {
       Map<String, List<String>> map = new HashMap<>();
        for(String str : strings){
            StringBuilder sb = new StringBuilder("t");
            for (int i = 1; i < str.length(); i++) {
                char ch = (char)((str.charAt(i) - 'a' - (str.charAt(0) - 'a') + 26)%26 + 'a');
                sb.append(ch);
            }
            map.computeIfAbsent(sb.toString(), K->new ArrayList<>()).add(str);
        }
        return new ArrayList<>(map.values());
    }
```
leetcode49

```java
	/**
     * 思路：1.若两个字符串互为异位词，那他们排序后内容一致
     *      2.取出每个字符串，转为字符数组，给数组排序，再变回字符串
     *      3.将排序后的字符串作为key，与他排序后相同的字符串集合作为value
     *      4.将map.values()装入List中，map.values()返回值是Collection
     */
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String,List<String>> map = new HashMap<>();
        for(int i = 0; i < strs.length; i++){
            //取出String数组中每个字符串，转为字符数组
            char[] chars = strs[i].toCharArray();
            //给字符数组排序
            Arrays.sort(chars);
            //将排序后的字符数组转为String
            String key = String.valueOf(chars);
            //computeIfAbsent():若key对应的value不存在，使用remappingFunction计算后的值做为key的value
            List<String> list = map.computeIfAbsent(key,K -> new ArrayList<>());
                         list.add(strs[i]);
        }
        //将map的所有值的集合存入List中
        return new ArrayList<>(map.values());
    }
```

