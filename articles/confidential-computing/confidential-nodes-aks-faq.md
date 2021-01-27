---
title: Vanliga frågor och svar om stöd för konfidentiella noder i Azure Kubernetes service (AKS)
description: Hitta svar på några vanliga frågor om Azure Kubernetes service (AKS) & stöd för Azure konfidentiell data behandling (ACC).
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: amgowda
ms.openlocfilehash: 51e2095b0df8fdc965926c6c612c45f582c9b9d2
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874492"
---
# <a name="frequently-asked-questions-about-confidential-computing-nodes-on-azure-kubernetes-service-aks"></a>Vanliga frågor och svar om konfidentiella databeräkningar i Azure Kubernetes service (AKS)

Den här artikeln behandlar vanliga frågor om Intel SGX-baserade data behandlings data i Azure Kubernetes service (AKS). Om du har fler frågor, e-post acconaks@microsoft.com .

## <a name="what-service-level-agreement-sla-and-azure-support-is-provided-during-the-preview"></a>Vad Serviceavtal (SLA) och Azure-support tillhandahålls under för hands versionen? 

Service avtal tillhandahålls inte under för hands versionen av produkten enligt definitionen [här](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Men produkt support tillhandahålls via supporten för Azure.

## <a name="what-is-attestation-and-how-can-we-do-attestation-of-apps-running-in-enclaves"></a>Vad är attestering och hur kan vi göra attestering av appar som körs i enclaves? 

Attestering är en process för att demonstrera och kontrol lera att en program varu enhet är korrekt instansierad på den specifika maskin varu plattformen. Det garanterar också att dess bevis är verifierbara för att tillhandahålla garantier att den körs på en säker plattform och inte har manipulerats. [Läs mer](attestation.md) om hur attestering sker för enklaven-appar.

## <a name="can-i-enable-accelerated-networking-with-azure-confidential-computing-aks-clusters"></a>Kan jag aktivera accelererat nätverk med Azures AKS-kluster med konfidentiella data behandling? 

Nej. Accelererat nätverk stöds inte på datakonfidentialitets datorer på AKS. Se till att det accelererade nätverket är inaktiverat i distributionen. 

## <a name="can-i-bring-my-existing-containerized-applications-and-run-it-on-aks-with-azure-confidential-computing"></a>Kan jag använda mina befintliga program i behållare och köra det på AKS med Azures konfidentiella data behandling? 

Ja, granska [sidan konfidentiella behållare](confidential-containers.md) för mer information om plattforms aktiveringar.

## <a name="what-intel-sgx-driver-version-is-installed-in-the-aks-image"></a>Vilken version av driv rutinen för Intel SGX installeras i AKS-avbildningen? 

För närvarande installeras virtuella Azure-datorer med DCSv2 virtuella datorer med Intel SGX-DCAP 1,33. 

## <a name="can-i-open-an-azure-support-ticket-if-i-run-into-issues"></a>Kan jag öppna ett support ärende för Azure om jag stöter på problem? 

Ja. Support för Azure tillhandahålls under för hands versionen. Det finns inget service avtal anslutet eftersom produkten är i förhands gransknings fasen.

## <a name="can-i-inject-post-install-scriptscustomize-drivers-to-the-nodes-provisioned-by-aks"></a>Kan jag mata in post installations skript/anpassa driv rutiner till noder som tillhandahålls av AKS? 

Nej. [AKS-baserade konfidentiella databeräknings noder](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) stöder konfidentiella databeräknings noder som tillåter anpassade installationer.

## <a name="should-i-be-using-a-docker-base-image-to-get-started-on-enclave-applications"></a>Ska jag använda en Docker-bas avbildning för att komma igång med enklaven-program? 

Olika aktiveringar (ISV-och OSS-projekt) tillhandahåller sätt att aktivera konfidentiella behållare. Granska [sidan konfidentiella behållare](confidential-containers.md) för mer information och enskilda referenser till implementeringar.

## <a name="can-i-run-acc-nodes-with-other-standard-aks-skus-build-a-heterogenous-node-pool-cluster"></a>Kan jag köra ACC-noder med andra standard AKS SKU: er (skapa ett heterogena-kluster för resurspool)? 

Ja, du kan köra olika noder i samma AKS-kluster inklusive ACC-noder. Om du vill rikta in dig på dina enklaven-program på en speciell nod kan du överväga att lägga till noder eller använda EPC-gränser. Mer information finns i snabb start på konfidentiella noder [här](confidential-nodes-aks-get-started.md).

## <a name="can-i-run-windows-nodes-and-windows-containers-with-acc"></a>Kan jag köra Windows-noder och Windows-behållare med ACC? 

Inte just nu. Kontakta oss om du har Windows-noder eller container krav. 

## <a name="what-if-my-container-size-is-more-than-available-epc-memory"></a>Vad händer om min behållar storlek är mer än tillgängligt EPC-minne? 

EPC-minnet gäller för den del av programmet som är programmerade att köras i enklaven. Den totala storleken på din behållare är inte rätt sätt att jämföra den med maximalt tillgängligt EPC-minne. I själva verket tillåts DCSv2-datorer med SGX att tillåta maximalt VM-minne på 32 GB där din ej betrodda del av programmet skulle använda. Men om din behållare använder mer än tillgängligt EPC-minne, kan prestandan för den del av programmet som körs i enklaven påverkas.

För att bättre hantera EPC-minnet i arbetsnoderna bör du ta hänsyn till den EPC Memory-baserade begränsnings hanteringen via Kubernetes. Följ exemplet nedan som referens

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
```

## <a name="what-happens-if-my-enclave-consumes-more-than-maximum-available-epc-memory"></a>Vad händer om min enklaven använder mer än maximalt maximalt tillgängligt EPC-minne? 

Totalt tillgängligt EPC-minne delas mellan enklaven-programmen i samma virtuella datorer eller arbetsnoder. Om programmet använder EPC-minne mer än tillgängligt kan programmets prestanda påverkas. Därför rekommenderar vi att du ställer in tolerera per program i din distributions yaml-fil för att bättre hantera tillgängliga EPC-minnen per arbetsnoder som visas i exemplen ovan. Alternativt kan du alltid välja att flytta upp de virtuella datorernas storlekar i arbets noden eller lägga till fler noder. 

## <a name="why-cant-i-do-forks--and-exec-to-run-multiple-processes-in-my-enclave-application"></a>Varför kan jag inte göra förgreningar () och exec för att köra flera processer i mitt enklaven-program? 

För närvarande stöder Azure konfidentiella DCsv2 SKU-VM: ar ett enda adress utrymme för det program som körs i en enklaven. Enkel process är en aktuell begränsning som är utformad runt hög säkerhet. Konfidentiella behållar aktiveringar kan dock ha alternativa implementeringar för att lösa den här begränsningen.

## <a name="do-you-automatically-install-any-additional-daemonsets-to-expose-the-sgx-drivers"></a>Installerar du automatiskt ytterligare daemonsets för att exponera SGX-drivrutinerna? 

Ja. Namnet på daemonset är SGX-Device-plug och SGX-quote-Helper. Läs mer i respektive syfte [här](confidential-nodes-aks-overview.md).  

## <a name="what-is-the-vm-sku-i-should-be-choosing-for-confidential-computing-nodes"></a>Vad ska jag välja VM-SKU: n I för konfidentiella data behandlings noder? 

DCSv2 SKU: er. [DCSv2 SKU: er](../virtual-machines/dcv2-series.md) är tillgängliga i de [regioner som stöds](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all).

## <a name="can-i-still-schedule-and-run-non-enclave-containers-on-confidential-computing-nodes"></a>Kan jag fortfarande schemalägga och köra icke-enklaven behållare på datakonfidentiella data behandlings noder? 

Ja. De virtuella datorerna har också ett vanligt minne som kan köra standard arbets belastningar för behållare. Överväg säkerhets-och hot modellen för dina program innan du bestämmer dig för distributions modellerna.

## <a name="can-i-provision-aks-with-dcsv2-node-pools-through-azure-portal"></a>Kan jag etablera AKS med DCSv2 Node-pooler via Azure Portal? 

Ja. Azure CLI kan också användas som ett alternativ som dokumenteras [här](confidential-nodes-aks-get-started.md).

## <a name="what-ubuntu-version-and-vm-generation-is-supported"></a>Vilken Ubuntu-version och generering av virtuella datorer stöds? 

18,04 på gen 2. 

## <a name="can-we-change-the-current-intel-sgx-dcap-diver-version-on-aks"></a>Kan vi ändra den aktuella Intel SGX DCAP Diver-versionen på AKS? 

Nej. Om du vill utföra anpassade installationer rekommenderar vi att du väljer distributioner av [AKS-motorns konfidentiella data behandling](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) . 

## <a name="what-version-of-kubernetes-do-you-support-and-recommend"></a>Vilken version av Kubernetes har du stöd för och rekommenderar? 

Vi stöder och rekommenderar Kubernetes version 1,16 och senare 

## <a name="what-are-the-known-current-limitation-or-technical-limitations-of-the-product-in-preview"></a>Vad är den kända aktuella begränsningen eller tekniska begränsningar för produkten i för hands versionen? 

- Stöder endast Ubuntu 18,04 gen 2 VM-noder 
- Inga Windows-noder stöder eller stöd för Windows-behållare
- Det finns inte stöd för autoskalning i EPC Memory-baserade vågräta pod. PROCESSOR-och normal minnes skalning stöds.
- Dev Spaces på AKS för konfidentiella appar stöds för närvarande inte

## <a name="next-steps"></a>Nästa steg
Gå till [sidan konfidentiella behållare](confidential-containers.md) om du vill ha mer information kring konfidentiella behållare.