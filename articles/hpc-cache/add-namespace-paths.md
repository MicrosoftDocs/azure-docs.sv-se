---
title: Konfigurera den Azure HPC Cache aggregerade namnrymden
description: Så här skapar du klientriktade sökvägar för backend-lagring med Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: 2cb8db4e73a8f4fa299031070bffc15a2b754d7e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870381"
---
# <a name="set-up-the-aggregated-namespace"></a>Konfigurera det aggregerade namnområdet

När du har skapat lagringsmål måste du också skapa sökvägar för namnområdet för dem. Klientdatorer använder dessa virtuella sökvägar för att komma åt filer via cachen i stället för att ansluta direkt till serversidans lagring. Med det här systemet kan cacheadministratörer ändra backend-lagringssystem utan att behöva skriva om klientinstruktioner.

Läs [Planera det aggregerade namnområdet om du](hpc-cache-namespace.md) vill veta mer om den här funktionen.

På **sidan Namnområde** i Azure Portal sökvägar som klienter använder för att komma åt dina data via cachen. Använd den här sidan för att skapa, ta bort eller ändra sökvägar för namnområdet. Du kan också konfigurera sökvägar för namnområden med hjälp av Azure CLI.

Alla klientriktade sökvägar som har definierats för den här cachen visas på **sidan Namnområde.** Lagringsmål som inte har några definierade namnrymdssökvägar visas inte i tabellen.

Du kan sortera tabellkolumnerna för att bättre förstå cacheminnets aggregerade namnområde. Sortera sökvägarna genom att klicka på pilarna i kolumnrubrikerna.

[![skärmbild av portalnamnområdessidan med två sökvägar i en tabell. Kolumnrubriker: Namnområdessökväg, Lagringsmål, Exportsökväg och Exportera underkatalog och Klientåtkomstprincip. Sökvägsnamnen i den första kolumnen är klickbara länkar. Översta knappar: Lägg till namnområdessökväg, uppdatera, ta bort ](media/namespace-page.png)](media/namespace-page.png#lightbox)

## <a name="add-or-edit-namespace-paths"></a>Lägga till eller redigera sökvägar för namnområden

Du måste skapa minst en sökväg för namnområdet innan klienter kan komma åt lagringsmålet. (Läs [Montera Azure HPC Cache för](hpc-cache-mount.md) mer information om klientåtkomst.)

Om du nyligen har lagt till ett lagringsmål eller anpassat en åtkomstprincip kan det ta någon minut innan du kan skapa en sökväg för namnområdet.

### <a name="blob-namespace-paths"></a>Sökvägar för blobnamnområden

Ett Azure Blob Storage-mål kan bara ha en sökväg för namnområdet.

Följ anvisningarna nedan för att ange eller ändra sökvägen med Azure Portal eller Azure CLI.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Från Azure Portal läser du in **sidan Namnområdesinställningar.** Du kan lägga till, ändra eller ta bort sökvägar för namnområdet från den här sidan.

* **Lägg till en ny sökväg:** Klicka på **knappen +** Lägg till längst upp och fyll i information i redigeringspanelen.

  ![Skärmbild av fälten för att lägga till namnområdesredigering med ett valt bloblagringsmål. Sökvägarna export och underkatalog anges till /och kan inte redigeras.](media/namespace-add-blob.png)

  * Ange sökvägen som klienterna ska använda för att komma åt lagringsmålet.

  * Välj vilken åtkomstprincip som ska användas för den här sökvägen. Läs mer om hur du anpassar klientåtkomst i [Använda klientåtkomstprinciper.](access-policies.md)

  * Välj lagringsmålet i listrutan. Om ett bloblagringsmål redan har en sökväg för namnområdet kan det inte väljas.

  * För ett Azure Blob Storage-mål anges sökvägarna export och underkatalog automatiskt till ``/`` .

* **Ändra en befintlig sökväg:** Klicka på sökvägen för namnområdet. Redigeringspanelen öppnas. Du kan ändra sökvägen och åtkomstprincipen, men du kan inte ändra till ett annat lagringsmål.

* **Ta bort en namnrymdssökväg:** Markera kryssrutan till vänster om sökvägen och klicka på knappen **Ta** bort.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC Cache](./az-cli-prerequisites.md).

När du använder Azure CLI måste du lägga till en sökväg för namnområdet när du skapar lagringsmålet. Mer [information finns i Lägga till ett nytt Azure Blob Storage-mål.](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-azure-blob-storage-target)

Om du vill uppdatera målets namnområdessökväg använder du kommandot [az hpc-cache blob-storage-target update.](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_update) Argumenten för uppdateringskommandot liknar argumenten i create-kommandot, förutom att du inte skickar containernamnet eller lagringskontot.

Du kan inte ta bort en namnrymdssökväg från ett bloblagringsmål med Azure CLI, men du kan skriva över sökvägen med ett annat värde.

---

### <a name="nfs-namespace-paths"></a>Sökvägar för NFS-namnområden

Ett NFS-lagringsmål kan ha flera virtuella sökvägar, så länge varje sökväg representerar en annan export eller underkatalog i samma lagringssystem.

När du planerar namnområdet för ett NFS-lagringsmål bör du komma ihåg att varje sökväg måste vara unik och inte får vara en underkatalog till en annan namnrymdssökväg. Om du till exempel har en namnrymdssökväg som heter kan du inte ``/parent-a`` skapa sökvägar för namnområdet som ``/parent-a/user1`` och ``/parent-a/user2`` . Dessa katalogsökvägar är redan tillgängliga i namnområdet som underkataloger i ``/parent-a`` .

Alla sökvägar för namnområdet för ett NFS-lagringssystem skapas på ett lagringsmål. De flesta cachekonfigurationer har stöd för upp till tio namnrymdssökvägar per lagringsmål, men större konfigurationer har stöd för upp till 20.

Den här listan visar det maximala antalet sökvägar för namnområden per konfiguration.

* Upp till 2 GB/s dataflöde:

  * 3 TB cache – 10 namnrymdssökvägar
  * 6 TB cache – 10 namnrymdssökvägar
  * 12 TB cache – 20 namnrymdssökvägar

* Upp till 4 GB/s dataflöde:

  * 6 TB cache – 10 namnrymdssökvägar
  * 12 TB cache – 10 namnrymdssökvägar
  * 24 TB cachelagring – 20 namnområdessökvägar

* Upp till 8 GB/s dataflöde:

  * 12 TB cache – 10 namnrymdssökvägar
  * 24 TB cache – 10 namnrymdssökvägar
  * 48 TB cache – 20 namnrymdssökvägar

För varje NFS-namnområdessökväg anger du den klientriktade sökvägen, lagringssystemet exporterar och eventuellt en exportunderkatalog.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Från Azure Portal läser du in **sidan Namnområdesinställningar.** Du kan lägga till, redigera eller ta bort sökvägar för namnområdet från den här sidan.

* **Så här lägger du till en ny sökväg:** Klicka på **knappen +** Lägg till längst upp och fyll i information i redigeringspanelen.
* **Så här ändrar du en befintlig sökväg:** Klicka på sökvägen för namnområdet. Redigeringspanelen öppnas och du kan ändra sökvägen.
* **Så här tar du bort en namnrymdssökväg:** Markera kryssrutan till vänster om sökvägen och klicka på knappen **Ta** bort.

Fyll i följande värden för varje namnområdessökväg:

* **Namnområdessökväg** – Den klientriktade filsökvägen.

* **Klientåtkomstprincip –** Välj vilken åtkomstprincip som ska användas för den här sökvägen. Läs mer om hur du anpassar klientåtkomst i [Använda klientåtkomstprinciper.](access-policies.md)

* **Lagringsmål** – Om du skapar en ny sökväg för namnområdet väljer du ett lagringsmål i den nedrullningsna menyn.

* **Exportera sökväg** – Ange sökvägen till NFS-exporten. Se till att ange exportnamnet korrekt – portalen validerar syntaxen för det här fältet men kontrollerar inte exporten förrän du skickar ändringen.

* **Exportera underkatalog –** Om du vill att den här sökvägen ska montera en specifik underkatalog för exporten anger du den här. Om inte, lämna det här fältet tomt.

![skärmbild av portalens namnområdessida med redigeringssidan öppen till höger. Redigeringsformuläret visar inställningar för en sökväg för NFS-lagringsmålområdet](media/namespace-edit-nfs.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC Cache](./az-cli-prerequisites.md).

När du använder Azure CLI måste du lägga till minst en sökväg för namnområdet när du skapar lagringsmålet. Mer [information finns i Lägga till ett nytt NFS-lagringsmål.](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target)

Om du vill uppdatera målets namnområdessökväg eller lägga till ytterligare sökvägar använder du kommandot [az hpc-cache nfs-storage-target update.](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_update) Använd alternativet ``--junction`` för att ange alla sökvägar för namnområdet som du vill använda.

Alternativen som används för uppdateringskommandot liknar kommandot "create", förutom att du inte skickar lagringssystemets information (IP-adress eller värdnamn) och användningsmodellen är valfri. Mer [information om alternativets](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) syntax finns i Lägga till ett nytt NFS-lagringsmål. ``--junction``

---

### <a name="adls-nfs-namespace-paths-preview"></a>SÖKVÄGAR för ADLS-NFS-namnområden (FÖRHANDSVERSION)

Precis som ett vanligt bloblagringsmål har ett ADLS-NFS-lagringsmål bara en export, så det kan bara ha en namnrymdssökväg.

Följ anvisningarna nedan för att ange eller ändra sökvägen med Azure Portal.

Läs in **sidan Namnområdesinställningar.**

* **Lägg till en ny sökväg:** Klicka på **knappen +** Lägg till längst upp och fyll i information i redigeringspanelen.

  ![Skärmbild av fälten för att lägga till namnområdesredigering med ett adls-NFS-lagringsmål valt. Sökvägarna export och underkatalog anges till /och kan inte redigeras.](media/namespace-add-adls.png)

  * Ange sökvägen som klienterna ska använda för att komma åt lagringsmålet.

  * Välj vilken åtkomstprincip som ska användas för den här sökvägen. Läs mer om hur du anpassar klientåtkomst i [Använda klientåtkomstprinciper.](access-policies.md)

  * Välj lagringsmålet i listrutan. Om ett ADLS-NFS-lagringsmål redan har en sökväg för namnområdet kan det inte väljas.

  * För ett ADLS-NFS-lagringsmål anges sökvägarna export och underkatalog automatiskt till ``/`` .

* **Ändra en befintlig sökväg:** Klicka på sökvägen för namnområdet. Redigeringspanelen öppnas. Du kan ändra sökvägen och åtkomstprincipen, men du kan inte ändra till ett annat lagringsmål.

* **Ta bort en namnrymdssökväg:** Markera kryssrutan till vänster om sökvägen och klicka på knappen **Ta** bort.

## <a name="next-steps"></a>Nästa steg

När du har skapat det aggregerade namnområdet för dina lagringsmål kan du montera klienter i cacheminnet. Läs de här artiklarna om du vill veta mer.

* [Montera Azure HPC Cache](hpc-cache-mount.md)
* [Flytta data till Azure Blob Storage](hpc-cache-ingest.md)
