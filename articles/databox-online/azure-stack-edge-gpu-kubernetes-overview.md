---
title: Översikt över Kubernetes-kluster på Microsoft Azure Stack Edge Pro-enhet | Microsoft Docs
description: Beskriver hur Kubernetes implementeras på din Azure Stack Edge Pro-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 6e9ae9b8d9d1154d55991d078af0faef65ff654b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699580"
---
# <a name="kubernetes-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes på din Azure Stack Edge Pro GPU-enhet

Kubernetes är en populär plattform med öppen källkod för att dirigera program i behållare. Den här artikeln innehåller en översikt över Kubernetes och en beskrivning av hur Kubernetes fungerar på din Azure Stack Edge Pro-enhet. 

## <a name="about-kubernetes"></a>Om Kubernetes 

Kubernetes tillhandahåller en enkel och tillförlitlig plattform för att hantera behållarebaserade program och deras associerade nätverks-och lagrings komponenter. Du kan snabbt skapa, leverera och skala appar i behållare med Kubernetes.

Som en öppen plattform kan du använda Kubernetes för att skapa program med önskat programmeringsspråk, OS-bibliotek eller meddelande buss. Om du vill schemalägga och distribuera versioner kan Kubernetes integrera med befintliga kontinuerliga integrerings-och kontinuerliga leverans verktyg.

Mer information finns i [så här fungerar Kubernetes](https://www.youtube.com/watch?v=q1PcAawa4Bg&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=2&t=0s).

## <a name="kubernetes-on-azure-stack-edge-pro"></a>Kubernetes på Azure Stack Edge Pro

På din Azure Stack Edge Pro-enhet kan du skapa ett Kubernetes-kluster genom att konfigurera beräkningen. När Compute-rollen har kon figurer ATS distribueras och konfigureras Kubernetes-klustret, inklusive huvud-och arbetsnoderna. Klustret används sedan för arbets belastnings distribution via `kubectl` , IoT Edge eller Azure-båge.

Azure Stack Edge Pro-enheten är tillgänglig som en konfiguration med 1 nod som utgör infrastruktur klustret. Kubernetes-klustret är skilt från infrastruktur klustret och distribueras ovanpå infrastruktur klustret. Infrastruktur klustret ger den beständiga lagringen för din Azure Stack Edge Pro-enhet medan Kubernetes-klustret endast är ansvarigt för programdirigering. 

Kubernetes-klustret i det här fallet har en huvud-nod och en arbetsnoden. Kubernetes-noderna i ett kluster är virtuella datorer som kör dina program och moln arbets flöden. 

Huvudnoden Kubernetes ansvarar för att underhålla det önskade läget för klustret. Huvudnoden styr också arbetsnoden som i sin tur kör program i behållare. 

Följande diagram illustrerar implementeringen av Kubernetes på en Azure Stack Edge Pro-enhet med 1 nod. 1-nodens enhet är inte hög tillgänglig och om den enskilda noden kraschar, stängs enheten av. Kubernetes-klustret försvinner också.

![Kubernetes-arkitektur för en 1-nods Azure Stack Edge Pro-enhet](media/azure-stack-edge-gpu-kubernetes-overview/kubernetes-architecture-1-node.png)

Mer information om Kubernetes-kluster arkitekturen finns i [Kubernetes Core-koncept](https://kubernetes.io/docs/concepts/architecture/).


<!--The Kubernetes cluster control plane components make global decisions about the cluster. The control plane has:

- *kubeapiserver* that is the front end of the Kubernetes API and exposes the API.
- *etcd* that is a highly available key value store that backs up all the Kubernetes cluster data.
- *kube-scheduler* that makes scheduling decisions.
- *kube-controller-manager* that runs controller processes such as those for node controllers, replications controllers, endpoint controllers, and service account and token controllers. -->

## <a name="storage-volume-provisioning"></a>Lagrings volym etablering

För att stödja arbets belastningar för program kan du montera lagrings volymer för beständiga data på dina Azure Stack Edge Pro-enhets resurser. Du kan använda både statiska och dynamiska volymer. 

Mer information finns i lagrings etablerings alternativ för program i [Kubernetes-lagring för din Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-kubernetes-storage.md).

## <a name="networking"></a>Nätverk

Med Kubernetes Networking kan du konfigurera kommunikation i ditt Kubernetes-nätverk, inklusive behållare-till-container-nätverk, Pod-till-Pod-nätverk, Pod-till-tjänst-nätverk och Internet-till-tjänst-nätverk. Mer information finns i nätverks modellen i Kubernetes- [nätverk för din Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-kubernetes-networking.md).

## <a name="updates"></a>Uppdateringar

När nya Kubernetes-versioner blir tillgängliga kan klustret uppgraderas med de standard uppdateringar som är tillgängliga för din Azure Stack Edge Pro-enhet. Anvisningar om hur du uppgraderar finns i [tillämpa uppdateringar för Azure Stack Edge Pro](azure-stack-edge-gpu-install-update.md).

## <a name="access-monitoring"></a>Åtkomst, övervakning

Kubernetes-klustret på din Azure Stack Edge Pro-enhet tillåter Kubernetes rollbaserad åtkomst kontroll (Kubernetes RBAC). Mer information finns i [Kubernetes-rollbaserad åtkomst kontroll på din Azure Stack Edge Pro GPU-enhet](azure-stack-edge-gpu-kubernetes-rbac.md).

Du kan också övervaka hälso tillståndet för klustret och resurserna via Kubernetes-instrumentpanelen. Behållar loggar är också tillgängliga. Mer information finns i [använda Kubernetes-instrumentpanelen för att övervaka Kubernetes-klustrets hälso tillstånd på din Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).

Azure Monitor är också tillgängligt som ett tillägg för att samla in hälso data från behållare, noder och styrenheter. Mer information finns i [Azure Monitor översikt](../azure-monitor/overview.md)

<!--## Private container registry

Kubernetes on Azure Stack Edge Pro device allows for the private storage of your images by providing a local container registry.-->

## <a name="application-management"></a>Programhantering

När ett Kubernetes-kluster har skapats på din Azure Stack Edge Pro-enhet kan du hantera de program som distribueras i det här klustret via någon av följande metoder:

- Inbyggd åtkomst via `kubectl`
- IoT Edge 
- Azure Arc

Dessa metoder beskrivs i följande avsnitt.


### <a name="kubernetes-and-kubectl"></a>Kubernetes och kubectl

När Kubernetes-klustret har distribuerats kan du hantera de program som distribueras i klustret lokalt från en klient dator. Du använder ett inbyggt verktyg som *kubectl* via kommando raden för att interagera med programmen. 

Mer information om hur du distribuerar Kubernetes-kluster finns i [distribuera ett Kubernetes-kluster på din Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-create-kubernetes-cluster.md). Mer information om hantering finns i [använda kubectl för att hantera Kubernetes-kluster på din Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-create-kubernetes-cluster.md).


### <a name="kubernetes-and-iot-edge"></a>Kubernetes och IoT Edge

Kubernetes kan också integreras med IoT Edge arbets belastningar på Azure Stack Edge Pro-enhet där Kubernetes tillhandahåller skalning och eko systemet och IoT tillhandahåller IoT-koncentriskt eko system. Kubernetes-skiktet används som ett infrastruktur lager för att distribuera Azure IoT Edge-arbetsbelastningar. Modulens livs längd och utjämning av nätverks belastning hanteras av Kubernetes medan gräns programmets plattform hanteras av IoT Edge.

Om du vill ha mer information om hur du distribuerar program i ditt Kubernetes-kluster via IoT Edge går du till: 

- [Exponera tillstånds lösa program på Azure Stack Edge Pro-enhet via IoT Edge](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).


### <a name="kubernetes-and-azure-arc"></a>Kubernetes och Azure-båge

Azure Arc är ett hybrid hanterings verktyg som gör att du kan distribuera program i Kubernetes-kluster. Med Azure ARC kan du också använda Azure Monitor för behållare för att visa och övervaka dina kluster. Mer information finns på [Azure-Arc aktiverade Kubernetes?](../azure-arc/kubernetes/overview.md). Information om priser för Azure Arc finns i [priser för Azure Arc](https://azure.microsoft.com/services/azure-arc/#pricing).

Från och med mars 2021 blir Azure Arc-aktiverade Kubernetes allmänt tillgängliga för användarna och standard avgifterna för användning gäller. Som en värdefull förhands gransknings kund blir Azure Arc-aktiverade Kubernetes tillgängligt för dig utan kostnad för Azure Stack gräns enhet (er). Skapa en [supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)för att utnyttja för hands versions erbjudandet:

1. Välj **Fakturering** under **Typ av problem**.
2. Välj din prenumeration under **Prenumeration**.
3. Under **tjänst** väljer du **Mina tjänster** och väljer sedan **Azure Stack Edge**.
4. Under **resurs** väljer du din resurs.
5. Under **Sammanfattning** anger du en beskrivning av problemet.
6. Välj **oväntade avgifter** under **problem typ**.
7. Under **typ av problem** väljer du **Hjälp mig att förstå debiteringar i min kostnads fria utvärderings version**.


## <a name="next-steps"></a>Nästa steg

- Läs mer om Kubernetes-lagring på [Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-kubernetes-storage.md).
- Förstå Kubernetes Network Model på [Azure Stack Edge Pro Device](azure-stack-edge-gpu-kubernetes-networking.md).
- Distribuera [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) i Azure Portal.
