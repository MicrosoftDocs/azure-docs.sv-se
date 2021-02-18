---
title: Anslut Azure front dörr Premium till ditt ursprung med privat länk
description: Lär dig hur du ansluter din Azure frontend-dörr till ditt ursprung med privat länk-tjänsten med hjälp av Azure Portal.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: tyao
ms.openlocfilehash: 3015a0560171b61aaab05e27b369d9ca531e81c1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099457"
---
# <a name="connect-azure-front-door-premium-to-your-origin-with-private-link"></a>Anslut Azure front dörr Premium till ditt ursprung med privat länk

Den här artikeln beskriver hur du konfigurerar Azure frontend-SKU: n för att ansluta till dina program som finns i ett virtuellt nätverk med hjälp av tjänsten Azure Private Link.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Skapa en [privat länk](../../private-link/create-private-link-service-portal.md) tjänst för dina ursprungliga webb servrar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="enable-private-endpoint-in-azure-front-door-service"></a>Aktivera privat slut punkt i Azures tjänst för frontend-dörr

I det här avsnittet ska du mappa Azure Private Link-tjänsten till en privat slut punkt som skapats i det privata nätverket i Azure frontend Premium SKU: n. 

1. I din Azure front dörr Premium-profil väljer du **ursprungs grupper** under *Inställningar*.

1. Välj den ursprungs grupp som innehåller det ursprung som du vill aktivera privat länk för.

1. Välj **+ Lägg till ett ursprung** för att lägga till ett nytt ursprung eller Välj ett tidigare skapat ursprung i listan. Markera sedan kryss rutan för att **aktivera tjänsten för privat länk**.

    :::image type="content" source="../media/how-to-enable-private-link/front-door-private-endpoint-private-link.png" alt-text="Skärm bild som visar hur du aktiverar privat länk på Lägg till en start sida.":::

1. För **Välj en Azure-resurs** väljer du **i min katalog**. Välj eller ange följande inställning för att konfigurera den resurs som du vill att Azure front dörr Premium ska ansluta till privat.
    
    | Inställning | Värde |
    | ------- | ----- |
    | Region | Välj den region som är samma eller närmast ditt ursprung. |
    | Resurstyp | Välj **Microsoft. Network/privateLinkServices**. |
    | Resurs | Välj **myPrivateLinkService**. |
    | Mål under resurs | Lämna det här fältet tomt. |
    | Begär ande meddelande | Anpassa meddelandet eller Välj standard meddelandet. |

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [privat länk för Azures främre dörrs premie](concept-private-link.md).
