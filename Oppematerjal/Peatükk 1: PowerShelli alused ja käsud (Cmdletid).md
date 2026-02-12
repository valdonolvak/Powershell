Tere tulemast PowerShelli maailma! See on tööriist, mis muudab tavalise IT-spetsialisti "superadministraatoriks". Selles peatükis teeme selgeks vundamendi: mis on PowerShell, kuidas see räägib ja kuidas panna see enda kasuks tööle.

---

## Peatükk 1: PowerShelli alused ja käsud (Cmdletid)

### 1. Mis on PowerShell?

Erinevalt vanast DOS-i meenutavast `cmd.exe`-st, ei ole PowerShell lihtsalt tekstipõhine käsurida. See on **objektipõhine** automatiseerimisraamistik.

* **CMD:** Tagastab teksti (sõned).
* **PowerShell:** Tagastab objekte (andmeid, millel on omadused ja meetodid).

### 2. Käskude struktuur: Tegusõna-Nimisõna

PowerShelli käsud (mida kutsutakse **cmdlet**ideks) järgivad alati ranget loogikat: `Verb-Noun` (Tegusõna-Nimisõna). See teeb nende meeldejätmise ja äraarvamise lihtsaks.

* **Get** – Võta andmeid (nt `Get-Service`)
* **Set** – Muuda seadeid (nt `Set-Date`)
* **New** – Loo midagi uut (nt `New-Item`)
* **Remove** – Kustuta midagi (nt `Remove-Item`)

### 3. Kolm kuldset käsku (Ellujäämiskomplekt)

Kui sa ei tea, mida teha, aitavad need kolm käsku sul alati tee leida:

1. `Get-Help`: Kasutusjuhend. Kirjuta `Get-Help Get-Service`, et näha, kuidas käsku kasutada.
2. `Get-Command`: Otsib käske. Kirjuta `Get-Command *process*`, et näha kõiki käske, mis tegelevad protsessidega.
3. `Get-Member`: Näitab, mis "asjad" (omadused) on käsu väljundi sees.

---

### Iseseisvad harjutused (Soojendus)

1. **Harjutus: Süsteemi info.** Leia käsk, mis kuvab arvuti praeguse kuupäeva ja kellaaja.
2. **Harjutus: Teenuste kontroll.** Kasuta käsku `Get-Service`. Kuidas saaksid kuvada ainult need teenused, mis praegu **töötavad** (Status: Running)? *Vihje: Proovi `Get-Help Get-Service`.*
3. **Harjutus: Abi otsimine.** Kasuta `Get-Help`, et teada saada, millist parameetrit on vaja käsul `Get-ChildItem`, et näha ka peidetud faile (hidden files).
4. **Harjutus: Protsesside sorteerimine.** Kuvage kõik arvutis jooksvad protsessid (`Get-Process`) ja sorteerige need nime järgi tähestikulises järjekorras.

---

### 4. Torustik (The Pipeline `|`)

See on PowerShelli võimsaim sümbol. Torustik võtab ühe käsu väljundi ja saadab selle järgmisele käsule sisendiks.

**Näide:** `Get-Service | Where-Object {$_.Status -eq "Stopped"}`

* See võtab kõik teenused ja "valab" need läbi sõela, jättes alles vaid seisvad teenused.

---

## Iseseisev praktiline töö: "Süsteemi audiitor"

**Ülesanne:** Koosta PowerShelli skript (faililaiendiga `.ps1`) või käsurida, mis teostab süsteemi kiire kontrolli. Tulemus peab olema õpetajale esitatav.

**Töö käik ja nõuded:**

1. **Failide loetelu:** Kuva kõik failid sinu `Documents` (Dokumendid) kaustas, mis on suuremad kui 10MB.
2. **Protsesside analüüs:** Leia 5 kõige rohkem protsessorit (CPU) koormavat protsessi ja järjesta need kahanevalt.
3. **Teenuste raport:** Leia kõik teenused, mille nimes sisaldub sõna "Network", ja kontrolli, kas need on käivitatud.
4. **Väljund faili:** Suuna kogu see info ühte tekstifaili nimega `Audit_Raport.txt`.
* *Vihje:* Kasuta käsu lõpus märki `>` või käsku `Out-File`.



**Esitamiseks:**

* Kopeeri oma kasutatud käsud tekstifaili ja selgita iga rea juures ühe lausega, mida see käsk teeb.
* Lisa juurde kuvatõmmis (screenshot) loodud `Audit_Raport.txt` faili sisust.

---
