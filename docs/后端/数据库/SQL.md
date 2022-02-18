# SQL

建表

```sql
CREATE TABLE 表名称
(
列名称1 数据类型,
列名称2 数据类型,
列名称3 数据类型,
....
)
```

```sql
-- auto-generated definition
create table wt_data
(
    s                                                        datetime       null,
    nacelle_vibration_sensor_x                               double         null,
    nacelle_vibration_sensor_y                               double         null,
    nacelle_vibration_sensor_momentary_offset_max            double         null,
    nacelle_vibration_effective_value                        double         null,
    wind_speed_1                                             double         null,
    wind_speed_2                                             double         null,
    NC001                                                    double         null,
    wind_direction_1                                         double         null,
    wind_direction_2                                         double         null,
    rotor_speed                                              double         null,
    rotor_speed_for_control                                  double         null,
    rotor_speed_1                                            double         null,
    rotor_speed_2                                            double         null,
    rotor_position                                           int            null,
    rotor_position_rotor_speed                               int            null,
    yaw                                                      double         null,
    yaw_yawing_speed                                         double         null,
    YW002                                                    decimal(10, 2) null,
    gearbox_temperature_input_shaft1                         double         null,
    gearbox_temperature_output_shaft2                        double         null,
    gearbox_oil_temperature_oil_inlet                        double         null,
    gearbox_oil_temperature_gearbox                          double         null,
    gearbox_cooling_water_temperature                        int            null,
    gearbox_lubrication_filter_inlet_pressure                double         null,
    gearbox_lubrication_filter_outlet_pressure               int            null,
    generator_bearing_temperature_a                          double         null,
    generator_bearing_temperature_b                          double         null,
    nacelle_outdoor_temperature                              double         null,
    nacelle_temperature                                      double         null,
    TR002                                                    double         null,
    main_bearing_rotor_side_temperature                      double         null,
    main_bearing_gearbox_side_temperature                    double         null,
    converter_motor_speed                                    double         null,
    main_loop_converter_torque_setpoint                      double         null,
    converter_power                                          double         null,
    converter_ctrl_converter_torque_setpoint                 double         null,
    converter_generator_torque                               double         null,
    main_loop_rotor_speed_demand                             double         null,
    local_gearbox_lubrication_oil_filter_pressure_ok_disable int            null,
    local_gearbox_lubrication_oil_pressure_ok_disable        int            null,
    c                                                        int            null,
    TR015                                                    int            null comment 'wt_status',
    RT101                                                    decimal(10, 2) null
);
```

>RT101，YW002必须要有小数。

```sql
CREATE TABLE wt_data(
    NC001 DECIMAL COMMENT'风速ws-wind_speed',
    			DECIMAL COMMENT'风向角wp',
    TR002 DECIMAL COMMENT'功率gp-grid_power',
    YW002 DECIMAL COMMENT'对风角yaw',
    RT101 DECIMAL COMMENT'发电机仪表温度generator_bearing_temperature_a-ba',
  				DECIMAL COMMENT'轮毂转速rotor_position',
    TR015 VARCHAR(255) COMMENT'风力发电机状态-wt_status',
    c INT COMMENT'风力发电机变量',
    s DATETIME COMMENT'时间'
)
```

```sql
create table result_log
(
    start_time      datetime       null comment '开始时间',
    end_time        datetime       null comment '结束时间',
    deviceNum       int            null comment '设备数量',
    status_code     varchar(20)    null comment '状态码',
    troubleType     int            null comment '问题类型',
    troubleLevel    int            null comment '问题等级',
    actualOccurTime varchar(255)   null comment '当前发生时间',
    dataJson        varchar(13000) null comment '数据JSON'
);
```



配置文件中

```
   wtid_var: c
   ws: NC001
   gp: TR002
   yaw: YW002
   dt: s
   ba: RT101
   wt_status: TR015
```

插入

```sql
INSERT INTO data.wt_data (NC001, TR002, YW002, RT101, TR015, c, s)
VALUES (16.64172, 1554.6, 111.2447, 33.6, '1', 1, '2018-06-04 00:00:00');
```





添加字段

```sql
alter table wt_data add colomn password default null COMMENT'密码' after user
```

> 在user字段后面添加一个password字段

在字段添加数据

```sql
UPDATE wt_data SET user= CONCAT(user,',phpchina')  WHERE id= '2';
```





## 随机生成大量数据

### 用sql语句

```sql
UPDATE wt_data set RT101=round(rand() * (95 - (-2))) /10 * 10  where RT101 is not null;
```



## 遇到各种语句卡死

https://blog.csdn.net/yangfengjueqi/article/details/81062123?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-0&spm=1001.2101.3001.4242

```sql
show full processlist;
kill xxxxxx;
```



## 牛客SQL

https://www.nowcoder.com/ta/sql



### SQL4查找所有已经分配部门的员工的last_name和first_name以及dept_no

```sql
select e.last_name,e.first_name,d.dept_no
from employees as e
join dept_emp as d
on e.emp_no = d.emp_no
```

### SQL5 查找所有员工的last_name和first_name以及对应部门编号dept_no

> 这道题和上一道区别在于，需要查所有
>
> left join是左连接，以左表为主。

```sql
select e.last_name, e.first_name, d.dept_no
from employees as e
left join dept_emp as d
on e.emp_no = d.emp_no;
```

### SQL7 查找薪水记录超过15次的员工号emp_no以及其对应的记录次数t

>WHERE语句在GROUP BY语句之前；SQL会在分组之前计算WHERE语句。HAVING语句在GROUP BY语句之后；SQL会在分组之后计算HAVING语句。

```sql
SELECT emp_no, COUNT(emp_no) AS t FROM salaries 
GROUP BY emp_no HAVING t > 15;
```



### SQL8 找出所有员工当前薪水salary情况

>关键词 DISTINCT 用于返回唯一不同的值。

```sql
select distinct salary
from salaries
where to_date='9999-01-01'
order by salary desc;
```

### SQL10 获取所有非manager的员工emp_no

> 先左连接，聚合两张表为一张表。然后在这张表里找到dept_no为空的那行数据。

```sql
select emp_no from (select * from employees
                             left join dept_manager 
                              on employees.emp_no = dept_manager.emp_no
                             )
where dept_no is null;
```

