See peatükk on järg seitsmendale osale, kus õppisid Active Directory (AD) aluseid. Kui kasutajad ja arvutid on organisatsiooni "elanikud", siis **OU (Organizational Unit)** ehk organisatsiooniüksus on "maja", kus nad elavad. Korralik OU struktuur on vundament rühmapoliitikate (GPO) rakendamiseks ja õiguste jagamiseks.

---

## Peatükk 7: OU struktuuri loomine ja haldus

### 1. Mis on OU ja miks see oluline on?

OU on konteiner Active Directory sees. Erinevalt tavalistest süsteemsetest kaustadest (nt *Users* konteiner), saab OU-dele määrata:

* **Group Policy (GPO):** Rakendada seadeid (nt ekraanilukk, tarkvara paigaldus) ainult kindlale osakonnale.
* **Delegation:** Anda kohalikule IT-spetsialistile õigus hallata ainult oma osakonna paroole, ilma et ta saaks muuta kogu domeeni.

### 2. OU loomine: `New-ADOrganizationalUnit`

OU loomisel on kõige olulisem määrata selle nimi ja asukoht AD puus (**Path**), mida kirjeldatakse *Distinguished Name* (DN) vormingus.

**Koodinäide:**

```powershell
# Loo OU nimega "Raamatupidamine" peakausta "CyberEstonia" alla
New-ADOrganizationalUnit -Name "Raamatupidamine" `
                         -Path "OU=CyberEstonia,DC=kool,DC=local" `
                         -Description "Raamatupidamise osakonna kasutajad ja arvutid" `
                         -ProtectedFromAccidentalDeletion $true

```

> **Märkus:** Parameeter `-ProtectedFromAccidentalDeletion $true` on PowerShelli "turvavöö". See takistab OU juhuslikku kustutamist (isegi `Delete` klahviga ADUC-is).

### 3. OU-de leidmine ja muutmine

* **`Get-ADOrganizationalUnit`**: Kuvab olemasolevad OU-d.
* **`Set-ADOrganizationalUnit`**: Muudab atribuute (nt kirjeldust või kustutamiskaitset).
* **`Remove-ADOrganizationalUnit`**: Kustutab üksuse.

**Koodinäide (Kaitse eemaldamine ja kustutamine):**

```powershell
# 1. Võtame kaitse maha
Set-ADOrganizationalUnit -Identity "OU=Test_Labor,DC=kool,DC=local" -ProtectedFromAccidentalDeletion $false

# 2. Kustutame OU
Remove-ADOrganizationalUnit -Identity "OU=Test_Labor,DC=kool,DC=local" -Confirm:$false

```

### 4. Automatiseerimine: Struktuuride loomine massiivist

Reaalses elus luuakse tihti terveid struktuure korraga. Siin tulevad appi eelmistes peatükkides õpitud massiivid ja tsüklid.

**Koodinäide (Osakondade mass-loomine):**

```powershell
$Osakonnad = "IT", "Turundus", "Muuk", "Juhtkond"
$ParentPath = "DC=kool,DC=local"

foreach ($Osakond in $Osakonnad) {
    New-ADOrganizationalUnit -Name $Osakond -Path $ParentPath
    Write-Host "Loodi osakond: $Osakond" -ForegroundColor Cyan
}

```

---

### Iseseisvad harjutused (Struktuuri harjutamine)

1. **Harjutus: Üksiku OU loomine.** Loo domeeni juurkataloogi uus OU nimega `Labor_Haldus`.
2. **Harjutus: Alam-OU-d.** Loo eelmises harjutuses loodud `Labor_Haldus` sisse kaks alam-OU-d: `Kasutajad` ja `Arvutid`.
3. **Harjutus: Kirjelduse lisamine.** Muuda OU `Labor_Haldus` kirjeldust (`Description`) ja lisa sinna tekst: "Loodud õppetöö käigus [SinuNimi] poolt".
4. **Harjutus: Otsing.** Leia käsk, mis kuvab kõik sinu domeenis olevad OU-d, mille nimes sisaldub sõna "Labor".

---
### Iseseisev praktiline töö: "Ettevõtte IT-arhitektuur"

**Ülesanne:** Sinu ülesandeks on luua uue ettevõtte "CyberEstonia" AD struktuur, kasutades ainult PowerShelli skripti.

**Töö käik ja nõuded:**

1. **Peamine struktuur:** Loo juurkataloogi peamine OU nimega `CyberEstonia`.
2. **Osakonnad:** Kasuta massiivi ja `foreach` tsüklit, et luua `CyberEstonia` sisse järgmised osakonnad:
* `Arendus`
* `Administratsioon`
* `Klienditugi`


3. **Standardimine:** Iga osakonna (alam-OU) sisse peab skript automaatselt looma veel kaks alam-OU-d: `Users` ja `Workstations`.
* *Vihje:* Kasuta pesastatud (nested) tsüklit või loo tee dünaamiliselt muutuja abil.


4. **Kontroll ja Logi:**
* Skript peab kontrollima, kas OU on juba olemas, et vältida veateateid.
* Kirjuta iga loodud OU nimi tekstifaili `Struktuur_Raport.txt`.


5. **Kustutamine (Valikuline lisaülesanne):** Lisa skripti lõppu osa, mis on välja kommenteeritud (`#`), mis suudaks kogu selle loodud struktuuri uuesti kustutada (vajab kaitse mahavõtmist!).

**Esitamiseks õpetajale:**

* Valmis skriptifail (`.ps1`).
* Screenshot Active Directory Users and Computers (ADUC) vaatest, kus on näha loodud hierarhia.
* **Selgitus:** Miks on hea praktika jagada kasutajad ja arvutid eraldi OU-desse, mitte hoida neid koos ühes suures osakonna kaustas?

---

**Nõuanne:** Kui sa ei taha päris serverit "sotki" keerata, kasuta alati käsu lõpus `-WhatIf`. See näitab sulle täpselt, millise Distinguished Name-ga ja kuhu PowerShell OU-sid looma hakkaks.
