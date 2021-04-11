---
title: Konfigurera den sammanställda Azure HPC cache-namnrymden
description: Så här skapar du klientbaserade sökvägar för Server dels lagring med Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: 5427389f007b7598274d35425a9b3e8e10a63e49
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798535"
---
# <a name="set-up-the-aggregated-namespace"></a>Konfigurera sammanställd namnrymd

När du har skapat lagrings mål måste du också skapa namn områdes Sök vägar för dem. Klient datorer använder dessa virtuella sökvägar för att komma åt filer via cacheminnet i stället för att ansluta till Server delens lagrings plats direkt. Med det här systemet kan cache-administratörer ändra backend-baserade lagrings system utan att behöva skriva om klient instruktioner.

Läs [planera det sammanställda namn området](hpc-cache-namespace.md) för att lära dig mer om den här funktionen.

**Namn områdes** sidan i Azure Portal visar de sökvägar som klienter använder för att komma åt dina data via cacheminnet. Använd den här sidan om du vill skapa, ta bort eller ändra namn områdes Sök vägar. Du kan också konfigurera namn områdes Sök vägar med hjälp av Azure CLI.

Alla klient Sök vägar som har definierats för denna cache visas på **namn områdes** sidan. Lagrings mål som inte har några definierade namn rymds Sök vägar visas ännu inte i tabellen.

Du kan sortera tabell kolumnerna för att bättre förstå cacheminnets sammanställda namnrymd. Klicka på pilarna i kolumn rubrikerna för att sortera Sök vägarna.

[![skärm bild av sidan med Portal namn område med två sökvägar i en tabell. Kolumn rubriker: sökväg till namnrymd, lagrings mål, export Sök väg, och export-under katalog samt klient åtkomst princip. Sök vägs namnen i den första kolumnen är klicknings bara länkar. Topp knappar: Lägg till sökväg för namnrymd, uppdatera, ta bort ](media/namespace-page.png)](media/namespace-page.png#lightbox)

## <a name="add-or-edit-namespace-paths"></a>Lägga till eller redigera namn områdes Sök vägar

Du måste skapa minst en namn områdes Sök väg innan klienterna kan komma åt lagrings målet. (Läs genom [att montera Azure HPC cache](hpc-cache-mount.md) för mer information om klient åtkomst.)

Om du nyligen har lagt till ett lagrings mål eller anpassat en åtkomst princip kan det ta en minut eller två innan du kan skapa en namn områdes Sök väg.

### <a name="blob-namespace-paths"></a>Sökväg till BLOB-namnrymd

Ett Azure Blob Storage-mål kan bara ha en namn områdes Sök väg.

Följ anvisningarna nedan för att ange eller ändra sökvägen med Azure Portal eller Azure CLI.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Läs in sidan **namn rymds** inställningar från Azure Portal. Du kan lägga till, ändra eller ta bort namn områdes Sök vägar från den här sidan.

* **Lägg till en ny sökväg:** Klicka på knappen **+ Lägg till** överst och fyll i informationen på Redigera-panelen.

  ![Skärm bild av Lägg till-namn område redigera fält med ett Blob Storage-mål valt. Sökvägarna export och under katalog är inställda på/och kan inte redige ras.](media/namespace-add-blob.png)

  * Ange sökvägen som klienter ska använda för att få åtkomst till det här lagrings målet.

  * Välj vilken åtkomst princip som ska användas för den här sökvägen. Läs mer om hur du anpassar klient åtkomst i [använda klient åtkomst principer](access-policies.md).

  * Välj lagrings målet i den nedrullningsbara listan. Om ett Blob Storage-mål redan har en namn områdes Sök väg kan du inte välja det.

  * För ett Azure Blob Storage-mål ställs export-och under katalog Sök vägar automatiskt till ``/`` .

* **Ändra en befintlig sökväg:** Klicka på namn områdes Sök vägen. Redigerings panelen öppnas. Du kan ändra sökvägen och åtkomst principen, men du kan inte ändra till ett annat lagrings mål.

* **Ta bort en sökväg för namn område:** Markera kryss rutan till vänster om sökvägen och klicka på knappen **ta bort** .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC-cache](./az-cli-prerequisites.md).

När du använder Azure CLI måste du lägga till en namn områdes Sök väg när du skapar lagrings målet. Läs [Lägg till ett nytt Azure Blob Storage-mål](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-azure-blob-storage-target) för mer information.

Om du vill uppdatera målets namn områdes Sök väg använder du kommandot [AZ HPC-cache Blob-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) . Argumenten för kommandot Update liknar argumenten i kommandot CREATE, förutom att du inte överför behållar namnet eller lagrings kontot.

Du kan inte ta bort en namn områdes Sök väg från ett Blob Storage-mål med Azure CLI, men du kan skriva över sökvägen med ett annat värde.

---

### <a name="nfs-namespace-paths"></a>Sökväg till NFS-namnrymd

Ett NFS-lagrings mål kan ha flera virtuella sökvägar, så länge varje sökväg representerar en annan export-eller under katalog på samma lagrings system.

När du planerar ditt namn område för ett NFS-lagrings mål bör du tänka på att varje sökväg måste vara unik och får inte vara en under katalog till en annan namn områdes Sök väg. Om du till exempel har en sökväg för namn område som kallas ``/parent-a`` kan du inte också skapa namn områdes Sök vägar som ``/parent-a/user1`` och ``/parent-a/user2`` . Dessa katalog Sök vägar är redan tillgängliga i namn området som under kataloger i ``/parent-a`` .

Alla Sök vägs Sök vägar för ett NFS-lagrings system skapas på ett lagrings mål. De flesta cache-konfigurationer har stöd för upp till tio namn rymds Sök vägar per lagrings mål, men större konfigurationer kan ha stöd för upp till 20.

I den här listan visas det maximala antalet sökvägar i namn område per konfiguration.

* Upp till 2 GB/s-genomflöde:

  * 3 TB cache-10 namn områdes Sök vägar
  * 6 TB cache-10 namn områdes Sök vägar
  * 12 TB cache-20 namn områdes Sök vägar

* Upp till 4 GB/s-genomflöde:

  * 6 TB cache-10 namn områdes Sök vägar
  * 12 TB cache-10 namn områdes Sök vägar
  * 24 TB cache – 20 namn rymds Sök vägar

* Upp till 8 GB/s-genomflöde:

  * 12 TB cache-10 namn områdes Sök vägar
  * 24 TB cache-10 namn områdes Sök vägar
  * 48 TB cache – 20 namn rymds Sök vägar

För varje NFS-namnrymd, ange sökvägen till klienten, lagrings system exporten och eventuellt en export under katalog.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Läs in sidan **namn rymds** inställningar från Azure Portal. Du kan lägga till, redigera eller ta bort namn områdes Sök vägar från den här sidan.

* **Så här lägger du till en ny sökväg:** Klicka på knappen **+ Lägg till** överst och fyll i informationen på Redigera-panelen.
* **Så här ändrar du en befintlig sökväg:** Klicka på namn områdes Sök vägen. Redigerings panelen öppnas och du kan ändra sökvägen.
* **Så här tar du bort en namn områdes Sök väg:** Markera kryss rutan till vänster om sökvägen och klicka på knappen **ta bort** .

Fyll i följande värden för varje namn områdes Sök väg:

* **Namn områdes Sök** väg – den klientbaserade fil Sök vägen.

* **Klient åtkomst princip** – Välj vilken åtkomst princip som ska användas för den här sökvägen. Läs mer om hur du anpassar klient åtkomst i [använda klient åtkomst principer](access-policies.md).

* **Lagrings mål** – om du skapar en ny namn områdes Sök väg väljer du ett lagrings mål i den nedrullningsbara menyn.

* **Exportera sökväg** – ange sökvägen till NFS-exporten. Se till att skriva export namnet korrekt – portalen validerar syntaxen för det här fältet, men kontrollerar inte exporten förrän du skickar ändringen.

* **Exportera under katalog** – om du vill att den här sökvägen ska montera en speciell under katalog för exporten anger du den här. Annars lämnar du fältet tomt.

![skärm bild av sidan för portalens namn område med redigerings sidan öppen till höger. Redigerings formuläret visar inställningar för en sökväg för NFS-lagringsplatsens namn område](media/namespace-edit-nfs.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC-cache](./az-cli-prerequisites.md).

När du använder Azure CLI måste du lägga till minst en namn områdes Sök väg när du skapar lagrings målet. Läs [Lägg till ett nytt NFS-mål](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) för mer information.

Om du vill uppdatera målets namn områdes Sök väg eller lägga till ytterligare sökvägar använder du kommandot [AZ HPC-cache-NFS-cache-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) . Använd ``--junction`` alternativet för att ange alla sökvägar i namn området som du vill använda.

Alternativen som används för kommandot Update liknar kommandot "skapa", förutom att du inte skickar information om lagrings system (IP-adress eller värdnamn) och användnings modellen är valfri. Läs [Lägg till ett nytt NFS-mål](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) för mer information om syntaxen för ``--junction`` alternativet.

---

### <a name="adls-nfs-namespace-paths-preview"></a>Sökvägar för ADLS-NFS-namnrymd (för hands version)

Precis som ett vanligt Blob Storage-mål har ett ADLS-NFS-mål bara en export, så det kan bara ha en namn områdes Sök väg.

Följ anvisningarna nedan för att ange eller ändra sökvägen med Azure Portal.

Läs in sidan **namn rymds** inställningar.

* **Lägg till en ny sökväg:** Klicka på knappen **+ Lägg till** överst och fyll i informationen på Redigera-panelen.

  ![Skärm bild av redigera fält för Lägg till namnrymd med ett ADLS-NFS valt. Sökvägarna export och under katalog är inställda på/och kan inte redige ras.](media/namespace-add-adls.png)

  * Ange sökvägen som klienter ska använda för att få åtkomst till det här lagrings målet.

  * Välj vilken åtkomst princip som ska användas för den här sökvägen. Läs mer om hur du anpassar klient åtkomst i [använda klient åtkomst principer](access-policies.md).

  * Välj lagrings målet i den nedrullningsbara listan. Om det redan finns en namn områdes Sök väg för ett ADLS-NFS-lagrings mål kan du inte välja det.

  * För ett ADLS-NFS-lagrings mål anges export-och under katalog Sök vägar automatiskt till ``/`` .

* **Ändra en befintlig sökväg:** Klicka på namn områdes Sök vägen. Redigerings panelen öppnas. Du kan ändra sökvägen och åtkomst principen, men du kan inte ändra till ett annat lagrings mål.

* **Ta bort en sökväg för namn område:** Markera kryss rutan till vänster om sökvägen och klicka på knappen **ta bort** .

## <a name="next-steps"></a>Nästa steg

När du har skapat den sammanställda namn rymden för dina lagrings mål kan du montera klienter i cacheminnet. Läs de här artiklarna om du vill veta mer.

* [Montera Azure HPC Cache](hpc-cache-mount.md)
* [Flytta data till Azure Blob Storage](hpc-cache-ingest.md)
