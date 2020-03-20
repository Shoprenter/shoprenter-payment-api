# ShopRenter Billing API

## Leírás

A Billing API egy olyan eszköztár amely olyan kártyás fizetési lehetőséget ad az alkalmazásokhoz 
amivel gyorsabban konvertálhatja a ShopRenterből érkező lead-eket bárki. Egyszeri és ismétlődő díj
fizetés is eszközölhető vele, emellett az automatikus számlakiállításról is gondoskodik az alkalmazás
fejlesztők közbenjárása nélkül. Biztosítunk a fenti funkciók mellett egy grafikus felületet is.
Itt elérhetőek az egyes alkalmazások pénzügyi statisztikái, illetve hogy milyen bevételekre 
tett szert a befizetésekből. Továbbá a fizetési tervek létrehozását is könnyedén elvégezhetjük
innen, amely ismétlődő díjfizetés esetén sokban könnyíti meg a munkánkat.

## Szükséges beállítások

A Billing API használata előtt - ha már van alkalmazásunk - igényelnünk kell a partnersupport@shoprenter.hu
email címen keresztül egy felhasználó nevet és egy token-t. A ShopRenter munkatársai regisztrálják a kérést
és ezután használatba vehető az API. Ekkor egy felhasználó név és token párost készítünk el ami a ShopRenter
Partner Dashboard oldalra való belépést biztosítja.

A Shoprenter Partner Dashboardon tekinthetőek meg a pénzügyi kimutatások az alkalmazásra vonatkozóan és
 kezelhetőek a fizetési tervek is: https://billing.shoprenter.hu/login


**Fontos!** **A felhasználó név és jelszó alkalmazás specifikus!**
Tehát ha több alkalmazásunk van, minden alkalmazáshoz külön hozzáférést kell igényelni.


Az igényelt teszt bolt számlázási adatait minden esetben a megfelelő adatokkal töltsük ki, hiszen hiányos számlázási adatok esetén, a rendszer nem képes számlát kiállítani, így ezen beállítások hiánya a fizetések létrehozását is gátolja.
A legfontosabb adat az email cím, mivel teszt üzemmódban ide fogja kiküldeni a rendszer
a számlázási adat-összesítő teszt emailt.

## Elérés

A számlázó három ShopRenter API resource-on keresztül érhető el.
POST _**https://<shop_name>.api.shoprenter.hu/oneTimeCharges**_
POST _**https://<shop_name>.api.shoprenter.hu/recurringCharges**_
DELETE _**https://<shop_name>.api.shoprenter.hu/recurringCharges/{recurring_charge_id}**_

A Shoprenter Partner Dashboardon tekinthetőek meg a pénzügyi kimutatások és kezelhetőek a Fizetési tervek:
http://billing.shoprenter.hu/login

A login felületen, a már korábban említett regisztráció során kapott felhasználó név/token párossal tudunk belépni az alkalmazáshoz tartozó dashboardra.

FONTOS: Nem szükséges a meglévő alkalmazás telepítéseket megismételni a Billing API integrációja után.


## Kezdeti lépések

Mivel a fizetési tervek létrehozásához API-t használunk, minden esetben a fejlesztőnek kell biztosítani egy felületet az alkalmazásán belül,
ahol a bolt tulajdonos elindíthatja a fizetési folyamatot. Pl.: Ha az Ismétlődő díj fizetést vesszük alapul, az alkalmazásunk tartalmazhat
egy olyan aloldalt, ahol különböző havi díjas csomagokat kínálhatunk. A csomag kiválasztása után, annak megfelelően POST kérést küld
az alkalmazás a Billing API felé, hogy hozzon létre egy Ismétlődő díj fizetést.

Amire szükség lesz tehát:

- Vásárlás elindításáért felelős aloldal ahol az összes elérhető fizetés lehetőség megtekinthető. Itt ki kell listázni az adott csomagokhoz tartozó funkciókat is!

- notificationUrl: Az API a fizetés közben történt eseményekről fog küldeni ide webhookot. Ezen események kezeléséről is kivetelezni kell

- failedUrl és successUrl: Itt olyan oldalakra kell irányítani a bolt tulajdonost, ahol tajékoztatást kap a fizetés kimeneteléről

#A Billing rendszer folyamatai

A fizetési tervek hatékony átlátáshoz készítettünk egy folyamat ábrát amit lentebb csatoltunk.
*BERAKNI NORBIÉK ÁLTAL KÉSZÍTETT FOLYAMATÁBRÁT ÉS MAGYARÁZNI HA KELL*

#Egyszeri díj fizetés (One Time Charge)

##Tulajdonságok

|Tulajdonság            |Leírás                                                                                                                                         |Kötelező       |Olvasható            |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------|:-------------:|:-------------------:|
|id                     | Azonosító                                                                                                                                     |               |          x          |
|name                   | Név (pl.: Teljes verzió, Havi Gold csomag, stb.)                                                                                                                      |       x       |          x          |
|status                 | Státusz (lásd: Státuszok)                                                                                                                     |               |          x          |
|price                  | Egy objektum, mely tartalmazza:                                                                                                               |               |          x          |
|                       | **grossAmount**: Bruttó ár                                                                                                                    |               |                     |
|                       | **vatAmount**: ÁFA tartalom                                                                                                                   |               |                     |
|                       | **netPrice**: Nettó ár                                                                                                                        |               |                     |
|                       | **roundedGrossAmount**: Kerekített bruttó ár                                                                                                  |               |                     |
|netPrice               | Nettó ár                                                                                                                                      |       x       |          x          |
|paymentUrl             | Fizetési url. A fizetést kezelő szolgáltatás felületének az elérésére szolgál. (A ShopRenter automatikusan kezeli az erre való átírányítást)  |               |                     |
|notificationUrl        | A rendszerben történő eseményekről erre az Url-re küld az API webhook értesítést                                                                      |               |          x          |
|successUrl             | A fizetés sikeressége esetén, ide írányítjuk a bolt tulajdonost                                                                                      |       x       |          x          |
|failedUrl              | A fizetés meghiúsulása esetén, ide írányítjuk a bolt tulajdonost                                                                                     |       x       |          x          |
|updatedAt              | Módosítás dátuma                                                                                                                              |               |          x          |
|createdAt              | Létrehozás dátuma                                                                                                                             |               |          x          |
|deletedAt              | Törlés dátuma                                                                                                                                 |               |          x          |
|test                   | Ha értéke **true**, akkor teszt üzemmódban történik a fizetés feldolgozása. Számlát nem állít ki.*                                                                     |               |          x          |
|confirmationUrl        | Létrehozás után, erre az url-re kell írányítani a Vásárlót                                                                                    |               |          x          |

* Egy számla adat-összeítő emailt küld ki a rendszer bolt számlázi adataiban megadott email címre

#Belépési pont

POST https://<shop_name>.api.shoprenter.hu/oneTimeCharges

Példa payload:

```javascript
{
    "name": "ACME alkalmazás megvétele",
    "netPrice": 10000,
    "notificationUrl": "https://notification-webhook-url.com",
    "failedUrl": "https://failedUrl.com",
    "successUrl": "https://successUrl.com",
    "test": true
}
```

Erre adott válasz:

```javascript
{
    "id": 5,
        "name": "ACME alkalmazás megvétele",
        "status": "pending",
        "price": {
            "grossAmount": 12700,
            "vatAmount": 2700,
            "netPrice": 10000,
            "roundedGrossAmount": 12700
        },
        "netPrice": 10000,
        "paymentUrl": "",
        "notificationUrl": "https://notification-webhook-url.com",
        "successUrl": "https://successUrl.com",
        "failedUrl": "https://failedUrl.com",
        "updatedAt": "2020-02-24 15:13:15",
        "createdAt": "2020-02-24 15:13:15",
        "deletedAt": null,
        "test": true,
        "confirmationUrl": "https://<shop_name>.api.shoprenter.hu/admin/app/payment/onetime/5"
}
```

## Használat
1. Létrehozok POST kéréssel egy új oneTimeCharge-ot
2. A válaszban megkapom a confirmationUrl-t, melyre átírányítom az aktuális Vásárlót
3. A aktuális Vásárló átkerül a ShopRenter fizetést megerősítő oldalára
4. A aktuális Vásárló eldönti, hogy elfogadja, vagy elutasítja a vásárlást. Elutasítás esetén, az alkalmazás belépési pontjára írányít vissza a rendszer.
5. Ha elfogadta, átírányítódik a tranzakicót lebonyolító szolgáltatás felületére.
6. Sikeres fizetés esetén a successUrl-re írányítja a rendszer a Vásárlót, Sikertelenség esetén a failedUrl-re.

A folyamat közben történő eseményekről folyamatosan tájékoztatja a rendszer a alkalmazást
a notificationUrl-en keresztül.

### Működés példán keresztül
Szeretném az alkalmazásomat egyszeri telepítési díj ellenében biztosítani a ShopRenter-es boltok számára. A telepítési díjam nem különbözik, csak egy féle összeget kell beszednem.

A fizetésem tehát egyszeri fizetés (One Time Charge) formájában megvásárolható nettó 10000 HUF-ért. Mivel telepítési díjat szedünk ezért a csomag neve legyen "Telepítési díj".
A "Telepítési díj" csomag így a következő képpen fog kinézni:

name: "Telepítési díj"
*IDE NEM TUDOM MI KELL MÉG*
netPrice: 10000

```javascript
{
    "name": "Telepítési díj",
    "netPrice": 10000,
    "notificationUrl": "https://notification-webhook-url.com",
    "failedUrl": "https://failedUrl.com",
    "successUrl": "https://successUrl.com",
    "test": false
}
```

Tehát, ha a bolt tulajdonos előfizet, egyszeri alkalommal történik meg 10000 nettó HUF értékben.

# Fizetési terv az ismétlődő díjfizetéshez

## Fizetési tervek kezelése

A fizetési terveinket a https://billing.shoprenter/plans oldalon tudjuk szerkeszteni.
Bejelentkezés után listaszerűen láthatjuk a meglévő terveink, illetve újat adhatunk hozzá.

[Kép 1](/image/plan1.jpg)

[Kép 2](/image/plan2.jpg)

Megjegyzés: A lista oldalon, az Azonosító oszlopban lévő értékkel tudunk hivatkozni az egyes Fizetési tervekre,
amikor Ismétlődő díj fizetést akarunk létrehozni az API-n keresztül.



#Ismétlődő díj fizetés (Recurring Charge)

##Tulajdonságok

|Tulajdonság            |Leírás                                                                                                                                                 |Kötelező       |Olvasható            |
|-----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------:|:-------------------:|
|planId                 | Fizetési terv azonosító. (Lásd: Fizetési Terv)                                                                                                        |       x       |          x          |
|billingCycleLength     | Fizetési ciklus hossza. Hány havonta történik díj fizetés. pl. az 1-es érték 1 hónapot jelent (30 nap), tehát havonta történik a levonás.             |               |          x          |
|billingCycleCount      | Fizetési ciklus száma. Hányszor történik ismételt díj fizetés. **Figyelem!** null esetén sose fog megállni a díjbeszedés                              |               |          x          |
|expirationDate         | Lejárati dátum. Azt mutatja, mikor fogja a rendszer ismételni a díj beszedését                                                                        |               |          x          |
|id                     | Azonosító                                                                                                                                             |               |          x          |
|name                   | Név (pl.: Teljes verzió)                                                                                                                              |               |          x          |
|status                 | Státusz (lásd: Státuszok)                                                                                                                             |               |          x          |
|price                  | Egy objektum, mely tartalmazza:                                                                                                                       |               |          x          |
|                       | **grossAmount**: Bruttó ár                                                                                                                            |               |                     |
|                       | **vatAmount**: ÁFA tartalom                                                                                                                           |               |                     |
|                       | **netPrice**: Nettó ár                                                                                                                                |               |                     |
|                       | **roundedGrossAmount**: Kerekített bruttó ár                                                                                                          |               |                     |
|netPrice               | Nettó ár                                                                                                                                              |               |          x          |
|paymentUrl             | Fizetési url. A fizetést kezelő szolgáltatás felületének az elérésére szolgál. (A ShopRenter automatikusan kezeli az erre való átírányítást)          |               |                     |
|notificationUrl        | A rendszerben történő eseményekről erre az Url-re küld az API értesítést                                                                              |               |          x          |
|successUrl             | A fizetés sikeressége esetén, ide írányítjuk a Vásárlót                                                                                               |       x       |          x          |
|failedUrl              | A fizetés meghiúsulása esetén, ide írányítjuk a Vásárlót                                                                                              |       x       |          x          |
|updatedAt              | Módosítás dátuma                                                                                                                                      |               |          x          |
|createdAt              | Létrehozás dátuma                                                                                                                                     |               |          x          |
|deletedAt              | Törlés dátuma                                                                                                                                         |               |          x          |
|test                   | Ha értéke true, akkor teszt üzemmódban történik a fizetés feldolgozása                                                                                |               |          x          |
|confirmationUrl        | Létrehozás után, erre az url-re kell írányítani a Vásárlót                                                                                            |               |          x          |


#Belépési pont

POST https://<shop_name>.api.shoprenter.hu/recurringCharges

Példa payload:

```javascript
{
    "planId": 1,
    "returnUrl": "https://returnUrl.com",
    "notificationUrl": "https://notification-webhook-url.com",
    "failedUrl": "https://failedUrl.com",
    "successUrl": "https://successUrl.com",
    "test": true
}
```

Erre adott válasz:

```javascript
{
    "planId": 1,
    "billingCycleLength": 1,
    "billingCycleCount": 12,
    "expirationDate": null,
    "id": 5,
    "name": "ACME alkalmazás Gold Csomagja",
    "status": "pending",
    "price": {
        "grossAmount": 12700,
        "vatAmount": 2700,
        "netPrice": 10000,
        "roundedGrossAmount": 12700
    },
    "netPrice": 10000,
    "paymentUrl": "",
    "notificationUrl": "https://notification-webhook-url.com",
    "successUrl": "https://successUrl.com",
    "failedUrl": "https://failedUrl.com",
    "updatedAt": "2020-02-24 15:13:15",
    "createdAt": "2020-02-24 15:13:15",
    "deletedAt": null,
    "test": true,
    "confirmationUrl": "https://<shop_name>.api.shoprenter.hu/admin/app/payment/recurring/5"
}
```

##Használat

Teljesen megegyezik a Egyszeri fizetés használatával. Egyedül a rendszeren belüli kezelésben különbözik.

##Működése

Miután az első időszak kifezetése megtörtént a Vásárló részéről, a rendszer automatikusan kezeli az ismételt díj beszedést.
A Billing API **minden nap** ellenőrzi, mely Recurring Charge-ok azok, amelyeket ismételni kell az adott napon.

Minden díjbeszedés után a rendszer megállípítja, melyik az az időpont, amikor meg kell történnie a következő tranzakciónak.
Pl.: Ha Fizetési tervben a létrehozás dátuma (**createdAt**) 2020-10-01 és a **billingCycleLength** 3 (3 havonta történik a levonás), akkor következő esedékes
díjbeszedés 3 * 30 nap eltolással történik meg: 2020-12-30 (expirationDay)
**expirationDay** minden sikeres tranzakció után frissül a következő esedékes díjbeszedés dátumára.

### Hiba esetén
1. Ha a tranzakció lebonyolítása alatt olyan hiba történik, amely a vásárló kártyaadatait érinti, pl. lejárt bankkártya, úgy a Recurring Charge státusza FROZEN státuszra vált. **15 napig** próbálja a rendszer az adott ismételt díjbeszedést végrehajtani.
Ha nem sikerül ezentúl se folytatni a díj beszedést, úgy a adott Recurring Charge-ot a rendszer CANCELLED státuszra állítja.

2. Ha a bankkártyást fizetést bonyolító szolgáltatás oldalán történt átmenti hiba, akkor óránként 4x próbálkozik a rendszer a tranzakcióval.

Természetesen itt is minden hibáról a notificationUrl értesítést kap az alkalmazás.

#Státuszok

A két fizetési típushoz tartoznak státuszok, melyek az adott fizetés életciklusának állapotáról adnak információt.
Bár az első sikeres tranzakció után a két típusnak eltérő az életpályája, a fizetések által felvehető állapotok nagyrészt megegyeznek

|Státusz                    |Leírás                                                                                                                                                                                                                                         |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|PENDING                    | Az újonnan létrehozott fizetés státusza. Függőben lévő                                                                                                                                                                                        |
|ACCEPTED                   | Elfogadott vásárlás utáni állapot                                                                                                                                                                                                             |
|ACTIVE                     | Sikeres fizetés utáni állapot                                                                                                                                                                                                                 |
|DECLINED                   | Elutasított fizetés. Ez megtörténhet a fizetést megerősítő oldalon vagy a trankakciót lebonyolító szolgáltatás felületén                                                                                                                      |
|EXPIRED                    | Az az Ismételt díj fizetés kerülhet ebbe a státuszba, amely véglegesen lejárt. (Nem kell tovább beszedni a díjat)                                                                                                                             |
|FROZEN                     | Az Ismételt díj fizetés esetén jöhet elő, ha a tranzakció lebonyolítása közben olyan hiba lép fel, mely a Vásárló problémás banki adatai miatt következik be                                                                                  |
|FAILED                     | A bankkártyás fizetést lebonyolító szolgáltatásban történt olyan hiba, amely nem feloldható, nem folytatható                                                                                                                                  |
|CANCELLED                  | Ha az Ismételt díj fizetés FROZEN állapotú, 15 nap után - ha nem sikerült ACTIVE állapotra visszaállítani - úgy ebbe az állapotba kerül. Illetve ha fizetés direkt megszakításra kerül pl.: alkalmazás törlésénél, CANCELLED lesz az státusz  |

#Értesítések: notificationUrl

A Billing API a fizetési folyamat közben történő események üzeneteit a notificationUrl tulajdonságnál megadott URL-re küldi el.
Ezek lehetnek a folyamat sikerességét jelző üzenetek, vagy a felmerülő hibák üzenetei.

## Válasz üzenet felépítése

| Tulajdonság | Leírás                                                |
|-------------|-------------------------------------------------------|
| id          | Fizetés azonosítója. Ez lehet One Time vagy Recurring |
| successful  | Értéke true vagy false                                |
| errorCode   | Értéke:  0 (FAILED), 1 (DECLINED), 2 (FROZEN). Csak false successful tulajdonság esetén lesz a payload-ban|
| domain      | A bolt domain-je. Csak DECLINE státuszba kerülés esetén lesz a payload-ba|


#Alkalmazás törlése

Az alkalmazás törlésénél, mint ahogy eddig is, az uninstallUrl-re érkezik egy webhook a ShopRentertől,
melyet az alkalmazásokhoz regisztrációjához kérünk az ügyfelektől. Annyiban lett bővítve az uninstall folyamat,
hogy az aktuálisan futó, Ismétlőd díj fizetéseket is **megszüntetjük**.


#Teszt üzemmód

Lehetőséget adunk arra, hogy a Billing API integrációját - annak fejlesztése közben - biztonságosan lehessen tesztelni.
Ahhoz, hogy egy fizetést teszt üzemmódban indítsuk el, csak annyi a dolgunk, hogy amikor létrehozunk egy új fizetést,
Egyszeri vagy Ismétlődőt, a payload-nak tartalmaznia kell a **test** tulajdonságot **true** értékkel.

Ebben az esetben a bankkártyás fizetés is sandbox üzemmódban indul, illetve számlakiállítás nem történik,
hanem a bolt számlázi adataiban megadott email címre egy számla adat összesítőt küld ki a rendszer,
hogy ellenőrízhetővé válljon, hogy elés fizetés esetén, milyen adatokkal állítódna ki a számla.

Teszt Barion kártyaadatok:
https://docs.barion.com/Sandbox

#Számlázási adatok és ÁFA kalkuláció

A számlázi adatokat a ShopRenter automatikusan kéri le a Vásárló boltjából. Így külön nem igényel további intézkedést
az Alkalmazás fejlesztő részéről.

Amit viszont feltétlen tudni kell, az az, hogy a számlázási adatok hogyan befolyásolják az aktuális ÁFA kiszámítását.

### Ki állítja ki a számlát?

Minden esetben a ShopRenter nevében állítja ki a számlát a rendszer.

### Milyen esetekben térhet el a mindekori Magyar ÁFA tartalomtól a Bruttó végösszeg?

Két tényező határozza meg, még pedig a bolthoz bejegyzett **ország** és a **közösségi adószám** megléte.

**Eszerint 4 eset lehetséges:**
1. A bolt üzemeltetője magyarországi cég és nincs közösségi adószáma: **27% ÁFA**

2. A bolt üzemeltetője uniós tagországhoz tartozik de nincs közösségi adószáma: **27% ÁFA**

3. A bolt üzemeltetője uniós tagországhoz tartozik és van közösségi adószáma: **0%**

4. A bolt üzemeltetője 3. országbeli: **0%**

## Hiányos számlázási adatok

Előfordulthat, hogy az adott boltban, ahol a kérdéses alkalmazást telepíteni szeretnék,
hiányos számlázási adatokkal rendelkezik.
Tehát One Time és Recurring Charge létrehozásakor kaphatunk olyan hibaüzenetet,
hogy hiányzó számlázási adatokat lát a rendszer.

40019-es hibakód

**Az üzenet tartalmazni fog egy logId-t, amelyet a partnersupport@shoprenter.hu kell elküldeni,
hogy a ShopRenteres kollégák tudjanak intézkedni a hiányos adatok pótlásának az ügyében.**