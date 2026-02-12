See on PowerShelli programmeerimise süda. Kui seni kasutasid käske ühekaupa, siis nüüd õpime, kuidas panna skript ise otsuseid tegema ja korduvaid tegevusi sooritama.

---

## Peatükk 4: Muutujad, massiivid ja tsüklid

### 1. Muutujad (Variables)

Muutuja on nagu "mälukarp", kuhu saad andmed hoiule panna, et neid hiljem kasutada. PowerShellis algavad kõik muutujad **$** märgiga.

* **Loomine:** `$nimi = "Juhan"`
* **Kasutamine:** `Write-Host "Tere, $nimi"`
* **Objekti salvestamine:** Võid salvestada terve käsu väljundi muutujasse:
`$teenused = Get-Service` (Nüüd on kõik teenused muutujas `$teenused`).

**Koodinäide:**

```powershell
# Teksti salvestamine
$nimi = "Juhan"

# Arvu salvestamine ja arvutamine
$a = 10
$b = 5
$summa = $a + $b

# Objekti salvestamine (terve käsu väljund läheb muutujasse)
$teenused = Get-Service

```

### 2. Massiivid (Arrays)

Massiiv on muutuja, mis hoiab **mitut väärtust korraga**. See on nagu nimekiri.

**Koodinäide:**

```powershell
# Massiivi loomine
$it_osakond = "Tanel", "Igor", "Teet"

# Liikme poole pöördumine (lugemine algab 0-st!)
$it_osakond[0] # Väljastab: Tanel

# Massiivi liikmete arv
$it_osakond.Count # Väljastab: 3

```



 ----
### 2. Massiivid (Arrays)

Massiiv on muutuja, mis hoiab **mitut väärtust korraga**. See on nagu nimekiri.

* **Loomine:** `$numbrid = 1, 2, 3, 4, 5` või `$nimed = @("Anni", "Bert", "Cadi")`
* **Elemendi poole pöördumine:** PowerShell alustab lugemist nullist!
`$nimed[0]` on Anni.
`$nimed[1]` on Bert.
* **Mitu elementi on massiivis?** `$nimed.Count`

---

### 3. Tsüklid (Loops) – Tegevuste kordamine

Tsükkel on skripti osa, mis kordab tegevust seni, kuni tingimus on täidetud või nimekiri saab otsa.

#### A. `foreach` tsükkel (Kõige populaarsem)

Seda kasutatakse massiivi läbimiseks. See tähendab: "Võta see nimekiri ja tee iga elemendiga midagi".

**Struktuur:**

```powershell
foreach ($üksik_asi in $nimekiri) {
    # Tegevus selle üksiku asjaga
}

```

**Näide:**

```powershell
$linnad = "Tallinn", "Tartu", "Pärnu"
foreach ($linn in $linnad) {
    Write-Host "Tere tulemast linna nimega $linn!" -ForegroundColor Green
}

```

#### B. `for` tsükkel (Loendamine)

Kasutatakse siis, kui tead täpselt, mitu korda soovid midagi teha.

```powershell
for ($i = 1; $i -le 5; $i++) {
    Write-Host "See on kordus number $i"
}

```

#### C. `while` tsükkel (Kordus kuni...)

Kordab tegevust nii kaua, kuni tingimus on tõene.

```powershell
$number = 1
while ($number -lt 5) {
    Write-Host "Number on $number"
    $number++ # Suurendab numbrit ühe võrra, muidu jääbki tsükkel käima!
}

```

---

### Õppematerjal: Kuidas valida õiget tsüklit?

* Kas sul on nimekiri (failid, teenused, nimed)? Kasuta **foreach**.
* Kas sa pead midagi tegema kindel arv kordi (nt 10 korda)? Kasuta **for**.
* Kas sa ootad mingit sündmust (nt ootad, kuni fail ilmub kausta)? Kasuta **while**.

---

### Iseseisvad harjutused (Tsüklid ja massiivid)

1. **Harjutus: Nimede massiiv.** Loo massiiv viie puuvilja nimega. Kirjuta `foreach` tsükkel, mis ütleb iga puuvilja kohta: "Mulle maitseb [puuvilja nimi]".
2. **Harjutus: Matemaatika.** Kasuta `for` tsüklit, et kuvada arvud 1-st 10-ni, aga iga arvu juures kuva ka selle arvu ruut (arv * arv).
3. **Harjutus: Failide loomine.** Loo massiiv nimedega `test1`, `test2`, `test3`. Kasuta `foreach` tsüklit, et luua iga nimega tühi `.txt` fail.
4. **Harjutus: Teenuste kontroll.** Salvesta muutujasse kõik seisvad teenused (`Get-Service | Where Status -eq 'Stopped'`). Kasuta `foreach` tsüklit, et kuvada ekraanile ainult nende teenuste nimed punase värviga.

---

### Iseseisev praktiline töö: "Kasutajate ja failide masshaldus"

**Ülesanne:** Administraator peab looma uutele töötajatele kaustad ja tervitusfailid. Sinu ülesanne on see protsess automatiseerida.

**Töö käik ja nõuded:**

1. **Andmete ettevalmistus:** Loo massiiv `$tootajad`, kuhu märgi vähemalt 5 nime (nt "Kadi", "Mati", "Tiina" jne).
2. **Kaustade loomine:** Kirjuta tsükkel, mis loob iga töötaja nimega kausta kataloogi `C:\IT_Haldus\Kasutajad\`.
* *Kontroll:* Lisa tsükli sisse kontroll, et kui kaust on juba olemas, siis seda uuesti ei looda (kasuta `if (-not (Test-Path ...))`).


3. **Tervitusfailid:** Iga töötaja kausta sisse peab tsükkel looma faili `terve_tulemast.txt`.
4. **Sisu lisamine:** Faili sisu peab olema personaalne: "Tere, [TöötajaNimi]! Sinu konto on loodud [Kuupäev]".
5. **Kokkuvõte:** Pärast tsüklit peab skript väljastama teate: "Töö lõpetatud. Loodi [massiivi pikkus] kasutaja keskkonda."

**Esitamiseks õpetajale:**

1. Skripti täielik kood (`.ps1` failina).
2. Selgitus: Miks kasutasid just seda tüüpi tsüklit (foreach, for või while)?
3. Screenshot `C:\IT_Haldus\Kasutajad\` kaustast, kus on näha loodud kataloogid.
4. Screenshot ühe töötaja tervitusfaili sisust (et oleks näha personaalne nimi ja kuupäev).

---

**Õpetaja märkus:** Pööra tähelepanu koodi vormistusele (taandread ehk *indentation*). Kõik, mis jääb loogeliste sulgude `{ }` vahele, peaks olema nihutatud paremale – nii on kood loetav!
