1.概念
=
spring data jpa是spring data模块的一个模块，依赖hibernate jpa实现，是一种orm技术（Object Relational Mapping）  
![定位](../../../../image/spring/springdata/jpa/springjpa定位.png "定位")

特点：
只需要定义接口，集成jpa提供的接口就可以，不需要自己编写接口实现类。

2.基本使用流程  
=  
* 添加依赖（pom.xml）
* 添加数据源（配置数据库连接，application.properties）
* 定义接口（编写Dao层）
* 编写实体类（Entity）
* 测试代码

3.spring data jpa接口继承体系
=
![继承体系](../../../../image/spring/springdata/jpa/jpa继承体系结构.png "继承体系")

4.spring data jpa原理
=



根据spring data jpa继承体系，看看各接口分别可以用来干什么：

5.Repository接口
=
* 方法名称命名规则查询  
    findBy(关键字)+属性名称(属性名称的首字母大写)+查询条件(首字母大写)  
    | 关键字 | 方法名称 | 对应sql条件 |
    | :----:| :----: | :----: |
    | And | findByNameAndPwd | where name= ? and pwd =? |
    | Or | findByNameOrSex | where name= ? or sex=? |
    | Is,Equal | findById | findByIdEquals |
    |Between	|findByIdBetween	|where id between ? and ?
    |LessThan	|findByIdLessThan	|where id < ?
    |LessThanEqual	|findByIdLessThanEquals	|where id <= ?
    |GreaterThan	|findByIdGreaterThan	|where id > ?
    |GreaterThanEqual	|findByIdGreaterThanEquals	|where id > = ?
    |After	|findByIdAfter	|where id > ?
    |Before	|findByIdBefore	|where id < ?
    |IsNull	|findByNameIsNull	|where name is null
    |isNotNull,Not Null	|findByNameNotNull	|where name is not
    |Like	|findByNameLike	|where name like ?
    |NotLike	|findByNameNotLike	|where name not like ?
    |StartingWith	|findByNameStartingWith	|where name like '?%'
    |EndingWith	|findByNameEndingWith	|where name like '%?'
    |Containing	|findByNameContaining	|where name like '%?%'
    |OrderBy	|findByIdOrderByXDesc	|where id=? order by x desc
    |Not	|findByNameNot	|where name <> ?
    |In	|findByIdIn(Collection<?> c)	|where id in (?)
    |NotIn	|findByIdNotIn(Collection<?> c)	|where id not in (?)
    |True	|findByAaaTue	|where aaa = true
    |False	|findByAaaFalse	|where aaa = false
    |IgnoreCase	|findByNameIgnoreCase	|where UPPER(name)=UPPER(?)

* 基于@Query 注解的查询  
    * 通过 JPQL 语句查询  
    ```
    public interface UsersDao extends Repository<Users, Integer> {	
        //使用@Query注解查询
        @Query(value="from Users where username = ?")
        List<Users> queryUserByNameUseJPQL(String name);
        
        @Query("from Users where username like ?")
        List<Users> queryUserByLikeNameUseJPQL(String name);
        
        @Query("from Users where username = ? and userage >= ?")
        List<Users> queryUserByNameAndAge(String name,Integer age);	
    }
    ```  
    * 通过 SQL 语句查询  
    ```
    public interface UsersDao extends Repository<Users, Integer> {
        //使用@Query注解查询SQL
        //nativeQuery:默认的是false.表示不开启sql查询。是否对value中的语句做转义。
        @Query(value="select * from t_users where username = ?",nativeQuery=true)
        List<Users> queryUserByNameUseSQL(String name);

        @Query(value="select * from t_users where username like ?",nativeQuery=true)
        List<Users> queryUserByLikeNameUseSQL(String name);
        
        @Query(value="select * from t_users where username = ? and userage >= ?",nativeQuery=true)
        List<Users> queryUserByNameAndAgeUseSQL(String name,Integer age);
        
    }
    ```  
    * 通过@Query 注解完成数据更新
    ```
    public interface UsersDao extends Repository<Users, Integer> {
        @Query("update Users set userage = ? where userid = ?")
        @Modifying //@Modifying当前语句是一个更新语句
        void updateUserAgeById(Integer age,Integer id);
    }
    ```  

6.CrudRepository接口  
=  
实现对数据的增删改查操作  
```
创建dao：
public interface UsersDao extends CrudRepository<Users, Integer> {
	
}

使用dao完成增删改查：
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class RepositoryTest {

	@Autowired
	private UsersDao usersDao;
	
	/**
	 * 添加单条数据
	 */
	@Test
	public void test1(){
		Users user = new Users();
		user.setUserage(21);
		user.setUsername("赵小丽");
		this.usersDao.save(user);
	}
	
	/**
	 * 批量添加数据
	 */
	@Test
	public void test2(){
		Users user = new Users();
		user.setUserage(21);
		user.setUsername("赵小丽");
		
		Users user1 = new Users();
		user1.setUserage(25);
		user1.setUsername("王小虎");
		
		List<Users> list= new ArrayList<>();
		list.add(user);
		list.add(user1);
		
		this.usersDao.save(list);
		
	}
	
	/**
	 * 根据ID查询单条数据
	 */
	@Test
	public void test3(){
		Users users = this.usersDao.findOne(13);
		System.out.println(users);
	}
	
	/**
	 * 查询全部数据
	 */
	@Test
	public void test4(){
		List<Users> list = (List<Users>)this.usersDao.findAll();
		for (Users users : list) {
			System.out.println(users);
		}
	}
	
	/**
	 * 删除数据
	 */
	@Test
	public void test5(){
		this.usersDao.delete(13);
	}
	
	/**
	 * 更新数据 方式一
	 */
	@Test
	public void test6(){
		Users user = this.usersDao.findOne(12);
		user.setUsername("王小红");
		this.usersDao.save(user);
	}
	
	/**
	 * 更新数据 方式二
	 */
	@Test
	@Transactional
	@Rollback(false)
	public void test7(){
		Users user = this.usersDao.findOne(12);//持久化状态的
		user.setUsername("王小小");
	}
}

```  
7.PagingAndSortingRepository 接口
=  
* 分页处理  
* 排序的处理  


8.JpaRepository 接口
=  



9.JpaSpecificationExecutor 接口
=  



10.自定义Repository接口
=  



11.关联映射操作
* 一对一的关联关系  
* 一对多的关联关系  
* 多对多的关联关系