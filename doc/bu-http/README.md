# bu-http

HTTP 代理.

用于将系统内部的 HTTP 请求转换成浏览器对外的请求,
提供访问外部 HTTP 资源的能力.


## HTTPS 代理方式

由于代理无法处理 HTTPS 请求,
所以系统内部的程序必须发送 HTTP 明文请求给本代理.

对于外部 HTTPS 资源的访问, 使用在域名后缀添加 `.https` 的方式.
比如:

`http://github.com.https/test`

实际请求的是:

`https://github.com/test`

目前 (2021-07-02) 为止, `.https` 不是一个顶级域名, 因此在域名添加此后缀不会造成任何问题.
如果未来 `.https` 成为了有效的顶级域名, 那么只需要更换一个新的后缀即可.


TODO
