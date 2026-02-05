
Struktuur:
1️⃣ Õpiülesanne õpilastele (juhend)
2️⃣ Olulised PowerShelli käsud ja seletused
3️⃣ Testimise juhend (väga oluline!)

---

# 1️⃣ ÕPIÜLESANNE ÕPILASTELE

## Mitteaktiivsete Active Directory kasutajate tuvastamine ja keelamine

---

## 🎯 Õppe-eesmärk

Selle ülesande läbimisel õpilane:

* õpib lugema kasutaja **viimast sisselogimise aega**
* oskab võrrelda kuupäevi PowerShellis
* tuvastab **mitteaktiivsed kasutajad**
* liigutab kasutajad teise **OU-sse**
* mõistab, miks **testimine on kriitilise tähtsusega**

---

## 📋 Ülesande kirjeldus

Koosta PowerShelli skript, mis:

1. Kontrollib Active Directory kasutajate **viimast autentimist**
2. Leiab kasutajad, kes:

   * ei ole **üle 1 aasta** sisse loginud
     **VÕI**
   * ei ole **kunagi autentinud**
3. Keelab (disable) need kasutajad
4. Liigutab need OU-sse **DISABLED**
5. OU **DISABLED** peab asuma OU **KASUTAJAD** all

---

## ⚠️ OLULINE TURVANÕUE (KOHUSTUSLIK!)

❗ **Enne skripti käivitamist PEAB:**

* OU **DISABLED** olema loodud
* skripti testima **ÜHE konkreetse testkasutajaga**
* veenduma, et **Domain Admin / Administrator kontosid ei puudutata**

📌 **Vale skript võib lukustada kogu domeeni!**

---

## 🏗️ Eeldused

* PowerShell käivitatud **Administratorina**
* ActiveDirectory moodul olemas
* Õpilane teab, mis on:

  * OU
  * kasutajakonto
  * Domain Admin

---

# 2️⃣ OLULISED POWERSHELLI KÄSUD JA SELETUSED

---

## 🔹 Active Directory mooduli laadimine

```powershell
Import-Module ActiveDirectory
```

---

## 🔹 OU DISABLED loomine (enne skripti käivitamist!)

```powershell
New-ADOrganizationalUnit -Name "DISABLED" -Path "OU=KASUTAJAD,DC=TEST,DC=LOCAL"
```

📘 **Seletus:**
Skript ei tohi OU-d ise luua – see on **teadlik turvameede**.

---

## 🔹 Kasutaja viimase sisselogimise aeg

```powershell
Get-ADUser testkasutaja -Properties LastLogonDate
```

📘 **Seletus:**

* `LastLogonDate` → koondatud ja inimloetav
* Kui väärtus on tühi → kasutaja pole kunagi loginud

---

## 🔹 Kuupäeva arvutamine (1 aasta tagasi)

```powershell
$limitDate = (Get-Date).AddYears(-1)
```

---

## 🔹 Kasutaja keelamine

```powershell
Disable-ADAccount -Identity testkasutaja
```

---

## 🔹 Kasutaja liigutamine OU-sse

```powershell
Move-ADObject `
  -Identity (Get-ADUser testkasutaja).DistinguishedName `
  -TargetPath "OU=DISABLED,OU=KASUTAJAD,DC=TEST,DC=LOCAL"
```

---

# 3️⃣ TESTIMISE JUHEND (VÄGA OLULINE)

### 🔴 ÄRA kunagi testi skripti kohe kõigi kasutajatega!

### ✅ Õige testimise protsess:

1. Loo **üks testkasutaja**
2. Kontrolli tema `LastLogonDate`
3. Käivita skript ainult selle kasutajaga
4. Veendu, et:

   * kasutaja keelatakse
   * kasutaja liigub OU-sse DISABLED
5. Alles siis kasuta skripti kõigi kasutajate peal

📌 **Skripti peab saama käivitada ka “dry-run” režiimis** (ainult kuvab, ei muuda).

---
