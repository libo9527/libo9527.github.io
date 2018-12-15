# 字符串格式化

## 日期格式化

### 将时间戳格式化为yyyy-MM-dd

1. 使用SimpleDateFormat类

   ```java
   SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd");
   String ret = format.format(new Date());
   ```

2. 使用String.format()方法

   ```java
   String ret = String.format("%tF", new Date());
   ```

   注：常见的日期和时间组合的格式

   | 转换符 | 说明                         | 示例      |
   | ------ | ---------------------------- | --------- |
   | %tF    | “年-月-日“格式（4位年份）    | 2018-7-11 |
   | %tT    | ”时：分：秒“格式（24小时制） | 20:47:27  |

   