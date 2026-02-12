See peatükk viib meid Active Directory halduse tuumani – kasutajakontode elutsükli haldamiseni. Saame teada, kuidas luua uusi töötajaid, jälgida nende aktiivsust ja hoida süsteem puhtana, liigutades mitteaktiivsed kasutajad "karantiini".

---

## Peatükk 8: Kasutaja loomine, haldus ja elutsükkel

### 1. Ühe kasutaja loomine: `New-ADUser`

Uue kasutaja loomisel on kriitiline määrata ära põhiandmed, et konto vastaks ettevõtte standarditele.

**Näide: Uue töötaja loomine:**

```powershell
New-ADUser -Name "Jaan Tamm" `
           -SamAccountName "jtamm" `
           -UserPrincipalName "jtamm@kool.local" `
           -GivenName "Jaan" `
           -Surname "Tamm" `
           -Path "OU=Users,OU=CyberEstonia,DC=kool,DC=local" `
           -AccountPassword (Read-Host -AsSecureString "Sisesta parool") `
           -Enabled $true `
           -ChangePasswordAtLogon $true

```

* **-SamAccountName:** Kasutajanimi sisselogimiseks (nt jtamm).
* **-Path:** Määrab ära OU, kuhu kasutaja läheb.
* **-ChangePasswordAtLogon $true:** Kohustab kasutajat esimesel sisselogimisel parooli muutma.

### 2. Kasutaja andmete muutmine: `Set-ADUser`

Kui töötaja vahetab osakonda või perenime:

```powershell
Set-ADUser -Identity "jtamm" -Title "Vanemspetsialist" -Department "IT"

```

### 3. Sisselogimise jälgimine: `LastLogonDate`

PowerShell võimaldab vaadata, millal kasutaja viimati süsteemi autentis. See on oluline turvameede.

**Käsk:**

```powershell
Get-ADUser -Identity "jtamm" -Properties LastLogonDate | Select-Object Name, LastLogonDate

```

*Märkus: `LastLogonDate` on konverteeritud ja loetav versioon AD atribuudist `lastLogonTimestamp`.*

---

### Iseseisvad harjutused (Kasutajate haldus)

1. **Harjutus: Täpne loomine.** Loo kasutaja nimega "Mari Maasikas", määra talle osakonnaks "Turundus" ja asukohaks juba varem loodud `CyberEstonia\Turundus\Users` OU.
2. **Harjutus: Info päring.** Leia kõik oma domeeni kasutajad ja kuva tabelina nende `Name`, `SamAccountName` ja `Enabled` staatus.
3. **Harjutus: Parooli lähtestamine.** Leia käsk, millega saab kasutaja parooli sunniviisiliselt muuta (Vihje: `Set-ADAccountPassword`).
4. **Harjutus: Kasutaja lukust lahti tegemine.** Kui kasutaja on parooliga eksinud, kasuta käsku `Unlock-ADAccount`.

---

### 4. Mitteaktiivsete kasutajate liigutamine (Arhiveerimine)

Administraatori üks tähtsamaid ülesandeid on hoida AD puhtana. Kui kasutaja pole pool aastat sisse loginud, tuleks tema konto turvalisuse huvides deaktiveerida ja liigutada eraldi OU-sse.

**Loogika:**

1. Määrame ajapiiri (tänane kuupäev miinus 180 päeva).
2. Otsime kasutajad, kelle `LastLogonDate` on väiksem kui see piir.
3. Liigutame nad (`Move-ADObject`) ja lülitame välja (`Disable-ADAccount`).

**Näidisskript:**

```powershell
$Piirkuupaev = (Get-Date).AddMonths(-6)
$SihtOU = "OU=DISABLED,DC=kool,DC=local"

$VanadKasutajad = Get-ADUser -Filter 'LastLogonDate -lt $Piirkuupaev -and Enabled -eq $true' -Properties LastLogonDate

foreach ($Kasutaja in $VanadKasutajad) {
    Write-Host "Töötlen kasutajat: $($Kasutaja.Name)" -ForegroundColor Yellow
    Disable-ADAccount -Identity $Kasutaja.DistinguishedName
    Move-ADObject -Identity $Kasutaja.DistinguishedName -TargetPath $SihtOU
}

```

---

### Iseseisev praktiline töö: "AD Puhastusüksus"

**Ülesanne:** Koosta täisautomaatne skript süsteemi puhastamiseks.

**Töö käik ja nõuded:**

1. **Ettevalmistus:**
* Loo käsitsi või skriptiga uus OU nimega `DISABLED_Users`.


2. **Skripti sisu:**
* Loo muutuja, mis arvutab välja kuupäeva **6 kuud tagasi**.
* Leia kõik kasutajad, kes pole sellest kuupäevast saadik sisse loginud.
* **Välista** otsingust "Administrator" konto (kasuta `-and Name -ne "Administrator"`).


3. **Tegevused tsükli sees:**
* Logi iga liigutatava kasutaja nimi faili `puhastus_logi.txt`.
* Deaktiveeri konto.
* Liiguta konto OU-sse `DISABLED_Users`.


4. **Kokkuvõte:** Skript peab lõpus teatama, mitu kasutajat kokku "arhiivi" saadeti.

**Esitamiseks õpetajale:**

* Valmis `.ps1` skript.
* Logifail `puhastus_logi.txt` näidisandmetega.
* **Selgitus:** Miks on turvalisem mitteaktiivsed kasutajad esialgu liigutada `DISABLED` OU-sse ja konto välja lülitada, selle asemel et need kohe kustutada?

---

**Nõuanne:** Kui testid liigutamist, lisa `Move-ADObject` käsule parameeter `-WhatIf`, et näha, kas Distinguished Name'id ja sihtasukoht on korrektsed!
