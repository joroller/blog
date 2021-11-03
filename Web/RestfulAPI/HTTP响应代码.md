# HTTP 响应码

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
