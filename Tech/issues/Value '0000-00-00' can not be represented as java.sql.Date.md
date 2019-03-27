# ##Value '0000-00-00' can not be represented as java.sql.Date

1. *Value '0000-00-00' can not be represented as java.sql.Date*?
_场景_：MySql数据库中日期字段存储的数据为’0000-00-00’，在转换为java.sql.Date对象的时候，产生异常。
_方法_：在Mysql数据库中使用DATETIME类型来存储时间，使用JDBC中读取这个字段的时候，应该使用 ResultSet.getTimestamp()，这样会得到一个java.sql.Timestamp类型的数据。在这里既不能使用 ResultSet.getDate()，也不能使用ResultSet.getTime()，因为前者不包括time数据，后者不包括date数据。
但是在使用ResultSet.getTimestamp()时也不是完全安全的，例如，当数据库中的TIMESTAMP类型的字段值为 '0000-00-00 00:00:00'时，使用此方法进行读取，会抛出异常：Cannot convert value '0000-00-00 00:00:00' from column 1 to TIMESTAMP，这是因为JDBC不能将'0000-00-00 00:00:00'转化为一个为一个java.sql.Timestamp，在Java中，想创建一个java.util.Date，使其值为 '0000-00-00'也是不可能的，最古老的日期应该是'0001-01-01 00:00:00'。
那么在程序中该怎么办捏？ 解决方案在这里：
>Datetimes with all-zero components (0000-00-00 ...) — These values can not be represented reliably in Java. Connector/J 3.0.x always converted them to NULL when being read from a ResultSet.
>Connector/J 3.1 throws an exception by default when these values are encountered as this is the most correct behavior according to the JDBC and SQL standards. This behavior can be modified using the zeroDateTimeBehavior configuration property. The allowable values are:
>exception (the default), which throws an SQLException with an SQLState of S1009.
>convertToNull, which returns NULL instead of the date.
>round, which rounds the date to the nearest closest value which is 0001-01-01.
>Starting with Connector/J 3.1.7, ResultSet.getString() can be decoupled from this behavior via noDatetimeStringSync=true (the default value is false) so that you can retrieve the unaltered all-zero value as a String. It should be noted that this also precludes using any time zone conversions, therefore the driver will not allow you to enable noDatetimeStringSync and useTimezone at the same time.
所以，在JDBC URL中加入zeroDateTimeBehavior信息，既可以解决：
String url = "jdbc:mysql://10.149.51.80:3306/test?relaxAutoCommit=true&zeroDateTimeBehavior=convertToNull";
[参考资料]
2. Every deriver table must have its own alias.
_场景_：
> select count(*) from   
>(
>select lru.licenseServerId,lru.featureName from license_realtime_userusage lru 
>group by lru.licenseServerId,lru.featureName
>)
_方法_：
跟Oracle不同，Mysql的表都要有表名或者别名，空名称是不行的，所以给连接产生的的新表添加一个别名的就可以了。
> select count(*) from   
>(
>select lru.licenseServerId,lru.featureName from license_realtime_userusage lru 
>group by lru.licenseServerId,lru.featureName
>) as c1
3. 
#工作/问题清单