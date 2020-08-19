---
title: Publicerings guide för mall för Azure Applications-lösningar – Azure Marketplace
description: I den här artikeln beskrivs kraven för att publicera Solution-mallar på Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 04/22/2020
ms.openlocfilehash: 2ccf5b99a5b1ec1db5e6bfb32911ee2f0094995e
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88506136"
---
# <a name="publishing-guide-for-azure-applications-solution-template-offers"></a>Guide för publicerings guide för Azure Applications-lösningar

I den här artikeln beskrivs kraven för att publicera lösnings mal len, vilket är ett sätt att publicera Azure-programerbjudanden på Azure Marketplace. I lösnings mallens erbjudande typ krävs en [Azure Resource Manager-mall (arm-mall)](../azure-resource-manager/templates/overview.md) för att automatiskt distribuera lösnings infrastrukturen.

Använd mallen för Azure Application *Solution-mallen* under följande omständigheter:

- Lösningen kräver ytterligare distributions-och konfigurations automatisering utöver en enskild virtuell dator (VM), till exempel en kombination av virtuella datorer, nätverk och lagrings resurser.
- Dina kunder kommer att hantera själva lösningen.

Anropet till den åtgärd som en kund ser för den här erbjudande typen är *nu*.

## <a name="requirements-for-solution-template-offers"></a>Krav för lösnings mal len erbjuder

| **Krav** | **Information**  |
| ---------------  | -----------  |
|Fakturering och mätning    |  Lösnings mal len erbjuder inte transaktions erbjudanden, men de kan användas för att distribuera betalda VM-erbjudanden som debiteras via Microsofts kommersiella marknads plats. De resurser som lösningens ARM-mall distribuerar konfigureras i kundens Azure-prenumeration. Virtuella datorer som du betalar per användning samverkar med kunden via Microsoft och faktureras via kundens Azure-prenumeration.<br/> För att få en BYOL-fakturering, även om Microsoft fakturerar infrastruktur kostnader som uppstår i kund prenumerationen, kan du använda licens avgifterna för program varan med kunden direkt.   |
|Azure-kompatibel virtuell hård disk (VHD)  |   Virtuella datorer måste byggas på Windows eller Linux. Mer information finns i: <ul> <li>[Skapa ett erbjudande för Azure-program](./partner-center-portal/create-new-azure-apps-offer.md) (för Windows-VHD: er).</li><li>[Linux-distributioner](../virtual-machines/linux/endorsed-distros.md) som har godkänts på Azure (för Linux-VHD: er).</li></ul> |
| Spårning av kundanvändning | Aktivering av kund användnings behörighet krävs för alla Solution-mallar som publicerats på Azure Marketplace. Mer information om kund användnings behörighet och hur du aktiverar det finns i [Azure-partner kund användnings behörighet](./azure-partner-customer-usage-attribution.md).  |
| Använd Managed disks | [Managed disks](../virtual-machines/windows/managed-disks-overview.md) är standard alternativet för bestående diskar av infrastruktur som en tjänst (IaaS) virtuella datorer i Azure. Du måste använda Managed disks i Solution templates. <ul><li>Om du vill uppdatera dina Solution templates följer du rikt linjerna i [använda hanterade diskar i Azure Resource Manager mallar](../virtual-machines/using-managed-disks-template-deployments.md)och använder de angivna [exemplen](https://github.com/Azure/azure-quickstart-templates).<br><br> </li><li>Om du vill publicera den virtuella hård disken som en avbildning på Azure Marketplace importerar du den underliggande virtuella hård disken för de hanterade diskarna till ett lagrings konto med någon av följande metoder:<ul><li>[Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md) </li> <li> [Azure CLI](../virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd.md) </li> </ul></ul> |

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det, lär du dig hur du kan [Utöka din moln verksamhet med Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Registrera dig och börja arbeta i Partner Center:

- [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa eller slutföra ditt erbjudande.
- Mer information finns i [skapa ett erbjudande för Azure-program](./partner-center-portal/create-new-azure-apps-offer.md) .
