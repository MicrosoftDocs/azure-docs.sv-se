---
title: Konfigurera nätverks åtkomst kontroll
titleSuffix: Azure SignalR Service
description: Konfigurera nätverks åtkomst kontroll för Azure SignalR-tjänsten.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 24e56736e0d033420f9aaf976b0fb7d9727c1a5b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92143229"
---
# <a name="configure-network-access-control"></a>Konfigurera nätverks åtkomst kontroll

Med Azure SignalR service kan du skydda och kontrol lera åtkomst nivån till tjänstens slut punkt baserat på typ av begäran och en delmängd av nätverken som används. När nätverks regler har kon figurer ATS kan endast program som begär data i den angivna uppsättningen nätverk komma åt Azure SignalR-tjänsten.

Azure SignalR service har en offentlig slut punkt som är tillgänglig via Internet. Du kan också skapa [privata slut punkter för Azure SignalR-tjänsten](howto-private-endpoints.md). Privat slut punkt tilldelar en privat IP-adress från ditt VNet till Azure SignalR-tjänsten och skyddar all trafik mellan ditt VNet och Azure SignalR-tjänsten via en privat länk. Nätverks åtkomst kontrollen i Azure SignalR service ger åtkomst kontroll för både offentlig och privat slut punkt.

Alternativt kan du välja att tillåta eller neka vissa typer av begär Anden för offentlig slut punkt och varje privat slut punkt. Du kan till exempel blockera alla [Server anslutningar](signalr-concept-internals.md#server-connections) från den offentliga slut punkten och se till att de endast härstammar från ett särskilt VNet.

Ett program som ansluter till en Azure SignalR-tjänst när regler för nätverks åtkomst kontroll fortfarande kräver rätt behörighet för begäran.

## <a name="scenario-a---no-public-traffic"></a>Scenario A – ingen offentlig trafik

Om du vill neka all offentlig trafik fullständigt bör du först konfigurera den offentliga nätverks regeln för att tillåta ingen typ av begäran. Sedan bör du konfigurera regler som beviljar åtkomst till trafik från vissa virtuella nätverk. Med den här konfigurationen kan du bygga en säker nätverks gränser för dina program.

## <a name="scenario-b---only-client-connections-from-public-network"></a>Endast scenario B – klient anslutningar från offentligt nätverk

I det här scenariot kan du konfigurera den offentliga nätverks regeln så att den bara tillåter [klient anslutningar](signalr-concept-internals.md#client-connections) från offentliga nätverk. Du kan sedan konfigurera privata nätverks regler så att de tillåter andra typer av begär Anden som kommer från ett angivet VNet. Den här konfigurationen döljer dina App-servrar från det offentliga nätverket och upprättar säkra anslutningar mellan dina App-servrar och Azure SignalR-tjänsten.

## <a name="managing-network-access-control"></a>Hantera nätverks åtkomst kontroll

Du kan hantera nätverks åtkomst kontroll för Azure SignalR service via Azure Portal.

### <a name="azure-portal"></a>Azure Portal

1. Gå till den Azure SignalR-tjänst som du vill skydda.

1. Klicka på menyn Inställningar som kallas **nätverks åtkomst kontroll**.

    ![Nätverks-ACL på Portal](media/howto-network-access-control/portal.png)

1. Om du vill redigera standard åtgärden växlar du till knappen **Tillåt/neka** .

    > [!TIP]
    > Standard åtgärd är den åtgärd vi vidtar när det inte finns någon matchande ACL-regel. Om standard åtgärden t. ex. är **neka**, kommer begär ande typer som inte uttryckligen godkänns att nekas.

1. Om du vill redigera den offentliga nätverks regeln väljer du tillåtna typer av begär Anden under **offentligt nätverk**.

    ![Redigera ACL för offentligt nätverk på portalen ](media/howto-network-access-control/portal-public-network.png)

1. Om du vill redigera nätverks regler för privata slut punkter väljer du tillåtna typer av begär anden i varje rad under **privata slut punkts anslutningar**.

    ![Redigera ACL för privat slut punkt på Portal ](media/howto-network-access-control/portal-private-endpoint.png)

1. Klicka på **Spara** för att tillämpa dina ändringar.

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azures privata länk](../private-link/private-link-overview.md).