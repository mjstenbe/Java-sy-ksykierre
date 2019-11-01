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

public class TaulukkoDemo {

	public static void main(String[] args) {

		String[] sarakkeet = { "Merkki", "Väri", "Rekisterinumero", "Vuosimalli" };
		String[][] rowData = { 
				{ "Toyota", "Punainen", "ABC-123", "1999" }, 
				{ "Ford", "Sininen", "BCD-456", "2018" },
				{ "Porsche", "Keltainen", "DEF-789", "1966" }
			};

		// Luodaan JTable
		//DefaultTableModel model = new DefaultTableModel();
		JTable table = new JTable(rowData, sarakkeet);
		
		// Luodaan Scrollpane taulukolle
		//JScrollPane scrollPane = new JScrollPane(table);
		table.setFillsViewportHeight(true);
		
		// Luodaan JFrame
		JFrame ikkuna = new JFrame();
		ikkuna.getContentPane().add(table);
		// Lisätään taulukon otsaketiedot ikkunan yläreunaan
		ikkuna.getContentPane().add(table.getTableHeader(), BorderLayout.PAGE_START);	 

		ikkuna.setTitle("Autorekisteri");
		ikkuna.setSize(500, 200);
		ikkuna.setVisible(true);
	}
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Tietokannan data JTable-komponenttiin

sdfsdf



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
			Connection con = DriverManager.getConnection("jdbc:mysql://sql7.freemysqlhosting.net:3306/sql7265083",
					"sql7265083", "UU8TSn6Z6G");

			// Luodaan Statement-olio, joka keskustelee tietokannan kanssa
			Statement stmt = con.createStatement();

			// Luodaan tulosjoukko, johon sijoitetaan kyselyn tulos
			ResultSet rs = stmt.executeQuery("SELECT * FROM kirja");

			// Tulosjoukko käydään silmukassa läpi

			while (rs.next()) {
				System.out.println(rs.getString(1) + "  " + rs.getString(2) + "  " + rs.getInt(3));

				data.add( new Object[] { rs.getString(1), rs.getString(2), rs.getString(3) } );
				
				// model.addRow(new Object[] { "1", "Column 2", "Column 3" });
			
			}
			con.close();

			// Varaudutaan virheisiin
		} catch (Exception e) {
			System.out.println(e);
		}

		JScrollPane scrollPane = new JScrollPane();

		// Lisätään sarakkeet kirjaa varten
		DefaultTableModel model = new DefaultTableModel();
		model.addColumn("Kirjan nimi");
		model.addColumn("Tekijä");
		model.addColumn("Julkaisuvuosi");

		JTable table = new JTable(model);

//		model.addRow(new Object[] { "1", "Column 2", "Column 3" });
//		model.addRow(new Object[] { "1", "Column 2", "Column 3" });
//		model.addRow(new Object[] { "1", "Column 2", "Column 3" });
		
		// Käydään silmukassa
		for (int i=0; i < data.size(); i++ ) {
			model.addRow(data.get(i));
		}

		JFrame ikkuna = new JFrame();
		ikkuna.setTitle("Taulukkodemo");

		ikkuna.add(scrollPane);
		scrollPane.setViewportView(table);

		ikkuna.pack();
		ikkuna.setVisible(true);

	}

}
```

