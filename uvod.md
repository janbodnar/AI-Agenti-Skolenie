# Úvod

Kurz **AI Agenti a customizácia** nadväzuje na základy práce s veľkými  
jazykovými modelmi. V predchádzajúcich kapitolách sme model používali ako  
nástroj, ktorému položíme otázku a dostaneme odpoveď. V tomto kurze sa  
posunieme ďalej: naučíme model **konať**. Ukážeme si, ako z jazykového modelu  
vznikne agent, ktorý si sám naplánuje postup, použije nástroje, pamätá si stav  
úlohy a výsledok skontroluje.  

> 💡 **Hlavná myšlienka kurzu:** model sám osebe je zdroj schopností, ale až  
> riadiaca vrstva okolo neho (harness) z neho robí užitočného a kontrolovaného  
> agenta.  

Cieľom úvodu je zjednotiť slovník. Skôr než začneme agentov stavať a  
prispôsobovať, musíme si ujasniť, čo agent je, z čoho sa skladá a čím sa líši  
od bežného chatbota.  

---

## Definícia a typy agentov

**Agent** je systém, ktorý na dosiahnutie zadaného cieľa vykonáva **postupnosť  
krokov**, pričom v každom kroku môže použiť nástroje, vyhodnotiť výsledok a  
rozhodnúť sa, čo urobí ďalej.  

Zjednodušene povedané: kým chatbot odpovedá, agent **koná**. Dostane cieľ,  
nie presný zoznam príkazov, a sám si nájde cestu k výsledku.  

> 🎓 **Analógia:** Jednorazový prompt je ako otázka na recepcii. Chatbot je ako  
> telefonát s operátorom, ktorý si pamätá, čo ste už povedali. Agent je ako  
> asistent, ktorému zadáte úlohu a on ju vyrieši – obvolá, čo treba, pripraví  
> podklady a prinesie výsledok.  

Podľa miery autonómie a spôsobu práce rozlišujeme niekoľko typov:  

| Typ agenta | Ako pracuje | Príklad |
| :--- | :--- | :--- |
| **Reflexný (jednokrokový)** | Zareaguje na vstup jednou akciou bez plánovania. | Klasifikácia e-mailu na „spam / nie spam". |
| **Nástrojový (tool-using)** | Použije jeden alebo viac nástrojov, ale drží sa pevného postupu. | Vyhľadanie zákazníka v databáze a vypísanie jeho objednávok. |
| **Reaktívny (ReAct)** | Strieda uvažovanie a akcie: *premýšľa → koná → pozoruje výsledok → pokračuje*. | Vyhľadá informáciu, zistí, že chýba, a upraví dopyt. |
| **Plánovací (plan-and-execute)** | Najprv zostaví celý plán, potom ho po krokoch vykoná. | Príprava reportu: naplánuj zdroje, zozbieraj dáta, zhrň, vygeneruj dokument. |
| **Multi-agentný** | Viac agentov s vymedzenými rolami; jeden zvyčajne riadi ostatných (orchestrátor + subagenti). | Tím: jeden hľadá, druhý píše, tretí kontroluje fakty. |
| **Proaktívny (autonómny)** | Pracuje na pozadí, sleduje stav a koná bez priameho podnetu. | Agent, ktorý ráno prejde schránku a pripraví návrhy odpovedí. |

> ⚠️ **Pozor:** Vyššia autonómia znamená vyšší výkon, ale aj vyššie riziko.  
> Väčšina produkčných riešení preto začína pri jednoduchších typoch a  
> autonómiu pridáva postupne spolu s pravidlami a kontrolami.  

---

## Štruktúra agenta: cieľ, nástroje, pamäť, spätná väzba

Každý agent, od najjednoduchšieho po najzložitejší, stojí na štyroch pilieroch.  

### 1. Cieľ

Cieľ určuje, **čo má byť na konci hotové**. Agent nedostáva presný recept, ale  
zadanie, ktoré musí pochopiť a rozložiť na kroky.  

* Dobre definovaný cieľ je konkrétny, merateľný a ohraničený.
* Ak je cieľ nejasný, agent „háda" – a výsledok býva nepredvídateľný.
* Súčasťou cieľa sú aj **obmedzenia**: rozpočet, čas, čo agent nesmie robiť.

### 2. Nástroje

**Nástroje (tools)** sú schopnosti, ktoré agent dostane navyše. Samotný model  
nemá prístup k databáze, e-mailu ani súborom – robí to iba cez nástroje.  

* vyhľadávanie na webe a v dokumentoch,
* čítanie a zápis súborov,
* volanie API a databázové dopyty,
* odoslanie e-mailu, vytvorenie úlohy, zápis do systému.

> Každý nástroj má popísaný názov, účel a schému vstupov. Model navrhne volanie,  
> ale o tom, či sa vykoná, rozhoduje riadiaca vrstva, nie model.  

### 3. Pamäť

**Pamäť** určuje, čo si agent pamätá medzi jednotlivými krokmi a reláciami.  

* **Krátkodobá (kontext)** – pracovná pamäť jednej úlohy: zadanie, výsledky
  nástrojov, rozpracované kroky.
* **Dlhodobá** – údaje, ktoré prežijú reláciu: preferencie používateľa, fakty
  v databáze, znalostná báza, zhrnutia starších konverzácií.

Harness rozhoduje, čo sa do kontextu dostane – nie celá história, ale relevantný  
výber, ktorý sa zmestí do kontextového okna.  

### 4. Spätná väzba

**Spätná väzba** uzatvára slučku. Agent po každej akcii skontroluje výsledok a  
podľa neho sa rozhodne, či pokračuje, upraví postup, alebo skončí.  

* Úspech nástroja → pokračuj na ďalší krok.
* Chyba alebo prázdny výsledok → skús iný nástroj alebo uprav dopyt.
* Neistý alebo rizikový krok → vyžiadaj schválenie človekom.

Bez spätnej väzby by agent iba slepo vykonával predvolený postup a nedokázal by  
sa prispôsobiť realite.  

> 💡 Cieľ, nástroje, pamäť a spätná väzba spolu tvoria **vykonávaciu slučku**.  
> Podrobne ju rozoberá kapitola o AI harnesse.  

---

## Rozdiel medzi agentom, chatbotom a jednorazovým promptom

Tieto tri spôsoby práce s modelom sa často zamieňajú. Líšia sa počtom krokov,  
pamäťou, používaním nástrojov a mierou autonómie.  

| Vlastnosť | Jednorazový prompt | Chatbot | Agent |
| :--- | :--- | :--- | :--- |
| **Počet krokov** | Jeden. | Jeden na jednu správu. | Viac, kým nie je cieľ splnený. |
| **Pamäť** | Žiadna. | História konverzácie. | Kontext úlohy + dlhodobá pamäť. |
| **Nástroje** | Žiadne. | Výnimočne (vyhľadávanie). | Viacero, volané podľa potreby. |
| **Rozhodovanie** | Robí človek. | Robí človek (vedie dialóg). | Rozhoduje agent v rámci pravidiel. |
| **Iniciátor akcie** | Človek. | Človek. | Agent pracuje aj sám. |
| **Výsledok** | Text. | Text. | Vyriešená úloha / zmena v systéme. |

**Zhrnutie:**  

* **Jednorazový prompt** – položíte otázku, dostanete odpoveď. Kroky vediete vy.
* **Chatbot** – viackrokový *rozhovor*, ale stále ho vedie človek; model
  odpovedá, nekoná.
* **Agent** – dostane **cieľ** a sám vyberá kroky, nástroje a rozhodnutia, aby
  ho naplnil.

> 🎓 **Praktické pravidlo:** Ak vám stačí jedna odpoveď, použite prompt. Ak  
> potrebujete konzultovať, použite chatbota. Ak potrebujete, aby sa niečo  
> **urobilo**, nasaďte agenta.  

---

## Príklady použitia

Nasledujúce tri príklady patria medzi najčastejšie nasadenia agentov v praxi.  
V každom z nich vidno všetky štyri piliere – cieľ, nástroje, pamäť aj spätnú  
väzbu.  

### Spracovanie e-mailov

**Cieľ:** pretriediť schránku a pripraviť návrhy odpovedí.  

1. Agent prečíta nové správy cez nástroj na prístup k pošte.
2. Roztriedi ich (pracovné, faktúry, požiadavky, spam).
3. Pri požiadavkách vyhľadá kontext – objednávku, klienta, predchádzajúcu
   komunikáciu.
4. Pripraví návrh odpovede a tam, kde je to citlivé, ho nechá na **schválenie
   človekom**.

*Nástroje:* e-mailové API, databáza klientov, vyhľadávanie v dokumentoch.  

### Výskum

**Cieľ:** zozbierať a overiť informácie k zadanej téme.  

1. Agent rozloží tému na podotázky.
2. Postupne vyhľadáva zdroje (web, databázy, interné dokumenty).
3. Porovná a overí nálezy; ak si nie sú isté, hľadá ďalšie zdroje.
4. Spracuje výsledky do prehľadu s odkazmi na zdroje.

*Nástroje:* webové a akademické vyhľadávanie, čítanie PDF, ukladanie poznámok.  

### Príprava reportov

**Cieľ:** z dostupných dát vytvoriť pravidelný report.  

1. Agent načíta aktuálne dáta z databázy alebo z tabuliek.
2. Vypočíta kľúčové ukazovatele a porovná ich s predchádzajúcim obdobím.
3. Zhrnie výsledky, upozorní na odchýlky a navrhne možné príčiny.
4. Vygeneruje dokument alebo prezentáciu a uloží ju na určené miesto.

*Nástroje:* databázové dopyty, výpočty, generovanie dokumentov, úložisko.  

> ⚠️ **Spoločný znak všetkých príkladov:** agent robí rutinnú prácu, ale  
> kritické a nezvratné kroky (odoslanie, zverejnenie, platba) zostávajú pod  
> ľudskou kontrolou.  

---

## Čo si z úvodu odnesieme

* **Agent** je systém, ktorý na dosiahnutie cieľa sám vykonáva kroky a používa
  nástroje.
* Každý agent stojí na štyroch pilieroch: **cieľ, nástroje, pamäť, spätná
  väzba**.
* Agent sa od chatbota a jednorazového promptu líši **autonómiou, pamäťou a
  schopnosťou konať**.
* Typické nasadenia sú spracovanie e-mailov, výskum a príprava reportov.

> 📝 *Toto je prvotný koncept úvodu. Slúži ako kostra pre ďalšie kapitoly  
> kurzu – jednotlivé časti (typy agentov, štruktúra, príklady) môžeme postupne  
> rozšíriť o konkrétne ukážky a praktické cvičenia.*  

