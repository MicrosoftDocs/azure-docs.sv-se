---
title: Lägga till repliker i Azure cache för Redis (för hands version)
description: Lär dig hur du lägger till fler repliker till din Premium-nivå i Azure-cache för Redis-instanser
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 87b5ec5eb13f2bc53bdf993547ce3da1c74404bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91566796"
---
# <a name="add-replicas-to-azure-cache-for-redis-preview"></a>Lägga till repliker i Azure cache för Redis (för hands version)
I den här artikeln får du lära dig hur du konfigurerar en Azure cache-instans med ytterligare repliker med hjälp av Azure Portal.

Azure cache för Redis standard-och Premium-nivåer erbjuder redundans genom att vara värd för varje cache på två dedikerade virtuella datorer (VM). Dessa virtuella datorer är konfigurerade som primära och repliker. När den primära virtuella datorn blir otillgänglig identifierar repliken och tar över som den nya primära servern automatiskt. Du kan nu öka antalet repliker i en Premium-cache upp till tre, vilket ger dig totalt fyra virtuella datorer som säkerhetskopierar en cache. Att ha flera repliker resulterar i högre återhämtning än vad en enskild replik kan ge.

> [!IMPORTANT]
> Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i kompletterande användnings [villkor för Microsoft Azure för hands versionerna.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 
> 

## <a name="prerequisites"></a>Förutsättningar
* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)

> [!NOTE]
> Den här funktionen är för närvarande i för hands version – [kontakta oss](mailto:azurecache@microsoft.com) om du är intresse rad av.
>

## <a name="create-a-cache"></a>Skapa en cache
Följ dessa steg om du vill skapa en cache:

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **Skapa en resurs**.
  
1. Välj **databaser** på sidan **nytt** och välj sedan **Azure cache för Redis**.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Välj Azure-cache för Redis.":::
   
1. På sidan **grundläggande** inställningar konfigurerar du inställningarna för det nya cacheminnet.
   
    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Prenumeration** | Välj din prenumeration. | Den prenumeration som du vill skapa den här nya Azure-cache för Redis-instansen för. | 
    | **Resursgrupp** | Välj en resurs grupp eller Välj **Skapa ny** och ange ett nytt resurs grupp namn. | Namnet på resurs gruppen där du vill skapa cachen och andra resurser. Genom att lägga till alla dina app-resurser i en resurs grupp kan du enkelt hantera eller ta bort dem tillsammans. | 
    | **DNS-namn** | Ange ett globalt unikt namn. | Cache-namnet måste vara en sträng mellan 1 och 63 tecken som bara innehåller siffror, bokstäver eller bindestreck. Namnet måste börja och sluta med en siffra eller en bokstav och får inte innehålla flera bindestreck i rad. Din cacheposts *värdnamn* är *\<DNS name> . Redis.cache.Windows.net*. | 
    | **Plats** | Välj en plats. | Välj en [region](https://azure.microsoft.com/regions/) nära andra tjänster som ska använda din cache. |
    | **Cachestorlek** | Välj ett cacheminne för [Premium-nivån](https://azure.microsoft.com/pricing/details/cache/) . |  Prisnivån avgör storlek, prestanda och funktioner som är tillgängliga för cacheminnet. Mer information finns i [Översikt över Azure Cache for Redis](cache-overview.md). |
   
1. På sidan **Avancerat** väljer du **replik antal**.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Antal repliker.":::

1. Lämna de andra alternativen i standardinställningarna. 

    > [!NOTE]
    > Stöd för flera repliker fungerar för närvarande endast med icke-klustrade cacheminnen.
    >

1. Klicka på **Skapa**.
   
    Det tar en stund innan cacheminnet skulle skapas. Du kan övervaka förloppet på **översikts** sidan för Azure-cache för Redis. När **statusen** är **igång** är cacheminnet redo att användas.

    > [!NOTE]
    > Antalet repliker i en cache kan inte ändras efter att det har skapats.
    >

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure cache för Redis-funktioner.

> [!div class="nextstepaction"]
> [Azure cache för Redis Premium service-nivåer](cache-overview.md#service-tiers)
