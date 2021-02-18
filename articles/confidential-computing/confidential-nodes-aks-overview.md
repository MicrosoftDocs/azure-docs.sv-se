---
title: Data behandlings noder i Azure Kubernetes service (AKS)
description: Data behandlings noder på AKS
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 2/08/2021
ms.author: amgowda
ms.openlocfilehash: 9ca98c032a7c8bd1820a92bff77079a61c515d65
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653388"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service"></a>Konfidentiella data behandlings noder i Azure Kubernetes-tjänsten

Med [Azures konfidentiella data behandling](overview.md) kan du skydda känsliga data medan de används. Den underliggande infrastrukturen för konfidentiell data behandling skyddar dessa data från andra program, administratörer och moln leverantörer med en maskin vara med program varu skydd för betrodda körnings miljöer. Genom att lägga till data för konfidentiell beräkning kan du köra mål behållar programmet i en isolerad, maskin vara som är skyddad och bestyrkande miljö.

## <a name="overview"></a>Översikt

Azure Kubernetes service (AKS) har stöd för att lägga till [DCsv2 konfidentiella data behandlings noder](confidential-computing-enclaves.md) som drivs av Intel SGX. Med de här noderna kan du köra känsliga arbets belastningar i en maskinvarubaserad betrodd körnings miljö (TEE). TEE kan använda kod på användar nivå från behållare för att allokera privata regioner av minne för att köra koden med CPU direkt. De här privata minnes regionerna som körs direkt med CPU kallas enclaves. Enclaves hjälper till att skydda data sekretessen, data integriteten och kod integriteten från andra processer som körs på samma noder. Intel SGX-körnings modellen tar också bort de mellanliggande lagren av gäst operativ system, värd operativ system och hypervisorer, vilket minskar risken för attack ytan. *Maskin vara baserad på isolerad körnings modell per behållare* i en nod gör det möjligt för program att köras direkt med processorn, samtidigt som det särskilda blocket minne som krypteras per behållare hålls kvar. Konfidentiella data behandlings-noder med konfidentiella behållare är ett utmärkt tillägg till din kostnads effektiva säkerhets planering och belysande behållar strategi.

![Översikt över SGX-nod](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>AKS funktioner för konfidentiella noder

- Maskin vara och behållar isolering på processnivå via Intel SGX Trusted Execution Environment (TEE) 
- Heterogena för noder i poolen (blandade konfidentiella och icke-konfidentiella noder i pooler)
- EPC-baserad (Encrypted Page cache) Pod schemaläggning (kräver tillägg)
- Intel SGX DCAP-drivrutin förinstallerad
- CPU-förbrukning baserad horisontell Pod autoskalning och automatisk skalning i kluster
- Linux-behållare stöder genom Ubuntu 18,04 gen 2 VM Worker-noder

## <a name="confidential-computing-add-on-for-aks"></a>Konfidentiellt data behandlings tillägg för AKS
Med tilläggs funktionen kan du använda extra funktioner på AKS när du kör pooler för konfidentiella data behandling i klustret. Det här tillägget aktiverar funktionerna nedan.

#### <a name="azure-device-plugin-for-intel-sgx"></a>Azure-enhets-plugin för Intel SGX <a id="sgx-plugin"></a>

Enhets-plugin-programmet implementerar plugin-gränssnittet för Kubernetes-enheten för EPC-minne (Encrypted Page cache) och visar enhets driv rutinerna från noderna. Det här plugin-programmet gör effektivt EPC-minne som en annan resurs typ i Kubernetes. Användare kan ange gränser för den här resursen precis som andra resurser. Förutom schemaläggnings funktionen hjälper enhets-plugin-programmet att tilldela driv rutins behörigheter för Intel SGX-enheter till konfidentiella arbets belastnings behållare. Med den här plugin-utvecklaren kan du undvika att montera driv rutins volymerna för Intel SGX i distributions filerna. En exempel implementering av EPC Memory-baserad distribution ( `kubernetes.azure.com/sgx_epc_mem_in_MiB` )-exemplet finns [här](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml)


## <a name="programming-models"></a>Programmeringsmodeller

### <a name="confidential-containers"></a>Konfidentiella behållare

Med [konfidentiella behållare](confidential-containers.md) kan du köra befintliga ej ändrade behållar program för de flesta **vanliga programmeringsspråk för programmeringsspråk** (python, Node, Java osv.) konfidentiellt. Den här förpacknings modellen behöver inte göra ändringar eller kompilera om käll koden. Detta är den snabbaste metoden för konfidentialitet som kan uppnås genom att paketera dina standard Docker-behållare med Open-Source-projekt eller Azure-partner lösningar. I den här förpacknings-och körnings modellen läses alla delar av behållar programmet in i den betrodda gränser (enklaven). Den här modellen fungerar bra för de program för hylletiketter som finns på marknaden eller anpassade appar som körs på generella noder.

### <a name="enclave-aware-containers"></a>Enklaven-medvetna behållare
Datakonfidentialitets data behandling på AKS stöder även behållare som är programmerade att köras i en enklaven för att använda **särskilda instruktioner** som är tillgängliga från processorn. Den här programmerings modellen ger bättre kontroll över ditt körnings flöde och kräver användning av särskilda SDK: er och ramverk. Den här programmerings modellen ger mest kontroll över program flödet med en lägsta, betrodd beräknings bas (TCB). Enklaven medveten behållar utveckling omfattar ej betrodda och betrodda delar till behållar programmet, så att du kan hantera det vanliga minnet och EPC-minnet (Encrypted Page cache) där enklaven körs. [Läs mer](enclave-aware-containers.md) om enklaven-medvetna behållare.

## <a name="next-steps"></a>Nästa steg

[Distribuera AKS-kluster med konfidentiella databeräknings noder](./confidential-nodes-aks-get-started.md)

[Snabb starts exempel för konfidentiella behållare](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU-lista](../virtual-machines/dcv2-series.md)

[Skydds djupgående med konfidentiella behållare webb seminarium-session](https://www.youtube.com/watch?reload=9&v=FYZxtHI_Or0&feature=youtu.be)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
