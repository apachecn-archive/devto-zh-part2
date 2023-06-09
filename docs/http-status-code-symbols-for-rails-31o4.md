# Rails 的 HTTP 状态代码符号

> 原文：<https://dev.to/daviducolo/http-status-code-symbols-for-rails-31o4>

一个非常有用的小命令，用于获取 HTTP 响应代码列表及其 Ruby on Rails 符号映射。

```
 Rack::Utils::HTTP_STATUS_CODES 
```

Enter fullscreen mode Exit fullscreen mode

结果是:

```
{100=>"Continue",
 101=>"Switching Protocols",
 102=>"Processing",
 200=>"OK",
 201=>"Created",
 202=>"Accepted",
 203=>"Non-Authoritative Information",
 204=>"No Content",
 205=>"Reset Content",
 206=>"Partial Content",
 207=>"Multi-Status",
 208=>"Already Reported",
 226=>"IM Used",
 300=>"Multiple Choices",
 301=>"Moved Permanently",
 302=>"Found",
 303=>"See Other",
 304=>"Not Modified",
 305=>"Use Proxy",
 306=>"Reserved",
 307=>"Temporary Redirect",
 308=>"Permanent Redirect",
 400=>"Bad Request",
 401=>"Unauthorized",
 402=>"Payment Required",
 403=>"Forbidden",
 404=>"Not Found",
 405=>"Method Not Allowed",
 406=>"Not Acceptable",
 407=>"Proxy Authentication Required",
 408=>"Request Timeout",
 409=>"Conflict",
 410=>"Gone",
 411=>"Length Required",
 412=>"Precondition Failed",
 413=>"Request Entity Too Large",
 414=>"Request-URI Too Long",
 415=>"Unsupported Media Type",
 416=>"Requested Range Not Satisfiable",
 417=>"Expectation Failed",
 422=>"Unprocessable Entity",
 423=>"Locked",
 424=>"Failed Dependency",
 425=>"Reserved for WebDAV advanced collections expired proposal",
 426=>"Upgrade Required",
 427=>"Unassigned",
 428=>"Precondition Required",
 429=>"Too Many Requests",
 430=>"Unassigned",
 431=>"Request Header Fields Too Large",
 500=>"Internal Server Error",
 501=>"Not Implemented",
 502=>"Bad Gateway",
 503=>"Service Unavailable",
 504=>"Gateway Timeout",
 505=>"HTTP Version Not Supported",
 506=>"Variant Also Negotiates (Experimental)",
 507=>"Insufficient Storage",
 508=>"Loop Detected",
 509=>"Unassigned",
 510=>"Not Extended",
 511=>"Network Authentication Required"} 
```

Enter fullscreen mode Exit fullscreen mode

以一种更开发者的方式:)

```
 Status Code Symbol
1xx Informational
100 :continue
101 :switching_protocols
102 :processing

2xx Success
200 :ok
201 :created
202 :accepted
203 :non_authoritative_information
204 :no_content
205 :reset_content
206 :partial_content
207 :multi_status
226 :im_used

3xx Redirection
300 :multiple_choices
301 :moved_permanently
302 :found
303 :see_other
304 :not_modified
305 :use_proxy
307 :temporary_redirect

4xx Client Error
400 :bad_request
401 :unauthorized
402 :payment_required
403 :forbidden
404 :not_found
405 :method_not_allowed
406 :not_acceptable
407 :proxy_authentication_required
408 :request_timeout
409 :conflict
410 :gone
411 :length_required
412 :precondition_failed
413 :request_entity_too_large
414 :request_uri_too_long
415 :unsupported_media_type
416 :requested_range_not_satisfiable
417 :expectation_failed
422 :unprocessable_entity
423 :locked
424 :failed_dependency
426 :upgrade_required

5xx Server Error
500 :internal_server_error
501 :not_implemented
502 :bad_gateway
503 :service_unavailable
504 :gateway_timeout
505 :http_version_not_supported
507 :insufficient_storage
510 :not_extended 
```

Enter fullscreen mode Exit fullscreen mode