---
title: Skydda ditt ursprung med privat länk i Azure front dörr standard/Premium (för hands version)
description: Den här sidan innehåller information om hur du skyddar anslutningen till ditt ursprung med hjälp av en privat länk.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: tyao
ms.custom: references_regions
ms.openlocfilehash: dead60b9d8e0872f3d46b1f223ccf5e6697cbd90
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100521"
---
# <a name="secure-your-origin-with-private-link-in-azure-front-door-standardpremium-preview"></a>Skydda ditt ursprung med privat länk i Azure front dörr standard/Premium (för hands version)

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [Azures front dörrs dokument](../front-door-overview.md).

## <a name="overview"></a>Översikt

Med [Azures privata länk](../../private-link/private-link-overview.md) kan du få åtkomst till Azure PaaS Services och Azure-värdbaserade tjänster via en privat slut punkt i det virtuella nätverket. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure front dörr Premium SKU kan ansluta till ditt ursprung med hjälp av den privata länk tjänsten. Dina program kan finnas i ditt privata virtuella nätverk eller bakom en PaaS-tjänst, som inte är tillgänglig från offentliga Internet.

:::image type="content" source="../media/concept-private-link/front-door-private-endpoint-architecture.png" alt-text="Arkitektur för privata slut punkter i front dörren":::

När du aktiverar privat länk till ditt ursprung i Azure front dörr Premium-konfigurationen skapar frontend-dörren en privat slut punkt för din räkning från Front dörrens regionala privata nätverk. Den här slut punkten hanteras av Azures front dörr. Du får ett meddelande om en privat slut punkt för Azures front dörr för godkännande i ditt ursprung. När du har godkänt begäran tilldelas en privat IP-adress från Front dörrens virtuella nätverk, trafik mellan Azure-hemdörren och ditt ursprung bläddrar den upprättade privata länken med Azures stamnät till nätverket. Inkommande trafik till ditt ursprung skyddas nu när den kommer från din Azure-front dörr.

:::image type="content" source="../media/concept-private-link/enable-private-endpoint.png" alt-text="Aktivera privat slut punkt":::

Azure front dörr Premium stöder olika typer av ursprung. Om ditt ursprung finns på en uppsättning virtuella datorer i ditt privata nätverk måste du först skapa en intern standard belastningsutjämnare, aktivera tjänsten för privat länk till standard belastnings utjämningen och sedan välja anpassad typ av ursprung. För konfiguration av privat länk väljer du "Microsoft. Network/PrivateLinkServices som resurs typ. För PaaS-tjänster, till exempel Azure-webbapp och lagrings konto, kan du aktivera Private Link service från motsvarande tjänster först och välja Microsoft. Web/Sites för Web App och Microsoft. Storage/StorageAccounts för privata tjänst typer för lagrings konton.

## <a name="limitations"></a>Begränsningar

Privata slut punkter i Azures front dörr är tillgängliga i följande regioner under den offentliga för hands versionen: östra USA, västra 2 USA och södra centrala USA.

För bästa svars tid bör du alltid välja en Azure-region som är närmast ditt ursprung när du väljer att aktivera den privata länk slut punkten för front dörren.

Azures front dörr privata slut punkter får hanteras av plattformen och under prenumerationen av Azures front dörr. Azures frontend-dörr tillåter privata länk anslutningar till samma kund prenumeration som används för att skapa profilen för front dörren.

## <a name="next-steps"></a>Nästa steg

* Information om hur du ansluter Azure frontend-dörren till Virtual Machines med hjälp av Private Link service finns i [skapa en privat slut punkt](../../private-link/create-private-endpoint-portal.md).
* Om du vill ansluta Azure frontend-dörren till din webbapp via Private Link service, se [ansluta till en webbapp med en privat slut punkt](../../private-link/tutorial-private-endpoint-webapp-portal.md).
* Om du vill ansluta Azure frontend-dörren till ditt lagrings konto via tjänsten privata länkar går [du till Anslut till ett lagrings konto med hjälp av privat slut punkt](../../private-link/tutorial-private-endpoint-storage-portal.md).
