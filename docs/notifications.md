# Értesítés webhookok - a notificationUrl

A Billing API, a fizetési folyamat közben történő események üzeneteit a notificationUrl tulajdonságnál megadott URL-re küldi el.
A webhook tartalmazza a fizetési mód azonosítóját és a aktuális állapotát. A Billing API által küldött webhook kérés HTTP metódusa **POST**.

A státuszok vizsgálatával következtethetünk egy fizetési mód életciklusában bekövetkezett változásokra, esetleges hibákra.
A lehetséges státuszok itt érhetőek el: [Elérhető státuszok](../docs/statuses.md)

## Az üzenet felépítése

| Tulajdonság | Leírás                                                |
|-------------|-------------------------------------------------------|
| id          | Fizetés azonosítója. Ez lehet One Time vagy Recurring |
| status   |    A fizetési mód aktuális állapota |

## Példa:

```javascript
{
    "id": 2,
    "status": "pending"
}
```
