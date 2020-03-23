# Alkalmazás törlése

Az alkalmazás törlés esetén, az adott boltban, a rendszer automatikusan megszünteti az aktuálisan futó, Ismétlőd díjfizetéseket **megszüntetjük**.
Ezután, az alkalmazás regisztrációjánál megadott uninstallUrl-re a ShopRenter küld egy **GET** kérést, ezzel jelezve a törlés tényét.