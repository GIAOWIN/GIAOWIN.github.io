# 通用Mapper使用笔记

## 使用方法

1.在SpringBoot启动类上添加tk包下的@MapperScan注解

2.dao接口继承Mapper<T>

# 常用方法

方法中但凡带有Selective，就会忽略空值

### 增

insert

insertSelective

### 删



### 改

update(T)

updateByPrimayKey(T)

### 查

根据ID查询 selectByPrimayKey(ID)

根据对象查询 select(T)



# 常用注解