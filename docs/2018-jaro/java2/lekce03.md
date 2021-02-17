Prohlizec pri odesilani pozadavku rozpoznava tyto casti adresy:
--------------------------------------------------------------
Priklady:
1.  ~~~~
    http://www.pokus.com/vtipy/zvireci/index.html
    ^^^^                                                Protokol (http)
           ^^^^^^^^^^^^^                                Domena, internetova adresa serveru (www.pokus.com)
                        ^^^^^^^^^^^^^^^^^^^^^^^^^       Adresa stranky (/vtipy/zvireci/index.html)
    ~~~~

2.  ~~~~
    https://pokus.com:8080/
    ^^^^^                                               Protokol (https)
            ^^^^^^^^^                                   Domena, internetova adresa serveru (pokus.com)
                      ^^^^                              Cislo sitoveho portu na serveru (8080)
                          ^                             Adresa stranky (/)
    ~~~~
    
3.  ~~~~
    https://pokus.com/vtipy/index.html?jazyk=cs
    ^^^^^                                               Protokol (https)
            ^^^^^^^^^                                   Domena, internetova adresa serveru (pokus.com)
                     ^^^^^^^^^^^^^^^^^                  Adresa stranky (/vtipy/index.html)
                                       ^^^^^^^^         1 parametr (jazyk=cs)
    ~~~~

4.  ~~~~
    https://pokus.com/index.html?jazyk=cs&barvy=tmave
    ^^^^^                                               Protokol (https)
            ^^^^^^^^^                                   Domena, internetova adresa serveru (pokus.com)
                     ^^^^^^^^^^^                        Adresa stranky (/vtipy/index.html)
                                 ^^^^^^^^^^^^^^^^^^^^   2 parametry (jazyk=cs, barvy=tmave)
    ~~~~

5.  ~~~~
    http://localhost:8000/index.html
    ^^^^                                                Protokol (http)
           ^^^^^^^^^                                    Domena, internetova adresa serveru
                     ^^^^                               Cislo sitoveho portu na serveru (8000)
                         ^^^^^^^^^^^                    Adresa stranky
    ~~~~


Poznamky:
*   http je klasicky protokol. https je to stejne, jen sifrovane (proti odposlechu).
*   Pokud neni uvedeno cislo sitoveho portu, pak se predpoklada 80 pro http a 443 pro https.
*   `pokus.com` a `www.pokus.com` jsou zcela jine internetove adresy serveru.
    Obvykle jsou nasmerovany na stejny server, ale vubec tomu tak byt nemusi.
*   localhost znamena vzdycky tento pocitac (z hlediska pocitace tedy pozadavek sam na sebe).


Webovy server pri obdrzeni pozadavku rozpoznava tyto casti adresy:
-----------------------------------------------------------------
Je dulezite si uvedomit, ze webovy server vi, jake ma nasazene webove aplikace.
Z toho plyne, ze rozpoznava stejne casti adresy jako webovy prohlizec, jen navic jmeno webove aplikace.
Definujme, ze ma nasazene:
*   ROOT.war            ... tedy aplikace nasazena na (prazdno)
*   vtipy.war           ... tedy aplikace nasazena na /vtipy
*   VideoBoss.war       ... tedy aplikace nasazena na /VideoBoss (pozor, rozlisuji se velka a mala pismena)

1.  ~~~~
    https://pokus.com/index.html?jazyk=cs&barvy=tmave
    ^^^^^                                               Protokol (https)
            ^^^^^^^^^                                   Domena, internetova adresa serveru (pokus.com)
                                                        Webova aplikace (prazdno, tedy ROOT.war)
                     ^^^^^^^^^^^                        Adresa stranky (/vtipy/index.html)
                                 ^^^^^^^^^^^^^^^^^^^^   2 parametry (jazyk=cs, barvy=tmave)
    ~~~~

2.  ~~~~
    http://www.pokus.com/vtipy/zvireci/index.html
    ^^^^                                                Protokol (http)
           ^^^^^^^^^^^^^                                Domena, internetova adresa serveru (www.pokus.com)
                        ^^^^^^                          Webova aplikace (/vtipy, tedy vtipy.war)
                              ^^^^^^^^^^^^^^^^^^^       Adresa stranky (/zvireci/index.html)
    ~~~~

3.  ~~~~
    http://tomcat.cloud/VideoBoss/customers/all.html
    ^^^^                                                Protokol (http)
           ^^^^^^^^^^^^                                 Domena, internetova adresa serveru (www.pokus.com)
                       ^^^^^^^^^^                       Webova aplikace (/VideoBoss, tedy VideoBoss.war)
                                 ^^^^^^^^^^^^^^^^^^^    Adresa stranky (/customers/all.html)
    ~~~~

4.  ~~~~
    https://tomcat.cloud/Pexeso/vecernicek/index.html
    ^^^^^                                               Protokol (https)
            ^^^^^^^^^^^^                                Domena, internetova adresa serveru (www.pokus.com)
                                                        Webova aplikace (prazdno, tedy ROOT.war).
                                                            Ackoliv to vypada, ze by na serveru
                                                            mohla byt webova aplikace Pexeso,
                                                            vyse jsme definovali, ze webovy server
                                                            ma nasazena jen 3 warka,
                                                            takze nutne pujde do ROOT.war
                                                            a bude tam hledat slozku Pexeso.
                        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^   Adresa stranky (/Pexeso/customers/all.html)
    ~~~~


Cesty z pohledu Spring Bootu
----------------------------
Pro Spring Boot je dulezity @RequestMapping uvnitr Java tridy oznacene @Controller.
Dejme tomu, ze webova aplikace je nasazena na /ukol02,
tedy ze je distribuovana jako ukol02.war
a webovy server bezi na internetove adrese `pokus.com`.

1.  ~~~~
    Pozadavek na adresu:
    http://pokus.com/ukol02/index.html
                           ^^^^^^^^^^^              Adresa stranky

    Spring Boot vyvola metodu oznacenou @RequestMapping("/index.html")
    ~~~~

2.  ~~~~
    Pozadavek na adresu:
    http://pokus.com/ukol02/vyroky/index.html
                           ^^^^^^^^^^^^^^^^^^       Adresa stranky

    Spring Boot vyvola metodu oznacenou @RequestMapping("/vyroky/index.html")
    ~~~~

3.  ~~~~
    Pozadavek na adresu:
    http://pokus.com/ukol02/vyroky/
                           ^^^^^^^^                 Adresa stranky

    Spring Boot vyvola metodu oznacenou @RequestMapping("/vyroky") nebo @RequestMapping("/vyroky/")
    ~~~~

4.  ~~~~
    Pozadavek na adresu:
    http://pokus.com/ukol02/vyroky/?id=100
                           ^^^^^^^^                 Adresa stranky
                                    ^^^^^^          Parametr

    Spring Boot vyvola metodu oznacenou @RequestMapping("/vyroky") nebo @RequestMapping("/vyroky/")
    ~~~~





Ktere soubory bude Spring Boot odesilat
---------------------------------------
Dejme tomu, ze webova aplikace je nasazena na /ukol02,
tedy ze je distribuovana jako ukol02.war a webovy server bezi na internetove adrese pokus.com.
Mejme ve webove aplikaci tyto soubory:
~~~~
PROJEKT
    src
        main
            resources
                static
                    index.html
                    css
                        styly.css
                    kontakt
                        o-nas.html
                    test
                        testik.html
                templates
                    meme-template.html
                    kontakt
                        napiste-nam-template.html
                    vsichni-uzivatele
                        login-template.html
                    admin
                        pokus.html
~~~~

Dale mejme ve webove aplikaci javovou tridu oznacenou @Controller, ktera ma nasledujici @RequestMapping:
~~~~
@RequestMapping("/meme.html")
metoda vraci ModelAndView("meme-template")

@RequestMapping("/test/testik.html")
metoda vraci ModelAndView("meme-template")

@RequestMapping("/kontakt/napiste-nam.html")
metoda vraci ModelAndView("kontakt/napiste-nam-template")

@RequestMapping("/admin/prihlaseni.html")
metoda vraci ModelAndView("vsichni-uzivatele/login-template")

@RequestMapping("/admin/pokus.html")
metoda vraci ModelAndView()         // Bez udani jmena sablony!
~~~~


1.  Prohlizec posle pozadavek na:<br/>
    http://pokus.com/ukol02/index.html

    Spring Boot vrati staticky soubor:<br/>
    PROJEKT/src/main/resources/static/index.html

2.  Prohlizec posle pozadavek na:<br/>
    http://pokus.com/ukol02/css/styly.css

    Spring Boot vrati staticky soubor:<br/>
    PROJEKT/src/main/resources/static/css/styly.css

3.  Prohlizec posle pozadavek na:<br/>
    http://pokus.com/ukol02/kontakt/o-nas.html

    Spring Boot vrati staticky soubor:<br/>
    PROJEKT/src/main/resources/static/kontakt/o-nas.html

4.  Prohlizec posle pozadavek na:<br/>
    http://pokus.com/ukol02/meme.html

    Existuje @RequestMapping("/meme.html"),<br/>
    metoda vraci ModelAndView("meme-template")

    Spring Boot provede sablonu:<br/>
    PROJEKT/src/main/resources/templates/meme-template.html

5.  Prohlizec posle pozadavek na:<br/>
    http://pokus.com/ukol02/test/testik.html

    Existuje @RequestMapping("/test/testik.html"),<br/>
    metoda vraci ModelAndView("meme-template")

    Spring Boot provede sablonu:<br/>
    PROJEKT/src/main/resources/templates/meme-template.html

6.  Prohlizec posle pozadavek na:<br/>
    http://pokus.com/ukol02/kontakt/napiste-nam.html

    Existuje @RequestMapping("/kontakt/napiste-nam.html"),<br/>
    metoda vraci ModelAndView("kontakt/napiste-nam-template")

    Spring Boot provede sablonu:<br/>
    PROJEKT/src/main/resources/templates/kontakt/napiste-nam-template.html

7.  Prohlizec posle pozadavek na:<br/>
    http://pokus.com/ukol02/admin/prihlaseni.html

    Existuje @RequestMapping("/admin/prihlaseni.html")<br/>
    metoda vraci ModelAndView("vsichni-uzivatele/login-template")

    Spring Boot provede sablonu:<br/>
    PROJEKT/src/main/resources/templates/vsichni-uzivatele/login-template.html

8.  Prohlizec posle pozadavek na:<br/>
    http://pokus.com/ukol02/admin/pokus.html

    Existuje @RequestMapping("/admin/pokus.html")<br/>
    metoda vraci ModelAndView()         // Bez udani jmena sablony!

    Spring Boot provede sablonu:
    PROJEKT/src/main/resources/templates/admin/pokus.html

9.  Prohlizec posle pozadavek na:
    http://pokus.com/ukol02/cervena-karkulka.html

    Neexistuje ani @RequestMapping("/cervena-karkulka.html")
    ani staticky soubor PROJEKT/src/main/resources/static/cervena-karkulka.html

    Spring Boot odesle chybovou stranku



Relativni vs absolutni cesta ve webove strance (uvnitr html souboru)
--------------------------------------------------------------------
Odkazy vyhodnocuje webovy prohlizec

Priklady:
1.  Jsme na strance:<br/>
    http://sladkost.tomcat.cloud/ukol02/vyrok/index.html

    V ni je:<br/>
    `<a href="pokus.html">Odkaz</a>`

    Po klinuti na odkaz pujde prohlizec na:<br/>
    http://sladkost.tomcat.cloud/ukol02/vyrok/pokus.html


2.  Jsme na strance:<br/>
    http://sladkost.tomcat.cloud/ukol02/vyrok/index.html

    V ni je:<br/>
    `<a href="../o-me/">Odkaz</a>`

    Po klinuti na odkaz pujde prohlizec na:<br/>
    http://sladkost.tomcat.cloud/ukol02/o-me/


3.  Jsme na strance:<br/>
    http://sladkost.tomcat.cloud/ukol02/vyrok/index.html

    V ni je:<br/>
    `<a href="/pokus.html">Odkaz</a>`

    Po klinuti na odkaz pujde prohlizec na:<br/>
    http://sladkost.tomcat.cloud/pokus.html


4.  Jsme na strance:<br/>
    http://sladkost.tomcat.cloud/ukol02/vyrok/index.html

    V ni je:<br/>
    `<a href="//zkouska/pokus.html">Odkaz</a>`

    Po klinuti na odkaz pujde prohlizec na:<br/>
    http://zkouska/pokus.html


5.  Jsme na strance:<br/>
    https://sladkost.tomcat.cloud/ukol02/index.html

    V ni je:<br/>
    `<a href="//zkouska/pokus.html">Odkaz</a>`

    Po klinuti na odkaz pujde prohlizec na:<br/>
    https://zkouska/pokus.html

