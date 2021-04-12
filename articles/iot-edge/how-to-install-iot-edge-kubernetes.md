---
title: Så här installerar du IoT Edge på Kubernetes | Microsoft Docs
description: Lär dig hur du installerar IoT Edge på Kubernetes med en lokal miljö för utvecklings kluster
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d5fc7b6b480056b56a2776cebd0fa87a5b96f9f0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201676"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Så här installerar du IoT Edge på Kubernetes (för hands version)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge kan integreras med Kubernetes med hjälp av den som ett elastiskt infrastruktur lager med hög tillgänglighet. Här är det här stödet passar i en hög nivå IoT Edge lösning:

![K8s – Intro](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>En lämplig psykiska modell för den här integrationen är att tänka på Kubernetes som en annan operativ miljö IoT Edge program kan köras tillsammans med Linux och Windows.

## <a name="architecture"></a>Arkitektur 
På Kubernetes tillhandahåller IoT Edge *anpassad resurs definition* (CRD) för distributioner av Edge-arbetsbelastningar. IoT Edge agent förutsätter rollen för en  *CRD-kontrollant* som stämmer överens med det lokala klustrets tillstånd.

Livs längden för modulen hanteras av Kubernetes Scheduler, som underhåller modulens tillgänglighet och väljer deras placering. IoT Edge hanterar Edge-programplattformen som körs överst och som kontinuerligt avstämr det önskade läget som anges i IoT Hub med tillstånd i gräns klustret. Program modellen är fortfarande den välkända modellen som baseras på IoT Edge moduler och vägar. Den IoT Edge agent styrenheten utför *Automatisk* översättning IoT Edge programmets program modell till Kubernetes inbyggda konstruktioner som poddar, distributioner, tjänster osv.

Här är ett diagram med hög nivå arkitektur:

![Kubernetes båge](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Varje komponent i Edge-distributionen är begränsad till ett Kubernetes-namnområde som är unikt för enheten, vilket gör det möjligt att dela samma kluster resurser mellan flera gräns enheter och deras distributioner.

>[!NOTE]
>IoT Edge på Kubernetes finns i [offentlig för hands version](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="tutorials-and-references"></a>Självstudier och referenser 

Mer information, inklusive djupgående kurser och referenser finns i [IoT Edge för för hands versionen av Kubernetes](https://aka.ms/edgek8sdoc) .
