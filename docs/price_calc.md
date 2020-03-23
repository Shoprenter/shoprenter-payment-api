# Számlázási adatok és ÁFA kalkuláció

A számlázi adatokat a ShopRenter automatikusan kéri le a tulajdonos boltjából. Így külön nem igényel további intézkedést
az Alkalmazás fejlesztő részéről.

Amit viszont feltétlen tudni kell, az az, hogy a számlázási adatok hogyan befolyásolják az aktuális ÁFA kiszámítását.

### Ki állítja ki a számlát?

Minden esetben a ShopRenter nevében kerül kiállításra a számla.

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