---
title: Flytta Azure-resurser mellan resursgrupper, prenumerationer eller regioner.
description: Översikt över Azure-resurstyper som kan flyttas mellan resursgrupper, prenumerationer eller regioner.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 646c0892ac3609fc531e91f4bfb9c1de6a61bc38
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730508"
---
# <a name="move-azure-resources-across-resource-groups-subscriptions-or-regions"></a>Flytta Azure-resurser mellan resursgrupper, prenumerationer eller regioner

Azure-resurser kan flyttas till en ny resursgrupp eller prenumeration, eller mellan regioner.

## <a name="move-resources-across-resource-groups-or-subscriptions"></a>Flytta resurser mellan resursgrupper eller prenumerationer

Du kan flytta Azure-resurser till antingen en annan Azure-prenumeration eller en annan resursgrupp under samma prenumeration. Du kan använda Azure-portalen, Azure PowerShell, Azure CLI eller REST-API:et för att flytta resurser. Mer information finns i Flytta [resurser till en ny resursgrupp eller prenumeration.](move-resource-group-and-subscription.md)

### <a name="upgrade-a-subscription"></a>Uppgradera en prenumeration

Om du faktiskt vill uppgradera din Azure-prenumeration (till exempel byta från kostnadsfri till att betala per användning) måste du konvertera din prenumeration.

- Om du vill uppgradera en kostnadsfri utvärderingsversion kan du läsa [Uppgradera din kostnadsfria utvärderingsversion eller Microsoft Imagine Azure-prenumeration till Betala per användning.](../../cost-management-billing/manage/upgrade-azure-subscription.md)
- Om du vill ändra ett betala per användning-konto kan du läsa Ändra din Betala per [användning-prenumeration för Azure till ett annat erbjudande.](../../cost-management-billing/manage/switch-azure-offer.md)

Om du inte kan konvertera prenumerationen skapar du [en Azure-supportbegäran.](../../azure-portal/supportability/how-to-create-azure-support-request.md) Välj **Prenumerationshantering** som typ av problem.

## <a name="move-resources-across-regions"></a>Flytta resurser mellan regioner

Geografiska områden, regioner och tillgänglighetszoner i Azure utgör grunden för den globala Azure-infrastrukturen. Azures [geografiska områden innehåller](https://azure.microsoft.com/global-infrastructure/geographies/) vanligtvis två eller flera [Azure-regioner.](https://azure.microsoft.com/global-infrastructure/regions/) En region är ett område inom ett geografiskt område som Tillgänglighetszoner och flera datacenter.

När du har distribuerat resurser i en viss Azure-region finns det många orsaker till att du kanske vill flytta resurser till en annan region.

- **Anpassa till en regionstart:** Flytta dina resurser till en nyligen introducerad Azure-region som inte var tillgänglig tidigare.
- **Justera för tjänster/funktioner:** Flytta resurser för att dra nytta av tjänster eller funktioner som är tillgängliga i en viss region.
- **Svara på affärsutvecklingen:** Flytta resurser till en region som svar på affärsförändringar, till exempel sammanslagningar eller förvärv.
- **Justera för närhet:** Flytta resurser till en region som är lokal för din verksamhet.
- **Uppfylla datakrav:** Flytta resurser för att uppfylla kraven på datahemhemlighet eller dataklassificering. [Läs mer](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Svara på distributionskraven:** Flytta resurser som har distribuerats i fel eller flytta som svar på kapacitetsbehov.
- **Svara på inaktivering:** Flytta resurser på grund av inaktiverade regioner.

### <a name="move-resources-with-resource-mover"></a>Flytta resurser med Resource Mover

Du kan flytta resurser till en annan region med [Azure Resource Mover](../../resource-mover/overview.md). Resource Mover tillhandahåller:

- En enda hubb för att flytta resurser mellan regioner.
- Kortare flytttid och komplexitet. Allt du behöver finns på en enda plats.
- En enkel och konsekvent upplevelse för att flytta olika typer av Azure-resurser.
- Ett enkelt sätt att identifiera beroenden mellan resurser som du vill flytta. Den här identifieringen hjälper dig att flytta relaterade resurser tillsammans, så att allt fungerar som förväntat i målregionen efter flytten.
- Automatisk rensning av resurser i källregionen om du vill ta bort dem efter flytten.
- Testning. Du kan prova en flytt och sedan ta bort den om du inte vill göra en fullständig flytt.

Du kan flytta resurser till en annan region med hjälp av ett par olika metoder:

- **Börja flytta resurser från en resursgrupp:** Med den här metoden startar du regionflyttningen från en resursgrupp. När du har valt de resurser som du vill flytta fortsätter processen i Resource Mover-hubben för att kontrollera resursberoenden och dirigera flyttprocessen. [Läs mer](../../resource-mover/move-region-within-resource-group.md).
- **Börja flytta resurser direkt från Resource Mover-hubben:** Med den här metoden startar du processen för regionsflyttning direkt i hubben. [Läs mer](../../resource-mover/tutorial-move-region-virtual-machines.md).

## <a name="next-steps"></a>Nästa steg

- Information om hur du kontrollerar om en resurstyp stöder flytt finns i [Stöd för flytt av resurser.](move-support-resources.md)
- Mer information om flyttprocessen för regioner finns [i Om flyttprocessen](../../resource-mover/about-move-process.md).
