Tere tulemast PowerShelli maailma! See on tööriist, mis muudab tavalise IT-spetsialisti "superadministraatoriks". Selles peatükis teeme selgeks vundamendi: mis on PowerShell, kuidas see räägib ja kuidas panna see enda kasuks tööle.

# Peatükk 1: PowerShelli alused ja käsud (Cmdletid)

Tere tulemast PowerShelli maailma! See on tööriist, mis muudab tavalise IT-spetsialisti "superadministraatoriks". Selles peatükis teeme selgeks vundamendi: mis on PowerShell, kuidas see "räägib" ja kuidas panna see enda kasuks tööle.

### 1. Mis on PowerShell?

Erinevalt vana kooli tekstipõhisest käsureast (CMD), on PowerShell **objektipõhine**. See tähendab, et kui küsid nimekirja failidest, ei saa sa lihtsalt rida teksti, vaid "targad" andmed, millega saab edasi tööd teha.

* **CMD:** Väljastab teksti (sõned).
* **PowerShell:** Väljastab objekte (andmed, millel on omadused nagu suurus, loomisaeg ja õigused).

### 2. Käskude struktuur: Tegusõna-Nimisõna

PowerShelli käsud ehk **cmdletid** järgivad alati loogikat: `Verb-Noun` (Tegusõna-Nimisõna).

* **Get** – Võta andmeid (nt `Get-Service`)
* **Set** – Muuda seadeid (nt `Set-Date`)
* **New** – Loo midagi uut (nt `New-Item`)
* **Remove** – Kustuta midagi (nt `Remove-Item`)

### 3. Kolm kuldset käsku (Sinu ellujäämiskomplekt)

Kui sa ei mäleta täpset käsku, siis need kolm aitavad sul alati lahenduse leida:

1. **`Get-Help`**: Kasutusjuhend.
* *Näide:* `Get-Help Get-Service -Full` (näitab kõike, mida see käsk suudab).


2. **`Get-Command`**: Otsib käske märksõna järgi.
* *Näide:* `Get-Command *process*` (leiab kõik käsud, mis tegelevad protsessidega).


3. **`Get-Member`**: Näitab, milliseid "omadusi" (andmeid) üks käsk endas peidab.
* *Näide:* `Get-Service | Get-Member` (näitab, et teenustel on staatus, nimi jne).



---

### 4. Praktilised näidiskäsud (Kuidas see päriselt käib?)

Siin on mõned näited, mida saad kohe proovida, et mõista PowerShelli võimekust:

#### A. Filtreerimine ja valimine

Selleks, et suurest andmehulgast leida üles just see vajalik, kasutame "torustikku" (`|`) ja filtreerimiskäsku `Where-Object`.

```powershell
# Leia protsess, mille nimi on "chrome"
Get-Process | Where-Object {$_.ProcessName -eq "chrome"}

# Vali välja ainult teatud veerud, et tabel oleks loetavam
Get-Service | Select-Object Name, Status, StartType

```

#### B. Failisüsteemiga töötamine

```powershell
# Vaata oma kasutaja Dokumendid kausta sisu
Get-ChildItem -Path "$home\Documents"

# Otsi faile, mis on suuremad kui 10MB (PowerShell tunneb ühikuid MB, GB)
Get-ChildItem -Path "$home\Documents" -Recurse | Where-Object {$_.Length -gt 10MB}

```

#### C. Sorteerimine ja piiramine

```powershell
# Sorteeri protsessid CPU koormuse järgi kahanevalt (suuremad eespool)
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5

```

---

### 5. Torustik (The Pipeline `|`)

Torustik on PowerShelli võimsaim sümbol. See võtab vasakpoolse käsu tulemuse ja "valab" selle järgmisele käsule sisendiks.

**Näide:** `Get-Service | Where-Object {$_.Status -eq "Stopped"}`

* Samm 1: `Get-Service` võtab kõik teenused.
* Samm 2: `|` saadab need edasi.
* Samm 3: `Where-Object` jätab alles vaid need, mis seisavad.

---

### Iseseisvad harjutused (Soojendus)

1. **Süsteemi info:** Leia käsk, mis kuvab arvuti praeguse kuupäeva ja kellaaja.
2. **Teenuste kontroll:** Kasuta käsku `Get-Service`. Kuidas saaksid kuvada ainult need teenused, mis praegu **töötavad** (Status: Running)?
3. **Abi otsimine:** Kasuta `Get-Help`, et teada saada, millist parameetrit on vaja käsul `Get-ChildItem`, et näha ka peidetud faile (*hidden files*).
4. **Protsesside sorteerimine:** Kuvage kõik arvutis jooksvad protsessid ja sorteerige need nime järgi tähestikulises järjekorras.

---

## Iseseisev praktiline töö: "Süsteemi audiitor"

**Ülesanne:** Koosta PowerShelli skript (`audit.ps1`), mis teostab süsteemi kiire kontrolli ja salvestab tulemused faili.

**Nõuded:**

1. **Suured failid:** Kuva kõik failid oma `Documents` kaustas, mis on suuremad kui 10MB.
2. **Ressursikulu:** Leia 5 kõige rohkem protsessorit (CPU) koormavat protsessi ja järjesta need kahanevalt.
3. **Võrguteenused:** Leia kõik teenused, mille nimes sisaldub sõna "Network", ja kontrolli nende staatust.
4. **Raport:** Suuna kogu see info tekstifaili nimega `Audit_Raport.txt`.
* *Vihje:* Kasuta märki `>` info ülekirjutamiseks või `>>` info lisamiseks faili lõppu.



**Esitamiseks:**

* Esita oma skripti kood.
* Lisa lühike selgitus iga käsu juurde (mida see teeb).
* Lisa kuvatõmmis (screenshot) loodud `Audit_Raport.txt` sisu kohta.

---
