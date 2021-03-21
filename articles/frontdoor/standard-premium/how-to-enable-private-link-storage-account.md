---
title: Anslut Azure front dörr Premium till ett lagrings konto ursprung med privat länk
titleSuffix: Azure Private Link
description: Lär dig hur du ansluter Azure frontend-dörren till ett privat lagrings konto.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/04/2021
ms.author: tyao
ms.openlocfilehash: 885b4d132208ab6f8b470d147438e26a5fd4bab7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201676"
---
# <a name="connect-azure-front-door-premium-to-a-storage-account-origin-with-private-link"></a>Anslut Azure front dörr Premium till ett lagrings konto ursprung med privat länk

Den här artikeln beskriver hur du konfigurerar Azure frontend-SKU: n för att ansluta till ditt lagrings konto ursprung privat med tjänsten Azure Private Link.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="enable-private-link-to-a-storage-account"></a>Aktivera privat länk till ett lagrings konto
 
I det här avsnittet ska du mappa den privata länk tjänsten till en privat slut punkt som skapats i Azures front dörrs privata nätverk. 

1. I din Azure front dörr Premium-profil väljer du **ursprungs grupper** under *Inställningar*.

1. Välj den ursprungs grupp som innehåller det lagrings konto ursprung som du vill aktivera privat länk för.

1. Välj **+ Lägg till ett ursprung** för att lägga till ett nytt lagrings konto ursprung eller Välj ett tidigare skapat lagrings konto ursprung från listan.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-storage-account.png" alt-text="Skärm bild som visar hur du aktiverar privat länk till ett lagrings konto.":::

1. För **Välj en Azure-resurs** väljer du **i min katalog**. Välj eller ange följande inställningar för att konfigurera den plats som du vill att Azure frontend-dörren ska ansluta till privat.

    | Inställning | Värde |
    | ------- | ----- |
    | Region | Välj den region som är samma eller närmast ditt ursprung. |
    | Resurstyp | Välj **Microsoft. Storage/storageAccounts**. |
    | Resurs | Välj ditt lagringskonto. |
    | Mål under resurs | Du kan välja *BLOB* eller *webb*. |
    | Begär ande meddelande | Anpassa meddelandet eller Välj standard. |

1. Välj sedan **Lägg till** för att spara konfigurationen.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>Godkänn privat slut punkts anslutning från lagrings kontot

1. Gå till det lagrings konto som du konfigurerar privat länk för i det sista avsnittet. Välj **nätverk** under **Inställningar**.

1. I **nätverk** väljer du **privata slut punkts anslutningar**. 

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/storage-account-configure-endpoint.png" alt-text="Skärm bild av nätverks inställningar i en webbapp.":::

1. Välj den *väntande* privata slut punkts förfrågan från Azure front dörr Premium och välj sedan **Godkänn**.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-pending-approval.png" alt-text="Skärm bild av väntande begäran om privat slut punkt för lagring.":::

1. När den har godkänts bör den se ut som skärm bilden nedan. Det tar några minuter för anslutningen att etableras fullständigt. Du kan nu komma åt ditt lagrings konto från Azures front dörr Premium.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="Skärm bild av en godkänd begäran om lagrings slut punkt.":::

## <a name="next-steps"></a>Nästa steg

Läs om [tjänsten för privat länk med lagrings kontot](../../storage/common/storage-private-endpoints.md).
