---
title: Uppdatera lagrings mål för Azure HPC cache
description: Redigera lagrings mål för Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/10/2021
ms.author: v-erkel
ms.openlocfilehash: 78010ef2d93b23a12fc7f3e988a536b4993b4dd4
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471860"
---
# <a name="edit-storage-targets"></a>Redigera lagringsmål

Du kan ta bort eller ändra lagrings mål med Azure Portal eller med hjälp av Azure CLI.

Beroende på typ av lagring kan du ändra dessa värden för lagrings mål:

* För Blob Storage-mål kan du ändra namn områdets sökväg och åtkomst princip.

* För NFS-lagrings mål kan du ändra dessa värden:

  * Namn områdes Sök vägar
  * Åtkomstprincip

  * Under katalogen lagrings export eller export som är associerad med en namn områdes Sök väg
  * Användnings modell

* För ADLS-NFS-lagrings mål kan du ändra namn rymds Sök vägen, åtkomst principen och användnings modellen.

Du kan inte redigera ett lagrings måls namn, typ eller backend-filsystem (BLOB container eller NFS-värdnamn/IP-adress). Om du behöver ändra dessa egenskaper tar du bort lagrings målet och skapar en ersättning med det nya värdet.

> [!TIP]
> I [Hantera Azure HPC cache-video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) visas hur du redigerar ett lagrings mål i Azure Portal.

## <a name="remove-a-storage-target"></a>Ta bort ett lagrings mål

### <a name="portal"></a>[Portal](#tab/azure-portal)

Om du vill ta bort ett lagrings mål öppnar du sidan **lagrings mål** . Välj lagrings målet i listan och klicka på knappen **ta bort** .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC-cache](./az-cli-prerequisites.md).

Använd [AZ HPC-cache Storage – Target Remove](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove) för att ta bort ett lagrings mål från cachen.

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

Om du tar bort ett lagrings mål tas lagrings systemets koppling bort från det här Azure HPC cache-systemet, men det ändrar inte Server dels lagrings systemet. Om du till exempel använde en Azure Blob Storage-behållare finns behållaren och dess innehåll fortfarande kvar när du har tagit bort den från cachen. Du kan lägga till behållaren i en annan Azure HPC-cache, lägga till den i cacheminnet igen eller ta bort den med Azure Portal.

Alla fil ändringar som lagras i cacheminnet skrivs till Server dels lagrings systemet innan lagrings målet tas bort. Den här processen kan ta en timme eller mer om mycket ändrade data finns i cacheminnet.

## <a name="change-a-blob-storage-targets-namespace-path"></a>Ändra namn områdes Sök vägen för ett Blob Storage-mål

Namn områdes Sök vägar är de sökvägar som klienter använder för att montera det här lagrings målet. (Mer information finns i [planera den aggregerade namn rymden](hpc-cache-namespace.md) och [Konfigurera den sammanställda namn rymden](add-namespace-paths.md)).

Namn områdes Sök vägen är den enda uppdatering du kan göra i ett Azure Blob Storage-mål. Använd Azure Portal eller Azure CLI för att ändra det.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Använd **namn områdes** sidan för Azure HPC-cachen. Namn områdes sidan beskrivs mer detaljerat i artikeln Konfigurera sammanställd [namnrymd](add-namespace-paths.md).

Klicka på namnet på den sökväg som du vill ändra och skapa den nya sökvägen i fönstret Redigera som visas.

![Skärm bild av namn områdes sidan efter att du har klickat på en BLOB namespace-sökväg. redigerings fälten visas i ett fönster till höger](media/edit-namespace-blob.png)

När du har gjort ändringarna klickar du på **OK** för att uppdatera lagrings målet, eller klicka på **Avbryt** om du vill ignorera ändringarna.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC-cache](./az-cli-prerequisites.md).

Om du vill ändra ett Blob Storage Target-namnområde med Azure CLI använder du kommandot [AZ HPC-cache Blob-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update). Endast `--virtual-namespace-path` värdet kan ändras.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>Uppdatera ett NFS-lagrings mål

För NFS-lagrings mål kan du ändra eller lägga till sökvägar för virtuella namn områden, ändra de NFS-export-eller under katalog värden som en namn områdes Sök väg pekar på och ändra användnings modellen.

Lagrings mål i cacheminnen med vissa typer av anpassade DNS-inställningar har också en kontroll för att uppdatera deras IP-adresser. (Den här typen av konfiguration är sällsynt.)

Mer information finns nedan:

* [Ändra sammanställda namn rymds värden](#change-aggregated-namespace-values) (sökväg för virtuell namn område, åtkomst princip, export och export)
* [Ändra användnings modellen](#change-the-usage-model)
* [Uppdatera DNS](#update-ip-address-custom-dns-configurations-only)

### <a name="change-aggregated-namespace-values"></a>Ändra sammanställda namn områdes värden

Du kan använda Azure Portal eller Azure CLI för att ändra sökvägen till klient-Facing-namnrymd, lagrings export och export-underkatalogen (om den används).

Läs rikt linjerna i [Lägg till NFS-namnområden sökvägar](add-namespace-paths.md#nfs-namespace-paths) om du behöver en påminnelse om hur du skapar flera giltiga sökvägar på ett lagrings mål.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Använd **namn områdes** sidan för Azure HPC-cachen för att uppdatera namn områdes värden. Den här sidan beskrivs mer detaljerat i artikeln Konfigurera sammanställd [namnrymd](add-namespace-paths.md).

![skärm bild av sidan för portalens namn område med NFS-uppdateringstjänsten öppen till höger](media/update-namespace-nfs.png)

1. Klicka på namnet på den sökväg som du vill ändra.
1. Använd fönstret Redigera om du vill ange nya värden för virtuell sökväg, exportera eller under katalog eller välja en annan åtkomst princip.
1. När du har gjort ändringarna klickar du på **OK** för att uppdatera lagrings målet eller **Avbryt** för att ignorera ändringar.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC-cache](./az-cli-prerequisites.md).

Använd ``--junction`` alternativet i kommandot [AZ HPC-cache-NFS-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) för att ändra namn områdets sökväg, NFS-export eller exportera under katalog.

``--junction``Parametern använder följande värden:

* ``namespace-path`` -Klientens virtuella fil Sök väg
* ``nfs-export`` – Lagrings system exporten som ska associeras med den klient-riktade sökvägen
* ``target-path`` (valfritt) – en under katalog till exporten, om det behövs

Exempel: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

Du måste ange alla tre värdena för varje sökväg i ``--junction`` instruktionen. Använd de befintliga värdena för värden som du inte vill ändra.

Cache-namn, lagrings mål namn och resurs grupp krävs även i alla uppdaterings kommandon.

Exempel kommando:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>Ändra användnings modellen

Användnings modellen påverkar hur cachen behåller data. Läs [Välj en användnings modell](hpc-cache-add-storage.md#choose-a-usage-model) om du vill veta mer.

Använd någon av dessa metoder för att ändra användnings modellen för ett NFS-lagrings mål.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Ändra användnings modellen från sidan **lagrings mål** i Azure Portal. Klicka på namnet på det lagrings mål som du vill ändra.

![skärm bild av redigerings sidan för ett NFS-lagrings mål](media/edit-storage-nfs.png)

Använd List rutan för att välja en ny användnings modell. Klicka på **OK** för att uppdatera lagrings målet, eller klicka på **Avbryt** om du vill ignorera ändringarna.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC-cache](./az-cli-prerequisites.md).

Använd kommandot [AZ HPC-cache-NFS-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) .

Uppdaterings kommandot är nästan identiskt med det kommando som du använder för att lägga till ett NFS-lagrings mål. Mer information och exempel finns i [skapa ett NFS-mål](hpc-cache-add-storage.md#create-an-nfs-storage-target) .

Uppdatera alternativet om du vill ändra användnings modellen ``--nfs3-usage-model`` . Exempel: ``--nfs3-usage-model WRITE_WORKLOAD_15``

Cache-namn, lagrings mål namn och resurs grupps värden krävs också.

Om du vill kontrol lera namnen på användnings modellerna använder du kommandot [AZ HPC-cache Usage-Model List](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list).

Om cachen har stoppats eller inte är i felfritt tillstånd kommer uppdateringen att gälla när cachen är felfri.

---

### <a name="update-ip-address-custom-dns-configurations-only"></a>Uppdatera IP-adress (endast anpassade DNS-konfigurationer)

Om din cache använder en DNS-konfiguration som inte är standard är det möjligt att din NFS-målplatss IP-adress kan ändras på grund av DNS-ändringar på Server sidan. Om DNS-servern ändrar Server dels lagrings systemets IP-adress kan Azure HPC-cache förlora åtkomst till lagrings systemet.

Vi rekommenderar att du arbetar med chefen för cacheminnets anpassade DNS-system för att planera för eventuella uppdateringar, eftersom dessa ändringar gör lagringen otillgänglig.

Om du behöver uppdatera lagrings målets DNS-angivna IP-adress finns det en knapp i listan lagrings mål. Klicka på **Uppdatera DNS** om du vill fråga den anpassade DNS-servern efter en ny IP-adress.

![Skärm bild av listan över lagrings mål. För ett lagrings mål, "..." menyn i den högra kolumnen är öppen och två alternativ visas: ta bort och uppdatera DNS.](media/refresh-dns.png)

Om det lyckas bör uppdateringen ta mindre än två minuter. Du kan bara uppdatera ett lagrings mål i taget. vänta tills den föregående åtgärden har slutförts innan du testar en annan.

## <a name="update-an-adls-nfs-storage-target-preview"></a>Uppdatera ett ADLS-NFS-lagrings mål (för hands version)

Precis som NFS-mål kan du ändra namn rymds Sök vägen och användnings modellen för ADLS-NFS-lagrings mål.

### <a name="change-an-adls-nfs-namespace-path"></a>Ändra sökvägen till en ADLS-NFS-namnrymd

Använd **namn områdes** sidan för Azure HPC-cachen för att uppdatera namn områdes värden. Den här sidan beskrivs mer detaljerat i artikeln Konfigurera sammanställd [namnrymd](add-namespace-paths.md).

![skärm bild av sidan Portal namn område med en ADS-NFS-uppdatera sida öppen till höger](media/update-namespace-adls.png)

1. Klicka på namnet på den sökväg som du vill ändra.
1. Använd redigera-fönstret för att ange en ny virtuell sökväg eller uppdatera åtkomst principen.
1. När du har gjort ändringarna klickar du på **OK** för att uppdatera lagrings målet eller **Avbryt** för att ignorera ändringar.

### <a name="change-adls-nfs-usage-models"></a>Ändra ADLS-NFS-användnings modeller

Konfigurationen för användnings modellerna ADLS-NFS är identisk med valet av NFS-användnings modell. Läs Portal anvisningarna i [ändra användnings modellen](#change-the-usage-model) i NFS-avsnittet ovan. Ytterligare verktyg för att uppdatera ADLS-NFS-lagrings mål är under utveckling.


## <a name="next-steps"></a>Nästa steg

* Läs [Lägg till lagrings mål](hpc-cache-add-storage.md) för mer information om de här alternativen.
* Läs [planera det sammanställda namn området](hpc-cache-namespace.md) för fler tips om hur du använder virtuella sökvägar.
