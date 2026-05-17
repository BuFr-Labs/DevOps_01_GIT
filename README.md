# DevOps_01_GIT
Repozitoř k 1. lekci

# DevOps Úkol 01: Distribuovaný Git a Air-Gapped Workflow

Tento repozitář obsahuje řešení domácího úkolu zaměřeného na pokročilou práci s verzovacím systémem Git v simulovaném firemním prostředí se zvýšenou bezpečností (Air-Gapped environment / Bankovní sektor).

## 🎯 Cíle projektu

* **Simulace Air-Gapped workflow:** Přenos zdrojového kódu a kompletní historie mezi izolovaným produkčním prostředím (Datacentrum / Složka 1) a vývojářskou stanicí s přístupem k internetu (Notebook / Složka 2).
* **Multi-Remote topologie:** Konfigurace a správa více vzdálených cílů (`remotes`) v rámci lokálního souborového systému i cloudu (GitHub).
* **Pokročilá správa historie (Rebase):** Lineární narovnání větvené historie a manuální řešení konfliktů v textových souborech při souběžné práci.
* **Práce s větvemi:** Vytvoření a izolace experimentálních funkcí v samostatných branchích (`branch-test`).

## 📂 Struktura projektu

Projekt demonstruje decentralizovanou povahu Gitu, kde každá složka funguje jako plnohodnotný uzel:
* **DevOps_01_GIT/** – Simulace zabezpečeného vnitřního datacentra (DC). Místo, kde vzniká primární kód. Má nastavený remote směřující na přenosový uzel.
* **DevOps_01_GIT_clone/** – Simulace přenosového notebooku/flashky. Funguje jako komunikační brána (most), která stahuje změny z DC a jako jediná má právo pushovat do internetu na GitHub.
* **textfile.txt** – Hlavní sledovaný soubor, na kterém byl demonstrován vznik konfliktu a jeho následné vyřešení.

## 🚀 Návod k použití a simulace workflow

### 1. Práce uvnitř izolovaného DC (Složka 1)
Vytvoření souboru a provedení offline commitu:
```bash
cd ~/DevOps/DevOps_01_GIT
echo "Test offline commitu z DC v Bance" >> textfile.txt
git add textfile.txt
git commit -m "Pridan radek: Test offline commitu z DC v Bance"
```

### 2. Přenos dat a narovnání historie na Notebooku (Složka 2)
Přepnutí na hlavní větev, přitažení offline změn z DC serveru a vyřešení souběžně vzniklých úprav pomocí rebase:

```Bash
cd ~/DevOps/DevOps_01_GIT_clone
git checkout main
git pull dc_server main --rebase
```

Poznámka: V případě konfliktu v souboru textfile.txt se manuálně odstraní konfliktové značky (<<<<, ====, >>>>), soubor se označí jako vyřešený přes git add a proces se dokončí pomocí git rebase --continue.

### 3. Odeslání dat do cloudu (GitHub)
Načtení SSH klíče do agenta pro autorizaci a finální push bezpečně spojené historie na GitHub:

```Bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
git push origin main
```

### 4. Kontrola stavu a historie
Ověření, že historie je lineární, čistá a synchronizovaná se vzdáleným repozitářem:

```Bash
git log --oneline --graph --all
```

### 🔒 Bezpečnost a Best Practices
Izolace DC: Lokální repozitář v bance (Složka 1) nikdy nekomunikuje přímo s internetem, čímž je chráněn před vnějšími útoky.

Lineární historie: Namísto vytváření nepřehledných "Merge commitů" byl striktně využit git rebase. Výsledný strom repozitáře je čistý a snadno čitelný pro code-review.

SSH Autentizace: Přístup na GitHub je zabezpečen privátním klíčem chráněným heslem (passphrase), který je bezpečně spravován pomocí ssh-agent.