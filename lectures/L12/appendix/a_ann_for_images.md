# Bilaga A - Varför inte mata en bild direkt till ett vanligt neuralt nätverk?
Ett vanligt neuralt nätverk (bestående enbart av dense-lager) går rent tekniskt att använda för att bearbeta bilder, men i praktiken fungerar det sällan bra. Nedan går vi igenom varför, och vad konvolutionella neurala nätverk (CNN) gör annorlunda.

---

### Rumslig struktur går förlorad
En bild är två- eller tredimensionell; varje pixel har ett naturligt förhållande till sina grannpixlar:
* Ska bilden matas in i ett vanligt neuralt nätverk måste den först plattas ut till en endimensionell vektor och då försvinner informationen om vilka pixlar som satt bredvid varandra.
* Nätverket ser bara en lång lista av lösryckta tal och måste, om det ska fungera alls, lära sig alla rumsliga samband på egen hand via sina vikter.

Detta gör inlärningen betydligt svårare än den behöver vara.

---

### Antalet parametrar exploderar
Säg att vi vill prediktera innehållet i en bild på 1024 × 1024 pixlar:
* Plattas bilden ut och matas rakt in i ett dense-lager behöver nätverket en input per pixel, det vill säga `1024 * 1024 = 1 048 576` inputs, och en egen vikt per pixel och nod i första dolda lagret.
* Redan ett enda dolt lager med 1000 noder skulle då kräva över en miljard vikter, innan vi ens lagt till fler lager, vilket oftast behövs för att hantera bilddatans komplexitet.

Detta hade medfört ett nätverk som är stort, minneskrävande och långsamt att både träna och köra.

---

### Generaliseringen blir sämre
Eftersom varje pixel behandlas som en helt egen input förstår nätverket inte att samma mönster kan dyka upp på olika ställen i bilden; det måste lära sig känna igen mönstret på nytt för varje position det förekommer på. Det innebär att nätverket:
* Kräver mer träningsdata.
* Riskerar att lära sig träningsbilderna utantill i stället för det som faktiskt är relevant.
* Blir sämre på att känna igen samma sak i nya bilder som ser lite annorlunda ut.

I värsta fall fungerar nätverket bara bra på bilder som ser ut nästan exakt som träningsdatan.

---

### Vad CNN gör i stället
Konvolutionella neurala nätverk är byggda specifikt för bilddata. Genom att
* bevara bildens rumsliga struktur,
* använda små lokala filter (kernels) samt
* dela samma vikter över hela bilden,
blir CNN samtidigt mindre, snabbare att träna och bättre på att känna igen mönster oavsett var i bilden de dyker upp.

---
