# Yksikkötestaus

### Yleistä

**Yksikkötestaaminen** \(engl. unit testing\) on tietokoneohjelman testaamisen ja laadunvarmistuksen menetelmä, jossa lähdekoodin osat testataan pienissä paloissa, esim. metodi kerrallaan. 

Tämä eroaa koko ohjelman testaamisesta yhtenä kokonaisuutena, josta yleensä puhutaan esim. _integraatiostestauksena_ \(järjestelmien osien yhteentoimivuuden testaus\), tai esim. _hyväksymistestauksena_ \(tilaaja hyväksyy toiminnallisuuden\). 

Yksikkötestaaminen suoritetaan tavallisesti automatisoidusti, mutta myös manuaalisesti suorittaminen on mahdollista. Testien kehittäminen voi vaatia paljon aikaa: jokaista Java-koodiriviä kohden tarvitaan keskimäärin 3–5 JUnit-koodiriviä riittävän kattavuuden saavuttamiseen. 

Tekniset standardit, kuten [IEC 61508](https://fi.wikipedia.org/w/index.php?title=IEC_61508&action=edit&redlink=1), voivat vaatia ohjelman yksikkötestaamista osana kehittämisprosessia.

### Testaaminen käytännössä

Erilaisia testialustoja on useita, mutta Javan yhteydessä [JUnit](https://junit.org/junit5/) on noussut lähes standardiksi. Sovelluskehittimissä onkin useimmiten valmiiksi ladattuna JUnit-kirjaston jokin versio.

Katsotaan alla esimerkki testien kirjoittamisesta yksinkertaisella esimerkillä. Luokkaan Matikkaa.java  on määritelty muutama yksinkertainen operaatio, jotka tarjoavat palveluita luokan ulkopuolelle staattisina metodeina. Main-metodissa on esitelty metodien toimintaa.

{% code-tabs %}
{% code-tabs-item title="Matikkaa.java" %}
```java
public class Matikkaa {

	public static int summa(int eka, int toka) {
		return eka + toka;
	}
	public static double potenssi(int eka, int toka) {
		return Math.pow(eka, toka);
	}
	
public static void main(String[] args) {

		System.out.println( Matikkaa.summa(2, 3) );
		System.out.println( Matikkaa.potenssi(4, 2) );

	}
}
```
{% endcode-tabs-item %}

{% code-tabs-item title=undefined %}
```

```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Testien luominen

Seuraavaksi luomme JUnit-testiluokan, johon määritellään testejä. Testit ovat itseasiassa metodeja, jotka kutsuvat luokan toimintoja \(eli metodeja\) halutuilla arvoilla. Tämän jälkeen ne vastaanottavat metodien palauttamat arvot ja tulkitsevat niitä testin laatijan ohjeiden mukaan; testi hyväksytään tai hylätään palautuneista arvoista riippuen. 

