---
title: Använda en extern cache i Azure API Management | Microsoft Docs
description: Lär dig hur du konfigurerar och använder en extern cache i Azure API Management. Med hjälp av en extern cache kan du undvika vissa begränsningar i det inbyggda cacheminnet.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 6288a10e111e42629abf5e09b84a6a7791dcfe95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018230"
---
# <a name="use-an-external-redis-compatible-cache-in-azure-api-management"></a>Använd en extern Redis-kompatibel cache i Azure API Management

Förutom att använda det inbyggda cacheminnet möjliggör Azure-API Management cachelagring av svar i en extern Redis-kompatibel cache, t. ex. Azure cache för Redis.

Med hjälp av en extern cache kan du lösa några begränsningar i det inbyggda cacheminnet:

* Undvika att ditt cacheminne rensas med jämna mellanrum API Management-uppdateringar
* Få mer kontroll över din cache-konfiguration
* Cachelagra större mängder data än vad din API Management-nivå tillåter
* Använda cachelagring med förbrukningsnivån för API Management
* Aktivera cachelagring i [API Management-gatewayer med egen värd](self-hosted-gateway-overview.md)

Mer detaljerad information om cachelagring finns i [Principer för cachelagring för API Management](api-management-caching-policies.md) och [Anpassad cachelagring i Azure API Management](api-management-sample-cache-by-key.md)

![Ta din egen cache till APIM](media/api-management-howto-cache-external/overview.png)

Detta får du får lära dig:

> [!div class="checklist"]
> * Lägga till en extern cache i API Management

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

+ [Skapa en Azure API Management-instans](get-started-create-service-instance.md)
+ Förstå [cachelagring i Azure API Management](api-management-howto-cache.md)

## <a name="create-azure-cache-for-redis"></a><a name="create-cache"> </a> Skapa Azure Cache for Redis

Det här avsnittet beskriver hur du skapar en Azure Cache for Redis i Azure. Om du redan har en Azure Cache for Redis i eller utanför Azure kan du <a href="#add-external-cache">hoppa över</a> detta till nästa avsnitt.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="deploy-redis-cache-to-kubernetes"></a><a name="create-cache"></a> Distribuera Redis cache till Kubernetes

För cachelagring är lokala gatewayer exklusivt beroende av externa cacheminnen. För att cachelagring ska vara effektiva gateways och det cacheminne som de förlitar sig på måste ligga nära varandra för att minimera sökningen och lagra fördröjning. Att distribuera en Redis-cache till samma Kubernetes-kluster eller i ett separat kluster i närheten är de bästa alternativen. Följ den här [länken](https://github.com/kubernetes/examples/tree/master/guestbook) om du vill lära dig hur du distribuerar Redis cache till ett Kubernetes-kluster.

## <a name="add-an-external-cache"></a><a name="add-external-cache"> </a>Lägga till en extern cache

Följ stegen nedan om du vill lägga till en extern Azure Cache for Redis i Azure API Management.

![Skärm bild som visar hur du lägger till en extern Azure-cache för Redis i Azure API Management.](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> Inställningen **Använd från** anger en Azure-region eller en lokal gateway-plats som ska använda det konfigurerade cacheminnet. Cacheminnena som har kon figurer ATS som **standard** kommer att åsidosättas av cacheminnen med en bestämd, matchande region eller plats värde.
>
> Om till exempel API Management hanteras i regionerna USA, östra, Asien, sydöstra och Europa, västra och det finns två cacher konfigurerade, en för **Standard** och en för **Asien, sydöstra**, använder API Management i **Asien, sydöstra** sin egen cache, medan de andra två regionerna använder cacheposten **Standard**.

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Lägga till en Azure Cache for Redis från samma prenumeration

1. Bläddra till API Management-instansen i Azure-portalen.
2. Välj fliken **Extern cache** på menyn till vänster.
3. Klicka på knappen **+ Lägg till**.
4. Välj din cache i det nedrullningsbara fältet **Cacheinstans**.
5. Välj **standard** eller ange önskad region i list rutan **Använd från** .
6. Klicka på **Spara**.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Lägga till en Azure Cache for Redis som hanteras utanför den aktuella Azure-prenumerationen eller Azure i allmänhet

1. Bläddra till API Management-instansen i Azure-portalen.
2. Välj fliken **Extern cache** på menyn till vänster.
3. Klicka på knappen **+ Lägg till**.
4. Välj **Anpassad** i det nedrullningsbara fältet **Cacheinstans**.
5. Välj **standard** eller ange önskad region i list rutan **Använd från** .
6. Ange din anslutningssträng för Azure Cache for Redis i fältet **Anslutningssträng**.
7. Klicka på **Spara**.

### <a name="add-a-redis-cache-to-a-self-hosted-gateway"></a>Lägg till en Redis-cache till en egen värd-Gateway

1. Bläddra till API Management-instansen i Azure-portalen.
2. Välj fliken **Extern cache** på menyn till vänster.
3. Klicka på knappen **+ Lägg till**.
4. Välj **Anpassad** i det nedrullningsbara fältet **Cacheinstans**.
5. Ange önskad Gateway-plats eller **standard** i list rutan **Använd** .
6. Ange din anslutningssträng för Redis-cache i fältet **Anslutningssträng**.
7. Klicka på **Spara**.

## <a name="use-the-external-cache"></a>Använda den externa cachen

När den externa cachen har konfigurerats i Azure API Management kan den användas via cachelagringsprinciper. Detaljerade steg finns i avsnittet om att [lägga till cachelagring för att förbättra prestanda i Azure API Management](api-management-howto-cache.md).

## <a name="next-steps"></a><a name="next-steps"> </a>Nästa steg

* Mer information om cachelagringsprinciper finns i [Cachelagringsprinciper][Caching policies] i [Principreferens för API Management][API Management policy reference].
* Mer information om hur du cachelagrar objekt med nycklar med hjälp av principuttryck finns i [Anpassad cachelagring i Azure API Management](api-management-sample-cache-by-key.md).

[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-caching-policies.md
