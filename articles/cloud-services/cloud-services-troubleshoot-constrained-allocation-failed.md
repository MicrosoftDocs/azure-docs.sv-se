---
title: Felsöka ConstrainedAllocationFailed när du distribuerar en molntjänst (klassisk) till Azure | Microsoft Docs
description: Den här artikeln visar hur du löser ett ConstrainedAllocationFailed-undantag när du distribuerar en molntjänst (klassisk) till Azure.
services: cloud-services
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 4a92246f81ddd10840bb0dcf7edf2b01853fd148
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876606"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-classic-to-azure"></a>Felsöka ConstrainedAllocationFailed när du distribuerar en molntjänst (klassisk) till Azure

I den här artikeln felsöker du allokeringsfel där Azure Cloud-tjänster (klassisk) inte kan distribueras på grund av allokeringsbegränsningar.

När du distribuerar instanser till en molntjänst (klassisk) eller lägger till nya webb- eller arbetsrollinstanser Microsoft Azure till beräkningsresurser.

Du kan ibland få fel under dessa åtgärder även innan du når gränsen för Azure-prenumerationen.

> [!TIP]
> Informationen kan också vara användbar när du planerar distributionen av dina tjänster.

## <a name="symptom"></a>Symptom

I Azure Portal du till molntjänsten (klassisk) och i sidopanelen väljer du *Åtgärdslogg (klassisk) för* att visa loggarna.

![Bild som visar bladet Åtgärdslogg (klassisk).](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

När du inspekterar loggarna för molntjänsten (klassisk) visas följande undantag:

|Undantagstyp  |Felmeddelande  |
|---------|---------|
|ConstrainedAllocationFailed     |Azure-åtgärden `{Operation ID}` misslyckades med koden Compute.ConstrainedAllocationFailed. Information: Allokeringen misslyckades; kan inte uppfylla begränsningar i begäran. Den begärda nya tjänstdistributionen är kopplad till en tillhörighetsgrupp eller är riktad mot ett virtuellt nätverk, eller så finns det en befintlig distribution under den här värdbaserade tjänsten. Alla dessa villkor begränsar den nya distributionen till specifika Azure-resurser. Försök igen senare eller försök att minska storleken på den virtuella datorn eller antalet rollinstanser. Alternativt kan du, om möjligt, ta bort de ovannämnda begränsningarna eller prova att distribuera till en annan region.|

## <a name="cause"></a>Orsak

När den första instansen distribueras till en molntjänst (i mellanlagring eller produktion) fästs molntjänsten i ett kluster.

Med tiden kan resurserna i det här klustret utnyttjas fullt ut. Om en molntjänst (klassisk) gör en allokeringsbegäran för fler resurser när det inte finns tillräckligt med resurser i det fästa klustret, resulterar begäran i ett allokeringsfel. Mer information finns i Vanliga problem [med allokeringsfel.](cloud-services-allocation-failures.md#common-issues)

## <a name="solution"></a>Lösning

Befintliga molntjänster *fästs* på ett kluster. Eventuella ytterligare distributioner för molntjänsten (klassisk) sker i samma kluster.

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

Om ditt Azure-problem inte åtgärdas i den här artikeln går du till Azure-forumen på [MSDN och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan publicera ditt problem i dessa forum eller publicera det [ @AzureSupport på Twitter.](https://twitter.com/AzureSupport) Du kan också skicka en Azure-supportbegäran. Om du vill skicka en supportbegäran går du [till Azure-supportsidan](https://azure.microsoft.com/support/options/) och väljer *Få support.*