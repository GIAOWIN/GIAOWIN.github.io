# 通用Mapper使用笔记

## 使用方法

1.在SpringBoot启动类上添加tk包下的@MapperScan（“继承了Mapper的接口路径”）注解

2.dao接口继承Mapper<T>

# 常用方法(service)

```java

    /**
     * 增加一个品牌
     * @param brand 品牌对象
     */
    void addBrand(Brand brand);

    /**
     * 删除一个品牌
     * @param id 品牌对象id
     */
    void deleteBrand(Integer id);

    /**
     * 修改一个品牌
     * @param brand 品牌对象
     */
    void updateBrand(Brand brand);

    /**
     * 根据ID查询 Brand【品牌】
     * @return 品牌集合
     */
    Brand findById(Integer id);

    /**
     * 根据品牌信息多条件查询 Brand【品牌】
     * @param brand 品牌
     * @return
     */
    List<Brand> findList(Brand brand);

    /**
     * 分页查询
     * @param page 当前页
     * @param size 每页显示记录数
     * @return
     */
    PageInfo<Brand> findPage(Integer page,Integer size);

    /**
     *
     * 条件搜索 + 分页查询
     * @param brand
     * @param page 当前页
     * @param size 每页显示记录数
     * @return
     */
    PageInfo<Brand> findPage(Brand brand,Integer page,Integer size);

    /**
     * 查询所有 Brand【品牌】
     * @return 品牌集合
     */
    List<Brand> findAll();
```



# 常用方法(serviceImpl)

```java
@Autowired
private BrandMapper brandMapper;

/**
 * 条件构建
 * @param brand
 * @return
 */
public Example createExample(Brand brand){
    //自定义搜索对象
    Example example = new Example(Brand.class);
    //条件构造器
    Example.Criteria criteria = example.createCriteria();

    //如果品牌对象不为空
    if(null != brand){
        //根据名字模糊查询
        if(StringUtil.isNotEmpty(brand.getName())){
            /**
             * 1.属性名
             * 2.占位符参数，即搜索条件
             */
            criteria.andLike("name","%"+brand.getName()+"%");
        }
        //根据名字首字母查询
        if(StringUtil.isNotEmpty(brand.getLetter())){
            criteria.andEqualTo("letter",brand.getLetter());
        }
    }
    return example;
}

@Override
public void addBrand(Brand brand) {
    brandMapper.insertSelective(brand);
}

@Override
public void deleteBrand(Integer id) {
    brandMapper.deleteByPrimaryKey(id);
}

@Override
public void updateBrand(Brand brand) {
    brandMapper.updateByPrimaryKeySelective(brand);
}

/**
 * 根据 id 查询
 * @param id
 * @return
 */
@Override
public Brand findById(Integer id) {
    return brandMapper.selectByPrimaryKey(id);
}

/**
 * 条件查询
 * @param brand 品牌
 * @return
 */
@Override
public List<Brand> findList(Brand brand) {
    Example example = createExample(brand);
    return brandMapper.selectByExample(example);
}

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


/**
 * 查询所有 Brand【品牌】
 * @return 品牌集合
 */
@Override
public List<Brand> findAll() {
    /**
     * 直接调用父接口的方法
     */
    return brandMapper.selectAll();
}
```



