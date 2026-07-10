# Bilaga B - Övningsuppgift: Enkelt neuralt nätverk i C++ (del II)
Ni ska färdigställa klassen `ml::neural_network::Shallow` från **L06** genom att implementera en fullständig träningsmetod.

---

### 1. Träningsmetod
Ersätt den tillfälliga versionen av `train()` i `source/neural_network/shallow.cpp` med en fullständig implementation:

**Indatakontroll:**
* Skriv ut ett felmeddelande och anropa `std::terminate()` (från `<exception>`) om `myTrainSetCount == 0`, `epochCount == 0` eller `learningRate <= 0.0 || learningRate >= 1.0`.

**Träning:**
* Iterera önskat antal epoker med en for-loop: `for (std::size_t epoch{}; epoch < epochCount; ++epoch)`.
* För varje epok, iterera genom samtliga träningsuppsättningar: `for (std::size_t x{}; x < myTrainSetCount; ++x)`.
* För varje träningsuppsättning `x`, genomför följande tre steg:
    1. **Feedforward:** Anropa `predict(myTrainInput[x])`. Detta genomför feedforward genom både det dolda lagret och utgångslagret.
    2. **Backpropagation:**
        * Beräkna felet i utgångslagret: `myOutputLayer.backpropagate(myTrainOutput[x])`.
        * Beräkna felet i det dolda lagret utifrån utgångslagrets fel och vikter: `myHiddenLayer.backpropagate(myOutputLayer)`.
    3. **Optimering:**
        * Optimera det dolda lagret: `myHiddenLayer.optimize(myTrainInput[x], learningRate)`.
        * Optimera utgångslagret utifrån det dolda lagrets output: `myOutputLayer.optimize(myHiddenLayer.output(), learningRate)`.

**Returvärde:** `true` efter genomförd träning.

---

### 2. Kompilering och test
Uppdatera `main.cpp` så att `train()` anropas (med valfritt antal epoker samt lärhastighet) innan prediktion genomförs. Kontrollera returvärdet från `train()`: Skriv ut ett felmeddelande och avsluta programmet med en felkod om träningen misslyckas.

Fortsätt använda `ml::dense_layer::Stub` för det dolda lagret och utgångslagret tills en skarp dense-lagerimplementation finns (se **L08–L09**).

Kompilera och testkör programmet. Bli inte oroliga om nätverket inte predikterar korrekt; dense-lagren är fortfarande stubbar och tränar därmed inte på riktigt. Det viktiga är att koden kompilerar och körs utan fel; en skarp implementation av `Dense` läggs till under **L08–L09**.

---
