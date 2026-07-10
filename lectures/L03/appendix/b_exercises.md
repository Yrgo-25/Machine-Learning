# Bilaga B - Övningsuppgifter
Ni ska bygga ut klassen `ml::lin_reg::Fixed` från L02 med randomiserad träningsordning.

---

### 1. MatrixU32-alias
Lägg till aliaset `MatrixU32` i headerfilen `ml/types.h` som ett substitut för `std::vector<std::uint32_t>`, i namnrymden `ml`.

---

### 2. Slumptalsgenerator
Lägg till en funktion döpt `initRandom()` i en anonym namnrymd i `source/ml/lin_reg/fixed.cpp`. Funktionen ska initiera slumptalsgeneratorn en enda gång vid anrop:
* Använd en statisk lokal variabel döpt `initialized` (av typen `bool`, initierad till `false`) för att hålla koll på om generatorn redan har initierats.
* Initiera generatorn med `std::srand(std::time(nullptr))`.
* Sätt `initialized` till `true` efter initiering.
* Inkludera `<cstdlib>` och `<ctime>` i `fixed.cpp`.
* Ska markeras `noexcept`.

---

### 3. Träningsordning
Lägg till en privat medlemsvariabel döpt `myTrainOrder` i `Fixed`:
* Ska innehålla indexen för träningsuppsättningarna som osignerade heltal (`ml::MatrixU32`).
* Initieras i konstruktorn med indexen `0, 1, 2 ... N-1`, där `N` är antalet träningsuppsättningar.

---

### 4. Shuffling
Lägg till en privat metod döpt `shuffle()` i `Fixed`:
* Ska blanda innehållet i `myTrainOrder` i slumpvis ordning.
* För varje index `i`, välj ett slumpmässigt index `r` och byt plats på `myTrainOrder[i]` och `myTrainOrder[r]`.
* Ska markeras `noexcept`.

---

### 5. Uppdatera träningsmetoden
Uppdatera metoden `train()` i `source/ml/lin_reg/fixed.cpp`:
* Anropa `initRandom()` i början av metoden.
* Anropa `shuffle(myTrainOrder)` i början av varje epok.
* Iterera genom träningsuppsättningarna i den ordning som `myTrainOrder` anger i stället för sekventiellt via index.

---

### 6. Kompilering och test
Kompilera och testkör programmet. Träningsordningen ska nu vara slumpmässig varje epok, men det predikterade resultatet ska fortfarande stämma överens med träningsdatan efter genomförd träning.

---
