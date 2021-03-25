---
title: Använd en hanterad avbildning för att skapa en anpassad avbildnings-pool
description: Skapa en anpassad avbildnings-pool från en hanterad avbildning för att etablera datornoder med program vara och data för ditt program.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 9baa65c0f1c1844ea10e3d5b4f0b48924912d233
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023885"
---
# <a name="use-a-managed-image-to-create-a-custom-image-pool"></a>Använd en hanterad avbildning för att skapa en anpassad avbildnings-pool

Om du vill skapa en anpassad skrivarpool för batch-poolens virtuella datorer, kan du använda en hanterad avbildning för att skapa en [avbildning av ett delat avbildnings Galleri](batch-sig-images.md). Att bara använda en hanterad avbildning stöds också, men endast för API-versioner upp till och inklusive 2019-08-01.

> [!IMPORTANT]
> I de flesta fall bör du skapa anpassade avbildningar med hjälp av den delade avbildnings galleriet. Genom att använda det delade avbildnings galleriet kan du etablera pooler snabbare, skala större kvantiteter av virtuella datorer och förbättra tillförlitligheten vid etablering av virtuella datorer. Mer information finns i [använda galleriet för delade avbildningar för att skapa en anpassad pool](batch-sig-images.md).

I det här avsnittet beskrivs hur du skapar en anpassad avbildning med endast en hanterad avbildning.

## <a name="prerequisites"></a>Förutsättningar

- **En hanterad avbildnings resurs**. Om du vill skapa en pool med virtuella datorer med en anpassad avbildning måste du ha eller skapa en hanterad avbildnings resurs i samma Azure-prenumeration och region som batch-kontot. Avbildningen bör skapas från ögonblicks bilder av den virtuella datorns OS-disk och eventuellt anslutna data diskar.
  - Använd en unik anpassad avbildning för varje pool som du skapar.
  - Om du vill skapa en pool med avbildningen med hjälp av batch-API: erna anger du **resurs-ID** för avbildningen, som är av typen `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage` .
  - Den hanterade avbildnings resursen måste finnas för att poolens livstid ska kunna skalas och kan tas bort när poolen har tagits bort.

- **Azure Active Directory autentisering (Azure AD)**. Batch-klientens API måste använda Azure AD-autentisering. Mer dokumentation om stödet för Azure Batch i Azure Active Directory finns i [Authenticate Batch service solutions with Active Directory](batch-aad-auth.md) (Autentisera lösningar för Batch-tjänsten med Active Directory).

## <a name="prepare-a-managed-image"></a>Förbereda en hanterad avbildning

I Azure kan du förbereda en hanterad avbildning från:

- Ögonblicks bilder av en virtuell Azure-dators operativ system och data diskar
- En generaliserad virtuell Azure-dator med Managed disks
- En generaliserad lokal virtuell hård disk som laddats upp till molnet

Vi rekommenderar att du skapar den hanterade avbildningen på ett tillförlitligt sätt *med hjälp av* den första metoden: med hjälp av ögonblicks bilder av den virtuella datorns diskar för att skala batch-pooler tillförlitligt Följande steg visar hur du förbereder en virtuell dator, tar en ögonblicks bild och skapar en hanterad avbildning från ögonblicks bilden.

### <a name="prepare-a-vm"></a>Förbered en virtuell dator

Om du skapar en ny virtuell dator för avbildningen använder du en Azure Marketplace-avbildning från första part som stöds av batch som bas avbildning för din hanterade avbildning. Endast första parts bilder kan användas som en bas avbildning. Om du vill få en fullständig lista över referens för Azure Marketplace-avbildningar som stöds av Azure Batch, se [noden List Node agent SKU: er](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) .

> [!NOTE]
> Du kan inte använda en avbildning från tredje part som har ytterligare licens-och inköps villkor som bas avbildning. Information om dessa Marketplace-avbildningar finns i rikt linjerna för virtuella [Linux](../virtual-machines/linux/cli-ps-findimage.md#check-the-purchase-plan-information) -eller [Windows](../virtual-machines/windows/cli-ps-findimage.md#view-purchase-plan-properties)-datorer.

- Se till att den virtuella datorn har skapats med en hanterad disk. Det här är standard lagrings inställningen när du skapar en virtuell dator.
- Installera inte Azure-tillägg, till exempel tillägget för anpassat skript, på den virtuella datorn. Om avbildningen innehåller ett förinstallerat tillägg kan Azure drabbas av problem när du distribuerar batch-poolen.
- När du använder anslutna data diskar måste du montera och Formatera diskarna inifrån en virtuell dator för att använda dem.
- Kontrol lera att den grundläggande OS-avbildningen som du anger använder standard-Temp-enheten. Batch Node-agenten förväntar sig för närvarande standard-Temp-enheten.
- Kontrol lera att operativ system disken inte är krypterad.
- När den virtuella datorn är igång ansluter du till den via RDP (för Windows) eller SSH (för Linux). Installera nödvändig program vara eller kopiera önskade data.  

### <a name="create-a-vm-snapshot"></a>Skapa en ögonblicks bild av en virtuell dator

En ögonblicks bild är en fullständig skrivskyddad kopia av en virtuell hård disk. Om du vill skapa en ögonblicks bild av en virtuell dators operativ system eller data diskar kan du använda Azure Portal eller kommando rads verktygen. Steg och alternativ för att skapa en ögonblicks bild finns i rikt linjerna för virtuella [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) -eller [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) -datorer.

### <a name="create-an-image-from-one-or-more-snapshots"></a>Skapa en avbildning från en eller flera ögonblicks bilder

Om du vill skapa en hanterad avbildning från en ögonblicks bild använder du kommando rads verktyg i Azure, till exempel kommandot [AZ image Create](/cli/azure/image) . Du kan skapa en avbildning genom att ange en ögonblicks bild av en OS-disk och eventuellt en eller flera ögonblicks bilder av data diskar.

## <a name="create-a-pool-from-a-managed-image"></a>Skapa en pool från en hanterad avbildning

När du har hittat resurs-ID: t för den hanterade avbildningen skapar du en anpassad avbildnings-pool från avbildningen. Följande steg visar hur du skapar en anpassad avbildning med hjälp av antingen batch-tjänsten eller batch Management.

> [!NOTE]
> Se till att identiteten som du använder för Azure AD-autentisering har behörighet till avbildnings resursen. Se [autentisera batch service-lösningar med Active Directory](batch-aad-auth.md).
>
> Resursen för den hanterade avbildningen måste finnas i poolens livstid. Om den underliggande resursen tas bort kan poolen inte skalas.

### <a name="batch-service-net-sdk"></a>Batch-tjänst, .NET SDK

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image definition name}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
```

### <a name="batch-management-rest-api"></a>REST API för Batch Management

REST API-URI

```http
 PUT https://management.azure.com/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Batch/batchAccounts/{account name}/pools/{pool name}?api-version=2020-03-01
```

Begärandetext

```json
 {
   "properties": {
     "vmSize": "{VM size}",
     "deploymentConfiguration": {
       "virtualMachineConfiguration": {
         "imageReference": {
           "id": "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image name}"
         },
         "nodeAgentSkuId": "{Node Agent SKU ID}"
       }
     }
   }
 }
```

## <a name="considerations-for-large-pools"></a>Att tänka på för stora pooler

Om du planerar att skapa en pool med hundratals virtuella datorer eller mer med en anpassad avbildning, är det viktigt att följa föregående vägledning för att använda en avbildning som skapats från en ögonblicks bild av en virtuell dator.

Tänk också på följande:

- **Storleks gränser** – batch begränsar Poolens storlek till 2500 dedikerade datornoder, eller 1000 med låg prioritet när du använder en anpassad avbildning.

  Om du använder samma bild (eller flera avbildningar baserade på samma underliggande ögonblicks bild) för att skapa flera pooler får inte den totala beräknings noderna i poolerna överskrida de tidigare gränserna. Vi rekommenderar inte att du använder en bild eller dess underliggande ögonblicks bild för mer än en pool.

  Gränserna kan minskas om du konfigurerar poolen med [inkommande NAT-pooler](pool-endpoint-configuration.md).

- **Tids gräns för storleks ändring** – om poolen innehåller ett fast antal noder (inte Autoskala) ökar du resizeTimeout-egenskapen för poolen till ett värde som 20-30 minuter. Om din pool inte når mål storleken inom den angivna tids gränsen, kan du utföra en annan [ändrings åtgärd](/rest/api/batchservice/pool/resize).

  Om du planerar en pool med fler än 300 Compute-noder kan du behöva ändra storlek på poolen flera gånger för att nå mål storleken.
  
Genom att använda det [delade avbildnings galleriet](batch-sig-images.md)kan du skapa större pooler med dina anpassade avbildningar tillsammans med fler delade avbildnings repliker. Med hjälp av delade avbildningar kan den tid det tar för poolen att uppnå stabilt tillstånd vara upp till 25% snabbare och svars tiden för den virtuella datorn är upp till 30% kortare.

## <a name="considerations-for-using-packer"></a>Att tänka på när du använder Packer

Att skapa en hanterad avbildnings resurs direkt med Packer kan bara göras med batch-konton för användar prenumerations läge. För batch service mode-konton måste du först skapa en virtuell hård disk och sedan importera den virtuella hård disken till en hanterad avbildnings resurs. Dina steg för att skapa en hanterad avbildnings resurs varierar beroende på poolens tilldelnings läge (användar prenumeration eller batch-tjänst).

Se till att resursen som används för att skapa den hanterade avbildningen finns för livs längden för alla pooler som refererar till den anpassade avbildningen. Om du inte gör det kan det leda till allokeringsfel och/eller ändra storlek på felen.

Om avbildningen eller den underliggande resursen tas bort kan det hända att du får ett fel som liknar: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed` . Om du får det här felet kontrollerar du att den underliggande resursen inte har tagits bort.

Mer information om hur du använder Packer för att skapa en virtuell dator finns i [skapa en Linux-avbildning med Packer](../virtual-machines/linux/build-image-with-packer.md) eller [skapa en Windows-avbildning med Packer](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder det [delade avbildnings galleriet](batch-sig-images.md) för att skapa en anpassad pool.
- En djupgående översikt över batch finns i [batch-tjänstens arbets flöde och resurser](batch-service-workflow-features.md).
