# Tietojen vieminen Graafiseen käyttöliittymään

Tietokannasta haettu data on käytettävissä ohjelmassa samalla tapaa kuin muuttujien data yleensäkin. Jos tietojen halutaan esittää graafisessa Käyttöliittymässä niin siihen sopii kätevästi JTable-komponentti, jota on esitelty jo GUI-asioiden yhteydessä.

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
		ikkuna.getContentPane().add(table.getTableHeader(), BorderLayout.PAGE_START);
		 

		ikkuna.setSize(500, 200);
		ikkuna.setVisible(true);

	}

}


```
{% endcode-tabs-item %}
{% endcode-tabs %}

