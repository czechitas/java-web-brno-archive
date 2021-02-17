Úkol - Metody s parametrem
--------------------------

### Část 1

Nejprve si okopírujte projekt **30-Turtle-Param_metody** na **40-Turtle-Param_ukol**.
Vymažte z něj všechny původní metody a ponechejte si jen prázdný `main(...)`.

Nyní vytvořte metody na kreslení základních tvarů (s pevnými velikostmi stran): 
Rovnoramenného trojúhelníku, čtverce, obdelníku a kolečka.


### Část 2

Pro další práci bude třeba, aby základní tvary měly volitelné velikosti stran,
tedy aby se jejich velikost dala nastavit.
Připravíme metody tak, aby přijímaly vstupní parametr(y).
Pro zopakování je níže vidět ukázka metody **nakresliRovnostrannyTrojuhelnik()**,
která přijímá jako vstupní parametr **velikostStrany** typu **double**.
Navenek je potřeba metodu zavolat s nějakou
hodnotou, uvnitř metody se **velikostStrany** chová jako proměnná
(proto musí mít definovaný typ, např. **double**).

    public void main(String[] args) {
        zofka.setLocation(100.0, 100.0);
        // Volani metody, do ktere se preda hodnota 10
        nakresliRovnostrannyTrojuhelnik(50.0);

        zofka.setLocation(300.0, 100.0);
        // Volani metody, do ktere se preda hodnota 15
        nakresliRovnostrannyTrojuhelnik(70.0);
    }

    public void nakresliRovnostrannyTrojuhelnik(double velikostStrany) {
        // Zde lze používat proměnnou velikostStrany.
        // Jeji hodnota je takova, s jakou byla metoda zavolana
        // napr.
        zofka.move(velikostStrany);
        zofka.turnLeft(120.0);
        zofka.move(velikostStrany);
        zofka.turnLeft(120.0);
        zofka.move(velikostStrany);
        zofka.turnLeft(120.0);
    }

Metoda může přijímat i více parametrů za sebou (oddělujeme čárkou):

    public void main(String[] args) {
        Color cervenaBarva;
        cervenaBarva = new Color(255, 0, 0);

        zofka.setLocation(100.0, 100.0);
        // Volani metody, do ktere se preda hodnota 10 a objekt, ktery je v promenne cervenaBarva
        nakresliBarevnyRovnostrannyTrojuhelnik(50.0, cervenaBarva);

        zofka.setLocation(300.0, 100.0);
        // Volani metody, do ktere se preda hodnota 15 a nove vytvoreny objekt barvy
        nakresliBarevnyRovnostrannyTrojuhelnik(70.0, new Color(0, 0, 255));
    }

    public void nakresliBarevnyRovnostrannyTrojuhelnik(double velikostStrany, Color barvaCary) {
        // Zde lze používat proměnnou velikostStrany a barva:
        zofka.setPenColor(barvaCary);
        zofka.move(velikostStrany);
        zofka.turnLeft(120.0);
        zofka.move(velikostStrany);
        zofka.turnLeft(120.0);
        zofka.move(velikostStrany);
        zofka.turnLeft(120.0);
    }

Vytvořte tedy parametrizované metody na kreslení
rovnoramenného trojuhelníku, čtverce, obdelníku a kolečka.
Metody by měly přijímat
jako vstupní parametr velikost strany (typu double).
V případě trojúhelníku ještě úhel mezi rameny.
V případě obdelníku budou nutné dva parametry (strana A, strana B).
V případě kolečka se bude předávat velikost kolečka
(zda poloměr, průměr nebo nějakou jinou charakteristiku, nechám na vás).

Pokud chcete, jako bonus můžou metody přijímat i barvu kreslení. Není to ale povinné.

Pokud byste chtěli vypočítat, jak má být dlouhá
třetí strana rovnoramenného trojúhelníku, pokud znáte délku ramene
a úhel mezi rameny,
ušetřím vás googlení analytické geometrie.
Zde je metoda, která délku třetí strany vypočítá.
V hodině jsme si neukazovali, jak vypadá metoda, která vrací nějaký výstup.
V tomto případě jde o kombinaci jak vstupních parametrů, tak výstupu.

    public void main(String[] args) {
        double stranaC;
        
        stranaC = vypocitejDelkuTretiStrany(100.0, 90.0);
        System.out.println("Strana A je 100, strana B je 100, strana C je " + stranaC);
    }

    public double vypocitejDelkuTretiStrany(double velikostRamene, double uhelMeziRameny) {
        double tretiStrana;
        tretiStrana = Math.abs((velikostRamene
                * Math.sin((uhelMeziRameny * Math.PI / 180.0) / 2.0))
                * 2.0);
        return tretiStrana;
    }


### Část 3

Pomocí metod výše nakreslete následující obrázky (zmrzlinu, sněhuláka a mašinku).

<img src="img/ukol03-zmrzlina.svg" width="100" />

<img src="img/ukol03-snehulak.svg" height="300" />

<img src="img/ukol03-lokomotiva.svg" height="150" />


### Odevzdání domácího úkolu

Nejprve appku (`40-Turtle-Param_ukol`)
zbavte přeložených spustitelných souborů.
Zařídíte to tak, že v IntelliJ IDEA vpravo zvolíte
Maven Projects -> Lifecycle -> Clean.
Úspěch se projeví tak, že v projektové složce zmizí
podsložka target.
Následně složku s projektem
zabalte pomocí 7-Zipu pod jménem `Ukol03-Vase_Jmeno.7z`.
(Případně lze použít prostý zip, například na Macu).
Takto vytvořený archív nahrajte na Google Drive
do složky `Ukol03`.

Vytvořte snímek obrazovky spuštěného programu
a pochlubte se s ním v galerii **Ukol03** na 
[Facebooku](https://www.facebook.com/groups/1149617575205475/photos/?filter=albums).

Pokud byste chtěli odevzdat revizi úkolu (např. po opravě),
zabalte ji a nahrajte ji na stejný Google Drive znovu,
jen tentokrát se jménem `Ukol03-Vase_Jmeno-verze2.7z`.

Termín odevzdání je do úterý 23. 10. 2018 23:59.
Pokud úkol nebo revizi odevzdáte později,
prosím pošlete svému opravujícímu kouči/lektorovi email nebo zprávu přes FB.
