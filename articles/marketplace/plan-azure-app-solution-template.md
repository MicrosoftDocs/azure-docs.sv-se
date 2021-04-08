---
title: Planera en lösnings mall för ett erbjudande för Azure-program
description: Ta reda på vad som krävs för att skapa en plan för lösnings mal len för ett nytt Azure-program med hjälp av den kommersiella Marketplace-portalen i Microsoft Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: aab856b1e1d2d991cc4964d061a990dbedbeddb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98876519"
---
# <a name="plan-a-solution-template-for-an-azure-application-offer"></a>Planera en lösnings mall för ett erbjudande för Azure-program

I den här artikeln beskrivs kraven för att publicera en plan för lösnings mal len för ett Azure Application erbjudande. En plan för lösnings mal len är en av de två typerna av planer som stöds av Azure Application erbjudanden. Information om skillnaden mellan dessa två typer av prenumerationer finns i [typer av planer](plan-azure-application-offer.md#plans). Om du inte redan har gjort det kan du läsa [planera ett erbjudande för Azure-program](plan-azure-application-offer.md).

Plan typen för lösnings mal len kräver en [Azure Resource Manager mall (arm-mall)](../azure-resource-manager/templates/overview.md) för att automatiskt distribuera lösnings infrastrukturen.

## <a name="solution-template-requirements"></a>Krav för lösnings mal len

| Krav | Information |
| ------------ | ------------- |
| Fakturering och mätning | Lösningar för lösnings mal len är inte transactable, men de kan användas för att distribuera betalda VM-erbjudanden som debiteras via Microsofts kommersiella marknads plats. De resurser som lösningens ARM-mall distribuerar konfigureras i kundens Azure-prenumeration. Virtuella datorer som du betalar per användning samverkar med kunden via Microsoft och faktureras via kundens Azure-prenumeration. <br><br> För att få en BYOL-fakturering, även om Microsoft fakturerar infrastruktur kostnader som uppstår i kund prenumerationen, kan du använda licens avgifterna för program varan med kunden direkt. |
| Azure-kompatibel virtuell hård disk (VHD) | Virtuella datorer måste byggas på Windows eller Linux. Mer information finns i:<ul><li>[Skapa en teknisk till gång för Azure VM](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) (för Windows-VHD: er)</li><li>[Linux-distributioner](../virtual-machines/linux/endorsed-distros.md) som har godkänts på Azure (för Linux-VHD: er).</li></ul> |
| Spårning av kundanvändning | Aktivering av kund användnings behörighet krävs för alla Solution-mallar som publicerats på Azure Marketplace. Mer information om kund användnings behörighet och hur du aktiverar det finns i [Azure-partner kund användnings behörighet](azure-partner-customer-usage-attribution.md). |
| Använd Managed disks | [Managed disks](../virtual-machines/managed-disks-overview.md) är standard alternativet för bestående diskar av infrastruktur som en tjänst (IaaS) virtuella datorer i Azure. Du måste använda Managed disks i Solution templates.<ul><li>Om du vill uppdatera dina Solution templates följer du rikt linjerna i [använda hanterade diskar i Azure Resource Manager mallar](../virtual-machines/using-managed-disks-template-deployments.md)och använder de angivna [exemplen](https://github.com/Azure/azure-quickstart-templates).</li><li>Om du vill publicera den virtuella hård disken som en avbildning på Azure Marketplace importerar du den underliggande virtuella hård disken för de hanterade diskarna till ett lagrings konto med hjälp av antingen [Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd) eller [Azure CLI](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd)</ul> |
| Distributions paket | Du behöver ett distributions paket som gör det möjligt för kunderna att distribuera planen. Om du skapar flera planer som kräver samma tekniska konfiguration kan du använda samma plan paket. Mer information finns i nästa avsnitt: distributions paket. |
|||

## <a name="deployment-package"></a>Distributions paket

Distributions paketet innehåller alla mallfiler som behövs för den här planen, samt ytterligare resurser, paketerade som en. zip-fil.

Alla Azure-program måste innehålla de här två filerna i rotmappen för ett zip-arkiv:

- En mall för en Resource Manager-mall med namnet [mainTemplate.jspå](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template). Den här mallen definierar de resurser som ska distribueras till kundens Azure-prenumeration. Exempel på Resource Manager-mallar finns i [galleriet för Azure snabb starts mallar](https://azure.microsoft.com/documentation/templates/) eller motsvarande [GitHub: Azure Resource Manager snabb starts mallar](https://github.com/azure/azure-quickstart-templates) lagrings platsen.
- En definition av användar gränssnittet för att skapa Azure-program med namnet [createUiDefinition.jspå](../azure-resource-manager/managed-applications/create-uidefinition-overview.md). I användargränssnittet anger du element som ger konsumenterna möjlighet att ange parametervärden.

Högsta antal fil storlekar som stöds är:

- Upp till 1 GB totalt komprimerat zip-arkiv storlek
- Upp till 1 GB för varje enskild okomprimerad fil i zip-arkivet

Alla nya erbjudanden för Azure-program måste också innehålla ett GUID för [Azure-partner kund användnings behörighet](azure-partner-customer-usage-attribution.md) .

## <a name="azure-regions"></a>Azure-regioner

Du kan publicera planen till den offentliga Azure-regionen, Azure Government region eller både och. Innan du publicerar till [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md)ska du testa och validera planen i miljön eftersom vissa slut punkter kan skilja sig. Ställ in och testa planen genom att begära ett utvärderings konto från [Microsoft Azure Government utvärderings version](https://azure.microsoft.com/global-infrastructure/government/request/).

Du, som utgivare, är ansvariga för alla kontroller, säkerhets åtgärder och bästa praxis. Azure Government använder fysiskt isolerade Data Center och nätverk (som finns i USA).

En lista över länder och regioner som stöds av den kommersiella marknads platsen finns i [stöd för geografisk tillgänglighet och valuta](marketplace-geo-availability-currencies.md).

Azure Government Services hanterar data som omfattas av vissa myndighets bestämmelser och krav. Till exempel FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 och CJIS. För att få kännedom om dina certifieringar för dessa program kan du ge upp till 100 länkar som beskriver dem. De kan antingen vara länkar till din lista över programmet direkt eller länkar till beskrivningar av dina efterlevnad med dem på dina egna webbplatser. Dessa länkar är bara synliga för Azure Government kunder.

## <a name="choose-who-can-see-your-plan"></a>Välj vem som kan se din plan

Du kan konfigurera varje plan så att den blir synlig för alla (offentlig) eller endast till en speciell publik (privat). Du kan skapa upp till 100-planer och upp till 45 av dem som kan vara privata. Du kanske vill skapa en privat plan för att erbjuda olika pris alternativ eller tekniska konfigurationer för vissa kunder.

Du beviljar åtkomst till en privat plan med Azure-prenumerations-ID: n med alternativet att inkludera en beskrivning av varje prenumerations-ID som du tilldelar. Du kan lägga till högst 10 prenumerations-ID: n manuellt eller upp till 10 000 prenumerations-ID med hjälp av en. CSV-fil. ID: n för Azure-prenumerationen visas som GUID och bokstäver måste vara gemena.

> [!NOTE]
> Om du publicerar en privat plan kan du ändra dess synlighet till offentlig senare. Men när du har publicerat ett offentligt abonnemang kan du inte ändra dess synlighet till privat.

För lösnings mal Lav planer kan du också välja att dölja planen från Azure Marketplace. Du kanske vill göra detta om planen bara distribueras indirekt via en annan lösnings mall eller ett hanterat program.

> [!NOTE]
> Privata planer stöds inte med Azure-prenumerationer som upprättats via en åter försäljare av Cloud Solution Provider-programmet (CSP).

Mer information finns [i privata erbjudanden på Microsofts kommersiella marknads](private-offers.md)plats.

## <a name="next-steps"></a>Nästa steg

- [Så här skapar du ett erbjudande för Azure-program på den kommersiella Marketplace](create-new-azure-apps-offer.md)