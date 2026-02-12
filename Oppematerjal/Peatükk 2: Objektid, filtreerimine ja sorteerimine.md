Kui esimene peatükk õpetas meid "rääkima", siis teine peatükk õpetab meid "mõtlema" nagu PowerShell. See on koht, kus tavalisest käsureast saab võimas programmeerimistööriist.

---

## Peatükk 2: Objektid, massiivid ja filtreerimine

### 1. Mis on objekt? (Andmed vs. Objektid)

Tavalises käsureas (CMD) on väljund lihtsalt tekst. PowerShellis on aga iga asi **objekt**.
Kujuta ette autot:

* **Omadused (Properties):** Värv, mudel, mootori võimsus.
* **Meetodid (Methods):** Käivita, pidurda, lülita tuled sisse.

Kui küsid PowerShellilt protsessi (`Get-Process`), ei anna ta sulle lihtsalt nime, vaid terve "objekti", millel on mälukasutus, ID ja omanik.

### 2. Massiivid (Arrays)

Massiiv on muutuja, mis hoiab **mitut objekti korraga**. See on nagu nimekiri. PowerShellis algavad kõik muutujate nimed **$** märgiga.

```powershell
# Massiivi loomine
$linnad = "Tallinn", "Tartu", "Pärnu"

# Massiivi liikme poole pöördumine (lugemine algab 0-st!)
$linnad[0] # Väljastab: Tallinn

```

### 3. Filtreerimine: `Where-Object`

Kuna käsud tagastavad tihti sadu objekte, peame neid piirama. Selleks kasutame võrdlusoperaatoreid:

* `-eq` (equal) – võrdne
* `-ne` (not equal) – mittevõrdne
* `-gt` (greater than) – suurem kui
* `-lt` (less than) – väiksem kui
* `-like` – sarnane (kasutatakse metsikuid kaarte nagu `*`)

**Näide:** Leia protsessid, mis kasutavad rohkem kui 500 MB mälu:

```powershell
Get-Process | Where-Object {$_.WorkingSet -gt 500MB}

```

> `$_.` tähistab "käesolevat objekti", mis parajasti torustikus liigub.

### 4. Sorteerimine ja valimine

* **`Sort-Object`**: Seab andmed ritta.
* **`Select-Object`**: Valib välja ainult need "veerud", mida soovid näha.

**Näide:** Kuva teenused, sorteeri need nime järgi ja näita ainult nime ja staatust:

```powershell
Get-Service | Sort-Object Name | Select-Object Name, Status

```

---

### Praktilised näidiskäsud

#### A. Massiiviga manipuleerimine

```powershell
$teenused = Get-Service # Salvestame kõik teenused massiivi
$teenused.Count        # Näitab, mitu teenust nimekirjas on
$teenused[0..4]        # Näitab viit esimest teenust nimekirjas

```

#### B. Täpne filtreerimine teksti abil

```powershell
# Leia kõik teenused, mille nimes esineb "Xbox"
Get-Service | Where-Object {$_.DisplayName -like "*Xbox*"}

```

#### C. Objektide "sisemusse" vaatamine

Kui soovid teada, milliseid omadusi (Properties) saad üldse filtreerida või sorteerida, kasuta `Get-Member`.

```powershell
Get-Process | Get-Member -MemberType Property

```

---

### Iseseisvad harjutused

1. **Harjutus: Massiivi loomine.** Loo muutuja `$arvutid`, mis sisaldab kolme arvuti nime (nt "Server01", "Klient01", "Laptop"). Kuva ekraanile neist teine (indeksiga 1).
2. **Harjutus: Protsesside mälukasutus.** Leia kõik protsessid, mille nimi on "svchost" ja sorteeri need ID järgi kasvavalt.
3. **Harjutus: Teenuste staatus.** Kasuta massiivi ja filtreerimist, et leida kõik teenused, mis on seadistatud automaatselt käivituma (`StartType -eq 'Automatic'`), aga mis hetkel **ei tööta** (`Status -ne 'Running'`).
4. **Harjutus: Omandite leidmine.** Kasuta `Get-Member` käsku protsessi peal (`Get-Process | Get-Member`). Leia, mis on selle omaduse nimi, mis näitab protsessi algusaega.

---

## Iseseisev praktiline töö: "Ressursiinspektor"

**Ülesanne:** Koosta skript, mis analüüsib arvuti mälu- ja kettakasutust ning väljastab korrastatud raporti.

**Töö käik ja nõuded:**

1. **Mälukasutus:** Leia 10 kõige rohkem mälu (WorkingSet) kasutavat protsessi.
2. **Vormistamine:** Vali väljundisse ainult protsessi nimi, ID ja mälukasutus. Sorteeri need mälukasutuse järgi kahanevalt.
3. **Ketta info:** Kasuta käsku `Get-Volume`. Filtreeri välja ainult need kettad, mille tüüp on "Fixed" (tavaliselt kõvakettad).
4. **Väljund:** Ühenda mälukasutuse ja kettakasutuse info ning salvesta see faili `Ressursi_Raport.txt`.

**Esitamiseks:**

* Skripti kood ja selgitus, kuidas kasutasid `Where-Object` ja `Sort-Object` käske.
* Screenshot faili `Ressursi_Raport.txt` sisust.

---
