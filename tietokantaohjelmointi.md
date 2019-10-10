---
description: Tässä osassa tutustutaan tietokannan käyttöön Java-sovelluksissa.
---

# Tietokantaohjelmointi

### Yleistä

Sovelluksissa muuttujiin tallennettu tieto säilyy ainoastaan ohjelman suorituksen ajan. Ohjelman päätyttyä myös muuttujat menetetään. Usein on kuitenkin tarpeen tallentaa sovelluksen tilatietoja ja/tai käyttäjän syöttämää dataa pitkäaikaisempaan säilöön; esim. tiedostoihin tai tietokantoihin. Tästä käytetään yleisesti nimitystä Object Persistence \(suom. olioiden pysyvyys/säilyvyys\).

### Javan tietokantarajapinta \(JDBC\)

Markkinoilla on saatavilla kymmenittäin erilaisia tietokantasovelluksia, osa ilmaisia ja osa maksullisia. Vaikka kyselykielenä relaatiotietokannoissa on useimmiten SQL, on niiden käytössä paljon tuotekohtaisia eroja. Näiden erojen "häivyttämiseksi" Javan kehittäjät ovat luoneet kieleen JDBC luokan. Se tarjoaa standardoidun tavan kommunikoida eri tietokantojen kanssa. Toisin sanoen sama Java-ohjelmakoodi toimii kaikkien eri tietokantaratkaisujen kanssa, jotka tarjoavat JDBC yhteensopivan ajurin tuotteeseensa. Ohjelmiston käyttäjät voivat näinollen myös vaihtaa sovelluksen käyttämää tietokantaa, ilman että tuote vaatisi muutoksia ohjelmistoon.

![JDBC rajapinta tarjoaa standardin tavan vuorovaikuttaa eri valmistajien tietokantatuotteiden kanssa.](.gitbook/assets/image%20%285%29.png)

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

		} catch (Exception e) {
			System.out.println("Virhe tietokannan käytössä!");
			System.out.println(e);
		} // catch
	} // main
} // class
```

Mikäli yhteys tietokantaan saadaan muodostettua, ohjelma tulostaa tästä tiedon. Virhetilanteessa näytetään käyttäjälle virheilmoitus.

### Tulosten käsittely

Täydennetään ohjelmaa kyselyn luomisellla ja tulosten käsittelyllä. Huomaa, että muuten koodi pysyy samana.

Tulokset palaututuvat ResultSet- tyyppisen rs-nimiseen muuttujaan, joka voidaan käydä taulukon tapaan silmukalla läpi. Tulosjoukosta rs poimitaan jokaista riviä kohden sarakkeiden 1,2,3 ja 4 arvo. Huomaa, että getString\(\) metodilla voidaan poimia mikä tahansa arvo String-tyyppisenä talteen. Jos tieto halutaan poimia numerona, niin voidaan käyttää getInt\(\) -metodia, kuten kolmannen sarakkeen kohdalla on tehty.

```java
while (rs.next()){
System.out.println(rs.getString(1) + "  " + rs.getString(2) + "  " + rs.getInt(3));
}
```

{% code-tabs %}
{% code-tabs-item title="MysqlCon.java" %}
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
{% endcode-tabs-item %}
{% endcode-tabs %}

## te

