---
title: Stöd för out-of-proc-attestering med Intel SGX offerter-hjälp Daemonset på Azure (för hands version)
description: DaemonSet för att skapa offerten utanför SGX-programprocessen. I den här artikeln förklaras hur funktionen out-of-proc attestering tillhandahålls för konfidentiella arbets belastningar som körs i en behållare.
ms.service: container-service
author: agowdamsft
ms.topic: overview
ms.date: 2/12/2021
ms.author: amgowda
ms.openlocfilehash: 89890740b06a399bad4678ff6ddd9be09c1cda0e
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653337"
---
# <a name="platform-software-management-with-sgx-quote-helper-daemon-set-preview"></a>Plattforms oberoende program varu hantering med SGX-offerter Helper daemon set (för hands version)

[Enklaven-program](confidential-computing-enclaves.md) som utför fjärrattestering kräver en genererad offert. Den här OFFERTen innehåller kryptografiskt bevis på identiteten och programmets tillstånd, samt vilken miljö som enklaven körs på. För att skapa OFFERTen krävs betrodda program komponenter som ingår i Intels plattforms program varu komponenter (PSW).

## <a name="overview"></a>Översikt
 
Intel stöder två attesterings lägen för att köra offert genereringen:
- **i-proc**: är värd för de betrodda program varu komponenterna i enklaven program process

- **utanför processen**: är värd för de betrodda program varu komponenterna utanför enklaven-programmet.
 
SGX-program som skapats med Open enklaven SDK används som standard i proc-hälsoläge. SGX-baserade program tillåter process brist och kräver extra värd och visar de nödvändiga komponenterna, till exempel arkitektoniska enklaven Service Manager (AESM), utanför programmet.

Användning av den här funktionen **rekommenderas**, eftersom den förbättrar drift tiden för dina enklaven-appar under Intel Platform updates eller DCAP driv rutins uppdateringar.

Om du vill aktivera den här funktionen i AKS-kluster ändrar du kommandot Add--Enable-sgxquotehelper till CLI när du aktiverar det konfidentiella beräknings tillägget. Detaljerade CLI-instruktioner finns [här](confidential-nodes-aks-get-started.md): 

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled and SGX Quote Helper
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom --enable-sgxquotehelper
```

## <a name="why-and-what-are-the-benefits-of-out-of-proc"></a>Varför och vilka är fördelarna med out-of-proc?

-   Det krävs inga uppdateringar för att skapa offert komponenter i PSW för varje container program: med out-of-proc behöver container ägare inte hantera uppdateringar i deras behållare. Container ägare förlitar sig i stället på providern som tillhandahöll den centraliserade tjänsten utanför behållaren, som kommer att uppdateras och hanteras av providern.

-   Du behöver inte bekymra dig om attesterings problem på grund av inaktuella PSW-komponenter: offert genereringen omfattar betrodda SW-komponenter – citat-enklaven (QE) & Provisioning Certificate enklaven (PCE), som är en del av den betrodda beräknings basen (TCB). Dessa SW-komponenter måste vara uppdaterade för att upprätthålla kraven för attestering. Eftersom providern hanterar uppdateringar av dessa komponenter behöver kunderna aldrig hantera attesterings problem på grund av inaktuella betrodda SW-komponenter i deras behållare.

-   Bättre användning av EPC-minne i läget i proc-attestering, varje enklaven-program måste instansiera kopian av QE och PCE för fjärrattestering. Med out-of-proc behöver inte behållaren vara värd för dessa enclaves och förbrukar inte enklaven-minne från container kvoten.

-   Skydd mot kernel-tvång när SGX-drivrutinen är inströmmad i en Linux-kernel, kan det finnas en enklaven för att ha högre behörighet. Med den här behörigheten kan enklaven anropa PCE, vilket innebär att enklaven-programmet som körs i läget i proc-läge avbryts. Enclaves får som standard inte den här behörigheten. Att bevilja den här behörigheten till ett enklaven-program kräver ändringar i programmets installations process. Detta hanteras enkelt för out-of-proc-modellen som providern för den tjänst som hanterar out-of-proc-begäranden som ser till att tjänsten installeras med den här behörigheten.

-   Du behöver inte söka efter bakåtkompatibilitet med PSW & DCAP. Uppdateringarna av de olika komponenterna i PSW för offerter verifieras för bakåtkompatibilitet av providern innan du uppdaterar. Detta hjälper dig att hantera kompatibilitetsproblem och åtgärda dem innan du distribuerar uppdateringar för konfidentiella arbets belastningar.

## <a name="how-does-the-out-of-proc-attestation-mode-work-for-confidential-workloads-scenario"></a>Hur fungerar inaktuella attesterings läge för konfidentiella arbets belastnings scenarier?

Den övergripande designen följer den modell där offert begär Anden och generering av offerter körs separat, men på samma fysiska dator. Genereringen av offerter görs på ett centraliserat sätt och hanterar begär Anden om OFFERter från alla entiteter. Gränssnittet måste vara korrekt definierat och kunna identifieras för alla entiteter för att begära offerter.

![hjälp program för SGX-offerter aesm](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

Den abstrakta modellen ovan gäller för ett konfidentiellt arbets belastnings scenario genom att dra nytta av redan tillgänglig AESM-tjänst. AESM är containerd och distribuerad som en daemonSet över Kubernetes-klustret. Kubernetes garanterar en enda instans av en AESM-tjänst behållare, omsluten i en pod, som ska distribueras på varje agent-nod. Den nya SGX-offertens daemonset har ett beroende på daemonset för SGX-Device-plugin, eftersom AESM service container skulle begära EPC-minne från SGX-Device-plugin för att starta QE och PCE enclaves.

För varje behållare måste du välja att använda offerter för att skapa offerter genom att ställa in miljövariabeln **SGX_AESM_ADDR = 1** när den skapas. Behållaren bör även innehålla paketet libsgx-offert – ex som ansvarar för att dirigera begäran till standard-UNIX-domänsuffix

Ett program kan fortfarande använda in-proc-attestering som tidigare, men både i-proc och out-of-proc kan inte användas samtidigt i ett program. Out-of-proc-infrastrukturen är tillgänglig som standard och använder resurser.

## <a name="sample-implementation"></a>Exempel på implementering

Nedanstående Docker-fil är ett exempel på ett öppet enklaven-baserat program. Ange miljövariabeln SGX_AESM_ADDR = 1 i Docker-filen eller genom att ange den i distributions filen. Följ exemplet nedan för Docker-filen och yaml information om distribution. 

  > [!Note] 
  > **Libsgx-offert-från-** Intel måste paketeras i program behållaren för att det ska gå att utföra en out-of-proc-attestering för att fungera korrekt.
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for detail
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
# this sets the flag for out of proc attestation mode. alternatively you can set this flag on the deployment files
ENV SGX_AESM_ADDR=1 

CMD make run
```
Du kan också ställa in läget för inaktiv attestering i yaml-filen för distribution enligt nedan

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
[Etablera konfidentiella noder (DCsv2-serien) på AKS](./confidential-nodes-aks-get-started.md)

[Snabb starts exempel konfidentiella behållare](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU-lista](../virtual-machines/dcv2-series.md)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
