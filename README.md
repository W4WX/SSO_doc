# SSO_doc


## 注册流程

1. 客户端注册时强制走https;
2. 服务端接收到注册请求，校验用户名，密码等是否满足条件（用户名是否有特殊字符，密码复杂性等），并判断是否有重复；
3. 验证通过后，根据userID，生成随机盐salt，并保存到数据库；
4. 根据生成的salt，使用HMAC算法，生成密码摘要，和用户信息一起保存到用户注册表；
5. 返回注册成功，并将salt返回客户端，客户端做持久化保存；

## 登录流程

1. 判断是否有salt，如果没有，调用服务端请求getSalt，如果有则进行第3步；
2. 服务端根据userID，返回对应salt；
3. 客户端使用相同HMAC算法，生成密码摘要，与当前时间戳（精确到分钟）哈希（md5）后发送给服务端；
4. 服务端根据保存的密码，使用同样方式哈希（md5）后与参数对比，不一致时，取当前时间前后两分钟的时间戳再次对比，有相同结果即为登录成功。
5. 生成认证token，并更新到redis，设置有效时常（客户端有效token校验通过后，刷新有效时常）；
6. 返回认证token；
