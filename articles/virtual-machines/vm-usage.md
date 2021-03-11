---
title: Förstå användningen av virtuella Azure-datorer
description: Förstå användnings information om virtuella datorer
services: virtual-machines
documentationcenter: ''
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 07/28/2020
ms.openlocfilehash: ba973bd5609dacf05eca842025d4e828d8a9f841
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550955"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Förstå användningen av virtuella Azure-datorer
Genom att analysera Azures användnings data kan kraftfulla förbruknings insikter uppnås – insikter som kan möjliggöra bättre kostnads hantering och allokering i hela organisationen. Det här dokumentet ger en detaljerad genom gång av information om din Azure Compute-förbrukning. Om du vill ha mer information om allmän Azure-användning går du till att [förstå din faktura](../cost-management-billing/understand/review-individual-bill.md).

## <a name="download-your-usage-details"></a>Ladda ned din användningsinformation
Börja med att [Hämta din användnings information](../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). Tabellen nedan innehåller definitionen och exempel på användnings värden för Virtual Machines som distribueras via Azure Resource Manager. Det här dokumentet innehåller ingen detaljerad information för virtuella datorer som distribueras via vår klassiska modell.


| Fält | Innebörd | Exempel värden | 
|---|---|---|
| Datum för användning | Det datum då resursen användes | `11/23/2017` |
| Meter ID | Identifierar den översta tjänst som den här användningen tillhör| `Virtual Machines`|
| Meter Sub-Category | Identifierare för faktureringsmätaren. <br><br> För beräkning av användnings tid finns det en mätning för varje VM-storlek + OS (Windows, icke-Windows) + region. <br><br> För förstklassig program varu användning finns det en mätare för varje program varu typ. De flesta Premium program avbildningar har olika mätare för varje kärn storlek. Mer information finns på sidan för [beräknings priser](https://azure.microsoft.com/pricing/details/virtual-machines/)</li></ul>| `2005544f-659d-49c9-9094-8e0aea1be3a5`|
| Meter Name| Detta är särskilt för varje tjänst i Azure. För Compute är det alltid "Beräkningstimmar".| `Compute Hours`|
| Meter Region| Anger datacenterplats för vissa tjänster som prissätts beroende på var datacentret ligger.|  `JA East`|
| Enhet| Identifierar i vilken enhet tjänsten debiteras. Beräknings resurser faktureras per timme.| `Hours`|
| Förbrukad| Mängden av den resurs som har för bruk ATS under den dagen. För Compute debiterar vi varje minut som den virtuella datorn kördes för en given timme (upp till 6 decimaler av noggrannhet).| `1, 0.5`|
| Resource Location  | Identifierar det datacenter där resursen körs.| `JA East`|
| Consumed Service | Den Azure-plattform som du använde.| `Microsoft.Compute`|
| Resursgrupp | Resursgruppen där den distribuerade resursen körs. Mer information finns i [Azure Resource Manager översikt.](../azure-resource-manager/management/overview.md)|`MyRG`|
| Instance ID | Resursens identifierare. Identifieraren innehåller namnet du angav för resursen när den skapades. För virtuella datorer kommer instans-ID: t innehålla SubscriptionId, ResourceGroupName och VMName (eller skalnings uppsättningens namn för användning av skalnings uppsättningar).| `/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1`<br><br>eller<br><br>`/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1`|
| Taggar| Tagg som du tilldelar till resursen. Använd taggar för att gruppera faktureringsposter. Lär dig att tagga Virtual Machines med [CLI](./tag-cli.md) eller [PowerShell](./tag-portal.md) som bara är tillgängligt för virtuella Resource Manager-datorer.| `{"myDepartment":"RD","myUser":"myName"}`|
| Ytterligare info | Tjänstspecifika metadata. För virtuella datorer fyller vi i följande data i fältet ytterligare information: <br><br> Bildtyp-bestämd bild som du körde. Hitta den fullständiga listan över strängar som stöds under avbildnings typer.<br><br> Tjänst typ: den storlek som du har distribuerat.<br><br> VMName: namnet på den virtuella datorn. Det här fältet är bara ifyllt för virtuella datorer med skalnings uppsättningar. Om du behöver ditt VM-namn för virtuella datorer med skalnings uppsättningar kan du se det i instans-ID-strängen ovan.<br><br> UsageType: här anges vilken typ av användning detta representerar.<br><br> ComputeHR är beräknings timmen för den underliggande virtuella datorn, t. ex. Standard_D1_v2.<br><br> ComputeHR_SW är avgiften för program varan om den virtuella datorn använder Premium program vara, t. ex. Microsoft R Server. | Virtual Machines<br>`{"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}`<br><br>Virtual Machine Scale Sets<br> `{"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}`<br><br>Premium program vara<br> `{"ImageType":"","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR_SW"}` |

## <a name="image-type"></a>Avbildnings typ
För vissa bilder i Azure-galleriet fylls avbildnings typen i fältet ytterligare information. Detta gör det möjligt för användarna att förstå och spåra vad de har distribuerat på den virtuella datorn. Följande värden som är ifyllda i det här fältet baseras på avbildningen som du har distribuerat:
- BitRock 
- Kanoniskt FreeBSD 
- Öppna logik 
- Oracle 
- SLES för SAP 
- SQL Server 14 för hands version av Windows Server 2012 R2 
- SUSE
- SUSE Premium
- StorSimple Cloud Appliance 
- Red Hat
- Red Hat för SAP Business Applications     
- Red Hat för SAP HANA 
- BYOL för Windows-klient 
- Windows Server-BYOL 
- Windows Server Preview 

## <a name="service-type"></a>Typ av tjänst
Fältet typ av tjänst i fältet ytterligare information motsvarar den exakta VM-storlek som du har distribuerat. Virtuella datorer i Premium Storage (SSD-baserade) och icke-Premium-virtuella datorer (HDD-baserade) är i samma pris. Om du distribuerar en SSD-baserad storlek, till exempel \_ standard \_ -DS2 v2, ser du storlekarna non-SSD ( `Standard\_D2\_v2 VM` ) i mätar Sub-Categorys kolumnen och SSD-storlek ( `Standard\_DS2\_v2` ) i fältet ytterligare information.

## <a name="region-names"></a>Region namn
Region namnet som fyllts i i fältet resurs plats i användnings informationen varierar från det region namn som används i Azure Resource Manager. Här är en mappning mellan värdena för regionen:

| **Namn på Resource Manager-region** | **Resurs plats i användnings information** |
|---|---|
| australienöstra |Australien, östra|
| Australien, sydöstra | Australien, sydöstra|
| Brasilien, södra | Brasilien, södra|
| Indiensödra | Kanada, centrala|
| Indien | Kanada, östra|
| Kanada | Indien, centrala|
| USA, centrala | Central US|
| chinaeast | Kina, östra|
| chinanorth | Kina, norra|
| eastasia | Asien, östra|
| USA, östra | East US|
| usaöstra2 | USA, östra 2|
| GermanyCentral | DE centrala|
| GermanyNortheast | DE nordöstra|
| Japan, östra | Japan, östra|
| japanvästra | Japan, västra|
| Centrala | Sydkorea, centrala|
| Koreasödra | Sydkorea, södra|
| usanorracentrala | USA, norra centrala|
| northeurope | Europa, norra|
| USA, södra centrala | USA, södra centrala|
| Asien, sydöstra | Sydostasien|
| Australienöstra | Indien, södra|
| UKNorth | Nord USA|
| södrastorbritannien | Storbritannien, södra|
| UKSouth2 | Storbritannien, södra 2|
| västrastorbritannien | Storbritannien, västra|
| USDoDCentral | USA DoD, centrala|
| USDoDEast | USA DoD, östra|
| USGovArizona | USGov Arizona|
| usgoviowa | USGov Iowa|
| USGovTexas | USGov Texas|
| usgovvirginia | USGov Virginia|
| USA, västra centrala | USA, västra centrala|
| Europa, västra | Europa, västra|
| Usavästracentrala | Indien, västra|
| westus | USA, västra|
| USA, västra 2 | USA, västra 2|


## <a name="virtual-machine-usage-faq"></a>Vanliga frågor om användning av virtuell dator
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Vilka resurser debiteras vid distribution av en virtuell dator?    
Virtuella datorer får kostnader för själva den virtuella datorn, all Premium-programvara som körs på den virtuella datorn, den lagrings account\managed disk som är kopplad till den virtuella datorn och nätverks bandbredds överföringarna från den virtuella datorn.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Hur kan jag se om en virtuell dator använder Azure Hybrid-förmån i CSV-filen för användning?
Om du distribuerar med hjälp av [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/)debiteras du den VM-taxa som inte är Windows-taxa eftersom du tar din egen licens till molnet. På din faktura kan du skilja på vilka virtuella datorer i Resource Manager som körs Azure Hybrid-förmån eftersom de har antingen "Windows \_ Server-BYOL" eller "Windows- \_ klientens BYOL" i kolumnen ImageType.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Hur skiljer sig Basic-och standard-VM-typer åt i användningen av CSV?
Både grundläggande och standard A-seriens virtuella datorer erbjuds. Om du distribuerar en virtuell dator i mätar under kategorin har den strängen "Basic". Om du distribuerar en standard virtuell dator med A-serien visas VM-storleken som "a1 VM" eftersom standard är standard. Mer information om skillnaderna mellan Basic och standard finns på [sidan med priser](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Vad är ExtraSmall, små, medel stora och ExtraLarge storlekar?
ExtraSmall-ExtraLarge är äldre namn för standard \_ a0 – standard \_ a4. I klassiska poster för VM-användning kan du se den här konventionen om du har distribuerat dessa storlekar.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Vad är skillnaden mellan mätar området och resurs platsen?
Mätar regionen är associerad med mätaren. För vissa Azure-tjänster som använder ett pris för alla regioner kan fältet mätar region vara tomt. Eftersom de virtuella datorerna har dedikerade priser per region för Virtual Machines fylls det här fältet i. På samma sätt är resurs platsen för Virtual Machines den plats där den virtuella datorn distribueras. Azure-regionerna i båda fälten är desamma, men de kan ha en annan sträng konvention för region namnet.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Varför är ImageType-värdet tomt i fältet ytterligare information?
Fältet ImageType är bara ifyllt för en delmängd av avbildningarna. Om du inte distribuerade någon av avbildningarna ovan är ImageType tomt.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Varför är VMName tomt i ytterligare information?
VMName fylls bara i ytterligare informations fält för virtuella datorer i en skalnings uppsättning. InstanceID-fältet innehåller det virtuella dator namnet för virtuella datorer som inte är skalade.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>Vad betyder ComputeHR i fältet UsageType i ytterligare information?
ComputeHR står för beräknings timme som representerar användnings händelsen för den underliggande infrastruktur kostnaden. Om UsageType är ComputeHR \_ SW representerar användnings händelsen Premium Software-avgiften för den virtuella datorn.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Hur gör jag för att veta om jag debiteras för Premium-program?
När du utforskar vilken VM-avbildning som passar bäst för dina behov bör du ta en titt på [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute). Avbildningen har program Plans priset. Om du ser "kostnads fri" för priset finns det ingen extra kostnad för program varan. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Vad är skillnaden mellan Microsoft. ClassicCompute och Microsoft. Compute i den förbrukade tjänsten?
Microsoft. ClassicCompute representerar klassiska resurser som distribueras via Azure-Service Manager. Om du distribuerar via Resource Manager fylls Microsoft. Compute i den förbrukade tjänsten. Läs mer om [Azures distributions modeller](../azure-resource-manager/management/deployment-models.md).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Varför är InstanceID-fältet tomt för min virtuella dator användning?
Om du distribuerar via den klassiska distributions modellen är InstanceID-strängen inte tillgänglig.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Varför är taggarna för mina virtuella datorer inte flödar till användnings informationen?
Taggar endast flödar till dig för användning av CSV för endast virtuella Resource Manager-datorer. Klassiska resurs taggar är inte tillgängliga i användnings informationen.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Hur kan den förbrukade kvantiteten vara mer än 24 timmar en dag?
I den klassiska modellen sammanställs faktureringen av resurser på moln tjänst nivån. Om du har fler än en virtuell dator i en moln tjänst som använder samma fakturerings mätare sammanställs användningen tillsammans. Virtuella datorer som distribueras via Resource Manager debiteras på VM-nivå, så denna agg regering gäller inte.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Varför är prissättning inte tillgängligt för DS/FS/GS/LS-storlekar på prissättnings Sidan?
Virtuella datorer med Premium Storage-kapacitet debiteras enligt samma taxa som icke-Premium Storage-kompatibla virtuella datorer. Endast dina lagrings kostnader skiljer sig åt. Besök [sidan med pris för lagring](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) för mer information.

## <a name="next-steps"></a>Nästa steg
Mer information om din användnings information finns i [förstå din faktura för Microsoft Azure.](../cost-management-billing/understand/review-individual-bill.md)