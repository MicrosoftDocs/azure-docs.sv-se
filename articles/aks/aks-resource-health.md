---
title: Sök efter Resource Health händelser som påverkar ditt AKS-kluster (för hands version)
description: Kontrol lera hälso tillståndet för ditt AKS-kluster med hjälp av Azure Resource Health.
services: container-service
author: yunjchoi
ms.topic: troubleshooting
ms.date: 08/18/2020
ms.author: yunjchoi
ms.openlocfilehash: a409ac944c51d893fc344f82ae83d97559e055ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92070664"
---
# <a name="check-for-resource-health-events-impacting-your-aks-cluster-preview"></a>Sök efter Resource Health händelser som påverkar ditt AKS-kluster (för hands version)


När du kör behållar arbets belastningar på AKS vill du se till att du kan felsöka och åtgärda problem så snart de uppstår för att minimera påverkan på tillgängligheten för dina arbets belastningar. [Azure Resource Health](../service-health/resource-health-overview.md) ger dig insyn i olika hälso händelser som kan orsaka att ditt AKS-kluster inte är tillgängligt.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="open-resource-health"></a>Öppna Resource Health

### <a name="to-access-resource-health-for-your-aks-cluster"></a>För att komma åt Resource Health för ditt AKS-kluster:

- Navigera till ditt AKS-kluster i [Azure Portal](https://portal.azure.com).
- Välj **Resource Health** i det vänstra navigerings fältet.

### <a name="to-access-resource-health-for-all-clusters-on-your-subscription"></a>För att få åtkomst till Resource Health för alla kluster i din prenumeration:

- Sök efter **service Health** i [Azure Portal](https://portal.azure.com) och navigera till den.
- Välj **resurs hälsa** i det vänstra navigerings fältet.
- Välj din prenumeration och ange resurs typen till Azure Kubernetes service (AKS).

![Skärm bild som visar resurs hälsan för dina ett K S-kluster.](./media/aks-resource-health/resource-health-check.png)

## <a name="check-the-health-status"></a>Kontrol lera hälso statusen

Azure Resource Health hjälper dig att diagnostisera och få support för tjänst problem som påverkar dina Azure-resurser. Resource Health rapporter om resursernas aktuella och tidigare hälsa och hjälper dig att avgöra om problemet orsakas av en åtgärd som initierats av användaren eller en plattforms händelse.

Resource Health tar emot signaler för ditt hanterade kluster för att fastställa hälso tillståndet för klustret. Den undersöker hälso tillståndet för ditt AKS-kluster och rapporterar åtgärder som krävs för varje hälso signal. Dessa signaler sträcker sig från lösningar för automatisk lösning, planerade uppdateringar, oplanerade hälso händelser och avtillgänglighet som orsakas av åtgärder som initierats av användaren. Dessa signaler klassificeras med hjälp av Azure Resource Healthens hälso status: tillgänglig *,* *otillgänglig*, *okänd* och *försämrad*.

- **Tillgängligt**: när det inte finns några kända problem som påverkar klustrets hälsa Resource Health rapporterar klustret som *tillgängligt*.

- **Otillgänglig**: när det finns en plattforms-eller icke-plattforms händelse som påverkar klustrets hälsa Resource Health rapporterar klustret som *otillgängligt*.

- **Okänd**: om det finns en tillfällig anslutnings förlust till klustrets hälso mått, Resource Health rapporterar ditt kluster som *Okänt*.

- **Försämrad**: när det finns ett hälso problem som kräver åtgärden Resource Health rapporterar klustret som *degraderat*.

Mer information om vad varje hälso status visar finns i [Resource Health översikt](../service-health/resource-health-overview.md#health-status).

### <a name="view-historical-data"></a>Visa historiska data

Du kan också visa de senaste 30 dagarna historik Resource Health information i avsnittet **hälso historik** .

## <a name="next-steps"></a>Nästa steg

Kör kontroller på klustret för att ytterligare felsöka kluster problem genom att använda [AKS-diagnostik](./concepts-diagnostics.md).