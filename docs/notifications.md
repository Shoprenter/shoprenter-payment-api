# Értesítés webhookok - a notificationUrl

A Billing API a fizetési folyamat közben történő események üzeneteit a notificationUrl tulajdonságnál megadott URL-re küldi el.
Ezek lehetnek a folyamat sikerességét jelző üzenetek, vagy a felmerülő hibák jelzései.

## Válasz üzenet felépítése

| Tulajdonság | Leírás                                                |
|-------------|-------------------------------------------------------|
| id          | Fizetés azonosítója. Ez lehet One Time vagy Recurring |
| status   |    Ez lesz a fizetési mód aktuális állapota              |               |