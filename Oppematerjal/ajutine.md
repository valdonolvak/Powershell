Väga hea! Nüüd jõuame punkti, kus sinu kood muutub "targaks" ja professionaalseks. **Peatükk 5** õpetab sulle, kuidas teha skripte, mis ei jookse vea korral kokku ja mis peavad oma tegevuse kohta logiraamatut.

---

## Peatükk 5: Skriptid, vigade haldus ja logimine

See peatükk õpetab sulle, kuidas vormistada oma koodi failina, kuidas püüda kinni ootamatuid vigu ja kuidas salvestada skripti tegevusajalugu tekstifaili.

### 1. Skripti fail ja käivituspoliitika

PowerShelli skript on tekstifail laiendiga **`.ps1`**. Enne skriptide käivitamist peab Windowsis lubama skriptide jooksmise (turvalisuse huvides on see algul kinni).

* **Kontrolli staatust:** `Get-ExecutionPolicy`
* **Luba skriptid:** `Set-ExecutionPolicy RemoteSigned` (vajab administraatori õigusi).

### 2. Vigade haldus: `Try-Catch`

Kui skriptis tekib viga (nt üritad kustutada faili, mida pole olemas), siis tavaliselt kuvatakse punane tekst ja skript katkeb. `Try-Catch` plokk võimaldab vigu "püüda" ja nendega viisakalt tegeleda.

**Koodinäide:**

```powershell
try {
    # Proovime teha midagi ohtlikku (ErrorAction Stop sunnib vea Catch-i)
    Remove-Item -Path "C:\Temp\olematu_fail.txt" -ErrorAction Stop
}
catch {
    # Kui tekkis viga, täidetakse see osa
    Write-Warning "Hoiatus: Faili ei leitud, aga skript jätkab tööd."
}

```

### 3. Logimine: Tegevuste salvestamine

Logimine on kriitiline, et administraator teaks, mis juhtus siis, kui ta ekraani ei vaadanud. Kasutame selleks käsku `Add-Content`.

**Koodinäide:**

```powershell
$LogiFail = "C:\Temp\skripti_logi.txt"
$Aeg = Get-Date -Format "yyyy-MM-dd HH:mm:ss"

# Kirjutame logisse teate
Add-Content -Path $LogiFail -Value "[$Aeg] INFO: Skript käivitus edukalt."

```

### 4. Skripti parameetrid

Selle asemel, et koodi sees andmeid muuta, saame skriptile need ette anda sarnaselt käskudele.

**Koodinäide (salvesta failina `Tervitus.ps1`):**

```powershell
param(
    [string]$Nimi = "Kasutaja"
)

Write-Host "Tere, $Nimi!"

```

*Käivitamine:* `.\Tervitus.ps1 -Nimi "Juhan"`

---

### Iseseisvad harjutused (Vead ja logid)

1. **Harjutus: Vigade püüdmine.** Kirjuta skript, mis üritab peatada teenust nimega `ValeTeenus`. Kasuta `Try-Catch` blokki, et vea korral kuvataks ekraanil tekst: "Seda teenust ei ole arvutis".
2. **Harjutus: Logi loomine.** Loo skript, mis kontrollib, kas kaust `C:\Windows` on olemas (`Test-Path`). Kirjuta tulemus faili `kontroll.log` koos kellaajaga.
3. **Harjutus: Parameetritega arvutamine.** Loo skript, mis võtab parameetritena kaks numbrit ja väljastab nende summa.
4. **Harjutus: Turvaline kustutamine.** Loo skript, mis üritab kustutada kausta `C:\Temp\Test`. Kui kausta pole, logi see faili `vead.log`.

---

## Iseseisev praktiline töö: "Süsteemi hooldusskript"

**Ülesanne:** Koosta professionaalne hooldusskript, mis tegeleb failide puhastamise ja logimisega.

**Töö käik ja nõuded:**

1. **Logimine:** Skripti alguses loo/täienda logifaili `C:\Temp\hooldus.log` teatega: "--- HOOLDUS ALUSTATUD [Aeg] ---".
2. **Puhastus ja Vigade haldus:**
* Skript peab üritama kustutada kõik `.tmp` failid kaustast `C:\Temp`.
* Kasuta **`Try-Catch`** blokki. Kui kustutamisel tekib viga (nt fail on kasutusel), logi veateade faili `hooldus.log`.


3. **Tulemus:** Loe kokku, mitu faili oli kaustas enne puhastust ja pärast puhastust (kasuta `.Count` omadust).
4. **Lõpetamine:** Logi skripti lõpus: "--- HOOLDUS LÕPETATUD. Kustutati [arv] faili. ---".

**Esitamiseks:**

* Esita täielik skripti kood.
* Selgita, miks on oluline kasutada parameetrit `-ErrorAction Stop` käsu juures, mida soovid `Try-Catch` blokiga püüda.
* Esita screenshot loodud logifaili sisust.

---

**Kas liigume edasi Peatükk 6 juurde (Active Directory moodul), et õppida, kuidas hallata kasutajaid ja gruppe suures võrgus?**
