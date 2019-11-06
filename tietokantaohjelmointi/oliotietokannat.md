# JPA ja Oliotietokannat

## Yleistä

Java-sovellukset käsittelevät tietoa Luokkina ja olioina. Tietokannat puolestaan käsittelevät tietoa taulukoissa olevissa riveissä. Olion tietokenttien vieminen tietokantaan vaatii jokaisen tietokentän kuvaamista taulukon sarakkeena ja sen sisältämän tiedon syöttämistä tietokantaan. Tästä käytetään nimeä Object Relational Mapping \(ORM\). Tällainen tietorakenteiden välinen tiedon siirtely tuottaa paljon toisteista koodia ja päänvaivaa ohjelmoijalle. Tilannetta helpottamaan on kehitetty tekniikka nimeltä JPA.

## Mikä on JPA

**Java Persistence API** \(**JPA**\) on Javan ohjelmointirajanpinta jonka tarkoituksena on helpottaa tietokantojen ja olioiden yhteiskäyttöä ohjelmissa. Ajatuksena on mahdollistaa sovellusriippumaton tapa säilöä Java-olioita relaatiotietokannan riveinä, sekä kohdistaa niihin hakuja ja palauttaa tietokannan rivit helposti taas tarvittaessa olioiksi. 

JPA rajapinan kautta on mahdollista käyttää monia erilaisia teknisiä ORM-ratkaisuja \(vertaa JDBC ja eri tietokannat\). Esimerkkitoteutus tunnetaan nimellä [EclipseLink](https://en.wikipedia.org/wiki/EclipseLink) ja muita tunnettuja ratkaisuja ovat esim. Hibernate ja Spring Data JPA. Ohjelmakoodin ei siis tarvitse muuttua vaikka taustalla käystettävää teknologista ratkaisua vaihdettaisiin.

## JPA käytännössä

Määritellään luokka nimeltä Asiakas. Se on rakenteeltaan tavanomainen Java-luokka, mutta siihen voidaan määritellä @-tägeillä ORM:iin liittyviä määreitä. Esim. olion tietokannassa yksilöivä tunniste määritetään tägillä @Id \(rivi 24\).

{% code-tabs %}
{% code-tabs-item title="Emplyee.java" %}
```java
import javax.persistence.Entity;
import javax.persistence.Id;

@Entity
public class Employee {

	private int eid;
	private String ename;
	private double salary;
	private String deg;

	public Employee(int eid, String ename, double salary, String deg) {
		super();
		this.eid = eid;
		this.ename = ename;
		this.salary = salary;
		this.deg = deg;
	}

	public Employee() {
		super();
	}

	@Id
	public int getEid() {
		return eid;
	}

	public void setEid(int eid) {
		this.eid = eid;
	}

	public String getEname() {
		return ename;
	}

	public void setEname(String ename) {
		this.ename = ename;
	}

	public double getSalary() {
		return salary;
	}

	public void setSalary(double salary) {
		this.salary = salary;
	}

	public String getDeg() {
		return deg;
	}

	public void setDeg(String deg) {
		this.deg = deg;
	}

	@Override
	public String toString() {
		return "Employee [eid=" + eid + ", ename=" + ename + ", salary=" + salary + ", deg=" + deg + "]";
	}

}

```
{% endcode-tabs-item %}
{% endcode-tabs %}

Määritellään META-INF -kansioon persistence.xml-tiedosto. Tiedosto määrittelee tietokantayhteyden sekä sen, loggauksen tason sekä miten ORM toimii tietyissä tilanteissa, esim. jos taulua ei ole olemassa kun tietoa yritetään tallentaa.

{% code-tabs %}
{% code-tabs-item title="Persistence.xml" %}
```markup
<?xml version="1.0" encoding="UTF-8"?>
<persistence version="2.1"
	xmlns="http://xmlns.jcp.org/xml/ns/persistence"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_1.xsd">
	<persistence-unit name="JPADemo">
		<jta-data-source> </jta-data-source>
		<class>Employee</class>
		<properties>
			<property name="javax.persistence.jdbc.url" value="jdbc:mysql://localhost:8082/jpademo" />
			<property name="javax.persistence.jdbc.user" value="root" />
			<property name="javax.persistence.jdbc.driver" value="com.mysql.jdbc.Driver" />
			<property name="eclipselink.logging.level" value="FINE" />
			<property name="eclipselink.ddl-generation" value="create-tables" />
			<property name="javax.persistence.schema-generation.database.action" value="create"/>
		</properties>
	</persistence-unit>
</persistence>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Työntekijän tallentaminen

{% code-tabs %}
{% code-tabs-item title="CreateEmplyee.java" %}
```java
import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.Persistence;
 

public class CreateEmployee {

   public static void main( String[ ] args ) {
      EntityManagerFactory emfactory = Persistence.createEntityManagerFactory( "JPADemo" );   
      EntityManager entitymanager = emfactory.createEntityManager( );
      entitymanager.getTransaction( ).begin( );

      Employee employee = new Employee( ); 
      employee.setEid( 1301 );
      employee.setEname( "Martin" );
      employee.setSalary( 50000 );
      employee.setDeg( "Technical Manager" );
      
      entitymanager.persist( employee );
      entitymanager.getTransaction( ).commit( );

      entitymanager.close( );
      emfactory.close( );
   }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Koodin suoritus

```text
[EL Config]: metadata: 2019-11-06 14:08:53.407--ServerSession(1076835071)--Thread(Thread[main,5,main])--The access type for the persistent class [class Employee] is set to [PROPERTY].
[EL Config]: metadata: 2019-11-06 14:08:53.424--ServerSession(1076835071)--Thread(Thread[main,5,main])--The alias name for the entity class [class Employee] is being defaulted to: Employee.
[EL Config]: metadata: 2019-11-06 14:08:53.425--ServerSession(1076835071)--Thread(Thread[main,5,main])--The table name for entity [class Employee] is being defaulted to: EMPLOYEE.
[EL Config]: metadata: 2019-11-06 14:08:53.436--ServerSession(1076835071)--Thread(Thread[main,5,main])--The column name for element [getEid] is being defaulted to: EID.
[EL Config]: metadata: 2019-11-06 14:08:53.437--ServerSession(1076835071)--Thread(Thread[main,5,main])--The column name for element [getEname] is being defaulted to: ENAME.
[EL Config]: metadata: 2019-11-06 14:08:53.437--ServerSession(1076835071)--Thread(Thread[main,5,main])--The column name for element [getDeg] is being defaulted to: DEG.
[EL Config]: metadata: 2019-11-06 14:08:53.438--ServerSession(1076835071)--Thread(Thread[main,5,main])--The column name for element [getSalary] is being defaulted to: SALARY.
[EL Warning]: transaction: 2019-11-06 14:08:53.446--ServerSession(1076835071)--Thread(Thread[main,5,main])--PersistenceUnitInfo JPADemo has transactionType RESOURCE_LOCAL and therefore jtaDataSource will be ignored
[EL Info]: 2019-11-06 14:08:53.447--ServerSession(1076835071)--Thread(Thread[main,5,main])--EclipseLink, version: Eclipse Persistence Services - 2.5.2.v20140319-9ad6abd
[EL Fine]: connection: 2019-11-06 14:08:53.654--Thread(Thread[main,5,main])--Detected database platform: org.eclipse.persistence.platform.database.MySQLPlatform
[EL Config]: connection: 2019-11-06 14:08:53.663--ServerSession(1076835071)--Connection(1177377518)--Thread(Thread[main,5,main])--connecting(DatabaseLogin(
	platform=>MySQLPlatform
	user name=> "root"
	datasource URL=> "jdbc:mysql://localhost:8082/jpademo"
))
[EL Config]: connection: 2019-11-06 14:08:53.667--ServerSession(1076835071)--Connection(1725017993)--Thread(Thread[main,5,main])--Connected: jdbc:mysql://localhost:8082/jpademo
	User: root@
	Database: MySQL  Version: 5.5.5-10.3.16-MariaDB
	Driver: MySQL Connector Java  Version: mysql-connector-java-5.1.48 ( Revision: 29734982609c32d3ab7e5cac2e6acee69ff6b4aa )
[EL Info]: connection: 2019-11-06 14:08:53.692--ServerSession(1076835071)--Thread(Thread[main,5,main])--file:/C:/Users/miksten/eclipse-workspace2/UusiJPA/build/classes/_JPADemo login successful
[EL Fine]: sql: 2019-11-06 14:08:53.726--ServerSession(1076835071)--Connection(1725017993)--Thread(Thread[main,5,main])--CREATE TABLE EMPLOYEE (EID INTEGER NOT NULL, DEG VARCHAR(255), ENAME VARCHAR(255), SALARY DOUBLE, PRIMARY KEY (EID))
[EL Fine]: sql: 2019-11-06 14:08:53.77--ClientSession(558187323)--Connection(1725017993)--Thread(Thread[main,5,main])--INSERT INTO EMPLOYEE (EID, DEG, ENAME, SALARY) VALUES (?, ?, ?, ?)
	bind => [1301, Technical Manager, Martin, 50000.0]
[EL Config]: connection: 2019-11-06 14:08:53.78--ServerSession(1076835071)--Connection(1725017993)--Thread(Thread[main,5,main])--disconnect
[EL Info]: connection: 2019-11-06 14:08:53.781--ServerSession(1076835071)--Thread(Thread[main,5,main])--file:/C:/Users/miksten/eclipse-workspace2/UusiJPA/build/classes/_JPADemo logout successful
[EL Config]: connection: 2019-11-06 14:08:53.781--ServerSession(1076835071)--Connection(1177377518)--Thread(Thread[main,5,main])--disconnect

```

Tietojen hakeminen

{% code-tabs %}
{% code-tabs-item title="FindEmployee.java" %}
```java
import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.Persistence;

public class FindEmployee {
	public static void main(String[] args) {

		EntityManagerFactory emfactory = Persistence.createEntityManagerFactory("JPADemo");
		EntityManager entitymanager = emfactory.createEntityManager();
		Employee employee = entitymanager.find(Employee.class, 1301);

		System.out.println("employee ID = " + employee.getEid());
		System.out.println("employee NAME = " + employee.getEname());
		System.out.println("employee SALARY = " + employee.getSalary());
		System.out.println("employee DESIGNATION = " + employee.getDeg());
	}
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Koodin suoritus

```text
[EL Config]: metadata: 2019-11-06 14:10:04.28--ServerSession(1076835071)--Thread(Thread[main,5,main])--The access type for the persistent class [class Employee] is set to [PROPERTY].
[EL Config]: metadata: 2019-11-06 14:10:04.296--ServerSession(1076835071)--Thread(Thread[main,5,main])--The alias name for the entity class [class Employee] is being defaulted to: Employee.
[EL Config]: metadata: 2019-11-06 14:10:04.298--ServerSession(1076835071)--Thread(Thread[main,5,main])--The table name for entity [class Employee] is being defaulted to: EMPLOYEE.
[EL Config]: metadata: 2019-11-06 14:10:04.308--ServerSession(1076835071)--Thread(Thread[main,5,main])--The column name for element [getEid] is being defaulted to: EID.
[EL Config]: metadata: 2019-11-06 14:10:04.309--ServerSession(1076835071)--Thread(Thread[main,5,main])--The column name for element [getEname] is being defaulted to: ENAME.
[EL Config]: metadata: 2019-11-06 14:10:04.309--ServerSession(1076835071)--Thread(Thread[main,5,main])--The column name for element [getDeg] is being defaulted to: DEG.
[EL Config]: metadata: 2019-11-06 14:10:04.31--ServerSession(1076835071)--Thread(Thread[main,5,main])--The column name for element [getSalary] is being defaulted to: SALARY.
[EL Warning]: transaction: 2019-11-06 14:10:04.319--ServerSession(1076835071)--Thread(Thread[main,5,main])--PersistenceUnitInfo JPADemo has transactionType RESOURCE_LOCAL and therefore jtaDataSource will be ignored
[EL Info]: 2019-11-06 14:10:04.32--ServerSession(1076835071)--Thread(Thread[main,5,main])--EclipseLink, version: Eclipse Persistence Services - 2.5.2.v20140319-9ad6abd
[EL Fine]: connection: 2019-11-06 14:10:04.529--Thread(Thread[main,5,main])--Detected database platform: org.eclipse.persistence.platform.database.MySQLPlatform
[EL Config]: connection: 2019-11-06 14:10:04.538--ServerSession(1076835071)--Connection(1177377518)--Thread(Thread[main,5,main])--connecting(DatabaseLogin(
	platform=>MySQLPlatform
	user name=> "root"
	datasource URL=> "jdbc:mysql://localhost:8082/jpademo"
))
[EL Config]: connection: 2019-11-06 14:10:04.542--ServerSession(1076835071)--Connection(1725017993)--Thread(Thread[main,5,main])--Connected: jdbc:mysql://localhost:8082/jpademo
	User: root@
	Database: MySQL  Version: 5.5.5-10.3.16-MariaDB
	Driver: MySQL Connector Java  Version: mysql-connector-java-5.1.48 ( Revision: 29734982609c32d3ab7e5cac2e6acee69ff6b4aa )
[EL Info]: connection: 2019-11-06 14:10:04.567--ServerSession(1076835071)--Thread(Thread[main,5,main])--file:/C:/Users/miksten/eclipse-workspace2/UusiJPA/build/classes/_JPADemo login successful
[EL Fine]: sql: 2019-11-06 14:10:04.618--ServerSession(1076835071)--Connection(1725017993)--Thread(Thread[main,5,main])--SELECT EID, DEG, ENAME, SALARY FROM EMPLOYEE WHERE (EID = ?)
	bind => [1301]
employee ID = 1301
employee NAME = Martin
employee SALARY = 50000.0
employee DESIGNATION = Technical Manager

```

Päivittäminen

{% code-tabs %}
{% code-tabs-item title="UpdateEmployee.java" %}
```java
import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.Persistence;

public class UpdateEmployee {
	public static void main(String[] args) {
		EntityManagerFactory emfactory = Persistence.createEntityManagerFactory("JPADemo");

		EntityManager entitymanager = emfactory.createEntityManager();
		entitymanager.getTransaction().begin();
		Employee employee = entitymanager.find(Employee.class, 1301);

		// before update
		System.out.println(employee);
		employee.setSalary(46000);
		entitymanager.getTransaction().commit();

		// after update
		System.out.println(employee);
		entitymanager.close();
		emfactory.close();
	}
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Koodin suoritus

```text
[EL Config]: metadata: 2019-11-06 14:10:49.476--ServerSession(1076835071)--Thread(Thread[main,5,main])--The access type for the persistent class [class Employee] is set to [PROPERTY].
[EL Config]: metadata: 2019-11-06 14:10:49.493--ServerSession(1076835071)--Thread(Thread[main,5,main])--The alias name for the entity class [class Employee] is being defaulted to: Employee.
[EL Config]: metadata: 2019-11-06 14:10:49.495--ServerSession(1076835071)--Thread(Thread[main,5,main])--The table name for entity [class Employee] is being defaulted to: EMPLOYEE.
[EL Config]: metadata: 2019-11-06 14:10:49.505--ServerSession(1076835071)--Thread(Thread[main,5,main])--The column name for element [getEid] is being defaulted to: EID.
[EL Config]: metadata: 2019-11-06 14:10:49.506--ServerSession(1076835071)--Thread(Thread[main,5,main])--The column name for element [getEname] is being defaulted to: ENAME.
[EL Config]: metadata: 2019-11-06 14:10:49.506--ServerSession(1076835071)--Thread(Thread[main,5,main])--The column name for element [getDeg] is being defaulted to: DEG.
[EL Config]: metadata: 2019-11-06 14:10:49.507--ServerSession(1076835071)--Thread(Thread[main,5,main])--The column name for element [getSalary] is being defaulted to: SALARY.
[EL Warning]: transaction: 2019-11-06 14:10:49.516--ServerSession(1076835071)--Thread(Thread[main,5,main])--PersistenceUnitInfo JPADemo has transactionType RESOURCE_LOCAL and therefore jtaDataSource will be ignored
[EL Info]: 2019-11-06 14:10:49.517--ServerSession(1076835071)--Thread(Thread[main,5,main])--EclipseLink, version: Eclipse Persistence Services - 2.5.2.v20140319-9ad6abd
[EL Fine]: connection: 2019-11-06 14:10:49.724--Thread(Thread[main,5,main])--Detected database platform: org.eclipse.persistence.platform.database.MySQLPlatform
[EL Config]: connection: 2019-11-06 14:10:49.733--ServerSession(1076835071)--Connection(1177377518)--Thread(Thread[main,5,main])--connecting(DatabaseLogin(
	platform=>MySQLPlatform
	user name=> "root"
	datasource URL=> "jdbc:mysql://localhost:8082/jpademo"
))
[EL Config]: connection: 2019-11-06 14:10:49.737--ServerSession(1076835071)--Connection(1725017993)--Thread(Thread[main,5,main])--Connected: jdbc:mysql://localhost:8082/jpademo
	User: root@
	Database: MySQL  Version: 5.5.5-10.3.16-MariaDB
	Driver: MySQL Connector Java  Version: mysql-connector-java-5.1.48 ( Revision: 29734982609c32d3ab7e5cac2e6acee69ff6b4aa )
[EL Info]: connection: 2019-11-06 14:10:49.764--ServerSession(1076835071)--Thread(Thread[main,5,main])--file:/C:/Users/miksten/eclipse-workspace2/UusiJPA/build/classes/_JPADemo login successful
[EL Fine]: sql: 2019-11-06 14:10:49.816--ServerSession(1076835071)--Connection(1725017993)--Thread(Thread[main,5,main])--SELECT EID, DEG, ENAME, SALARY FROM EMPLOYEE WHERE (EID = ?)
	bind => [1301]
Employee [eid=1301, ename=Martin, salary=50000.0, deg=Technical Manager]
[EL Fine]: sql: 2019-11-06 14:10:49.823--ClientSession(1975546571)--Connection(1725017993)--Thread(Thread[main,5,main])--UPDATE EMPLOYEE SET SALARY = ? WHERE (EID = ?)
	bind => [46000.0, 1301]
Employee [eid=1301, ename=Martin, salary=46000.0, deg=Technical Manager]
[EL Config]: connection: 2019-11-06 14:10:49.832--ServerSession(1076835071)--Connection(1725017993)--Thread(Thread[main,5,main])--disconnect
[EL Info]: connection: 2019-11-06 14:10:49.832--ServerSession(1076835071)--Thread(Thread[main,5,main])--file:/C:/Users/miksten/eclipse-workspace2/UusiJPA/build/classes/_JPADemo logout successful
[EL Config]: connection: 2019-11-06 14:10:49.832--ServerSession(1076835071)--Connection(1177377518)--Thread(Thread[main,5,main])--disconnect

```

Tietojen poistaminen

{% code-tabs %}
{% code-tabs-item title="DeleteEmployee.java" %}
```java
import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.Persistence;

public class DeleteEmployee {
	public static void main(String[] args) {

		EntityManagerFactory emfactory = Persistence.createEntityManagerFactory("JPADemo");
		EntityManager entitymanager = emfactory.createEntityManager();
		entitymanager.getTransaction().begin();

		Employee employee = entitymanager.find(Employee.class, 1201);
		entitymanager.remove(employee);
		entitymanager.getTransaction().commit();
		entitymanager.close();
		emfactory.close();
	}
}


```
{% endcode-tabs-item %}
{% endcode-tabs %}

Koodin suoritus

```text
[EL Config]: metadata: 2019-11-06 14:11:19.464--ServerSession(1076835071)--Thread(Thread[main,5,main])--The access type for the persistent class [class Employee] is set to [PROPERTY].
[EL Config]: metadata: 2019-11-06 14:11:19.48--ServerSession(1076835071)--Thread(Thread[main,5,main])--The alias name for the entity class [class Employee] is being defaulted to: Employee.
[EL Config]: metadata: 2019-11-06 14:11:19.481--ServerSession(1076835071)--Thread(Thread[main,5,main])--The table name for entity [class Employee] is being defaulted to: EMPLOYEE.
[EL Config]: metadata: 2019-11-06 14:11:19.492--ServerSession(1076835071)--Thread(Thread[main,5,main])--The column name for element [getEid] is being defaulted to: EID.
[EL Config]: metadata: 2019-11-06 14:11:19.493--ServerSession(1076835071)--Thread(Thread[main,5,main])--The column name for element [getEname] is being defaulted to: ENAME.
[EL Config]: metadata: 2019-11-06 14:11:19.493--ServerSession(1076835071)--Thread(Thread[main,5,main])--The column name for element [getDeg] is being defaulted to: DEG.
[EL Config]: metadata: 2019-11-06 14:11:19.494--ServerSession(1076835071)--Thread(Thread[main,5,main])--The column name for element [getSalary] is being defaulted to: SALARY.
[EL Warning]: transaction: 2019-11-06 14:11:19.503--ServerSession(1076835071)--Thread(Thread[main,5,main])--PersistenceUnitInfo JPADemo has transactionType RESOURCE_LOCAL and therefore jtaDataSource will be ignored
[EL Info]: 2019-11-06 14:11:19.504--ServerSession(1076835071)--Thread(Thread[main,5,main])--EclipseLink, version: Eclipse Persistence Services - 2.5.2.v20140319-9ad6abd
[EL Fine]: connection: 2019-11-06 14:11:19.712--Thread(Thread[main,5,main])--Detected database platform: org.eclipse.persistence.platform.database.MySQLPlatform
[EL Config]: connection: 2019-11-06 14:11:19.722--ServerSession(1076835071)--Connection(1177377518)--Thread(Thread[main,5,main])--connecting(DatabaseLogin(
	platform=>MySQLPlatform
	user name=> "root"
	datasource URL=> "jdbc:mysql://localhost:8082/jpademo"
))
[EL Config]: connection: 2019-11-06 14:11:19.726--ServerSession(1076835071)--Connection(1725017993)--Thread(Thread[main,5,main])--Connected: jdbc:mysql://localhost:8082/jpademo
	User: root@
	Database: MySQL  Version: 5.5.5-10.3.16-MariaDB
	Driver: MySQL Connector Java  Version: mysql-connector-java-5.1.48 ( Revision: 29734982609c32d3ab7e5cac2e6acee69ff6b4aa )
[EL Info]: connection: 2019-11-06 14:11:19.753--ServerSession(1076835071)--Thread(Thread[main,5,main])--file:/C:/Users/miksten/eclipse-workspace2/UusiJPA/build/classes/_JPADemo login successful
[EL Fine]: sql: 2019-11-06 14:11:19.807--ServerSession(1076835071)--Connection(1725017993)--Thread(Thread[main,5,main])--SELECT EID, DEG, ENAME, SALARY FROM EMPLOYEE WHERE (EID = ?)
	bind => [1301]
[EL Fine]: sql: 2019-11-06 14:11:19.815--ClientSession(1975546571)--Connection(1725017993)--Thread(Thread[main,5,main])--DELETE FROM EMPLOYEE WHERE (EID = ?)
	bind => [1301]
[EL Config]: connection: 2019-11-06 14:11:19.823--ServerSession(1076835071)--Connection(1725017993)--Thread(Thread[main,5,main])--disconnect
[EL Info]: connection: 2019-11-06 14:11:19.823--ServerSession(1076835071)--Thread(Thread[main,5,main])--file:/C:/Users/miksten/eclipse-workspace2/UusiJPA/build/classes/_JPADemo logout successful
[EL Config]: connection: 2019-11-06 14:11:19.823--ServerSession(1076835071)--Connection(1177377518)--Thread(Thread[main,5,main])--disconnect

```

Funktioiden käyttö

{% code-tabs %}
{% code-tabs-item title="ScalarandAggregateFunctions.java" %}
```java
import java.util.List;
import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.Persistence;
import javax.persistence.Query;

public class  {
	public static void main(String[] args) {

		EntityManagerFactory emfactory = Persistence.createEntityManagerFactory("JPADemo");
		EntityManager entitymanager = emfactory.createEntityManager();

		// Scalar function
		// Query query = entitymanager.createQuery("Select UPPER(e.ename) from Employee e");
		// Query query = entitymanager.createQuery("Select e.ename from Employee e");
		Query query = entitymanager.createQuery("Select e.ename from Employee e where e.salary between 40000 and 60000");
		List<String> list = query.getResultList();

		for (String e : list) {
			System.out.println("Employee NAME : " + e);
		}

		// Aggregate function
		Query query1 = entitymanager.createQuery("Select MAX(e.salary) from Employee e");
		Double result = (Double) query1.getSingleResult();
		System.out.println("Max Employee Salary :" + result);
	}
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Suoritus

Huomaa, että edellinen esimerkki poistaa tietokannasta aiemmin lisätyn työntekijän. Jos haluat ao. tuloksen, tulee kantaan ensin lisätä työntekijä, jotta koodi löytää sieltä tietoja.

```text
[EL Config]: metadata: 2019-11-06 14:11:40.875--ServerSession(1076835071)--Thread(Thread[main,5,main])--The access type for the persistent class [class Employee] is set to [PROPERTY].
[EL Config]: metadata: 2019-11-06 14:11:40.892--ServerSession(1076835071)--Thread(Thread[main,5,main])--The alias name for the entity class [class Employee] is being defaulted to: Employee.
[EL Config]: metadata: 2019-11-06 14:11:40.894--ServerSession(1076835071)--Thread(Thread[main,5,main])--The table name for entity [class Employee] is being defaulted to: EMPLOYEE.
[EL Config]: metadata: 2019-11-06 14:11:40.905--ServerSession(1076835071)--Thread(Thread[main,5,main])--The column name for element [getEid] is being defaulted to: EID.
[EL Config]: metadata: 2019-11-06 14:11:40.906--ServerSession(1076835071)--Thread(Thread[main,5,main])--The column name for element [getEname] is being defaulted to: ENAME.
[EL Config]: metadata: 2019-11-06 14:11:40.906--ServerSession(1076835071)--Thread(Thread[main,5,main])--The column name for element [getDeg] is being defaulted to: DEG.
[EL Config]: metadata: 2019-11-06 14:11:40.906--ServerSession(1076835071)--Thread(Thread[main,5,main])--The column name for element [getSalary] is being defaulted to: SALARY.
[EL Warning]: transaction: 2019-11-06 14:11:40.916--ServerSession(1076835071)--Thread(Thread[main,5,main])--PersistenceUnitInfo JPADemo has transactionType RESOURCE_LOCAL and therefore jtaDataSource will be ignored
[EL Info]: 2019-11-06 14:11:40.917--ServerSession(1076835071)--Thread(Thread[main,5,main])--EclipseLink, version: Eclipse Persistence Services - 2.5.2.v20140319-9ad6abd
[EL Fine]: connection: 2019-11-06 14:11:41.123--Thread(Thread[main,5,main])--Detected database platform: org.eclipse.persistence.platform.database.MySQLPlatform
[EL Config]: connection: 2019-11-06 14:11:41.133--ServerSession(1076835071)--Connection(1177377518)--Thread(Thread[main,5,main])--connecting(DatabaseLogin(
	platform=>MySQLPlatform
	user name=> "root"
	datasource URL=> "jdbc:mysql://localhost:8082/jpademo"
))
[EL Config]: connection: 2019-11-06 14:11:41.137--ServerSession(1076835071)--Connection(1725017993)--Thread(Thread[main,5,main])--Connected: jdbc:mysql://localhost:8082/jpademo
	User: root@
	Database: MySQL  Version: 5.5.5-10.3.16-MariaDB
	Driver: MySQL Connector Java  Version: mysql-connector-java-5.1.48 ( Revision: 29734982609c32d3ab7e5cac2e6acee69ff6b4aa )
[EL Info]: connection: 2019-11-06 14:11:41.162--ServerSession(1076835071)--Thread(Thread[main,5,main])--file:/C:/Users/miksten/eclipse-workspace2/UusiJPA/build/classes/_JPADemo login successful
[EL Fine]: sql: 2019-11-06 14:11:41.33--ServerSession(1076835071)--Connection(1725017993)--Thread(Thread[main,5,main])--SELECT ENAME FROM EMPLOYEE WHERE (SALARY BETWEEN ? AND ?)
	bind => [40000.0, 60000.0]
Employee NAME : Martin
[EL Fine]: sql: 2019-11-06 14:11:41.335--ServerSession(1076835071)--Connection(1725017993)--Thread(Thread[main,5,main])--SELECT MAX(SALARY) FROM EMPLOYEE
Max Employee Salary :50000.0

```

