# Teszt üzemmód

Lehetőséget adunk arra, hogy a Billing API integrációját - annak fejlesztése közben - biztonságosan lehessen tesztelni.
Ahhoz, hogy egy fizetést teszt üzemmódban indítsuk el, csak annyi a dolgunk, hogy amikor létrehozunk egy új fizetést,
(Egyszeri vagy Ismétlődő) a payload-nak tartalmaznia kell a **test** tulajdonságot **true** értékkel.

Példa POST adatokra recurring charge esetén:

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

Ebben az esetben a bankkártyás fizetés is sandbox üzemmódban indul, illetve számlakiállítás nem történik,
hanem a bolt számlázi adataiban megadott email címre egy számla adat összesítőt küld ki a rendszer,
hogy ellenőrízhetővé válljon, hogy éles fizetés esetén, milyen adatokkal állítódna ki a számla.

Teszt Barion kártyaadatok:
https://docs.barion.com/Sandbox