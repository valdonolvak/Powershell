See peatükk viib meid Active Directory halduse tuumani – kasutajakontode elutsükli haldamiseni. Saame teada, kuidas luua uusi töötajaid, jälgida nende aktiivsust ja hoida süsteem puhtana, liigutades mitteaktiivsed kasutajad "karantiini".

---

## Peatükk 8: Kasutajate haldus ja elutsükkel

### 1. Ühe kasutaja loomine: `New-ADUser`

Uue kasutaja loomisel on kriitiline määrata ära põhiandmed, et konto vastaks ettevõtte standarditele ja asuks õiges OU-s.

**Koodinäide:**

```powershell
# Uue töötaja loomine koos parooli ja OU määramisega
$Parool = Read-Host -AsSecureString "Sisesta kasutaja esmane parool"

New-ADUser -Name "Jaan Tamm" `
           -SamAccountName "jaan.tamm" `
           -UserPrincipalName "jaan.tamm@kool.local" `
           -GivenName "Jaan" `
           -Surname "Tamm" `
           -Path "OU=Users,OU=CyberEstonia,DC=kool,DC=local" `
           -AccountPassword $Parool `
           -Enabled $true `
           -ChangePasswordAtLogon $true

```

* **`-ChangePasswordAtLogon $true`**: Kohustab kasutajat esimesel sisselogimisel parooli muutma – see on oluline turvanõue.

### 2. Sisselogimise jälgimine: `LastLogonDate`

PowerShell võimaldab vaadata, millal kasutaja viimati domeeni sisse logis. See on parim viis tuvastada "kummituskontosid" (töötajad, kes on lahkunud, aga kelle konto on ikka aktiivne).

**Koodinäide:**

```powershell
# Vaata konkreetse kasutaja viimast sisselogimist
Get-ADUser -Identity "jaan.tamm" -Properties LastLogonDate | Select-Object Name, LastLogonDate

```

### 3. Kasutajate liigutamine: `Move-ADObject`

Kui kasutaja vahetab osakonda või tema konto deaktiveeritakse, tuleb ta liigutada teise OU-sse.

**Koodinäide:**

```powershell
# Liiguta kasutaja "DISABLED" üksusesse
# Identity parameetriks peab olema objekti DistinguishedName (DN)
$KasutajaDN = (Get-ADUser -Identity "jaan.tamm").DistinguishedName
Move-ADObject -Identity $KasutajaDN -TargetPath "OU=DISABLED,DC=kool,DC=local"

```

---

### 4. Mitteaktiivsete kasutajate "puhastus"

Administraatori töö on hoida süsteem turvalisena. See tähendab, et kasutajad, kes pole näiteks **pool aastat (180 päeva)** sisse loginud, tuleks deaktiveerida ja liigutada eraldi OU-sse.

**Koodinäide (Loogika selgitus):**

```powershell
# 1. Arvutame kuupäeva, mis oli 180 päeva tagasi
$PiirKuupaev = (Get-Date).AddDays(-180)

# 2. Leiame aktiivsed kasutajad, kes pole loginud pärast seda kuupäeva
$VanadKasutajad = Get-ADUser -Filter "LastLogonDate -lt '$PiirKuupaev' -and Enabled -eq 'True'" -Properties LastLogonDate

# 3. Käime nad tsükliga läbi, lülitame välja ja liigutame
foreach ($Kasutaja in $VanadKasutajad) {
    Disable-ADAccount -Identity $Kasutaja.DistinguishedName
    Move-ADObject -Identity $Kasutaja.DistinguishedName -TargetPath "OU=DISABLED,DC=kool,DC=local"
    Write-Host "Kasutaja $($Kasutaja.Name) on arhiveeritud." -ForegroundColor Yellow
}

```

---

### Iseseisvad harjutused (Kasutajate haldus)

1. **Harjutus: Täpne loomine.** Loo kasutaja "Mari Maasikas" (`mari.maasikas`), määra talle osakonnaks "Turundus" ja asukohaks juba varem loodud `CyberEstonia\Turundus\Users` OU.
2. **Harjutus: Info päring.** Leia kõik oma domeeni kasutajad ja kuva tabelina nende `Name`, `SamAccountName` ja `LastLogonDate`. Sorteeri nimekiri sisselogimise aja järgi.
3. **Harjutus: Parooli lähtestamine.** Leia käsk, millega saab kasutaja parooli sunniviisiliselt muuta, kui ta on selle unustanud. (*Vihje: `Set-ADAccountPassword*`).
4. **Harjutus: Lukust lahti tegemine.** Kui kasutaja on parooliga eksinud ja konto on lukus, kasuta käsku `Unlock-ADAccount`.

---

## Iseseisev praktiline töö: "AD Puhastusüksus"

**Ülesanne:** Koosta skript, mis tuvastab mitteaktiivsed kasutajad ja liigutab nad "karantiini".

**Töö käik ja nõuded:**

1. **Ettevalmistus:** Loo domeeni juurde OU nimega `DISABLED_Users`.
2. **Filtreerimine:** Leia kõik kasutajad, kes pole sisse loginud viimase **6 kuu** jooksul.
* **Välista** otsingust "Administrator" ja teised kriitilised süsteemikontod.


3. **Tegevus:**
* Deaktiveeri need kontod (`Disable-ADAccount`).
* Liiguta kontod OU-sse `DISABLED_Users`.


4. **Logimine:** Kirjuta iga liigutatud kasutaja nimi ja kellaaeg faili `C:\Temp\puhastus_audit.log`.
5. **Raport:** Skripti lõpus kuva ekraanile lause: "Puhastus lõpetatud. Kokku arhiveeriti [arv] kasutajat."

**Esitamiseks õpetajale:**

* Valmis skript (`.ps1`).
* Screenshot `DISABLED_Users` OU sisust pärast skripti käivitamist.
* **Selgitus:** Miks on turvalisem mitteaktiivsed kasutajad esialgu liigutada `DISABLED` OU-sse ja konto välja lülitada, selle asemel et need kohe AD-st kustutada?

---

### Iseseisvad harjutused (Kasutajate haldus)

1. **Harjutus: Täpne loomine.** Loo kasutaja nimega "Mari Maasikas", määra talle osakonnaks "Turundus" ja asukohaks juba varem loodud `CyberEstonia\Turundus\Users` OU.
2. **Harjutus: Info päring.** Leia kõik oma domeeni kasutajad ja kuva tabelina nende `Name`, `SamAccountName` ja `Enabled` staatus.
3. **Harjutus: Parooli lähtestamine.** Leia käsk, millega saab kasutaja parooli sunniviisiliselt muuta (Vihje: `Set-ADAccountPassword`).
4. **Harjutus: Kasutaja lukust lahti tegemine.** Kui kasutaja on parooliga eksinud, kasuta käsku `Unlock-ADAccount`.

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
