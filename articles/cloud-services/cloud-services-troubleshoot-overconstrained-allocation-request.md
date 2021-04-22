---
title: Felsöka OverconstrainedAllocationRequest när du distribuerar en molntjänst (klassisk) till Azure | Microsoft Docs
description: Den här artikeln visar hur du löser ett OverconstrainedAllocationRequest-undantag när du distribuerar en molntjänst (klassisk) till Azure.
services: cloud-services
documentationcenter: ''
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 1a5880107aaa414da42fe5e36e0cb3315071d8a0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877434"
---
# <a name="troubleshoot-overconstrainedallocationrequest-when-deploying-cloud-services-classic-to-azure"></a>Felsöka OverconstrainedAllocationRequest när du distribuerar molntjänster (klassisk) till Azure

I den här artikeln felsöker du över begränsade allokeringsfel som förhindrar distribution av Azure Cloud Services (klassisk).

När du distribuerar instanser till en molntjänst eller lägger till nya webb- eller arbetsrollinstanser, allokerar Microsoft Azure beräkningsresurser.

Du kan ibland få fel under dessa åtgärder även innan du når gränsen för Azure-prenumerationen.

> [!TIP]
> Informationen kan också vara användbar när du planerar distributionen av dina tjänster.

## <a name="symptom"></a>Symptom

![Bild som visar bladet Åtgärdslogg (klassisk).](./media/cloud-services-troubleshoot-overconstrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

|Undantagstyp  |Felmeddelande  |
|---------|---------|
|OverconstrainedAllocationRequest |Vm-storleken (eller kombinationen av VM-storlekar) som krävs för den här distributionen kan inte etableras på grund av begränsningar för distributionsbegäran. Om möjligt kan du prova att lätta på begränsningar, till exempel bindningar för virtuella nätverk, distribuera till en värdtjänst utan någon annan distribution, till en annan tillhörighetsgrupp eller utan tillhörighetsgrupp, eller prova att distribuera till en annan region.|

## <a name="cause"></a>Orsak

Rotorsaken varierar om molntjänsten **är fäst eller** inte **fäst.**

- [**Inte fäst:** Fel från en första distribution av en ny molntjänst (klassisk)](#not-pinned-to-a-cluster)
- [**Fäst:** Fel från en befintlig molntjänst (klassisk)](#pinned-to-a-cluster)

> [!NOTE]
> När den första instansen distribueras till en molntjänst (i mellanlagring eller produktion) fästs molntjänsten i ett kluster.
>
> Med tiden kan resurserna i klustret utnyttjas fullt ut. Om en molntjänst (klassisk) gör en allokeringsbegäran för ytterligare resurser när det inte finns tillräckligt med resurser i det fästa klustret, resulterar begäran i ett [allokeringsfel.](cloud-services-allocation-failures.md)

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

Befintliga molntjänster *fästs på* ett kluster. Eventuella ytterligare distributioner för molntjänsten (klassisk) sker i samma kluster.

När du får ett allokeringsfel i det här scenariot rekommenderar vi att du distribuerar om till en ny molntjänst (klassisk) (och uppdaterar *CNAME*).

> [!TIP]
> Den här lösningen är förmodligen bäst eftersom den gör att plattformen kan välja bland alla kluster i den regionen.

> [!NOTE]
> Den här lösningen bör medföra noll avbrottstid.

1. Distribuera arbetsbelastningen till en ny molntjänst (klassisk).
    - Se guiden [Så här skapar och distribuerar du en molntjänst (klassisk)](cloud-services-how-to-create-deploy-portal.md) för ytterligare instruktioner.

    > [!WARNING]
    > Om du inte vill förlora IP-adressen som är kopplad till det här distributionsfacket kan du använda [Lösning 3 – Behåll IP-adressen](cloud-services-allocation-failures.md#solutions).

1. Uppdatera *CNAME- eller* *A-posten* för att peka trafik till den nya molntjänsten (klassisk).
    - Mer information [finns i guiden Konfigurera ett anpassat domännamn för en Azure-molntjänst (klassisk).](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records)

1. När ingen trafik kommer till den gamla webbplatsen kan du ta bort den gamla molntjänsten (klassisk).
    - Mer information [finns i guiden Ta bort distributioner och en molntjänst (klassisk).](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service)
    - Om du vill se nätverkstrafiken i din molntjänst (klassisk) kan du gå [till Introduktion till molntjänstövervakning (klassisk).](cloud-services-how-to-monitor.md)

Se [Felsöka allokeringsfel för molntjänster (klassisk) | Microsoft Docs](cloud-services-allocation-failures.md#common-issues) ytterligare reparationssteg.

## <a name="next-steps"></a>Nästa steg

För fler lösningar för allokeringsfel och bakgrundsinformation:

> [!div class="nextstepaction"]
> [Allokeringsfel – molntjänst (klassisk)](cloud-services-allocation-failures.md)

Om ditt Azure-problem inte åtgärdas i den här artikeln går du till Azure-forumen på [MSDN och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan publicera ditt problem i dessa forum eller publicera det [ @AzureSupport på Twitter.](https://twitter.com/AzureSupport) Du kan också skicka en Azure-supportbegäran. Om du vill skicka en supportbegäran går du [till azure-supportsidan](https://azure.microsoft.com/support/options/) och väljer *Få support.*
