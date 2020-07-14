# Ismétlődő díjfizetés (Recurring Charge)

## Tulajdonságok

|Tulajdonság            |Leírás                                                                                                                                                 |Kötelező       |Olvasható            |
|-----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------:|:-------------------:|
|planId                 | Fizetési terv azonosító. (lásd: [Fizetési Terv](../docs/plan.md))                                                                                                        |       x       |          x          |
|billingCycleLength     | Fizetési ciklus hossza. Hány havonta történik díj fizetés. pl. az 1-es érték 1 hónapot jelent (30 nap), tehát havonta történik a levonás.             |               |          x          |
|billingCycleCount      | Fizetési ciklus száma. Hányszor történik ismételt díjfizetés. **Figyelem:** null esetén sose fog megállni a díjbeszedés!                              |               |          x          |
|expirationDate         | Lejárati dátum. Azt mutatja, mikor fogja a rendszer ismételni a díj beszedését                                                                        |               |          x          |
|id                     | Azonosító                                                                                                                                             |               |          x          |
|name                   | Név (pl.: Teljes verzió)                                                                                                                              |               |          x          |
|status                 | Státusz (lásd: [Státuszok](../docs/statuses.md))                                                                                                                             |               |          x          |
|price                  | Egy objektum, mely tartalmazza:                                                                                                                       |               |          x          |
|                       | **grossAmount**: Bruttó ár                                                                                                                            |               |                     |
|                       | **vatAmount**: ÁFA tartalom                                                                                                                           |               |                     |
|                       | **netPrice**: Nettó ár                                                                                                                                |               |                     |
|                       | **roundedGrossAmount**: Kerekített bruttó ár                                                                                                          |               |                     |
|netPrice               | Nettó ár                                                                                                                                              |               |          x          |
|notificationUrl        | A rendszerben történő eseményekről erre az URL-re küld az API értesítést                                                                              |               |          x          |
|successUrl             | A fizetés sikeressége esetén, ide irányítjuk a bolt tulajdonost                                                                                                |       x       |          x          |
|failedUrl              | A fizetés meghiúsulása esetén, ide irányítjuk a bolt tulajdonost                                                                                               |       x       |          x          |
|updatedAt              | Módosítás dátuma                                                                                                                                      |               |          x          |
|createdAt              | Létrehozás dátuma                                                                                                                                     |               |          x          |
|deletedAt              | Törlés dátuma                                                                                                                                         |               |          x          |
|test                   | Ha értéke **true**, akkor teszt üzemmódban történik a fizetés feldolgozása. Számlát nem állít ki. (Default: false)*                                                                                |               |          x          |
|confirmationUrl        | Létrehozás után, erre az URL-re kell irányítani a bolt tulajdonost                                                                                             |               |          x          |

\* A rendszer egy számlaadat-összesítő email-t küld ki a bolt számlázási adatainál megadott email címre.

# Belépési pont

POST https://<shop_name>.api.shoprenter.hu/billing/recurringCharges

Példa payload:

```javascript
{
    "planId": 1,
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
    "confirmationUrl": "https://<shop_name>.shoprenter.hu/admin/app/payment/recurring/5"
}
```

## Recurring Charge megszüntetése

A fejlesztőknek lehetőségük van megszüntetni a Recurring Charge futását, így lezárva a fizetési ciklust.
Egyszerűen, a fent taglalt belépési pontra, egy DELETE HTTP kérést kell küldeni a recurring charge ID-val.

DELETE https://<shop_name>.api.shoprenter.hu/billing/recurringCharges/<recurring_charge_id>

## Használat

Teljesen megegyezik a Egyszeri fizetés használatával. Egyedül a rendszeren belüli kezelésben különbözik.

### Működés példán keresztül
Szeretném az alkalmazásomat havi díjas konstrukcióban értékesíteni. Ehhez 3 féle havidíjas csomagot akarok hozzárendelni:
Bronze, Silver, Gold.
Ez 3 különböző fizetési tervet jelent, melynek eltérnek a nevei, az árai és esetleg a számlázási időszakai is.

A billing.shoprenter.hu/plans/create linken hozhatunk létre belépés után a terveket.

A Bronz csomagot havidíjassá szeretném tenni, egy éves időszakra adom 10000 nettó HUF-ért, "Az alkalmazásom Bronze csomagja" a neve.
Tehát a havi díjas Bronze csomagom a következőképpen kell kinézzen:

- Fizetési terv név: "Az alkalmazásom Bronze csomagja"
- Fizetési ciklusok hossza (hónapban): 1
- Fizetési ciklusok száma (ha üresen hagyja végtelen): 12
- Fizetési terv nettó ára forintban: 10000

Tehát, ha a bolt tulajdonos előfizet, havonta (billingCycleLength: 1) fizet 10000 HUF nettó összeget és ez a terhelés 12-szer (billingCycleCount: 12) történik meg.

Összefoglalva tehát: 1 évig havonta fizet 10000 HUF nettót a bolt tulajdonos az alkalmazásért (billingCycleLength * billingCycleCount).

**Figyelem**: Ha "Fizetési ciklusok száma" mezőt üresen hagyjuk, úgy ezen fizetési terv alapján készült Ismétlődő fizetés sose fog lejárni.

### Hiba esetén
Ha a tranzakció lebonyolítása alatt olyan hiba történik, amely a vásárló kártyaadatait érinti, pl. lejárt bankkártya, úgy a Recurring Charge státusza FROZEN státuszra vált. Ettől kezdve **15 napig** próbálja a rendszer az adott ismételt díjbeszedést végrehajtani.
Ha nem sikerül ezentúl se folytatni a díj beszedést, úgy a adott Recurring Charge-ot a rendszer CANCELLED státuszra állítja.

Természetesen itt is minden hibáról a notificationUrl értesítést kap az alkalmazás.

## Folyamat ábra

![One Time Charge](../image/Recurring%20Charge%20flow.png)
