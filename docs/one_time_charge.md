# Egyszeri díj fizetés (One Time Charge)

## Tulajdonságok

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


## Folyamat ábra

![One Time Charge](../image/One%20time%20charge%20flow.png)