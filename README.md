
## spring-boot-features
1. Spring Boot starter POMs(to simplify maven configuration)
2. Dependency version conflicts resolved
3. Auto-cofiguration
4. Acutuators (Production read features of application)
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

What is HandlerMapper?


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

