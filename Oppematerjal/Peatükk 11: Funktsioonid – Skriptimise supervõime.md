Kui sa oled seni kirjutanud skripte "ülevalt alla" pika joru koodina, siis **funktsioonid** on see hetk, kus sa muutud *skriptijast* *programmeerijaks*.

Funktsioonid teevad sinu koodi puhtaks, korduvkasutatavaks ja professionaalseks.

---

# Peatükk 11: Funktsioonid – Skriptimise supervõime

### 1. Mis on funktsioon ja miks seda vaja on?

Kujuta ette, et sa oled kokk.

* **Ilma funktsioonideta skript** on nagu kokaraamat, kus iga kord, kui on vaja muna praadida, on kirjutatud uuesti välja kogu pikk õpetus: "Võta pann, pane pliidile, lisa õli, löö muna katki...".
* **Funktsioon** on nagu "kiirkäsklus". Sa defineerid ühe korra tegevuse nimega "Prae-Muna". Edaspidi kirjutad retseptis lihtsalt: `Prae-Muna`.

**IT-maailmas tähendab see:**
Kui sul on koodijupp, mida kasutad rohkem kui üks kord (näiteks logi kirjutamine või parooli genereerimine), siis ära kopeeri seda mitu korda (Copy-Paste on kurjast!). Pane see funktsiooni sisse.

**Funktsioonide eelised:**

1. **Loetavus:** Sinu põhikood on lühike ja arusaadav.
2. **Muudetavus:** Kui tahad midagi muuta (nt logimise formaati), muudad seda vaid ühes kohas (funktsiooni sees), mitte kümnes kohas üle terve skripti.
3. **Vähem vigu:** Mida vähem koodi sa kopeerid, seda vähem vigu sisse lipsab.

---

### 2. Funktsiooni anatoomia PowerShellis

Funktsiooni loomine koosneb kolmest osast:

1. Märksõna `function`.
2. Nimi (Soovitatav on kasutada `Tegusõna-Nimisõna` formaati, nt `Kirjuta-Logi`).
3. Sisu `{ ... }`.

**Kõige lihtsam näide:**

```powershell
function Tervita-Maailma {
    Write-Host "Tere tulemast PowerShelli funktsioonide maailma!" -ForegroundColor Green
}

# Funktsiooni väljakutsumine (NB! Ilma sulgudeta!)
Tervita-Maailma

```

---

### 3. Parameetrid – Funktsiooni "kütus"

Funktsioon ilma parameetriteta on igav – ta teeb alati täpselt ühte asja. Parameetrid võimaldavad anda funktsioonile infot, millega tööd teha.

Kasutame plokki `param(...)`.

**Näide: Kohandatud tervitus**

```powershell
function Tervita-Kasutajat {
    param (
        [string]$Nimi,       # Ootame teksti
        [string]$Osakond     # Ootame teksti
    )

    Write-Host "Tere, $Nimi! Sinu osakond on $Osakond."
}

# Väljakutsumine (NB! Tühikuga, mitte komadega!)
Tervita-Kasutajat -Nimi "Jaan" -Osakond "IT"

```

> **HOIATUS ALGAJATELE:**
> * **VALE:** `Tervita-Kasutajat("Jaan", "IT")` (See on C# või Pythoni stiil)
> * **ÕIGE:** `Tervita-Kasutajat -Nimi "Jaan" -Osakond "IT"` (See on PowerShelli stiil)
> 
> 

---

### 4. Tagastusväärtus (`Return`) vs. Ekraanile kirjutamine

See on koht, kus 90% algajatest eksib.

* `Write-Host`: See on mõeldud **inimesele**. See paneb teksti ekraanile. Arvuti ei saa selle tekstiga midagi edasi teha.
* `Return` (või lihtsalt väljund): See on mõeldud **arvutile/skriptile**. See saadab andmed tagasi, et saaksid need muutujasse salvestada.

**Näide: Matemaatika**

```powershell
function Arvuta-Ruut {
    param ([int]$Number)
    
    $Tulemus = $Number * $Number
    return $Tulemus  # Saadame numbri tagasi skriptile
}

# Õige kasutamine:
$Vastus = Arvuta-Ruut -Number 5
# Nüüd on $Vastus väärtus 25 ja saame sellega edasi arvutada
$UusVastus = $Vastus + 5 
Write-Host "Lõpptulemus on: $UusVastus"

```

Kui oleksime kasutanud `Write-Host`, oleks ekraanile ilmunud "25", aga muutuja `$Vastus` oleks tühi!

---

### 5. Täiustatud funktsioonid (Advanced Functions)

Et sinu funktsioon käituks nagu päris PowerShelli käsk (toetaks `-Verbose`, `-ErrorAction` jne), lisame talle maagilise rea `[CmdletBinding()]`.

**Näide: Logimise funktsioon (Mida kasutasime eelmises peatükis)**

```powershell
function Kirjuta-Logisse {
    [CmdletBinding()] # See teeb funktsiooni "targaks"
    param (
        [Parameter(Mandatory=$true)] # Nõuab, et kasutaja peab sõnumi sisestama
        [string]$Sonum,

        [string]$Failitee = "C:\Temp\script.log" # Vaikimisi väärtus
    )

    $Aeg = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $LogiRida = "[$Aeg] $Sonum"
    
    Add-Content -Path $Failitee -Value $LogiRida
    
    # Kui kasutaja käivitab käsuga -Verbose, näeb ta seda ka ekraanil
    Write-Verbose "Logisse kirjutatud: $LogiRida"
}

# Kasutamine:
Kirjuta-Logisse -Sonum "Skript käivitus"
Kirjuta-Logisse -Sonum "Viga andmebaasis" -Failitee "C:\Temp\errors.log" -Verbose

```

---

### Iseseisvad harjutused (Funktsioonid)

1. **Harjutus: Lihtne matemaatika.** Kirjuta funktsioon `Arvuta-Pindala`, mis võtab parameetriteks `Pikkus` ja `Laius` ning tagastab (return) ristküliku pindala. Katseta seda, salvestades tulemuse muutujasse.
2. **Harjutus: Värviline teade.** Kirjuta funktsioon `Kirjuta-Hoiatus`, mis võtab parameetriks `Tekst`. Funktsioon peab kuvama selle teksti ekraanil kollaselt ja lisama ette sõna "TÄHELEPANU: ".
3. **Harjutus: AD Kasutaja kontroll.** (Vajab AD moodulit). Kirjuta funktsioon `Kontrolli-Kasutajat`, mis võtab parameetriks `Kasutajanimi`.
* Kui kasutaja on AD-s olemas, tagasta "OLEMAS".
* Kui kasutajat pole, tagasta "PUUDUB".
* Kasuta `Try-Catch` ja `Get-ADUser` käsku.



---

### Iseseisev praktiline töö: "Tööriistakast"

**Ülesanne:** Sinu ülesanne on luua oma isiklik PowerShelli moodul (või lihtsalt skript failiga), mis sisaldab kolme kasulikku funktsiooni, mida saad tulevikus kasutada.

**Funktsioon 1: `Get-KettaInfo**`

* Ei võta parameetreid.
* Tagastab info C: ketta kohta: Vaba ruum (GB) ja Kogumaht (GB).

**Funktsioon 2: `New-BackupFolder**`

* Võtab parameetriks `KaustaTee` (nt "C:\Temp").
* Loob sinna sisse uue kausta nimega "Backup_[Kuupäev]" (nt `Backup_20231025`).
* Tagastab loodud kausta täistee.

**Funktsioon 3: `Kirjuta-Raport**`

* Võtab parameetriks `Sisu` ja `Failinimi`.
* Kirjutab sisu HTML-formaadis faili (lisa `<h1>` jne tagid ümber).

**Lõppskript:**
Pane need kolm funktsiooni skripti algusesse ja skripti põhiosas kasuta neid kõiki järjest, et:

1. Saada ketta info.
2. Luua varukoopia kaust.
3. Kirjutada ketta info HTML raportina sinna uude varukoopia kausta.

**Esitamiseks:**

* Skriptifail (`.ps1`), kus on funktsioonide definitsioonid ja nende väljakutsumine.

---
