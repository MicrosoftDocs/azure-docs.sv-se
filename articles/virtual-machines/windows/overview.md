---
title: Översikt över virtuella Windows-datorer i Azure
description: Översikt över virtuella Windows-datorer i Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4e955a51906f160264fc4e81f263fe7677be91de
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589630"
---
# <a name="windows-virtual-machines-in-azure"></a>Virtuella Windows-datorer i Azure

Virtuella Azure-datorer (Virtual Machines, VM) är en av flera typer av [behovsbaserade och skalbara datorresurser](/azure/architecture/guide/technology-choices/compute-decision-tree) som Azure erbjuder. Normalt använder du en virtuell dator om du behöver mer kontroll över datormiljön än vad de andra alternativen erbjuder. Den här artikeln innehåller information om vad du bör tänka på innan du skapar en virtuell dator, hur du skapar den och hur du hanterar den.

En virtuell dator i Azure ger dig virtualiseringsflexibilitet utan att du behöver köpa och underhålla den fysiska maskinvara som den virtuella datorn körs på. Du behöver dock fortfarande underhålla den virtuella datorn genom att utföra uppgifter, som att konfigurera, korrigera och underhålla programvaran som körs på den virtuella datorn.

Virtuella datorer i Azure kan användas på olika sätt. Några exempel är:

* **Utveckling och tester** – Virtuella datorer i Azure erbjuder ett snabbt och enkelt sätt att skapa en dator med specifika konfigurationer som krävs för att koda och testa ett program.
* **Program i molnet** – Eftersom behovet av ditt program kan variera kan det vara bra ur ekonomisk synpunkt att köra den på en virtuell dator i Azure. Du betalar extra för virtuella datorer när du behöver dem och stänger av dem när du inte behöver dem.
* **Utökat datacenter** – Virtuella datorer i ett virtuellt nätverk i Azure kan enkelt anslutas till organisationens nätverk.

Antalet virtuella datorer som programmet använder kan skalas upp och ned beroende på vilka behov du har.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Vad behöver jag tänka på innan jag skapar en virtuell dator?
Det finns alltid en rad [överväganden vid utformning](/azure/architecture/reference-architectures/n-tier/windows-vm) när du utökar en programinfrastruktur i Azure. Följande aspekter av en virtuell dator är viktiga att tänka på innan du börjar:

* Programresursernas namn
* Lagringsplatsen för resurserna
* Den virtuella datorns storlek
* Det högsta antalet virtuella datorer som kan skapas
* Operativsystemet som körs på den virtuella datorn
* Konfigurationen av den virtuella datorn när den startats
* Relaterade resurser som krävs för den virtuella datorn

### <a name="locations"></a>Platser
Alla resurser som skapats i Azure fördelas på flera [geografiska områden](https://azure.microsoft.com/regions/) runtom i världen. Vanligtvis kallas regionen **plats** när du skapar en virtuell dator. För en virtuell dator anger platsen var de virtuella hårddiskarna lagras.

I den här tabellen finns några exempel på hur du kan hämta en lista över tillgängliga platser.

| Metod | Beskrivning |
| --- | --- |
| Azure Portal |Välj en plats i listan när du skapar en virtuell dator. |
| Azure PowerShell |Använd kommandot [Get-AzLocation](/powershell/module/az.resources/get-azlocation). |
| REST-API |Använd åtgärden [List locations](/rest/api/resources/subscriptions) (Listplatser). |
| Azure CLI |Använd åtgärden [az account list-locations](/cli/azure/account?view=azure-cli-latest). |

### <a name="singapore-data-residency"></a>Placering för Singapore-data

I Azure är funktionen för att aktivera lagring av kunddata i en enda region för närvarande endast tillgänglig i Sydostasien region (Singapore) för Asien och stillahavsområdet geo. För alla andra regioner lagras kund information på Geo. Mer information finns i [säkerhets Center](https://azuredatacentermap.azurewebsites.net/).

## <a name="availability"></a>Tillgänglighet
Azure har tillkännagivit ett branschledande serviceavtal på 99,9 % för virtuella datorer med en instans, förutsatt att du distribuerar den virtuella datorn med premiumlagring för alla diskar.  För att distributionen ska kunna omfattas av standardserviceavtalet på 99,95 % för virtuella datorer behöver du fortfarande distribuera två eller flera virtuella datorer som kör arbetsbelastningen i en tillgänglighetsuppsättning. En tillgänglighetsuppsättning säkerställer att dina virtuella datorer distribueras via flera feldomäner i Azure-datacentren och på värdar med olika underhållsfönster. I det fullständiga[Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/virtual-machines/) förklaras den garanterade tillgängligheten för Azure som helhet.


## <a name="vm-size"></a>Storlek på virtuell dator
[Storleken](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) på den virtuella datorn som du använder bestäms av den arbetsbelastning som du vill köra. Storleken som du väljer avgör sedan faktorer som processorkraft, minne och lagringskapacitet. Azure erbjuder en rad olika storlekar för att passa en mängd olika användningar.

Azure debiterar ett [Tim pris](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) baserat på den virtuella datorns storlek och operativ system. För delar av timmar tar Azure bara betalt för användningen per minut. Lagringsutrymme prissätts och debiteras separat.

## <a name="vm-limits"></a>Begränsningar för den virtuella datorn
Prenumerationen har [standardkvotgränser](../../azure-resource-manager/management/azure-subscription-service-limits.md) som kan påverka ditt projekt om många virtuella datorer distribueras. Den aktuella gränsen på basis av per prenumeration är 20 virtuella datorer per region. Begränsningen kan ökas om du [anmäler ett supportärende och begär en ökning](../../azure-portal/supportability/resource-manager-core-quotas-request.md)

### <a name="operating-system-disks-and-images"></a>Operativsystemsdiskar och avbildningar
Virtuella datorer använder [virtuella hård diskar (VHD: er)](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att lagra sitt operativ system (OS) och data. VHD:ar används också för de avbildningar du kan välja mellan för att installera ett operativsystem. 

Azure tillhandahåller många [Marketplace-avbildningar](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images%3Bwindows&page=1) som kan användas med olika versioner och typer av Windows Server-operativsystem. Marketplace-avbildningar identifieras av avbildningens utgivare, erbjudande, sku och version (vanligtvis anges versionen som den senaste). Endast 64-bitars operativsystem stöds. Mer information om kompatibla gästoperativsystem, roller och funktioner finns i [Microsoft serverprogramvarusupport för Microsoft virtuella Azure-datorer](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

I den här tabellen hittar du några olika sätt för att hitta informationen om en avbildning.

| Metod | Beskrivning |
| --- | --- |
| Azure Portal |Värdena anges automatiskt åt dig när du väljer en avbildning som du vill använda. |
| Azure PowerShell |[Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) -Location *location*<BR>[Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) -Location *location* -Publisher *publisherName*<BR>[Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) -Location *location* -Publisher *publisherName* -Offer *offerName* |
| REST API:er |[Lista över avbildningsutgivare](/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[Lista över avbildningserbjudanden](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[Lista över avbildnings-SKU:er](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |
| Azure CLI |[AZ VM Image List-Publishers](/cli/azure/vm/image?view=azure-cli-latest) --location *location*<BR>[AZ VM Image List-erbjudanden](/cli/azure/vm/image?view=azure-cli-latest) --location *location* --Publisher *publisherName*<BR>[AZ VM Image List-SKU](/cli/azure/vm?view=azure-cli-latest) --location *location* --Publisher *publisherName* --erbjudande *offerName*|

Du kan välja att [ladda upp och använda en egen avbildning](upload-generalized-managed.md). Om du gör det används inte utgivarnamn, erbjudande och sku.

### <a name="extensions"></a>Tillägg
[Tillägg](../extensions/features-windows.md?toc=/azure/virtual-machines/windows/toc.json) för virtuella datorer ger din virtuella dator fler funktioner genom konfiguration efter distribution och automatiserade uppgifter.

Dessa vanliga uppgifter kan utföras med hjälp av tillägg:

* **Köra anpassade skript** – [Tillägget för anpassat skript](../extensions/custom-script-windows.md?toc=/azure/virtual-machines/windows/toc.json) hjälper dig att konfigurera arbetsbelastningar på den virtuella datorn genom att köra skriptet när den virtuella datorn har etablerats.
* **Distribuera och hantera konfigurationer** – Med [PowerShell-tillägget för önskad tillståndskonfiguration (DSC)](../extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json) kan du konfigurera DSC på en virtuell dator så att du kan hantera konfigurationer och miljöer.
* **Samla in diagnostikdata** – Med [Azure Diagnostics-tillägget](../extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json) kan du konfigurera den virtuella datorn så att den samlar in diagnostikdata som kan användas för att övervaka hälsotillståndet för programmet.

### <a name="related-resources"></a>Relaterade resurser
Resurserna i den här tabellen används av den virtuella datorn och måste finnas eller skapas när den virtuella datorn skapas.

| Resurs | Krävs | Beskrivning |
| --- | --- | --- |
| [Resursgrupp](../../azure-resource-manager/management/overview.md) |Yes |Den virtuella datorn måste ingå i en resursgrupp. |
| [Lagringskonto](../../storage/common/storage-account-create.md) |Yes |Den virtuella datorn behöver lagringskontot för att kunna lagra sina virtuella hårddiskar. |
| [Virtuellt nätverk](../../virtual-network/virtual-networks-overview.md) |Yes |Den virtuella datorn måste ingå i ett virtuellt nätverk. |
| [Offentlig IP-adress](../../virtual-network/public-ip-addresses.md) |No |Den virtuella datorn kan tilldelas en offentlig IP-adress för att möjliggöra fjärråtkomst till den. |
| [Nätverksgränssnitt](../../virtual-network/virtual-network-network-interface.md) |Yes |Den virtuella datorn behöver nätverksgränssnittet för att kunna kommunicera i nätverket. |
| [Datadiskar](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |No |Den virtuella datorn kan innehålla datadiskar för att expandera lagringskapaciteten. |

## <a name="next-steps"></a>Nästa steg

Skapa din första virtuella dator!

- [Portal](quick-create-portal.md)
- [PowerShell](quick-create-powershell.md)
- [Azure CLI](quick-create-cli.md)
