# Alkalmazás törlése

Az alkalmazás törlése esetén, az adott boltban a rendszer automatikusan megszünteti az aktuálisan futó Ismétlődő díjfizetéseket.
Ezután az alkalmazás regisztrációjánál megadott uninstallUrl-re a ShopRenter küld egy **GET** kérést, ezzel jelezve a törlés tényét.
