---
title: Felsöka FabricInternalServerError eller ServiceAllocationFailure när du distribuerar en molntjänst (klassisk) till Azure | Microsoft Docs
description: Den här artikeln visar hur du löser ett FabricInternalServerError- eller ServiceAllocationFailure-undantag när du distribuerar en molntjänst (klassisk) till Azure.
services: cloud-services
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 0883178779179df2e531123b8a500c62d42530e4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877452"
---
# <a name="troubleshoot-fabricinternalservererror-or-serviceallocationfailure-when-deploying-a-cloud-service-classic-to-azure"></a>Felsöka FabricInternalServerError eller ServiceAllocationFailure när du distribuerar en molntjänst (klassisk) till Azure

I den här artikeln felsöker du allokeringsfel där infrastrukturkontrollanten inte kan allokera när du distribuerar en Azure-molntjänst (klassisk).

När du distribuerar instanser till en molntjänst eller lägger till nya webb- eller arbetsrollinstanser, allokerar Microsoft Azure beräkningsresurser.

Du kan ibland få fel under dessa åtgärder även innan du når gränsen för Azure-prenumerationen.

> [!TIP]
> Informationen kan också vara användbar när du planerar distributionen av dina tjänster.

## <a name="symptom"></a>Symptom

I Azure Portal du till molntjänsten (klassisk) och i sidopanelen väljer du *Åtgärdslogg (klassisk) för* att visa loggarna.

![Bild som visar bladet Åtgärdslogg (klassisk).](./media/cloud-services-troubleshoot-fabric-internal-server-error/cloud-services-troubleshoot-allocation-logs.png)

När du inspekterar loggarna för molntjänsten (klassisk) visas följande undantag:

|Undantag  |Felmeddelande  |
|---------|---------|
FabricInternalServerError     |Åtgärden misslyckades med felkoden "InternalError" och errorMessage "Servern påträffade ett internt fel. Försök att skicka begäran igen.".|
|ServiceAllocationFailure     |Åtgärden misslyckades med felkoden "InternalError" och errorMessage "Servern påträffade ett internt fel. Försök att skicka begäran igen.".|

## <a name="cause"></a>Orsak

*FabricInternalServerError* och *ServiceAllocationFailure* är undantag som kan uppstå när infrastrukturkontrollanten inte kan allokera instanser i klustret. Rotorsaken varierar om molntjänsten **är fäst eller** inte **fäst.**

- [**Inte fäst:** Fel från en första distribution av en ny molntjänst](#not-pinned-to-a-cluster)
- [**Fäst:** Fel från befintliga molntjänster](#pinned-to-a-cluster)

> [!NOTE]
> När den första instansen distribueras till en molntjänst (i mellanlagring eller produktion) fästs molntjänsten i ett kluster.
>
> Med tiden kan resurserna i den här resurspoolen bli fullt utnyttjade. Om en molntjänst gör en allokeringsbegäran för ytterligare resurser när det inte finns tillräckligt med resurser i den fästa resurspoolen, resulterar begäran i ett [allokeringsfel.](cloud-services-allocation-failures.md)

## <a name="solution"></a>Lösning

Följ riktlinjerna för allokeringsfel i följande scenarier.

### <a name="not-pinned-to-a-cluster"></a>Inte fäst på ett kluster

Första gången du distribuerar en molntjänst (klassisk) har klustret inte valts ännu, så molntjänsten är inte *fäst.* Azure kan ha ett distributionsfel eftersom:

- Du har valt en viss storlek som inte är tillgänglig i regionen.
- Kombinationen av storlekar som behövs för olika roller är inte tillgänglig i regionen.

När du får ett allokeringsfel i det här scenariot rekommenderar vi att du kontrollerar de tillgängliga storlekarna i regionen och ändrar den storlek som du angav tidigare.

1. Du kan kontrollera vilka storlekar som är tillgängliga i en region på [produktsidan Molntjänst (klassisk).](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services)

    > [!NOTE]
    > På *sidan* Produkter visas inte den tillgängliga kapaciteten. För alla nya allokeringar bör Azure kunna välja det optimala klustret i din region vid den tidpunkten.

1. Uppdatera tjänstdefinitionsfilen för molntjänsten (klassisk) för att ange en annan [produktstorlek](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services) än din region.

### <a name="pinned-to-a-cluster"></a>Fäst på ett kluster

Befintliga molntjänster *fästs* på ett kluster. Eventuella ytterligare distributioner för molntjänsten (klassisk) sker i samma kluster.

När du får ett allokeringsfel i det här scenariot rekommenderar vi att du distribuerar om till en ny molntjänst (klassisk) (och uppdaterar *CNAME*).

> [!TIP]
> Den här lösningen är förmodligen bäst eftersom den gör att plattformen kan välja bland alla kluster i den regionen.

> [!NOTE]
> Den här lösningen bör medföra noll driftstopp.

1. Distribuera arbetsbelastningen till en ny molntjänst (klassisk).
    - Mer information [finns i guiden Så här skapar och distribuerar du](cloud-services-how-to-create-deploy-portal.md) en molntjänst (klassisk).

    > [!WARNING]
    > Om du inte vill förlora IP-adressen som är associerad med det här distributionsfacket kan du använda [Lösning 3 – Behåll IP-adressen](cloud-services-allocation-failures.md#solutions).

1. Uppdatera *CNAME- eller* *A-posten* för att peka trafik till den nya molntjänsten (klassisk).
    - Mer information [finns i guiden Konfigurera ett anpassat domännamn för en Azure Cloud-tjänst (klassisk).](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records)

1. När ingen trafik kommer till den gamla platsen kan du ta bort den gamla molntjänsten (klassisk).
    - Mer information [finns i guiden Ta bort distributioner och en molntjänst (klassisk).](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service)
    - Om du vill se nätverkstrafiken i molntjänsten (klassisk) kan du gå [till Introduktion till molntjänstövervakning (klassisk).](cloud-services-how-to-monitor.md)

Se [Felsöka allokeringsfel för molntjänster (klassisk) | Microsoft Docs](cloud-services-allocation-failures.md#common-issues) ytterligare reparationssteg.

## <a name="next-steps"></a>Nästa steg

För fler lösningar för allokeringsfel och bakgrundsinformation:

> [!div class="nextstepaction"]
> [Allokeringsfel – molntjänst (klassisk)](cloud-services-allocation-failures.md)

Om ditt Azure-problem inte åtgärdas i den här artikeln går du till Azure-forumen på [MSDN och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan publicera ditt problem i dessa forum eller publicera det [ @AzureSupport på Twitter.](https://twitter.com/AzureSupport) Du kan också skicka en Azure-supportbegäran. Om du vill skicka en supportbegäran går du [till azure-supportsidan](https://azure.microsoft.com/support/options/) och väljer *Få support.*
