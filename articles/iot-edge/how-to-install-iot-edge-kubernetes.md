---
title: Så här installerar du IoT Edge på Kubernetes-| Microsoft Docs
description: Lär dig hur du installerar IoT Edge Kubernetes med hjälp av en lokal miljö för utvecklingskluster
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: iotedge-2018-06
ms.openlocfilehash: 1c7c221a2fea60f3bbbc4f2cde960dcb8638efe2
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576575"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Så här installerar IoT Edge på Kubernetes (förhandsversion)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge kan integreras med Kubernetes som ett motståndskraftigt infrastrukturlager med hög kapacitet. Här passar det här stödet in i en IoT Edge lösning:

![k8s-introduktion](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>En bra mental modell för den här integreringen är att tänka på Kubernetes som en annan driftsmiljö IoT Edge kan köras på förutom Linux och Windows.

## <a name="architecture"></a>Arkitektur 
På Kubernetes tillhandahåller IoT Edge *anpassad resursdefinition* (CRD) för distribution av gränsarbetsbelastningar. IoT Edge agenten rollen som  *CRD-kontrollant* som stämer av moln-hanterat önskat tillstånd med det lokala klustertillståndet.

Modulens livslängd hanteras av Kubernetes-schemaläggaren, som upprätthåller modulens tillgänglighet och väljer deras placering. IoT Edge hanterar edge-programplattformen som körs ovanpå och avstämning av det önskade tillstånd som anges i IoT Hub med tillståndet i gränsklustret. Programmodellen är fortfarande den välbekanta modellen som baseras IoT Edge moduler och vägar. Agentkontrollanten IoT Edge utför automatisk  översättning IoT Edge programmodellen till Kubernetes interna konstruktioner som poddar, distributioner, tjänster osv.

Här är ett översiktsdiagram över arkitekturen:

![kubernetes-valv](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Varje komponent i edge-distributionen är begränsad till ett Kubernetes-namnområde som är specifikt för enheten, vilket gör det möjligt att dela samma klusterresurser mellan flera gränsenheter och deras distributioner.

>[!NOTE]
>IoT Edge på Kubernetes finns i [offentlig förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="tutorials-and-references"></a>Självstudier och referenser 

Mer information [finns IoT Edge på kubernetes-förhandsgranskningsdokumenten,](https://aka.ms/edgek8sdoc) inklusive djupgående självstudier och referenser.
