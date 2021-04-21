---
title: Konfidentiell databehandling i Azure på virtuella datorer
description: Lär dig mer om konfidentiella databehandlingslösningar i Azure på virtuella datorer.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 580c53f311bc8ee70e974df2bc4111e6361d06f6
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818970"
---
# <a name="solutions-on-azure-virtual-machines"></a>Lösningar på virtuella Azure-datorer

Den här artikeln beskriver information om hur du distribuerar virtuella Datorer för konfidentiell databehandling i Azure som kör Intel-processorer som backas upp av [Intel Software Guard-tillägget](https://software.intel.com/sgx) (Intel SGX). 

## <a name="azure-confidential-computing-vm-sizes"></a>Storlekar på virtuella datorer med konfidentiell databehandling i Azure

Virtuella datorer med konfidentiell databehandling i Azure är utformade för att skydda sekretessen och integriteten hos dina data och din kod medan de bearbetas i molnet 

[DCsv2-serien](../virtual-machines/dcv2-series.md) Virtuella datorer är den senaste och senaste storleksfamiljen för konfidentiell databehandling. Dessa virtuella datorer stöder ett större antal distributionsfunktioner, har 2x EPC (Enclave Page Cache) och ett större urval av storlekar jämfört med våra virtuella DC-Series datorer. De [virtuella datorerna i DC-serien](../virtual-machines/sizes-previous-gen.md#preview-dc-series) är för närvarande i förhandsversion och kommer att bli inaktuella och inte inkluderas i allmän tillgänglighet.

Börja distribuera en virtuell DCsv2-Series via Microsofts kommersiella marknadsplats genom att följa [snabbstartskursen](quick-create-marketplace.md).

### <a name="current-available-sizes-and-regions"></a>Aktuella tillgängliga storlekar och regioner

Om du vill hämta en lista över alla allmänt tillgängliga konfidentiella beräkningsstorlekar för virtuella datorer i tillgängliga regioner och tillgänglighetszoner kör du följande kommando i [Azure CLI:](/cli/azure/install-azure-cli-windows)

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" `
    --all `
    --output table
```

Om du vill ha en mer detaljerad vy över ovanstående storlekar kör du följande kommando:

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family']"
```
### <a name="dedicated-host-requirements"></a>Krav för dedikerad värd
Distribution av **Standard_DC8_v2** virtuell datorstorlek i DCSv2-Series VM-familjen upptar den fullständiga värden och delas inte med andra klienter eller prenumerationer. Den här VM-SKU-familjen ger den isolering som du kan behöva för att uppfylla efterlevnads- och säkerhetskrav som normalt uppfylls genom att ha en dedikerad värdtjänst. När du **Standard_DC8_v2** SKU allokerar den fysiska värdservern alla tillgängliga maskinvaruresurser, inklusive EPC-minne, endast till den virtuella datorn. Observera att den här funktionen finns i infrastrukturdesignen och att alla **funktioner i Standard_DC8_v2** stöds. Den här distributionen är inte samma som [Azure Dedicated Host](../virtual-machines/dedicated-hosts.md) som tillhandahålls av andra Azure VM-familjer.


## <a name="deployment-considerations"></a>Distributionsöverväganden

Följ en snabbstartsguide för att distribuera DCsv2-Series virtuell dator på mindre än 10 minuter. 

- **Azure-prenumeration** – Om du vill distribuera en instans av en virtuell dator med konfidentiell databehandling bör du överväga en prenumeration med användningsbaserade betalning eller ett annat köpalternativ. Om du använder ett kostnadsfritt [Azure-konto](https://azure.microsoft.com/free/)har du ingen kvot för rätt mängd Azure-beräkningskärnor.

- **Priser och regional tillgänglighet** – Hitta priser för virtuella DCsv2-Series på sidan [med priser för virtuella datorer.](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) Kontrollera [tillgängligheten för produkter som är](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) tillgängliga per region i Azure-regioner.


- **Kärnkvot –** Du kan behöva öka kärnkvoten i din Azure-prenumeration från standardvärdet. Din prenumeration kan också begränsa antalet kärnor som du kan distribuera i vissa VM-storleksfamiljer, inklusive DCsv2-serien. Om du vill begära en kvotökning [öppnar du en supportbegäran online](../azure-portal/supportability/per-vm-quota-requests.md) utan kostnad. Observera att standardgränserna kan variera beroende på din prenumerationskategori.

  > [!NOTE]
  > Kontakta Azure Support om du har storskaliga kapacitetsbehov. Azure-kvoter är kreditgränser, inte kapacitetsgarantier. Oavsett din kvot debiteras du bara för kärnor som du använder.
  
- **Storleksändring** – På grund av deras specialiserade maskinvara kan du bara ändra storlek på konfidentiella beräkningsinstanser inom samma storleksfamilj. Du kan till exempel bara ändra storlek på en virtuell dator i DCsv2-serien från en storlek i DCsv2-serien till en annan. Storleksändring från en icke-konfidentiell databehandlingsstorlek till en konfidentiell databehandlingsstorlek stöds inte.  

- **Bild** – För att ge Intel Software Guard-tillägget (Intel SGX) stöd för konfidentiella beräkningsinstanser måste alla distributioner köras på generation 2-avbildningar. Konfidentiell databehandling i Azure stöder arbetsbelastningar som körs på Ubuntu 18.04 Gen 2, Ubuntu 20.04 Gen 2, Windows Server 2019 gen2 och Windows Server 2016 Gen 2. Läs om [stöd för virtuella datorer i generation 2 på Azure om](../virtual-machines/generation-2.md) du vill veta mer om scenarier som stöds och inte stöds. 

- **Lagring** – Datadiskar för konfidentiell databehandling i Azure och våra tillfälliga OS-diskar finns på NVMe-diskar. Instanser stöder endast Premium SSD och Standard SSD diskar, inte Ultra SSD eller Standard HDD. Storleken på virtuella **DC8_v2** stöder inte Premium-lagring. 

- **Diskkryptering** – Konfidentiella beräkningsinstanser stöder inte diskkryptering just nu. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>Överväganden för hög tillgänglighet och haveriberedskap

När du använder virtuella datorer i Azure ansvarar du för att implementera en lösning för hög tillgänglighet och haveriberedskap för att undvika driftstopp. 

Konfidentiell databehandling i Azure stöder inte zonredundans via Tillgänglighetszoner just nu. Använd tillgänglighetsuppsättningar för högsta tillgänglighet och redundans för [konfidentiell databehandling.](../virtual-machines/availability-set-overview.md) På grund av maskinvarubegränsningar kan tillgänglighetsuppsättningar för instanser av konfidentiell databehandling bara ha högst 10 uppdateringsdomäner. 

## <a name="deployment-with-azure-resource-manager-arm-template"></a>Arm-Azure Resource Manager (Deployment with Azure Resource Manager)

Azure Resource Manager är Azures tjänst för distribution och hantering. Det tillhandahåller ett hanteringslager som gör att du kan skapa, uppdatera och ta bort resurser i din Azure-prenumeration. Du kan använda hanteringsfunktioner som åtkomstkontroll, lås och taggar för att skydda och organisera dina resurser efter distributionen.

Mer information om ARM-mallar finns i [Malldistribution översikt.](../azure-resource-manager/templates/overview.md)

Om du vill DCsv2-Series virtuell dator i en ARM-mall använder du [virtual machine-resursen](../virtual-machines/windows/template-description.md). Se till att du anger rätt egenskaper **för vmSize** och för **imageReference**.

### <a name="vm-size"></a>Storlek på virtuell dator

Ange någon av följande storlekar i ARM-mallen i den virtuella datorresursen. Den här strängen läggs som **vmSize** i **egenskaperna**.

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Gen2 OS-avbildning

Under **egenskaper** måste du också referera till en avbildning under **storageProfile**. Använd *bara en* av följande bilder för **imageReference**.

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
      "20_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "20_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>Nästa steg 

I den här artikeln har du lärt dig om de färdigheter och konfigurationer som krävs när du skapar en konfidentiell virtuell dator för databehandling. Nu kan du gå till Microsoft Azure Marketplace för att distribuera en DCsv2-Series virtuell dator.

> [!div class="nextstepaction"]
> [Distribuera en DCsv2-Series virtuell dator i Azure Marketplace](quick-create-marketplace.md)