---
title: Attestationsstöd med Intel SGX-offerthjälparen DaemonSet på Azure (förhandsversion)
description: En DaemonSet för att generera offerten utanför Intel SGX-programprocessen. I den här artikeln förklaras hur den process utanför processens atterstationsanläggning tillhandahålls för konfidentiella arbetsbelastningar som körs i en container.
ms.service: container-service
ms.subservice: confidential-computing
author: agowdamsft
ms.topic: overview
ms.date: 2/12/2021
ms.author: amgowda
ms.openlocfilehash: 849fd7afa3f9365f31ee8e03d9f9cc2174d64304
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484412"
---
# <a name="platform-software-management-with-intel-sgx-quote-helper-daemonset-preview"></a>Plattformsprogramvarahantering med Intel SGX-offerthjälparen DaemonSet (förhandsversion)

[Enklavprogram som](confidential-computing-enclaves.md) utför fjärrsattesering kräver ett genererat citattecken. Den här offerten ger kryptografiskt bevis på identiteten och programmets tillstånd, samt miljön som enklaven körs i. Offertgenereringen kräver betrodda programvarukomponenter som ingår i Intel Platform Software Components (PSW).

## <a name="overview"></a>Översikt
 
Intel stöder två attestationslägen för att köra offertgenereringen:

- *Den här processen* är värd för de betrodda programvarukomponenterna i enklavprogramprocessen.

- *Out-of-process* är värd för betrodda programvarukomponenter utanför enklavprogrammet.
 
Intel Software Guard-tillägget (Intel SGX) som skapats med hjälp av Open Enclave SDK använder som standard det processbaserade attstationsläget. Intel SGX-baserade program tillåter attstationsläge utanför processen. Om du vill använda det här läget behöver du extra värdtjänster och du måste exponera nödvändiga komponenter, till exempel AESM (Architectural Enclave Service Manager), utanför programmet.

Den här funktionen förbättrar drifttiden för enklavappar under Intel-plattformsuppdateringar eller DCAP-drivrutinsuppdateringar. Därför rekommenderar vi att du använder den.

Om du vill aktivera den här funktionen i ett AKS-kluster (Azure Kubernetes Services) lägger du till kommandot i Azure CLI när du aktiverar tillägget för `--enable-sgxquotehelper` konfidentiell databehandling. 

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled and SGX Quote Helper
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom --enable-sgxquotehelper
```

Mer information finns i [Snabbstart: Distribuera ett AKS-kluster med konfidentiella beräkningsnoder med hjälp av Azure CLI.](confidential-nodes-aks-get-started.md)

## <a name="benefits-of-the-out-of-process-mode"></a>Fördelar med out-of-process-läge

I följande lista beskrivs några av de viktigaste fördelarna med det här atterstationsläget:

-   Inga uppdateringar krävs för offertgenereringskomponenter i PSW för varje containerprogram. Containerägare behöver inte hantera uppdateringar i sin container. Containerägare förlitar sig i stället på providergränssnittet som anropar den centraliserade tjänsten utanför containern. Providern uppdaterar och hanterar containern.

-   Du behöver inte bekymra dig om atterstationsfel på grund av inkontredna PSW-komponenter. Providern hanterar uppdateringarna av dessa komponenter.

-   Out-of-process-läget ger bättre användning av EPC-minne än i processläge. I processläge måste varje enklavprogram instansiera kopian av QE och PCE för fjärr attestation. I out-of-process-läge finns det inget behov av att containern är värd för dessa enklaver, och därför förbrukar den inte enklavminnet från containerkvoten.

-   När du har uppströms Intel SGX-drivrutinen till en Linux-kernel måste en enklav ha högre behörighet. Med den här behörigheten kan enklaven anropa PCE, vilket bryter enklavprogrammet som körs i processläge. Som standard får enklaver inte den här behörigheten. Att bevilja den här behörigheten till ett enklavprogram kräver ändringar i programinstallationsprocessen. I läget out-of-process ser däremot den tjänstleverantör som hanterar out-of-process-begäranden till att tjänsten installeras med den här behörigheten.

-   Du behöver inte kontrollera bakåtkompatibiliteten med PSW och DCAP. Uppdateringarna av offertgenereringskomponenterna i PSW verifieras för bakåtkompatibilitet av providern innan uppdateringen. Detta hjälper dig att hantera kompatibilitetsproblem innan du distribuerar uppdateringar för konfidentiella arbetsbelastningar.

## <a name="confidential-workloads"></a>Konfidentiella arbetsbelastningar

Offertförfrågan och offertgenereringen körs separat, men på samma fysiska dator. Offertgenereringen är centraliserad och betjänar begäranden om offerter från alla entiteter. För att en entitet ska kunna begära offerter måste gränssnittet vara korrekt definierat och kunna upptäckas.

![Diagram som visar relationerna mellan offertens beställare, offertgenerering och gränssnitt.](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

Den här abstrakta modellen gäller för scenariot med konfidentiell arbetsbelastning genom att dra nytta av den AESM-tjänst som redan är tillgänglig. AESM containeriseras och distribueras som en DaemonSet i Kubernetes-klustret. Kubernetes garanterar att en enda instans av en AESM-tjänstcontainer, omsluten i en podd, distribueras på varje agentnod. Den nya Intel SGX-offerten DaemonSet kommer att ha ett beroende på Sgx-device-plugin DaemonSet, eftersom AESM-tjänstens container begär EPC-minne från sgx-device-plugin för att starta QE- och PCE-enklaver.

Varje container måste välja att använda offertgenerering utanför processen genom att ange miljövariabeln `SGX_AESM_ADDR=1` när den skapas. Containern bör också innehålla paketet libsgx-quote-ex, som ansvarar för att dirigera begäran till Unix-standarddomänsocketen.

Ett program kan fortfarande använda den processbaserade attestation som tidigare, men in-process-and-of-process kan inte användas samtidigt i ett program. Out-of-process-infrastrukturen är tillgänglig som standard och förbrukar resurser.

## <a name="sample-implementation"></a>Exempelimplementering

Följande Docker-fil är ett exempel för ett program som baseras på Open Enclave. Ange `SGX_AESM_ADDR=1` miljövariabeln i Docker-filen eller genom att ange den på distributionsfilen. Följande exempel innehåller information om Docker-filen och distributionen. 

  > [!Note] 
  > För att out-of-process-attestation ska fungera korrekt måste libsgx-quote-ex från Intel paketeras i programcontainern.
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for details
FROM ubuntu:18.04 as sgx_base
RUN apt-get update && apt-get install -y \
    wget \
    gnupg

# Add the repository to sources, and add the key to the list of
# trusted keys used by the apt to authenticate packages
RUN echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main" | tee /etc/apt/sources.list.d/intel-sgx.list \
    && wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add -
# Add Microsoft repo for az-dcap-client
RUN echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | tee /etc/apt/sources.list.d/msprod.list \
    && wget -qO - https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

FROM sgx_base as sgx_sample
RUN apt-get update && apt-get install -y \
    clang-7 \
    libssl-dev \
    gdb \
    libprotobuf10 \
    libsgx-dcap-ql \
    libsgx-quote-ex \
    az-dcap-client \
    open-enclave
WORKDIR /opt/openenclave/share/openenclave/samples/remote_attestation
RUN . /opt/openenclave/share/openenclave/openenclaverc \
    && make build
# This sets the flag for out-of-process attestation mode. Alternatively you can set this flag on the deployment files.
ENV SGX_AESM_ADDR=1 

CMD make run
```
Du kan också ställa in läget för atterering utanför processen i .yaml-distributionsfilen. Gör så här:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
spec:
  template:
    spec:
      containers:
      - name: sgxtest
        image: <registry>/<repository>:<version>
        env:
        - name: SGX_AESM_ADDR
          value: 1
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10
        volumeMounts:
        - name: var-run-aesmd
          mountPath: /var/run/aesmd
      restartPolicy: "Never"
      volumes:
      - name: var-run-aesmd
        hostPath:
          path: /var/run/aesmd
```

## <a name="next-steps"></a>Nästa steg

[Snabbstart: Distribuera ett AKS-kluster med noder för konfidentiell databehandling med hjälp av Azure CLI](./confidential-nodes-aks-get-started.md)

[Snabbstartsexempel för konfidentiella containrar](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU:er](../virtual-machines/dcv2-series.md)
