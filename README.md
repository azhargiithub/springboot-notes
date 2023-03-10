
## spring-boot-features
1. Spring Boot starter POMs(to simplify maven configuration)
2. Dependency version conflicts resolved
3. Auto-cofiguration
4. Acutuators (Production ready features of application)
5. Embedded server
6. Embedded database support

## folder-structure
We can see below folders in our maven based boot application
```
src/main/java
    - Application.java
src/main/resources
 - application.properties
src/test/java
Maven Dependencies
target
pom.xml
```
## What is Bootstrapping process in Spring Boot
-> When we create boot application one java class will be created by
default that class is called as Start Class or main class.

-> Spring Boot application execution will begin from start class only.

-> Start class is the entry point for boot application execution.
```@SpringBootApplication
public class Application {
public static void main(String[] args) {
SpringApplication.run(Application.class, args);
}
}
```
-> SpringApplication.run() method is used to bootstrap our spring boot
application.

-> Below things are executing as part of run( ) method

-> It will identify type of the project
- Reactive (webflux starter)
- Web Servlet (web starter)
- Default Standalone (boot starter)

-> It will start stop watch to calculate time

-> It will start listeners based on spring.factories file

-> It will prepare environment (profiles it will load)

-> It will print banner (we can customize banner)

-> It will create IOC based on application type

-> It will prepare & refresh application context

-> It will stop the StopWatch and will calculate total time

-> Call Runners

-> It returns IOC container reference( ConfigurableAppCtxt )

## what is **@springbootApplication**?
The @SpringBootApplication annotation is equal to below 3
annotations
1) @SpringBootConfiguration
2) @EnableAutoConfiguration
3) @ComponentScan

**@SpringBootConfiguration** annotation is equal to @Configuration annotation.It represents our class as configuration class.

**@EnableAutoConfiguration** annotation identifies the configuration which is required to run our application and will provide that.

**@ComponentScan** annotation enables component scanning for our application.It is the process of recognising  spring bean classes in our application.

## Component Scanninng 
-> Component Scanning is the process of identifying spring bean
classes available in the application.

-> In Spring Boot, Component Scanning is enabled by default.

-> In Spring Boot, Component Scanning will start from base package.
Note: The package which contains start class is called as base
package.

-> Once Base package scanning is completed, it will search for sub
packages of base package and will scan them.

Note: The packages which are starting with base package name are
called as Sub packages of base package.
```
in.ashokit -----------------> Base package
in.ashokit.beans ------> It is sub package for base
pkg
in.ashokit.dao -------> It is sub package for base pkg
in.ashokit.service ------> It is sub pkg for base pkg
in.ashokit.controller-----> It is sub pkg for base pkg
in.ashokit.util-------> It is sub pkg for base pkg
in.ashokit.service.admin-----> It is sub pkg for base
pkg
com.ashokit.model ----------> Not a sub package
```

-> If we have multiple packages with different names then we have to
specify @ComponentScan annotation like below
```java
package in.ashokit;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.annotation.ComponentScan;
@SpringBootApplication
@ComponentScan(basePackages = { "com.ashokit", "in.ashokit", "" })
public class Application {
public static void main(String[] args) {
ConfigurableApplicationContext ctxt =
SpringApplication.run(Application.class, args);
}
}
```

Note: It is highly recommended to follow base package naming
convention so that we no need to write @ComponentScan manually.
## What is Spring Bean?

-> The java class which is managing by IOC is called as Spring Bean.

-> To represent java class as Spring Bean we have below annotations
```
1.@Component
2.@Service
3.@Repository
4.@Controller
5.@RestController
6.@Configuration
7.@Bean (This is method level annotation)
```
## Stereotype annotations
-> To represent java class as Spring Bean we will use Stereo Type
annotations
```
@Component
@Service
@Repository
```
![Ster](/images/annotation.png)

-> If we want IOC container to create object for class then we will
use below annotations at class level
```
@Component
@Service
@Repository
```
-> To represent java class as configuration class we will use
@Configuration annotation.

Note: When we want to perform customized configuration then we will do
that by using Configuration class with the help of @Configuration
annotation.

-> If we want to customize the object creation for a class and wants to
represent that as Spring Bean then we will write method and use @Bean annotation.

Note: **@Bean** annotation is method level annotation.

```java
@Configuration
public class AppConfig{
@Bean
public DataSource getDataSource(){
DataSource ds = new DataSource();
ds.setUname(uname);
ds.setPwd(Decryptor.decrypt(pwd));
//set all values
return ds;
}
}
```

-> In application development we will use @Bean for below scenarios

-> DataSource creation

-> Swagger Docket obj creation

-> Spring Security Configuration

-> Kafka Producer Configuration

-> Kafka Consumer Configuration etc...


-> Component Scan will start from base package. After base package
scanning completed it will scan all sub packages in alphabetical
order.

Note: @Bean annotated methods will be called at the end (after all
packages are scanned).

-> @Bean annotated method can be available in any Spring Bean class
but it is recommended to keep in Configuration class.

## Layered Structure of application development
-> In Realtime we will develop our applications using Layered
Architecture

-> Presentation Layer (Ui)

-> Web Layer (Controller Classes)

-> Business Layer (Service classes)

-> Persistence Layer (Dao/Repository classes)


-> User Interface Logic will be available in Presentation Layer

-> Web layer contains Controller classes which are responsible to handle request and response in web application.

-> Business Layer Contains Service classes which are responsible to execute business logic

Ex: Sendng Email, Password Encryption & Decryption, Report
Generation etc.

-> Persistence Layer Contains Dao/Repository classes which are
responsible to communicate with database.

![Ster](/images/layer.png)
-> Controller class method should call service class method so Service Class Object should be injected into controller class object.

-> Service class method should call dao method so dao class object should be injected into service class object.
## Dependency injection
-> The process of injecting dependent bean object into target bean  object is called as ' Dependency Injection '.

-> To inject one class object into another class object we will use @Autowired annotation

-> In Spring / Spring Boot IOC container is responsible to perform
this Dependency Injection.

-> @Autowired annotation we can use at below 3 places
1) At Constructor Level (Constructor Injection)
2) At Setter Level (Setter Injection)
3) At Field Level (Field Injection)
## Constructor Injection
-> The process of injecting dependent bean object into target bean
object through target bean constructor is called as 'Constructor
Injection'.

-> To peform Constructor Injection, IOC will create Dependent Bean
object first then it will create Target Bean object.

-> To perform Constructor Injection we will write
@Autowired annotation at target class constructor.

Note: If target class is having only one parameterized constructor
then writing @Autowired is optional.

```java
package in.ashokit.dao;
import org.springframework.stereotype.Repository;
@Repository
public class UserDao {
public boolean saveUser(String uname, String email, String pwd) {
// logic to store in db
System.out.println("Storing record in database....");
return true;
   }
}
--------------------------------------------------------------
package in.ashokit.service;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import in.ashokit.dao.UserDao;
@Service
public class UserService {
private UserDao userDao = null;
@Autowired
public UserService(UserDao userDao) {
this.userDao = userDao;
}
public void registerUser() {
boolean isSaved = userDao.saveUser("ashok",
"ashokitschool@gmail.com", "ashok@123");
if (isSaved) {
System.out.println("Record Saved....");
} else {
System.out.println("Failed to save....");
       }
    }
}
--------------------------------------------------------------
package in.ashokit;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;
import in.ashokit.service.UserService;
@SpringBootApplication
public class Application {
public static void main(String[] args) {
ConfigurableApplicationContext run =
SpringApplication.run(Application.class, args);
UserService userService =
run.getBean(UserService.class);
userService.registerUser();
     }
}
..............................................................
```
## Setter Injection
-> The process of injecting dependent bean object into target bean object through target class setter method is called as Setter Injection.

-> To perform Setter Injection we will write @Autowired annotation at target class setter method.

-> In Setter Injection target bean object will be created first then dependent bean object will be created.

```java
package in.ashokit.util
import org.springframework.stereotype.Component;

@Component
public class EmailUtils {
	public EmailUtils() {
		System.out.println("EmailUtils:: constructor");
	}
    public void sendEmail() {
    	System.out.println("sending email for register user");
    }
	
}
..............................................................
package in.ashokit.util
import org.springframework.stereotype.Component;

@Component
public class PwdUtils {
	public PwdUtils() {
		System.out.println("PwdUtils:: constructor");
	}
	public void encryptPwd() {
		System.out.println("Encrypting pwd for new user");
	}
}
.............................................................
package in.ashokit.dao
import org.springframework.stereotype.Repository;

@Repository
public class UserDao {
	public UserDao() {
		System.out.println("UserDao:: constructor");
	}

	public void save() {
		System.out.println("Saving user record in database");
	}
}
.............................................................
package in.ashokit.service;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import in.ashokit.dao.UserDao;
import in.ashokit.util.EmailUtils;
import in.ashokit.util.PwdUtils;
@Service
public class UserService {
private PwdUtils pwdUtils;
private EmailUtils emailUtils;
private UserDao userDao;
public UserService() {
System.out.println("UserService::Constructor");
}
@Autowired
public void setPwdUtils(PwdUtils pwdUtils) {
this.pwdUtils = pwdUtils;
}
@Autowired
public void setEmailUtils(EmailUtils emailUtils) {
this.emailUtils = emailUtils;
}
@Autowired
public void setUserDao(UserDao userDao) {
this.userDao = userDao;
}
public void registerUser() {
pwdUtils.encryptPwd();
userDao.save();
emailUtils.sendEmail();
System.out.println("User Record Saved....");
}
}
.............................................................................
```
![Ster](/images/appdev.png)

### If we do both setter and constructor injection then what will happen?
 
-> Always constructor injection will happen first

-> After Constructor Injection, Setter Injection will execute

-> Finally, Setter Injection Value will override Constructor Injection Value. So Setter Injection is final.
## Field Injection
-> It is the process of injecting dependent object into target object
using target class variable.

-> To perform field injection we will specify @Autowired annotation at
variable level.
-> To perform Field Injection IOC will internally uses Reflection API.

**Note**: Using Reflection API we can access private variables outside of
the class also but it is not recommended approach.

-> With in one target class we can declare multiple dependent beans
and we can injecting them using Field Injection.

### Why Field Injection is not recommended ?

1) Internally it will use Reflection Api to inject value into private variable outside of the class which is against OOPS principles.

2) With Field Injection if we inject too many dependencies our class will violates Single Responsibility Principle.
```java
package com.azhar.util;

import java.time.LocalDate;

import org.springframework.stereotype.Component;

@Component
public class DateUtil {

	public LocalDate getDate() {
		LocalDate now = LocalDate.now();
		return now;
	}
}
..............................................................................
package com.azhar.service;

import java.time.LocalDate;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.azhar.util.DateUtil;

@Service
public class ReportService {

	@Autowired
	private DateUtil du;

	public void generateReport() {
		LocalDate date = du.getDate();
		System.out.println("Report generated for ::" + date);
	}
}
............................................................................
package com.azhar;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;

import com.azhar.service.ReportService;

@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		ConfigurableApplicationContext context = SpringApplication.run(Application.class, args);
		ReportService service = context.getBean(ReportService.class);
		service.generateReport();
		
	}

}
.......................................................................
```
## @Qualifier annotation
-> If more than one bean is eligible for Autowiring then we will use @Qualifier annotation to resolve that ambiguity problem.

-> @Autowired annotation we can use at 3 places hence @Qualifier
annotation also can be used at those 3 places.
1) Constructor level
2) Setter level
3) Field level
## Banner in Spring Boot
-> When Spring Boot application started it will print Spring logo on the console that is called as Spring Boot banner.

-> SpringApplication.run( ) method contains logic to print banner

-> Spring Boot banner works based on banner mode
1) OFF
2) LOG
3) CONSOLE ( This is by default )

**spring.main.banner-mode=**

-> To customize banner text we can create banner.txt file in src/main/resources folder. Boot will load banner.txt file from src/main/resources folder by default.

-> In Realtime people will configure Project Name or Company Name as banner text in ASCII Format.
## Runners in Spring Boot
-> Runners are used to execute the logic only one time when the application got started.

-> Runners will be executed as part of SpringApplication.run ( ) method

-> In Spring Boot We can use 2 types of Runners
1) ApplicationRunner
2) CommandLineRunner

-> The above two are functional interfaces because they contain only one abstract method i.e run( ) method.

### Usecases to go for Runners

-> Load the data from database to cache.
-> Send email once application started

## Database Setup (DB server & DB Client)

-> When we install oracle db in our system we will get sql commandline to execute sql queries.

Note: It is very difficult to write complex sql queries in command line.

In industry people will use SQL developer as a client software to write sql queries.

-> To communicate with DB using data jpa our java application should have below things.

1) DB Config
2) Entity Class
3) Repository Interface

-> DB Config contains Database Configuration Properites
(Driver class, url, username and password)

-> The java class which is mapped with db table is called as Entity
class.

-> Repository interface provided method to perform CRUD operations with db table.

-> In spring boot application we will write db config in application.properties file

............................... application.properties...............................
```
spring.datasource.url=
spring.datasource.username=
spring.datasource.password=
spring.datasource.driverClassName=
```
-> Based on above properties Data JPA will create connection pool and
it provide DB connection to communicate with database.

.........................................................................................................................................................................

-> We will map our java class with database table using below
annotations
```
a) @Entity : To represent java class as entity (It is mandatory)
b) @Table : To map class name with db table name (It is optional).
Note: If db table name & entity class name is same then @Table is
optional.
c) @Id : To represent primary key column mapped variable (It is
mandatory)
d) @Column : To map entity class variable with Db tbl column name
(Optional)
Note: If db table column name & entity cls varibale name is same
then @Column is optional.
```

-> Spring Data JPA provided 2 interfaces to perform curd operations.
They are

1) CrudRepository
2) JpaRepository

-> CrudRepository provided methods to perform only CRUD operations

-> JpaRepository provided methods to perform Crud operations +
Pagination + Sorting

Note:JpaRepository is having more functionalities than CrudRepository

## Developing First Application Using Spring Data JPA

1) Create a table in database
```sql
CREATE TABLE EMP_TBL(
EMP_ID NUMBER(10),
EMP_NAME VARCHAR2(20),
EMP_SAL NUMBER(10,2),
PRIMARY KEY (EMP_ID)
);
```
2) Create Spring Boot application with below dependencies

1. spring-boot-starter-data-jpa
2. oracle ( or ) mysql
3. Configure datasource properties in application.properties file
```
spring.datasource.url=jdbc:oracle:thin:@localhost:1521/XE
spring.datasource.username=IES_DEV
spring.datasource.password=IES_DEV
spring.datasource.driverClassName=jdbc.oracle.OracleDriver
```
4) Create Java class and map with db table
```java
@Entity
@Table(name="EMP_TBL")
public class Employee{
@Id
@Column(name="EMP_ID")
private Integer empId;
@Column(name="EMP_NAME")
private String empName;
@Column(name="EMP_SAL")
private Double empSal;
//setters & getters
}
```
5) Create a interface and extend the properties from Spring Data JPA
Repository interface.
```java
@Repository
public interface EmpRepository extends CrudRepository<Employee,
Integer>{
}
```
6) Get EmpRepository bean object in boot start class using IOC
reference and call the methods to perform Crud operations.

## CrudRepository provided methods

-> Spring Data Jpa provided repository interfaces to perform DB
operations

1) CrudRepository
2) JpaRepository

-> CrudRepository provided methods to perform only crud operations
-> JpaRepository provided methods to perform crud operations +
pagination + sorting also.

-> If our interface extending the properties from JPA interfaces then
we no need to write single line of code also to perform crud
operations.

-> When we create our interface by extending jpa repository interface
then implementation class will be created in the run time for our
interface. By using that implementation class object we can call the
methods to perform DB operations.

Note: Implementation class is a proxy class

CrudRepository interface methods
--------------------------------------------------------------------
```
save ( ) : To insert one record
saveAll ( ) : To insert collection of records
Note: We don't have update method.... save( ) method is polymorphic
method. If primary key already exist then it will update the record
else it will insert the record.
findById( ) : To retrieve record using Primary Key
findAllById ( ) : To retrieve records using multiple primary keys.
findAll ( ) : To retrieve all records available in table
existsById ( ) : To check presense of the record
count ( ): To check total no.of records in available in table.
deleteById ( ) : To delete a record using Primary Key
deleteAllById ( ) : To delete multiple records using primary keys
delete(E obj) : To delete record using entity obj
deleteAll ( ) : To delete all records from the table.
---------------------------------------------------------
```
-> In Data JPA we can perform DB operations in 3 ways
1) Using predefined methods->crudeRepository and jpaRepository methods
2) By Writing findByXXX methods
3) By Using Custom Queries

### findByXXX methods

-> findBy methods are used to perform retrival operatoins

-> When we write a findBy method Data JPA will construct the query
based on our method name

-> Method Naming convention is very very important for findBy methods.

```java
package in.ashokit.entity;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.Table;
@Entity
@Table(name = "EMP_TBL")
public class Employee {
public Employee() {
}
public Employee(Integer empId, String empName, Double empSal)
{
this.empId = empId;
this.empName = empName;
this.empSal = empSal;
}
@Id
@Column(name = "EMP_ID")
private Integer empId;
@Column(name = "EMP_NAME")
private String empName;
@Column(name = "EMP_SAL")
private Double empSal;
public Integer getEmpId() {
return empId;
}
public void setEmpId(Integer empId) {
this.empId = empId;
}
public String getEmpName() {
return empName;
}
public void setEmpName(String empName) {
this.empName = empName;
}
public Double getEmpSal() {
return empSal;
}
public void setEmpSal(Double empSal) {
this.empSal = empSal;
}
@Override
public String toString() {
return "Employee [empId=" + empId + ", empName=" +
empName + ", empSal=" + empSal + "]";
}
}
----------------------------------------------------------------------
---
package in.ashokit.repository;
import java.io.Serializable;
import java.util.List;
import org.springframework.data.repository.CrudRepository;
import in.ashokit.entity.Employee;
public interface EmpRepository extends CrudRepository<Employee,
Serializable> {
public List<Employee> findByEmpName(String name);
public List<Employee> findByEmpSal(Double sal);
public List<Employee> findByEmpNameAndEmpSal(String name,
Double salary);
public List<Employee> findByEmpSalGreaterThan(Double empSal);
public List<Employee> findByEmpNameIn(List<String> names);
}
```
-> Using Data JPA we can execute custom queries also

-> To execute custom queries we will use @Query annotation.

What is the difference between HQL and Native SQL queries?

----------------------------------------------------------
-> In SQL queries we will use table name and column names directley

-> In HQL queries we will use Entity class name and variable names

-> SQL queries are database dependent

-> HQL queries are database in-dependent

Note: Database can't understand HQL... DB will understand only SQL.

-> To convert HQL queries to SQL queries Dialect classes will be used.

SQL TO HQL Query example
```sql
SQL :: select emp_name from emp_tbl where emp_id=101;
HQL :: select empName from Employee where empId=101;
SQL :: select emp_name, emp_sal from emp_tbl where emp_id=101;
HQL :: select empName, empSal from Employee where empId=101;
SQL :: select * from emp_tbl where emp_id=101;
HQL :: from Employee where empId=101;
```

## Query By Example

Requirement:

Retrieve all Managers working for IRCTC project in Hyd location.

//if we select all 3 drop-down values then below query should execute
```sql
select * from emp_tbl where emp_role='Manager' and emp_project='IRCTC'
and emp_location='HYD';
```
//if we select only emp_role then below query should execute
```sql
select * from emp_tbl where emp_role='Manager';
```
//if we select only emp_project then below query should execute
```sql
select * from emp_tbl where emp_project='SBI';
```
//if we select only emp_location then below query should execute
```sql
select * from emp_tbl where emp_location='PUNE';
```
```java

public List<Employee> searchEmps(EmpSearchCriteria esc){
StringBuilder sql = "select * from emp_tbl where 1=1";
if(esc.getEmpRole()!=null){
sql.append(" and emp_role='+esc.getEmpRole()+"'";
}
if(esc.getEmpProject()!=null){
sql.append(" and emp_project='+esc.getEmpProject()+"'";
}
if(esc.getEmpLoc()!=null){
sql.append(" and emp_loc='+esc.getEmpLoc()+"'";
}
}
---------------------------------------------------------------------------
```

Employee [empId=103, empName=John, empSal=18000.0]

Employee [empId=104, empName=Ram, empSal=23000.0]

Employee [empId=105, empName=Rani, empSal=24000.0]

Employee [empId=101, empName=Ashok, empSal=14000.0]

Employee [empId=102, empName=Kumar, empSal=15000.0]

Employee [empId=106, empName=Smith, empSal=15000.0]

## Connection Pooling
-> If we want to communicate with database then we need to have
connection with database.

-> If application having connection with database then only
application can perform DB operations.

-> To establish connection with Database Java applications will use
JDBC api.

-> In JDBC api we have DriverManager class to get connection with
database.

Connection con = DriverManager.getConnection(url,uname,pwd);

-> getConnection(...) method returning Connection object.

Note: The connection which is returned by getConnection(...) method is
physical connection. Its mandatory that we have to close that
connection once our operation got completed.

-> When we open physical connections from database, if we forgot close
them after sometime there is a change of getting connections exhausted
problem.

-> To avoid this connection exhausted problem we will use Connection
Pooling concept.

**Connection Pooling**

-> It is the process of getting connections when the application
started and keeping them ready to use in the application.

-> Connection pooling will be managed by Pool Manager.
-> The connection which is taken from Connection Pool is called as
Logical Connection.

-> If we forgot to close the logical connection then Pool Manager will
close that connection.

Advantages of Connection Pooling

-> No need to make network call everytime to get the connection with
DB

-> Connection Exhausted problem can be avoided.

-> Spring Boot internally uses Hikari Connection Pool.

Note: We can customize connection pool properties using
application.properties file.
1) max-pool-size
2) connection-timeout
3) idel-timeout etc.

![Ster](/images/connection.png)

## Transactions
*Working with Transactions in Data JPA

-> An unit amount of work is called as Transaction.

-> When we are performing operations with DB then Transactions are
very very important.

-> Transaction is mandatory for non-select operations
(Insert/Update/Delete)

-> Transaction is optional for select operations (Retriving)
```java
Connection con = DriverManager.getConnection(Url, Uname, Pwd);
Statement stmt = con.createStatement();
int rowsEffected = stmt.executeUpdate(sqlQuery);
```
-> In JDBC connection obj will be created with default autoCommit
mode. When we execute any non-select query using jdbc api then
immediatley it will commit the transaction.

-> In order to disable auto-commit mode in JDBC api we will use
```java
setAutoCommit(false).
con.setAutoCommit(false);
con.commit( ) or con.rollback( );
```

-> When we disable auto-commit mode then programmatically we have to
commit the transaction or rollback the transaction manually.

-> If all operations are successful in Tx then we will commit
it

-> If any one operation got failed in Tx then we will rollback
it.

![Ster](/images/transaction.png)

**How to rollback transaction in Data JPA**

-> We can use @Transactional(rollbackFor=T) to rollback the
transaction when exception occured.

-> If exception not occured then data jpa will commit the transaction

## auto-ddl and composite primary key

-> Hibernate provided several ORM properties for us to configure in
the application

show_sql : To print queries on the console

format_sql : To print queries in formatted way

dialect : To convert HQL queries to SQL queries

auto-ddl : To generate schema dynamically

-> For auto-ddl property having 5 options
1) create
2) create-drop
3) update
4) validate
5) none

-> 'create' option will always create the new tables. If tables are
already existing it will drop the existing tables and will create new
tables. We will loose table old data in this approach.

-> 'create-drop' option will create tables then it will perform the
operation and it will delete the tables at the end. This option is
used for staging operations.

-> 'update' option will create tables only for first time. If tables
are already available then it will use existing tables.

-> 'validate' option will not make any changes to database. It will
just verify entities with db tables.

-> 'none' option will disable auto-ddl in application. This is default
value.

## Primary and Composite Primary Key In Database

-> Primary Key is a constraint which is used to maintain unique data
in the column.

-> Primary key constraint is the combination of below 2 constraints
1) NOT NULL
2) UNIQUE

-> To map variable with Primary Key Column we will use @Id annotation.
-> If a table is having more than one primary key then it is called as
composite Primary Key.

```sql
---------------------Tbl creation with PK-----------------------------
CREATE TABLE EMP_TBL(
EMP_ID NUMBER(10),
EMP_NAME VARCHAR2(20),
EMP_SALARY NUMBER(10,2),
PRIMARY KEY(EMP_ID)
)
---------------------TBL creation with Composite PK------------------
CREATE TABLE ACCOUNTS(
ACC_ID NUMBER(10),
ACC_TYPE VARCHAR2(10),
HOLDER_NAME VARCHAR2(20),
MIN_BAL NUMBER(10,2),
BRANCH_NAME VARCHAR2(20),
PRIMARY KEY(ACC_ID, ACC_TYPE, HOLDER_NAME)
)
----------------------------------------------------------------------
```

-> We can map composite primary keys in 2 ways
1) Using @IdClass annotation (approach-1)
2) Using @Embeddable & @EmbeddedId annotations
(approch-2)

**Approach-1 & Approach-2 examples are given below**
----------------------------------------------------------------------

## Composite Primary Key - Approach - 1
----------------------------------------------------------------------
```java
public class AccountPK implements Serializable {
private Integer accId;
private String accType;
private String holderName;
//setters & getters
}
----------------------------------------------------------------------
-------------
@Entity
@Table(name = "BANK_ACCOUNTS")
@IdClass(AccountPK.class)
public class Account {
@Column(name = "BRANCH_NAME")
private String branchName;
@Column(name = "MIN_BAL")
private Double minBal;
@Id
private Integer accId;
@Id
private String accType;
@Id
private String holderName;
//setters & getters
----------------------------------------------------------------------
------------
package in.ashokit.service;
import org.springframework.stereotype.Service;
import in.ashokit.entity.Account;
import in.ashokit.entity.AccountPK;
import in.ashokit.repository.AccountRepository;
@Service
public class AccountService {
private AccountRepository accRepo;
public AccountService(AccountRepository accRepo) {
this.accRepo = accRepo;
}
public void saveAccData() {
Account acc = new Account();
acc.setBranchName("Ameerpet");
acc.setMinBal(5000.00);
acc.setAccId(102);
acc.setAccType("SAVINGS");
acc.setHolderName("IBM");
accRepo.save(acc);
}
}
----------------------------------------------------------------------
-------------
package in.ashokit;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;
import in.ashokit.service.AccountService;
@SpringBootApplication
public class Application {
public static void main(String[] args) {
ConfigurableApplicationContext context =
SpringApplication.run(Application.class, args);
AccountService accountService =
context.getBean(AccountService.class);
accountService.saveAccData();
context.close();
}
}
----------------------------------------------------------------------
-------------
spring.datasource.url=jdbc:oracle:thin:@localhost:1521/XE
spring.datasource.username=IES_DEV
spring.datasource.password=IES_DEV
spring.datasource.driver-class-name=oracle.jdbc.driver.OracleDriver
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update
----------------------------------------------------------------------
``` 
## Composite-Primary key : Approach-2
```java
----------------------------------------------------------------------

package in.ashokit.entity;
import java.io.Serializable;
import javax.persistence.Embeddable;
@Embeddable
public class AccountPK implements Serializable {
private Integer accId;
private String accType;
private String holderName;
//setters & getters
}
----------------------------------------------------------------------
------------
package in.ashokit.entity;
import javax.persistence.Column;
import javax.persistence.EmbeddedId;
import javax.persistence.Entity;
import javax.persistence.Table;
@Entity
@Table(name = "BANK_ACCOUNTS")
public class Account {
@Column(name = "BRANCH_NAME")
private String branchName;
@Column(name = "MIN_BAL")
private Double minBal;
@EmbeddedId
private AccountPK accPk;
//setters & getters
}
----------------------------------------------------------------------
------------
package in.ashokit.service;
import org.springframework.stereotype.Service;
import in.ashokit.entity.Account;
import in.ashokit.entity.AccountPK;
import in.ashokit.repository.AccountRepository;
@Service
public class AccountService {
private AccountRepository accRepo;
public AccountService(AccountRepository accRepo) {
this.accRepo = accRepo;
}
public void saveAccData() {
AccountPK pk = new AccountPK();
pk.setAccId(103);
pk.setAccType("SAVINGS");
pk.setHolderName("TCS");
Account acc = new Account();
acc.setBranchName("Ameerpet");
acc.setMinBal(5000.00);
acc.setAccPk(pk); // setting pk class obj to entity
obj
accRepo.save(acc);
}
}
----------------------------------------------------------------------
-----------
package in.ashokit;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;
import in.ashokit.service.AccountService;
@SpringBootApplication
public class Application {
public static void main(String[] args) {
ConfigurableApplicationContext context =
SpringApplication.run(Application.class, args);
AccountService accountService =
context.getBean(AccountService.class);
accountService.saveAccData();
context.close();
}
}
----------------------------------------------------------------------
```
## How to retrieve data using Composite Primary Keys
```java
----------------------------------------------------------------------

public void getDataUsingPK() {
AccountPK pk = new AccountPK();
pk.setAccId(101);
pk.setAccType("CURRENT");
pk.setHolderName("IBM");
Optional<Account> findById = accRepo.findById(pk);
if (findById.isPresent()) {
System.out.println(findById.get());
}
}
----------------------------------------------------------------------
```

-> All the primary key values we need to store in Embeddable class obj
and pass that obj as parameter for findById( ) method like above.


## Timestamping In hibernate
-> It is highly recommended to maintain created_date and updated_date
values in table.

-> created_date and updated_Date column values will help us in
anaylyzing the table data.

-> In realtime for every table we will have CREATED_DT and UPDATED_DT
columns.

-> If we use timestamping concept then hibernate will take care of
populating values into CREATED_DT and UPDATED_DT columns.

-> To work with Timestamping we have below 2 annotations
1) @CreationTimestamp
2) @UpdateTimestamp
```java
----------------------------------------------------------------------
---
@Entity
@Table(name = "INSURANCE_PLANS")
public class InsurancePlan {
@Id
@GeneratedValue
private Integer planId;
private String planName;
private String planStatus;
@CreationTimestamp
@Column(updatable = false)
private LocalDate createdDate;
@UpdateTimestamp
@Column(insertable = false)
private LocalDate updatedDate;
----------------------------------------------------------------------
--
```
### If we are using java.util.Date for variable data types then we can use @Temporal annotation to specify format of date.

1) Best Practises To Follow in Persistence Layer
2) Hibernate vs Data JPA
3) What is Entity class ?
4) What is Repository interface?
5) Data Jpa Repositories
a) CrudRepository
b) JpaRepository
6) Pagination
7) Sorting
8) QueryByExample
9) CrudRepository predefined methods
10) findByxxx methods syntax
11) Custom Queries in data jpa
12) Connection pooling (Hikari)
13) Transactions & How to rollback transactions
14) Primary Key & Composite Primary keys
15) Generators
16) Custom Generators
# **SPRING WEB MVC MODULE(PART3)**

-> Spring Web MVC is one module available in Spring Framework

-> By Using Spring Web MVC module we can develop below types of
applications
1) Web Applications ( C 2 B )
2) Distributed Applications (B 2 B)

-> Spring Boot also uses Spring Web MVC module internally to develop
web applications.

-> Spring Web MVC module is developed based on 2 design patterns
1) MVC Design Pattern
2) Front Controll Design Pattern

## MVC Design Pattern

-> MVC stands for Model, View and Controller

-> Model represents data

-> View represents presentation logic

-> Controller is responsible for handling request (Mediator btwn Model
and View)

-> MVC design pattern is used to develop our applications with loosely
coupling.

## FrontController Design Pattern

-> Spring MVC module is using front controller design pattern to deal
with pre-processing and post-processing of a request.

-> In Spring MVC 'DispatcherServlet' acting as Front Controller.
Note: DispatcherServlet is a predefined class available in Spring MVC
module.

![Ster](/images/mvc.png)

## **Advantages of Spring Web MVC Module**

-> Supports for developing both web applications and distributed
applications

-> Spring Web MVC application can deal with multiple presentation
technologies
1) JSP
2) Thymeleaf

-> Spring MVC supports for Form Binding

Note: Form Binding means form data will be stored in object and object
data will be displayed in the form.

-> Spring MVC supports flexibility in form binding (It will take care
of typecasting)

-> It is having Front Controller to deal with Pre-Processing and Post-
Processing of a request.

## **Spring Web MVC Architecture**

![Ster](/images/architecture.png)


1) FrontController
2) HandlerMapper
3) Controller
4) ModelAndView
5) ViewResolver
6) View

### What is FrontController?

-> FrontController is responsible to perform pre-processing and postprocessing
of a request.

-> In Spring Web MVC, DispatcherServlet will act as Front Controller

-> DispatcherServlet is a pre-defined class.

## What is HandlerMapper?


-> HandlerMapper is a pre-defined component available in Spring Web
MVC

-> HandlerMapper is responsible to identify Request Handler class for
incoming request.

-> From Spring 3.0 onwards RequestMappingHandlerMapper is executing as
Default HandlerMapper.

## What is Controller?

-> Controller is web component which is responsible to handle web
request

-> Controller is also called as Request Handler

-> To represent java class as a Controller we will use @Controller
annotation.

## What is ModelAndView?

-> After handling the request, controller method will return
ModelAndView object.

-> Model is a map which stores the data in the form of Key-Value pair

-> Model is used to send the data to presentation layer in key-value
format.

-> View Represents Logical view name that should be rendered as a
response.

Note: In Spring Web MVC, Controller don't know where the view files
will be available and what is the view technology.

## What is View Resolver?

-> View Resolver is used to identify location of view files and view
technology

-> We will configure ViewResolver with prefix and suffix

prefix ------> Represents view files location

suffix ------> Represents view files extension

## What is View?

-> View Component is used to render model data in view file

## First App Development using Spring Web MVC

1) Create Spring Starter Project with below dependencies

a) spring-boot-starter-web

b) spring-boot-devtools

c) tomcat-embed-jasper

-> web-starter is used to develop web applications using Spring MVC.
It will provide Tomcat as default embedded container.

-> devtools dependency is used to re-start the server when changes
happend in project code/configuration.

-> tomcat-embed-jasper dependency is used to compile jsp files
Note: If tomcat-embed jasper dependency not available then jsp file
will be downloaded when user make a request.

2) Configure below properties in application.properties file
a) Embedded server port
b) View Resolver
3) Create Controller class using @Controller annotation
4) Write method in controller class to handle the request
5) Create View file with presentation logic
6) Run the application and test it.
## Methods in Controller class & Multiple Controllers
-> To develop web application using Spring Boot we have to add below
starter in pom.xml file
           
		    spring-boot-starter-web
-> When we add spring-boot-starter-web dependency it will get 'Tomact'
as default container.

-> Default Container runs on port number 8080. If we want to change
that port number we have to write below property in application.properties file
           
		   server.port = 9090
-> To represent class as a controller, we will use @Controller
annotation.

-> Inside controller class we will write multiple methods and we will
bind those methods to Http Protocol methods using below annotations

GET Request -> @GetMapping

POST Request -> @PostMapping

PUT Request -> @PutMapping

DELETE Request -> @DeleteMapping

Note: Earlier people used to work with @RequestMapping annotation to
bind controller methods to Http Protocol methods.
@RequestMapping(value="", method=POST)

-> In one application we can have multiple controllers.
-> One controller can have multiple methods

-> We need to make sure all the methods are binded to unique url
patterns.

-> To avoid ambiguity problem in url patterns we will write both class
level and method level url patterns.

## Sending data from Controller to UI (response data)

-> In Spring MVC application, Controller is responsible for sending
data to UI.

-> To send the data from Controller to UI we have Model or
ModelAndView objects.

-> Model and ModelAndView objects will represent the data in key-value
format.
syntax: model.addAttribute(String key, Object value);

-> Based on keys we will access values in Presentation files using
expression language.
                 
				 syntax : ${key}
```java
package in.ashokit.controller;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
@Controller
public class BookController {
@GetMapping("/bookdata")
public String getBookData(Model model) {
// setting data to model obj
model.addAttribute("name", "Spring Boot");
model.addAttribute("author", "Rod Johnson");
model.addAttribute("price", 450.00);
return "book-data";// returning logical view name
}
}
----------------------------------------------------------------------
-
package in.ashokit.controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.servlet.ModelAndView;
import in.ashokit.pojo.Product;
@Controller
public class ProductController {
@GetMapping("/productdata")
public ModelAndView getProductData() {
ModelAndView mav = new ModelAndView();
Product pobj = new Product();
pobj.setProductId(101);
pobj.setProductName("Keyboard");
pobj.setProductPrice(1500.00);
mav.addObject("product", pobj); // adding product obj
to model in Key-value format
mav.setViewName("product-data"); // setting logical
view name
return mav;
}
}
----------------------------------------------------------------------
---
package in.ashokit.controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
@Controller
public class IndexController {
@GetMapping
public String index() {
return "index";
}
}
----------------------------------------------------------------------
--
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
pageEncoding="ISO-8859-1"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="ISO-8859-1">
<title>Insert title here</title>
</head>
<body>
<h2>Welcome to our store.......</h2>
<hr />
<a href="bookdata">Get Book Data</a>
<br />
<a href="productdata">Get Product Data</a>
</body>
</html>
----------------------------------------------------------------------
---
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
pageEncoding="ISO-8859-1"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="ISO-8859-1">
<title>Insert title here</title>
</head>
<body>
<h2>Book Data</h2>
Book Name : ${name} <br/>
Author Name : ${author} <br/>
Book Price: ${price} <br/>
<a href="/">Home</a>
</body>
</html>
----------------------------------------------------------------------
---
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
pageEncoding="ISO-8859-1"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="ISO-8859-1">
<title>Insert title here</title>
</head>
<body>
<h2> Product Data </h2>
Product ID : ${product.productId} <br/>
Product Name : ${product.productName} <br/>
Product Price : ${product.productPrice} <br/>
<a href="/">Home</a>
</body>
</html>
----------------------------------------------------------------------
---
Get Book Data Hyperlink =====> localhost:9090/bookdata
Get Product Data Hyperlink ====> localhost:9090/productdata
Home Hyperlink ====> localhost:9090/
----------------------------------------------------------------------
```

## How to send data from UI to Controller

-> In Web application we will have several scenarios to send data from
UI to controller.

Examples:

1) Getting Emp Salary Based on Emp ID
2) Getting Book Price based on ISBN
3) Getting Ticket Status based on Ticket ID
4) Storing Employee Information using Form
5) Searching Products in E-commerce application etc..

-> We can use below approches to send data from UI to Controller
1) Query Parameter
2) Path Parameter
3) Request Body
## Query Parameters

-> Query Parameters are used to send data to server in URL

-> Query Parameter will represent data in key-value format

-> Query Parameter will start with '?' symbol

-> Query Parameter will be seperated by '&'

-> Query Parameter should present only at the end of the URL

Ex-1: www.ashokit.in?course=SBMS

Ex-2: www.ashokit.in?course=SBMS&trainer=Ashok

Ex:3 : https://www.youtube.com/results?

search_query=debugging+ashok+it

Ex:4 : https://www.youtube.com/watch?v=2WxsClYhreE

-> To read query parameter from URL we will use @RequestParam
annotation in controller.
```java
syntax :
@GetMapping("/getPrice")
public ModelAndView getBookPrice(@RequestParam String isbn){
//logic
return mav;
}
```
```java
package in.ashokit.controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
@Controller
@RequestMapping("/book")
public class BookController {
@GetMapping("/price")
@ResponseBody
public String getBookPrice(@RequestParam("isbn") String str) {
String msg = "The Book Price Of " + str + " Is :
450.00 INR";
return msg;
}
}
----------------------------------------------------------------------
---
package in.ashokit.controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
@Controller
@RequestMapping("/book")
public class BookController {
@GetMapping("/price")
@ResponseBody
public String getBookPrice(@RequestParam("isbn") String str) {
String msg = "The Book Price Of " + str + " Is :
450.00 INR";
return msg;
}
}
----------------------------------------------------------------------
---
package in.ashokit.controller;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;
@Controller
public class CourseController {
@GetMapping("/course")
@ResponseBody
public String getCourseDtls(String cname, String trainer) {
if (cname.equals("SBMS")) {
String msg = cname + " By " + trainer + "
Starting From 23-Jun-21 @6:30 AM IST";
return msg;
} else if (cname.equals("JRTP")) {
String msg = cname + " By " + trainer + "
Starting From 30-Jun-21 @11:30 AM IST";
return msg;
}
return "Contact Admin Team :: + 91-6301921083";
}
}
----------------------------------------------------------------------
```
Note: If query-param key name is same as method parameter name then
@RequestParam annotation is optional.

## Path Parameters

-> Path Parameters are used to send data to Server in URL

-> Path Parameters will represent data directley (No Key)

-> Path Parameters can present anywhere in the URL

-> Path Parameters will be seperated by slash ('/')

Note: Path Paramters should be represented in URL Pattern
Sample URL : "/book/{isbn}/price"

Note: In the above URL 'isbn' is path parameter

-> To read path parameter values from URL we will use @PathVariable
annotation in Controller class.
```java
----------------------------------------------------------------------
---
package in.ashokit;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
@Controller
public class CarController {
@GetMapping("/price/{carName}")
@ResponseBody
public String getCarPrice(@PathVariable("carName") String
carName) {
String msg = carName + " Price is 7.8 lakhs";
return msg;
}
@GetMapping("/check/{carName}/{location}/{branch}/cars")
@ResponseBody
public String checkDtls(@PathVariable String carName,
@PathVariable String location, @PathVariable
String branch) {
String responseMsg = "In " + location + " " + branch +
" Branch " + carName + " Cars are available";
return responseMsg;
}
}
----------------------------------------------------------------------
```
-> If we use Query Params or Path Params to send the data to server
then data will be exposed in URL

-> If data is exposed in URL we have security breaches

Note-1: It is not at all recommended to send confidential information
to server in the URL (password, credit card number, Pin number, SSN,
Aadhar etcc)

Note-2: URL will have length limitation (we can send 1024 characters)

Note-3: We can't send binary data (videos, audios, images etc) in URL

-> If we want to send any small text information which is not
sensitive then we can use Query Parameter and Path Parameter to send
that data to server

-> In Web based application we will use Query Params

-> In Rest Apis we will use Path Params

-> To resolve drawbacks of Query Params and Path Params we will use
Request Body to send huge/sensitive/confidential/binary data to
server.

-> The data which is sending to server in Request Body will not be
exposed in URL.

## Spring Web MVC Tag Library Introduction
![Ster](/images/library.png)

## Form Development using MVC Tag Library

```
1) Create Spring Boot Starter Project with below dependencies
a)spring-boot-starter-web
b)spring-boot-devtools
c)tomcat-embed-jasper
2) Configure below properties in application.properties file
a)Embedded Server Port
b)View Resolver
3) Create Form Binding Class (Command Class)
4) Create Controller class with below methods
a) Method to load the form
b) Method to handle form submission
5) Create View file
6) Run the application and test it.

**Create Spring Boot application with below dependencies**
a)spring-boot-starter-web
b)spring-boot-starter-validation
c)tomcat-embed-jasper
d)spring-boot-devtools
```
```java
-----------------------------ProductController.java-------------------
-----------------------
package in.ashokit.controller;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import in.ashokit.bindings.Product;
@Controller
public class ProductController {
@GetMapping("/loadForm")
public String loadForm(Model model) {
Product pobj = new Product();
model.addAttribute("product", pobj);
return "index";
}
@PostMapping("/saveProduct")
public String handleSaveBtnClick(Product product, Model model)
{
System.out.println(product);
model.addAttribute("msg", "Product Saved
Successfully");
return "dashboard";
}
}
----------------------------------index.jsp---------------------------
-----------------------
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
pageEncoding="ISO-8859-1"%>
<%@ taglib uri="http://www.springframework.org/tags/form"
prefix="form"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="ISO-8859-1">
<title>Insert title here</title>
</head>
<body>
<h3>Product Form</h3>
<form:form action="saveProduct" modelAttribute="product"
method="POST">
<table>
<tr>
<td>Product ID</td>
<td><form:input path="productId" />
</td>
</tr>
<tr>
<td>Product Name</td>
<td><form:input path="productName" />
</td>
</tr>
<tr>
<td>Product Price</td>
<td><form:input path="productPrice" />
</td>
</tr>
<tr>
<td></td>
<td><input type="submit" value="Save"
/></td>
</tr>
</table>
</form:form>
</body>
</html>
---------------------------------dashboard.jsp------------------------
-------------------------
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
pageEncoding="ISO-8859-1"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="ISO-8859-1">
<title>Insert title here</title>
</head>
<body>
<h2>${msg}</h2>
<a href="loadForm">Go Back</a>
</body>
</html>
---------------------------application.properties---------------------
-----------------------
server.port=9090
spring.mvc.view.prefix=/pages/
spring.mvc.view.suffix=.jsp
----------------------------------------------------------------------
-----------------------
```
## Student Registration Form Development
```
1) Create Project with below dependencies
a)spring-boot-starter-web
b)spring-data-JPA
c)tomcat-embed-jasper
d)spring-boot-devtools
e)Oracle driver
----------------------------------------------------------------------
--
2) Configure below properties in application.properties
----------------------------------------------------------------------
---
server.port=9090
spring.mvc.view.prefix=/pages/
spring.mvc.view.suffix=.jsp
spring.datasource.url=jdbc:oracle:thin:@localhost:1521/XE
spring.datasource.username=IES_DEV
spring.datasource.password=IES_DEV
spring.datasource.driver-class-name=oracle.jdbc.driver.OracleDriver
spring.jpa.show-sql=true
```
```java
3) Create Entity classes (Course, Timings, Genders)
----------------------------------------------------------------------
---
@Entity
@Table(name = "COURSES")
public class Courses {
@Id
@Column(name = "COURSE_ID")
private Integer courseId;
@Column(name = "COURSE_NAME")
private String courseName;
}
@Entity
@Table(name = "GENDERS")
public class Genders {
@Id
@Column(name = "GENDER_ID")
private Integer genderId;
@Column(name = "GENDER_NAME")
private String genderName;
}
@Entity
@Table(name = "TIMINGS")
public class Timings {
@Id
@Column(name = "TIMING_ID")
private Integer timingId;
@Column(name = "TIMING_NAME")
private String timingName;
}
----------------------------------------------------------------------
---
4) Create Repository Interfaces for tables
----------------------------------------------------------------------
---
public interface CoursesRepository extends JpaRepository<Courses,
Serializable> {
}
public interface GendersRepository extends JpaRepository<Genders,
Serializable> {
}
public interface TimingsRepository extends JpaRepository<Timings,
Serializable> {
}
----------------------------------------------------------------------
---
5) Create Service Class
----------------------------------------------------------------------
--
Service
public class StudentService {
@Autowired
private GendersRepository gendersRepo;
@Autowired
private CoursesRepository coursesRepo;
@Autowired
private TimingsRepository timingsRepo;
public List<String> getGenders() {
List<Genders> findAll = gendersRepo.findAll();
List<String> genders = new ArrayList<>();
findAll.forEach(gender -> {
genders.add(gender.getGenderName());
});
return genders;
}
public List<String> getCourses() {
List<Courses> findAll = coursesRepo.findAll();
List<String> courses = new ArrayList<>();
findAll.forEach(course -> {
courses.add(course.getCourseName());
});
return courses;
}
public List<String> getTimings() {
List<Timings> findAll = timingsRepo.findAll();
List<String> timings = new ArrayList<>();
findAll.forEach(timing -> {
timings.add(timing.getTimingName());
});
return timings;
}
}
----------------------------------------------------------------------
---
6) Create Form Binding Class
----------------------------------------------------------------------
---
public class Student {
private String name;
private String email;
private Long phno;
private String gender;
private String course;
private String[] timings;
}
----------------------------------------------------------------------
---
7) Create StudentController class
----------------------------------------------------------------------
---
@Controller
public class StudentController {
@Autowired
private StudentService studentService;
@GetMapping("/loadForm")
public String loadRegForm(Model model) {
model.addAttribute("genders",
studentService.getGenders());
model.addAttribute("courses",
studentService.getCourses());
model.addAttribute("timings",
studentService.getTimings());
Student sobj = new Student();
model.addAttribute("student", sobj);
return "index";
}
@PostMapping("/saveStudent")
public String handleRegBtnClick(Student student, Model model)
{
System.out.println(student);
model.addAttribute("msg", "Student Registered
Successfully...!!");
return "dashboard";
}
}
----------------------------------------------------------------------
---
8) Create View Files
----------------------------------------------------------------------
--
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
pageEncoding="ISO-8859-1"%>
<%@ taglib uri="http://www.springframework.org/tags/form"
prefix="form"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="ISO-8859-1">
<title>Insert title here</title>
</head>
<body>
<h2>Student Registration Form</h2>
<form:form action="saveStudent" modelAttribute="student"
method="POST">
<table>
<tr>
<td>Name</td>
<td><form:input path="name" /></td>
</tr>
<tr>
<td>Email</td>
<td><form:input path="email" /></td>
</tr>
<tr>
<td>Phno</td>
<td><form:input path="phno" /></td>
</tr>
<tr>
<td>Gender</td>
<td><form:radiobuttons
items="${genders}" path="gender" /></td>
</tr>
<tr>
<td>Course</td>
<td><form:select path="course">
<form:options
items="${courses}" />
</form:select></td>
</tr>
<tr>
<td>Timings</td>
<td><form:checkboxes
items="${timings}" path="timings" /></td>
</tr>
<tr>
<td></td>
<td><input type="submit"
value="Register"/>
</tr>
</table>
</form:form>
</body>
</html>
----------------------------------------------------------------------
---
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
pageEncoding="ISO-8859-1"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="ISO-8859-1">
<title>Insert title here</title>
</head>
<body>
<h2>${msg}</h2>
<a href="loadForm">Go Back</a>
</body>
</html>
----------------------------------------------------------------------
```
## Form validations
```java
1) Create Spring Boot application with below dependencies
a)spring-boot-starter-web
b)spring-boot-starter-validation
c)tomcat-embed-jasper
d)spring-boot-devtools
2) Configure below properties in application.properties file
a)server port
b)view resolver
3) Create Form Binding class with validation annotations
4) Create Controller class with Required methods
a) Method to load form
b) Method to handle form submission
5) Create View Files With Presentation Logic
----------------------------------------------------------------------
---
```
```java
----------------------------------------------------------------------
---
package in.ashokit.binding;
import javax.validation.constraints.Email;
import javax.validation.constraints.NotEmpty;
import javax.validation.constraints.NotNull;
public class User {
@NotEmpty(message = "Please enter username")
private String uname;
@NotEmpty(message = "Please enter password")
private String pwd;
@NotEmpty(message = "Please enter email")
@Email(message = "Please enter valid email")
private String email;
@NotNull(message = "Please enter phno")
private Long phno;
public String getUname() {
return uname;
}
public void setUname(String uname) {
this.uname = uname;
}
public String getPwd() {
return pwd;
}
public void setPwd(String pwd) {
this.pwd = pwd;
}
public String getEmail() {
return email;
}
public void setEmail(String email) {
this.email = email;
}
public Long getPhno() {
return phno;
}
public void setPhno(Long phno) {
this.phno = phno;
}
@Override
public String toString() {
return "User [uname=" + uname + ", pwd=" + pwd + ",
email=" + email + ", phno=" + phno + "]";
}
}
----------------------------------------------------------------------
---
package in.ashokit.controller;
import javax.validation.Valid;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import in.ashokit.binding.User;
@Controller
public class UserController {
@GetMapping("/user-form")
public String loadUserForm(Model model) {
User userObj = new User();
model.addAttribute("user", userObj);
return "index";
}
@PostMapping("/saveUser")
public String saveUser(@Valid User user, BindingResult result,
Model model) {
if (result.hasErrors()) {
return "index";
}
System.out.println(user);
model.addAttribute("msg", "User saved successfully");
return "dashboard";
}
}
----------------------------------------------------------------------
--
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
pageEncoding="ISO-8859-1"%>
<%@ taglib uri="http://www.springframework.org/tags/form"
prefix="form"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="ISO-8859-1">
<title>Insert title here</title>
<style>
.error {
color: red;
}
</style>
</head>
<body>
<h3>User Form</h3>
<form:form action="saveUser" modelAttribute="user"
method="POST">
<table>
<tr>
<td>Username:</td>
<td><form:input path="uname" /></td>
<td><form:errors path="uname"
class="error" /></td>
</tr>
<tr>
<td>Password:</td>
<td><form:password path="pwd" /></td>
<td><form:errors path="pwd"
class="error" /></td>
</tr>
<tr>
<td>Email:</td>
<td><form:input path="email" /></td>
<td><form:errors path="email"
class="error" /></td>
</tr>
<tr>
<td>Phone Number:</td>
<td><form:input path="phno" /></td>
<td><form:errors path="phno"
class="error" /></td>
</tr>
<tr>
<td><input type="submit" value="Save"
/></td>
</tr>
</table>
</form:form>
</body>
</html>
----------------------------------------------------------------------
---
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
pageEncoding="ISO-8859-1"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="ISO-8859-1">
<title>Insert title here</title>
</head>
<body>
<h3>${msg}</h3>
<a href="user-form">Go Back</a>
</body>
</html>
----------------------------------------------------------------------
```
## Thymeleaf
-> We are developing web based applications using Spring Web MVC
Module

-> In Spring web mvc module we are using JSP as a presentation
technology

-> Traditionally this JSP is the default choice for generating html
files while building web based applications using java.

-> JSP is very matured technology

Some Important Points To Know About JSP

1) JSP is not a template engine it will not render web page directley
on browser

2) JSP files will be converted into servlet to produce response on the
browser

## Thymeleaf

-> Thymeleaf is true template engine

-> Thymeleaf takes HTML file, parse it and then produce web content on
browser directley

-> Thymeleaf is more like HTML

-> Using Thymeleaf we can display dynamic data in HTML files.

-> Spring Boot supports for Thymeleaf as a presentation technology

-> To work with thymeleaf in our project, we need to add below starter
```
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-thymeleaf<artifactId>
<dependency>
```
Note: We no need to do any other configurations to work with
thymeleaf. Spring Boot will take care of all configurations required
for thymeleaf

Developing First Application Using Thmeleaf
----------------------------------------------------------------------
-------------
1) Create Spring Boot application with below dependencies
a)web starter
b)thymeleaf starter
c)devtools
2) Create Controller class with required methods
3) Create HTML file using Thymeleaf template
Note-1 : View Files(HTML) should be created in
src/main/resources/templates
Note-2 : View extension will be .html only
4) Configure Server Port in application.properties file
5) Run the application and test it.

# RESTful Services (Spring With REST)
-> By using java we can develop several types of applications
1) Standalone/Desktop application
2) Web applications
3) Distributed applications (webservices)
![Ster](/images/intro.png)


## What is standlone application?

-> The application which runs in only one computer is called as
Standlone application.

Ex: Eclipse IDE, Calc, Notepad, Antivirus etc...

## What is web application?

-> The application whichs run over internet is called as Web
application.

Ex: gmail, facebook, linkdin, irctc, ashokit.in etc...

-> Multiple users can access web application at a time.

-> Web applications are used for customer 2 business communication
(C2B)

## what is distributed application?
**What is Distributed application & why ?**

-> If one application is communicating with another application then
it is called as Distributed application.

-> With the help of distributed application we can re-use business
services.

-> Distributed applications are meant for business to business
communication.

Passport app -----------> Aadhar app

Makemytrip app ----------> Air Lines, Hotels, irctc..

-> To develop Distributed applications we will use Webservices.

What is Webservice?

-> Webservices is a distributed technology which is used to develop
distributed applications with Intereoperability

What is Intereoperability?

Irrespective of the platform and irrespective of the programming
language if applications are communicating then they are called as
Intereoperable applications.
```
Java <--------> .Net
Python <-------> Salesforce
Angular <------> Java
Python <--------> Java
----------------------------------------------------------------------
```
-> In distributed applications consumer & provider applications will
exchange the data.

-> Provider application will decide input data structure & output data
structure.

Note: The data which is exchanging between consumer and provider
should be intereoperable.

-> XML, JSON and YML are universal data formats for exchanging data in
webservices.

Note: In distributed applications we should not exchange the data in
object format.

-> In Java we can develop distributed applications with
Intereoperability in 2 ways
1) Webservices (SOAP Webservices)
2) RESTful Services

# Distributed application architecture
-> In distributed applications 2 actors will be available
1) Provider App
2) Consumer App

-> The application which is giving business services to other
applications is called as provider application.

-> The application which is taking business services from other
applications is called as consumer application.

-> Provider and consumer applications will exchange the data in
intereoperable format.

-> XML, JSON and YAML are universal formats to exchange the data.

-> HTTP protocol will acts as mediator between Consumer and Provider
to exchange the data.

-> Distributed applications can be developed in 2 ways
1) SOAP Based Webservices (Outdated)
2) Restful Services (Trending)

-> SOAP Webservices can be developed in 2 ways
1) JAX-RPC api
2) JAX-WS api

-> RESTFul services can be developed in 2 ways
1) JAX-RS api
2) Spring with REST (Trending)
![Ster](/images/rest.png)

## RESTful Services Evolution
-> Initially people used to develop distributed applications with
intereoperablity using SOAP Webservices.

-> Over a period "Roy Fielding" identified some challenges to work
with Soap webservices.

-> Roy Fielding provided some architecture principles to develop
distributed applications with intereoperability.

-> If we develop our application by using "Roy Fielding" principles
then our application is called "Restful service".

## REST Architecture Principles
1) Unique Addressbility
2) Uniform Constraint Interfaces
3) Message Oriented Representation
4) Communication Stateless
5) HATEOS

## 1) Unique Addressbility
-> In Rest API we can write several methods to perform distributed
operations. Every method should have unique address. This is called as
Unique Addressability.
```java
public class IrctcRestApi{
("/ticket")
public Ticket BookTicket();
("/status")
public String getPnrStatus()
("/cancel")
public String cancelTicket();
("/trains")
public List<Train> getTrains();
}
```
## 2) Uniform Constraint Interfaces

-> All the methods which are available in Rest api should bind with
HTTP Protocol methods. This is called Uniform Constraint Interfaces.
```
GET Request ----> GET Method --------> @GetMapping
POST Request ----> POST method ------> @PostMapping
PUT Request -----> PUT method -------> @PutMapping
DELETE Request --> DELETE method ----> @DeleteMapping
```
## 3) Message Oriented Representation

-> Message Oriented Representation nothing but Provider and Consumer
can exchange the data as per their requirement.
![Ster](/images/orient.png)
## 4) Communication Stateless

-> Consumer request information should not be store at provider side.
Every request should be treated as first request only. This is called
communication stateless.
## 5) HATEOS

-> HATEOS stands for Hypermedia as an engine for application state.
Provider should send data to consumer in hypermedia format.
```
Few Important Annotations We will Use in Spring With REST
----------------------------------------------------------------------
---
@RestController : To represent java class as distributed component
@GetMapping : To bind our method to HTTP GET request
@PostMapping : To bind our method to HTTP POST Request
@PutMapping : To bind our method to HTTP PUT Request
@DeleteMapping : To bind our method to HTTP DELETE request
@RequestParam : To read query parameter from URL
@PathVariable : To read URI parameter/Path Parameter from URL
@RequestBody : To read data from Request Body sent by client
@ResponseBody : To represent data as response to client
----------------------------------------------------------------------
```

## Developing First REST API using Spring With REST
1) Create Spring Boot application with below dependencies

a)spring-boot-starter-web

b)spring-boot-devtools

2) Create Distributed Component Using @RestController
3) Write Required methods in Rest Controller and bind them to HTTP
Protocol methods
4) Configure Server Port Number in application.properties file
5) Run the application and test it.

## REST API development
```java
 package in.ashokit.rest;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
@RestController
@RequestMapping("/rest1")
public class GreetRestController {
@GetMapping
public String getGreetMsg() { // http://localhost:9090/rest1/
String msg = "Good Morning...!!";
return msg;
}
@GetMapping("/wish") // http://localhost:9090/rest1/wish
public String getWishMsg() {
String msg = "All the best..!!";
return msg;
}
@GetMapping("/quote") // http://localhost:9090/rest1/quote
public String getQuote() {
String msg = "Do or Die";
return msg;
}
}
----------------------------------------------------------------------
--
package in.ashokit.rest;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
@RestController
@RequestMapping("/rest2")
public class WelcomeRestController {
/*@GetMapping("/welcome") //http://localhost:9090/rest2/welcome
public String getWelcomeMsg() {
String msg = "Welcome To Ashok IT..!!";
return msg;
}*/
@GetMapping("/welcome")
public ResponseEntity<String> getWelcomeMsg(){
String msg = "Welcome To Ashok IT..!!";
return new ResponseEntity<String>(msg, HttpStatus.OK);
}
}
```
## REST API development
```java
package in.ashokit.rest;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
@RestController
@RequestMapping("/rest1")
public class GreetRestController {
@GetMapping
public String getGreetMsg() { // http://localhost:9090/rest1/
String msg = "Good Morning...!!";
return msg;
}
@GetMapping("/wish") // http://localhost:9090/rest1/wish
public String getWishMsg() {
String msg = "All the best..!!";
return msg;
}
@GetMapping("/quote") // http://localhost:9090/rest1/quote
public String getQuote() {
String msg = "Do or Die";
return msg;
}
}
----------------------------------------------------------------------
--
package in.ashokit.rest;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
@RestController
@RequestMapping("/rest2")
public class WelcomeRestController {
/*@GetMapping("/welcome")
public String getWelcomeMsg() { //
http://localhost:9090/rest2/welcome
String msg = "Welcome To Ashok IT..!!";
return msg;
}*/
@GetMapping("/welcome")
public ResponseEntity<String> getWelcomeMsg(){
String msg = "Welcome To Ashok IT..!!";
return new ResponseEntity<String>(msg, HttpStatus.OK);
}
}
```
-> ResponseEntity class we are using Construct response to client.

-> Using ResponseEntity class we can set "response body" and
"http status code" for response.

-> Consumer/Client application will send request to Provider/Resource
application.
## HTTP Status Codes

-> Provider/Resouce application will process the request and will send
response to Consumer/Client with HTTP Status Code.

-> HTTP Status Codes will tell how that request got processed by
server.
```
1xx - (100-199) Req info
2xx - (200 - 299 ) Success (ok)
3xx - (300 - 399) Redirect
4xx - (400 - 499) Client Error
5xx - (500 - 599) Server Error
```
## HTTP Protocol Structures
Http Protocol is divided into 2 parts
1) HTTP Request
2) HTTP Response
![Ster](/images/request.png)
![Ster](/images/response.png)

## HTTP Protocol Methods
-> Rest api methods should be binded to HTTP Protocol methods.

1) Get Request --------> @GetMapping
2) Post Request -------> @PostMapping
3) Put Request -------> @PutMapping
4) Delete Request ----> @DeleteMapping

## HTTP GET Method
-> If client wants to get data from rest api method, then that rest
api method should be binded to get request using @GetMapping
annotation.

-> GET Request will not contain request body

-> Clients can send data to rest api in the URL (query params & path
Params) while sending GET request.

## HTTP POST Method
-> If REST api method is responsible to create a new record with
client given data then we will bind that method to HTTP Post Method
using @PostMapping annotation.

-> HTTP Post Method will have request body.

-> Clients can send data to POST Request Method using
1) Path Params
2) Query Params
3) Request Body

-> Simple and text data can be sent in URL and
complex/secured/sensitive data should be sent in request body.

## HTTP PUT Request
-> HTTP PUT method is used for updating the existing record

-> If client wants to update any existing record then we should bind
our rest api method to HTTP PUT request using @PutMapping annotation

-> HTTP Put Request contains Request body.

-> Clients can send data to PUT Request Method using
1) Path Params
2) Query Params
3) Request Body

## HTTP DELETE

-> HTTP DELETE method is responsible for deleting the record

-> If client wants to delete any record using rest api method, then we
should bind that rest api method to DELETE request usign
@DeleteMapping annotation.

-> HTTP DELETE will have Request Body.

-> Clients can send data to DELETE Request Method using
1) Path Params
2) Query Params
3) Request Body

## Message Oriented Representation
-> Client app & Resource app can exchange the data as per their
requirement.
![Ster](/images/object.png)

## Message Converters
-> MessageConverters are used to convert the data into intereoperable
format

-> We are using objects for rest api methods development.

-> Method parameter and method return type we are using as object.

-> If we use objects then our methods will be loosely coupled with
format of data.
```java
@RestController
public class BookRestController {
@GetMapping("/book")
public Book getBookData() {
Book b = new Book(101, "Spring", 450.00);
return b;
}
}
----------------------------------------------------------------------
```

Q-1) As the above rest api method is returning object, client will
recieve that object or will not recieve that object?

Ans) Client will not recieve object bcz of intereoperability. Rest api
side Message Converters will be available to convert object data into
client understandable format.

Q-1) How Rest api will understand client expected format for response
data?

Ans) Client should send "Accept" header with expected response format
in the request.

Accept = application/xml (For xml response)

Accept = application/json (For json response)

## How Java Object Is Converted To Json ?

-> To convert Java Object To Json format we have JACKSON api.

-> Jackson is a third party api which is getting used in Spring Boot.

-> MessageConverters will use Jackson api to deal with JSON data.

-> Using Jackson api we can convert java object to json and json to
java obj.
```java
----------------------------------------------------------------------
------------
<dependency>
<groupId>com.fasterxml.jackson.core</groupId>
<artifactId>jackson-databind</artifactId>
</dependency>
----------------------------------------------------------------------
------------
package in.ashokit;
import com.fasterxml.jackson.databind.ObjectMapper;
public class JavaToJsonConverter {
public static void main(String[] args) throws Exception {
Book book = new Book();
book.setBookId(101);
book.setBookName("Spring");
book.setBookPrice(450.00);
// convert book data into json
ObjectMapper mapper = new ObjectMapper();
String json = mapper.writerWithDefaultPrettyPrinter()
.writeValueAsString(book);
System.out.println(json);
}
}
----------------------------------------------------------------------
```
![Ster](/images/converter.png)

## How to convert Java object to XML format?

-> To convert java object to xml format we will use JAX-B api.

-> JAX-B api is part of jdk. We no need to add any dependency for
JAXB.

-> To convert java object into xml format, that java class should have
@XmlRootElement annotation.
```java
package in.ashokit;
import javax.xml.bind.annotation.XmlRootElement;
@XmlRootElement
public class Book {
private Integer bookId;
private String bookName;
private Double bookPrice;
public Integer getBookId() {
return bookId;
}
public void setBookId(Integer bookId) {
this.bookId = bookId;
}
public String getBookName() {
return bookName;
}
public void setBookName(String bookName) {
this.bookName = bookName;
}
public Double getBookPrice() {
return bookPrice;
}
public void setBookPrice(Double bookPrice) {
this.bookPrice = bookPrice;
}
@Override
public String toString() {
return "Book [bookId=" + bookId + ", bookName=" +
bookName + ", bookPrice=" + bookPrice + "]";
}
}
----------------------------------------------------------------------
-----------
package in.ashokit;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.Marshaller;
public class JavaToXmlConverter {
public static void main(String[] args) throws Exception {
Book b = new Book();
b.setBookId(201);
b.setBookName("Hibernate");
b.setBookPrice(500.00);
JAXBContext context =
JAXBContext.newInstance(Book.class);
Marshaller marshaller = context.createMarshaller();
marshaller.marshal(b, System.out);
}
}
```
## JSON Data Using Jackson api

-> JSON stands for Java Script Object Notation

-> JSON will represent data in key-value format

-> JSON is platform independent and language independent

-> JSON is called as Intereoperable format

-> Now a days applications are using JSON format to exchange the data
```
Syntax:
{
"id" : 101,
"name" : "Ashok",
"phno" : 6301921083
}
```
-> Jackson api provided ObjectMapper class to perform convertions

-> The process of converting Java object to JSON format is called as
"Serialization"

-> The process of converting JSON data to Java object is called as
"De-Serialization"

-> To convert object data to json and json data to object we need
class which represents structure of the JSON.
```java
----------------------------------------------------------------------

@Data
@JsonInclude(value = Include.NON_NULL)
public class Book {
@JsonProperty("bid")
private Integer bookId;
private String bookName;
private Double bookPrice;
}
----------------------------------------------------------------------
-----------
package in.ashokit;
import com.fasterxml.jackson.databind.ObjectMapper;
public class JavaToJsonConverter {
public static void main(String[] args) throws Exception {
Book book = new Book();
book.setBookId(101);
book.setBookName("Spring");
book.setBookPrice(450.00);
// convert book data into json
ObjectMapper mapper = new ObjectMapper();
String json =
mapper.writerWithDefaultPrettyPrinter().writeValueAsString(book);
System.out.println(json);
}
}
----------------------------------------------------------------------
-----------
package in.ashokit;
import java.io.File;
import com.fasterxml.jackson.databind.ObjectMapper;
public class JsonToJavaConverter {
public static void main(String[] args) throws Exception {
File file = new File("Book.json");
ObjectMapper mapper = new ObjectMapper();
Book book = mapper.readValue(file, Book.class);
System.out.println(book);
}
}
----------------------------------------------------------------------
-------------
{
"bookName": "Spring",
"bookPrice": 450.0,
"bid": 101
}
----------------------------------------------------------------------
-----------
String json writeValueAsString(Object) ---- converts obj to
json
Object readValue(File f, Class type) ---> converts json to obj
----------------------------------------------------------------------
```
## XML Data with JAX-B Api
-> XML stands for Extensible Markup Language

-> XML is platform independent and language independent

-> XML will represent data in in the form of elements
```
<person>
<id>101</id>
<name>Ashok</name>
<phno>6301921083</phno>
</person>
```
-> To convert Java Object to xml and xml to java object we have JAX-B
api.

-> The process of converting java obj to xml is called as
"Marshalling"

-> The process of converting xml data to java obj is called as "Un-
Marshalling"

-> To perform Marshalling and Un-Marshalling we need to have binding
class

-> The java class which is representing stucture of xml is called as
Binding class

-> To represent java class as Binding class we will use
@XmlRootElement annotation.
```java
----------------------------------------------------------------------
@XmlRootElement
public class Book {
private Integer bookId;
private String bookName;
private Double bookPrice;
}
----------------------------------------------------------------------
----------
package in.ashokit;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.Marshaller;
public class JavaToXmlConverter {
public static void main(String[] args) throws Exception {
Book b = new Book();
b.setBookId(201);
b.setBookName("Hibernate");
b.setBookPrice(500.00);
JAXBContext context =
JAXBContext.newInstance(Book.class);
Marshaller marshaller = context.createMarshaller();
marshaller.marshal(b, System.out);
}
}
----------------------------------------------------------------------
---------
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<book>
<bookId>201</bookId>
<bookName>Hibernate</bookName>
<bookPrice>500.0</bookPrice>
</book>
----------------------------------------------------------------------
----------
package in.ashokit;
import java.io.File;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.Unmarshaller;
public class XmlToJavaConverter {
public static void main(String[] args) throws Exception {
JAXBContext context =
JAXBContext.newInstance(Book.class);
Unmarshaller unmarshaller =
context.createUnmarshaller();
Object object = unmarshaller.unmarshal(new
File("Book.xml"));
Book b = (Book) object;
System.out.println(b);
}
}
----------------------------------------------------------------------
```
## Query Parameters

-> Query Parameters are used to send data to server in the URL

-> Query Parameters will represent data in Key-Value format

-> Query Parameters will start with '?'

-> Query Parameters will be seperated by '&'

Ex-1: www.ashokit.in/courses?name=SBMS

Ex-2: www.ashokit.in/courses?name=SBMS&trainer=Ashok

**Note**: Query Parameters should present only at the end of the URL.

-> At REST API side we will use @RequestParam annotation to read query
parameters from URL

URL : www.ashokit.in/courses?name=SBMS

public String getCourseDetails(@RequestParam String name){..}

```java
@RestController
public class CourseRestController {
@GetMapping(value = "/course")
public String getCourseDetails(@RequestParam("name") String
name) {
String msg = "";
if ("SBMS".equals(name)) {
msg = "New Batch for SBMS From 15-Jul-2021
@8:00 PM IST";
} else if ("JRTP".equals(name)) {
msg = "New Batch For JRTP From 14-Jul-2021
@11:30 AM IST";
} else if ("AWS".equals(name)) {
msg = "New Batch For AWS From 13-Jul-2021
@7:00 PM IST";
} else {
msg = "Please visit www.ashokit.in for more
details";
}
return msg;
}
@GetMapping(value = "/fee")
public String getCourseFee(@RequestParam("cname") String
cname, @RequestParam("tname") String tname) {
String msg = cname + " By " + tname + " is 5000 INR
Only";
return msg;
}
}
----------------------------------------------------------------------
--
Endpoint-1 URL : http://localhost:9090/course?name=AI
Endpoint-2 URL : http://localhost:9090/fee?cname=JRTP&tname=Ashok
```
## Path Parameters
-> Path Parameters also used to send data to server in the URL

-> Path Parameters are also called as URI parameters

-> Path Parameters can present anywhere in the URL

-> Path Parameters will start with '/' and will be seperated by '/'
only

-> Path Parameters will represent the value directley

Ex-1: www.ashokit.in/course/{SBMS}

Ex-2: www.ashokit.in/course/{SBMS}/trainer/{Ashok}

-> At Rest api side we will use @PathVariable annotation to read Path
Parameter value from URL
```java
@RestController
public class CourseRestController {
@GetMapping("/course/{cname}/trainer/{tname}")
public String getCourseDetails(@PathVariable String cname,
@PathVariable String tname) {
String msg = cname + " By " + tname + " Starting From
15-Jul-2021 @8:00 PM IST";
return msg;
}
}
```
-> In C 2 B (Customer To Business) applications we will use Query Params

-> In B 2 B (Business To Business) applications we will use Path Params.

## Challenges with Path Params & Query Params
-> Data Will Be Exposed in URL

-> Not recommended to send sensitive data

-> Bindary Data we can't send

-> URL length limitation  (2038 characters)

Note: If we want to send any non-sensitive and small info to server then we can use Path Params & Query Params.

-> To overcome the limitations of Path Params & Query Params we will use Request Body to send data to server.

Note: GET request doesn't have body. POST , PUT and DELETE requests are having request body.

## HTTP POST Request
-------------------------------------------------------------------------
-> If REST api method is responsible to create a new record/resource at server then we will bind our method to HTTP POST request using @PostMapping annotation.

-> HTTP Post request will have request body

-> Client can send data to HTTP POST Request method using Request Body.

-> HTTP Request Methods will return status code as "201 Created"

![Ster](/images/post.png)


```java
-------------------------------------------------------------------------
package in.ashokit.bindings;

import javax.xml.bind.annotation.XmlRootElement;

@XmlRootElement
public class User {

	private Integer uid;
	private String fname;
	private String lname;
	private String email;

	public User() {
		System.out.println("User::Constructor");
	}

	public Integer getUid() {
		return uid;
	}

	public void setUid(Integer uid) {
		this.uid = uid;
	}

	public String getFname() {
		return fname;
	}

	public void setFname(String fname) {
		this.fname = fname;
	}

	public String getLname() {
		return lname;
	}

	public void setLname(String lname) {
		this.lname = lname;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}

	@Override
	public String toString() {
		return "User [uid=" + uid + ", fname=" + fname + ", lname=" + lname + ", email=" + email + "]";
	}

}
-----------------------------------------------------------------------
package in.ashokit.rest;

import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

import in.ashokit.bindings.User;

@RestController
public class UserRestController {

	@PostMapping(
			value = "/user",
			consumes = { "application/json", "application/xml" },
			produces = { "text/plain" }
	)
	public String addUser(@RequestBody User user) {
		System.out.println(user);
		String msg = "User Saved Successfully..";
		return msg;
	}
}

------------------------------------------------------------------------
{
    "uid" : 101,
    "fname" : "Ashok",
    "lname" : "IT",
    "email" : "ashokit@gmail.com"
}

---------------------------------------------------------------------
<user>
	<uid>102</uid>
	<fname>Ashok</fname>
	<lname>IT</lname>
	<email>ashokit@gmail.com</email>
</user>
-------------------------------------------------------------------------
```
## Consumes & Content-Type
-> "consumes" attribute will represent in which format rest api method can take the input data.

-> "Content-Type" header is used to represent in which format client is sending data to server in request body.

1) produces : Represent method supporting response formats
2) Accept : Represents client expecting response format

3) consumes : Represent method supporting request formats
4) Content-Type : Represents client sending request format
![Ster](/images/consume.png)


## Procedure to follow to develop REST API
1) Collect Requirements from functional team

2) Analyze the requirements

3) Analyze Risk Factor

4) Decide Input and Output Formats (Consumes & Produces)

5) Design Request Binding Classes

6) Design Response Binding Classes

7) Create Rest Controller with Endpoint method(s)

8) Implement Business logic

9) Test api functionality using POSTMAN

## IRCTC App
```java
-----------------------------------------------------------------------------
package com.azhar.binding;

import javax.xml.bind.annotation.XmlRootElement;

import lombok.Data;

@Data
@XmlRootElement
public class PassengerRequest {

	
	private String name;
	private String dob;
	private String gender;
	private String doj;
	private String from;
	private String to;
	private String train;
	
}
-----------------------------------------------------------------------------
@Data
@XmlRootElement
public class TicketResponse {

	private String name;
	private String dob;
	private String gender;
	private String doj;
	private String from;
	private String to;
	private String train;
	
	private String status;
	private double price;
}
-----------------------------------------------------------------------------
package com.azhar.rest;

import org.springframework.beans.BeanUtils;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

import com.azhar.binding.PassengerRequest;
import com.azhar.binding.TicketResponse;

@RestController
public class IrctcRestController {

	@PostMapping(
			value="/bookticket",
			consumes={
					"application/json",
			        "application/xml"
			},
			produces= {
					"application/json",
					"application/xml"
		    }	
       )
	public ResponseEntity<TicketResponse> savePassDetails(@RequestBody PassengerRequest request) {
		
		//logic to book ticket
		TicketResponse response = new TicketResponse();
		response.setStatus("CONFIRMED");
		response.setPrice(1500);
		BeanUtils.copyProperties(request, response);
		return new ResponseEntity<TicketResponse>(response, HttpStatus.CREATED);
	}
}
data-----------------------------------
{
	
"name" : "Ashok",
 "dob" : "20-May-1991",
 "gender" : "M",
 "doj" : "20-Jul-2021",
 "from" : "Hyd",
 "to" : "Pune",
 "train" : 1356

}
---------------------------response-data---------------------------------
{
    "name": "Ashok",
    "dob": "20-May-1991",
    "gender": "M",
    "doj": "20-Jul-2021",
    "from": "Hyd",
    "to": "Pune",
    "train": "1356",
    "status": "CONFIRMED",
    "price": 1500.09
}
------------------------------------------------------------------------
```
## How HTTP Post request working  internally
1) DispatcherServlet process
2) HandlerMapper
3) HttpMsgBodyReader
4) HttpMsgBodyWriter
5) MessageConverters
6) consumes
7) produces
8) Content-Type
9) Accept

![Ster](/images/inter.png)

##  HTTP GET request
-> If rest api method is responsible to provide data to client then we will bind our method to HTTP GET request using @GetMapping annotation.

-> HTTP GET request will not have any request body

-> If client wants to send some data to rest api GET request method then client will use Query Parameters & Path Parameters

-> By default DispatcherServlet will decide HTTP Status code and will send to client.

-> If we want to customize Http status code for our method we can use ResponseEntity.

Syntax :    ResponseEntity(T body, HttpStatus status)

Ex:	    new ResponseEnity(ticketResObj, HttpStatus.CREATED);

## HTTP PUT Request
-> If our REST API method is responsible to update an existing record then we will bind our method to HTTP PUT Request Method.

-> HTTP PUT Request method contains Request body.
```java
@PutMapping
public ResponseEntity<String> updateTicket(@RequestBody Ticket request){

	//logic to update the ticket

	return new ResponseEntity<>(body, HttpStatus.OK);
}
```
## HTTP DELETE Request

-> If our REST API method is responsible for deleting a record then we will bind that method to HTTP DELETE Request using @DeleteMapping annotation.

-> HTTP DELETE request contains Request Body
```java
@DeleteMapping("/delete/{empId}")
public String deleteEmp(@PathVariable Integer empId){
	//logic to delete record

      return "Record Deleted";
}
```
## REST Documentation
-> REST APIs we are developing for business to business communications (B 2 B).

-> REST Apis are distributed applications with intereoperability.

-> When we are developing distributed applications one application will act as Resource Application and another application will act as client application.


	Client App (Consumer) <-----------------------> Resource App (Provider)

-> If client wants to access our API they should know details of our api.

-> Resource App development team should provide API documentation to Client App development team.

-> API documentation should contain below details

		1) Endpoint URL
		2) Request Method
		3) consumes formats
		4) produces formats
		5) Request data structure
		6) Response data structure

-> Swagger is one of the famous software which is used to generate REST API documentation.

-> Swagger UI is providing user interface for testing REST APIs.
![Ster](/images/develope.png)

## Configuring Swagger In Rest API
---------------------------------------------------------------------------------
1) Add below 2 dependencies in pom.xml file

		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger2</artifactId>
			<version>2.8.0</version>
		</dependency>

		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger-ui</artifactId>
			<version>2.8.0</version>
			<scope>compile</scope>
		</dependency>

2) Create SwaggerConfig class like below
```java
@Configuration
@EnableSwagger2
public class SwaggerConfig {
	
	@Bean
	public Docket apiDoc() {
		
		return new Docket(DocumentationType.SWAGGER_2)
				   .select()
				   .apis(RequestHandlerSelectors.basePackage									("in.ashokit.rest"))
				   .paths(PathSelectors.any())
				   .build();
	}
}
```
-> Once above 2 steps are completed then we can access Swagger Documentation and Swagger UI using below URLS

	Documentation : http://localhost:9090/v2/api-docs

	Swagger UI : http://localhost:9090/swagger-ui.html

## REST Client Introduction
-> The application which is accessing business services from other applications is called as Client application.

-> In order to access business services we will develop Rest Client

-> Using Spring we can develop Rest client in 2 ways

		1) RestTemplate (Sync) - Outdated
		2) WebClient (Sync & Async)
-> RestTemplate is a predefined class which supports for Synchronus communication. This is old approach.

-> Synchronus means after making a request it will wait for response.

-> WebClient got introduced in Spring 5.x version. It supports both Synchronus and Asynchronus communications. It is trending in the market.

-> Asynchronus means after making a request without waiting for response it will continue its execution.

-> In order to develop REST Client we need to know API details like below

		1) Endpoint URL
		2) Request Method Type
		3) Consumes
		4) Produces
		5) Input Data Structure
		6) Output Data Structure
-------------------------------------------------------------------------------------------
API URL : https://jsonplaceholder.typicode.com/posts/1

Request Method : GET

consumes : N/A
produces : application/json

Request data structure : N/A
Response data structure : 
```
{
    "type": "success",
    "value": {
        "id": 11,
        "quote": "I have two hours today to build an app from scratch. @springboot to the rescue!"
    }
}
```
![Ster](/images/client.png)

## RestTemplate details
-> RestTemplate is a pre-defined class available in Spring Web MVC module

-> Using RestTemplate we can send Http Request to server (Synchronus)

-> After Making a Request to Server, RestTemplate will wait for response to continue its execution.

-> RestTemplate provided several methods to send Http Request to server

```java
package com.azhar.response;

import lombok.Data;

@Data
public class QuoteApiResponse {

	private Integer userId;
	private Integer id;
	private String title;
	private String body;
	
	
	
}
---------------------------------------------------------------------------
package com.azhar.service;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;

import com.azhar.response.QuoteApiResponse;

@Service
public class QuoteService {

	public String invokeQuoteApi() {
		String url = "https://jsonplaceholder.typicode.com/posts/1 ";
		RestTemplate restTemplate= new RestTemplate();
		ResponseEntity<String> entity = restTemplate.getForEntity(url, String.class);
		int statusCode = entity.getStatusCodeValue();
		if (statusCode==200) {
			String body = entity.getBody();
			return body;
		}
		else {
			return null;
		}
			
	}
		public String invokeRandomQuoteApi() {
			String url = "https://jsonplaceholder.typicode.com/posts/1 ";
			RestTemplate restTemplate= new RestTemplate();
			ResponseEntity<QuoteApiResponse> entity = restTemplate.getForEntity(url, QuoteApiResponse.class);
			int statusCode = entity.getStatusCodeValue();
			if (statusCode==200) {
				QuoteApiResponse body = entity.getBody();
				String string = body.getBody().toUpperCase();
				return string;
				
			}
			else {
				return null;
			}
	}
}

-------------------------------------------------------------------------
package com.azhar.rest;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import com.azhar.service.QuoteService;

@RestController
public class QuoteRestController {

	@Autowired
	private QuoteService quoteService;
	
	@GetMapping("/quote")
	public String getQuote() {
	    	
		//String quote = quoteService.invokeQuoteApi();  //Approach1
		String quote = quoteService.invokeRandomQuoteApi();
		return quote;
	}
	
	
}

-------------------------------------------------------------------
```
![Ster](/images/resource.png)

![Ster](/images/restt.png)

## RestClient development using RestTemplate for United Flights API
United Flights API Details
Endpoint URL : http://mu.mulesoft-training.com/essentials/united/flights/

Request Method : GET

consumes: NA

produces : application/json

Input : NA

Output : Flights Info in Json format

## Project Structure
![Ster](/images/invoke.png)


```java
package com.azhar.controller;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import com.azhar.response.Flight;
import com.azhar.service.UnitedFlightService;

@RestController
public class FlightController {

	@Autowired
	private UnitedFlightService service;
 
	@GetMapping("/flight")
	public String getFlightInfo() {
		String schedule = service.invokeFlightSchedule();
		return schedule;
		
	}
	@GetMapping(value="/flightdata",produces= {"application/json"})
	public List<Flight> getUnitedFlightSchedule() {
		List<Flight> schedule = service.invokeUnitedFlightSchedule();
		System.out.println(schedule);
		return schedule;
	}
	
}-----------------------------------------------------------------------------
package com.azhar.service;

import java.util.List;

import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;

import com.azhar.response.Flight;
import com.azhar.response.Root;

@Service
public class UnitedFlightService {

	public String invokeFlightSchedule() {
		String url = "http://mu.mulesoft-training.com/essentials/united/flights/";
		RestTemplate restTemplate = new RestTemplate();
		ResponseEntity<String> forEntity = restTemplate.getForEntity(url, String.class);
		int codeValue = forEntity.getStatusCodeValue();
		if (codeValue==200) {
		String body = forEntity.getBody();
			return body;
		}
		else {
			return null;
		}
		
	}
	
	public List<Flight> invokeUnitedFlightSchedule() {
		String url = "http://mu.mulesoft-training.com/essentials/united/flights/";
		RestTemplate restTemplate = new RestTemplate();
		ResponseEntity<Root> forEntity = restTemplate.getForEntity(url, Root.class);
		int codeValue = forEntity.getStatusCodeValue();
		if (codeValue==200) {
		Root root = forEntity.getBody();
			 List<Flight> flight = root.getFlights(); 
			 return flight;
		}
		else {
			return null;
		}
		
	}
}

---------------------------------------------------------------------------
package com.azhar.response;

import lombok.Data;

@Data
public class Flight {

	private String code;
	private Float price;
	private String origin;
	private String destination;
	private String departureDate;
	private String airlineName;
	private String planeType;
	private Integer emptySeats;
	
}
--------------------------------------------------------------------------
package com.azhar.response;

import java.util.List;

import lombok.Data;
@Data
public class Root {

	private List<Flight> flights;
	
}
--------------------------------------------------------------------------
```
# How to send POST request in RestClient
# How to send headers in request & exchange method
-> To set headers to the request we will use HttpHeaders class

-> To add Headers to the request we will uses HttpEntity class

		HttpHeaders headers = new HttpHeaders();
			headers.set(key1,value1);
			headers.set(key2,value2);

		HttpEntity entity = new HttpEntity(body,headers);

		rt.exchange(Url, Method, entity, type);
```java
package com.azhar.request;

import lombok.Data;

@Data
public class PassengerRequest {

	private String dob;
	private String doj;
	private String from	;
	private String gender;
	private String name;
	private String to;
	private String train;
}
-----------------------------------------------------------------------------
package com.azhar.response;

import lombok.Data;

@Data
public class TicketResponse {

	private String dob;
	private String doj;
	private String from;
	private String gender;
	private String name;
	private Double price;
	private String status;
	private String to;
	private String train;
}
-----------------------------------------------------------------------------
package com.azhar.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

import com.azhar.request.PassengerRequest;
import com.azhar.response.TicketResponse;
import com.azhar.service.MakeMyTripService;

@RestController
public class TicketBookingRestController {

	@Autowired
	private MakeMyTripService makeMyTripService;

	@PostMapping(value = "/bookTicket", 
			    consumes = { "application/json", "application/xml" }, 
			    produces = { "application/json", "application/xml" })
	public TicketResponse invokeBookTicket(@RequestBody PassengerRequest request) {
		TicketResponse ticketResponse = makeMyTripService.initiateTicketBookProcess(request);
		return ticketResponse;

	}
}
----------------------------------------------------------------------------
package com.azhar.service;

import org.springframework.http.HttpEntity;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpMethod;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;

import com.azhar.request.PassengerRequest;
import com.azhar.response.TicketResponse;

@Service
public class MakeMyTripService {

	public TicketResponse initiateTicketBookProcess(PassengerRequest request) {
		String irctcUrl = "http://localhost:9090/bookticket";
		RestTemplate restTemplate = new RestTemplate();
//        ResponseEntity<TicketResponse> postForEntity = restTemplate.postForEntity(irctcUrl, request, TicketResponse.class);
//        int value = postForEntity.getStatusCodeValue();
//        if (value ==201) {
//			TicketResponse body = postForEntity.getBody();
//			return body;
//		}
//        else {
//        	return null;
//        }
		
		HttpHeaders headers= new HttpHeaders();
		headers.set("Accept", "application/json");
		headers.set("Content-Type", "application/json");
		HttpEntity<PassengerRequest> entity= new HttpEntity<>(request,headers);
		ResponseEntity<TicketResponse> postForEntity = restTemplate.postForEntity(irctcUrl,entity, TicketResponse.class);
		ResponseEntity<TicketResponse> exchange = restTemplate.exchange(irctcUrl,HttpMethod.POST, entity, TicketResponse.class);
		TicketResponse response = postForEntity.getBody();
		return response;
		
		
	}
}
---------------------------------------------------------------------------
```
Q) What is Synchronus Communication?
------------------------------------
-> After making the request if our thread is waiting for the response to continue its execution then it is called as Sync communication.


Q) What is Asynchronus Communication?
------------------------------------
-> After making the request if our thread will continue its execution without waiting for the response then it is called as Aync communication.

------------------------------------------------------------------------

-> RestTemplate class is part of spring-boot-starter-web dependency where as WebClient is part of spring-boot-starter-webflux dependency
## UseCases 


![Ster](/images/usecase.png)

## WebClient
-> WebClient got introduced in Spring 5.x version

-> Using WebClient api we can developed rest clients

-> WebClient supports for both Synchronous and Asynchronous communication
-> To use WebClient in our application we have to add below dependency in pom.xmlfile
         springboot starter webflux(reactive web)
```java
         WebClient webClient= WebClient.create();
		     webClient.get()
			          .uri(endPointUrl)
					  .retrieve()
					  .bodyToMono(String.class)
					  .block()
		// further logic to deal with response			  
