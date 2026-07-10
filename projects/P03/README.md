# **P03** – Maskininlärning i ett inbyggt system

## Syfte
Ni ska i grupper bygga vidare på er kodbas från **P02** (Hårdvarunära programmering C/C++) genom att implementera maskininlärning i systemet i två faser.

Syftet är att:
* Implementera en adaptiv regressionsmodell som ett naturligt tillägg till befintlig kodbas och design.
* Skapa en smart temperatursensordriver som använder modellen för att prediktera temperaturen.
* Själva välja och implementera ytterligare en ML-algoritm för en egenvald uppgift.
* Skriva enhetstester för er egen implementation.
* Kombinera tidigare kunskaper om inbyggda system med nyvunnen kunskap inom maskininlärning.

---

## Upplägg och tidsplan
Projektet genomförs i två faser:
* **Fas 1 – Adaptiv linjär regressionsmodell.** Här integreras en fullständig, adaptiv regressionsmodell i er inbyggda systemkodbas. Denna modell används sedan för att skapa en smart temperatursensor.
* **Fas 2 – Valfri ML-algoritm.** När ni har gått igenom neurala nätverk (L05–L10) väljer ni själva en ytterligare ML-algoritm (t.ex. ANN, CNN eller annan) och löser en egenvald uppgift med den.

Fas 1 påbörjas under L03, direkt efter att linjär regression introducerats i L01–L02. Vid behov kan arbetet med Fas 1 fortsätta in i L05–L10, under avsatt lektionstid och eget arbete. Fas 2 påbörjas när neurala nätverk har gåtts igenom, tidigast efter L05 och senast efter L10.

---

## Fas 1 – Adaptiv linjär regressionsmodell

### Bakgrund
Temperatursensorn TMP36 har visat sig vara inkonsistent beroende på omgivning och fukt, vilket gör det svårt att beräkna temperaturen med en exakt ekvation. I stället för att byta hårdvara har kunden bestämt sig för en mjukvarulösning.

### Uppdrag
Ert uppdrag är att implementera en adaptiv maskininlärningsalgoritm i er befintliga kodbas för att prediktera rumstemperaturen. Den befintliga `driver::tempsensor::Tmp36`-instansen ska ersättas med en ny, smart driver som internt använder ML-modellen. Vid behov av kalibrering ska en utvecklare enkelt kunna träna om modellen genom att uppdatera träningsdatan i `main`, kompilera och köra om programmet.

### Träningsdata
**Träningsdatan** ska bestå av 10–15 träningsuppsättningar med:
* **Indata:** Råvärdet från temperatursensorn (den A/D-omvandlade insignalen).
* **Utdata:** Aktuell rumstemperatur i grader Celsius.

Träningen ska ske direkt vid systemstart, innan initiering av omgivande hårdvara.

### Kravspecifikation

#### Nya klasser
1. **`ml::lin_reg::Adaptive`**
   * Ärver `ml::lin_reg::Interface`.
   * Tränas med adaptiv lärhastighet som justeras automatiskt utefter träningsförloppet.
   * Träningsordningen randomiseras varje epok.
   * Träning pågår tills precisionen överstiger ett tröskelvärde som gruppen själva väljer (exempelvis 99,9 %).

2. **`driver::tempsensor::Smart`**
   * Ärver `driver::tempsensor::Interface`.
   * Använder internt en `ml::lin_reg::Adaptive`-instans för att prediktera temperaturen utifrån råvärdet från sensorn.
   * Tränar modellen via en dedikerad `train()`-metod.

#### Integration i befintlig kodbas
* Ersätt den befintliga `driver::tempsensor::Tmp36`-instansen med `driver::tempsensor::Smart` i systemlogiken. Systemet ska bete sig som tidigare, men nu med ML-baserad temperaturprediktering.

#### Funktionellt beteende
* Temperaturen ska predikteras och skrivas ut i en seriell terminal vid:
   * nedtryckning av en tryckknapp, **eller**
   * 60 sekunder efter senaste prediktionen.
* Nedtryckning av tryckknappen under pågående träning ska inte medföra någon temperaturmätning.

#### Kodkvalitet
* All tillagd kod ska följa befintlig kodstil och designmönster i kodbasen.
* Tydliga Doxygen-kommentarer för alla nya klasser och publika metoder.

### Designkrav
* `ml::lin_reg::Adaptive` får inte inkludera hårdvarunära headers; den ska vara plattformsoberoende.
* `driver::tempsensor::Smart` ska ta emot en `driver::adc::Interface&` i sin konstruktor, precis som `Tmp36`.
* Systemlogiken ska inte behöva känna till att en ML-modell används; den pratar enbart mot `driver::tempsensor::Interface`.

---

## Fas 2 – Valfri ML-algoritm

### Uppdrag
Varje grupp väljer själva en ytterligare ML-algoritm (t.ex. ett artificiellt neuralt nätverk, ett konvolutionellt neuralt nätverk, eller något annat ni motiverar) samt en egen uppgift och ett eget dataset att lösa/träna på. Uppgiften behöver inte vara kopplad till det inbyggda systemet eller temperatursensorn – välj något ni tycker är intressant och rimligt att hinna med.

Stäm av vald algoritm, uppgift och dataset med läraren innan ni börjar implementera, så att omfattningen blir rimlig.

### Krav
* Algoritmen ska implementeras för hand av gruppen (se [Användning av AI-verktyg](#användning-av-ai-verktyg)) – inte via ett färdigt ML-bibliotek som gör själva inlärningen/predikteringen åt er.
* Där det är rimligt ska samma designprinciper som i Fas 1 följas (tydliga interface, plattformsoberoende ML-kod, Doxygen-kommentarer).
* Implementationen ska ha enhetstester, se nedan.

### Enhetstester
* Er implementation av ML-algoritmen (och gärna även `ml::lin_reg::Adaptive` från Fas 1) ska ha enhetstester som verifierar centrala delar, t.ex. beräkningar i enskilda lager/steg, träningens konvergens och rimliga gränsfall.
* Välj ett lämpligt C++-testramverk (t.ex. Catch2, GoogleTest eller Unity) och motivera valet kort i `p01_rapport.md`.
* Testerna behöver inte köras på målhårdvaran – det är fullt tillräckligt att köra dem på värddatorn.

### Användning av AI-verktyg
Ni får använda Claude (eller motsvarande AI-verktyg) till:
* att skriva enhetstester,
* att sätta upp repot/projektet (t.ex. build-konfiguration, mappstruktur, CI).

Ni får **inte** använda AI för att skriva själva ML-implementationen (algoritmen i Fas 1 eller er valda algoritm i Fas 2) – den koden ska vara skriven av gruppen för hand. Ange tydligt i `p01_rapport.md` var och hur AI-verktyg har använts.

---

## Versionshantering (Git)
* All kod ska versionshanteras i samma repo som **P02**.
* All utveckling ska ske via branches och pull requests.
* Alla studenter ska bidra med egna commits.
* Läraren ska bjudas in som collaborator och genomför en kod-review på GitHub.

---

## Gruppindelning och individuell bedömning
* Samma grupper som i **P02**.
* Projektet genomförs i grupp.

### Bidragsrapport (obligatorisk)
Ni lämnar in en gemensam bidragsrapport i filen `p01_rapport.md` där ni övergripande beskriver vem som gjorde vad, vilken algoritm och uppgift ni valde till Fas 2, samt var AI-verktyg har använts. Utvärderingsfrågorna besvaras i samma fil (se [Utvärdering](#utvärdering)).

### Bedömning
* Projektets funktion och design bedöms på gruppnivå.
* Individuellt betyg baseras på bidragsrapporten, Git-aktivitet och förmågan att förklara kod och designbeslut vid redovisning.

**OBS!** En student som inte kan visa tillräckligt eget bidrag riskerar att inte bli godkänd, även om gruppens projekt uppfyller kraven.

**OBS!** Samtliga gruppmedlemmar förväntas ha grundläggande förståelse för den nya koden i sin helhet — även för delar man inte själv skrivit.

---

## Bedömningskriterier (G)
* Klassen `ml::lin_reg::Adaptive` är implementerad och fungerar korrekt.
* Klassen `driver::tempsensor::Smart` är implementerad och använder `Adaptive` internt.
* `Tmp36` är ersatt med `Smart` i systemlogiken utan att systemets yttre beteende förändras.
* Systemet uppfyller det funktionella beteendet för Fas 1 ovan.
* Vald ML-algoritm i Fas 2 är implementerad för hand och löser den egenvalda uppgiften på ett rimligt sätt.
* Både Fas 1 och Fas 2 har enhetstester som täcker centrala delar av implementationen.
* Koden följer befintlig kodstil och är väl dokumenterad.
* Det framgår tydligt var AI-verktyg har använts, i enlighet med [Användning av AI-verktyg](#användning-av-ai-verktyg).

---

## Redovisning
Projektet redovisas för lärare under lektionstid:
* Demo i hårdvara på ESP32-S3 av Fas 1.
* Demo av Fas 2, på målhårdvara eller värddator beroende på uppgiftens natur.
* Ni ska kunna förklara er implementation och designbeslut, samt visa och gå igenom era enhetstester.

---

## Utvärdering
Besvara följande frågor gemensamt i `p01_rapport.md`, tillsammans med bidragsrapporten:
1. Varför ärver `driver::tempsensor::Smart` interfacet i stället för att ersätta `Tmp36` direkt?
2. Vad är fördelen med att träna modellen vid systemstart i stället för att hårdkoda temperaturformeln?
3. Hur påverkar lärhastigheten träningsförloppet och varför är en adaptiv lärhastighet fördelaktig?
4. Varför valde ni er algoritm, uppgift och dataset i Fas 2, och vilka avvägningar gjorde ni?
5. Hur upplevde ni att skriva enhetstester för er ML-implementation, och vad testade ni?
6. Hur användes AI-verktyg i projektet, och vad tycker ni var för- och nackdelarna med den avgränsningen?
7. Vad lärde ni er av projektet? Vad var lätt respektive svårt?

---

## Bilaga A – Formler för temperatursensorn TMP36
Sambandet mellan AD-omvandlat värde $ADC_{value}$ och analog inspänning $U_{IN}$:

$$U_{IN} = \frac{ADC_{value}}{1023} \times 5\ \text{V}$$

Sambandet mellan inspänningen $U_{IN}$ och rumstemperaturen $T$:

$$T = (U_{IN} - 0{,}5) \times 100 = 100\, U_{IN} - 50$$

---
