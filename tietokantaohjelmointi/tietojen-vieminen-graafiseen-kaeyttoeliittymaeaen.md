# Tietojen vieminen Graafiseen käyttöliittymään

## JTable-komponentti

Tietokannasta haettu data on käytettävissä ohjelmassa samalla tapaa kuin muuttujien data yleensäkin. Jos tietojen halutaan esittää graafisessa Käyttöliittymässä niin siihen sopii kätevästi JTable-komponentti, jota on esitelty jo GUI-asioiden yhteydessä. 

![JTable-komponenttiin ladatut autotiedot.](../.gitbook/assets/image%20%2812%29.png)

Allaoleva koodi luo edellä näkyvän kuvan JFrame-ikkunan sisälle ensin JTable-komponentin ja esittää siinä rowData-muuttujan sisällön.

{% code-tabs %}
{% code-tabs-item title="TaulukkoDemo.java" %}
```java
import java.awt.BorderLayout;

import javax.swing.JFrame;
import javax.swing.JTable;
import javax.swing.table.DefaultTableModel;

public class TaulukkoDemo {

	public static void main(String[] args) {

		String[] sarakkeet = { "Merkki", "Väri", "Rekisterinumero", "Vuosimalli" };
		String[][] rowData = { 
				{ "Toyota", "Punainen", "ABC-123", "1999" }, 
				{ "Ford", "Sininen", "BCD-456", "2018" },
				{ "Porsche", "Keltainen", "DEF-789", "1966" }

		};

		// Luodaan Taulukon data ensin
		DefaultTableModel model = new DefaultTableModel(rowData, sarakkeet);
		// Lisätään se JTable-komponenttiin
		JTable table = new JTable(model);
		
		// Taulukkoon voidaan lisätä / poistaa rivejä addRow ja deleteRow -metodeilla
		model.addRow(new Object[]{"Column 1", "Column 2", "Column 3"});
		// Postetaan viimeinen rivi
		model.removeRow(  model.getRowCount()-1 );
		
		
		// Luodaan Scrollpane taulukolle
		//JScrollPane scrollPane = new JScrollPane(table);
		table.setFillsViewportHeight(true);
		 
		// Luodaan JFrame
		JFrame ikkuna = new JFrame();
		ikkuna.getContentPane().add(table);
		
		// Lisätään taulukon sarakkeiden otsakkeet ikkunaan
		ikkuna.getContentPane().add(table.getTableHeader(), BorderLayout.PAGE_START);
		 // Ikkunan otsikko, koko ja esillepano
		ikkuna.setTitle("Autorekisteri");
		ikkuna.setSize(500, 200);
		ikkuna.setVisible(true);

	}

}


```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Tietokannan data JTable-komponenttiin

Edellisessä koodissa taulukon tiedot oli kovakoodattu muuttujaan rowData. Seuraavassa esimerkissä data haetaan tietokannasta. Muuten ohjelman toimintalogiikka on samankaltainen. Tulokset käydään silmukassa läpi ja joka kierroksella data lisätään addRow\(\) -metodilla taulukkoon.

```java
// Luodaan tulosjoukko, johon sijoitetaan kyselyn tulos
ResultSet rs = stmt.executeQuery("SELECT * FROM kirja");

// Tulosjoukko käydään silmukassa läpi, joka kierroksella taulukkoon lisätään dataa
while (rs.next()) {
    model.addRow(new Object[] { rs.getString(1), rs.getString(2), rs.getString(3) });
}
```

Alla vielä toimiva esimerkkiohjelma kokonaisuudessaan.

```java
import javax.swing.JFrame;
import javax.swing.JScrollPane;
import javax.swing.JTable;
import javax.swing.table.DefaultTableModel;
import java.sql.*;

public class JTableMysql {

	public static void main(String[] args) {

		// Lisätään sarakkeet kirjaa varten, tällä käyttäen addColumn-metodia
		DefaultTableModel model = new DefaultTableModel();
		model.addColumn("Kirjan nimi");
		model.addColumn("Tekijä");
		model.addColumn("Julkaisuvuosi");

		JTable table = new JTable(model);
  	    JScrollPane scrollPane = new JScrollPane();

		JFrame ikkuna = new JFrame();
		ikkuna.setTitle("Taulukkodemo");

		ikkuna.add(scrollPane);
		scrollPane.setViewportView(table);

		ikkuna.pack();
		ikkuna.setVisible(true);

		// Tietokantarutiinit

		try {

			// Luodaan tietokantayhteys
			Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306/kirjasto", "root", "");

			// Luodaan Statement-olio, joka keskustelee tietokannan kanssa
			Statement stmt = con.createStatement();

			// Luodaan tulosjoukko, johon sijoitetaan kyselyn tulos
			ResultSet rs = stmt.executeQuery("SELECT * FROM kirja");

			// Tulosjoukko käydään silmukassa läpi, joka kierroksella taulukkoon lisätään dataa

			while (rs.next()) {
				System.out.println(rs.getString(1) + "  " + rs.getString(2) + "  " + rs.getInt(3));
						model.addRow(new Object[] { rs.getString(1), rs.getString(2), rs.getString(3) });
				}
				
		// Suljetaan tietokanyhteys
		con.close();

		// Varaudutaan virheisiin
		} catch (Exception e) {
			System.out.println(e);
		}
 
	}
}
```

## ArrayListin hyödyntäminen datan siirtämisessä

Joskus voi olla mielekästä siirtää tietokannan data ensin johonkin Javan omaan muuttujarakenteeseen kokonaisuudessaan. Tämä mahdollistaa esim. tietojen tutkimisen tai muokkauksen Javan omien metodien avulla yms. Suurten datamäärien kanssa tällainen ratkaisu saattaa olla suorituskykyisempi, kuin taulukon rivien päivittäminen reaaliaikaisesti suoraan käyttöliittymään.

Ao. esimerkissä luodaan ArrayList-tyyppinen muuttuja, johon tietokannan data luetaan ensin talteen. Vasta kun tiedot on kaikki luettu, siirretään ne kootusti JTable-komponenttiin. 

Ohjelman toiminta ei muutu merkittävästi. 

```java
// Luodaan tietorakenne, jonne tietokannasta haetut rivit voidaan tallentaa
ArrayList<Object[]> data = new ArrayList<Object[]>();

...

// Tulosjoukko käydään silmukassa läpi. Jokainen rivi lisätään ArrayListiin

while (rs.next()) {
  data.add(new Object[] { rs.getString(1), rs.getString(2), rs.getString(3) });
}

...

// Käydään silmukassa ArrayList läpi ja lisätään rivit addRow()-metodilla taulukkoon
		for (int i = 0; i < data.size(); i++) {
			model.addRow(data.get(i));
		}

```

Alla vielä toimiva esimerkkiohjelma kokonaisuudessaan.

```java
import javax.swing.JFrame;
import javax.swing.JScrollPane;
import javax.swing.JTable;
import javax.swing.table.DefaultTableModel;
import java.sql.*;
import java.util.ArrayList;

public class JTableMysql {

	public static void main(String[] args) {

		// Luodaan tietorakenne, jonne tietokannasta haetut rivit voidaan tallentaa
		ArrayList<Object[]> data = new ArrayList<Object[]>();

		try {
			// Luodaan tietokantayhteys
			Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306/kirjasto", "root", "");

			// Luodaan Statement-olio, joka keskustelee tietokannan kanssa
			Statement stmt = con.createStatement();

			// Luodaan tulosjoukko, johon sijoitetaan kyselyn tulos
			ResultSet rs = stmt.executeQuery("SELECT * FROM kirja");

			// Tulosjoukko käydään silmukassa läpi

			while (rs.next()) {
				data.add(new Object[] { rs.getString(1), rs.getString(2), rs.getString(3) });
			}
			con.close();

			// Varaudutaan virheisiin
		} catch (Exception e) {
			System.out.println(e);
		}

		// GUIn rakentaminen
		JFrame ikkuna = new JFrame();
		JScrollPane scrollPane = new JScrollPane();

		ikkuna.setTitle("Taulukkodemo");
		ikkuna.add(scrollPane);

		// Lisätään sarakkeet kirjaa varten
		DefaultTableModel model = new DefaultTableModel();
		model.addColumn("Kirjan nimi");
		model.addColumn("Tekijä");
		model.addColumn("Julkaisuvuosi");

		JTable table = new JTable(model);
		scrollPane.setViewportView(table);

		// Käydään silmukassa
		for (int i = 0; i < data.size(); i++) {
			model.addRow(data.get(i));
		}

		ikkuna.pack();
		ikkuna.setVisible(true);

	}
}

```

## Tietokantahakujen integrointi käyttöliittymään

Tietokantahaut sekä tiedon lisääminen on helppo integroida graafisen käyttöliittymän toimintoihin, kuten nappeihin. Tämä tapahtuu samalla tapaa kuin minkä tahansa muun toiminnon lisääminen erilaisiin tapahtumiin. Halutulle komponentille lisätään kuuntelija ja kuuntelijan havaitessa tapahtuman, suoritetaan toiminto.

Esimerkiksi tietokantahaun lisääminen nappiin tapahtuisi luontevasti kutsumalla kuuntelijassa metodia, joka suorittaa tietokantahakuun soveltuvan koodin. Tätä varten tietokantaoperaatioiden koodi kannattaakin eriyttää selkeästi omaan luokkaansa, jolloin sopivaa toiminnallisuutta voidaan kutsua yhdellä metodikutsulla kymmenien toisteisten rivien tapaan.

