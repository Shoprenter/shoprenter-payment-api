## Elérés

A számlázó három ShopRenter API resource-on keresztül érhető el.
POST _**https://<shop_name>.api.shoprenter.hu/oneTimeCharges**_
POST _**https://<shop_name>.api.shoprenter.hu/recurringCharges**_
DELETE _**https://<shop_name>.api.shoprenter.hu/recurringCharges/{recurring_charge_id}**_

A Shoprenter Partner Dashboardon tekinthetőek meg a pénzügyi kimutatások és kezelhetőek a Fizetési tervek:
http://billing.shoprenter.hu/login

A login felületen, a már korábban említett regisztráció során kapott felhasználó név/token párossal tudunk belépni az alkalmazáshoz tartozó dashboardra.

FONTOS: Nem szükséges a meglévő alkalmazás telepítéseket megismételni a Billing API integrációja után.