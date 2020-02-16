# 简单使用

### 普通分页

```java
/**
 * 分页查询
 * @param page 当前页
 * @param size 每页显示记录数
 * @return
 */
@Override
public PageInfo<Brand> findPage(Integer page, Integer size) {

    /**
     * 分页实现后，后面的查询紧跟集合查询
     */
    //分页实现
    PageHelper.startPage(page, size);
    //集合查询
    List<Brand> brands = brandMapper.selectAll();
    //封装起来返回
    return new PageInfo<>(brands);
}
```

### 分页+条件查询

```java
/**
 * 条件 + 分页查询
 * @param brand
 * @param page 当前页
 * @param size 每页显示记录数
 * @return
 */
@Override
public PageInfo<Brand> findPage(Brand brand, Integer page, Integer size) {
    //分页
    PageHelper.startPage(page,size);
    //条件
    Example example = createExample(brand);
    //集合
    List<Brand> brands = brandMapper.selectByExample(example);
    return new PageInfo<>(brands);
}
```