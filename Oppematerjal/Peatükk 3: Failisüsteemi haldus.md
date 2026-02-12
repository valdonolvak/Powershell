## Peatükk 3: Failisüsteemi haldus süvitsi

Kui seni oskasid faile lihtsalt vaadata, siis selles peatükis õpime neid looma, kopeerima, "süvitsi" otsima ja ohutult haldama. PowerShellis on failisüsteem justkui suur andmebaas, kus igal failil on oma omadused (suurus, muutmisaeg, laiend).

### 1. Navigeerimine: Kus ma olen?

PowerShellis liigume failisüsteemis sarnaselt vanale CMD-le, kuid käskude taga on tegelikult võimsad cmdletid.

* `Get-Location` (alias: `pwd`) – Näitab praegust asukohta.
* `Set-Location` (alias: `cd`) – Muudab asukohta.

**Koodinäide:**

```powershell
# Liigu Windowsi kausta
Set-Location -Path "C:\Windows"

# Liigu tagasi oma kasutaja kodukausta
Set-Location -Path $home

```

### 2. Sisu vaatamine ja otsimine: `Get-ChildItem`

See on üks enimkasutatavaid käske. Parameeter **`-Recurse`** on administraatori parim sõber – see vaatab läbi kõik alamkaustad.

**Koodinäide:**

```powershell
# Sisu vaatamine (ainult praegune kaust)
Get-ChildItem

# Süvitsi otsimine: Leia kõik .log failid Windowsi kaustast
Get-ChildItem -Path "C:\Windows" -Filter "*.log" -Recurse -ErrorAction SilentlyContinue

# Filtreerimine suuruse järgi: Leia failid, mis on suuremad kui 50MB
Get-ChildItem -Path "C:\Temp" -Recurse | Where-Object {$_.Length -gt 50MB}

```

### 3. Elementide loomine: `New-Item`

PowerShell kasutab failide ja kaustade jaoks ühte käsku, määrates ära tüübi (`ItemType`).

**Koodinäide:**

```powershell
# Kausta loomine
New-Item -Path "C:\Labor" -ItemType Directory

# Faili loomine koos esmase tekstiga
New-Item -Path "C:\Labor\test.txt" -ItemType File -Value "See on faili esimene rida."

```

### 4. Faili sisu lugemine ja kirjutamine

* `Get-Content` – Loeb faili sisu.
* `Add-Content` – Lisab teksti lõppu (ei kustuta vana sisu).
* `Set-Content` – Kirjutab sisu üle.

**Koodinäide:**

```powershell
# Faili sisu vaatamine
Get-Content -Path "C:\Labor\test.txt"

# Uue rea lisamine (kasulik logimiseks)
Add-Content -Path "C:\Labor\test.txt" -Value "Kasutaja logis sisse kell $(Get-Date)"

# Kogu faili asendamine uue tekstiga
Set-Content -Path "C:\Labor\test.txt" -Value "Faili sisu on nüüd täielikult muudetud."

```

### 5. Kopeerimine, liigutamine ja kustutamine

* `Copy-Item` – Kopeerib faili või kausta.
* `Move-Item` – Liigutab faili või muudab nime.
* `Remove-Item` – Kustutab (parameeter `-Recurse` kustutab ka kausta sisu).

**Koodinäide:**

```powershell
# Faili kopeerimine uude asukohta
Copy-Item -Path "C:\Labor\test.txt" -Destination "C:\Labor\test_koopia.txt"

# Faili nime muutmine (sisuliselt liigutamine uue nimega samas kaustas)
Move-Item -Path "C:\Labor\test_koopia.txt" -Destination "C:\Labor\arhiiv_test.txt"

# Faili kustutamine (kasutades turvameedet -WhatIf)
Remove-Item -Path "C:\Labor\arhiiv_test.txt" -WhatIf

```

---

### Iseseisvad harjutused (Failisüsteem)

1. **Harjutus: Navigeerimine.** Liigu oma kasutajakausta `Downloads` ja kuva sealne sisu. Sorteeri failid viimase muutmise aja (`LastWriteTime`) järgi nii, et uusimad on eespool.
2. **Harjutus: Süvitsi otsing.** Otsi `C:\Windows` kaustast (koos alamkaustadega) üles kõik failid, mille laiend on `.exe` ja mis on suuremad kui 10MB.
3. **Harjutus: Struktuur.** Loo käsu abil oma töölauale kaust `IT_Ope` ja selle sisse alamkaustad `Raportid` ja `Arhiiv`. Loo kausta `Raportid` sisse tühi fail `seisund.txt`.
4. **Harjutus: Sisu muutmine.** Lisa faili `seisund.txt` tekst "Süsteem kontrollitud" ja uuele reale praegune kuupäev, kasutades `Add-Content`.

---

### Iseseisev praktiline töö: "Automaatne arhiveerija"

**Ülesanne:** Koosta skript, mis simuleerib lihtsat logide haldamise süsteemi ja failide elutsüklit.

**Töö käik ja nõuded:**

1. **Keskkonna ettevalmistus:** Loo kaust `C:\Temp\LogiHaldus`.
2. **Failide genereerimine:** Loo selles kaustas kolm faili: `log1.txt`, `log2.txt` ja `vana_pilt.jpg`.
3. **Sisu lisamine:** Kirjuta failidesse `log1.txt` ja `log2.txt` tänane kuupäev ja kellaaeg (kasuta `Get-Date`).
4. **Sorteerimine ja puhastus:**
* Tuvasta ja kustuta fail `vana_pilt.jpg`.
* Loo alamkaust nimega `Arhiiv`.
* Liiguta kõik allesjäänud `.txt` failid kausta `Arhiiv`.


5. **Aruandlus:** Loo kausta `LogiHaldus` fail `tegevused.log` ja lisa sinna kirje: "Arhiveerimine lõpetatud: [Mitu faili liigutati] faili liigutatud asukohta Arhiiv".

**Esitamiseks:**

* Esita skript (`.ps1` failina).
* Lisa kommentaarid koodi sisse: Selgita oma sõnadega, mis vahe on `Set-Content` ja `Add-Content` käskudel.
* Esita screenshot, kus on näha `LogiHaldus\Arhiiv` kausta sisu pärast skripti käivitamist.

---

**Nõuanne:** Failisüsteemiga töötades ole alati ettevaatlik käsuga **`Remove-Item`**. PowerShell ei küsi vaikimisi kinnitust ja kustutab failid jäädavalt (mitte prügikasti).
Kasuta testimiseks parameetrit **`-WhatIf`**, et näha, mida käsk teeks ilma seda tegelikult tegemata!
*Näide: `Remove-Item "C:\TähtisKaust" -Recurse -WhatIf*`

---
