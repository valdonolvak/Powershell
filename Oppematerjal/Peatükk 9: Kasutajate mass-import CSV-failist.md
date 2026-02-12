# Peatükk 9: Kasutajate nutikas mass-import CSV-failist

Selles peatükis õpime, kuidas luua professionaalne skript, mis suudab andmeid lugeda välisest failist, kontrollida süsteemi olekut ning jätta oma tegevustest maha korrektse logi.

### 1. CSV-faili struktuur

CSV on skripti "toit". Selleks, et PowerShell oskaks andmeid lugeda, peavad päised (First row) ühtima skriptis kasutatavate muutujatega.

**Näidis `uued_tootajad.csv`:**

```csv
Eesnimi,Perenimi,Kasutajatunnus,Osakond,Parool,Staatus
Kati,Kask,kkask,IT,Tervitus2026!,True
Mati,Mänd,mmand,Turundus,Kevad2026?,True

```

### 2. Kuidas lugeda CSV-faili rida-realt?

Andmete importimiseks kasutame käsku `Import-Csv`. Selleks, et iga rida eraldi töödelda, kasutame `foreach` tsüklit. Tsükli sees tähistab muutuja (nt `$Rida`) parajasti käsil olevat rida.

**Koodinäide:**

```powershell
# 1. Loeme kogu faili muutujasse
$KasutajateNimekiri = Import-Csv -Path "C:\Temp\uued_tootajad.csv"

# 2. Töötleme iga rida eraldi
foreach ($Rida in $KasutajateNimekiri) {
    # Nüüd saame pöörduda veergude poole kasutades punkti (.)
    Write-Host "Töötlen kasutajat: $($Rida.Eesnimi) $($Rida.Perenimi)"
    Write-Host "Tema kasutajatunnus on: $($Rida.Kasutajatunnus)"
}

```

### 3. Kontroll: Kas OU on olemas?

Enne kasutaja loomist peame veenduma, et sihtkoht (OU) eksisteerib. Kui ei eksisteeri, loome selle.

**Koodinäide:**

```powershell
$OU_Tee = "OU=IT,DC=kool,DC=local"

if (-not (Get-ADOrganizationalUnit -Filter "DistinguishedName -eq '$OU_Tee'")) {
    New-ADOrganizationalUnit -Name "IT" -Path "DC=kool,DC=local"
    Write-Host "OU loodi edukalt."
}

```

### 4. Kontroll: Kas kasutaja on olemas?

Selleks, et skript ei viskaks punast veateadet, kontrollime kasutajatunnust (`SamAccountName`).

**Koodinäide:**

```powershell
$Tunnus = "kkask"

if (-not (Get-ADUser -Filter "SamAccountName -eq '$Tunnus'")) {
    # Siia tuleb kasutaja loomise käsk
    Write-Host "Kasutajat pole, võime luua."
} else {
    Write-Host "Kasutaja on juba olemas, liigume edasi."
}

```

### 5. Logimine: Kuidas kirjutada ajalugu?

Logifaili kirjutamiseks kasutame `Add-Content` ja kellaaja jaoks `Get-Date`.

**Koodinäide:**

```powershell
$Aeg = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
$LogiRida = "[$Aeg] LOODI KASUTAJA: kkask"
Add-Content -Path "C:\Temp\import.log" -Value $LogiRida

```

### 6. Parool ja Staatus

AD vajab parooli `SecureString` vormingus ja staatust `boolean` (tõeväärtus) tüübina.

**Koodinäide:**

```powershell
$SecurePW = ConvertTo-SecureString "Tervitus2026!" -AsPlainText -Force
$IsActive = [bool]"True"

```

---

### Iseseisvad harjutused õpilasele

1. **Harjutus: Tsükli testimine.** Koosta CSV-fail kolme nimega. Kirjuta `foreach` tsükkel, mis väljastab iga nime kohta teate: "Kasutaja [Eesnimi] on impordiks valmis".
2. **Harjutus: OU detektiiv.** Kirjuta `if-else` lause, mis kontrollib OU "Muuk" olemasolu. Kui see on olemas, kirjuta ekraanile "Konteiner leitud", kui mitte, siis "Konteiner puudu".
3. **Harjutus: Veateate püüdmine.** Kasuta `Try-Catch` blokki koodis, mis üritab lugeda olematut faili. Kirjuta veateade faili `error.log`.
4. **Harjutus: Kellaaja vorming.** Koosta muutuja, mis hoiab kellaaega vormingus "PP.KK.AAAA TT:MM" ja väljasta see ekraanile.

---

### Iseseisev praktiline töö: "Vigadeta Mass-import"

**Ülesanne:** Koosta täielik skript, mis loeb `uued_tootajad.csv` faili ja teeb järgmist:

1. Käib `foreach` tsükliga läbi kõik CSV-faili read.
2. Kontrollib ja loob vajadusel puuduva **OU**.
3. Kontrollib, ega **kasutaja** pole juba AD-s olemas.
4. Loob kasutaja õigesse OU-sse, määrab parooli ja staatuse.
5. Kirjutab **iga õnnestumise ja vea** kohta logifaili `C:\Temp\import_raport.log` koos kuupäeva ja kellaajaga.

```

**Esitamiseks õpetajale:**

* Valmis skript `.ps1` failina.
* Töö tulemusena tekkinud logifail.
* Selgitus: Mis on `foreach ($Rida in $Kasutajad)` lause roll selles skriptis?

---

**Kas soovid, et lisaksime siia juurde ka näite, kuidas CSV-faili veerge ümber nimetada, kui personaliüksus saadab faili teistsuguste päistega?**
