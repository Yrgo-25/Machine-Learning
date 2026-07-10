# Bilaga B - Övningsuppgifter
Ni ska bygga ut klassen `ml::lin_reg::Fixed` från L03 med precisionsberäkning.

---

### 1. Precisionsberäkning
Lägg till en privat metod döpt `precision()` i klassen `Fixed`. Metoden ska beräkna precisionen för modellen givet träningsdatan:
* Beräkna medelabsolutfelet (MAE) för samtliga träningsuppsättningar:
    * För varje träningsuppsättning, beräkna det absoluta felet: `abs(output - predict(input))`.
    * Summera samtliga absoluta fel och dividera med antalet träningsuppsättningar.
* Returnera `1.0 - MAE` som ett flyttal.
* Ska markeras `const` samt `noexcept`.

---

### 2. Uppdatera träningsmetoden
Uppdatera metoden `train()` i `source/ml/lin_reg/fixed.cpp` så att träningen avslutas tidigt om precisionen överstiger ett givet tröskelvärde:
* Lägg till ett ingående argument döpt `precision` (ett flyttal som anger tröskelvärdet för precision). Defaultvärde: `0.999999` (99,9999 %).
* Skriv ut ett felmeddelande och anropa `std::terminate()` (från `<exception>`) om `precision` faller utanför intervallet [0.0, 1.0].
* Anropa `precision()` efter var tionde epok.
* Avbryt träningen och returnera `true` om precisionen överstiger `precision`.

---

### 3. Kompilering och test
Kompilera och testkör programmet. Modellen ska nu avbryta träningen så snart precisionen överstiger tröskelvärdet. Skriv ut antal epoker och uppnådd precision i `train()` för att verifiera.

---
