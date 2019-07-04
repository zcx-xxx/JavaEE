# JDBC

## JDBC概述

**1. JDBC访问数据库的步骤**

* 加载一个driver驱动
* 创建一个数据库连接
* 创建SQL命令发送器Statement
* 通过Statement发送SQL命令并得到结果
* 处理结果
* 关闭数据库资源（ResultSet，Statement，Connection）

## JDBC基本操作

**1. 使用executeUpdate（）方法对数据库进行增加，删除和修改**

~~~java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.Statement;

public class TestInsert {

	public static void main(String[] args) throws Exception {

		//1. 加载对应数据库的驱动
		String className = "com.mysql.jdbc.Driver";
		Class.forName(className);          //作用：第一次加载类
        
		//2. 和数据库服务器建立连接
		String url = "jdbc:mysql://localhost:3306/test1";
		String usr = "root";
		String pwd = "123456";
		
		Connection conn = DriverManager.getConnection(url, usr, pwd);
		
		//3. 获取SQL命令发送器
		Statement stmt = conn.createStatement();
		
		//4. 使用SQL命令发送器发送命令并且得到结果
//		String sql = "insert into qqinfo values('zcx3', 'haha', '6528974')";
		String sql = "update qqinfo set hobby = 'papa' where QQNum='1307125643'";
		
		//对于更新，插入，删除都用该方法，不同的仅仅是SQL语句，n返回的是影响的行数
		int n = stmt.executeUpdate(sql);
		
		//5. 处理结果
		if(n <= 0) {
			System.out.println("添加失败");
		} else {
			System.out.println("添加成功");
		}
		//6. 关闭各种数据库资源
		stmt.close();
		conn.close();
	}
}
~~~

> 注意：
>
> 1. 在java中成员变量有默认值，局部变量没有默认值

**2. 使用executeQuery（）方法对数据库进行查询操作**

~~~java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

public class TestSelect {

	public static void main(String[] args) throws Exception {

		//1. 加载对应数据库的驱动
		String className = "com.mysql.jdbc.Driver";
		Class.forName(className);          //作用：第一次加载类
		//2. 和数据库服务器建立连接
		
		String url = "jdbc:mysql://localhost:3306/test1";
		String usr = "root";
		String pwd = "123456";
		
		Connection conn = DriverManager.getConnection(url, usr, pwd);
		
		//3. 创建SQL命令发送器
		Statement stmt = conn.createStatement();
		
		//4. 使用SQL命令发送器发送命令并且得到结果
		String sql = "select * from qqinfo";
		
		//对于查询使用该方法
		ResultSet rs = stmt.executeQuery(sql);
		
		//5. 处理结果
		while(rs.next()) {
			//注意该处的下标并不是从0开始的，而是从1开始
			String name = rs.getString(1);
			String hobby = rs.getString(2);
			String qqNum = rs.getString(3);
			
			System.out.println(name + " " + hobby + " " + qqNum);
		}
		//6. 关闭各种数据库资源
		rs.close();
		stmt.close();
		conn.close();
	}
}
~~~

> 注意：
>
> ResultSet：
>
> 1. ResultSet对象是executeQuery（）方法的返回值，他被称为结果集，他代表符合SQL语句条件的所有行，并且他通过一系列getxxx（）方法对当前行的不同列中的数据进行访问。
> 2. ResultSet对象自动维护指向当前数据行的游标，游标初始位置指向第一条记录的上一个位置，可以使用next（）方法执行下一个位置（该方法返回值为Boolean，存在下一条记录返回true，否则返回false）。如果想要访问该结果集中所有的数据，可以使用while循环进行遍历，遍历结束之后游标指向最后一条记录的下一个位置。
> 3. ResultSet默认情况下是只能向下移动游标的，但是可以通过设置相应的参数使其可以向上，向下，或者直接跳到指定的行。

**3. 将数据库中的数据查询出来之后进行保存**

在一些时候将数据库中的数据查询出来之后，不是仅仅进 行输出，在后边的过程中可能会做其他的操作，因此经常将其保存在一个集合中。集合中的元素为一个实体类，数据库表中的每一行数据，对应一个实体类的对象。

> 返回集合的两个好处：
>
> 1. 如果想要返回ResultSet，则不可以对数据库连接进行关闭，但是这是相当不好的，如果关闭连接，则无法取出数据
> 2. 返回ResultSet，则业务逻辑层会出现数据持久层的代码，分层无法体现。

**4. 手动提交事务**

出现的原因：为了保证事务的原子性，即多条SQL语句要执行一次都执行，要不执行都不执行

~~~java
public void saveUser() {
    Connection con=null;
    try {
        //1. 加载驱动
        ...
        //2. 创建连接
		...
            
        //设置不进行自动提交事务
        con.setAutoCommit(false);
        
        //3. 准备手枪
        Statement sm = con.createStatement();
        
        //4. 准备子弹
       	String sql = ...
       
        //5. 执行SQL语句

        //6. 关闭sm
        sm.close();
        
        //7. 手动提交事务
        con.commit();
    } catch (Exception e) {
        e.printStackTrace();
        try {
            //事务出现异常，进行回滚
            con.rollback();
        } catch (SQLException e1) {
            e1.printStackTrace();
        }
    }finally {
        if(con!=null) {
            try {
                con.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
~~~

> 注：
>
> 在数据库的操作中，一条DML语句便是一个事务，缺省情况下，数据库对事务是自动提交的，即执行完一个DML语句便提交一次。如果一项事务同时涉及多条DML语句，则有可能出现一半成功执行，一半执行失败的情况。
>
> 比如：
>
> 两个人A和B进行转账，A成功将钱打给B，A账户的金额减少，事务提交，写回数据库；而B账户在执行金额增加操作的过程中发生错误，DBSM自动对事务进行回滚操作，结果B中的金额未发生变化。
>
> 解决办法：
>
> 使用手动提交事务。

**5. PreparedStatement的使用**

**PreparedStatement和Statement的关系**

PreparedStatement是Statement的子接口，实现了比Statement更强大的功能。

~~~java
public interface PreparedStatement extends Statement
~~~

**引入的原因：**

防止SQL注入。

**解决方法：**

PreparedStatement使用了占位符

**PreparedStatement的优点：**

* 安全（可以一定程度上防止SQL注入）
* 代码的可读性强，避免了繁琐的字符串拼接
* 速度快

> 速度快：
>
> PrepareStatement是预编译，对于批量处理可以大大提高运行效率。
>
> 对于Statement，每执行一条SQL语句都会对其进行重新的编译，而PreparedStatement带预编译，对于重复性的预编译语句，不会重复进行编译，直接传值即可。

**小例子**

~~~java
import java.sql.*;

public class TestPrepareStatement {
	public static void main(String[] args) throws Exception {
		
		//加载类
		String className = "com.mysql.jdbc.Driver";
		Class.forName(className);
		
		//建立连接
		String url = "jdbc:mysql://localhost:3306/test1";
		String usr = "root";
		String pwd = "123456";
		
		Connection conn = DriverManager.getConnection(url, usr, pwd);
		
		//准备子弹
		String sql = "select * from qqinfo where name = ? and id = ?";
		
		//准备手枪
		PreparedStatement pStatement = conn.prepareStatement(sql);
		pStatement.setString(1, "zcx");
		pStatement.setInt(2, 2);
		
		ResultSet resultSet = pStatement.executeQuery();
		
		while(resultSet.next()) {
			String string = resultSet.getString(1);
			String string1 = resultSet.getString(2);
			String string2 = resultSet.getString(3);
			int id = resultSet.getInt(4);
			
			System.out.println(string + " " + string1 + " " + string2 + " " + id);
		}
		
		resultSet.close();
		pStatement.close();
		conn.close();
	}
}
~~~

## 小项目

**员工管理系统**