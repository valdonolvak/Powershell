## Peatükk 3: Failisüsteemi haldus

### 1. Navigeerimine: Kus ma olen?

PowerShellis liigume failisüsteemis sarnaselt vanale CMD-le, kuid käskude taga on tegelikult võimsad cmdletid.

* `Get-Location` (alias: `pwd`) – Näitab praegust asukohta.
* `Set-Location` (alias: `cd`) – Muudab asukohta (nt `cd C:\Windows`).

### 2. Sisu vaatamine: `Get-ChildItem` (alias: `ls` või `dir`)

See on üks enimkasutatavaid käske. See loetleb faile ja kaustu.

* `Get-ChildItem -Recurse` – Vaatab ka kõikide alamkaustade sisse.
* `Get-ChildItem -Filter *.txt` – Näitab ainult tekstifaile.

### 3. Elementide loomine ja muutmine: `New-Item`

PowerShell kasutab failide ja kaustade jaoks ühte käsku, määrates ära tüübi (`ItemType`).

* **Kausta loomine:** `New-Item -Path "C:\Test" -ItemType Directory`
* **Faili loomine:** `New-Item -Path "C:\Test\test.txt" -ItemType File`

### 4. Kopeerimine, liigutamine ja kustutamine

* `Copy-Item` – Kopeerib faili või kausta.
* `Move-Item` – Liigutab faili või kausta (kasutatakse ka nime muutmiseks).
* `Remove-Item` – Kustutab (parameeter `-Recurse` kustutab kausta koos sisuga).

### 5. Faili sisu lugemine ja kirjutamine

* `Get-Content` – Loeb faili sisu (nt logifaili vaatamine).
* `Add-Content` – Lisab teksti faili lõppu (ei kustuta vana sisu).
* `Set-Content` – Kirjutab faili sisu üle.

---

### Iseseisvad harjutused (Failisüsteem)

1. **Harjutus: Navigeerimine.** Liigu oma kasutajakausta `Downloads` (Allalaadimised) ja kuva sealne sisu. Sorteeri failid viimase muutmise aja (`LastWriteTime`) järgi.
2. **Harjutus: Peitusemäng.** Otsi `C:\Windows` kaustast (ilma alamkaustadeta) üles kõik failid, mille laiend on `.log`.
3. **Harjutus: Struktuur.** Loo käsu abil oma töölauale kaust nimega `IT_Ope` ja selle sisse tühi fail nimega `markmed.txt`.
4. **Harjutus: Sisu muutmine.** Lisa eelmises harjutuses loodud faili `markmed.txt` rida "Peatükk 3 on läbitud" ilma faili algset sisu (kui seal midagi oleks) kustutamata.

---

### Iseseisev praktiline töö: "Automaatne arhiveerija"

**Ülesanne:** Koosta skript, mis simuleerib lihtsat logide haldamise süsteemi. Skript peab tegema läbi terve faili elutsükli.

**Töö käik ja nõuded:**

1. **Keskkonna ettevalmistus:** Loo oma kettale (nt `C:\Temp\` või kodukataloogi) kaust nimega `LogiHaldus`.
2. **Failide genereerimine:** Loo selles kaustas kolm faili: `log1.txt`, `log2.txt` ja `vana_pilt.jpg`.
3. **Sisu lisamine:** Kirjuta failidesse `log1.txt` ja `log2.txt` tänane kuupäev ja kellaaeg (kasuta selleks muutujat või käsku `Get-Date`).
4. **Sorteerimine ja puhastus:** * Tuvasta ja kustuta fail `vana_pilt.jpg` (kuna see pole logifail).
* Loo alamkaust nimega `Arhiiv`.
* Liiguta kõik allesjäänud `.txt` failid kausta `Arhiiv`.


5. **Kontroll:** Kuva ekraanile kausta `Arhiiv` sisu ja failide kogusuurus.

**Esitamiseks:**

* Esita skript (`.ps1` failina või koodiblokina).
* Lisa kommentaarid: Selgita koodis, mis vahe on `Set-Content` ja `Add-Content` käskudel.
* Esita screenshot, kus on näha `LogiHaldus\Arhiiv` kausta sisu pärast skripti käivitamist.

---

**Nõuanne:** Failisüsteemiga töötades ole alati ettevaatlik käsuga `Remove-Item`. PowerShell ei küsi vaikimisi kinnitust (või saadab selle prügikasti asemel jäädavalt minema). 
Kasuta testimiseks parameetrit `-WhatIf`, et näha, mida käsk teeks ilma seda tegelikult tegemata!
