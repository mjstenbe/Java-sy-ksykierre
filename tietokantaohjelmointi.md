---
description: Tässä osassa tutustutaan tietokannan käyttöön Java-sovelluksissa.
---

# Tietokantaohjelmointi

### Yleistä

Sovelluksissa muuttujiin tallennettu tieto säilyy ainoastaan ohjelman suorituksen ajan. Ohjelman päätyttyä myös muuttujat menetetään. Usein on kuitenkin tarpeen tallentaa sovelluksen tilatietoja ja/tai käyttäjän syöttämää dataa pitkäaikaisempaan säilöön; esim. tiedostoihin tai tietokantoihin. Tästä käytetään yleisesti nimitystä Object Persistence \(suom. olioiden pysyvyys/säilyvyys\).

### Javan tietokantarajapinta \(JDBC\)

Markkinoilla on saatavilla kymmenittäin erilaisia tietokantasovelluksia, osa ilmaisia ja osa maksullisia. Vaikka kyselykielenä relaatiotietokannoissa on useimmiten SQL, on niiden käytössä paljon tuotekohtaisia eroja. Näiden erojen "häivyttämiseksi" Javan kehittäjät ovat luoneet kieleen JDBC luokan. Se tarjoaa standardoidun tavan kommunikoida eri tietokantojen kanssa. Toisin sanoen sama Java-ohjelmakoodi toimii kaikkien eri tietokantaratkaisujen kanssa, jotka tarjoavat JDBC yhteensopivan ajurin tuotteeseensa. Ohjelmiston käyttäjät voivat näinollen myös vaihtaa sovelluksen käyttämää tietokantaa, ilman että tuote vaatisi muutoksia ohjelmistoon.

![JDBC rajapinta tarjoaa standardin tavan vuorovaikuttaa eri valmistajien tietokantatuotteiden kanssa.](.gitbook/assets/image%20%285%29.png)





