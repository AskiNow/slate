## Education Experience Object

> 一个用户教育经历对象

```json
{
  "id": 2,
  "school_name": "Peking University",
  "degree": "Bachelor of Science",
  "field_of_study": "Computer Science and Technology",
  "started_at": "2012-09-01",
  "ended_at": "2016-07-01",
  "description": "During my undergraduate study at Peking University, I ...",
  "profile_id": 1,
  "created_at": "2016-08-13T09:20:16.161Z",
  "updated_at": "2016-08-13T09:20:27.095Z"
}
```

Parameter         | Type      | Description
----------------- | --------- | -----------
`id`              | `Integer` | 教育经历对象在数据表中的唯一ID
`profile_id`      | `Integer` | 关联的profile对象的ID（不建议使用，后续可能会不显示）
`school_name`     | `String`  | 学校名称
`degree`          | `String`  | 学位名称
`field_of_study`  | `String`  | 专业名称
`started_at`      | `Date`    | 教育经历的开始日期
`ended_at`        | `Date`    | 教育经历的结束日期
`description`     | `String`  | 用户对教育经历的描述
`created_at`      | `Date`    | 教育经历创建入库的时间戳
`updated_at`      | `Date`    | 教育经历上次更新的时间戳