# Bilaga A - Bakgrund
Denna bilaga introducerar några grundläggande koncept som används senare i kursen. Först presenteras `std::vector`, som används för att lagra träningsdata och parametrar. Därefter följer en introduktion till maskininlärning och hur denna skiljer sig från traditionell programmering.

---

## 1. Vektorer i C++
`std::vector` är standardbibliotekets dynamiska array. Till skillnad från en vanlig array kan dess storlek ändras under programmets körning. Minnesallokeringen hanteras automatiskt, så man behöver inte tänka på att frigöra minne för att undvika minnesläckor. I denna kurs används vektorer bland annat för att lagra träningsdata, parametrar och prediktioner.

`std::vector` definieras i headern `<vector>`:

```cpp
#include <vector>
```

---

### Skapa och initiera en vektor
Vektorer kan skapas enligt nedan:

```cpp
// Skapar tom vektor som kan lagra heltal.
std::vector<int> v1{};

// Skapar flyttalsvektor initierad med tre element.
std::vector<double> v2{1.0, 2.0, 3.0};

// Skapar bytevektor som innehåller 100 femmor.
std::vector<std::uint8_t> v3(100U, 5U);
```

---

### Vanliga metoder

| Metod | Beskrivning |
| :---- | :---------- |
| `push_back(val)` | Lägger till `val` sist i vektorn |
| `pop_back()` | Tar bort det sista elementet |
| `size()` | Returnerar antalet element |
| `resize(size, val)` | Ändrar storlek till `size` element; nya element sätts till `val` |
| `clear()` | Tömmer vektorn |
| `empty()` | Returnerar `true` om vektorn är tom |
| `begin()` / `end()` | Iteratorer till början/slutet |
| `erase(it)` | Raderar elementet på iterator `it` |

### Iteration
* Iteration via range-baserad for-loop (rekommenderas):

```cpp
for (const auto& val : values)
{         
    std::cout << val << "\n";
}
```

* Iteration via index (C-style):

```cpp
for (std::size_t i{}; i < values.size(); ++i) 
{
    std::cout << values[i] << "\n";
}
```

* Iteration via vektor-iteratorer:

```cpp
for (auto it = values.begin(); it != values.end(); ++it)
{
    std::cout << *it << "\n";
}
```

### Passera vektorer via referens
Vektorer bör som regel passeras via referens för att undvika onödig kopiering:

```cpp
void print(const std::vector<double>& values);      // Enbart läsning
void fill(std::vector<double>& values, double val); // Läsning och skrivning
```

Ytterligare information om `std::vector` finns på [cppreference.com](https://en.cppreference.com/w/cpp/container/vector).

---

## 2. Introduktion till maskininlärning

### Traditionell programmering
I traditionell programmering används någon typ av **indata** i kombination med uppsatta **regler** för att generera **utdata**:
* **Indata** kan exempelvis utgöras av insignaler från sensorer, en tryckknapp, en terminal eller en fil.
* **Regler** utgörs av villkor implementerade i programmet, exempelvis via vilkorssatser.
* **Utdata** kan exempelvis utgöras av signaler som skrivs till en aktuator, exempelvis för att styra en lysdiod, en motor eller en fläkt.

Som exempel på ett traditionellt program inom ett inbyggt system kan temperaturen läsas in som indata via en temperatursensor, där regler är implementerade via en villkorssats för att styra ett ventilationssystem utefter aktuell rumstemperatur. Nedan visas ett exempel på hur en metod för att implementera detta hade kunnat se ut i C++ för en metod `runVentilation()` i en fiktiv klass döpt `HardwareController`:

```cpp
/**
 * @brief Run the PWM controlled ventilation system. 
 */
void HardwareController::runVentilation() noexcept
{
    constexpr std::int8_t lowerTempLimit{10};
    constexpr std::int8_t upperTempLimit{25};

    constexpr double ventilatorOff{0.0};
    constexpr double ventilatorPwm{0.5};
    constexpr double ventilatorOn{1.0};

    // Read the room temperature with the tempsensor.
    const auto temperature = myTempSensor.read();

    // Disable the ventilator if the temperature is below 10 degrees Celsius.
    if (lowerTempLimit > temperature) 
    { 
        myVentilator.run(ventilatorOff);
    }
    // Run the ventilator at 50% if the temperature is between 10 and 25 degrees Celsius.
    else if ((lowerTempLimit <= temperature) && (upperTempLimit >= temperature)) 
    { 
        myVentilator.run(ventilatorPwm); 
    }
    // Run the ventilator at 100% if the temperature exceeds 25 degrees Celsius.
    else
    {
        myVentilator.run(ventilatorOn);
    }
}
```

Gällande ovanstående program kan vi enkelt urskilja **indata**, **regler** samt **utdata**:
* **Indatan** utgörs av insignaler från en temperatursensor implementerad via en instans döpt `myTempSensor`.
* **Reglerna** utgörs av en if else-sats för att styra ventilationssystemet utefter aktuell temperatur. Dessa regler har satts av en programmerare.
* **Utdatan** sätts antingen till `0.0`, `0.5` eller `1.0` utefter reglerna i vilkorssatsen. Denna utdata används för att styra hastigheten på ventilationssystemet, så att det antingen är avstängt eller går på hel- eller halvfart.

Ovanstående program utgör ett traditionellt program i den mån att en människa har satt reglerna. Utdatan utgör därmed en funktion av aktuell indata samt de satta reglerna:

```
Traditionell programmering: indata + regler => utdata
```

Exempel:

```text
Temperatur = 15 °C
Regler = "Kör ventilatorn på 50 %"
Utdata = PWM = 0.5
```

---

### Maskininlärning
I maskininlärning används i stället **indata** i kombination med **utdata** för att träna maskinen i syfte att denna ska upptäcka **regler/mönster** själv i form av sambandet mellan in- och utdatan. Vi behöver då använda träningsuppsättningar med indata samt utdata, som maskinen utsätts för många gånger.

När maskinen på detta sätt upptäcker reglerna själv sägs den bli tränad och datan den tränas på kallas träningsdata. Träningsdatan består av en eller flera träningsuppsättningar, där en given träningsuppsättning består av en insignal samt motsvarande utsignal.

Gällande det tidigare exemplet med styrning av ett ventilationssystem hade vi kunnat skapa träningsuppsättningar i form av olika temperaturer som indata samt värden `0.0`, `0.5` samt `1.0` som utdata:

| Indata | Utdata |
|--------|--------|
|  -10   |  0.0   |
|  -5    |  0.0   |
|   0    |  0.0   |
|   5    |  0.0   |
|   10   |  0.5   |
|   15   |  0.5   |
|   20   |  0.5   |
|   25   |  0.5   |
|   30   |  1.0   |
|   35   |  1.0   |
|   40   |  1.0   |

Vi hade sedan kunnat träna maskinen genom att utsätta denna för ovanstående träningsuppsättningar under ett visst antal **epoker** (omgångar). Under en epok utsätts modellen för samtliga träningsuppsättningar. Träning med en uppsättning sker enligt nedan:
* **Gissa:** 
    * Vi låter maskinen **prediktera** vad utdatan ska bli vid en viss temperatur. 
    * Som exempel ger vi maskinen indatan `15` och den gissar till en början (innan den har blivit tränad ordentligt) att rätt svar är `0.2`.
* **Mät felet:**
    * Vi **utvärderar** sedan maskinens svar genom att jämföra detta med referensdatan, alltså rätt svar, vilket vi får från vår utdata. 
    * Som exempel gissade vår maskin på `0.2` när vi gav den indatan `15`, men rätt svar är `0.5`. Skillnaden mellan prediktionen och referensvärdet är därmed `0.3`.
* **Förbättra modellen:**
    * Vi **optimerar** sedan maskinen så att den gissar bättre nästa gång. 
    * Detta gör vi genom att justera modellens parametrar. I ett neuralt nätverk motsvaras dessa parametrar av de vikter som kopplar samman neuronerna. 
    * Eftersom maskinen gissade lite för lågt bör vi öka dess parametrar något. Vid nästa gissning bör maskinens gissning vara närmare `0.5` än nu. 

Efter många små förändringar bör maskinen prediktera mycket nära träningsdatan. Vår maskin kan då anses vara tränad. Detta kan ta allt från ett tiotal upp till tusentals epoker beroende på hur komplext mönster maskinen ska lära sig. 

```
Maskininlärning: indata + utdata => regler
```

Exempel:

```text
Träningsdata:
Temperatur = 15 °C
PWM = 0.5

Vad lär sig modellen?
"Temperaturer omkring 15 °C bör ge en PWM-signal nära 0.5"
```

När modellen är färdigtränad används den sedan på samma sätt som ett traditionellt program: den tar emot indata och producerar utdata. Skillnaden är att reglerna har lärts in automatiskt i stället för att programmeras manuellt.

För att realisera en välfungerande maskininlärningsalgoritm krävs tre faktorer:
* Indata, exempelvis bilder på objekt, data från sensorer eller dylikt.
* Förväntad utdata, så att maskinen kan lära sig vilken utdata som förväntas för given indata.
* En metod för att mäta ifall algoritmen fungerar väl, alltså ifall aktuell utdata matchar förväntad utdata. Vid behov måste algoritmen justeras, vilket kallas lärande.

Sammanfattningsvis:
* Traditionell programmering bygger på att en människa skriver reglerna.
* Maskininlärning bygger på att reglerna lärs in från data.
* Efter träningen används modellen på samma sätt som ett traditionellt program för att generera utdata från indata.

---

### Varför maskininlärning?
Låt oss anta att vi ska försöka efterlikna mänsklig intelligens via ett traditionellt program. Det hade krävts en ofantlig mängd villkorssatser för att täcka alla beslut en människa tar under en given dag, särskilt med tanke på alla kombinationer av villkor som kan uppstå. När vi sedan tänker oss att detta program ska täcka alla beslut vi tar över lång tid är det lätt att inse att programmet hade blivit så ofantligt stort och komplext att:
* Det hade tagit evigheter att implementera programmet.
* Programmet hade tagit en ofantlig mängd minne.
* Programmet måste uppdateras när vi lär oss nya saker, vilket hade krävt enorma resurser sett till arbetstimmar.

Därmed är det inte görbart att efterlikna mänsklig intelligens med ett traditionellt program. Dock var detta den dominerande synen inom artificiell intelligens fram till slutet av 1980-talet och början av 1990-talet. Faktum är att det som presenterades ovan är ett exempel på vad som numera är känt som *symbolisk AI*, vilket var det dominerande paradigmet inom artificiell intelligens innan maskininlärning blev utbrett. Symbolisk AI är alltså i praktiken traditionell programmering, där en maskin förses med en mycket stor mängd med regler för ett enormt antal olika scenarion.

Symbolisk AI verkade lovande, då det medförde att maskiner kunde lösa komplexa, men mycket väldefinierade uppgifter, exempelvis att spela schack. Dock visade sig det vara inadekvat för mindre väldefinierade uppgifter, såsom datorseende, taligenkänning och språköversättning, där indata (bilder, ord, fraser) kan variera stort gällande olika ljus, vinklar, vem som säger något, dialekt och dylikt.

Därmed uppstod i stället maskininlärning, där maskinen själv får hitta reglerna genom att bli tränad via stora mängder indata samt motsvarande förväntad utdata. Om ett nytt scenario dyker upp får maskinen prediktera vad som ska göras utifrån tidigare erfarenheter från träningsdatan, vilket påminner om hur vi människor lär oss.

---
