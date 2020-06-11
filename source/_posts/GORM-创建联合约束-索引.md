---
title: GORM 创建联合约束/索引
date: 2020-06-11 11:50:55
categories: GORM 
tags:
- GORM
- Golang
---

### GROM创建联合索引

之前提到一个联合约束，那么根据需求再次做一个演示：

```go
type Demo struct {
	ID           uint          `gorm:"primary_key"`
	CreatedAt    time.Time
	UpdatedAt    time.Time
	DeletedAt    *time.Time    `gorm:"index;unique_index:name_d"`
	Name     string            `gorm:"unique_index:name_d"`
	Status       int         
}
```


通过demo 迁移后，`deleted_at` 与 `name` 会形成一个联合约束。

-OK,完结-