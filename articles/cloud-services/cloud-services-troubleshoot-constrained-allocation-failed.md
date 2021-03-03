---
title: Felsöka ConstrainedAllocationFailed när du distribuerar en moln tjänst (klassisk) till Azure | Microsoft Docs
description: Den här artikeln visar hur du löser ett ConstrainedAllocationFailed-undantag när du distribuerar en moln tjänst (klassisk) till Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 346e7eb77039ab80e6f9dffb8ea8360198040504
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738299"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-classic-to-azure"></a>Felsöka ConstrainedAllocationFailed när du distribuerar en moln tjänst (klassisk) till Azure

I den här artikeln får du felsöka allokeringsfel där Azure Cloud Services (klassisk) inte kan distribueras på grund av tilldelnings begränsningar.

När du distribuerar instanser till en moln tjänst (klassisk) eller lägger till nya webb-eller arbets Rolls instanser, allokerar Microsoft Azure beräknings resurser.

Du kan ibland få fel under dessa åtgärder även innan du når gränsen för Azure-prenumerationen.

> [!TIP]
> Informationen kan också vara användbar när du planerar distributionen av dina tjänster.

## <a name="symptom"></a>Symptom

I Azure Portal navigerar du till din moln tjänst (klassisk) och i panelen Välj *Åtgärds logg (klassisk)* för att visa loggarna.

![Bild visar bladet åtgärds logg (klassisk).](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

När du har granskat loggarna för din moln tjänst (klassisk) visas följande undantag:

|Undantags typ  |Felmeddelande  |
|---------|---------|
|ConstrainedAllocationFailed     |Azure-åtgärden `{Operation ID}` misslyckades med kod Compute. ConstrainedAllocationFailed. Information: allokeringen misslyckades; Det går inte att uppfylla begränsningarna i begäran. Den begärda nya tjänstdistributionen är kopplad till en tillhörighetsgrupp eller är riktad mot ett virtuellt nätverk, eller så finns det en befintlig distribution under den här värdbaserade tjänsten. Något av dessa villkor begränsar den nya distributionen till vissa Azure-resurser. Försök igen senare eller försök att minska storleken på den virtuella datorn eller antalet roll instanser. Alternativt kan du, om möjligt, ta bort de ovannämnda begränsningarna eller prova att distribuera till en annan region.|

## <a name="cause"></a>Orsak

När den första instansen distribueras till en moln tjänst (antingen i mellanlagring eller produktion) fästs moln tjänsten i ett kluster.

Med tiden kan resurserna i det här klustret bli helt använda. Om en moln tjänst (klassisk) gör en allokering av fler resurser när det inte finns tillräckligt med resurser i det fästa klustret, leder begäran till ett allokeringsfel. Mer information finns i [vanliga frågor om tilldelnings fel](cloud-services-allocation-failures.md#common-issues).

## <a name="solution"></a>Lösning

Befintliga moln tjänster är *fästa* på ett kluster. Eventuella ytterligare distributioner för moln tjänsten (klassisk) sker i samma kluster.

När ett allokeringsfel uppstår i det här scenariot är den rekommenderade åtgärden att omdistribuera till en ny moln tjänst (klassisk) (och uppdatera *CNAME*).

> [!TIP]
> Den här lösningen är förmodligen bäst eftersom den gör att plattformen kan välja bland alla kluster i den regionen.

> [!NOTE]
> Den här lösningen bör ådra sig noll stillestånds tid.

1. Distribuera arbets belastningen till en ny moln tjänst (klassisk).
    - Mer information finns i guiden [skapa och distribuera en moln tjänst (klassisk)](cloud-services-how-to-create-deploy-portal.md) .

    > [!WARNING]
    > Om du inte vill förlora den IP-adress som är kopplad till distributions facket kan du använda [Lösning 3 – Behåll IP-adressen](cloud-services-allocation-failures.md#solutions).

1. Uppdatera *CNAME* -eller *A* -posten för att peka trafik till den nya moln tjänsten (klassisk).
    - Mer information finns i [Konfigurera ett anpassat domän namn för en guide för Azure Cloud Service (klassisk)](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records) .

1. När noll-trafik skickas till den gamla platsen kan du ta bort den gamla moln tjänsten (klassisk).
    - Mer information finns i guiden [ta bort distributioner och en moln tjänst (klassisk)](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service) .
    - Information om hur du ser nätverks trafiken i din moln tjänst (klassisk) finns i [Introduktion till moln tjänst (klassisk) övervakning](cloud-services-how-to-monitor.md).

Se [fel sökning av allokeringsfel för Cloud Service (klassisk) | Microsoft Docs](cloud-services-allocation-failures.md#common-issues) för ytterligare reparations steg.

## <a name="next-steps"></a>Nästa steg

För fler lösningar för allokeringsfel och bakgrunds information:

> [!div class="nextstepaction"]
> [Allokeringsfel – moln tjänst (klassisk)](cloud-services-allocation-failures.md)

Om ditt Azure-problem inte åtgärdas i den här artikeln kan du gå till Azure-forumen på [MSDN och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan publicera ditt problem i dessa forum eller publicera på [ @AzureSupport Twitter](https://twitter.com/AzureSupport). Du kan också skicka en support förfrågan för Azure. Om du vill skicka en supportbegäran väljer du *Hämta support* på sidan [Support för Azure](https://azure.microsoft.com/support/options/) .