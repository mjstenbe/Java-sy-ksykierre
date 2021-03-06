---
description: Tässä osassa tutustutaan tietokannan käyttöön Java-sovelluksissa.
---

# Tietokantaohjelmointi

### Yleistä

Sovelluksissa muuttujiin tallennettu tieto säilyy ainoastaan ohjelman suorituksen ajan. Ohjelman päätyttyä myös muuttujat menetetään. Usein on kuitenkin tarpeen tallentaa sovelluksen tilatietoja ja/tai käyttäjän syöttämää dataa pitkäaikaisempaan säilöön; esim. tiedostoihin tai tietokantoihin. Tästä käytetään yleisesti nimitystä Object Persistence \(suom. olioiden pysyvyys/säilyvyys\).

### Javan tietokantarajapinta \(JDBC\)

Markkinoilla on saatavilla kymmenittäin erilaisia tietokantasovelluksia, osa ilmaisia ja osa maksullisia. Vaikka kyselykielenä relaatiotietokannoissa on useimmiten SQL, on niiden käytössä paljon tuotekohtaisia eroja. Näiden erojen "häivyttämiseksi" Javan kehittäjät ovat luoneet kieleen JDBC luokan. Se tarjoaa standardoidun tavan kommunikoida eri tietokantojen kanssa. Toisin sanoen sama Java-ohjelmakoodi toimii kaikkien eri tietokantaratkaisujen kanssa, jotka tarjoavat JDBC yhteensopivan ajurin tuotteeseensa. Ohjelmiston käyttäjät voivat näinollen myös vaihtaa sovelluksen käyttämää tietokantaa, ilman että tuote vaatisi muutoksia ohjelmistoon.

![JDBC rajapinta tarjoaa standardin tavan vuorovaikuttaa eri valmistajien tietokantatuotteiden kanssa.](../.gitbook/assets/image%20%285%29.png)

### Yhteyden luominen tietokantaan

Tietokannan käyttöön osana Java-sovellusta kuuluu seuraavat vaiheet:

1. Yhteyden luominen tietokantaan; joka voi sijaita joko paikallisella koneella, tai esim. jossain pilvessä
2. Kyselyn rakentaminen ja suorittaminen 
3. Tulosten vastaanotto ja käsittely

Allaolevassa ohjelmassa yritetään luoda yhteys tietokantaan. Kyselyä ei vielä luoda lainkaan. Koodi sijoitetaan try-catch -lohkon sisään, jotta mahdolliset virhetilanteet, kuten yhteysongelmat, voidaan käsitellä hallitusti. 

Huomaa myös import-lause ohjelman alussa, ohjelmaan tulee sisällyttää java.sql -pakkaus.

```java
import java.sql.*;

class MysqlCon {
	public static void main(String args[]) {
	
		// Varaudutaan poikkeuksiin; esim. jos tietokantaan ei saada yhteyttä
		try {
			// Määritellään tietokannan yhteysosoite sekä tietokannan nimi
			String URL = "jdbc:mysql://localhost:3306/kirjasto";
			// Määritellään käyttäjätiedot yhteyden luomista varten
			String USERID = "root";
			String PASSWORD = "";
			
			// Luodaan yhteys käyttäen edellänmainittuja tietoja
			Connection con = DriverManager.getConnection(URL, USERID, PASSWORD);
			
			System.out.println("Yhteys tietokantaan on luotu.");
			
			// Tässä kohtaa voitaisiin alkaa luomaan kyselyitä
		
			// Suljetaan yhteys
			con.close();

		} catch (SQLException e) {
			System.out.println("Virhe tietokannan käytössä!");
			System.out.println(e);
		} // catch
	} // main
} // class
```

Mikäli yhteys tietokantaan saadaan muodostettua, ohjelma tulostaa tästä allanäkyvän tiedon.

```text
Yhteys tietokantaan on luotu.
```

Jos tietokantaan yhdistämisessä tapahtuu virhe, näyttää ohjelman ajo seuraavalta. Tätä voit testata sammuttamalla tietokannan hetkeksi.

```text
Virhe tietokannan käytössä!
com.mysql.jdbc.exceptions.jdbc4.CommunicationsException: Communications link failure

The last packet sent successfully to the server was 0 milliseconds ago. The driver has not received any packets from the server.
```

Voit myös esim. muuttaa ohjelmakoodissa käyttäjätunnusta jota käytetään kirjautumiseen. Virheellinen kirjautuminen näyttää ohjelman suorituksessa tältä.

```text
Virhe tietokannan käytössä!
com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: Access denied for user ''@'localhost' to database 'kirjasto'
```

Valveutunut kehittäjä voi toki tehdä erityyppisille poikkeuksille omat catch-haaransa tai tutkia tietokannan aiheuttamaa virhetta tarkemmin ja tulostaa tilanteisiin sopivia  virheilmoituksia. Tämän voisi toteuttaa esim. seuraavasti.

Huomaa myös tietokannan antamien mahdollisten varoitusten tutkiminen getWarning\(\) -metodia käyttäen.

```java
// Varaudutaan erikseen tietokannan aiheuttamiin poikkeuksiin SQLException haaralla
// ja muihin poikkeuksiin yleisellä Exception-haaralla
		try {	
			// Yhteyden luominen ja kyselyt kuten yllä 

			// Tietokannan antamia varoituksia voidaan myös tutkia
			System.out.println("Tietokannan varoitukset: " con.getWarning());

		} catch (SQLException e) {
			// Kaivetaan tarkempi tieto virheen aiheuttajasta ja halutessa
			// tiettyihin tilanteisiin voidaan reagoida vielä esim. if-lauseilla
			System.out.println("Tietokantavirhe: " + e.getMessage());
			System.out.println("SQL state: "+ e.getSQLState());
			System.out.println("Valmistajan virhekoodi: "+ e.getErrorCode());
			
		} 
		catch (Exception e) {
			// Täällä varaudutaan muihin poikkeustilanteisiin
			System.out.println("Virhe ohjelman suorituksessa!");
			System.out.println(e);
		} // catch
```

### Kyselyn tekeminen ja tulosten käsittely

Täydennetään ohjelmaa kyselyn luomisellla ja tulosten käsittelyllä. Huomaa, että muuten koodi pysyy samana. Kyselyyn käytetään SQL:n SELECT lausetta, joka syötetään **executeQuery\(\)** -metodiin parametrina. Tämä suorittaa kyselyn tietokannassa.

Tulokset palaututuvat ResultSet- tyyppisen rs-nimiseen muuttujaan, joka voidaan käydä taulukon tapaan silmukalla läpi. Tulosjoukosta rs poimitaan jokaista riviä kohden sarakkeiden 1,2,3 ja 4 arvo. Huomaa, että getString\(\) metodilla voidaan poimia mikä tahansa arvo String-tyyppisenä talteen. Jos tieto halutaan poimia numerona, niin voidaan käyttää getInt\(\) -metodia, kuten kolmannen sarakkeen kohdalla on tehty.

```java
while (rs.next()){
System.out.println(rs.getString(1) + "  " + rs.getString(2) + "  " + rs.getInt(3));
}
```

{% code title="MysqlCon.java" %}
```java
import java.sql.*;


class MysqlCon {
	public static void main(String args[]) {
	
		// Varaudutaan poikkeuksiin; esim. jos tietokantaan ei saada yhteyttä
		try {
			// Määritellään tietokannan yhteysosoite sekä tietokannan nimi
			String URL = "jdbc:mysql://localhost:3306/kirjasto";
			// Määritellään käyttäjätiedot yhteyden luomista varten
			String USERID = "root";
			String PASSWORD = "";
			
			// Luodaan yhteys käyttäen edellänmainittuja tietoja
			Connection con = DriverManager.getConnection(URL, USERID, PASSWORD);
			// Luodaan uusi kysely
			Statement stmt = con.createStatement();
			
			System.out.println("Yhteys tietokantaan on luotu.");
			
			// Suoritetaan kysely ja otetaan tulokset talteen
			ResultSet rs = stmt.executeQuery("SELECT * FROM KIRJAT");
			
			// Tulosjoukko on taulukko-tyyppinen rakenne, joka
			// voidaan käydä läpi esim. while silmukalla
			
			while (rs.next()){
				System.out.println(rs.getString(1) + "  " + rs.getString(2) + "  " + rs.getInt(3));
				}
			// Tulostetaan myös palautuneiden rivien määrä (hieman hankalasti) 
			// hyppäämällä viimeiselle tulosriville ja tulostamalla sen indeksi
			rs.last();
			System.out.println("Tuloksia palautui: "+rs.getRow()+" riviä.");
			
			// Suljetaan yhteys
			con.close();

		} catch (Exception e) {
			System.out.println("Virhe tietokannan käytössä!");
			System.out.println(e);
		} // catch
	} // main
} // class
```
{% endcode %}

Ohjelman suoritus näyttää seuraavalta:

```text
Yhteys tietokantaan on luotu.

Suuri illusioni  Waltari, Mika  1928
Siddhartha  Hesse, Hermann  1922
Java-ohjelmointi  Vesterholm, Mika; Kyppö, Jorma  2018
Älä pakota minua ajattelemaan  Krug, Steve  2006

Tuloksia palautui: 4 riviä.
```

### Tulosten poimiminen muuttujiin

Edellä olevassa koodissa taulukon tiedot poimittiin tulosjoukosta ja tulostettiin sellaisenaan. Joskus on helpompi poimia ne talteen muuttujiin, joita sitten käytetään ohjelmassa edelleen. Tämä muuttaisi while-silmukkaa seuraavasti.

```java
 while (rs.next()) {
	String nimi = rs.getString(1);
	String tekija = rs.getString(2);
	int vuosiluku = rs.getInt(3);
				
	System.out.println("Kirjan nimi: " + nimi);
	System.out.println("Tekijän nimi: " + tekija);
	System.out.println("Vuosiluku nimi: " + vuosiluku);
		
	System.out.println();
}
```

### Tiedon syöttäminen tietokantaan

Tiedon syöttäminen tietokantaan tapahtuu SQL:n INSERT lauseiden avulla. Prosessi on pitkälti sama kuin edellä, ensin muodostetaan yhteys jonka jälkeen INSERT-lause viedään tietokantaan. Aiemmasta poiketen tietoa lisäävä operaatio tulee suorittaa **executeUpdate\(\) -metodin avulla**. Operaatio palauttaa tiedon muuttuneiden nimien määrästä, joka voidaan ottaa talteen. 

```java
// Päivitysoperaatio täytyy tehdä executeUpdate -metodilla. 
// Käytetään siisteyden vuoksi SQL lausetta joka on määritelty muuttujaan
			
String SQL_lause = "INSERT INTO KIRJAT VALUES ('Uusi Kirja','Uusi Tekijä','2018','123456789')";
int tuloksia = stmt.executeUpdate(SQL_lause);

// Tulostetaan muuttuneiden rivien määrä			
System.out.println("Päivitys vaikutti "+tuloksia+ " riviin.");
```

Mikäli tietokannan sisältöä halutaan tarkastella lisäyksen jälkeen, pitää perään tehdä toinen SELECT-kysely tietojen hakemiseksi. Alla koko prosessi ohjelmassa.

```java
import java.sql.*;

public class MysqlCon_update {

	public static void main(String args[]) {

		try {
			// Luodaan tietokantayhteys
			Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306/kirjasto", "root", "");

			// Luodaan Statement-olio, joka keskustelee tietokannan kanssa
			Statement stmt = con.createStatement();

			// Päivitysoperaatio täytyy tehdä executeUpdate -metodilla. 
			// Käytetään siisteyden vuoksi SQL lausetta joka on määritelty muuttujaan
			
			String SQL_lause = "INSERT INTO KIRJAT VALUES ('Uusi Kirja','Uusi Tekijä','2018','123456789')";
			int tuloksia = stmt.executeUpdate(SQL_lause);
			
			System.out.println("Päivitys vaikutti "+tuloksia+ " riviin.\n");
			
			// Luodaan tulosjoukko, johon sijoitetaan kyselyn tulos
			ResultSet rs = stmt.executeQuery("SELECT * FROM kirjat");

			// Tulosjoukko käydään silmukassa läpi
			while (rs.next())
				System.out.println(rs.getString(1) + "  " + rs.getString(2) + "  " + rs.getInt(3));

			con.close();

			// Varaudutaan virheisiin
		} catch (Exception e) {
			System.out.println(e);
		}

	}
}
```

Ohjelma tuottaa seuraavanlaisen tulostuksen. Huomaa viimeisellä rivillä lisätty kirja.

```text
Päivitys vaikutti 1 riviin.

Suuri illusioni  Waltari, Mika  1928
Siddhartha  Hesse, Hermann  1922
Java-ohjelmointi  Vesterholm, Mika; Kyppö, Jorma  2018
Älä pakota minua ajattelemaan  Krug, Steve  2006
Uusi Kirja  Uusi Tekijä  2018
```

### Tietojen muokkaaminen tietokannassa

Olemassaolevien rivien muokkaus tapahtuu käyttäen samaa **executeUpdate\(\) -metodia** kuin edellä. Ainoastaan SQL-lause täytyy muuttaa sopivaksi UPDATE-komennoksi. Huomaa, että ao. SQL-lause päivittää taulun kaikki rivit, jotka vastaavat WHERE-ehtoa, eli siinä on mielekästä käyttää mahdollisimman yksilöivää tietoa kuten ISBN.

```java
// Päivitysoperaatio täytyy tehdä executeUpdate -metodilla. 
// Käytetään siisteyden vuoksi SQL lausetta joka on määritelty muuttujaan

String SQL_lause = "UPDATE KIRJAT SET julkaisuvuosi='2019' WHERE isbn='123456789'";
int tuloksia = stmt.executeUpdate(SQL_lause);

// Tulostetaan muuttuneiden rivien määrä				
System.out.println("Päivitys vaikutti "+tuloksia+ " riviin.");
```

Mikäli tietokannan sisältöä halutaan tarkastella lisäyksen jälkeen, pitää perään tehdä toinen SELECT-kysely tietojen hakemiseksi. Alla koko prosessi ohjelmassa.

```java
package MySQL;
import java.sql.*;

public class MysqlCon_modify {

	public static void main(String args[]) {

		try {

			// Luodaan tietokantayhteys
			Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306/kirjasto", "root", "");

			// Luodaan Statement-olio, joka keskustelee tietokannan kanssa
			Statement stmt = con.createStatement();

			// Päivitysoperaatio täytyy tehdä executeUpdate -metodilla. 
			// Käytetään siisteyden vuoksi SQL lausetta joka on määritelty muuttujaan
			
			String SQL_lause = "UPDATE KIRJAT SET julkaisuvuosi='2019' WHERE isbn='123456789'";
			int tuloksia = stmt.executeUpdate(SQL_lause);
			
			System.out.println("Päivitys vaikutti "+tuloksia+ " riviin.\n");
			
			// Luodaan tulosjoukko, johon sijoitetaan kyselyn tulos
			ResultSet rs = stmt.executeQuery("SELECT * FROM kirjat");

			// Tulosjoukko käydään silmukassa läpi
			while (rs.next())
				System.out.println(rs.getString(1) + "  " + rs.getString(2) + "  " + rs.getInt(3));

			con.close();

			// Varaudutaan virheisiin
		} catch (Exception e) {
			System.out.println(e);
		}

	}
}
```

Ohjelma tuottaa seuraavanlaisen tulostuksen. Huomaa viimeisellä rivillä muuttunut julkaisuvuosi 2019.

```text
Päivitys vaikutti 1 riviin.

Suuri illusioni  Waltari, Mika  1928
Siddhartha  Hesse, Hermann  1922
Java-ohjelmointi  Vesterholm, Mika; Kyppö, Jorma  2018
Älä pakota minua ajattelemaan  Krug, Steve  2006
Uusi Kirja  Uusi Tekijä  2019
```

### Tietojen poistaminen tietokannasta

Rivien poistaminen tietokannasta tapahtuu samalla **executeUpdate\(\)-metodilla** kuin edellisetkin muokkaukset \(lisäys- ja päivitysoperaatiot\). SQL-lauseessa käytetään tällä kertaa DELETE-komentoa.

```java
// Päivitysoperaatio täytyy tehdä executeUpdate -metodilla. 
// Käytetään siisteyden vuoksi SQL lausetta joka on määritelty muuttujaan
			
String SQL_lause = "DELETE FROM KIRJAT WHERE isbn='123456789'";
int tuloksia = stmt.executeUpdate(SQL_lause);

// Tulostetaan muuttuneiden rivien määrä				
System.out.println("Päivitys vaikutti "+tuloksia+ " riviin.");
```

Ohjelma tuottaa seuraavanlaisen tulostuksen. Huomaa viimeiseltä riviltä poistettu kirja.

```text
Päivitys vaikutti 1 riviin.

Suuri illusioni  Waltari, Mika  1928
Siddhartha  Hesse, Hermann  1922
Java-ohjelmointi  Vesterholm, Mika; Kyppö, Jorma  2018
Älä pakota minua ajattelemaan  Krug, Steve  2006
```

### Kyselylauseiden optimointia: valmistellut lauseet \(Prepared Statements\)

Jos tietokantaan tehdään toistuvasti samantapaisia kyselyitä, joissa vain paramereinä käytettävät arvot muuttuvat, voidaan Javassa käyttää ns. valmisteltuja lauseita \(Prepared Statements\). Niiden idea on se, että lauseet esikäännetään \(precompile\) ja optimoidaan tietokannan toimesta valmiiksi. Kun hakuehdot myöhemmin liitetään kyselyyn on  lopullisen SQL-lauseen suorittaminen tehokkaampaa ja tulokset saadaan jatkokäyttöön nopeammin.

```java
package MySQL;

import java.sql.*;

public class MysqlCon_preparedstatements {

	public static void main(String args[]) {

		try {

			// Esitellään muuttujat jotka lisätään. Tavallisesti nämä saadaan ohjelmaan
			// esim. käyttäjän täyttämästä lomakkeesta tai metodikutsun parametreina

			String nimi = "Rautatie";
			String tekijä = "Aho, Juhani";
			int vuosi = 1920;
			String isbn = "123-456-789-0";

			// Luodaan tietokantayhteys
			Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306/kirjasto", "root", "");

			// SQL Lausekkeen esittely, käytetään ? muuttuvien tietojen kohdalla
			String sql = "INSERT INTO kirjat values (?,?,?, '123')";

			// Luodaan PreparedStatement-olio, joka keskustelee tietokannan kanssa
			// Statement stmt = con.createStatement();
			PreparedStatement preparedStmt = con.prepareStatement(sql);

			// Täydennetään SQL-lauseisiin puuttuvat muuttujat
			preparedStmt.setString(1, nimi);
			preparedStmt.setString(2, tekijä);
			preparedStmt.setInt(3, vuosi);

			// Suoritetaan SQL lause

			preparedStmt.execute();

			System.out.println("Tiedot Lisätty");

			// Luodaan Statement-olio, joka keskustelee tietokannan kanssa
			Statement stmt = con.createStatement();
			// Luodaan tulosjoukko, johon sijoitetaan kyselyn tulos
			ResultSet rs = stmt.executeQuery("SELECT * FROM kirjat");

			// Tulosjoukko käydään silmukassa läpi
			while (rs.next())
				System.out.println(rs.getString(1) + "  " + rs.getString(2) + "  " + rs.getInt(3));

			con.close();

			// Varaudutaan virheisiin
		} catch (Exception e) {
			System.out.println(e);
		}

	}
}
```

Ohjelma tuottaa seuraavanlaisen tulostuksen. Huomaa viimeisellä rivillä lisätty kirja.

```text
Tiedot Lisätty

Suuri illusioni  Waltari, Mika  1928
Siddhartha  Hesse, Hermann  1922
Java-ohjelmointi  Vesterholm, Mika; Kyppö, Jorma  2018
Älä pakota minua ajattelemaan  Krug, Steve  2006
Rautatie  Ahi, Juhani  1920
```

### Valmistellut lauseet ja parametrina välitetty data

Edellisessä esimerkissä PreparedStatement-olio sai kyselyyn liitettävät tiedot kovakoodatuista muuttujista. Todellisuudessa tietokantaoperaatio lienee mielekkäintä rakentaa metodikutsuun, joka saa parametrina kutsujaltaan tiedot, joita liitetään osaksi kyselyä.

Allaolevassa esimerkissä on identtinen koodi kuin edellä, mutta se on sijoitettu suoritaKysely\(\)-nimisen metodin sisään. 

```java
public static void suoritaKysely(String nimi, String tekijä, int vuosi, String isbn){
```

Kutsuvaihesssa metodille välitetään seuraava data:  

```java
suoritaKysely("Java-ohjelmointi", "Peltonen, Matti", 2010, "123-456-789-1");
```

Parametreihin tallennettu tieto puolestaan päätyy osaksi SQL-lausetta setString\(\) -metodien kautta:

```java
// SQL Lausekkeen esittely, käytetään ? muuttuvien tietojen kohdalla
String sql = "INSERT INTO kirjat values (?,?,?,?)";

// Luodaan PreparedStatement-olio, joka keskustelee tietokannan kanssa
PreparedStatement preparedStmt = con.prepareStatement(sql);

// Täydennetään SQL-lauseisiin puuttuvat muuttujat
preparedStmt.setString(1, nimi);
preparedStmt.setString(2, tekijä);
preparedStmt.setInt(3, vuosi);
preparedStmt.setString(4, isbn);

// Suoritetaan SQL lause
preparedStmt.execute();
```

Alla esimerkkikoodi kokonaisuudessaan.

```java
package MySQL;

import java.sql.*;

public class MysqlCon_preparedstatements_method {

	public static void main(String args[]) {

		suoritaKysely("Java-ohjelmointi", "Peltonen, Matti", 2010, "123-456-789-1");

	}
	// Syötettävä data saadaan kutsujalta parametreina

	public static void suoritaKysely(String nimi, String tekijä, int vuosi, String isbn) {

		try {

			// Luodaan tietokantayhteys
			Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306/kirjasto", "root", "");

			// SQL Lausekkeen esittely, käytetään ? muuttuvien tietojen kohdalla
			String sql = "INSERT INTO kirjat values (?,?,?,?)";

			// Luodaan PreparedStatement-olio, joka keskustelee tietokannan kanssa
			// Statement stmt = con.createStatement();
			PreparedStatement preparedStmt = con.prepareStatement(sql);

			// Täydennetään SQL-lauseisiin puuttuvat muuttujat
			preparedStmt.setString(1, nimi);
			preparedStmt.setString(2, tekijä);
			preparedStmt.setInt(3, vuosi);
			preparedStmt.setString(4, isbn);

			// Suoritetaan SQL lause

			preparedStmt.execute();

			System.out.println("Tiedot Lisätty");

			// Luodaan Statement-olio, joka keskustelee tietokannan kanssa
			Statement stmt = con.createStatement();
			// Luodaan tulosjoukko, johon sijoitetaan kyselyn tulos
			ResultSet rs = stmt.executeQuery("SELECT * FROM kirjat");

			// Tulosjoukko käydään silmukassa läpi
			while (rs.next())
				System.out.println(rs.getString(1) + "  " + rs.getString(2) + "  " + rs.getInt(3));

			con.close();

			// Varaudutaan virheisiin
		} catch (Exception e) {
			System.out.println(e);
		}

	}
}
```

Ohjelman suoritus tuottaa seuraavan tulosteen. Huomaa viimeisenä lisätty rivi.

```text
Tiedot Lisätty

Suuri illusioni  Waltari, Mika  1928
Siddhartha  Hesse, Hermann  1922
Java-ohjelmointi  Vesterholm, Mika; Kyppö, Jorma  2018
Älä pakota minua ajattelemaan  Krug, Steve  2006
Rautatie  Ahi, Juhani  1920
Java-ohjelmointi  Peltonen, Matti  2010
```

### Tietokantaoperaatioiden vieminen omaan luokkaansa

Ohjelmia kirjoittaessa koodari huomaa nopeasti että tietokannan käyttö tuottaa paljon toisteisia koorivejä. Käytännössä tietokantaoperaatioiden koodi kannattaakin eriyttää selkeästi omaan luokkaansa, jolloin sopivaa toiminnallisuutta voidaan kutsua yhdellä metodikutsulla kymmenien toisteisten rivien tapaan.

Luokkaa voisi käyttää esim. graafisen käyttöliittymän kuuntelijoista seuraavalla tavalla:

```java
JButton lataaNappi = new JButton("Tallenna kirjat");
tallennaNappi.addActionListener(new ActionListener() {
			public void actionPerformed(ActionEvent arg0) {
				// Kutsutaan tietokantaoperaatiota
				Tietokantaoperaatiot.tallennaKirja(uusiKirja);
			}
	});
```

Alla hahmotelma luokasta, johon erilaisia tietokanmetodeita voisi alkaa rakentamaan. 

{% code title="Tietokantaoperaatiot.java" %}
```java
public class Tietokantaoperaatiot{

	// JDBC driver name and database URL
	static final String DB_URL = "jdbc:mysql://localhost:3306/kirjat";

	// Database credentials
	static final String USER = "root";
	static final String PASS = "";

	// Metodit tietokannan käyttöä varten
	public static void tallennaKirja(Kirja opus){ ... }

	public static void lataaKirjat(){ ... }

	public static void poistaKirja(String kirjanNimi){ ... }
}
```
{% endcode %}



