---
title: Felsöka FabricInternalServerError eller ServiceAllocationFailure när du distribuerar en moln tjänst (klassisk) till Azure | Microsoft Docs
description: Den här artikeln visar hur du löser ett FabricInternalServerError-eller ServiceAllocationFailure-undantag när du distribuerar en moln tjänst (klassisk) till Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 0a4111f569b751ace80a2a886ed2ce7e4968ce16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746755"
---
# <a name="troubleshoot-fabricinternalservererror-or-serviceallocationfailure-when-deploying-a-cloud-service-classic-to-azure"></a>Felsöka FabricInternalServerError eller ServiceAllocationFailure när du distribuerar en moln tjänst (klassisk) till Azure

I den här artikeln får du felsöka allokeringsfel där Fabric Controller inte kan allokeras när du distribuerar en Azure Cloud Service (klassisk).

När du distribuerar instanser till en moln tjänst eller lägger till nya webb-eller arbets Rolls instanser, allokerar Microsoft Azure beräknings resurser.

Du kan ibland få fel under dessa åtgärder även innan du når gränsen för Azure-prenumerationen.

> [!TIP]
> Informationen kan också vara användbar när du planerar distributionen av dina tjänster.

## <a name="symptom"></a>Symptom

I Azure Portal navigerar du till din moln tjänst (klassisk) och i panelen Välj *Åtgärds logg (klassisk)* för att visa loggarna.

![Bild visar bladet åtgärds logg (klassisk).](./media/cloud-services-troubleshoot-fabric-internal-server-error/cloud-services-troubleshoot-allocation-logs.png)

När du har granskat loggarna för din moln tjänst (klassisk) visas följande undantag:

|Undantag  |Felmeddelande  |
|---------|---------|
FabricInternalServerError     |Åtgärden misslyckades med felkod "InternalError" och errorMessage "servern påträffade ett internt fel. Försök utföra begäran igen.|
|ServiceAllocationFailure     |Åtgärden misslyckades med felkod "InternalError" och errorMessage "servern påträffade ett internt fel. Försök utföra begäran igen.|

## <a name="cause"></a>Orsak

*FabricInternalServerError* och *ServiceAllocationFailure* är undantag som kan uppstå när infrastruktur styrenheten inte kan allokera instanser i klustret. Rotor saken varierar om moln tjänsten är **Fäst** eller **inte fäst**.

- [**Inte fäst:** Problem vid en första distribution av en ny moln tjänst](#not-pinned-to-a-cluster)
- [**Fäst:** Problem från befintliga moln tjänster](#pinned-to-a-cluster)

> [!NOTE]
> När den första instansen distribueras till en moln tjänst (antingen i mellanlagring eller produktion) fästs moln tjänsten i ett kluster.
>
> Med tiden kan resurserna i den här resurspoolen bli fullt utnyttjade. Om en moln tjänst gör en tilldelnings förfrågan för ytterligare resurser när det inte finns tillräckligt med resurser i den fasta resurspoolen, leder begäran till ett [allokeringsfel](cloud-services-allocation-failures.md).

## <a name="solution"></a>Lösning

Följ rikt linjerna för tilldelnings felen i följande scenarier.

### <a name="not-pinned-to-a-cluster"></a>Inte fäst i ett kluster

Första gången du distribuerar en moln tjänst (klassisk) har klustret inte valts ännu, så moln tjänsten är inte *Fäst*. Det kan finnas ett distributions problem i Azure eftersom:

- Du har valt en viss storlek som inte är tillgänglig i regionen.
- Den kombination av storlekar som krävs för olika roller är inte tillgänglig i regionen.

När du upplever ett allokeringsfel i det här scenariot är den rekommenderade åtgärden att kontrol lera de tillgängliga storlekarna i regionen och ändra storleken som du har angett tidigare.

1. Du kan kontrol lera vilka storlekar som är tillgängliga i en region på sidan [produkter för moln tjänster (klassiska)](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services) .

    > [!NOTE]
    > Sidan *produkter* visar inte den tillgängliga kapaciteten. För alla nya allokeringar ska Azure kunna välja det optimala klustret i din region vid den tidpunkten.

1. Uppdatera tjänst definitions filen för din moln tjänst (klassisk) om du vill ange en annan [produkt storlek](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services) från din region.

### <a name="pinned-to-a-cluster"></a>Fäst i ett kluster

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
