基础注解：
    @Entity：对象会成为被JPA管理的实体，映射到指定的数据库表  

    @Table：指定数据库的表名  

    @Id：数据库主键  

    @IdClass：利用外部类的联合主键  

    @GeneratedValue：主键生成策略  

    @Basic：表示属性是到数据库表的字段的映射，如果实体的字段上
    没有任何注解，默认即为@Basic    

    @Transient：表示该属性并非一个到数据库表的字段的映射，表示
    非持久化属性，与@Basic作用相反，JPA映射数据库的时候忽略它  

    @Column：定义该属性对应数据库中的列名

    @Temporal  

    @Enumerated

    @Lob

关联关系注解  
@JoinColumn  

@OneToOne  

@OneToMany  

@ManyToOne  

@ManyToMany  

@JoinTable  

@OrderBy  
