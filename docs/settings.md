## Szükséges beállítások

A Payment API használata előtt - ha már van alkalmazásunk - igényelnünk kell a partnersupport@shoprenter.hu
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

Minden ide vonatkozó adatot a teszt bolt admin felületén a "Fiókom" menüpontban tudnak módosítani. Link: https://valami.shoprenter.hu/admin/sales/account#/billingAddress

Továbbá ugyancsak **fontos** megjegyezni, és felkészíteni alkalmazásunkat arra, hogy képes legyen az elkészített fizetési módok adatainak tárolására. Itt konkrétan az [Egyszeri díjfizetés](../docs/one_time_charge.md) és az [Ismétlődő díjfizetés](../docs/recurring_charge.md) létrehozása után kapott
válasz üzenetben szereplő adatokról van szó.
Ez azért nagyon fontos, hogy az alkalmazás fejlesztője éretelmezni tudja, hogy az [Értesítési webhookokban](../docs/notifications.md) érkező
ID mely fizetési módhoz tartozik.
