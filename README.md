# semestralni_projekt_PMA_2026_TK
Repozitář pro semestrální projekt na předmět "Programování mobilních aplikací" pro rok 2026

# Záměr semestrálního projektu: aplikace „Hospoda"

## 1. Obecné představení aplikace
„Hospoda" je mobilní aplikace pro Android, která propojuje **hosty a provozovatele hospod** v jednom prostředí. Uživatel si může procházet a vyhledávat hospody, prohlížet jejich menu a otevírací dobu a sestavit si z nabídky objednávku. Stejný uživatel může zároveň sám **spravovat vlastní hospody**: zakládat je, upravovat informace a menu nebo je mazat.

Aplikace nerozlišuje pevné role. Každý přihlášený uživatel je zákazník a hospodským se stává tím, že si založí vlastní hospodu. Data jsou uložena v cloudu a mezi zařízeními se synchronizují v reálném čase.

Platba je simulovaná a objednávka se hospodě neodesílá, slouží jako osobní záznam útraty uživatele. Aplikace je určena pro **hosty hledající hospodu** a pro **provozovatele**, kteří chtějí svou nabídku jednoduše zveřejnit.

## 2. Funkcionality

**Společné**
- Registrace, přihlášení a odhlášení e-mailem a heslem.
- Dvě jazykové mutace uživatelského rozhraní: angličtina (výchozí) a čeština.

**Pohled zákazníka**
- Seznam všech hospod s **vyhledáváním podle názvu a města**.
- Detail hospody: název, adresa (text), kontakt (telefon, e-mail), otevírací doba po dnech a menu.
- Menu je řazeno do kategorií: předkrmy, hlavní jídla, přílohy, dezerty, pití, příplatkové položky.
- Přidávání položek do **košíku** (ikona plus). Košík je vždy jen z jedné hospody.
- Přehled košíku: položky, ceny, celková cena, souhlas s obchodními podmínkami a **simulovaná platba**.
- **Historie útrat** seskupená po dnech. Po rozkliknutí dne se zobrazí součet a seznam položek. Jednotlivý záznam lze z historie smazat.

**Pohled hospodského („Moje hospody")**
- Přidání nové hospody se všemi údaji.
- Úprava údajů hospody (adresa, kontakt, otevírací doba).
- Správa menu: přidání, úprava (název, cena, kategorie) a smazání položky.
- Smazání hospody, při kterém se odstraní i celé její menu. Historie zákazníků zůstane zachována.
- Hospodu může upravovat jen její vlastník, uživatel může vlastnit více hospod.

## 3. Použité technologie
- Android (telefon)
- Kotlin
- Jetpack Compose (Material 3)
- Navigation Compose
- MVVM (ViewModel, StateFlow, repository)
- Firebase Authentication
- Cloud Firestore
- Firestore Security Rules
- Git, GitHub

## 4. Využití databáze a CRUD operací
Data jsou uložena ve Firestore v těchto kolekcích:

- `users`: uživatel (uid, e-mail)
- `pubs`: hospoda (název, město, adresa, telefon, e-mail, otevírací doba po dnech, `ownerId`)
  - podkolekce `menuItems`: položka menu (název, cena, kategorie)
- `orders`: záznam útraty (`userId`, datum, hospoda, celková cena, **kopie položek** s názvem a cenou v době nákupu)

| Entita | Create | Read | Update | Delete |
|---|---|---|---|---|
| **Hospoda** | Přidání nové hospody | Seznam, vyhledávání, detail | Úprava údajů | Smazání hospody včetně menu |
| **Položka menu** | Přidání položky | Zobrazení menu po kategoriích | Změna názvu, ceny, kategorie | Smazání položky |
| **Útrata (objednávka)** | Potvrzení a zaplacení košíku | Historie po dnech a detail dne | – | Smazání záznamu z historie |
| **Uživatel** | Registrace | Přihlášení | – | – |

Seznam hospod a menu se aktualizují v reálném čase přes Firestore listenery, takže změna, kterou provede vlastník, se hned propíše všem uživatelům. Historie ukládá kopie položek, aby ji pozdější změna ceny nebo smazání položky či hospody neovlivnila.
