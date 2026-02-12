See on PowerShelli ja Active Directory halduse "meistriklass". Reaalses töökeskkonnas ei looda administraator 50 uut töötajat käsitsi, vaid impordib nad personaliüksuse saadetud tabelist. Selles peatükis õpime, kuidas siduda failihaldus, tsüklid ja AD käsud üheks võimsaks automaatikaks.

---

## Peatükk 9: Kasutajate mass-import CSV-failist

### 1. CSV-faili ettevalmistus

CSV (*Comma Separated Values*) on tekstifail, kus andmed on eraldatud komade või semikoolonitega. PowerShelli jaoks on CSV rida nagu **objekt** ja veeru pealkiri nagu **omadus**.

**Näidis `kasutajad.csv` failist:**

```csv
Eesnimi,Perenimi,Kasutajatunnus,Osakond,Parool,Staatus
Kati,Kask,kkask,IT,Tervitus2024!,True
Mati,Mänd,mmand,Muuk,Suvi2024?,True
Siim,Sikk,ssikk,Turundus,Saladus123,False

```

### 2. CSV importimine: `Import-Csv`

Käsk `Import-Csv` loeb faili sisse ja muudab selle PowerShelli objektide massiiviks.

```powershell
$andmed = Import-Csv -Path "C:\Temp\kasutajad.csv" -Delimiter ","

```

### 3. Paroolide töötlemine (SecureString)

AD ei luba parooli sisestada tavalise tekstina. See peab olema konverteeritud turvaliseks sõneks (*SecureString*).

```powershell
$turvalineParool = ConvertTo-SecureString "TekstParool" -AsPlainText -Force

```

---

### Iseseisvad harjutused (CSV ja ettevalmistus)

1. **Harjutus: CSV lugemine.** Loo ise tekstiredaktoriga (Notepad) 3-realine CSV fail ja kuva selle sisu PowerShellis tabelina (`Format-Table`).
2. **Harjutus: Filtreerimine massiivist.** Kasuta `Import-Csv` ja `Where-Object`, et näha ainult neid inimesi, kes on CSV failis märgitud "IT" osakonda.
3. **Harjutus: Tee testimine.** Koosta skript, mis kontrollib, kas sinu poolt määratud CSV fail üldse eksisteerib (`Test-Path`), enne kui üritab seda importida.
4. **Harjutus: Dünaamiline OU tee.** Katseta koodirida, mis paneb kokku asukoha stringi: `"OU=$osakond,OU=CyberEstonia,DC=kool,DC=local"`.

---

### 4. Skripti loogika: Kõik kokku

Nüüd paneme kokku tsükli, parooli teisendamise ja kasutaja loomise õigesse asukohta.

```powershell
$kasutajad = Import-Csv "C:\Temp\kasutajad.csv"

foreach ($rida in $kasutajad) {
    # 1. Muudame parooli turvaliseks
    $pw = ConvertTo-SecureString $rida.Parool -AsPlainText -Force
    
    # 2. Määrame dünaamiliselt OU vastavalt osakonnale
    $targetOU = "OU=$($rida.Osakond),OU=CyberEstonia,DC=kool,DC=local"
    
    # 3. Loome kasutaja
    New-ADUser -Name "$($rida.Eesnimi) $($rida.Perenimi)" `
               -GivenName $rida.Eesnimi `
               -Surname $rida.Perenimi `
               -SamAccountName $rida.Kasutajatunnus `
               -UserPrincipalName "$($rida.Kasutajatunnus)@kool.local" `
               -Path $targetOU `
               -AccountPassword $pw `
               -Enabled ([bool]$rida.Staatus) `
               -ChangePasswordAtLogon $true
}

```

---

### Iseseisev praktiline töö: "Suurettevõtte käivitus"

**Ülesanne:** Sulle on antud nimekiri 10 uue töötajaga. Pead nad kõik korraga süsteemi kandma, määrates neile paroolid ja õiged osakonnad.

**Töö käik ja nõuded:**

1. **CSV fail:** Loo fail `uued_tootajad.csv`. Lisa sinna vähemalt 5 inimest. Veergudeks peavad olema: `Firstname, Lastname, Username, Dept, Pass, IsActive`.
2. **Skripti funktsionaalsus:**
* Skript peab lugema andmed CSV failist.
* Iga kasutaja puhul peab skript kontrollima, kas vastav osakonna OU (`OU=Dept...`) on olemas. Kui ei ole, peab skript selle looma.
* Kasutaja tuleb luua vastavasse osakonna OU-sse.
* Kasutaja peab olema aktiveeritud (`Enabled`) vastavalt CSV veeru `IsActive` väärtusele (vihje: kasuta tüübiteisendust `[bool]`).


3. **Logimine ja Try-Catch:**
* Kasuta `Try-Catch` blokki `New-ADUser` ümber. Kui kasutaja loomine ebaõnnestub (nt kasutajanimi on juba võetud), siis logi viga faili `vead.log`.
* Eduka loomise korral logi: "Kasutaja [Username] loodi edukalt asukohta [OU]".



**Esitamiseks õpetajale:**

1. Sinu koostatud `uued_tootajad.csv` fail.
2. Täielik `.ps1` skriptifail.
3. Screenshot Active Directory vaatest, kus on näha uued kasutajad erinevates osakondades.
4. **Selgitus:** Miks on oluline kasutada CSV-s parameetrit `-Delimiter` (eraldaja), kui töötad erinevates regioonides (Eesti vs USA) loodud failidega?

---

**Nõuanne:** Kui CSV-st loetud `IsActive` väli on tekst "True", siis `New-ADUser` ei pruugi seda otse booleanina mõista. Kasuta alati `[bool]$rida.IsActive`.
