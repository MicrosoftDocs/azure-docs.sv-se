---
title: Skapa datakontrollant
description: Skapa en Azure Arc-datakontrollant på ett typiskt Kubernetes-kluster med flera noder som du redan har distribuerat.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: cd41f740d3cdb8130199a5dda6e73209637d5142
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2021
ms.locfileid: "98745942"
---
# <a name="create-the-azure-arc-data-controller"></a>Skapa data styrenheten för Azure-bågen

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-creating-the-azure-arc-data-controller"></a>Översikt över att skapa data styrenheten för Azure-bågen

Azure Arc-aktiverade data tjänster kan skapas på flera olika typer av Kubernetes-kluster och hanterade Kubernetes-tjänster med flera olika metoder.

För närvarande är den lista över Kubernetes tjänster och distributioner som stöds följande:

- Azure Kubernetes Service (AKS)
- Azure Kubernetes service Engine (AKS motor) på Azure Stack
- Azure Kubernetes service på Azure Stack HCI
- Azure RedHat OpenShift (ARO)
- OpenShift container Platform (OCP)
- AWS Elastic Kubernetes Service (EKS)
- Google Cloud Kubernetes Engine (GKE)
- Öppen källkod, överordnad Kubernetes vanligtvis distribuerad med kubeadm

> [!IMPORTANT]
> * Den lägsta versionen av Kubernetes som stöds är v-1.17. Se [kända problem](./release-notes.md#known-issues) för ytterligare information. 
> * Den lägsta versionen av OCP som stöds är 4,3.
> * Se [anslutnings kraven](connectivity.md) för att förstå vilken anslutning som krävs mellan din miljö och Azure.
> * Mer information om hur du konfigurerar beständiga lagrings enheter finns i [rikt linjer för lagrings konfiguration](storage-configuration.md) .
> * Om du använder Azure Kubernetes-tjänsten bör klustrets virtuella dator storlek vara minst **Standard_D8s_v3** och använda **Premium diskar.** Klustret ska inte omfatta flera tillgänglighets zoner. 
> * Om du använder en annan Kubernetes-distribution eller tjänst bör du se till att du har en minsta Node-storlek på 8 GB RAM-minne och 4 kärnor och en total total kapacitet på 32 GB RAM-minne tillgängligt för alla dina Kubernetes-noder. Du kan till exempel ha 1 nod med 32 GB RAM-minne och 4 kärnor, eller så kan du ha 2 noder med 16 GB RAM-minne och 4 kärnor vardera.

> [!NOTE]
> Om du använder Red Hat OpenShift container Platform på Azure rekommenderar vi att du använder den senaste tillgängliga versionen.

Beroende på vilket alternativ du väljer _krävs_ vissa verktyg, men vi rekommenderar att du [installerar alla klient verktyg](./install-client-tools.md) innan du börjar skapa data styrenheten för Azure-bågen.

Oavsett vilket alternativ du väljer måste du ange följande information under skapande processen:

- **Namn på datakontrollant** – ett beskrivande namn för data styrenheten – t. ex. "produktions data styrenhet", "Seattle-datakontrollant".
- **Datacontroller-användar namn** – valfritt användar namn för administratörs användaren för data kontrollanten.
- **Lösen ord för datakontrollant** – ett lösen ord för administratörs användaren för datakontrollanten.
- **Namnet på ditt Kubernetes-namnområde** – namnet på Kubernetes-namnområdet som du vill skapa data styrenheten i.
- **Anslutnings** läge – anslutnings läget fastställer anslutnings graden från din Azure Arc-aktiverad data tjänst miljö till Azure. För hands versionen stöder för närvarande endast indirekt anslutna och direktanslutna lägen.  Mer information finns i [anslutnings läge](./connectivity.md). 
- **Azure-prenumerations-ID** – GUID för Azure-prenumerationen där du vill att data styrenhets resursen i Azure ska skapas.
- **Namn på Azure-resurs grupp** – namnet på den resurs grupp där du vill att data styrenhets resursen i Azure ska skapas.
- **Azure-plats** – Azure-platsen där data styrenhetens resurs-metadata ska lagras i Azure. En lista över tillgängliga regioner finns i [Azure Global Infrastructure/products by region](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

## <a name="next-steps"></a>Nästa steg

Det finns flera alternativ för att skapa data styrenheten för Azure-bågen:

> **Vill du bara testa saker?**  
> Kom igång snabbt med [Azure Arc rivstart med](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) på Azure Kubernetes service (AKS), AWS elastisk Kubernetes service (EKS), Google Cloud Kubernetes Engine (GKE) eller i en virtuell Azure-dator!
> 
- [Skapa en datakontrollant med [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](create-data-controller-using-azdata.md)
- [Skapa en datakontrollant med Azure Data Studio](create-data-controller-azure-data-studio.md)
- [Skapa en datakontrollant från Azure Portal via en Jupyter Notebook i Azure Data Studio](create-data-controller-resource-in-azure-portal.md)
- [Skapa en datakontrollant med Kubernetes-verktyg som kubectl eller oc](create-data-controller-using-kubernetes-native-tools.md)
- [Skapa en datakontrollant med Azure Arc-rivstart med för en snabbare upplevelse av en test distribution](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)
