---
title: Vanliga frågor och svar om Windows Server-noder
titleSuffix: Azure Kubernetes Service
description: Se vanliga frågor och svar när du kör Windows Server-nodkonfigurationer och program arbets belastningar i Azure Kubernetes service (AKS).
services: container-service
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: cc5a5ec2bbfb64a1e787277bf67579bad0543cd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739584"
---
# <a name="frequently-asked-questions-for-windows-server-node-pools-in-aks"></a>Vanliga frågor och svar om Windows Server Node-pooler i AKS

I Azure Kubernetes service (AKS) kan du skapa en Node-pool som kör Windows Server som gäst operativ system på noderna. Dessa noder kan köra interna Windows-programbehållare, till exempel de som skapats på .NET Framework. Det finns skillnader i hur Linux och Windows OS tillhandahåller stöd för behållare. Vissa vanliga Linux-Kubernetes och Pod-relaterade funktioner är för närvarande inte tillgängliga för Windows-adresspooler.

Den här artikeln beskriver några vanliga frågor och OS-koncept för Windows Server-noder i AKS.

## <a name="which-windows-operating-systems-are-supported"></a>Vilka Windows-operativsystem stöds?

AKS använder Windows Server 2019 som värd-OS-version och stöder bara process isolering. Behållar avbildningar som skapats med andra Windows Server-versioner stöds inte. Mer information finns i [kompatibilitet för Windows container version][windows-container-compat].

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Är Kubernetes annorlunda för Windows och Linux?

Window Server Node pool support innehåller vissa begränsningar som ingår i den överordnade Windows Server i Kubernetes-projektet. Dessa begränsningar är inte begränsade till AKS. Mer information om det här överordnade stödet för Windows Server i Kubernetes finns i avsnittet [funktioner som stöds och begränsningar][upstream-limitations] i [introduktionen till Windows-stöd i Kubernetes][intro-windows] -dokument, från Kubernetes-projektet.

Kubernetes är historiskt Linux-fokuserad. Många exempel som används i den överordnade [Kubernetes.io][kubernetes] -webbplatsen är avsedda att användas på Linux-noder. När du skapar distributioner som använder Windows Server-behållare gäller följande vid OS-nivå:

- **Identitet** – Linux identifierar en användare med en heltals användar IDENTIFIERARE (UID). En användare har också ett alfanumeriskt användar namn för inloggning, vilket innebär att Linux översätts till användarens UID. Linux identifierar på samma sätt en användar grupp med en heltals grupp identifierare (GID) och översätter ett grupp namn till motsvarande GID.
    - Windows Server använder en större säkerhets identifierare för binärfiler (SID) som lagras i Windows Security Access Manager-databasen (SAM). Den här databasen delas inte mellan värden och behållare, eller mellan behållare.
- **Fil behörigheter** – Windows Server använder en åtkomst kontrol lista baserat på sid, i stället för en bitmask med behörigheter och UID + GID
- **Fil Sök vägar** – konvention på Windows Server är att använda \ i stället för/.
    - I pod-specifikationer som monterar volymer anger du sökvägen korrekt för Windows Server-behållare. I stället för en monterings punkt för */mnt/Volume* i en Linux-behållare anger du till exempel en enhets beteckning och en plats, till exempel */K/Volume* , som ska monteras som *K:* enhet.

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Vilken typ av diskar stöds för Windows?

Azure-diskar och Azure Files är de volym typer som stöds. Dessa kan nås som NTFS-volymer i Windows Server-behållaren.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Kan jag endast köra Windows-kluster i AKS?

Huvudnoderna (kontroll planet) i ett AKS-kluster är värd för AKS-tjänsten, men du kommer inte att exponeras för operativ systemet för de noder som är värdar för huvud komponenterna. Alla AKS-kluster skapas med en standardinställd första Node-pool, som är Linux-baserad. Den här noden innehåller system tjänster som behövs för att klustret ska fungera. Vi rekommenderar att du kör minst två noder i den första noden för att säkerställa hur tillförlitlig klustret är och möjligheten att utföra kluster åtgärder. Den första Linux-baserade Node-poolen kan inte tas bort om inte själva AKS-klustret tas bort.

## <a name="how-do-i-patch-my-windows-nodes"></a>Hur gör jag för att korrigera mina Windows-noder?

För att få de senaste korrigeringarna för Windows-noder kan du antingen [Uppgradera Node-poolen][nodepool-upgrade] eller [Uppgradera noden avbildningen][upgrade-node-image]. Windows-uppdateringar är inte aktiverade på noder i AKS. AKS släpper nya avbildningar för resurspooler så snart korrigeringarna är tillgängliga och det är användarens ansvar att uppgradera nodkonfigurationer för att hålla dig uppdaterad om korrigeringar och snabb korrigeringar. Detta gäller även för den Kubernetes-version som används. [AKS viktig information][aks-release-notes] visar när nya versioner är tillgängliga. Mer information om hur du uppgraderar hela Windows Server Node-poolen finns i [uppgradera en Node-pool i AKS][nodepool-upgrade]. Om du bara är intresse rad av att uppdatera Node-avbildningen, se [uppgraderingar av AKS Node-avbildningar][upgrade-node-image].

> [!NOTE]
> Den uppdaterade Windows Server-avbildningen kommer bara att användas om en kluster uppgradering (kontroll Plans uppgradering) har utförts innan du uppgraderade Node-poolen.
>

## <a name="what-network-plug-ins-are-supported"></a>Vilka nätverks-plugin-program stöds?

AKS-kluster med Windows-noder måste använda nätverks modellen Azure CNI (avancerat). Kubernetes-nätverk (Basic) stöds inte. Mer information om skillnaderna i nätverks modeller finns i [nätverks koncept för program i AKS][azure-network-models]. Nätverks modellen i Azure CNI kräver ytterligare planering och överväganden för hantering av IP-adresser. Mer information om hur du planerar och implementerar Azure-CNI finns i [Konfigurera Azure cni Networking i AKS][configure-azure-cni].

Windows-noder i AKS-kluster har också [direkt Server retur (DSR)][dsr] aktiverat som standard när Calico är aktiverat.

## <a name="is-preserving-the-client-source-ip-supported"></a>Stöds IP-adressen för klient källan?

För närvarande stöds inte [IP-konservering för klient källa][client-source-ip] med Windows-noder.

## <a name="can-i-change-the-max--of-pods-per-node"></a>Kan jag ändra max. # för poddar per nod?

Ja. Information om vilka effekter och alternativ som är tillgängliga finns i [Max antalet poddar][maximum-number-of-pods].

## <a name="why-am-i-seeing-an-error-when-i-try-to-create-a-new-windows-agent-pool"></a>Varför ser jag ett fel när jag försöker skapa en ny Windows-agent?

Om du skapade klustret före februari 2020 och aldrig har utfört några kluster uppgraderings åtgärder, använder klustret fortfarande en gammal Windows-avbildning. Du kanske har sett ett fel som liknar:

"Det gick inte att hitta följande lista över avbildningar som refereras från distributions mal len: utgivare: Microsoft Windows Server, erbjudande: Windows Server, SKU: 2019-Data Center-Core-smalldisk-2004, version: senaste. https://docs.microsoft.com/azure/virtual-machines/windows/cli-ps-findimageMer information om hur du hittar tillgängliga bilder finns i.

Så här löser du det här felet:

1. Uppgradera [kluster kontroll planet][upgrade-cluster-cp] om du vill uppdatera avbildnings erbjudandet och utgivaren.
1. Skapa nya Windows agent-pooler.
1. Flytta Windows-poddar från befintliga Windows-Gent till nya Windows agent-pooler.
1. Ta bort gamla Windows agent-pooler.

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Hur gör jag för att rotera tjänstens huvud namn för min Windows Node-pool?

Windows Node-pooler stöder inte tjänstens huvuds rotation. Om du vill uppdatera tjänstens huvud namn skapar du en ny Windows Node-pool och migrerar poddar från den äldre poolen till den nya. När detta har slutförts tar du bort den äldre Node-poolen.

Använd i stället hanterade identiteter, som huvudsakligen benämns med tjänstens huvud namn. Mer information finns i [använda hanterade identiteter i Azure Kubernetes-tjänsten][managed-identity].

## <a name="how-many-node-pools-can-i-create"></a>Hur många noder I pooler kan jag skapa?

AKS-klustret kan ha högst 10 noder i pooler. Du kan ha högst 1000 noder i noderna. [Begränsningar för Node-pool][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Vad kan jag ge till mina Windows-nodkonfigurationer?

Du måste ha ett namn på högst 6 tecken (sex). Detta är en aktuell begränsning på AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Stöds alla funktioner med Windows-noder?

Kubernetes stöds för närvarande inte med Windows-noder.

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Kan jag köra ingångs styrenheter på Windows-noder?

Ja, en ingress-styrenhet som stöder Windows Server-behållare kan köras på Windows-noder i AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Kan jag använda Azure dev Spaces med Windows-noder?

Azure dev Spaces är för närvarande bara tillgängligt för Linux-baserade nodkonfigurationer.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Kan mina Windows Server-behållare använda gMSA?

GMSA-stöd (Group Managed Service accounts) är för närvarande inte tillgängligt i AKS.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Kan jag använda Azure Monitor för behållare med Windows-noder och behållare?

Ja du kan dock Azure Monitor finns i en offentlig för hands version för att samla in loggar (STDOUT, STDERR) och mått från Windows-behållare. Du kan också ansluta till den aktiva strömmen av STDOUT-loggar från en Windows-behållare.

## <a name="are-there-any-limitations-on-the-number-of-services-on-a-cluster-with-windows-nodes"></a>Finns det några begränsningar för antalet tjänster i ett kluster med Windows-noder?

Ett kluster med Windows-noder kan ha ungefär 500 tjänster innan det påträffar port överbelastning.

## <a name="can-i-use-azure-hybrid-benefit-with-windows-nodes"></a>Kan jag använda Azure Hybrid-förmån med Windows-noder?

Ja. Azure Hybrid-förmån för Windows Server minskar drifts kostnaderna genom att låta dig ta din lokala Windows Server-licens till AKS Windows-noder.

Azure Hybrid-förmån kan användas på hela AKS-klustret eller på enskilda noder. För enskilda noder måste du navigera till [resurs gruppen för noden][resource-groups] och tillämpa Azure Hybrid-förmån på noderna direkt. Mer information om hur du tillämpar Azure Hybrid-förmån på enskilda noder finns i [Azure Hybrid-förmån för Windows Server][hybrid-vms]. 

Använd argumentet för att använda Azure Hybrid-förmån på ett nytt AKS-kluster `--enable-ahub` .

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku Standard \
    --windows-admin-password 'Password1234$' \
    --windows-admin-username azure \
    --network-plugin azure
    --enable-ahub
```

Om du vill använda Azure Hybrid-förmån på ett befintligt AKS-kluster uppdaterar du klustret med hjälp av `--enable-ahub` argumentet.

```azurecli
az aks update \
    --resource-group myResourceGroup
    --name myAKSCluster
    --enable-ahub
```

Om du vill kontrol lera om Azure Hybrid-förmån har angetts för klustret använder du följande kommando:

```azurecli
az vmss show --name myAKSCluster --resource-group MC_CLUSTERNAME
```

Om klustret har Azure Hybrid-förmån aktiverat, ser utdata ut `az vmss show` ungefär så här:

```console
"platformFaultDomainCount": 1,
  "provisioningState": "Succeeded",
  "proximityPlacementGroup": null,
  "resourceGroup": "MC_CLUSTERNAME"
```

## <a name="can-i-use-the-kubernetes-web-dashboard-with-windows-containers"></a>Kan jag använda Kubernetes-webbinstrumentpanelen med Windows-behållare?

Ja, du kan använda [Kubernetes-webbinstrumentpanelen][kubernetes-dashboard] för att få åtkomst till information om Windows-behållare, men för tillfället kan du inte köra *kubectl exec* i en Windows-behållare direkt från Kubernetes-webbinstrumentpanelen. Mer information om hur du ansluter till en Windows-behållare finns i [ansluta med RDP till Azure Kubernetes service (AKS) Cluster Windows Server-noder för underhåll eller fel sökning][windows-rdp].

## <a name="what-if-i-need-a-feature-thats-not-supported"></a>Vad händer om jag behöver en funktion som inte stöds?

Vi arbetar hårt för att ta med alla funktioner som du behöver i Windows i AKS, men om du stöter på luckor ger det ett [AKS-motor][aks-engine] projekt med öppen källkod ett enkelt och helt anpassningsbart sätt att köra Kubernetes i Azure, inklusive Windows-support. Se till att ta en titt på vår översikt över funktionerna som kommer [AKS-översikt][aks-roadmap].

## <a name="next-steps"></a>Nästa steg

Kom igång med Windows Server-behållare i AKS genom att [skapa en noduppsättning som kör Windows Server i AKS][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1
[aks-release-notes]: https://github.com/Azure/AKS/releases

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[upgrade-cluster]: upgrade-cluster.md
[upgrade-cluster-cp]: use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md#what-does-azure-monitor-for-containers-provide
[client-source-ip]: concepts-network.md#ingress-controllers
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-rdp]: rdp.md
[upgrade-node-image]: node-image-upgrade.md
[managed-identity]: use-managed-identity.md
[hybrid-vms]: ../virtual-machines/windows/hybrid-use-benefit-licensing.md
[resource-groups]: faq.md#why-are-two-resource-groups-created-with-aks
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip