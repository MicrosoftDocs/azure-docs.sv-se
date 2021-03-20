---
title: Så här ställer du in eller redigerar principer för Azure API Management | Microsoft Docs
description: Lär dig hur du ställer in eller redigerar Azure API Management-principer. Dessa principer är XML-dokument som beskriver en sekvens med inkommande och utgående instruktioner.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2018
ms.author: apimpm
ms.openlocfilehash: a737fada3e019029967bc752aaa0dedc354fa880
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92078331"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Så anger eller redigerar du Azure API Management-principer

Princip definitionen är ett XML-dokument som beskriver en sekvens med inkommande och utgående instruktioner. XML-filen kan redige ras direkt i definitions fönstret. Du kan också välja en fördefinierad princip i listan som finns till höger i princip fönstret. De instruktioner som gäller för det aktuella omfånget är aktiverade och markerade. Om du klickar på en aktive rad instruktion läggs lämplig XML till i positionen för markören i definitions vyn. 

Detaljerad information om principer finns [i principer i Azure API Management](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Ange eller redigera en princip

Följ stegen nedan om du vill ange eller redigera en princip:

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Bläddra till APIM-instansen.
3. Klicka på fliken **API:er**.

    ![Redigera princip](./media/set-edit-policies/code-editor.png)

4. Välj ett av de API:er som du tidigare har importerat.
5. Välj fliken **Design**.
6. Välj en åtgärd som du vill tillämpa principen på. Om du vill tillämpa principen på alla åtgärder väljer du **alla åtgärder**.
7. Välj **</>** ikonen (kod redigerare) i avsnittet **inkommande bearbetning** eller **utgående bearbetning** .
8. Klistra in önskad princip kod i ett av de lämpliga blocken.

    ```xml
    <policies>
        <inbound>
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```
 
## <a name="configure-scope"></a>Konfigurera omfång

Principer kan konfigureras globalt eller i omfånget för en produkt, API eller åtgärd. Om du vill börja konfigurera en princip måste du först välja det omfång som principen ska gälla för.

Princip omfattningarna utvärderas i följande ordning:

1. Globalt omfång
2. Produkt omfattning
3. API-omfång
4. Åtgärds omfång

Uttrycken i principer utvärderas enligt `base` elementets placering, om det finns. Den globala principen har ingen överordnad princip och använder `<base>` elementet i den har ingen påverkan.

Om du vill se principerna i det aktuella omfånget i princip redigeraren klickar du på **Beräkna om en effektiv princip för det valda omfånget**.

### <a name="global-scope"></a>Globalt omfång

Globalt scope har kon figurer ATS för **alla API: er** i din APIM-instans.

1. Logga in på [Azure Portal](https://portal.azure.com/) och navigera till din APIM-instans.
2. Klicka på **alla API: er**.

    ![Globalt omfång](./media/api-management-howto-policies/global-scope.png)

3. Klicka på triangel-ikonen.
4. Välj **Kodredigeraren**.
5. Lägg till eller redigera principer.
6. Tryck på **Spara**. 

    Ändringarna sprids omedelbart till API Management gatewayen.

### <a name="product-scope"></a>Produkt omfattning

Produkt omfattning har kon figurer ATS för den valda produkten.

1. Klicka på **produkter**.

    ![Produkt omfattning](./media/api-management-howto-policies/product-scope.png)

2. Välj den produkt som du vill tillämpa principer på.
3. Klicka på **principer**.
4. Lägg till eller redigera principer.
5. Tryck på **Spara**. 

### <a name="api-scope"></a>API-omfång

API-omfång har kon figurer ATS för **alla åtgärder** för det valda API: et.

1. Välj det **API** som du vill tillämpa principer på.

    ![API-omfång](./media/api-management-howto-policies/api-scope.png)

2. Välj **Alla åtgärder**
3. Klicka på triangel-ikonen.
4. Välj **Kodredigeraren**.
5. Lägg till eller redigera principer.
6. Tryck på **Spara**. 

### <a name="operation-scope"></a>Åtgärds omfång 

Åtgärds omfånget har kon figurer ATS för den valda åtgärden.

1. Välj ett **API**.
2. Välj den åtgärd som du vill tillämpa principer på.

    ![Åtgärds omfång](./media/api-management-howto-policies/operation-scope.png)

3. Klicka på triangel-ikonen.
4. Välj **Kodredigeraren**.
5. Lägg till eller redigera principer.
6. Tryck på **Spara**. 

## <a name="next-steps"></a>Nästa steg

Se följande Närliggande avsnitt:

+ [Transformera API: er](transform-api.md)
+ [Princip referens](./api-management-policies.md) för en fullständig lista över princip satser och deras inställningar
+ [Principexempel](./policy-reference.md)