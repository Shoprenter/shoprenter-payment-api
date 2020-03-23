## Kezdeti lépések

Mivel a fizetési tervek létrehozásához API-t használunk, minden esetben a fejlesztőnek kell biztosítani egy felületet az alkalmazásán belül,
ahol a bolt tulajdonos elindíthatja a fizetési folyamatot. Pl.: Ha az Ismétlődő díj fizetést vesszük alapul, az alkalmazásunk tartalmazhat
egy olyan aloldalt, ahol különböző havi díjas csomagokat kínálhatunk. A csomag kiválasztása után, annak megfelelően POST kérést küld
az alkalmazás a Billing API felé, hogy hozzon létre egy Ismétlődő díj fizetést.

Amire szükség lesz tehát:

- Vásárlás elindításáért felelős aloldal ahol az összes elérhető fizetés lehetőség megtekinthető. Itt ki kell listázni az adott csomagokhoz tartozó funkciókat is!

- notificationUrl: Az API a fizetés közben történt eseményekről fog küldeni ide webhookot. Ezen események kezeléséről is kivetelezni kell

- failedUrl és successUrl: Itt olyan oldalakra kell irányítani a bolt tulajdonost, ahol tajékoztatást kap a fizetés kimeneteléről