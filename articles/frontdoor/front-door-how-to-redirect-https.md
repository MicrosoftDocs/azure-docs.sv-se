---
title: Skapa en frontend-dörr med HTTP till HTTPS-omdirigering med hjälp av Azure Portal
description: Lär dig hur du skapar en frontend-dörr med Omdirigerad trafik från HTTP till HTTPS med hjälp av Azure Portal.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 19908b3cba63bc76a205097ef8d16e612d58503b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91626650"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Skapa en frontend-dörr med HTTP till HTTPS-omdirigering med hjälp av Azure Portal

Du kan använda Azure Portal för att [skapa en frontend-dörr](quickstart-create-front-door.md) med ett certifikat för TLS-avslutning. En regel för routning används för att omdirigera HTTP-trafik till HTTPS.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Skapa en frontend-dörr med en befintlig Web App-resurs

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

1. Välj **skapa en resurs** som finns i det övre vänstra hörnet av Azure Portal.

1. Sök efter **front dörr** med Sök fältet och när du har hittat resurs typen väljer du **skapa**.

1. Välj en *prenumeration* och Använd antingen en befintlig resurs grupp eller skapa en ny. Välj **Nästa** för att ange fliken konfiguration.

    > [!NOTE]
    > Den plats som visas i användar gränssnittet är endast för resurs gruppen. Konfigurationen av din front dörr kommer att distribueras över alla [pop-platser i Azures front dörr](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door).

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-basics.png" alt-text="Konfigurera grunderna för ny front dörr":::

1. Konfigurationen för front dörren sker i tre steg – lägga till en standard klient dels värd, lägga till Server delar i en backend-pool och sedan skapa routningsregler för att mappa routnings beteendet för klient dels värden. Välj **+** ikonen på _klient dels värdarna_ för att skapa en klient dels värd.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer.png" alt-text="Konfigurations design för front dörr":::

1. Ange ett globalt unikt namn för din standard klient värd för din front dörr. Välj **Lägg till** för att fortsätta till nästa steg.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-frontend-host.png" alt-text="Lägg till en klient dels värd":::

### <a name="create-backend-pool"></a>Skapa backend-pool

1. Välj **+** ikonen i _backend-poolerna_ för att skapa en backend-pool. Ange ett namn för backend-poolen och välj sedan **Lägg till en server** del.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-backend-pool.png" alt-text="Backend-pool för konfigurations design för front dörr":::

1. Välj server dels värd typen som _App Service_. Välj den prenumeration där din webbapp finns och välj sedan den aktuella webbappen i list rutan för **Server dels värdens namn**.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-backend-pool.png" alt-text="Lägg till en server del i en backend-pool":::

1. Välj **Lägg till** för att spara Server delen och välj **Lägg till** igen för att spara konfiguration av backend-poolen. 

## <a name="create-http-to-https-redirect-rule"></a>Skapa Omdirigerad regel för HTTP till HTTPS

1. Välj **+** ikonen i *routningsregler* för att skapa en väg. Ange ett namn för vägen, till exempel ' HttpToHttpsRedirect ', och ange sedan fältet *godkänt protokoll* till **"endast http"**. Kontrol lera att rätt *klient del/domäner* är markerade.  

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-routing-rule.png" alt-text="Regel för design av front dörrs konfiguration":::

1. Under avsnittet *flödes information* anger du den *väg typ* som ska **omdirigeras**. Se till att *omdirigerings typen* get anges till **found (302)** och *omdirigerade Protocol* get set till **endast https**. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-redirect-config-example.png" alt-text="Lägg till en HTTP till HTTPS-Omdirigerad väg":::

1. Välj **Lägg till** för att spara regeln för omdirigering av http till https.

## <a name="create-forwarding-rule"></a>Skapa vidarebefordrings regel

1. Lägg till en annan routningsprincip för att hantera HTTPS-trafiken. Välj " **+** ' sign on the *Routing Rules* och ange ett namn för vägen, till exempel ' DefaultForwardingRoute '. Ange sedan fältet *accepterade protokoll* till **endast https**. Kontrol lera att rätt *klient del/domäner* är markerade.

1. I avsnittet flödes information anger du vilken *typ av väg* som ska **vidarebefordras**. Kontrol lera att rätt backend-pool väljs och att *vidarebefordrings protokollet* är inställt på **endast https**. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-forward-route-example.png" alt-text="Lägg till en vidarebefordrande väg för HTTPS-trafik" border="false":::

1. Välj **Lägg till** för att spara regeln för vidarebefordran av förfrågningar.

1. Välj **Granska + skapa** och sedan **skapa** för att skapa din profil för din front dörr. Gå till resursen när den har skapats.

## <a name="next-steps"></a>Nästa steg

- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
- Läs mer om [URL-omdirigering på front dörren](front-door-url-redirect.md).
