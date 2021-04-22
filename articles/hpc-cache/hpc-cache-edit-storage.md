---
title: Uppdatera Azure HPC Cache lagringsmål
description: Så här redigerar du Azure HPC Cache lagringsmål
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: ebf68c1eb06984e2de8114c53e1bb55d52aed70a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862641"
---
# <a name="edit-storage-targets"></a>Redigera lagringsmål

Du kan ta bort eller ändra lagringsmål med Azure Portal eller med hjälp av Azure CLI.

Beroende på typen av lagring kan du ändra dessa lagringsmålvärden:

* För Blob Storage-mål kan du ändra sökvägen för namnområdet och åtkomstprincipen.

* För NFS-lagringsmål kan du ändra följande värden:

  * Sökvägar för namnområde
  * Åtkomstprincip

  * Lagringsexport- eller exportunderkatalogen som är associerad med en namnområdessökväg
  * Användningsmodell

* För ADLS-NFS-lagringsmål kan du ändra sökvägen för namnområdet, åtkomstprincipen och användningsmodellen.

Du kan inte redigera lagringsmålnamnet, typen eller backend-lagringssystemet (blobcontainer eller NFS-värdnamn/IP-adress). Om du behöver ändra dessa egenskaper tar du bort lagringsmålet och skapar en ersättning med det nya värdet.

> [!TIP]
> Videon [Hantera Azure HPC Cache visar](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) hur du redigerar ett lagringsmål i Azure Portal.

## <a name="remove-a-storage-target"></a>Ta bort ett lagringsmål

### <a name="portal"></a>[Portal](#tab/azure-portal)

Om du vill ta bort ett lagringsmål öppnar **du sidan Lagringsmål.** Välj lagringsmålet i listan och klicka på knappen **Ta** bort.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC Cache](./az-cli-prerequisites.md).

Använd [az hpc-cache storage-target remove för](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage_target_remove) att ta bort ett lagringsmål från cachen.

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

Om du tar bort ett lagringsmål tas lagringssystemets association med Azure HPC Cache lagringssystemet bort, men det ändrar inte lagringssystemet i backend-systemet. Om du till exempel har använt en Azure Blob Storage-container finns containern och dess innehåll fortfarande kvar när du har tagit bort den från cachen. Du kan lägga till containern i en Azure HPC Cache, lägga till den i cacheminnet igen eller ta bort den med Azure Portal.

Alla filändringar som lagras i cachen skrivs till backend-lagringssystemet innan lagringsmålet tas bort. Den här processen kan ta en timme eller mer om många ändrade data finns i cacheminnet.

## <a name="change-a-blob-storage-targets-namespace-path"></a>Ändra sökvägen för ett Blob Storage-måls namnområde

Sökvägar för namnområden är sökvägarna som klienter använder för att montera lagringsmålet. (Mer information finns i [Planera det aggregerade namnområdet](hpc-cache-namespace.md) [och Konfigurera det aggregerade namnområdet](add-namespace-paths.md)).

Sökvägen till namnområdet är den enda uppdatering som du kan göra för ett Azure Blob Storage-mål. Använd Azure Portal eller Azure CLI för att ändra den.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Använd sidan **Namnområde** för din Azure HPC Cache. Namnområdessidan beskrivs i detalj i artikeln Konfigurera [det aggregerade namnområdet](add-namespace-paths.md).

Klicka på namnet på den sökväg som du vill ändra och skapa den nya sökvägen i redigeringsfönstret som visas.

![Skärmbild av namnområdessidan när du har klickat på en sökväg för blobnamnområdet – redigeringsfälten visas i ett fönster till höger](media/update-namespace-blob.png)

När du har gjort ändringarna klickar du **på OK** för att uppdatera lagringsmålet eller klickar på **Avbryt för** att ta bort ändringarna.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC Cache](./az-cli-prerequisites.md).

Om du vill ändra namnområdet för ett bloblagringsmål med Azure CLI använder du kommandot [az hpc-cache blob-storage-target update](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_update). Endast `--virtual-namespace-path` värdet kan ändras.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>Uppdatera ett NFS-lagringsmål

För NFS-lagringsmål kan du ändra eller lägga till sökvägar för virtuellt namnområde, ändra de NFS-export- eller underkatalogvärden som en namnrymdssökväg pekar på och ändra användningsmodellen.

Lagringsmål i cacheminnen med vissa typer av anpassade DNS-inställningar har också en kontroll för att uppdatera sina IP-adresser. (Den här typen av konfiguration är sällsynt.)

Information finns nedan:

* [Ändra aggregerade namnområdesvärden](#change-aggregated-namespace-values) (virtuell namnrymdssökväg, åtkomstprincip, export och export-underkatalog)
* [Ändra användningsmodellen](#change-the-usage-model)
* [Uppdatera DNS](#update-ip-address-custom-dns-configurations-only)

### <a name="change-aggregated-namespace-values"></a>Ändra aggregerade namnområdesvärden

Du kan använda Azure Portal eller Azure CLI för att ändra sökvägen till det klientriktade namnområdet, lagringsexporten och underkatalogen export (om det används).

Läs riktlinjerna i Lägg [till sökvägar för NFS-namnrymd](add-namespace-paths.md#nfs-namespace-paths) om du behöver en påminnelse om hur du skapar flera giltiga sökvägar på ett lagringsmål.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Använd sidan **Namnområde** för din Azure HPC Cache uppdatera namnområdesvärden. Den här sidan beskrivs i detalj i artikeln [Konfigurera det aggregerade namnområdet](add-namespace-paths.md).

![skärmbild av portalens namnområdessida med NFS-uppdateringssidan öppen till höger](media/update-namespace-nfs.png)

1. Klicka på namnet på den sökväg som du vill ändra.
1. Använd redigeringsfönstret för att skriva in nya värden för virtuell sökväg, export eller underkatalog eller för att välja en annan åtkomstprincip.
1. När du har gjort ändringarna klickar du **på OK** för att uppdatera lagringsmålet eller **Avbryt för** att ta bort ändringarna.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC Cache](./az-cli-prerequisites.md).

Använd alternativet ``--junction`` i kommandot az [hpc-cache nfs-storage-target update](/cli/azure/hpc-cache/nfs-storage-target) för att ändra underkatalogen för namnområdessökväg, NFS-export eller export.

Parametern ``--junction`` använder följande värden:

* ``namespace-path`` – Den klientriktade virtuella filsökvägen
* ``nfs-export`` – Lagringssystemet exporteras för att associeras med den klientriktade sökvägen
* ``target-path`` (valfritt) – En underkatalog för exporten, om det behövs

Exempel: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

Du måste ange alla tre värden för varje sökväg i ``--junction`` -instruktionen. Använd de befintliga värdena för alla värden som du inte vill ändra.

Cachenamnet, lagringsmålnamnet och resursgruppen krävs också i alla uppdateringskommandon.

Exempelkommando:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>Ändra användningsmodellen

Användningsmodellen påverkar hur cachen behåller data. Läs [Förstå cacheanvändningsmodeller om](cache-usage-models.md) du vill veta mer.

> [!NOTE]
> Om du ändrar användningsmodeller kan du behöva återmontera klienter för att undvika NLM-fel. Mer [information finns i Veta när klienter ska återmonteras.](cache-usage-models.md#know-when-to-remount-clients-for-nlm)

Om du vill ändra användningsmodellen för ett NFS-lagringsmål använder du någon av dessa metoder.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Ändra användningsmodellen från **sidan Lagringsmål** i Azure Portal. Klicka på namnet på lagringsmålet som ska ändras.

![skärmbild av redigeringssidan för ett NFS-lagringsmål](media/edit-storage-nfs.png)

Använd listr listrutan för att välja en ny användningsmodell. Klicka **på OK** för att uppdatera lagringsmålet eller klicka på Avbryt **för** att ta bort ändringarna.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC Cache](./az-cli-prerequisites.md).

Använd kommandot [az hpc-cache nfs-storage-target update.](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_update)

Uppdateringskommandot är nästan identiskt med kommandot som du använder för att lägga till ett NFS-lagringsmål. Mer information [och exempel finns i Skapa ett NFS-lagringsmål.](hpc-cache-add-storage.md#create-an-nfs-storage-target)

Om du vill ändra användningsmodellen uppdaterar du ``--nfs3-usage-model`` alternativet . Exempel: ``--nfs3-usage-model WRITE_WORKLOAD_15``

Cachenamn, lagringsmålnamn och resursgruppsvärden krävs också.

Om du vill kontrollera namnen på användningsmodellerna använder du kommandot [az hpc-cache usage-model list](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage-model-list).

Om cacheminnet stoppas eller inte är i felfritt tillstånd gäller uppdateringen när cachen är felfri.

---

### <a name="update-ip-address-custom-dns-configurations-only"></a>Uppdatera IP-adress (endast anpassade DNS-konfigurationer)

Om ditt cacheminne använder en DNS-konfiguration som inte är standard är det möjligt att IP-adressen för ditt NFS-lagringsmål ändras på grund av DNS-ändringar i serverslutet. Om DIN DNS-server ändrar lagringssystemets IP-adress kan Azure HPC Cache förlora åtkomst till lagringssystemet.

Vi rekommenderar att du samarbetar med cachehanterarens anpassade DNS-system för att planera för eventuella uppdateringar, eftersom dessa ändringar gör lagringen otillgänglig.

Om du behöver uppdatera ett lagringsmåls DNS-angivna IP-adress finns det en knapp i listan Lagringsmål. Klicka **på Uppdatera DNS** för att fråga den anpassade DNS-servern efter en ny IP-adress.

![Skärmbild av listan med lagringsmål. För ett lagringsmål är "..." menyn längst till höger är öppen och två alternativ visas: Ta bort och Uppdatera DNS.](media/refresh-dns.png)

Om uppdateringen lyckas bör uppdateringen ta mindre än två minuter. Du kan bara uppdatera ett lagringsmål i taget. vänta tills den föregående åtgärden har slutförts innan du provar en annan.

## <a name="update-an-adls-nfs-storage-target-preview"></a>Uppdatera ett ADLS-NFS-lagringsmål (FÖRHANDSVERSION)

På samma sätt som med NFS-mål kan du ändra sökvägen för namnområdet och användningsmodellen för ADLS-NFS-lagringsmål.

### <a name="change-an-adls-nfs-namespace-path"></a>Ändra en sökväg för ADLS-NFS-namnområdet

Använd sidan **Namnområde** för din Azure HPC Cache uppdatera namnområdesvärden. Den här sidan beskrivs i detalj i artikeln [Konfigurera det aggregerade namnområdet](add-namespace-paths.md).

![skärmbild av portalens namnområdessida med en ADS-NFS-uppdateringssida öppen till höger](media/update-namespace-adls.png)

1. Klicka på namnet på den sökväg som du vill ändra.
1. Använd redigeringsfönstret för att skriva in en ny virtuell sökväg eller uppdatera åtkomstprincipen.
1. När du har gjort ändringarna klickar du **på OK** för att uppdatera lagringsmålet eller **Avbryt för** att ta bort ändringarna.

### <a name="change-adls-nfs-usage-models"></a>Ändra användningsmodeller för ADLS-NFS

Konfigurationen för ADLS-NFS-användningsmodeller är identisk med valet av NFS-användningsmodell. Läs portalanvisningarna i [Ändra användningsmodellen](#change-the-usage-model) i NFS-avsnittet ovan. Ytterligare verktyg för att uppdatera ADLS-NFS-lagringsmål är under utveckling.


## <a name="next-steps"></a>Nästa steg

* Läs [Lägga till lagringsmål](hpc-cache-add-storage.md) om du vill veta mer om de här alternativen.
* Läs [Planera det aggregerade namnområdet för fler](hpc-cache-namespace.md) tips om hur du använder virtuella sökvägar.
