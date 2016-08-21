
## Update User

更新用户信息，需要**附加验证信息**。

> 同时更新用户头像`avatar`和用户的名称`name`，`avatar`的值是指向本地文件`avatar.jpg`的相对路径。当不更新`avatar`的时候也可以只用`-d`传输数据。

```shell
curl https://askiapi.herokuapp.com/api/users/7 \
  -F "user[avatar]=@spec/assets/avatar.jpg" \
  -F "user[name]=Vincent Zhao" \
  -H "Access-Token: vzuTxIccqCtL3P2nKGYnmQ" \
  -H "Client: 6cZIvrVtjKPtniZugxbkKQ" \
  -H "Uid: vincent.zhao@askinow.com" \
  -iX PATCH

# 不更新头像
curl https://askiapi.herokuapp.com/api/users/7 \
  -d "user[name]=Vincent Zhao" \
  -H "Access-Token: vzuTxIccqCtL3P2nKGYnmQ" \
  -H "Client: 6cZIvrVtjKPtniZugxbkKQ" \
  -H "Uid: vincent.zhao@askinow.com" \
  -iX PATCH
```

> 返回结果，可以看到`avatar`文件已经上传到S3中，并返回了URL。

```json
{
  "id": 7,
  "avatar": {
    "url": "https://askiapi.s3.amazonaws.com/uploads/user/avatar/7/avatar.jpg"
  },
  "name": "Vincent Zhao",
  "email": "vincent.zhao@askinow.com",
  "provider": "email",
  "uid": "vincent.zhao@askinow.com",
  "nickname": null,
  "created_at": "2016-08-14T12:56:05.668Z",
  "updated_at": "2016-08-21T16:04:03.118Z",
  "image": null
}
```

Parameter        | Type      | Description
---------------- | --------- | -----------
`user[avatar]`   | `File`    | 头像文件，只允许`jpg`，`jpeg`，`png`和`gif`格式的文件
`user[name]`     | `String`  | 更新用户名称
`user[nickname]` | `String`  | 更新用户昵称（暂时用不到）

上述中括号的含义相当于JSON中的嵌套，即该接口传输的键值对都要放在`user`键之下。

### HTTP Request

`PUT/PATCH /users/:userid`