---
title: Hantera trafik till appar för flera klienter med hjälp av portalen
titleSuffix: Azure Application Gateway
description: Den här artikeln innehåller rikt linjer för hur du konfigurerar Azure App tjänst webbappar som medlemmar i backend-poolen på en befintlig eller ny Programgateway.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/02/2021
ms.author: victorh
ms.openlocfilehash: aadd4904ff218613c0dd24daff784ad5b8b90fbb
ms.sourcegitcommit: c538b6e4cf27b992500c079ad9c914c05d55eb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854918"
---
# <a name="configure-app-service-with-application-gateway"></a>Konfigurera App Service med Application Gateway

Eftersom App Service är en tjänst för flera innehavare och inte en dedikerad distribution, används värdhuvud i inkommande begäran för att matcha begäran med rätt App Service-slutpunkt. DNS-namnet på programmet, som i sin tur är det DNS-namn som är associerat med programgatewayen fram till App Service, skiljer sig vanligt vis från domän namnet för Server delens app service. Därför är värd rubriken i den ursprungliga begäran som togs emot av Application Gateway inte samma som värd namnet för backend-tjänsten. På grund av detta, om inte värd huvudet i begäran från programgatewayen till Server delen ändras till Server dels tjänstens värdnamn, kan inte Server delen för flera klient organisationer lösa begäran till rätt slut punkt.

Application Gateway innehåller en växel som anropar `Pick host name from backend target` och som åsidosätter värd rubriken i begäran med värd namnet för Server delen när begäran dirigeras från Application Gateway till Server delen. Den här funktionen aktiverar stöd för Server delar för flera klient organisationer, till exempel Azure App service och API Management. 

I den här artikeln kan du se hur du:

- Redigera en backend-pool och Lägg till en App Service
- Redigera HTTP-inställningar med växeln Välj värdnamn aktive rad

## <a name="prerequisites"></a>Förutsättningar

- Application Gateway: skapa en Programgateway utan mål för Server delens pool. Mer information finns i [snabb start: direkt webb trafik med Azure Application Gateway – Azure Portal](quick-create-portal.md)

- App Service: om du inte har en befintlig App Service kan du läsa [dokumentationen om App Service](../app-service/index.yml).

## <a name="add-app-service-as-backend-pool"></a>Lägg till App Service som backend-pool

1. I Azure Portal väljer du din Application Gateway.

2. Under **backend-pooler** väljer du backend-poolen.

4. Under **måltyp** väljer du **app Services**.

5. Under **mål** väljer du App Service.

   :::image type="content" source="./media/configure-web-app-portal/backend-pool.png" alt-text="App Service-backend":::
   
   > [!NOTE]
   > List rutan fyller bara i de app Services som finns i samma prenumeration som din Application Gateway. Om du vill använda en app-tjänst som finns i en annan prenumeration än den där Application Gateway är, i stället för att välja **app Services** i list rutan **mål** , väljer du **IP-adress eller värdnamn** och anger värdnamn (exempel. azurewebsites.net) för App Service.
1. Välj **Spara**.

## <a name="edit-http-settings-for-app-service"></a>Redigera HTTP-inställningar för App Service

1. Under **http-inställningar** väljer du den befintliga http-inställningen.

2. Välj **Ja** under **åsidosättande med nytt värdnamn**.
3. Under **Åsidosätt värdnamn** väljer du **Välj värdnamn från backend-målet**.
4. Välj **Spara**.

   :::image type="content" source="./media/configure-web-app-portal/http-settings.png" alt-text="Välj värd namnet från Server delens http-inställningar":::

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Ytterligare konfiguration i händelse av omdirigering till app Services relativa sökväg

När App Service skickar ett svar för omdirigering till klienten som ska omdirigeras till dess relativa sökväg (till exempel en omdirigering från `contoso.azurewebsites.net/path1` till `contoso.azurewebsites.net/path2` ), använder den samma värdnamn i plats rubriken i svars listan som den som finns i den begäran som togs emot från Application Gateway. Klienten kommer därför att göra begäran direkt till `contoso.azurewebsites.net/path2` i stället för att gå igenom Application Gateway ( `contoso.com/path2` ). Att kringgå Application Gateway är inte önskvärt.

Om du är i ditt användnings fall finns det scenarier där App Service måste skicka ett svar på omdirigering till klienten, utföra de [ytterligare stegen för att skriva om plats rubriken](./troubleshoot-app-service-redirection-app-service-url.md#sample-configuration).

## <a name="restrict-access"></a>Begränsa åtkomst

Webbapparna som distribueras i de här exemplen använder offentliga IP-adresser som kan nås direkt från Internet. Detta hjälper till med fel sökning när du lär dig mer om en ny funktion och testar nya saker. Men om du planerar att distribuera en funktion till produktion, vill du lägga till fler begränsningar.

Ett sätt som du kan begränsa åtkomsten till dina webbappar är att använda [Azure App Service statiska IP-begränsningar](../app-service/app-service-ip-restrictions.md). Du kan till exempel begränsa webbappen så att den bara tar emot trafik från Application Gateway. Använd funktionen för IP-begränsning i App Service för att Visa programgatewayens VIP som den enda adressen med åtkomst.

## <a name="next-steps"></a>Nästa steg

Mer information om app service och andra stöd för flera innehavare med Application Gateway finns i [service support för flera innehavare med Application Gateway](./application-gateway-web-app-overview.md).
