# java基础
# 数组  
- 构建数组  
1. Arrays.asList(n1,n2,n3)  
- 排序  
1. Arrays.sort(nums);  
# 字符串String
- 遍历方法
```
for (int i = 0; i < str.length(); i++) {
    str.charAt(i);
}
```
```
char[] c = str.toCharArray();
遍历char
```
- 切割方法 ``` str.substring(i,j)```
- char转String ：String.valueOf(c);
## StringBuilder类：可变的字符串，可以append

# HashMap
HashMap<String,Integer> map = new HashMap<>();  
map.put(String,Integer)  
map.get();   
大小：map.size()  
遍历键 keySet(); values();  
map.getOrDefault(key, V defaultValue); 找key对应的value  
map.containsKey()  

# int 和 string 相互转换
1. int 转 String   
    - String s = String.valueOf(i);
    - String s = Integer.toString(i);
    - String s = "" + i;
2. String 转 int
    - int i = Integer.parseInt(s);
# HashSet
