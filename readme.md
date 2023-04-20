## 酒店管理系统开发文档

一个简单的酒店管理系统，其中包括会员管理、房间类型管理、房间管理、预约订单管理、订单管理等功能
后端使用 springboot + mybatis-plus + mysql 自动生成 + 手写， 前端使用 light2f 在线开发工具生成的 react18 + antd5 单页项目

### 调用说明

为了方便，字段名就随意定义，接口都使用 post 请求方式， 以及任意域都可以访问（任何人都可以直接访问，可以用于前端项目练习接口）V
登录接口不需要校验 token，调用成功会返回正确的 token，其它接口会校验请求的 header 中是否有正确的 token 信息

接口 base 路径：

```text
https://test.light2f.com/test/
```

接口返回的数据结构：
```js
{
    code: 0, //非零表示有问题
    data: {}, //接口数据
    message: "", //消息
}
```

**注意** 每个分页查询都有固定分页参数，后续不再在每个查询条件中一一枚举
```ts
pageNo: number, //页数
pageSize: number //每页大小
```
**注意** 每个分页查询返回的数据结构中的 data 也是相同如下:
```ts
{
    current: number, //当前页数
    size: number, //每页大小
    total: number,//查询的总数
    records: any[], //查询出来的数据，后续分页查询出的数据只解释此单个对象
}
```

### 接口说明

下面是各个接口的路径、入参、返回数据的详情

#### 登录

```js
//路径
user/login 
//request params：  
{
    userAccount: 'admin', //帐号，固定 admin
    userPassword: '123456' //密码，固定 123456
} 
//数据结构中的 data
{
    token: '...', //令牌
    user: {} //个人信息
}
```

#### 会员管理

1. 添加或更新

```ts
//路径
HotelVip/insertOrUpdate 
//request params：  
{
    vipId?: number, //主键，有为更新
    vipName: string, //名称 
    vipPhone: string //手机，有唯一验证
} 
//数据结构中的 data
data: boolean //成功或失败
```

2. 删除

```ts
//路径
HotelVip/delete 
//request params：  
{
    vipId: number, //主键，可以按主键删除，也可以按其它条件
    vipName?: string, //名称 
    vipPhone?: string //手机
} 
//数据结构中的 data
data: boolean //成功或失败
```

3. 查询

```ts
//路径
HotelVip/search 
//request params：  
{
    vipName?: string, //名称 
    vipPhone?: string //手机 
} 
//records 对象结构
{
    vipId: number, //主键，可以按主键删除，也可以按其它条件
    vipName: string, //名称 
    vipPhone: string //手机
    createdAt: number //创建日期时间戳
} 
```

#### 房间类型管理

1. 添加或更新

```ts
//路径
HotelRoomType/insertOrUpdate 
//request params：  
{
    typeId?: number, //主键，有为更新
    typeName: string, //名称 
    typePrice: number, //单价
    typeArea: number, //面积
    typeUserCount: number //容纳人数
} 
//数据结构中的 data
data: boolean //成功或失败
```

2. 删除

```ts
//路径
HotelRoomType/delete 
//request params：  
{
    typeId: number, //主键，可以按主键删除，也可以按其它条件
    typeName: string, //名称 
    typePrice: number, //单价
    typeArea: number, //面积
    typeUserCount: number //容纳人数
} 
//数据结构中的 data
data: boolean //成功或失败
```

3. 查询

```ts
//路径
HotelRoomType/search 
//request params：  
{
    typeName?: string, //名称  
} 
//records 对象结构
{
    typeId: number, //主键
    typeName: string, //名称 
    typePrice: number, //单价
    typeArea: number, //面积
    typeUserCount: number, //容纳人数
    createdAt: number //创建日期时间戳
} 
```

#### 房间管理

1. 添加或更新

```ts
//路径
HotelRoom/insertOrUpdate 
//request params：  
{
    roomId?: number, //主键，有为更新
    roomNo: string, //房号，有唯一验证
    typeId: number, //房间类型主键 
} 
//数据结构中的 data
data: boolean //成功或失败
```
2. 删除

```ts
//路径
HotelRoom/delete 
//request params：  
{
    roomId: number, //主键，可以按主键删除，也可以按其它条件
} 
//数据结构中的 data
data: boolean //成功或失败
```

3. 查询

```ts
//路径
HotelRoom/search 
//request params：  
{
    roomNo?: string, //房号  
    typeId?: number //房房间类型表主鍵
} 
//records 对象结构
{
    roomId: number, //主键
    roomNo: string, //房号
    roomStatus: string, //房间状态，empty:空闲, ing: 正在使用（已入住）
    createdAt: number, //创建日期时间戳
    //房间类型表
    typeId: number, //主键
    typeName: string, //名称 
    typePrice: number, //单价
    typeArea: number, //面积
    typeUserCount: number //容纳人数 
} 
```

4. 选择房间类型的选项

 调用 **房间类型管理** - **查询**，参数：
```ts
{
    pageNo:1
    pageSize: 9999
}
```

#### 预约订单管理

1. 添加或更新

```ts
//路径
HotelPrebook/insertOrUpdate 
//request params：  
{
    prebookId?: number, //主键，有为更新
    vipId: number, //会员主键
    typeId: number, //房间类型主键 
    prebookStartedAt: date, //开始入住日期
    prebookEndedAt: date //结束日期
} 
//数据结构中的 data
data: boolean //成功或失败
```

2. 删除

```ts
//路径
HotelPrebook/delete 
//request params：  
{
    prebookId: number, //主键，可以按主键删除，也可以按其它条件
    vipId: number, //会员主键
    typeId: number, //房间类型主键 
    prebookStartedAt: date, //开始入住日期
    prebookEndedAt: date //结束日期
} 
//数据结构中的 data
data: boolean //成功或失败
```

3. 查询

```ts
//路径
HotelPrebook/search 
//request params：  
{
    vipName?: string, //会员名
    vipPhone?: string, //会员手机号  
} 
//records 对象结构
{
    prebookId: number, //主键
    prebookStartedAt: date, //开始入住日期
    prebookEndedAt: date， //结束日期
    prebookDayCount: number, //入住天数
    prebookPrice: number //总价
    prebookStatus: string //预定状态，book: 已预定, finish: 已转订单, cancel: 已取消
    createdAt: number, //创建日期时间戳
    //会员表
    vipId: number,
    vipName: string, //会员名
    vipPhone: string, //会员手机号  
    //房间类型表
    typeId: number, //主键
    typeName: string, //名称 
    typePrice: number, //单价
    typeArea: number, //面积
    typeUserCount: number //容纳人数 
} 
```

4. 取消预约
```ts
//路径
HotelPrebook/cancel 
//request params：  
{
    prebookId: number //主键
} 
//数据结构中的 data
data: boolean //成功或失败
```

5. 转为订单
```ts
//路径
HotelPrebook/toOrder 
//request params：  
{
    prebookId: number, //主键
    prebookStartedAt: date, //开始入住日期
    prebookEndedAt: date， //结束日期
    prebookDayCount: number, //入住天数
    prebookPrice: number //总价
    prebookStatus: string //预定状态
    createdAt: number, //创建日期时间戳
    //会员表
    vipId: number,
    vipName: string, //会员名
    vipPhone: string, //会员手机号  
    //房间类型表
    typeId: number, //主键
    typeName: string, //名称 
    typePrice: number, //单价
    typeArea: number, //面积
    typeUserCount: number //容纳人数 
    //房间信息
    roomId: number
} 
//数据结构中的 data
data: boolean //成功或失败
```

6. 转为订单选择房间的列表

 调用 **房间管理** - **查询**，参数：
```ts
{
    roomNo?: string, //房间号
    typeId: number, //房间类型主键
    roomStatus: 'empty', //固定值，空房
}
```

7. 选择会员的选项

 调用 **会员管理** - **查询**，参数：
```ts
{
    pageNo:1
    pageSize: 9999
}
```

8. 选择房间类型的选项

 调用 **房间类型管理** - **查询**，参数：
```ts
{
    pageNo:1
    pageSize: 9999
}
```

#### 订单管理

1. 添加或更新

```ts
//路径
HotelOrder/insertOrUpdate 
//request params：  
{
    orderId?: number, //主键，有为更新
    vipId: number, //会员主键
    typeId: number, //房间类型主键 
    roomId: number, //房间主键
    orderStartedAt: date, //开始日期
    orderEndedAt: date //结束日期
} 
//数据结构中的 data
data: boolean //成功或失败
```

2. 删除

```ts
//路径
HotelOrder/delete 
//request params：  
{
    orderId: number, //主键，可以按主键删除，也可以按其它条件
    vipId: number, //会员主键
    typeId: number, //房间类型主键 
    roomId: number, //房间主键
    orderStartedAt: date, //开始日期
    orderEndedAt: date //结束日期
} 
//数据结构中的 data
data: boolean //成功或失败
```

3. 查询

```ts
//路径
HotelOrder/search 
//request params：  
{
    vipName?: string, //会员名
    vipPhone?: string, //会员手机号  
} 
//records 对象结构
{
    orderId: number, //主键
    orderStartedAt: date, //开始日期
    orderEndedAt: date， //结束日期
    orderDayCount: number, //入住天数
    orderPrice: number //总价
    orderStatus: string //订单状态, ing: 进行中, finish: 已完成
    createdAt: number, //创建日期时间戳
    //会员表
    vipId: number,
    vipName: string, //会员名
    vipPhone: string, //会员手机号  
    //房间类型表
    typeId: number, //主键
    typeName: string, //名称 
    typePrice: number, //单价
    typeArea: number, //面积
    typeUserCount: number //容纳人数 
    //房间表
    roomId: number //主键
    roomNo: string //房间号
} 
```

4. 退房

```ts
//路径
HotelOrder/checkOut 
//request params：  
{
    orderId: string, //主键 
} 
//数据结构中的 data
data: boolean //成功或失败
```

5. 选择会员的选项

 调用 **会员管理** - **查询**，参数：
```ts
{
    pageNo:1
    pageSize: 9999
}
```

6. 选择房间类型的选项

 调用 **房间类型管理** - **查询**，参数：
```ts
{
    pageNo:1
    pageSize: 9999
}
```

7. 选择房间的选项

 调用 **房间管理** - **查询**，参数：
```ts
{
    typeId: number, //选择的房间类型主键
    orderId?: number, //订单主键，有的话，编辑时候会将当前订单使用的房间也查出来
    pageNo:1
    pageSize: 9999
}
```



