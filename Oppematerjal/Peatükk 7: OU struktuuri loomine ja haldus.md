See peatükk on järg seitsmendale osale, kus õppisid Active Directory (AD) aluseid. Kui kasutajad ja arvutid on organisatsiooni "elanikud", siis **OU (Organizational Unit)** ehk organisatsiooniüksus on "maja", kus nad elavad. Korralik OU struktuur on vundament rühmapoliitikate (GPO) rakendamiseks ja õiguste jagamiseks.

---

## Peatükk 7: OU struktuuri loomine ja haldus

### 1. Mis on OU ja miks see oluline on?

OU on konteiner Active Directory sees. Erinevalt tavalistest kaustadest (nt *Users* või *Computers* konteinerid), saab OU-dele määrata:

* **Group Policy (GPO):** Rakendada seadeid ainult kindlale osakonnale.
* **Delegation:** Anda kohalikule IT-spetsialistile õigus hallata ainult oma osakonna paroole.

### 2. OU loomine: `New-ADOrganizationalUnit`

OU loomisel on kõige olulisem määrata selle **Distinguished Name (DN)**, mis kirjeldab ära asukoha AD puus.

**Struktuur:**
`New-ADOrganizationalUnit -Name "OsakonnaNimi" -Path "OU=Parent,DC=domeen,DC=ee"`

**Näide:** Loo OU nimega "Raamatupidamine" peakausta "Kasutajad" alla:

```powershell
New-ADOrganizationalUnit -Name "Raamatupidamine" -Path "OU=Kasutajad,DC=kool,DC=local" -ProtectedFromAccidentalDeletion $true

```

> **Märkus:** `-ProtectedFromAccidentalDeletion $true` on PowerShelli "turvavöö" – see takistab OU juhuslikku kustutamist.

### 3. OU-de leidmine ja muutmine

* `Get-ADOrganizationalUnit -Filter *` – Kuvab kõik domeeni OU-d.
* `Set-ADOrganizationalUnit` – Võimaldab muuta nime või asukohta.
* `Remove-ADOrganizationalUnit` – Kustutab üksuse (eeldab, et kaitse on maha võetud).

---

### Iseseisvad harjutused (Struktuuri harjutamine)

1. **Harjutus: Üksiku OU loomine.** Loo oma domeeni juurkataloogi uus OU nimega `Test_Labor`.
2. **Harjutus: Alam-OU-d.** Loo eelmises harjutuses loodud `Test_Labor` sisse kaks alam-OU-d: `Kasutajad` ja `Arvutid`.
3. **Harjutus: Kirjelduse lisamine.** Muuda `Test_Labor` kirjeldust (`Description`) ja lisa sinna tekst: "Loodud õppetöö käigus [SinuNimi] poolt".
4. **Harjutus: Kaitse eemaldamine.** Uuri käsku `Set-ADOrganizationalUnit` ja leia parameeter, millega lülitada välja kogemata kustutamise kaitse (`ProtectedFromAccidentalDeletion`).

---

### 4. Automatiseerimine: Mass-loomine massiivist

Reaalses elus luuakse tihti terveid struktuure korraga. Siin tulevad appi eelmistes peatükkides õpitud massiivid ja tsüklid.

**Näide: Mitme osakonna loomine korraga:**

```powershell
$osakonnad = "IT", "Turundus", "Muük", "Juhtkond"
foreach ($osakond in $osakonnad) {
    New-ADOrganizationalUnit -Name $osakond -Path "DC=kool,DC=local"
}

```

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
