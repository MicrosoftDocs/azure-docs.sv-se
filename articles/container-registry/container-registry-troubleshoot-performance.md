---
title: Felsöka registerprestanda
description: Symptom, orsaker och lösningar på vanliga problem med prestanda i ett register
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 15129ebe1da2e52fac106a34863f609c440549ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148401"
---
# <a name="troubleshoot-registry-performance"></a>Felsöka registerprestanda

Den här artikeln hjälper dig att felsöka problem som kan uppstå i Azure Container Registry. 

## <a name="symptoms"></a>Symtom

Kan innehålla ett eller flera av följande:

* Pull-eller push-avbildningar med Docker CLI tar längre tid än förväntat
* Distribution av avbildningar till en tjänst, till exempel Azure Kubernetes-tjänsten tar längre tid än förväntat
* Det går inte att slutföra ett stort antal samtidiga pull-eller push-åtgärder inom den förväntade tiden
* Pull-eller push-åtgärder i ett geo-replikerat register tar längre tid än förväntat, eller push Miss lyckas med fel `Error writing blob` eller `Error writing manifest`

## <a name="causes"></a>Orsaker

* Nätverks anslutningens hastighet kan leda till dåliga register åtgärder – [lösning](#check-expected-network-speed)
* Bild skikts komprimering eller extrahering kan vara långsamt på klient [lösningen](#check-client-hardware)  
* Du håller på att nå en konfigurerad gräns i register tjänst nivån eller miljö [lösningen](#review-configured-limits)
* Ditt geo-replikerade register har repliker i närliggande regioner – [lösning](#configure-geo-replicated-registry)
* Du drar från en geografiskt avlägsen register replik – [lösning](#configure-dns-for-geo-replicated-registry)

Om du inte löser problemet här kan du läsa mer i [Avancerad fel sökning](#advanced-troubleshooting) och [Nästa steg](#next-steps) för andra alternativ.

## <a name="potential-solutions"></a>Möjliga lösningar

### <a name="check-expected-network-speed"></a>Kontrol lera förväntad nätverks hastighet

Kontrol lera din Internet uppladdning och nedladdnings hastighet eller Använd ett verktyg som AzureSpeed för att testa [uppladdning](https://www.azurespeed.com/Azure/Uploadß) och [Ladda ned](https://www.azurespeed.com/Azure/Download) från Azure Blob Storage, som är värd för register avbildnings lager.

Kontrol lera din avbildnings storlek mot den högsta storlek som stöds och nedladdnings-eller överförings bandbredd som stöds för register tjänst nivån. Om ditt register finns på nivån Basic eller standard, bör du överväga att uppgradera för att förbättra prestanda. 

För avbildnings distribution till andra tjänster, kontrol lera regionerna där registret och målet finns. Överväg att leta upp registret och distributions målet i samma eller nätverks nära regioner för att förbättra prestandan.

Relaterade länkar:

* [Azure Container Registry tjänst nivåer](container-registry-skus.md)    
* [Vanliga frågor om container Registry](container-registry-faq.md)
* [Prestanda-och skalbarhets mål för Azure Blob Storage](../storage/blobs/scalability-targets.md)

### <a name="check-client-hardware"></a>Kontrol lera klient maskin vara

Disk typen och CPU: n på Docker-klienten kan påverka hastigheten för att extrahera eller komprimera avbildnings lager på klienten som en del av pull-eller push-åtgärder. Lager extrahering på en hård disk tar till exempel längre tid än på en solid-state-disk. Jämför pull-åtgärder för jämförbara bilder från ditt Azure Container Registry och ett offentligt register som Docker Hub.

### <a name="review-configured-limits"></a>Granska konfigurerade gränser

Om du samtidigt skickar eller tar emot flera eller många bilder i flera lager i registret, kan du granska de ReadOps och WriteOps gränser som stöds för register tjänst nivån. Om ditt register finns på nivån Basic eller standard, bör du överväga att uppgradera för att öka gränserna. Kontrol lera också med nätverks leverantören om nätverks begränsning som kan uppstå med många samtidiga åtgärder. 

Granska konfigurationen av Docker daemon för maximalt antal samtidiga överföringar eller nedladdningar för varje push-eller pull-åtgärd på klienten. Konfigurera högre gränser om det behövs.

Eftersom varje bild lager kräver en separat register Läs-eller Skriv åtgärd, kontrollerar du antalet lager i dina bilder. Överväg att använda strategier för att minska antalet bild lager.

Relaterade länkar:

* [Azure Container Registry tjänst nivåer](container-registry-skus.md)
* [dockerd](https://docs.docker.com/engine/reference/commandline/dockerd/)

### <a name="configure-geo-replicated-registry"></a>Konfigurera geo-replikerat register

En Docker-klient som skickar en avbildning till ett geo-replikerat register kanske inte push-överför alla avbildnings lager och dess manifest till en enda replikerad region. Den här situationen kan inträffa eftersom Azure Traffic Manager dirigerar register förfrågningar till det nätverks närmast replikerade registret. Om registret har två närliggande replikerade regioner kan bild lager och manifestet distribueras till de två platserna och push-åtgärden Miss lyckas när manifestet verifieras.

Om du vill optimera DNS-matchningen till den närmaste repliken när du skickar avbildningar konfigurerar du ett geo-replikerat register i samma Azure-regioner som källan till push-åtgärderna eller den närmaste regionen när du arbetar utanför Azure.

Om du vill felsöka åtgärder med ett geo-replikerat register kan du också tillfälligt inaktivera Traffic Manager routning till en eller flera replikeringar.

Relaterade länkar:

* [Geo-replikering i Azure Container Registry](container-registry-geo-replication.md)

### <a name="configure-dns-for-geo-replicated-registry"></a>Konfigurera DNS för geo-replikerat register

Om pull-åtgärder från ett geo-replikerat register verkar vara långsamma kan DNS-konfigurationen på klienten matcha till en geografiskt avlägsen DNS-server. I det här fallet kan Traffic Manager dirigera begär anden till en replik som är nätverks nära DNS-servern men som är avlägsen från klienten. Kör ett verktyg som `nslookup` eller `dig` (i Linux) för att avgöra vilken replik som Traffic Manager dirigerar register begär anden till. Exempel:

```console
nslookup myregistry.azurecr.io
```

En möjlig lösning är att konfigurera en närmare DNS-server.

Relaterade länkar:

* [Geo-replikering i Azure Container Registry](container-registry-geo-replication.md)
* [Felsöka push-åtgärder med geo-replikerade register](container-registry-geo-replication.md#troubleshoot-push-operations-with-geo-replicated-registries)
* [Inaktivera routning till replikering tillfälligt](container-registry-geo-replication.md#temporarily-disable-routing-to-replication)
* [Traffic Manager vanliga frågor](../traffic-manager/traffic-manager-faqs.md)

### <a name="advanced-troubleshooting"></a>Avancerad felsökning

[Kontrol lera hälso tillståndet för register miljön](container-registry-check-health.md)om dina behörigheter till register resurser tillåter. Om fel rapporteras kan du läsa [fel referensen](container-registry-health-error-reference.md) för möjliga lösningar.

Om [samlingen av resurs loggar](container-registry-diagnostics-audit-logs.md) är aktive rad i registret, granskar du ContainterRegistryRepositoryEvents-loggen. I den här loggen lagras information om åtgärder som push-eller pull-händelser. Fråga loggen efter [Åtgärds problem på lagrings nivå](container-registry-diagnostics-audit-logs.md#repository-level-operation-failures). 

Relaterade länkar:

* [Loggar för diagnostisk utvärdering och granskning](container-registry-diagnostics-audit-logs.md)
* [Vanliga frågor om container Registry](container-registry-faq.md)
* [Bästa metoder för Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Nästa steg

Om du inte löser problemet här kan du läsa följande alternativ.

* Andra fel söknings avsnitt för registret innehåller:
  * [Felsöka inloggning av registret](container-registry-troubleshoot-login.md)
  * [Felsöka nätverks problem med registret](container-registry-troubleshoot-access.md)
* Alternativ för [Community-support](https://azure.microsoft.com/support/community/)
* [Microsoft Q&A](/answers/products/)
* [Öppna en supportbegäran](https://azure.microsoft.com/support/create-ticket/)