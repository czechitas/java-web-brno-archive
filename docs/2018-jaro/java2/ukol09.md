Úkol 09 - Repository nad JDBC
=============================

Ukolem je zprovoznit VideoBoss s pristupem do databaze.

Druhym ukolem je zalozit novou databazi
a predelat webovou aplikaci Daily Planet (evidence clanku) na tuto databazi.


## VideoBoss - prvni cast

V hodine jste jiz delaly:
1. Pristup do databaze 
z konzolove aplikace v Jave (viz `21-Simple_JDBC_Customers`).

2. Dale jste zprovoznili webovou aplikaci, ktera vyuzila jednou odladeny kod 
z konzolove aplikace (viz `31-Simple_JDBC_Web`).

3. Takhle spagetove by ale pristup k databazi nemel vypadat, takze jste mely za ukol 
vyuzit vzor Repository a separovat databazovy pristup
do `CustomerRepository` (viz `41-JDBC_Repository`).
Prozatim jen metody `findAll()` a pripadne `findOne()`.

Podle toho, jak jste byly rychle, jste nektere stihly bod 3, nektere jen bod 2.

Dalsim ukolem je do `CustomerRepository` dopsat zbyle metody `save()` a `delete()`
a zprovoznit cele stranky VideoBossu (viz `51-JDBC_VideoBoss`).
V lekci jsem vam neposkytl webove podklady,
jen jiz hotovy `51-JDBC_VideoBoss`, ze ktereho sly webove stranky "vykrast".
Kazdopadne v ramci tohoto domaciho ukolu dodavam tyto webove podklady rovnou,
dale dodavam implementaci metod `pridej(...)`, `updatuj(...)` a `delete(...)`, ktere muzete vyuzit
ve svoji implementaci `CustomerRepository`.


## Daily Planet - druha cast

Druha cast je **povinna** jen **pro pokrocile studentky**.

Adaptujte predchozi ukol 07 (Daily planet) take na databazovou repository.
Repository bude mit stejne hlavicky metod, jako v puvodni verzi (v ukolu 07).
Pro jejich implementaci cerpejte inspiraci z repository ve VideoBossu.

Pro Daily Planet si budete muset zalozit v MySQL serveru novou databazi.
A to jak lokalne (`localhost`), tak az aplikaci budete chtit nasadit na Tomcat.cloud,
tak stejnym zpusobem jeste jednou na `db.tomcat.cloud`. 

Pokud byste nemely hotovy ukol 07 a chtely se vrhnout rovnou na tento,
vyjdete z meho reseni ukolu 07 v odevzdavarne.



Detaily zadani
==============


## Webove stranky VideoBoss - prvni cast

Podklady webovych stranek VideoBoss si muzete stahnout zde:
[VideoBoss-podklady.zip](../../data/2018-jaro/java2/VideoBoss-podklady.zip)

### Repository pro VideoBoss

Repository by mela mit tyto metody:

~~~~
public class CustomerRepository {

    public List<Customer> findAll() {
    }

    public Customer findOne(Long id) {
    }

    public Customer save(Customer zakaznikKUlozeniNeboPridani) {
    }

    public void delete(Long id, int version) {
    }

}
~~~~


Zdrojovy text metod na pridavani, updatovani a mazani najdete nize.
Nemusite je umet stvorit samy, ale je nutne, abyste jim rozumely
a predevsim si vsimnete zabudovanych dotazu SQL. Dale si vsimnete to,
ze SQL je parametrizovano `?` a ze se vykonava pomoci
metody `jdbcTemplate.update(...)`
(`odesilacDotazu` je `jdbcTemplate`),
podobne jako nacitani dat z databaze se provadi metodou `jdbcTemplate.query(...)`. 

Poznamka: Metoda `clone(...)` okopiruje
objekt Customer do noveho objektu Customer. Je to nepovinna praktika,
kterou ja sam delam rad, protoze je program potom mene nachylny na vedlejsi efekty.
Povinne to ale rozhodne neni.
Zdrojovy text teto metody neprikladam.
Bud si ji muzete napsat samy nebo program modifikujte a nepouzivejte ji.
~~~~
    private Customer pridej(Customer zaznamKPridani) {
        Customer zakaznik = clone(zaznamKPridani);
        GeneratedKeyHolder drzakNaVygenerovanyKlic = new GeneratedKeyHolder();
        String sql = "INSERT INTO customers (Firstname, Lastname, Address, Version) " +
                "VALUES (?, ?, ?, ?)";
        odesilacDotazu.update((Connection con) -> {
                    PreparedStatement prikaz = con.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);
                    prikaz.setString(1, zakaznik.getFirstName());
                    prikaz.setString(2, zakaznik.getLastName());
                    prikaz.setString(3, zakaznik.getAddress());
                    prikaz.setInt(4, zakaznik.getVersion());
                    return prikaz;
                },
                drzakNaVygenerovanyKlic);
        zakaznik.setId(drzakNaVygenerovanyKlic.getKey().longValue());
        return zakaznik;
    }

    private Customer updatuj(Customer zaznamKUlozeni) {
        Customer zakaznik = clone(zaznamKUlozeni);
        odesilacDotazu.update(
                "UPDATE customers SET Firstname = ?, Lastname = ?, Address = ?, Version = Version + 1 WHERE id = ? AND Version = ?",
                zakaznik.getFirstName(),
                zakaznik.getLastName(),
                zakaznik.getAddress(),
                zakaznik.getId(),
                zakaznik.getVersion());
        zakaznik.setVersion(zakaznik.getVersion() + 1);
        return zakaznik;
    }

    public void delete(Long id, int version) {
        odesilacDotazu.update(
                "UPDATE customers SET Deleted = TRUE, Version = Version + 1 WHERE id = ? AND Version = ?",
                id,
                version);
    }
~~~~

Poznamka: Vsimnete si, ze pri mazani se zaznam ve skutecnosti nemaze,
jen se oznaci priznakem `deleted` a v pri zobrazovani vsech zakazniku
se proste preskakuje.

Aby se skutecne preskakoval, zkontrolujte, ze vase metoda `findAll()` pouziva 
SQL dotaz v tomto zneni (jde o klauzuli `WHERE Deleted = FALSE`):
~~~~
SELECT ID, Firstname, Lastname, Address, Deleted, Version FROM Customers WHERE Deleted = FALSE
~~~~


### Nasazeni na Tomcat.cloud

VideoBoss vystavte na vas Tomcat.cloud na https://sladkost.tomcat.cloud/ukol09/.
Pokud jste pokrocila studentka a *musite :-)* tudiz delat i druhou cast (Daily Planet),
pak VideoBoss nasadte jako `ukol09videoboss` a Daily Planet jako `ukol09`.

Na serveru Tomcat.cloud jiz MySQL server bezi a jiz v sobe ma databazi VideoBoss,
nemusite proto vasi lokalni databazi kopirovat na Tomcat.cloud.
V aplikaci nemusite nic menit, protoze pri instantciovani MariaDbDataSource
jste totiz uvedly cestu k databazi jako `localhost`
(presneji `jdbc:mysql://localhost:3306/VideoBoss`),
coz znamena, ze na Tomcat.cloudu
se proste pouzije databazovy server bezici lokalne na Tomcat.cloudu.

Poznamka: To take znamena, ze domaci ukoly vsech z vas budou 
v *produkcnim nasazeni* sdilet jednu databazi.
Chovejte se k ni, prosim, hezky a moc tam zaznamy nemazte :-)



## Daily Planet - druha cast

### Zalozeni nove databaze

Tak jako webovy server Tomcat hostuje jednu 
nebo vice webovych aplikaci (*"warka"*),
ktere se skladaji ze stranek HTML,
MySQL je databazovy server, coz znamena, ze hostuje jednu nebo vice databazi
(a databaze se skladaji z tabulek). Databazi si muzete predstavit jako 
excelovy soubor (ktery obsahuje sesity neboli tabulky).

Aktualne mate ve svem MySQL serveru na svem pocitaci pouze databazi `VideoBoss`.
Pro Daily Planet bude treba zalozit novou databazi.
To zaridite tak, ze se pripojite k MySQL serveru k existujici databazi (k `VideoBossu`)
a odeslete SQL prikazy na zalozeni nove databaze (nize).
Teprve pak se budete moct odpojit a pripojit k nove databazi.

Jak na to: V IntelliJ IDEA, v pod-okne Database mate nakonfigurovan
`VideoBoss Global DataSource`, ktery se umi pripojit do lokalniho MySQL serveru. 
Kliknete na nej pravym tlacitkem mysi a zvolte Open Console.
Ve stredove editorove oblasti se otevre SQL konzole,
ze ktere vymazte jakekoliv puvodni prikazy (pokud tam nejake byly).

Vlozte prikazy nize.

Poznamka: Jmeno databaze necht obsahuje jmeno vasi sladkosti.
Moje jmeno je `margot`.

Poznamka: Aby se provedly konkretni prikazy,
je vzdy nutne oznacit tyto prikazy
a stisknout zelenou sipku Execute nad konzoli.
~~~~
CREATE DATABASE DailyPlanet_margot
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_czech_ci;
  
USE DailyPlanet_margot;

CREATE TABLE Clanky (
  ID INT PRIMARY KEY NOT NULL AUTO_INCREMENT,
  Nazev VARCHAR(250) NOT NULL,
  Autor VARCHAR(250) NOT NULL,
  Datum DATE NOT NULL
);
~~~~

Pro archivacni ucely je dobre vykonany skript ulozit jako textovy soubor
do projektu, napr. `PROJEKT/zalozeni_db.sql`. Opravujici lektor/kouc 
si tak bude moct zalozit stejnou databazi, jako mate vy u sebe.

Az to budete mit, zalozte si novy data source v IntelliJ IDEA
prave pro databazi `DailyPlanet_sladkost`.
IntelliJ IDEA -> pod-okno Database -> Tlacitko + (pridat) -> 
DataSource -> MariaDB. Objevi se dialog. Vyplnte:
~~~~
Name:       DailyPlanet_sladkost Global DataSource
Host:       localhost
Port:       3306
Database:   DailyPlanet_sladkost
User:       student
Password:   password
~~~~

Stisknete Test Connection a overte, ze se do databaze jde pripojit.
Dale kliknete pravym tlacitkem mysi v seznamu Project Data Sources na
`DailyPlanet_sladkost Global DataSource` a zvolte Make global.
Data source se presune do skupiny Global Data Sources.
`DailyPlanet_sladkost Global DataSource` tim padem nebude svazany s timto ani jimym 
projektem, ale bude nakonfigurovan globalne v IntelliJ IDEA.

Pokud byste si chtely zalozit i jine tabulky nez jen `Clanek`,
jde na `DailyPlanet_sladkost Global DataSource`
kliknout pravym tlacitkem mysi v pod-okne Database
a zvolit New -> Table.
V dialogovem okne Create New Table si muzete naklikat pozadovanou
formu nove tabulky. V dolni polovine se zobrazuje SQL skript,
ktery se skutecne odesle do databaze a zalozi danou tabulku.
Tento skript si pred odeslanim uschovejte a pridejte ho k domacimu ukolu 
do `PROJEKT/zalozeni_db.sql`, aby si opravujici 
lektor/kouc mohl vytvorit stejnou databazi vcetne teto pridane tabulky.
  

### Uprava tridy Clanek  

Trida Clanek ma v sobe generovani primarniho klice (ID).
To je nesikovne, to by mela delat databaze (v prikaze `INSERT`).
 
Zmente tedy tridu Clanek tak, ze odeberete 
~~~~
private static AtomicLong idSequence = new AtomicLong(3000L);
~~~~ 
a zaroven ze vsech konstruktoru odeberete generovani ID.


### SQL prikazy pro praci s clanky

Abyste nemusely vymyslet SQL prikazy pro komunikaci s databazi, zde jsou:
~~~~
SELECT ID, Nazev, Autor, Datum FROM Clanky
SELECT ID, Nazev, Autor, Datum FROM Clanky WHERE ID=?
INSERT INTO Clanky (Nazev, Autor, Datum) VALUES (?, ?, ?)
UPDATE Clanky SET Nazev = ?, Autor = ?, Datum = ? WHERE ID = ?
DELETE FROM Clanky WHERE ID = ?
~~~~

  
### Nasazeni na Tomcat.cloud
  
Pro odevzdani ukolu jej bude nutne nasadit jeste na Tomcat.cloud.
Na Tomcat.cloudu take bezi MySQL server a pripojit se k nemu muzete
pres internet uplne stejne jako k vasemu lokalnimu MySQL,
jen pri zakladani data sourcu v IntelliJ IDEA nevyplnite 
`localhost`, ale `db.tomcat.cloud`.

Na MySQL serveru na Tomcat.cloudu je vytvorena pouze databaze `VideoBoss`,
ale zadne dalsi.

Zalozte si tedy nejprve novy data source v IntelliJ IDEA (v pod-okne Database),
ktery povede na tuto existujici databazi `VideoBoss` na `db.tomcat.cloud`.

Pripojte se k ni a pomoci SQL konzole v IntelliJ IDEA (Open Console)  
odeslete prikazy na zalozeni nove databaze `DailyPlanet-sladkost` (na `db.tomcat.cloud`).
  
Nakonec si zalozte jeste posledni novy data source v IntelliJ IDEA (v pod-okne Database),
ktery uz povede na vasi prave zalozenou databazi
`DailyPlanet-sladkost` na `db.tomcat.cloud` z minuleho kroku.

Web Daily Planet vystavte na vas Tomcat.cloud na [https://sladkost.tomcat.cloud/ukol09/](https://sladkost.tomcat.cloud/ukol09/).




## Tipy

- Ukázkový web VideoBoss můžete vidět na [https://margot.tomcat.cloud/ukol09videoboss/](https://margot.tomcat.cloud/ukol09videoboss/)

- Ukázkový web DailyPlanet můžete vidět na [https://margot.tomcat.cloud/ukol09/](https://margot.tomcat.cloud/ukol09/)

- Nastavte si v `PROJEKT/src/main/resources/application.properties` 
vlastnost `server.context-path = /ukol09`,
aby Spring Boot věděl, že chcete spouštět zabudovaný Tomcat tak,
že appka bude k dispozici
na `https://localhost:8080/ukol09`.

- Aby se vám při Maven Projects -> `package` vytvořil soubor .war se správným jménem,
  nastavte si ještě v `PROJEKT/pom.xml` /project/build/finalName na `ukol09`.
           
           
## Odevzdání domácího úkolu

Projekty nejprve zbavte přeložených artefaktů,
aby byly menší.
To zařídíte ručním smazáním složky PROJEKT/target
nebo pomocí v IntelliJ IDEA -> Maven Projects -> ukol -> Lifecycle -> `clean`.
Poté projekty zabalte pomocí 7-Zipu pod jménem
`Ukol09-Vase_Jmeno.7z`.
Lze použít i jen prostý zip (například na Macu).

Takto vytvořený archív nahrajte na Google Drive
do složky `Ukol09`.

Vytvořte archív .war v IntelliJ IDEA -> Maven Projects -> ukol -> Lifecycle -> `clean`
a následně IntelliJ IDEA -> Maven Projects -> ukol -> Lifecycle -> `package`.
Goal `package` vytvoří archív .war `PROJEKT/target/ukol09.war`.
Nasaďte jej do vašeho lokálního Tomcatu (`JAVATRAINING/Tomcat/webapps`)
a vyzkoušejte, že funguje (`https://localhost:8080/ukol09/`).
               
Po odladění nasaďte tento archív ještě přes FTP na server Tomcat.cloud
(`https://sladkost.tomcat.cloud/ukol09/`).

Vytvořte snímek obrazovky spuštěného programu
a pochlubte se s ním ve fotoalbu **Úkol09** na
[Facebooku](https://www.facebook.com/groups/2065013227074688/photos/?filter=albums).

Pokud byste chtěli odevzdat revizi úkolu (např. po opravě),
zabalte ji a nahrajte ji na stejný Google Drive znovu,
jen tentokrát se jménem `Ukol09-Vase_Jmeno-verze2.7z`

Termín odevzdání je do úterý 2. 5. 2018 23:59.
Pokud úkol nebo revizi odevzdáte později,
prosím pošlete svému opravujícímu kouči/lektorovi email nebo zprávu přes FB.
