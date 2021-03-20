---
title: Konfiguration av HTTP-inställningar för Azure Application Gateway
description: Den här artikeln beskriver hur du konfigurerar HTTP-inställningar för Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: c0c939a6a8323dfdfafddb46ccdb7d7ef3dd2f2c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89653024"
---
# <a name="application-gateway-http-settings-configuration"></a>Application Gateway konfiguration av HTTP-inställningar

Application Gateway dirigerar trafik till backend-servrarna med hjälp av den konfiguration som du anger här. När du har skapat en HTTP-inställning måste du associera den med en eller flera regler för begäran-routning.

## <a name="cookie-based-affinity"></a>Cookie-baserad tillhörighet

Azure Application Gateway använder Gateway-hanterade cookies för att underhålla användarsessioner. När en användare skickar den första begäran till Application Gateway, ställer den in en tillhörighets-cookie i svaret med ett hash-värde som innehåller sessionsinformation, så att efterföljande begär Anden som använder tillhörighets-cookien dirigeras till samma backend-server för att upprätthålla varaktighet. 

Den här funktionen är användbar när du vill behålla en användarsession på samma server och när sessionstillstånd sparas lokalt på servern för en användarsession. Om programmet inte kan hantera cookie-baserad tillhörighet kan du inte använda den här funktionen. Kontrol lera att klienterna har stöd för cookies för att använda den.

Chrome [Browser](https://www.chromium.org/Home) - [V80 uppdaterar](https://chromiumdash.appspot.com/schedule) ett uppdrag där http-cookies utan [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) -attribut måste behandlas som SameSite = lax. Om en cookie måste skickas i en tredjeparts kontext måste den använda *SameSite = none för CORS-begäranden (Cross-Origin resurs delning). Säkra* attribut och bör endast skickas över https. I annat fall skickar webbläsaren inte cookies i den tredje partens sammanhang. Målet med den här uppdateringen från Chrome är att förbättra säkerheten och undvika CSRF-attacker (Cross-Site request förfalskning). 

För att stödja den här ändringen, från och med 17 2020 februari, kommer Application Gateway (alla SKU-typer) att injicera en annan cookie med namnet *ApplicationGatewayAffinityCORS* förutom den befintliga *ApplicationGatewayAffinity* -cookien. *ApplicationGatewayAffinityCORS* -cookien har två fler attribut som läggs till i den (*"SameSite = none; Säker "*) så att den tröga sessionen upprätthålls även för frågor över andra ursprung.

Observera att standard tillhörighets-Cookiens namn är *ApplicationGatewayAffinity* och att du kan ändra det. Om du använder ett namn på en anpassad tillhörighet, läggs en ytterligare cookie till med CORS som suffix. Till exempel *CustomCookieNameCORS*.

> [!NOTE]
> Om attributet *SameSite = inget* anges är det obligatoriskt att cookien även innehåller en *säker* flagga och måste skickas via https.  Om session tillhörighet krävs över CORS måste du migrera din arbets belastning till HTTPS. Läs mer i TLS Offload och end-to-end TLS-dokumentation för Application Gateway här – [Översikt](ssl-overview.md), [Konfigurera en PROGRAMgateway med TLS-avslutning med hjälp av Azure Portal](create-ssl-portal.md), [Konfigurera end-to-end-TLS genom att använda Application Gateway med portalen](end-to-end-ssl-portal.md).

## <a name="connection-draining"></a>Anslutningstömning

Med anslutnings tömning kan du på ett smidigt sätt ta bort backend-medlemmarnas medlemmar under planerade tjänst uppdateringar. Du kan använda den här inställningen för alla medlemmar i en backend-pool genom att aktivera anslutning som töms på HTTP-inställningen. Det garanterar att alla avregistreringar av instanser av en backend-pool fortsätter att underhålla befintliga anslutningar och att betjäna begär Anden för en konfigurerbar tids gräns och inte får några nya förfrågningar eller anslutningar. Det enda undantaget till detta är begär Anden som är kopplade till deregistrering av instanser på grund av en Gateway-hanterad session tillhörighet och fortsätter att vidarebefordras till Avregistrerings instanserna. Anslutnings tömning gäller för Server dels instanser som uttryckligen tas bort från backend-poolen.

## <a name="protocol"></a>Protokoll

Application Gateway stöder både HTTP och HTTPS för routnings begär anden till backend-servrarna. Om du väljer HTTP är trafik till backend-servrarna okrypterad. Om okrypterad kommunikation inte är acceptabel väljer du HTTPS.

Den här inställningen kombinerad med HTTPS i lyssnaren stöder [end-to-end-TLS](ssl-overview.md). På så sätt kan du på ett säkert sätt överföra känsliga data som har krypterats till Server delen. Varje backend-server i backend-poolen där TLS från slut punkt till slut punkt är aktiverat måste konfigureras med ett certifikat för att tillåta säker kommunikation.

## <a name="port"></a>Port

Den här inställningen anger den port där backend-servrarna lyssnar på trafik från programgatewayen. Du kan konfigurera portar mellan 1 och 65535.

## <a name="request-timeout"></a>Timeout för begäran

Den här inställningen är antalet sekunder som Application Gateway väntar på att få svar från backend-servern.

## <a name="override-back-end-path"></a>Åsidosätt backend-sökväg

Med den här inställningen kan du konfigurera en valfri anpassad vidarebefordrings Sök väg som ska användas när begäran vidarebefordras till Server delen. Alla delar av den inkommande sökvägen som matchar den anpassade sökvägen i fältet **Åsidosätt sökväg till Server** del kopieras till den vidarebefordrade sökvägen. Följande tabell visar hur den här funktionen fungerar:

- När HTTP-inställningen är kopplad till en grundläggande regel för begäran-routning:

  | Ursprunglig begäran  | Åsidosätt backend-sökväg | Begäran vidarebefordras till Server delen |
  | ----------------- | --------------------- | ---------------------------- |
  | bostad            | åsidosättningsinställning            | /override/home/              |
  | /home/secondhome/ | åsidosättningsinställning            | /override/home/secondhome/   |

- När HTTP-inställningen är kopplad till en Sök vägs baserad regel för begäran-routning:

  | Ursprunglig begäran           | Sök vägs regel       | Åsidosätt backend-sökväg | Begäran vidarebefordras till Server delen |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | pathrule      | åsidosättningsinställning            | /override/home/              |
  | /pathrule/home/secondhome/ | pathrule      | åsidosättningsinställning            | /override/home/secondhome/   |
  | bostad                     | pathrule      | åsidosättningsinställning            | /override/home/              |
  | /home/secondhome/          | pathrule      | åsidosättningsinställning            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | åsidosättningsinställning            | åsidosättningsinställning                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | åsidosättningsinställning            | /override/secondhome/        |
  | pathrule                 | pathrule      | åsidosättningsinställning            | åsidosättningsinställning                   |

## <a name="use-for-app-service"></a>Använd för App Service

Det här är endast ett användar gränssnitt som väljer de två nödvändiga inställningarna för Azure App Service server del. Det aktiverar **Välj värd namnet från backend-adressen** och skapar en ny anpassad avsökning om du inte redan har en. (Mer information finns i avsnittet [Välj värdnamn från backend-adress](#pick-host-name-from-back-end-address)i den här artikeln.) En ny avsökning skapas och avsöknings huvudet plockas från Server delens medlem adress.

## <a name="use-custom-probe"></a>Använd anpassad avsökning

Den här inställningen associerar en [anpassad avsökning](application-gateway-probe-overview.md#custom-health-probe) med en http-inställning. Du kan bara associera en anpassad avsökning med en HTTP-inställning. Om du inte uttryckligen associerar en anpassad avsökning används [standard avsökningen](application-gateway-probe-overview.md#default-health-probe-settings) för att övervaka Server delens hälso tillstånd. Vi rekommenderar att du skapar en anpassad avsökning för bättre kontroll över hälso övervakningen av dina Server delar.

> [!NOTE]
> Den anpassade avsökningen övervakar inte Server delens hälso tillstånd om inte den motsvarande HTTP-inställningen uttryckligen är associerad med en lyssnare.

## <a name="pick-host-name-from-back-end-address"></a>Välj värdnamn från Server dels adress

Den här funktionen ställer dynamiskt in *värd* rubriken i begäran till värd namnet för backend-poolen. Den använder en IP-adress eller ett fullständigt domän namn.

Den här funktionen hjälper när Server dels domän namnet skiljer sig från DNS-namnet på programgatewayen och Server delen förlitar sig på en specifik värd rubrik för att matcha rätt slut punkt.

Ett exempel är ett exempel på flera klient tjänster som server delen. En app service är en tjänst för flera innehavare som använder ett delat utrymme med en enda IP-adress. Därför kan en app service bara nås via de värdnamn som kon figurer ATS i inställningarna för den anpassade domänen.

Som standard är det anpassade domän namnet *example.azurewebsites.net*. För att få åtkomst till din app service genom att använda en Programgateway via ett värdnamn som inte uttryckligen registreras i App Service eller via programgatewayens FQDN, åsidosätter du värd namnet i den ursprungliga begäran till App Service-värdnamnet. Det gör du genom att aktivera inställningen **Välj värd namn från Server dels adress** .

För en anpassad domän vars befintliga anpassade DNS-namn mappas till App Service behöver du inte aktivera den här inställningen.

> [!NOTE]
> Den här inställningen krävs inte för App Service-miljön, som är en dedikerad distribution.

## <a name="host-name-override"></a>Åsidosätt värdnamn

Den här funktionen ersätter *värd* rubriken i den inkommande begäran på programgatewayen med det värdnamn som du anger.

Om *www.contoso.com* till exempel anges i inställningen **värd namn** , ändras den ursprungliga begäran * `https://appgw.eastus.cloudapp.azure.com/path1` till * `https://www.contoso.com/path1` när begäran vidarebefordras till backend-servern.

## <a name="next-steps"></a>Nästa steg

- [Lär dig mer om backend-poolen](configuration-overview.md#back-end-pool)