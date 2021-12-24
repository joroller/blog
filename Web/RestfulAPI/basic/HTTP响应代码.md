# HTTP 响应码
## 204 No Content

**规范**：

The 204 (No Content) status code indicates that the server has successfully fulfilled the request and that there is no additional content to send in the response payload body. Metadata in the response header fields refer to the target resource and its selected representation after the requested action was applied.

For example, if a 204 status code is received in response to a PUT request and the response contains an ETag header field, then the PUT was successful and the ETag field-value contains the entity-tag for the new representation of that target resource.

The 204 response allows a server to indicate that the action has been successfully applied to the target resource, while implying that the user agent does not need to traverse away from its current "document view" (if any). The server assumes that the user agent will provide some indication of the success to its user, in accord with its own interface, and apply any new or updated metadata in the response to its active representation.

For example, a 204 status code is commonly used with document editing interfaces corresponding to a "save" action, such that the document being saved remains available to the user for editing. It is also frequently used with interfaces that expect automated data transfers to be prevalent, such as within distributed version control systems.

A 204 response is terminated by the first empty line after the header fields because it cannot contain a message body.

A 204 response is cacheable by default; i.e., unless otherwise indicated by the method definition or explicit cache controls (see Section 4.2.2 of [RFC7234]).

**参考**：[204 No Content](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/204)

**举例**： 。

## 409 Conflict

**规范**：

The 409 (Conflict) status code indicates that the request could not
be completed due to a conflict with the current state of the target
resource.  This code is used in situations where the user might be
able to resolve the conflict and resubmit the request.  The server
SHOULD generate a payload that includes enough information for a user
to recognize the source of the conflict.

Conflicts are most likely to occur in response to a PUT request.  For
example, if versioning were being used and the representation being
PUT included changes to a resource that conflict with those made by
an earlier (third-party) request, the origin server might use a 409
response to indicate that it can't complete the request.  In this
case, the response representation would likely contain information
   useful for merging the differences based on the revision history.

**参考**：[409 Conflict](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/409)

**举例**： 当给资源重命名时为了保证资源名唯一，那么必须进行检查。当接收到重命名请求时，如果
检查到服务端已经存在相同的名字时，则会拒绝重命名请求，此时可以返回409与相对应的描述message给客户端。


## 参考资料

[HTTP响应代码](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status)
