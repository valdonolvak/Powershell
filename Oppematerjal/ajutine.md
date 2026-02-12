See on suurepärane täiendus! IT-maailmas on `eesnimi.perenimi` (näiteks `jaan.tamm`) üks levinumaid standardeid, kuna see on selge ja loetav.

Siin on uuendatud **Peatükk 9** täielik õppematerjal, kus on korrigeeritud CSV-fail (50 kasutajat) ja koodinäited, mis toetavad punktiga eraldatud kasutajatunnuseid ning nende loomist `OU=KASUTAJAD` struktuuri sisse.

---

# Peatükk 9: Kasutajate nutikas mass-import CSV-failist

Selles peatükis õpime, kuidas luua professionaalne skript, mis suudab andmeid lugeda välisest failist, kontrollida süsteemi olekut ning jätta oma tegevustest maha korrektse logi.

### 1. CSV-faili ettevalmistus

CSV-fail on skripti sisend. Selles versioonis on kasutajatunnus vormingus `eesnimi.perenimi`.

**Näidis `uued_tootajad.csv` (faili sisu):**

```csv
Eesnimi,Perenimi,Kasutajatunnus,Osakond,Parool,Staatus
Jaan,Tamm,jaan.tamm,OPETAJAD,Passw0rd!,True
Mari,Kask,mari.kask,OPETAJAD,Passw0rd!,True
Kevin,Karu,kevin.karu,OPILASED,Passw0rd!,True
Tanel,Tehnika,tanel.tehnika,IT,Passw0rd!,True

```

### 2. Kuidas lugeda CSV-faili rida-realt?

Andmete importimiseks kasutame käsku `Import-Csv`. Selleks, et iga rida eraldi töödelda, kasutame `foreach` tsüklit.

**Koodinäide:**

```powershell
$Kasutajad = Import-Csv -Path "C:\Temp\uued_tootajad.csv"

foreach ($Rida in $Kasutajad) {
    # Pöördume veergude poole muutuja $Rida abil
    Write-Host "Loon kasutajat: $($Rida.Kasutajatunnus) osakonda $($Rida.Osakond)"
}

```

### 3. Kontroll: Kas OU on olemas?

Kasutajad peavad minema `OU=KASUTAJAD` sisse. Skript peab kontrollima, kas see ja ka osakonna alam-OU (nt IT) on olemas.

**Koodinäide:**

```powershell
# Dünaamiline tee koostamine
$SihtOU = "OU=$($Rida.Osakond),OU=KASUTAJAD,DC=kool,DC=local"

if (-not (Get-ADOrganizationalUnit -Filter "DistinguishedName -eq '$SihtOU'")) {
    # Kui alam-OU-d pole, loome selle
    New-ADOrganizationalUnit -Name $Rida.Osakond -Path "OU=KASUTAJAD,DC=kool,DC=local"
}

```

### 4. Logimine: Kuidas kirjutada ajalugu?

Administraator peab teadma, mis kell ja mida tehti. Logifaili kirjutamiseks kasutame `Add-Content`.

**Koodinäide:**

```powershell
$Aeg = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
$LogiRida = "[$Aeg] EDUKAS: Kasutaja $($Rida.Kasutajatunnus) loodi."
Add-Content -Path "C:\Temp\import.log" -Value $LogiRida

```

---

### Iseseisev praktiline töö: "50 kasutaja automaatne import"

**Ülesanne:** Koosta skript, mis loeb allolevat 50 kasutajaga CSV-faili ja loob kasutajad Active Directorysse.

1. Kontrolli ja loo vajadusel puuduva **Osakonna OU** (asukoht: `OU=KASUTAJAD`).
2. Kontrolli, ega **kasutajatunnus** pole juba olemas.
3. Kirjuta **logifaili** tegevuse kuupäev, kellaaeg ja tulemus.

