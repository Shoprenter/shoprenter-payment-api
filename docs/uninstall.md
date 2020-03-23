# Alkalmazás törlése

Az alkalmazás törlésénél, mint ahogy eddig is, az uninstallUrl-re érkezik egy webhook a ShopRentertől,
melyet az alkalmazásokhoz regisztrációjához kérünk az ügyfelektől. Annyiban lett bővítve az uninstall folyamat,
hogy az aktuálisan futó, Ismétlőd díj fizetéseket is **megszüntetjük**.