## Cookie
### 一、Cookie 简介
`Cookie` 本质上就是浏览器里面存储的一个很小的文本文件，内部以键值对的方式来存储  
向同一个域名下发送请求，都会携带相关的 `Cookie`，服务器拿到 `Cookie` 进行解析，便能拿到客户端的状态  
服务端可以通过响应头中的 `Set-Cookie` 字段来对客户端写入`Cookie`

```HTTP
// 请求头
Cookie: key1=value1; key2=value2
// 响应头
Set-Cookie: key1=name1; domain=www.test.com; path=/; secure; HttpOnly
Set-Cookie: key2=name2; Max-Age=3600
```
### 二、Cookie 属性
#### 生存周期
`Cookie` 的有效期可以通过 `Expires` 和 `Max-Age` 来设置

- `Expires`：过期时间，`Date` 型时间点，或 `Session` 代表 `会话Cookie`
- `Max-Age` ：时间间隔，单位是秒。为负数则表示是一个 `会话Cookie`

假如 `Expires` 和 `Max-Age` 都存在，`Max-Age` 优先级更高

#### 作用域
`Cookie` 的作用域通过 `Domain` 和 `Path` 来设置

- `Domain`：绑定 `Cookie` 域名
- `Path`：绑定域名下的路径，`/` 表示域名下的任意路径都允许使用 `Cookie`

#### 安全相关
- `Secure`：标记为 `Secure` 的 `Cookie` 只应通过被 `HTTPS` 协议加密过的请求发送给服务端
- `HttpOnly`：设置 `HTTPOnly` 属性可以防止客户端脚本通过  `document.cookie` 等方式访问 `Cookie`，有助于避免 `XSS` 攻击
- `SameSite`：`SameSite` 属性可以让 `Cookie` 在跨站请求时不会被发送，从而可以阻止`跨站请求伪造攻击（CSRF）`
  - **Strict**：只发送相同站点请求的 `Cookie`
  - **Lax**：允许`部分第三方请求`携带 `Cookie`
  - **None**：无论是否跨站都会发送 `Cookie`

### 三、Cookie 的缺点
- 容量缺陷：`Cookie` 的体积上限只有 `4KB`，只能用来存储少量的信息
- 性能缺陷：不管域名下面的某一个地址需不需要，请求都会携带上完整的 `Cookie`，会造成性能浪费，可以通过 `Domain`和 `Path` 指定作用域来解决
- 安全缺陷：
  - `Cookie` 可以被 JS 脚本获取，`HttpOnly` 解决
  - 通过 `Cookie` 进行 `CSRF` 攻击，`SameSite` 解决