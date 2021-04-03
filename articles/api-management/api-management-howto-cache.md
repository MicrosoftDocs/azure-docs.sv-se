---
title: Förbättra prestanda i Azure API Management med cachelagring | Microsoft Docs
description: Lär dig hur du förbättrar svarstider, bandbreddsanvändning och webbtjänstbelastning i API Management-tjänstanrop.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: apimpm
ms.openlocfilehash: 732abed830afdb759ed52fd933673edd8e5cade6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94638741"
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Förbättra prestanda i Azure API Management med cachelagring

API: er och åtgärder i API Management kan konfigureras med cachelagring av svar. Cachelagring av svar kan avsevärt minska svars tiderna för API-anrop och Server dels belastning för API-providers.

> [!IMPORTANT]
> Inbyggd cache är temporär och delas av alla enheter i samma region i samma API Management tjänst.

Mer detaljerad information om cachelagring finns i [Principer för cachelagring för API Management](api-management-caching-policies.md) och [Anpassad cachelagring i Azure API Management](api-management-sample-cache-by-key.md)

![principer för cachelagring](media/api-management-howto-cache/cache-policies.png)

Detta får du får lära dig:

> [!div class="checklist"]
> * Lägg till cachelagring med svar för ditt API
> * Verifiera cachelagring i praktiken

## <a name="availability"></a>Tillgänglighet

> [!NOTE]
> Internt cacheminne är inte tillgängligt på nivån **Förbrukning** för Azure API Management. Du kan [använda en extern Azure Cache for Redis](api-management-howto-cache-external.md) istället.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

+ [Skapa en Azure API Management-instans](get-started-create-service-instance.md)
+ [Importera och publicera ett API](import-and-publish.md)

## <a name="add-the-caching-policies"></a><a name="caching-policies"> </a>Lägg till cachelagringsprinciperna

Med cachelagringsprinciperna i det här exemplet returnerar den första begäran till **GetSpeakers** ett svar från serverdeltjänsten. Svaret cachelagras och registreras av de angivna sidhuvudena och frågesträngsparametrarna. För efterföljande anrop till åtgärden, med matchande parametrar, returneras det cachelagrade svaret till cachlagringsintervallets slut.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Bläddra till APIM-instansen.
3. Välj fliken **API**.
4. Klicka på **Demo Conference API** i API-listan.
5. Välj **GetSpeakers**.
6. Överst på skärmen väljer du fliken **Design**.
7. I avsnittet **Inkommande bearbetning** klickar du på ikonen **</>**.

    ![kodredigeraren](media/api-management-howto-cache/code-editor.png)

8. I **inkommande** element lägger du till följande princip:

   ```
   <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
       <vary-by-header>Accept</vary-by-header>
       <vary-by-header>Accept-Charset</vary-by-header>
       <vary-by-header>Authorization</vary-by-header>
   </cache-lookup>
   ```

9. I **utgående** element lägger du till följande princip:

   ```
   <cache-store duration="20" />
   ```

    **Varaktighet** anger giltighetsintervallet för de cachelagrade svaren. I det här exemplet är intervallet **20** sekunder.

> [!TIP]
> Om du använder en extern cache enligt beskrivningen i [Använda en extern Azure Cache for Redis i Azure API Management](api-management-howto-cache-external.md) kan det vara bra att ange attributet `caching-type` för cachelagringsprinciperna. Mer information finns i [Principer för API Management-cachelagring](api-management-caching-policies.md).

## <a name="call-an-operation-and-test-the-caching"></a><a name="test-operation"> </a>Anropa en åtgärd och testa cachelagringen
Om du vill se hur cachelagringen fungerar i praktiken kan du anropa åtgärden från utvecklarportalen.

1. Bläddra till APIM-instansen i Azure Portal.
2. Välj fliken **API: er** .
3. Välj det API där du har lagt till cachelagringsprinciper.
4. Välj åtgärden **GetSpeakers**.
5. Klicka på fliken **Test** på menyn längst upp till höger.
6. Tryck på **Skicka**.

## <a name="next-steps"></a><a name="next-steps"> </a>Nästa steg
* Mer information om cachelagringsprinciper finns i [Cachelagringsprinciper][Caching policies] i [Principreferens för API Management][API Management policy reference].
* Mer information om hur du cachelagrar objekt med nycklar med hjälp av principuttryck finns i [Anpassad cachelagring i Azure API Management](api-management-sample-cache-by-key.md).
* Mer information om hur du använder extern Azure Cache for Redis finns i [Använda en extern Azure Cache for Redis i Azure API Management](api-management-howto-cache-external.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md

[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-caching-policies.md

[Create an API Management service instance]: get-started-create-service-instance.md

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
