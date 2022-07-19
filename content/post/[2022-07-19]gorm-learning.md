---
title: "GORM常用方法备注"
date: 2022-07-19
draft: false
keywords: ["golang","orm","gorm"]
description: "GORM常用方法备注"
tags: ["GORM"]
categories: ["GORM"]
author: "古道" 
---
## 官方网站
中文官方地址:[GORM指南](https://gorm.io/zh_CN/docs/index.html)
官方的指南可以作为参考目录随时查阅的。
## 结构定义
下面所有举例将以用户类型对应的user表为例。
```
type user struct {
	Id int `json:"Id"    gorm:"column:Id"   `
	Name string `json:"Name"    gorm:"column:Name"   `
	Age int `json:"Age"    gorm:"column:Age"   `
	Birthday string`json:"Birthday"    gorm:"column:Birthday"   `
}

func (t user) TableName() string {
	return "tb_user"
}
```
个人倾向于将所有字段的首字母大小，并且json和数据库字段都跟实体一致。
数据库表名可以使用TableName自定义。
下文所使用的user为单个用户对象，users为用户列表。
### 指定使用的表
`.Model`只要类型正确就会安装类型对应的表查询，如果不能根据后续的查询结果判断出是那个表时，就需要指定Model类型。如：
.Create(&user)、.Find(&users)、.Take(&user)等都可以根据查询结果判断出对应的表，
像.Update("name","xxx")、Count(&count)不能推断出表的就需要指定类型，比如：db.Model(&user).Update("","")
Table 和 Select 也可以作为表的推断依据。
### 条件查询
`Where` 有多种方法可以作为条件查询，个人常用SQL加参数的形式，优势在于可以根据条件拼接查询条件。
如：db.Where("name = ? AND age >= ?", "jinzhu", "22").Find(&users)
其中的参数可以通过外部传入，动态拼接,其中的args参数为多参数。

```
sql:="1=1"
var args []interface{}
if len(name)>0{
	sql+=" And Name=?"
	args=append(args,name)
}
db.Where(sql,args...).Find(&users)
```
### 查询结果
查询除了按照表结构全查的情况其实用的最多的是指定字段查询，可以使用指定字段名查询，如：
```
db.Select("name", "age").Find(&users)
```
也可以指定结果类型查询，需要先定义要查询结果struct，然后用Scan(&result)返回。
如：
```
type Result struct {
  ID   int
  Name string
  Age  int
}

var result Result
db.Raw("SELECT id, name, age FROM users WHERE name = ?", 3).Scan(&result)
```
## 自定义查询
原生SQL可以更灵活的执行单表多表查询
有结果的可以用 Raw("")加Scan(&result)形式，
无结果的可以用 Exec("")
## 我的常用
- 批量插入 
```
db.
Create(&users)
```
- 冲突更新 Clauses
```
db.
Clauses(clause.OnConflict{DoNothing: true}).
Create(&users)
```
- 单独更新 
```
MyDb.Model(&user{}).Where("Id=?", id).Updates(user{
			UpdateTime: time.Now().Format("2006-01-02 15:04:05")
		})
```
- 限量查询
```
	db.
		Select("Id").
		Where("Birthday>'2019-01-01 00:00:00' "). //2019年以后的
		Order("Age  DESC").
		Limit(count).
		Find(&users)
```
- 分页 
```
Raw 下的Limit()会失效，需要自行拼接，可以使用Sprintf格式化，注意要放注入
fmt.Sprintf(" ORDER BY Id DESC LIMIT %d,%d ", (pageIndex-1)*pageSize, pageSize)
```