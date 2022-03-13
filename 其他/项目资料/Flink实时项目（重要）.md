1.反向代理服务器：负载均衡

2.在kafka中做分层（不同的topic）

3.封装kafka工具类获取数据

4.flink操作：

DWD:

Flink(dwd_BaseLogApp)

​      1.从kafka的ODS层读取数据
      2.判断新老访客
      3.分流(页面|曝光|启动)  
      4.写到kafka的DWD     dwd_start_log、dwd_display_log、dwd_page_log

Flink(dwd_BaseDBApp)
      1.从kafka的ODS层读取数据
      2.对数据进行ETL，过滤空以及非法数据
      3.实现动态分流(维度-hbase，事实-kafka)      dwd_order_info|dwd_order_detail|dwd_payment_info......

DWM:

Flink(UniqueVisitApp)

​	1.从kafka的dwd_page_log读取数据

​	2.过滤得到独立访客

​	3.写到kafka的DWM    dwm_unique_visit

Flink(UserJumpDetailApp)

​	1.从kafka的dwd_page_log读取数据

​	2.通过CEP对数据进行处理

​	3.写到kafka的DWM    dwm_user_jump_detail

Flink(OrderWiderApp)

​	1.从kakfka的dwd_order_info\dwd_order_detail读取数据

​	2.双流join

​	3.维度关联

​	4.协会到kafka的DWM    dwm_order_wide

Flink(PaymentWideApp)

​	1.从kafka的dwd_payment_info获取支付数据

​	2.从kafka的dwm_order_wide中获取订单宽表数据

​	3.双流join

​	4.写回到kafka的DWM     dwm_payment_wide

DWS:

Flink(dws_ProductStatsApp)

​	1.从kafka的dwd_page_log获取点击以及曝光

​	2.从kafka的dwm_order_wide中订单宽表

​	3.从kafka的dwm_payment_wide中获取支付宽表

​	4.从业务数据得到dwd层获取收藏、加购、退单、评论数据

​	5.union

​	6.分组、开窗、聚合

​	7.写回到Clickhouse数据库

Flink(dws_ProvinceStatsApp)

​	1.从kafka的dwm_order_wide中订单宽表

​	2.转换为动态表

​	3.分组、开窗、聚合

​	4.写回到clickhouse数据库

Flink(dws_VisitorStatsApp)

​	1.从kafka的dwd_page_log获取pv、持续访问时间、判断是否新的会话

​	2.从kafka的dwm_unique_visit中获取独立访问数据

​	3.从kafka的dwm_user_jump_detail中获取跳出数据

​	4.union

​	5.分组、开窗、聚合

​	6.写回到clickhouse数据库

Flink(dws_KeywordStatsApp)

​	1.从kafka的dwd_page_log获取关键词搜索数据

​	2.对搜索关键词进行分词处理

​	3.分组、开窗、聚合

​	4.写回到clickhouse数据库

Flink(dws_KeywordStats11App)

​	1.从kafka的dws_product_stats获取关键词搜索数据

​	2.对搜索关键词进行分词处理

​	3.分组、开窗、聚合

​	4.写回到clickhouse数据库

————》》》dws_product_stats



5.测输出流：接收迟到数据、分流

6.ORM：object+relation+mapping

7.maxwell可以处理历史数据，比canal更轻量级一点

8.p80总结

9.jump(跳出率)：关注跳出率可以看出引流过来的访客是否能很快的被吸引，渠道引流过来的用户之间的质量对比，对于应用优化前后跳出率的对比也能看出优化改进的效果。

10.flink自带的CEP技术非常适合通过多条数据组合来识别某个事件

11.双流join

12.异步IO

13.业务数据是存在mysql中的，通过maxwell我们可以用kafka去消费他

14.dws：访客主题、商品主题、地区主题、关键字主题      （分组、开窗、聚合）

15.simpleDateFprmat：

format 日期转换成字符串          parse 字符串转换成日期

16.DWS：

​           对数据进行聚合计算

​	   -访客

​                   pv，

​                   sv，

​                   持续访问时间      ---->dwd_page_log

​                   uv,                        ---->dwm_unique_visit

​                   userJump            ---->dwm_user_jump_detail

​                                                 ---->VisitorStats(union)

​                   指定水位线以及抽取事件时间

​                   分组（按维度）

​                   开窗

​                   聚合

​                   输出到ClickHouse（JdbcSink.sink）

​           -商品

​                 点击

​                 曝光          ---->dwd_page_log

​                 收藏          ---->dwd_facor_info

​                 加购          ---->dwd_cart_info

​                 退款          ---->dwd_refund_info

​                 评价          ---->dwd_comment_info

​                 下单          ---->dwm_order_wide

​                 支付          ---->dwm_payment_wide

​         -地区

​                 FlinkSQL

​         -关键字

​                  FlinkSQL

17.数据大屏：阿里datav、百度sugar

18.数据接口

19.京东万象接口

20.-使用SpringBoot开发数据服务接口
	-使用的技术
		Spring SpringMVC Mybatis

	-对web项目进行了分层  Controller<-->Service<-->Mapper  面向接口编程、面向抽象编程不要面向具体
		*表示层 Controller
			和客户端打交道，接收用户的情况，将请求交给业务层处理，返回响应给客户端
		*业务层	Service
			处理具体的业务
		*持久层	dao|mapper
			和数据库打交道
21.可视化总结：dws层聚合好的数据放到clickhouse中，dws层一共有商品、地区、访客、关键词四个方面。用百度的sugar大屏，需要提供一个接口，先在sugar上看所需要的数据格式，利用spring、mybatis来开发接口，首先在mapper层连接数据库得到数据（标注法@sql），在service层创建接口实现类（注解法），在controller层创建格式和接口（@RequestMapping（“/api/sugar/gmv”））-->内网穿透工具花生壳：内网穿透可以让你的局域网中的电脑实现被外网访问的功能。大屏主要有销售额、地区热力图、词云图、topN等区域。

22.

```
独立分窗口启动
bin/flink run -m hadoop202:8081 -c com.atguigu.gmall.realtime.app.dwd.BaseLogApp ./gmall0820-realtime.jar

bin/flink run -m hadoop202:8081 -c com.atguigu.gmall.realtime.app.dws.VisitorStatsApp ./gmall0820-realtime.jar
```

