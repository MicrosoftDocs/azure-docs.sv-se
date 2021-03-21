---
title: 'Snabb start: skapa en Redis företags-cache'
description: I den här snabb starten får du lära dig hur du skapar en instans av Azure cache för Redis på företags nivåer
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 02/08/2021
ms.openlocfilehash: 8b450ddff0952777652a957ba2ed554a4bc6497d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584830"
---
# <a name="quickstart-create-a-redis-enterprise-cache"></a>Snabb start: skapa en Redis företags-cache

Azure cache för Redis "företags nivåer ger fullständigt integrerat och hanterade [Redis Enterprise](https://redislabs.com/redis-enterprise/) på Azure. Dessa nya nivåer är:
* Enterprise, som använder flyktigt minne (DRAM) på en virtuell dator för att lagra data
* Enterprise Flash, som använder både flyktigt och icke-flyktigt minne (NVMe eller SSD) för att lagra data.

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Azure-prenumeration innan du börjar. Skapa ett [konto](https://azure.microsoft.com/)om du inte har något. Mer information finns i [särskilda överväganden för företags nivåer](cache-overview.md#special-considerations-for-enterprise-tiers).

## <a name="create-a-cache"></a>Skapa en cache
1. Om du vill skapa en cache loggar du in på Azure Portal och väljer **skapa en resurs**.

1. Välj **databaser** på sidan **nytt** och välj sedan **Azure cache för Redis**.
   
   :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Välj Azure-cache för Redis":::
   
1. På sidan **ny Redis cache** konfigurerar du inställningarna för din nya cache.
   
   | Inställning      | Föreslaget värde  | Beskrivning |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Prenumeration** | List rutan och välj din prenumeration. | Den prenumeration som du vill skapa den här nya Azure-cache för Redis-instansen för. | 
   | **Resursgrupp** | List rutan och välj en resurs grupp, eller Välj **Skapa ny** och ange ett nytt resurs grupp namn. | Namnet på resurs gruppen där du vill skapa cachen och andra resurser. Genom att lägga till alla dina app-resurser i en resurs grupp kan du enkelt hantera eller ta bort dem tillsammans. | 
   | **DNS-namn** | Ange ett globalt unikt namn. | Cache-namnet måste vara en sträng mellan 1 och 63 tecken som bara innehåller siffror, bokstäver eller bindestreck. Namnet måste börja och sluta med en siffra eller en bokstav och får inte innehålla flera bindestreck i rad. Din cacheposts *värdnamn* är *\<DNS name> . <Azure region> .. redisenterprise.cache.azure.net*. | 
   | **Plats** | List rutan och välj en plats. | Företags nivåer är tillgängliga i de valda Azure-regionerna. |
   | **Cachestorlek** | List rutan och välj en *Enterprise* -eller *Enterprise Flash* -nivå och en storlek. |  Nivån avgör storlek, prestanda och funktioner som är tillgängliga för cachen. |
   
   :::image type="content" source="media/cache-create/enterprise-tier-basics.png" alt-text="Fliken grundläggande för företags nivå":::

   > [!NOTE] 
   > Se till att markera kryss rutan under "villkor" innan du fortsätter.
   >

1. Välj **Nästa: nätverk** och hoppa över.

1. Välj **Nästa: Avancerat** och ange **kluster princip** till **företag**. Aktivera **icke-TLS-åtkomst endast** om du planerar att ansluta till den nya cachen utan att använda TLS. Detta rekommenderas dock inte.

   :::image type="content" source="media/cache-create/enterprise-tier-advanced.png" alt-text="Fliken Avancerat på företags nivå":::

1. Välj **Nästa: Taggar** och hoppa över.

1. Välj **Nästa: Granska + skapa**.

   :::image type="content" source="media/cache-create/enterprise-tier-summary.png" alt-text="Granskning av företags nivå + fliken Skapa":::

1. Granska inställningarna och klicka på **skapa**.
   
   Det tar lite tid för cacheminnet att skapas. Du kan övervaka förloppet på **översikts** sidan för Azure-cache för Redis. När **statusen** är **igång** är cacheminnet redo att användas.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar en Azure-instans av Azure cache för Redis.

> [!div class="nextstepaction"]
> [Skapa en ASP.NET-webbapp som använder en Azure Cache for Redis.](./cache-web-app-howto.md)

