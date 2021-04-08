---
title: Azure-lösningar för konfidentiell behandling på virtuella datorer
description: Lär dig mer om Azures lösningar för konfidentiell behandling på virtuella datorer.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 8621dc8cfc10ab44ecb358a40fdae1a1b2081734
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566591"
---
# <a name="solutions-on-azure-virtual-machines"></a>Lösningar på virtuella Azure-datorer

Den här artikeln beskriver hur du distribuerar virtuella datorer med Azure konfidentiella data bearbetning (VM) som kör Intel-processorer som backas upp av [Intel Software Guard-tillägget](https://software.intel.com/sgx) (Intel SGX). 

## <a name="azure-confidential-computing-vm-sizes"></a>VM-storlekar för Azure konfidentiella datorer

Virtuella Azure-datorer med konfidentiell användning är utformade för att skydda konfidentialiteten och integriteten hos dina data och kod när de bearbetas i molnet 

[DCsv2-serien](../virtual-machines/dcv2-series.md) Virtuella datorer är den senaste och senaste konfidentiella data bearbetnings familjen. De här virtuella datorerna har stöd för ett större antal distributions funktioner, har 2x enklaven Page cache (EPC) och ett större urval av storlekar jämfört med våra DC-Series virtuella datorer. VM [-seriens](../virtual-machines/sizes-previous-gen.md#preview-dc-series) virtuella datorer är för närvarande i för hands version och kommer att vara inaktuella och ingår inte i allmän tillgänglighet.

Börja distribuera en DCsv2-Series virtuell dator via Microsofts kommersiella marknads plats genom att följa [snabb starts guiden](quick-create-marketplace.md).

### <a name="current-available-sizes-and-regions"></a>Aktuella tillgängliga storlekar och regioner

Om du vill hämta en lista över alla allmänt tillgängliga storlekar för konfidentiell beräkning av virtuella datorer i tillgängliga regioner och tillgänglighets zoner kör du följande kommando i [Azure CLI](/cli/azure/install-azure-cli-windows):

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" `
    --all `
    --output table
```

Om du vill ha en mer detaljerad vy över storlekarna ovan kör du följande kommando:

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family']"
```
### <a name="dedicated-host-requirements"></a>Krav för dedikerad värd
Att distribuera en **Standard_DC8_v2** virtuell dator storlek i DCSv2-Series VM-serien upptar den fullständiga värden och delas inte med andra klienter eller prenumerationer. Denna VM SKU-serie ger den isolering som du kan behöva för att uppfylla kraven på efterlevnad och säkerhet som normalt uppfylls genom att ha en särskild värd tjänst. När du väljer **Standard_DC8_v2** SKU allokerar den fysiska värd servern alla tillgängliga maskin varu resurser, inklusive endast EPC-minne till den virtuella datorn. Observera att den här funktionen finns i infrastruktur designen och att alla funktioner i **Standard_DC8_v2** stöds. Den här distributionen är inte samma som den [Azure-dedikerade värd](../virtual-machines/dedicated-hosts.md) tjänst som tillhandahålls av andra Azure VM-familjer.


## <a name="deployment-considerations"></a>Distributionsöverväganden

Följ en snabb starts guide för att distribuera en DCsv2-Series virtuell dator på mindre än 10 minuter. 

- **Azure-prenumeration** – om du vill distribuera en konfidentiell VM-instans bör du fundera över en prenumeration där du betalar per användning eller något annat köp alternativ. Om du använder ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/)har du inte någon kvot för lämplig mängd Azure-beräknings kärnor.

- **Priser och regional tillgänglighet** – hitta prissättningen för DCsv2-Series virtuella datorer på [pris sidan för virtuella](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)datorer. Kontrol lera [vilka produkter som är tillgängliga i region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) för tillgänglighet i Azure-regioner.


- **Kärnor-kvot** – du kan behöva öka kvoten för kärnor i din Azure-prenumeration från standardvärdet. Din prenumeration kan också begränsa antalet kärnor som du kan distribuera i vissa storlekar på virtuella datorer, inklusive DCsv2-serien. Om du vill begära en kvot ökning [öppnar du en support förfrågan online](../azure-portal/supportability/per-vm-quota-requests.md) utan kostnad. Observera att standard gränserna kan variera beroende på din prenumerations kategori.

  > [!NOTE]
  > Kontakta Azure-supporten om du har storskaliga kapacitets behov. Azure-kvoter är kredit gränser, inte kapacitets garantier. Oavsett din kvot debiteras du bara för kärnor som du använder.
  
- **Ändra storlek** – på grund av deras specialiserade maskin vara kan du bara ändra storlek på konfidentiella dator instanser inom samma storleks familj. Du kan till exempel bara ändra storlek på en virtuell dator i DCsv2-serien från en DCsv2-serie till en annan. Det går inte att ändra storlek på en icke-konfidentiell dator storlek till en konfidentiell data behandling.  

- **Avbildning** – för att tillhandahålla Intel-stöd för Software Guard-tillägget (Intel SGX) på konfidentiella beräknings instanser måste alla distributioner köras på generation 2-avbildningar. Azures konfidentiella data behandling har stöd för arbets belastningar som körs på Ubuntu 18,04 gen 2, Ubuntu 16,04 gen 2, Windows Server 2019 Gen2 och Windows Server 2016 gen 2. Läs om [stöd för virtuella datorer i generation 2 på Azure](../virtual-machines/generation-2.md) för att lära dig mer om scenarier som stöds och som inte stöds. 

- **Lagring** – data diskar för virtuella datorer i Azure konfidentiell dator och våra tillfälliga OS-diskar finns på NVMe-diskar. Instanser stöder bara Premium SSD och Standard SSD diskar, inte Ultra SSD eller Standard HDD. Den virtuella datorns storlek **DC8_v2** stöder inte Premium Storage. 

- **Disk kryptering** – konfidentiella beräknings instanser stöder inte disk kryptering för tillfället. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>Överväganden för hög tillgänglighet och haveri beredskap

När du använder virtuella datorer i Azure är det du som ansvarar för att implementera en lösning för hög tillgänglighet och haveri beredskap för att undvika avbrott. 

Azures konfidentiella data behandling har inte stöd för zon-redundans via Tillgänglighetszoner för tillfället. Använd [tillgänglighets uppsättningar](../virtual-machines/availability-set-overview.md)för högsta tillgänglighet och redundans för konfidentiell dator användning. På grund av maskin varu begränsningar kan tillgänglighets uppsättningar för konfidentiella data bearbetnings instanser bara ha högst 10 uppdaterings domäner. 

## <a name="deployment-with-azure-resource-manager-arm-template"></a>Distribution med Azure Resource Manager-mall (ARM)

Azure Resource Manager är Azures tjänst för distribution och hantering. Det tillhandahåller ett hanterings lager som gör att du kan skapa, uppdatera och ta bort resurser i din Azure-prenumeration. Du kan använda hanterings funktioner som åtkomst kontroll, lås och taggar för att skydda och organisera dina resurser efter distributionen.

Mer information om ARM-mallar finns i [malldistribution översikt](../azure-resource-manager/templates/overview.md).

Om du vill distribuera en DCsv2-Series virtuell dator i en ARM-mall använder du den [virtuella dator resursen](../virtual-machines/windows/template-description.md). Se till att du anger rätt egenskaper för **vmSize** och för din **imageReference**.

### <a name="vm-size"></a>Storlek på virtuell dator

Ange en av följande storlekar i ARM-mallen i den virtuella dator resursen. Den här strängen anges som **vmSize** i **Egenskaper**.

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Gen2 OS-avbildning

Under **Egenskaper** måste du också referera till en bild under **storageProfile**. Använd *endast en* av följande avbildningar för din **imageReference**.

```json
      "2019-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2019-datacenter-gensecond",
        "version": "latest"
      },
      "2016-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2016-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "16_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "16_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>Nästa steg 

I den här artikeln har du lärt dig om de kvalifikationer och konfigurationer som behövs när du skapar en virtuell dator med konfidentiell dator användning. Nu kan du gå till Microsoft Azure Marketplace för att distribuera en DCsv2-Series virtuell dator.

> [!div class="nextstepaction"]
> [Distribuera en DCsv2-Series virtuell dator på Azure Marketplace](quick-create-marketplace.md)