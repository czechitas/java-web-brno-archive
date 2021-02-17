Úkol 11 - Pexeso s REST API
===========================

Ukolem je predelat pexeso tak, aby negenerovalo webove stranky z sablon ThymeLeafu,
ale aby vystavovalo REST API a to bylo konzumovano staticky web s JavaScriptem. 



## 1. Rozbehnuti a prozkoumani architektury

Nejprve prozkoumejte nasazenou aplikaci pexesa na
[https://margot.tomcat.cloud/ukol11/](https://margot.tomcat.cloud/ukol11/).

Obzlvast se podivejte, jak tam funguje JavaScript
a jake dela pozadavky pomoci `XMLHttpRequest`.
Zkuste si vyvolat ekvivalentni pozadavek samy.
Bud pomoci prohlizece nebo
pomoci online nastroje *hurl.it*.
 
 

## 2. Predelani komponenty HlavniController

Materialy pro pexeso najdete zde:
[Ukol11-pexeso-REST_API.zip](../../data/2018-jaro/java2/Ukol11-pexeso-REST_API.zip)

Srovnejte, jak se lisi projekty `10-Pexeso-ThymeLeaf` a `20-Pexeso-REST_API-zadani`.

Zamerme se na `HlavniController`. Jeho puvodni podoba z minuleho ukolu byla asi takovato:

~~~
@Controller
public class HlavniController {

    @RequestMapping(value = "/")
    public String zobrazIndex();

    @RequestMapping(value = "/stul.html", method = RequestMethod.GET)
    public ModelAndView zobrazStul(@RequestParam("id") Long id);

    @RequestMapping(value = "/stul.html", method = RequestMethod.POST)
    public String zpracujTah(@RequestParam("id") Long idHerniPlochy,
                             @RequestParam Map<String, String> allParams);

}
~~~


Nova podoba by mohla byt takovato:

~~~
@Controller
public class HlavniController {

    @RequestMapping(value = "/")
    public String zobrazIndex();

    @RequestMapping(value = "/api/stul", method = RequestMethod.GET)
    public @ResponseBody HerniPlocha zobrazStul(@RequestParam("id") Long id);

    @RequestMapping(value = "/api/stul", method = RequestMethod.POST)
    public @ResponseBody HerniPlocha zpracujTah(@RequestParam("id") Long idHerniPlochy,
                                                @RequestBody Tah vybranaKarta);
}
~~~

Poznamka: Moje metoda `zpracujTah(...)` prijima `Tah`,
coz je objekt nesouci pozici vybrane karty:
~~~
public class Tah {

    private int poziceKarty;

    public int getPoziceKarty() {
        return poziceKarty;
    }

    public void setPoziceKarty(int newValue) {
        poziceKarty = newValue;
    }
}
~~~
 
Naprogramujte tedy metody v `HlavniController` tak, aby JavaScript fungoval.

PexesoService ani zbytek aplikace se nemusi menit. Menime jen webovou vrstvu.


 
## 3. Provedeni stejne zmeny ve vasi verzi pexesa 

Vyjdete ze sveho reseni pexesa z ukolu 10
a zkuste i predelat webovou stranku na pouziti JavaScriptu/AJAXu.

Pokud byste nemely svoji verzi pexesa, ale stejne si chtely vyzkouset tento podukol,
muzete vyjit z `10-Pexeso-ThymeLeaf` a predelat si tento projekt.


Poznamka: V moji minule verzi pexesa byla zabudovana nesikovnost:
Na server se **neposilala pozice karty**, na kterou se kliknulo,
ale **cislo karty** (= dvojnasobek cisla obrazku karty).
Tim padem se hra dala dohrat snadno, kdyz se hrac podival do zdrojoveho textu HTML v prohlizeci,
ktera cisla karet jsou na webove strance kde umistena.
Tuto nesikovnost jsem opravil v puvodni verzi a v REST API je tato oprava uz zohlednena.  


          
           
## Odevzdání domácího úkolu

Svoje pexeso vystavte na Tomcat.cloud na https://sladkost.tomcat.cloud/ukol11/.

Ukázkové pexeso můžete vidět na [https://margot.tomcat.cloud/ukol11/](https://margot.tomcat.cloud/ukol11/).

Projekty nejprve zbavte přeložených artefaktů,
aby byly menší.
To zařídíte ručním smazáním složky PROJEKT/target
nebo pomocí v IntelliJ IDEA -> Maven Projects -> ukol -> Lifecycle -> `clean`.
Poté projekty zabalte pomocí 7-Zipu pod jménem
`Ukol11-Vase_Jmeno.7z`.
Lze použít i jen prostý zip (například na Macu).

Takto vytvořený archív nahrajte na Google Drive
do složky `Ukol11`.

Vytvořte archív .war v IntelliJ IDEA -> Maven Projects -> ukol -> Lifecycle -> `clean`
a následně IntelliJ IDEA -> Maven Projects -> ukol -> Lifecycle -> `package`.
Goal `package` vytvoří archív .war `PROJEKT/target/ukol11.war`.
Nasaďte jej do vašeho lokálního Tomcatu (`JAVATRAINING/Tomcat/webapps`)
a vyzkoušejte, že funguje (`https://localhost:8080/ukol11/`).
               
Po odladění nasaďte tento archív ještě přes FTP na server Tomcat.cloud
(`https://sladkost.tomcat.cloud/ukol11/`).

Vytvořte snímek obrazovky spuštěného programu
a pochlubte se s ním ve fotoalbu **Úkol11** na
[Facebooku](https://www.facebook.com/groups/2065013227074688/photos/?filter=albums).

Pokud byste chtěli odevzdat revizi úkolu (např. po opravě),
zabalte ji a nahrajte ji na stejný Google Drive znovu,
jen tentokrát se jménem `Ukol11-Vase_Jmeno-verze2.7z`

Termín odevzdání je do středy 16. 5. 2018 23:59.
Pokud úkol nebo revizi odevzdáte později,
prosím pošlete svému opravujícímu kouči/lektorovi email nebo zprávu přes FB.
