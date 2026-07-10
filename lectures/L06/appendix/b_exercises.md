# Bilaga B - Övningsuppgift: Enkelt neuralt nätverk i C++ (del I)
Ni ska bygga vidare på er `ml`-kodbas med ett interface samt en klass för ett enkelt neuralt 
nätverk innehållande ett dolt lager samt ett utgångslager.

---

### 1. Katalogstruktur
Bygg ut katalogstrukturen i er befintliga `ml`-kodbas enligt nedan:

```
ml/
├── include/
│   └── ml/
│       ├── dense_layer/
│       │   ├── interface.h
│       │   └── stub.h
│       ├── neural_network/
│       │   ├── interface.h
│       │   └── shallow.h
│       └── types.h
├── source/
│   ├── neural_network/
│   │   └── shallow.cpp
│   └── main.cpp
└── Makefile
```

Glöm inte att lägga till `source/neural_network/shallow.cpp` i er makefil.

---

### 2. Dense-lagrets interface och stubbklass
Det dolda lagret samt utgångslagret representeras av interfacet `ml::dense_layer::Interface`. 
En skarp implementation skapas först under **L08–L09**. Fram tills dess implementerar ni en enkel 
stubbklass `ml::dense_layer::Stub` som placeholder.

**Interfacet (`ml/dense_layer/interface.h`):**
I namnrymden `ml::dense_layer`, implementera ett interface döpt `Interface`. Samtliga metoder 
(förutom destruktorn) ska deklareras som rent virtuella (`= 0`).

* **`~Interface()`:** Ska sättas till `default` samt markeras `virtual` och `noexcept`.

Getters, samtliga `const`, `noexcept` samt `[[nodiscard]]`:

| Metod | Returnerar |
|---|---|
| `nodeCount()` | Antal noder i lagret (`std::size_t`). |
| `weightCount()` | Antal vikter per nod (`std::size_t`). |
| `output()` | Referens till lagrets utdata (skrivskyddad flyttalsvektor). |
| `error()` | Referens till lagrets fel (skrivskyddad flyttalsvektor). |
| `weights()` | Referens till lagrets vikter (skrivskyddad, tvådimensionell flyttalsvektor). |

Beräkningsmetoder, samtliga `noexcept` och utan returvärde (`void`). Vid ogiltig indata (felaktig 
dimension eller ogiltig lärhastighet) ska ett felmeddelande skrivas ut och `std::terminate()` 
anropas:

* **`feedforward(input)`:** Genomför feedforward. `input`: skrivskyddad flyttalsvektor med indata.
* **`backpropagate(output)`** (utgångslager): Beräknar fel utifrån referensvärden. `output`: 
  skrivskyddad flyttalsvektor med referensvärden.
* **`backpropagate(nextLayer)`** (dolt lager): Beräknar fel utifrån nästa lager. `nextLayer`: 
  referens till nästa lager (`const Interface&`).
* **`optimize(input, learningRate)`:** Uppdaterar bias och vikter. `input`: skrivskyddad 
  flyttalsvektor. `learningRate`: flyttal.

**Stubbklassen (`ml/dense_layer/stub.h`):**
I namnrymden `ml::dense_layer`, implementera en underklass döpt `Stub` som ärver `Interface` via 
publikt arv. Klassen ska markeras `final`. Stubben genomför ingen riktig beräkning - den finns 
enbart för att det neurala nätverket ska gå att kompilera och testköra innan en skarp 
`Dense`-implementation finns (se **L08–L09**).

* **`Stub()`:** Tar emot `nodeCount` samt `weightCount` (osignerade heltal). Initierar 
  utdatavektorn till ett fast värde (t.ex. `0.5`), övriga vektorer (fel, bias, vikter) till 
  nollor. Ska markeras `explicit` samt `noexcept`.
* **`~Stub()`:** Ska markeras `default`, `noexcept` samt `override`.
* Övriga getters: Ska markeras `override` (behåller interfacets `const` och `noexcept`, men 
  **inte** `[[nodiscard]]`).
* **`feedforward()`**, båda varianterna av **`backpropagate()`** samt **`optimize()`**: Gör endast 
  range-checkar och anropar `std::terminate()` vid mismatch. Ska markeras `override` samt 
  `noexcept`.

För denna klass ska default-konstruktorn samt copy- och move-konstruktorerna (och tillhörande 
operatorer) raderas.

Lägg till lämpliga privata medlemsvariabler för att lagra antalet noder, antalet vikter per nod, 
utdata, fel, bias samt vikter.

---

### 3. Interface för neurala nätverk
I headerfilen `ml/neural_network/interface.h`, lägg till en namnrymd döpt `ml::neural_network`. 
I denna namnrymd, implementera ett interface döpt `Interface`:

* **`~Interface()`:** Ska sättas till `default` samt markeras `virtual` och `noexcept`.
* **`predict(input)`:** Rent virtuell. `input`: skrivskyddad flyttalsvektor med indatan som 
  prediktionen ska baseras på. Returnerar en referens till en flyttalsvektor med det predikterade 
  värdet. Ska markeras `noexcept` (**inte** `const`, eftersom lagrens output uppdateras vid varje 
  prediktion).

---

### 4. Klassen Shallow - deklaration
I headerfilen `ml/neural_network/shallow.h`, lägg till namnrymden `ml::neural_network`. 
Implementera en underklass döpt `Shallow` som ärver `Interface` via publikt arv. Klassen ska 
markeras `final`.

* **`Shallow()`:** Tar emot `hiddenLayer` samt `outputLayer` (nätverkets dolda lager respektive 
  utgångslager, `ml::dense_layer::Interface&`), samt `trainInput` och `trainOutput` 
  (skrivskyddade, tvådimensionella flyttalsvektorer med träningsdatans in- och utdata). Ska 
  markeras `explicit` samt `noexcept`.
* **`~Shallow()`:** Ska markeras `default`, `noexcept` samt `override`.
* **`predict()`:** Överlagring av motsvarande metod i interfacet. Ska markeras `noexcept` samt 
  `override`.
* **`train(epochCount, learningRate = 0.01)`:** Tränar nätverket (implementeras i sin helhet i 
  **L07**). `epochCount`: antal epoker att träna (osignerat heltal). `learningRate`: lärhastighet 
  (flyttal). Returnerar `true` om träning genomfördes, annars `false`. Ska markeras `noexcept`.

Ni får gärna lägga till fler (privata) metoder vid behov.

---

### 5. Borttagna konstruktorer och operatorer
Radera klassens default-konstruktor, kopierings- och förflyttningskonstruktorer samt tillhörande operatorer.

---

### 6. Privata medlemsvariabler
Lägg till följande privata medlemsvariabler i `Shallow`:
* **`myHiddenLayer`:** Referens till nätverkets dolda lager, erhålles via konstruktorn.
* **`myOutputLayer`:** Referens till nätverkets utgångslager, erhålles via konstruktorn.
* **`myTrainInput`:** Referens till träningsdatans indata, erhålles via konstruktorn.
* **`myTrainOutput`:** Referens till träningsdatans utdata, erhålles via konstruktorn.
* **`myTrainSetCount`:** Konstant osignerat heltal som anger antalet fullständiga träningsuppsättningar (dvs. det minsta av `myTrainInput.size()` och `myTrainOutput.size()`).

---

### 7. Konstruktor och prediktion
Implementera följande i `source/neural_network/shallow.cpp`:

**Konstruktorn:**
* Initiera samtliga medlemsvariabler enligt beskrivningen ovan.

**Metoden `predict()`:**
* Genomför feedforward genom hela nätverket:
    1. Anropa `myHiddenLayer.feedforward(input)` med given indata.
    2. Anropa `myOutputLayer.feedforward(myHiddenLayer.output())` med det dolda lagrets output som indata.
* Returnera `myOutputLayer.output()` (en referens - ingen egen lagringsvariabel behövs i `Shallow`).

---

### 8. Träningsmetod (placeholder)
Implementera en tillfällig version av `train()` i `source/neural_network/shallow.cpp` som enbart returnerar `true`. Den fullständiga implementationen (feedforward, backpropagation samt optimering för varje träningsuppsättning och epok) genomförs under **L07**.

---

### 9. Kompilering och test
Skriv en `main`-funktion i `main.cpp` som:
* Skapar en `ml::dense_layer::Stub`-instans för det dolda lagret samt en för utgångslagret (t.ex. 3 noder/2 vikter per nod respektive 1 nod/3 vikter per nod - antalet vikter i utgångslagret ska matcha antalet noder i det dolda lagret).
* Skapar en `ml::neural_network::Shallow`-instans utifrån dessa två lager samt valfri träningsdata (t.ex. ett 2-bitars XOR-mönster).
* Genomför en prediktion för varje träningsuppsättnings indata, och skriver ut indatan samt den predikterade utdatan i terminalen.

Kompilera och testkör programmet. Ni ska få följande utskrift (dense-lagren är fortfarande stubbar, så prediktionen är alltid 0.5):

```
--------------------------------------------------------------------------------
Input: 0 0, predicted output: 0.5
Input: 0 1, predicted output: 0.5
Input: 1 0, predicted output: 0.5
Input: 1 1, predicted output: 0.5
--------------------------------------------------------------------------------
```

---
