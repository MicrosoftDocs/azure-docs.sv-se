---
title: Felsöka moln tjänst (klassisk) allokeringsfel | Microsoft Docs
description: Felsök ett tilldelnings fel när du distribuerar Azure Cloud Services. Lär dig hur allokeringen fungerar och varför allokeringen kan gå sönder.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 95fe4a8e1f6c6ee5f519311f8e756be89a09acf8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738318"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-classic-in-azure"></a>Felsöka allokeringsfel när du distribuerar Cloud Services (klassisk) i Azure

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).


## <a name="summary"></a>Sammanfattning

När du distribuerar instanser till en moln tjänst eller lägger till nya webb-eller arbets Rolls instanser, allokerar Microsoft Azure beräknings resurser. Ibland kan fel uppstå när du utför dessa åtgärder även innan du når gränserna för Azure-prenumerationen. I den här artikeln beskrivs orsakerna till några vanliga allokeringsfel och förslag på möjliga åtgärder. Informationen kan också vara användbar när du planerar distributionen av dina tjänster.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Bakgrund – hur allokeringen fungerar

Servrarna i Azure-datacenter partitioneras i kluster. En ny begäran om allokering av moln tjänster görs i flera kluster. När den första instansen distribueras till en moln tjänst (antingen i mellanlagring eller produktion) fästs moln tjänsten i ett kluster. Eventuella ytterligare distributioner för moln tjänsten sker i samma kluster. I den här artikeln kommer vi att se detta som "fäst i ett kluster". Diagram 1 nedan illustrerar fallet med en normal allokering som görs i flera kluster. Diagram 2 visar fallet för en allokering som är fäst på kluster 2, eftersom det är där den befintliga moln tjänsten CS_1 finns.

![Tilldelnings diagram](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Varför allokeringsfel uppstår

När en tilldelnings förfrågan fästs i ett kluster, finns det en högre chans att det inte går att hitta kostnads fria resurser eftersom den tillgängliga resurspoolen är begränsad till ett kluster. Om din tilldelnings förfrågan fästs i ett kluster, men den typ av resurs som du har begärt inte stöds av det klustret, kommer begäran att Miss Miss förväntas även om klustret har en kostnads fri resurs. Diagram 3 nedan visar det fall där en fäst allokering Miss lyckas eftersom det enda kandidat klustret inte har några lediga resurser. Diagram 4 visar det fall där en fäst allokering Miss lyckas eftersom det enda kandidat klustret inte stöder den begärda virtuella dator storleken, även om klustret har kostnads fria resurser.

![Det har fästs ett allokeringsfel](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Felsöka allokeringsfel för moln tjänster

### <a name="error-message"></a>Felmeddelande

I Azure Portal går du till moln tjänsten och väljer *Åtgärds loggar (klassisk)* i marginal listen för att visa loggarna.

Se ytterligare lösningar för undantagen nedan:

|Undantags typ  |Felmeddelande  |Lösning  |
|---------|---------|---------|
|FabricInternalServerError     |Åtgärden misslyckades med felkod "InternalError" och errorMessage "servern påträffade ett internt fel. Försök utföra begäran igen.|[Felsöka FabricInternalServerError](cloud-services-troubleshoot-fabric-internal-server-error.md)|
|ServiceAllocationFailure     |Åtgärden misslyckades med felkod "InternalError" och errorMessage "servern påträffade ett internt fel. Försök utföra begäran igen.|[Felsöka ServiceAllocationFailure](cloud-services-troubleshoot-fabric-internal-server-error.md)|
|LocationNotFoundForRoleSize     |Åtgärden `{Operation ID}` misslyckades: den begärda VM-nivån är för närvarande inte tillgänglig i region ( `{Region ID}` ) för den här prenumerationen. Försök med en annan nivå eller distribuera till en annan plats.|[Felsöka LocationNotFoundForRoleSize](cloud-services-troubleshoot-location-not-found-for-role-size.md)|
|ConstrainedAllocationFailed     |Azure-åtgärden `{Operation ID}` misslyckades med kod Compute. ConstrainedAllocationFailed. Information: allokeringen misslyckades; Det går inte att uppfylla begränsningarna i begäran. Den begärda nya tjänstdistributionen är kopplad till en tillhörighetsgrupp eller är riktad mot ett virtuellt nätverk, eller så finns det en befintlig distribution under den här värdbaserade tjänsten. Något av dessa villkor begränsar den nya distributionen till vissa Azure-resurser. Försök igen senare eller prova att minska storleken på den virtuella datorn eller antalet rollinstanser. Alternativt kan du, om möjligt, ta bort de ovannämnda begränsningarna eller prova att distribuera till en annan region.|[Felsöka ConstrainedAllocationFailed](cloud-services-troubleshoot-constrained-allocation-failed.md)|
|OverconstrainedAllocationRequest     |Den VM-storlek (eller kombination av VM-storlekar) som krävs för den här distributionen kan inte tillhandahållas på grund av krav för distributions begär Anden. Om möjligt kan du prova slappa-begränsningar som virtuella nätverks bindningar, distribuera till en värdbaserad tjänst utan andra distributioner och till en annan tillhörighets grupp eller utan någon tillhörighets grupp, eller försök att distribuera till en annan region.|[Felsöka OverconstrainedAllocationRequest](cloud-services-troubleshoot-overconstrained-allocation-request.md)|

Exempel på fel meddelande:

> "Azure-åtgärden {operation ID} misslyckades med kod Compute. ConstrainedAllocationFailed. Information: allokeringen misslyckades; Det går inte att uppfylla begränsningarna i begäran. Den begärda nya tjänstdistributionen är kopplad till en tillhörighetsgrupp eller är riktad mot ett virtuellt nätverk, eller så finns det en befintlig distribution under den här värdbaserade tjänsten. Något av dessa villkor begränsar den nya distributionen till vissa Azure-resurser. Försök igen senare eller prova att minska storleken på den virtuella datorn eller antalet rollinstanser. Alternativt kan du, om möjligt, ta bort de ovannämnda begränsningarna eller prova att distribuera till en annan region. "

### <a name="common-issues"></a>Vanliga problem

Här är vanliga distributions scenarier som gör att en allokering av begäran fästs i ett enda kluster.

* Distribuera till mellanlagringsplats – om en moln tjänst har en distribution i någon av platserna fästs hela moln tjänsten i ett särskilt kluster.  Det innebär att om en distribution redan finns på produktionsplatsen kan en ny mellanlagringsdistribution endast allokeras i samma kluster som produktionsplatsen. Om klustret närmar sig kapaciteten kan begäran Miss lyckas.
* Skalning – När nya instanser läggs till i en befintlig molntjänst måste de allokeras i samma kluster.  Små skalningsbegäranden går vanligtvis att allokera, men inte alltid. Om klustret närmar sig kapaciteten kan begäran Miss lyckas.
* Tillhörighets grupp – en ny distribution till en tom moln tjänst kan allokeras av infrastruktur resursen i alla kluster i den regionen, om inte moln tjänsten fästs på en tillhörighets grupp. Distributioner till samma tillhörighets grupp görs i samma kluster. Om klustret närmar sig kapaciteten kan begäran Miss lyckas.
* Tillhörighets grupp vNet-äldre virtuella nätverk var knutna till tillhörighets grupper i stället för regioner och moln tjänster i dessa virtuella nätverk fästs i tillhörighets grupps klustret. Distributioner till den här typen av virtuellt nätverk görs i det lokala klustret. Om klustret närmar sig kapaciteten kan begäran Miss lyckas.

## <a name="solutions"></a>Lösningar

1. Distribuera om till en ny moln tjänst – den här lösningen är förmodligen mest lyckad eftersom den tillåter plattformen att välja från alla kluster i den regionen.

   * Distribuera arbets belastningen till en ny moln tjänst  
   * Uppdatera CNAME-eller A-posten för att peka trafik till den nya moln tjänsten
   * När noll-trafik skickas till den gamla platsen kan du ta bort den gamla moln tjänsten. Den här lösningen bör ådra sig noll stillestånds tid.
2. Ta bort både produktions-och mellanlagringsplatser – den här lösningen bevarar ditt befintliga DNS-namn, men kommer att orsaka drift stopp för ditt program.

   * Ta bort produktions-och mellanlagrings platser för en befintlig moln tjänst så att moln tjänsten är tom och
   * Skapa en ny distribution i den befintliga moln tjänsten. Detta görs ett nytt försök att allokera på alla kluster i regionen. Se till att moln tjänsten inte är kopplad till en tillhörighets grupp.
3. Reserverad IP – den här lösningen kommer att bevara din befintliga IP-adress, men kommer att orsaka drift stopp för ditt program.  

   * Skapa en ReservedIP för din befintliga distribution med hjälp av PowerShell

     ```azurepowershell
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```

   * Följ #2 från ovan och se till att ange den nya ReservedIP i tjänstens CSCFG.
4. Ta bort tillhörighets grupp för nya distributioner – tillhörighets grupper rekommenderas inte längre. Följ stegen i punkt 1 ovan för att distribuera en ny molntjänst. Se till att moln tjänsten inte finns i en tillhörighets grupp.
5. Konvertera till en regional Virtual Network – se [hur du migrerar från tillhörighets grupper till en regional Virtual Network (VNet)](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet).
