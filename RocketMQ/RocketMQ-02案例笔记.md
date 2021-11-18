# 1.案例介绍

## 1.1 业务分析

模拟电商网站购物场景中的【下单】和【支付】业务

### 1）下单

![image-20211115114635255](C:\Users\69079\AppData\Roaming\Typora\typora-user-images\image-20211115114635255.png)

1. 用户请求订单系统下单
2. 订单系统通过 RPC 调用订单服务下单
3. 订单服务调用优惠券服务，扣减优惠券
4. 订单服务调用库存服务，校验并扣减库存
5. 订单服务调用用户服务，扣减用户余额
6. 订单服务完成确认订单

### 2）支付

![image-20211115114916164](C:\Users\69079\AppData\Roaming\Typora\typora-user-images\image-20211115114916164.png)

1. 用户请求支付系统
2. 支付系统调用第三方支付平台 API 进行发起支付流程
3. 用户通过第三方平台支付成功后，第三方支付平台回调通知支付系统
4. 支付系统调用订单服务修改订单状态
5. 支付系统调用积分服务添加积分
6. 支付系统调用日志服务记录日志

## 1.2 问题分析

### 问题1

用户提交订单后，扣减库存成功、扣减优惠券成功、使用余额成功，但是在确认订单操作失败，需要对库存、优惠券、余额进行回退。如何保证数据的完整性？

<u>使用MQ保证在下单失败后系统数据的完整性</u>

![image-20211115120008725](C:\Users\69079\AppData\Roaming\Typora\typora-user-images\image-20211115120008725.png)

### 问题2

用户通过第三方支付平台（支付宝、微信）支付成功后，第三方支付平台要通过回调 API 异步通知商家支付系统用户支付结果，支付系统根据支付结果修改订单状态、记录支付日志和给用户增加积分。

商家支付系统如何保证在收到第三方支付平台的异步通知时，如何快速给第三方支付凭条做出回应？

<u>通过MQ进行数据分发，提高系统处理性能</u>

![image-20211115120536854](C:\Users\69079\AppData\Roaming\Typora\typora-user-images\image-20211115120536854.png)

# 2.技术分析

## 2.1 技术选型

* SpringBoot
* Dubbo
* Zookeeper
* RocketMQ
* Mysql

![image-20211115120642088](C:\Users\69079\AppData\Roaming\Typora\typora-user-images\image-20211115120642088.png)

![image-20211115120649998](C:\Users\69079\AppData\Roaming\Typora\typora-user-images\image-20211115120649998.png)

![image-20211115120658147](C:\Users\69079\AppData\Roaming\Typora\typora-user-images\image-20211115120658147.png)

![image-20211115121005910](C:\Users\69079\AppData\Roaming\Typora\typora-user-images\image-20211115121005910.png)

## 2.2 SpringBoot整合RocketMQ

下载rocketmq-spring项目

将 rocketmq-spring 安装到本地仓库

```shell
mvn install -Dmaven.skip.test=true
```

### 2.2.1 消息生产者

#### 1）添加依赖

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.1.RELEASE</version>
</parent>

<properties>
    <rocketmq-spring-boot-starter-version>2.0.4</rocketmq-spring-boot-starter-version>
</properties>

<dependencies>
    <dependency>
        <groupId>org.apache.rocketmq</groupId>
        <artifactId>rocketmq-spring-boot-starter</artifactId>
        <version>${rocketmq-spring-boot-starter-version}</version>
    </dependency>
    
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.6</version>
    </dependency>
    
    <denpendency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </denpendency>
</dependencies>
```

#### 2）配置文件

```properties
# application.properties
rocketmq.name-server=192.168.25.135:9876;192.168.25.138:9876
rocketmq.producer.group=my-group
```

#### 3）启动类

```java
@SpringBootApplication
public class MQProducerApplication {
    public static void main(String[] args) {
        SpringApplication.run(MQSpringBootApplication.class,args);
    }
}
```

#### 4）测试类

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = {MQSpringBootApplication.class})
public class ProducerTest {
    
    @Autowired
    private RocketMQTemplate rocketMQTemplate;
    
    @Test
    public void test1() {
        rocketMQTemplate.convertAndSend("springboot-mq","hello springboot rocketmq");
	}
}
```

### 2.2.2 消息消费者

#### 1）添加依赖

同生产者

```xml
<!--添加WEB启动依赖-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

#### 2）配置文件

同生产者

#### 3）启动类

```java
@SpringBootApplication
public class MQConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(MQConsumerApplication.class,args);
    }
}
```

#### 4）消息监听器

```java
@RocketMQMessageListener(topic = "springboot-rocketmq", consumerGroup = "${rocketmq.consumer.group}")
@Component
public class Consumer implements RocketMQListener<String> {
    
    @Override
    public void onMessage(String s) {
        System.out.println(s);
    }
}
```

## 2.3 SpringBoot整合Dubbo

下载dubbo-spring-boot-starter依赖包

将 `dubbo-spring-boot-starter` 安装到本地仓库

```shell
mvn install -Dmaven.skip.test=true
```

### 2.3.1 搭建Zookeeper集群

#### 1）准备工作

1. 安装 JDK

2. 将 Zookeeper 上传到服务器

3. 解压 Zookeeper，并创建 data 目录，将 conf 下的 zoo_sample.cfg 文件改名为 zoo.cfg

4. 建立 `/user/local/zookeeper-cluster`，将解压后的 Zookeeper 复制到一下三个目录

   ```tex
   /user/local/zookeeper-cluster/zookeeper-1
   /user/local/zookeeper-cluster/zookeeper-2
   /user/local/zookeeper-cluster/zookeeper-3
   ```

5. 配置每一个 Zookeeper 的 dataDir（zoo.cfg）clientPort 分别为 2181 2812 2183

   修改 `/user/local/zookeeper-cluster/zookeeper-1/conf/zoo.cfg`

   ```properties
   clientPort=2181
   dataDir=/user/local/zookeeper-cluster/zookeeper-1/data
   ```

   修改 `/user/local/zookeeper-cluster/zookeeper-2/conf/zoo.cfg`

   ```properties
   clientPort=2182
   dataDir=/user/local/zookeeper-cluster/zookeeper-2/data
   ```

   修改 `/user/local/zookeeper-cluster/zookeeper-3/conf/zoo.cfg`

   ```properties
   clientPort=2183
   dataDir=/user/local/zookeeper-cluster/zookeeper-3/data
   ```

#### 2）配置集群

1. 在每个 zookeeper 的 data 目录下创建一个 myid 文件，内容分别是 1、2、3。这个文件就是记录每个服务器的 ID

2. 在每一个 zookeeper 的 zoo.cfg 配置客户端访问端口（clientPort）和集群服务器 IP 列表。

   集群服务器 IP 列表如下

   ```shell
   server.1=192.168.25.140:2881:3881
   server.2=192.168.25.140:2882:3882
   server.3=192.168.25.140:2883:3883
   ```

   解释：server.服务器 ID=服务器 IP 地址：服务器之间通信端口：服务器之间投票选举端口

#### 3）启动集群

启动集群就是分别启动每个实例。

### 2.3.2 RPC服务接口

```java
public interface IUserService {
    public String sayHello(String name);
}
```

### 2.3.3 服务提供者

#### 1）添加依赖

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.1.RELEASE</version>
</parent>

<dependencies>
    <!--dubbo-->
    <dependency>
        <groupId>com.alibaba.spring.boot</groupId>
        <artifactId>dubbo-spring-boot-starter</artifactId>
        <version>2.0.0</version>
    </dependency>
    
    <!--spring-boot-starter-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
        <exclusions>
            <exclusion>
                <artifactId>log4j-to-slf4j</artifactId>
                <groupId>org.apache.logging.log4j</groupId>
            </exclusion>
        </exclusions>
    </dependency>
    
    <!--zookeeper-->
    <dependency>
        <groupId>org.apache.zookeeper</groupId>
        <artifactId>zookeeper</artifactId>
        <version>3.4.10</version>
        <exclusions>
        	<exclusion>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
            <exclusion>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    
    <dependency>
        <groupId>com.101tec</groupId>
        <artifactId>zkclient</artifactId>
        <version>0.9</version>
        <exclusions>
            <exclusion>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    
    <!--API-->
    <dependency>
        <groupId>com.itheima.demo</groupId>
        <grtifactId>dubbo-api</grtifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
    
</dependencies>
```

#### 2）配置文件

```properties
# application.properties
spring.application.name=dubbo-demo-provider
spring.dubbo.application.id=dubbo-demo-provider
spring.dubbo.application.name=dubbo-demo-provider
spring.dubbo.registry.address=zookeeper://192.168.25.140:2181;zookeeper://192.168.25.140:2182;zookeeper://192.168.25.140:2183
spring.dubbo.server=true
spring.dubbo.protocol.name=dubbo
spring.dubbo.protocol.prot=20880
```

#### 3）启动类

```java
@EnableDubboConfiguration
@SpringBootApplication
public calss ProdiverApplication {
    public static void main(String[] args) {
        SpringApplication.run(ProdiverApplication.class,args);
    }
}
```

#### 4）服务实现

```java
@Server
@Component
public class UseServiceImpl implements IUseService {
    @Override
    public String sayHello(String name) {
        return "hello:" + name;
    }
}
```

### 2.3.4 服务消费者

#### 1）添加依赖

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.1.RELEASE</version>
</parent>

<dependencies>
    <!--添加WEB启动依赖-->
	<dependency>
    	<groupId>org.springframework.boot</groupId>
    	<artifactId>spring-boot-starter-web</artifactId>
	</dependency>
    
    <!--dubbo-->
    <dependency>
        <groupId>com.alibaba.spring.boot</groupId>
        <artifactId>dubbo-spring-boot-starter</artifactId>
        <version>2.0.0</version>
    </dependency>
    
    <!--spring-boot-starter-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
        <exclusions>
            <exclusion>
                <artifactId>log4j-to-slf4j</artifactId>
                <groupId>org.apache.logging.log4j</groupId>
            </exclusion>
        </exclusions>
    </dependency>
    
    <!--zookeeper-->
    <dependency>
        <groupId>org.apache.zookeeper</groupId>
        <artifactId>zookeeper</artifactId>
        <version>3.4.10</version>
        <exclusions>
        	<exclusion>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
            <exclusion>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    
    <dependency>
        <groupId>com.101tec</groupId>
        <artifactId>zkclient</artifactId>
        <version>0.9</version>
        <exclusions>
            <exclusion>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    
    <!--API-->
    <dependency>
        <groupId>com.itheima.demo</groupId>
        <grtifactId>dubbo-api</grtifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
    
</dependencies>
```

#### 2）配置文件

```properties
# application.properties
spring.application.name=dubbo-demo-consumer
spring.dubbo.application.name=dubbo-demo-consumer
spring.dubbo.application.id=dubbo-demo-consumer
spring.dubbo.registry.address=zookeeper://192.168.25.140:2181;zookeeper:192.168.25.140:2182;zookeeper://192.168.25.140:2183
```

#### 3）启动类

```java
@EnableDubboConfiguration
@SpringBootApplication
public calss ConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class,args);
    }
}
```

#### 4）Controller

```java
@RestController
@RequestMapping("/user")
public class UserController {
    
    @Reference
    private IUseService useService;
    
    @RequestMapping("/sayHello")
    public String sayHello(String name) {
        return useService.sayHello(name);
    }
}
```

# 3.环境搭建

## 3.1 数据库

### 1）优惠券表

| Field        | Type                | Comment                  |
| ------------ | ------------------- | ------------------------ |
| coupon_id    | bigint(50) NOT NULL | 优惠券ID                 |
| coupon_price | decimal(10,2) NULL  | 优惠券金额               |
| user_id      | bigint(50) NULL     | 用户ID                   |
| order_id     | bigint(32) NULL     | 订单ID                   |
| is_used      | int(1) NULL         | 是否使用 0未使用 1已使用 |
| used_time    | timestamp NULL      | 使用时间                 |

### 2）商品表

| Field        | Type                | Comment  |
| ------------ | ------------------- | -------- |
| goods_id     | bigint(50) NOT NULL | ID       |
| goods_name   | varchar(225) NULL   | 商品名称 |
| goods_number | int(11) NULL        | 商品库存 |
| goods_price  | decimal(10,2) NULL  | 商品价格 |
| goods_desc   | varchar(255) NULL   | 商品描述 |
| add_time     | timestamp NULL      | 添加时间 |

### 3）订单表

| Field           | Type                | Comment                                      |
| --------------- | ------------------- | -------------------------------------------- |
| order_id        | bigint(50) NOT NULL | 订单ID                                       |
| user_id         | bigint(50) NULL     | 用户ID                                       |
| order_status    | int(1) NULL         | 订单状态 0未确认 1已确认 2已取消 3无效 4退款 |
| pay_status      | int(1) NULL         | 支付状态 0未支付 1支付中 2已支付             |
| shipping_status | int(1) NULL         | 发货状态 0未支付 1已发货 2已退货             |
| address         | varchar(255) NULL   | 收货地址                                     |
| consignee       | varchar(255) NULL   | 收货人                                       |
| goods_id        | bigint(50) NULL     | 商品ID                                       |
| goods_number    | int(11) NULL        | 商品数量                                     |
| goods_price     | decimal(10,2) NULL  | 商品价格                                     |
| goods_amount    | decimal(10,0) NULL  | 商品总价                                     |
| shipping_fee    | decimal(10,2) NULL  | 运费                                         |
| order_amount    | decimal(10,2) NULL  | 订单价格                                     |
| coupon_id       | bigint(50) NULL     | 优惠券ID                                     |
| coupon_paid     | decimal(10,2) NULL  | 优惠券                                       |
| money_paid      | decimal(10,2) NULL  | 已付金额                                     |
| pay_amount      | decimal(10,2) NULL  | 支付金额                                     |
| add_time        | timestamp NULL      | 创建时间                                     |
| confirm_time    | timestamp NULL      | 订单确认时间                                 |
| pay_time        | timestamp NULL      | 支付时间                                     |

### 4）订单商品日志表

| Field        | Type                 | Comment  |
| ------------ | -------------------- | -------- |
| goods_id     | int(11) NOT NULL     | 商品ID   |
| order_id     | varchar(32) NOT NULL | 订单ID   |
| goods_number | int(11) NULL         | 库存数量 |
| log_time     | datetime NULL        | 记录时间 |

### 5）用户表

| Field         | Type                | Comment  |
| ------------- | ------------------- | -------- |
| user_id       | bigint(50) NOT NULL | 用户ID   |
| user_name     | varchar(255) NULL   | 用户姓名 |
| user_password | varchar(255) NULL   | 用户密码 |
| user_mobile   | varchar(255) NULL   | 手机号   |
| user_score    | int(11) NULL        | 积分     |
| user_reg_time | timestamp NULL      | 注册时间 |
| user_money    | decimal(10,0) NULL  | 用户余额 |

### 6）用户余额日志表

| Field          | Type                | Comment                      |
| -------------- | ------------------- | ---------------------------- |
| user_id        | bigint(50) NOT NULL | 用户ID                       |
| order_id       | bigint(50) NOT NULL | 订单ID                       |
| money_log_type | int(1) NOT NULL     | 日志类型 1订单类型 2订单退款 |
| use_money      | decimal(10,2) NULL  | 操作金额                     |
| create_time    | timestamp NULL      | 日志时间                     |

### 7）订单支付表

| Field      | Type                | Comment            |
| ---------- | ------------------- | ------------------ |
| pay_id     | bigint(50) NOT NULL | 支付编号           |
| order_id   | bigint(50) NULL     | 订单编号           |
| pay_amount | decimal(10,2) NULL  | 支付金额           |
| is_paid    | int(1) NULL         | 是否已支付 1否 2是 |

### 8）MQ消息生产表

| Field       | Type                  | Comment             |
| ----------- | --------------------- | ------------------- |
| id          | varchar(100) NOT NULL | ID                  |
| group_name  | varchar(100) NULL     | 生产者组别          |
| msg_topic   | varchar(100) NULL     | 消息主题            |
| msg_tag     | varchar(100) NULL     | Tag                 |
| msg_key     | varchar(100) NULL     | Key                 |
| msg_body    | varchar(500) NULL     | 消息内容            |
| msg_status  | int(1) NULL           | 0:未处理;1:已经处理 |
| create_time | timestamp NOT NULL    | 记录时间            |

### 9）MQ消息消费表

| Field              | Type                 | Comment                          |
| ------------------ | -------------------- | -------------------------------- |
| msg_id             | varchar(50) NOT NULL | 消息ID                           |
| group_name         | varchar(100) NULL    | 消费者组别                       |
| msg_tag            | varchar(100) NULL    | Tag                              |
| msg_key            | varchar(100) NULL    | Key                              |
| msg_body           | varchar(500) NULL    | 消息体                           |
| consumer_status    | int(1) NULL          | 0:正在处理;1:处理成功;2:处理失败 |
| consumer_times     | int(1) NULL          | 消费次数                         |
| consumer_timestamp | timestamp NULL       | 消费时间                         |
| remark             | varchar(500) NULL    | 备注                             |

## 3.2 项目初始化

shop 系统基于 Maven 进行项目管理

### 3.1.1 工厂浏览

* 父工程：shop-parent
* 订单系统：shop-order-web
* 支付系统：shop-pay-web
* 优惠券服务：shop-coupon-service
* 订单服务：shop-order-server
* 支付服务：shop-pay-service
* 商品服务：shop-goods-service
* 用户服务：shop-user-service
* 实体类：shop-pojo
* 持久层：shop-dao
* 接口层：shop-api
* 工具工程：shop-common

## 3.3 Mybatics逆向工程使用

### 1）代码生成

使用 Mybatis 逆向工程针对数据表生成 CURD 持久层代码

### 2）代码导入

* 将实体类导入到 shop-pojo 工程
* 在服务层工程中导入对应的 Mapper 类和对应配置文件

## 3.4 公共类介绍

* ID 生成器

  IDWorker：Twitter 雪花算法

* 异常处理类

  CustomerException：自定义异常类

  CastException：异常抛出类

* 常量类

  ShopCode：系统状态类

* 响应实体类

  Result：封装响应状态和响应信息

# 4.下单业务

## 4.1 下单基本流程

### 1）接口定义

* IOrderService

```java
public interface IOrderService {
    Result confirmOrder(TradeOrder order);
}
```

* IGoodsService

```java
public interface IGoodsService {
    TradeGoods findOne(Long goodsId);
    
    //扣减库存
    Result reduceGoodsNum(TradeGoodsNumberLog goodsNumberLog);
}
```

* IUserService

```java
public interface IUserService {
    TradeUser findOne(Long goodsId);
    
    Result updateMoneyPaid(TradeUserMoneyLog userMoneyLog);
}
```

* ICouponService

```java
public interface ICouponService {
    TradeCoupon findOne(Long couponId);
    
	//更新优惠券状态    
    Result updateCouponStatus(TradeCoupon coupon);
}
```



### 2）接口实现

* OrderServiceImpl

```java
@Slf4j
@Component
@Service(interfaceClass = IOrderService.class)
public clss OrderServiceImpl implements IOrderService {
    @Override
    public Result confirmOrder(TradeOrder order) {
        // 校验订单
        checkOrder(order);
        //生成预订单
        Long orderId = savePreOrder(order);
        try {
            //扣减库存
        	reduceGoodsNum(order);
            //扣减优惠券
            updateCouponStatus(order);
            //扣减余额
            reduceMoneyPaid(order);
            //确认订单
            updateOrderStatus(order);
            //返回成功状态
            return new Result(ShopCode.SHOP_SUCCESS.getSuccess(), ShopCode.SHOP_SUCCESS.getMessage());
        } catch (Exception e) {
            //确认订单失败，发送消息
            MQEntity mqEntity = new MQEntity();
            mqEntity.setOrderId(orderId);
            mqEntity.setUserId(order.getUserId);
            mqEntity.setUserMoney(order.getMoneyPaid());
            mqEntity.setGoodsId(order.getGoodsId());
            mqEntity.setGoodsNum(order.getGoodsNumber());
            mqEntity.setCouponId(order.getCouponId());
            
            sendCancelOrder(topic, tag, keys, JSON.toJSONString(mqEntity));
            
            return new Result(ShopCode.SHOP_FAIL.getSuccess(), ShopCode.SHOP_FAIL.getMessage());
        }
        
        return null;
    }
}
```

* IGoodsServiceImpl

```java
@Component
@Service(interfaceClass = IGoodsService.class)
public class GoodsServiceImpl implements IGoodsService {
    
    @AutoWired
    private TradeGoodsMapper goodsMapper;
    
    @Override
    public TradeGoods findOne(Long goodsId) {
        if(goodsId == null) {
            CastException.cast(ShopCode.SHOP_REQUEST_PARAMETER_VALID);
        }
        return goodsMapper.selectByPrimaryKey(goodsId);
    }
    
    @Override
    public Result reduceGoodsNum(TradeGoodsNumberLog goodsNumberLog) {
        if(goodsNumberLog == null || goodsNumberLog.getGoodsId() == null || goodsNumberLog.getOrderId() == null || goodsNumberLog.getGoodsNumber() == null || goodsNumberLog.getGoodsNumber().intvalue() <= 0) {
            CastException.cast(ShopCode.SHOP_REQUEST_PARAMETER_VALID);
        }
        
        TradeGoods goods = goodsMapper.selectByPrimaryKey(goodsNumberLog.getGoodsId());
        if(goods.getGoodsNumber() - goodsNumberLog.getGoodsNumber());
        goodsMapper.updateByPrimaryKey(goods);
        
        goodsNumberLog.setGoodsNumber(-(goodsNumberLog.getGoodsNumber()));
        goodsNumberLog.setLogTime(new Date());
        goodsNumberLogMapper.insert(goodsNumberLog);
    }
    
}
```

* UserServiceImpl

```java
@Component
@Service(interfaceClass = IUserService.class)
public class UserServiceImpl implements IUserService {
    @AutoWired
    private TradeUserMapper userMapper;
    
    @Override
    public TradeUser findOne(Long userId) {
        if(userId == null) {
            CastException.cast(ShopCode.SHOP_REQUEST_PARAMETER_VALID);
        }
        return userMapper.selectByPrimaryKey(userId);
    }
    
    @Override
    public Result updateMoneyPaid(TradeUserMoneyLog userMoneyLog) {
        //检验参数是否合法
        if(userMoneyLog == null || userMoneyLog.getUserId() == null || userMoneyLog.getOrderId() == null || userMoneyLog.getUseMoney() == null || userMoneyLog.getUseMoney().compareTo(BigDecimal.ZERO) <= 0) {
            CastException.cast(ShopCode.SHOP_REQUEST_PARAMETER_VALID);
        }
        //查询订单余额使用日志
        TradeUserMoneyLogExample userMoneyLogExample = new TradeUserMoneyLogExample();
        TradeUserMoneyLogExample.Criteria criteria = userMoneyLogExample.createCriteria();
        criteria.andOrderIdEqualTo(userMoneyLog.getOrderId());
        criteria.andUserIdEqualTo(userMoneyLog.getUserId());
        criteria.andMoneyLogTypeEqualTo(ShopCode.SHOP_USER_MONEY_PAID.getCode());
        int r = userMoneyLogMapper.countByExample(userMoneyLogExample);
        TradeUser user = userMapper.selectByPrimaryKey(userMoneyLog.getUserId());
        //扣减余额
        if(userMoneyLog.getMoneyLogType().intValue() == ShopCode.SHOP_USER_MONEY_PAID.getCode().intValue()) {
            if(r > 0) {
                //已经付款
                CastException.cast(ShopCode.SHOP_ORDER_PAY_STATUS_IS_PAY);
            }
            
            user.setUserMoney(new BigDecimal(user.getUserMoney()).substract(userMoneyLog.getUseMoney()).longValue());
        }
        //回退余额
        if(userMoneyLog.getMoneyLogType().intValue() == ShopCode.SHOP_USER_MONEY_REFUND.getCode().intValue()) {
            if(r == 0) {
                CastException.cast(ShopCode.SHOP_ORDER_PAY_STATUS_NO_PAY);
            }
            //防止多次退款
 			criteria.andMoneyLogTypeEqualTo(ShopCode.SHOP_USER_MONEY_REFUND.getCode());
            int r2 = userMoneyLogMapper.countByExample(userMoneyLogExample);
            if(r2 > 0) {
                 CastException.cast(ShopCode.SHOP_USER_MONEY_REFUND_ALREADY);
            }
            user.setUserMoney(new BigDecimal(user.getUserMoney()).add(userMoneyLog.getUseMoney()).longValue());
        }
        
        userMapper.updateByPrimaryKey(user);
        //记录订单余额使用日志
        userMoneyLog.setCreateTime(new Date());
        userMoneyLogMapper.insert(userMoneyLog);
        return new Result(ShopCode.SHOP_SUCCESS.getSuccess(), ShopCode.SHOP_SUCCESS.getMessage());
    }
}
```

* CouponServiceImpl

```java
@Component
@Service(interfaceClass = ICouponService.class)
public class CouponServiceImpl implements ICouponService {
    @AutoWired
    private TradeCouponMapper couponMapper;
    
    @Override
    public TradeCoupon findOne(Long couponId) {
        if(couponId == null) {
            CastException.cast(ShopCode.SHOP_REQUEST_PARAMETER_VALID);
        }
        return couponMapper.selectByPrimaryKey(couponId);
    }
    
    @Override
    public Result updateCouponStatus(TradeCoupon coupon) {
        if(coupon == null || coupon.getCouponId() == null) {
            CastException.cast(ShopCode.SHOP_REQUEST_PARAMETER_VALID);
        }
        
        couponMapper.updateByPrimaryKey(coupon);
        
        return new Result(ShopCode.SHOP_SUCCESS.getSuccess(), ShopCode.SHOP_SUCCESS.getMessage());
    }
}
```



### 3）校验订单

```java
@Reference
private IGoodsService goodsService;

@Reference
private IUserService userService;

private void checkOrder(TradeOrder order) {
    //校验订单是否存在
    if (order == null) {
        CastException.cast(ShopCode.SHOP_ORDER_INVALID);
    }
    //校验订单中的商品是否存在
    TradeGoods goods = goodsService.findOne(order.getGoodsId());
    if(goods == null) {
        CastException.cast(ShopCode.SHOP_GOODS_NO_EXIST);
    }
    //校验下单用户是否存在
    TradeUser user = userService.findOne(order.getUserId());
    if(user == null) {
        CastException.cast(ShopCode.SHOP_USER_NO_EXIST);
    }
    //校验订单金额是否合法
    if(order.getGoodsPrice().compareTo(goods.getGoodsPrice()) != 0) {
        CastException.cast(ShopCode.SHOP_ORDERAMOUNT_INVALID);
    }
    //校验订单商品数量是否合法
    if(order.getGoodsNumber() > goods.getGoodsNumber()) {
        CastException.cast(ShopCode.SHOP_GOODS_NUM_NOT_ENOUGH);
    }
}
```

### 4）生成预订单

```java
@Autowrid
private IDWorker idWorker;

@Reference
private ICouponService couponService;

@AutoWrid
private OrderMapper orderMapper;

private Long savePreOrder(TradeOrder order) {
    //设置订单状态不可见
    order.setOrderStatus(ShopCode.SHOP_ORDER_NO_CONFIRM.getCode());
    //设置订单ID
    long orderId = idWorker.nextId();
    order.setOrderId(orderId);
    //核算订单运费
    BigDecimal shippingFee = calculateShippingFee(order.getOrderAmount());
    if(order.getShippingFee().compareTo(shippingFee) != 0) {
        CastException.cast(ShopCode.SHOP_ORDER_SHIPPINGFEE_INVALID);
    }
    //核算订单总金额
    BigDecimal orderAmount = order.getGoodsPrice().multiply(new BigDecimal(order.getGoodsNumber()));
    orderAmount.add(shippingFee);
    if(order.getOrderAmount().compareTo(orderAmount) != 0) {
        CastException.cast(ShopCode.SHOP_ORDERAMOUNT_INVALID);
    }
    //判断用户是否使用余额
    BigDecimal moneyPaid = order.getMoneyPaid();
    if(moneyPaid != null) {
        int r = moneyPaid.compareTo(BigDecimal.ZERO);
        if(r == -1) {
            CastException.cast(ShopCode.SHOP_MONEY_PAID_LESS_ZERO);
        }
        
        if(r == 1) {
            TradeUser user = userService.findOne(order.getUserId());
            if(moneyPaid.compareTo(new BigDecimal(user.getUserMoney())) == 1) {
                CastException.cast(ShopCode.SHOP_MONEY_PAID_INVALID);
            }
        }
    } else {
        order.setMoneyPaid(BigDecimal.ZERO);
    }
    //判断用户是否使用优惠券
    Long couponId = order.getCouponId();
    if(couponId != null) {
        TradeCoupon coupon = couponService.findOne(couponId);
        if(coupon == null) {
            CastException.cast(ShopCode.SHOP_COUPON_NO_EXIST);
        }
        if(coupon.getIsUsed().intValue() == ShopCode.SHOP_COUPON_ISUSED.getCode().intValue()) {
            CastException.cast(ShopCode.SHOP_COUPON_ISUSED);
        }
    } else {
        order.setCouponPaid(BigDecimal.ZERO);
    }
    //核算订单支付金额
    BigDecimal payAmount = orer.getOrderAmout().subtract(order.getMoneyPaid()).substract(order.getCouponPaid());
    order.setPayAmount(payAmount);
    //设置订单下单时间
    order.setAddTime(new Date());
    //保存订单到数据库
    orderMapper.insert(order);
    //返回订单ID
    return orderId;
}

private BigDecimal calculateShippingFee(BigDecimal orderAmount) {
    if(orderAmount.compareTo(new BigDecimal(100)) == 1) {
        return BigDecimal.ZERO;
    } else P
        return new BigDecimal(10);
}
```

### 5）扣减库存

```java
private void reduceGoodsNum(TradeOrder order) {
    TradeGoodsNumberLog goodsNumberLog = new TradeGoodsNumberLog();
    goodsNumberLog.setOrderId(order.getOrderId());
    goodsNumberLog.setGoodsId(order.getGoodsId());
    goodsNumberLog.setGoodsNumber(order.getGoodsNumber());
    Result result = goodsService.reduceGoodsNum(goodsNumberLog);
    if(result.getSuccess().equals(ShopCode.SHOP_FAIL.getSuccess())) {
        CastException.cast(ShopCode.SHOP_REDUCE_GOODS_NUM_FAIL);
    }
}
```

### 6）使用优惠券

```java
private void updateCouponStatus(TradeOrder order) {
    if(order.getCouponId() != null) {
        TradeCoupon coupon = couponService.findOne(order.getCouponId());
        coupon.setOrderId(order.getOrderId());
        coupon.setIsUsed(ShopCode.SHOP_COUPON_ISUSED.getCode());
        coupon.setUseTime(new Date());
        
        //更新优惠券状态
        Result result = couponService.updateCouponStatus(coupon);
        if(result.getSuccess().equals(ShopCode.SHOP_FAIL.getSuccess())) {
            CastException.cast(ShopCode.SHOP_COUPON_USE_FAIL);
        }
    }
}
```

### 7）扣减余额

```java
private void reduceMoneyPaid(TradeOrder order) {
    if(order.getMoneyPaid() != null && order.getMoneyPaid().compareTo(BigDecimal.ZERO) == 1) {
        TradeUserMoneyLog userMoneyLog = new TradeUserMoneyLog();
        userMoneyLog.setOrderId(order.getOrderId());
        userMoneyLog.setUserId(order.getUserId());
        userMoneyLog.setUseMoney(order.getMoneyPaid());
        userMoneyLog.setMoneyLogType(ShopCode.SHOP_USER_MONEY_PAID.getCode());
        Result result = userService.updateMoneyPaid(userMoneyLog);
        if(result.getSuccess().equals(ShopCode.SHOP_FAIL.getSuccess())) {
            CastException.cast(ShopCode.SHOP_USER_MONEY_REDUCE_FAIL);
        }
    }
}
```

### 8）确认订单

```java
private void updateOrderStatus(TradeOrder order) {
    order.setOrderStatus(ShopCode.SHOP_ORDER_CONFIRM.getCode());
    order.setPayStatus(ShopCode.SHOP_ORDER_PAY_STATUS_NO_PAY.getCode());
    order.setConfirmTime(new Date());
    int r = orderMapper.updateByPrimaryKey(order);
    if(r == 0) {
    	CastException.cast(ShopCode.SHOP_ORDER_CONFIRM_FAIL);    
	}
}
```

## 4.2 失败补偿机制

### 4.2.1 消息发送方

```java
@AutoWired
private RocketMQTemplate rocketMQTemplate;

private void sendCancelOrder(String topic, String tag, String keys, String body) {
    Message message = new Message(topic, tag, keys, body.getBytes());
    try {
    	rocketMQTemplate.getProducer().send(message);
    } catch (MQClientException e) {
        e.printStackTrack();
    } catch (RemotingException e) {
        e.printStackTrack();
    } catch (MQBrokerException e) {
        e.printStackTrack();
    } catch (InterruptedException e) {
        e.printStackTrack();
    }
}
```



### 4.2.2 消息接收方

* 创建监听类，消费消息

```java
@Component
@RocketMQMessageListener(topic = "${mq.order.topic}", consumerGroup = "${mq.order.consumer.group.name}", messageModel = messageModel.BROADCASTING)
public class CancelOrderConsumer implements RocketMQListener<MessageExt> {
    @Override
    public void onMessage(MessageExt messageExt) {
        
    }
}
```

#### 1）回退库存

```java
@Component
@RocketMQMessageListener(topic = "${mq.order.topic}", consumerGroup = "${mq.order.consumer.group.name}", messageModel = messageModel.BROADCASTING)
public class CancelOrderConsumer implements RocketMQListener<MessageExt> {
    @Override
    public void onMessage(MessageExt messageExt) {
        //解析消息内容
        String msgId = messageExt.getMsgId();
        String tags = messageExt.getTags();
        String keys = messageExt.getKeys();
        String body = new String(messageExt.getBody(), "UTF-8");        
        
        
        try {
            //查询消息消费记录
            TradeMqConsumerLogKey primaryKey = new TradeMqConsumerLogKey();
            primaryKey.setMsgTag(tags);
            primaryKey.setMsgKey(keys);
            primaryKey.setGroupName(groupName);
			TradeMqConsumerLog mqConsumerLog = mqConsumerMapper.selectByPrimaryKey(primaryKey);
            //判断如果消费过...
			if(mqConsumer != null) {
                Integer status = mqConsumerLog.getConsumerStatus();
                //处理过...返回
                if(ShopCode.SHOP_MQ_MESSAGE_STATUS_SUCCESS.getCode().intValue() == status.intValue()) {
                    return;
                }
                //正在处理...返回
                if(ShopCode.SHOP_MQ_MESSAGE_STATUS_PROCESSING.getCode().intValue() == status.intValue()) {
                    return;
                }
                //处理失败
                if(ShopCode.SHOP_MQ_MESSAGE_STATUS_FAIL.getCode().intValue() == status.intValue()) {
                    //获得消息处理次数
                    Integer time = mqConsumerLog.getConsumerTimes();
                    if(time > 3) {
                        return;
                    }
                   mqConsumerLog.setConsumerStatus(ShopCode.SHOP_MQ_MESSAGE_STATUS_PROCESSING.getCode());
                   TradeMqConsumerLogExample example = new TradeMqConsumerLogExample();
                   TradeMqConsumerLogExample.Criteria criteria = example.createCriteria();
                    criteria.andMsgTagEqualTo(mqConsumerLog.getMsgTag());
                    criteria.andMsgKeyEqualTo(mqConsumerLog.getMsgKey());
                    criterial.andGroupNameEqualTo(groupName);
                    criterial.andConsumerTimesEqualTo(mqConsumerTimes());
                    int r = mqConsumerLogMapper.updateByExampleSelective(mqConsumerLog, example);
                }
            } else {
                //判断如果没有消费过... 
                mqConsumerLog = new TradeMqConsumerLog();
                mqConsumerLog.setMsgTag(tags);
                mqConsumerLog.setMsgKey(keys);
                mqConsumerLog.setConsumerStatus(ShopCode.SHOP_MQ_MESSAGE_STATUS_PROCESSING.getCode());
                mqConsumerLog.setMsgBody(body);
                mqConsumerLog.setMsgId(msgId);
                mqConsumerLog.setConsumerTimes(0);
                    
                //将此消息处理信息添加到数据库
                mqConsumerLogMapper.insert(mqConsumerLog);
            }

           	//回退库存
			MQEntity mqEntity = JSON.parseObject(body,MqEntity.class);
            Long goodsId = mqEntity.getGoodsId();
            TradeGoods goods = goodsMapper.selectByPrimaryKey(goodsId);
            goods.setGoodsNumber(goods.getGoodsNumber() + mqEntity.getGoodsNum());
            goodsMapper.updateByPrimaryKey(goods);
            //记录消息消费日志
            TradeGoodsNumberLog goodsNumberLog = new TradeGoodsNumberLog();
            goodsNumberLog.setOrderId(mqEntity.getGoodsNum());
            goodsNumberLog.setGoodsId(goodsId);
            goodsNumberLog.setGoodsNumber(mqEntity.getGoodsNum());
            goodsNumberLog.setLogTime(new Date());
            goodsNumberLogMapper.insert(goodsNumberLog);
            
            //将消息处理状态改为处理成功
            mqConsumerLog.setConsumerStatus(ShopCode.SHOP_MQ_MESSAGE_STATUS_SUCCESS.getCode());
            mqConsumerLog.setConsumerTimestamp(new Date());
            mqConsumerLogMapper.updateByPrimaryKey(mqConsumerLog);
        } catch (Exception e) {
            e.printStackTrack();
            TradeMqConsumerLogKey primaryKey = new TradeMqConsumerLogKey();
            primaryKey.setMsgTag(tags);
            primaryKey.setMsgKey(keys);
            primaryKey.setGroupName(groupName);
            TradeMqConsumerLog mqConsumerLog = mqConsumerLogMapper.selectByPrimaryKey(primaryKey);
            if(mqConsumerLog == null) {
                //数据库未有记录
                mqConsumerLog = new TradeMqConsumerLog();
                mqConsumerLog.setMsgTag(tags);
                mqConsumerLog.setMsgKey(keys);
                mqConsumerLog.setConsumerStatus(ShopCode.SHOP_MQ_MESSAGE_STATUS.FAIL.getCode());
                mqConsumerLog.setMsgBody(body);
                mqConsumerLog.setMsgId(msgId);
                mqConsumerLog.setConsumerTimes(1);
                mqConsumerLogMapper.insert(mqConsumerLog);
            } else {
                mqConsumerLog.setConsumerTimes(mqConsumerLog.getConsumerTimes()++);
                mqConsumerLogMapper.updateByPrimaryKeySelevtive(mqConsumerLog);
            }
        }
        
    }
}
```

#### 2）回退优惠券

```java
@Component
@RocketMQMessageListener(topic = "${mq.order.topic}", consumerGroup = "${mq.order.consumer.group.name}", messageModel = messageModel.BROADCASTING)
public class CancelOrderConsumer implements RocketMQListener<MessageExt> {
    @Override
    public void onMessage(MessageExt message) {
        try {
            //解析消息内容
            String body = new String(message.getBody(), "UTF-8");
            MQEntity mqEntity = JSON.ParseObject(body, MQEntity.class);
            //查询优惠券信息
            if(mqEntity.getCouponId != null) {
                TradeCoupon coupon = couponMapper.selectByPrimaryKey(mqEntity.getCouponId());
                //更改优惠券状态
                coupon.setUsedTime(null);
                coupon.setIsUsed(ShopCode.SHOP_COUPON_UNUSED.getCode());
                coupon.setOrderId(null);
                couponMapper.updateByPrimaryKey(coupon);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

#### 3）回退用户余额

```java
@Component
@RocketMQMessageListener(topic = "${mq.order.topic}", consumerGroup = "${mq.order.consumer.group.name}", messageModel = messageModel.BROADCASTING)
public class CancelOrderConsumer implements RocketMQListener<MessageExt> {
    @Override
    public void onMessage(MessageExt messageExt) {
        try {
            String body = new String(messageExt.getBody(), "UTF-8");
            MQEntity mqEntity = JSON.ParseObject(body, MQEntity.class);
            if(mqEntity.getUserMoney() != null && mqEntity.getUserMoney().compareTo(BigDecimal.ZERO) > 0) {
                //调用业务层 进行余额修改
                TradeUserMoneyLog userMoneyLog = new TradeUserMoneyLog();
                userMoneyLog.setUseMoney(mqEntity.getUserMoney());
                userMoneyLog.setMoneyLogType(ShopCode.SHOP_USER_MONEY_REFUND.getCode());
                userMoneyLog.setUserId(mqEntity.getUserId());
                userMoneyLog.setOrderId(mqEntity.getOrderId());
                userService.updateMoneyPaid(userMoneyLog);
            }
        } catch (Exception e) {
            e.printStackTrade();
        }
    }
}
```

#### 4）取消订单

```java
@Component
@RocketMQMessageListener(topic = "${mq.order.topic}", consumerGroup = "${mq.order.consumer.group.name}", messageModel = messageModel.BROADCASTING)
public class CancelOrderConsumer implements RocketMQListener<MessageExt> {
    @Override
    public void onMessage(MessageExt messageExt) {
        try {
            //解析消息内容
            String body = new String(messageExt.getBody(), "UTF-8");
            MQEntity mqEntity = JSON.ParseObject(body, MQEntity.class);
            if(mqEntity.getOrderId() != null) {
                //查询订单
                TradeOrder order = order.selectByPrimaryKey(mqEntity.getOrderId);
                //更新订单状态为取消
                order.setOrderStatus(ShopCode.SHOP_ORDER_MESSAGE_STATUS_CANCEL.getCode());
                orderMapper.updateByPrimaryKey(order);
       		}
        } catch (Exception e) {
            e.printStackTrade();
        }
    }
}
```

# 5.支付业务

## 5.1 创建支付订单

* 接口 IpayService

```java
public interface IPayService {
    
    Result createPayment(TradePay tradePay);
    
    Result callbackPayment(TradePay tradePay);
    
}
```

* 接口实现 IpayServiceImpl

```java
@Component
@Service(interfaceClass = IPayService.class)
public class PayServiceImpl implements IPayService {
    
    @Autowired
    private TradePayMapper tradePayMapper;
    
    @Autowired
    private IDWorker idWorker;
    
    @Override
    public Result createPayment(TradePay tradePay) {
        if(tradePay == null || tradePay.getOrderId() == null) {
            CastException.cast(ShopCode.SHOP_REQUEST_PARAMETER_VALID);
        }
        //判断订单支付状态
        TradePayExample example = new TradePayExample();
        TradePayExample.Criteria criteria = example.createCriteria();
        criteria.andOrderIdEqualTo(tradePay.getOrderId());
        criteria.andIsPaidEqualTo(ShopCode.SHOP_PAYMENT_IS_PAID.getCode());
        int r = tradePayMapper.countByExample(example);
        if(r > 0) {
            CastException.cast(ShopCode.SHOP_PAYMENT_IS_PAID);
        }
        //设置订单的状态为 未支付
        tradePay.setIsPaid(ShopCode.SHOP_ORDER_PAY_STATUS_NO_PAY.getCode());
        //保存支付订单
        tradePay.setPayId(idWorker.nextId());
        tradePayMapper.insert(tradePay);
        return new Result(ShopCode.SHOP_SUCCESS.getSuccess(),ShopCode.SHOP_SUCCESS.getMessage());
    }
}
```



## 5.2 支付回调

### 5.2.1 代码实现

* 实现 callbackPayment 方法

```java
@Autowired
private TradeMqProducerTempMapper tradeMqProducerTempMapper;

@Override
public Result callbackPayment(TradePay tradePay) {
    
    //判断用户支付状态
    if(tradePay.getIsPaid().intValue() != ShopCode.SHOP_ORDER_PAY_STATUS_IS_PAY.getCode().intValue()) {
		        CastException.cast(ShopCdoe.SHOP_PAYMENT_PAY_ERROR);
        return new Result(ShopCode.SHOP_FAIL.getSuccess(), ShopCode.SHOP_FAIL.getMessage());
    } else {
        //更新支付订单状态为 已支付
		Long payId = tradePay.getPayId();
        //判断支付订单是否存在
        TradePay pay = tradePayMapper.selectByPrimaryKey(payId);
        if(pay == null) {
            CastException.cast(ShopCode.SHOP_PAYMENT_NOT_FOUND);
        }
        pay.setIsPaid(ShopCode.SHOP_ORDER_PAY_STATUS_IS_PAY.getCode());
        int r = tradePayMapper.updateByPrimaryKey(pay);
        if(r == 1) {
            //创建支付成功的消息
			TradeMqProducerTemp tradeMqProducerTemp = new TradeMqProducerTemp();
            tradeMqProducerTemp.setId(String.valueOf(idWorker.nextId()));
            tradeMqProducerTemp.setGroupName(groupName);
            tradeMqProducerTemp.setMsgTopic(topic);
            tradeMqProducerTemp.setMsgTag(tag);
            tradeMqProducerTemp.setMsgKey(String.valueOf(tradePay.getPayId()));
            tradeMqProducerTemp.setMsgBody(JSON.toJSONString(tradePay));
            tradeMqProducerTemp.setCreateTime(new Date());
            //将消息持久化数据库
			mqProducerTempMapper.insert(tradeMqProducerTemp);
            
            //在线程池中进行处理
            /**
            //发送消息到MQ
			SendResult sendResult = sendMessage(topic, tag, String.valueOf(tradePay.getPayId()), tradeMqProducerTemp.getMsgBody());
            //等待发送结果，如果MQ接受到消息，删除发送成功的消息
            if(sendResult != null && sendResult.getSendStatus().equals(SendStatus.SEND_OK)) {
                mqProducerTempMapper.deleteByPrimaryKey(tradeMqProducerTemp.getId());
            }**/
        }
        return new Result(ShopCode.SHOP_SUCCESS.getSuccess(), ShopCode.SHOP_SUCCESS.getMessage());
    }
}
```

* 发送支付成功消息

```java
@Autowired
private RocketMQTemplate rocketMQTemplate;

private SendResult sendMessage(String topic, String tag, String key, String body) throw Exception {
    if(StringUtils.isEmpty(topic)) {
        CastException.cast(ShopCode.SHOP_MQ_TOPIC_IS_EMPTY);
    }
    if(StringUtils.isEmpty(body)) {
        CastException.cast(ShopCode.SHOP_MQ_MESSAGE_BODY_IS_EMPTY);
    }
    
    Message message = new Message(topic, tag, key, body.getBytes());
    SendResult sendResult = rocketMQTemplate.getProducer().send(message);
    return sendResult;

```

#### 线程池优化消息发送逻辑

* 创建线程池对象

```java
@Bean
public ThreadPoolTaskExecutor getThreadPool() {
    ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
    executor.setCorePoolSize(4);
    executor.setMaxPoolSize(8);
    executor.setQueueCapacity(100);
    executor.setKeepAliveSeconds(60);
    executor.setThreadNameOrefix("Pool-A");
    executor.setRejectedExecutionHandler(new ThreadPoolExecutor.callerRunsPolicy());
    executor.initialize();
    return executor;
}
```

* 使用线程池

```java
@AutoWired
private ThreadPoolTaskExecutor threadPoolTaskExecutor;

threadPoolTaskExecutor.submit(new Runnable() {
   	@Override
    public void run() {
        //发送消息到MQ
        SendResult result = null;
        try {
            SendResult sendResult = sendMessage(topic, tag, String.valueOf(tradePay.getPayId()), tradeMqProducerTemp.getMsgBody());
        } catch (Exception e) {
            e.printStackTrace();
        }
        //等待发送结果，如果MQ接受到消息，删除发送成功的消息
        if(sendResult != null && sendResult.getSendStatus().equals(SendStatus.SEND_OK)) {
            mqProducerTempMapper.deleteByPrimaryKey(tradeMqProducerTemp.getId());
        }
    }
});
```

### 5.2.2 处理消息

支付成功后，支付服务 payService 发送 MQ 消息，订单服务、用户服务、日志服务需要订阅消息进行处理

1. 订单服务修改订单状态为已支付
2. 日志服务记录支付日志
3. 用户服务负责给用户增加积分

以下用订单服务为例说明消息的处理情况

#### 1）配置RocketMQ属性值

```properties
mq.pay.topic=payTopic
mq.pay.consumer.group.name=pay_payTopic_group
```

#### 2）消费消息

* 在订单服务中，配置公共的消息处理类

```java
@Component
@RocketMQMessageListener(topic = "${mq.pay.topic}", consumerGroup = "${mq.pay.group.name}", messageModel = MessageModel.BROADCASTING)
public class PaymentListener implements RocketMQListener<MessageExt> {
    @Autowired
    private TradeOrderMapper orderMapper;
    
    @Override
    public void onMessage(MessageExt messageExt) {
        
        try {
            //解析消息内容
			String body = new String(messageExt.getBody(), "UTF-8");
            TradePay tradePay = JSON.parseObject(body, TradePay.class);
            //根据订单ID查询订单对象
			TradeOrder tradeOrder = orderMapper.selectByPrimaryKey(tradePay.getOrderId());
            //更改订单支付状态为 已支付
			tradeOrder.setPayStatus(ShopCode.SHOP_ORDER_PAY_STATUS_IS_PAY.getCode());
            //更新订单数据到数据库
            orderMapper.updateByPrimaryKey(tradeOrder);
        } catch (Exception e) {
            e.printStackTrade();
        }
    }
}
```

