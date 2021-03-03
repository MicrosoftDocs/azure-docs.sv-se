---
title: Azure Arc Enabled Kubernetes-validerings program
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Beskriver Arc-validerings program för Kubernetes-distributioner
keywords: Kubernetes, båge, Azure, K8s, verifiering
ms.openlocfilehash: 0d7756f407a3a176c8913d99bdb45a5026300569
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101689223"
---
# <a name="azure-arc-validation-program"></a>Azure Arc-validerings program

Azure Arc-aktiverade Kubernetes fungerar med alla CNCF-certifierade (Cloud Native Computing Foundation) Kubernetes-kluster. Azure båg-teamet har också arbetat med Kubernetes-leverantörer för nyckel branschen för att verifiera Azure Arc-aktiverade Kubernetes med sina Kubernetes-distributioner. Framtida och lägre versioner av Kubernetes-distributioner som släpps av de här leverantörerna kommer att verifieras för kompatibilitet med Azure Arc-aktiverade Kubernetes.

## <a name="validated-distributions"></a>Verifierade distributioner

Följande Microsoft tillhandahöll Kubernetes-distributioner och infrastruktur leverantörer har klarat omslags test för Azure Arc-aktiverade Kubernetes:

| Provider för distribution och infrastruktur | Version |
| ---------------------------------------- | ------- |
| Kluster-API-Provider på Azure            | Version: [0.4.12](https://github.com/kubernetes-sigs/cluster-api-provider-azure/releases/tag/v0.4.12); Kubernetes-version: [1.18.2](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.2) |
| AKS på Azure Stack HCI                   | Version: [uppdatering December 2020](https://github.com/Azure/aks-hci/releases/tag/AKS-HCI-2012); Kubernetes-version: [1.18.8](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.8) |

Följande providers och deras motsvarande Kubernetes-distributioner har klarat omslags test för Azure Arc-aktiverade Kubernetes:

| Providernamn | Distributions namn | Version |
| ------------ | ----------------- | ------- |
| Redhat       | [OpenShift Container Platform](https://www.openshift.com/products/container-platform) | [4,5](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html), [4,6](https://docs.openshift.com/container-platform/4.6/release_notes/ocp-4-6-release-notes.html), [4,7](https://docs.openshift.com/container-platform/4.7/release_notes/ocp-4-7-release-notes.html) |
| VMware       | [Tanzu Kubernetes-rutnät](https://tanzu.vmware.com/kubernetes-grid) | Kubernetes-version: v 1.17.5 |
| Canonical    | [Snabb knapp Kubernetes](https://ubuntu.com/kubernetes) | [1,19](https://ubuntu.com/kubernetes/docs/1.19/components) |
| SUSE rancher      | [Rancher Kubernetes-motor](https://rancher.com/products/rke/) | RKE CLI-version: [v 1.2.4](https://github.com/rancher/rke/releases/tag/v1.2.4); Kubernetes-versioner: [1.19.6](https://github.com/kubernetes/kubernetes/releases/tag/v1.19.6)), [1.18.14](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.14)), [1.17.16](https://github.com/kubernetes/kubernetes/releases/tag/v1.17.16))  |
| Nutanix      | [Karbon](https://www.nutanix.com/products/karbon)    | Version 2.2.1 |

Azure båg-teamet körde också omslags test och godkända Azure Arc-aktiverade Kubernetes-scenarier för följande offentliga moln leverantörer:

| Namn på offentlig moln leverantör | Distributions namn | Version |
| -------------------------- | ----------------- | ------- |
| Amazon Web Services        | Elastisk Kubernetes-tjänst (EKS) | v-1.18.9  |
| Google Cloud Platform      | Google Kubernetes Engine (GKE) | v-1.17.15 |

## <a name="scenarios-validated"></a>Scenarier har validerats

Omställnings testerna körs som en del av den Azure Arc-aktiverade Kubernetes-verifieringen behandlar följande scenarier:

1. Anslut Kubernetes-kluster till Azure-bågen: 
    * Distribuera Azure Arc Enabled Kubernetes agent Helm-diagram på kluster.
    * Konfigurera hanterat system identitets certifikat (MSI) i kluster.
    * Agenter skickar kluster-metadata till Azure.

2. Konfiguration: 
    * Skapa konfiguration ovanpå Azure Arc-aktiverade Kubernetes-resurser.
    * [Flöde, som](https://docs.fluxcd.io/)krävs för att konfigurera GitOps-arbetsflöde, distribueras i klustret.
    * Flöden hämtar manifest och Helm diagram från demo git lagrings platsen och distribuerar till kluster.

## <a name="next-steps"></a>Nästa steg

* [Ansluta ett kluster till Azure-bågen](./connect-cluster.md)
* [Skapa konfigurationer på ditt Arc-aktiverade Kubernetes-kluster](./use-gitops-connected-cluster.md)
* [Använd Azure Policy för att tillämpa konfigurationer i skala](./use-azure-policy.md)
