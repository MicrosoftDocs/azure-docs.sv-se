---
title: Hantera och övervaka appen med Azure Spring Boot Actuator
description: Lär dig hur du hanterar och övervakar appar med våren Boot-motstånd.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 93fd286aa76a0409a515abbf8c9dabd88a9a65c4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878311"
---
# <a name="manage-and-monitor-app-with-azure-spring-boot-actuator"></a>Hantera och övervaka appen med Azure Spring Boot Actuator

**Den här artikeln gäller för:** ✔️ Java

När du har distribuerat ny binärfil till appen kanske du vill kontrol lera funktionerna och se information om ditt program som körs. Den här artikeln förklarar hur du får åtkomst till API: et från en test slut punkt som tillhandahålls av Azure våren Cloud och som visar produktions klara funktioner för din app.

## <a name="prerequisites"></a>Förutsättningar
I den här artikeln förutsätter vi att du har ett program med vår start 2. x som kan distribueras och startas på Azure våren Cloud service.  Se [snabb start: starta ett befintligt Azure våren Cloud-program med hjälp av Azure Portal](spring-cloud-quickstart.md)

## <a name="verify-app-through-test-endpoint"></a>Verifiera appen via test slut punkten
1. Gå till **instrument panelen för program** och klicka på din app för att ange appens översikts sida.

1. I **översikts** fönstret bör du se **test-slutpunkt**.  Gå till den här slut punkten från kommando raden eller webbläsaren och observera API-svaret.

1. Observera den **test slut punkts** -URI som ska användas i det kommande avsnittet.

>[!TIP]
> * Om appen returnerar en klient dels sida och refererar till andra filer via relativ sökväg, kontrollerar du att test slut punkten slutar med ett snedstreck (/). På så sätt ser du till att CSS-filen läses in på rätt sätt.
> * Om du visar ditt API från en webbläsaren och din webbläsare kräver att du anger inloggnings uppgifter för att visa sidan använder du [URL-avkodning](https://www.urldecoder.org/) för att avkoda test slut punkten. URL-avkodning returnerar en URL i formatet "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/ \<app-name> / \<deployment-name> ".  Använd det här formuläret för att få åtkomst till din slut punkt.

## <a name="add-actuator-dependency"></a>Lägg till motstånds beroende

Lägg till en manövreringsre i ett maven-baserat projekt genom att lägga till "Start"-beroendet:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

Kompilera den nya binärfilen och distribuera den till din app.

## <a name="enable-production-ready-features"></a>Aktivera produktions klara funktioner
Med hanterings slut punkter kan du övervaka och interagera med ditt program. Som standard visar våren Boot Application program `health` och `info` slut punkter för att visa information om godtycklig program information och hälso information.

För att studera konfigurationen och konfigurerbar miljö behöver vi `env` även aktivera och `configgrops` slut punkter.

1. Gå till **översikts** fönstret för appen, klicka på **konfiguration** i menyn Inställningar, gå till konfigurations sidan för **miljövariabler** .
1. Lägg till följande egenskaper som i formuläret "nyckel: värde". I den här miljön öppnas våren "miljö", "Health", "info" för våren-manövrerings punkten.

   ```
   management.endpoints.web.exposure.include: env,health,info
   ```
1. Klicka på knappen **Spara** , programmet startas om automatiskt och läser in de nya miljövariablerna.

Nu kan du gå tillbaka till fönstret Översikt över appen och vänta tills etablerings statusen ändras till "lyckades".  Det kommer att finnas fler än en instans som körs.

> [!Note] 
> När du exponerar appen för offentlig är dessa motstånds-och utgångs punkter även tillgängliga för offentliga. Du kan dölja alla slut punkter genom att ta bort miljövariablerna `management.endpoints.web.exposure.include` och ange `management.endpoints.web.exposure.exclude=*`

## <a name="view-the-actuator-endpoint-to-view-application-information"></a>Visa programinformationens slut punkt genom att Visa programinformation
1. Nu kan du få åtkomst till URL: en `"<test-endpoint>/actuator/"` för att se alla slut punkter som exponeras av våren Boot Boot-motstånd.
1. Åtkomst `"<test-endpoint>/actuator/env"` -URL, du kan se aktiva profiler som används av appen och alla miljövariabler som har lästs in.
1. Om du vill söka i en viss miljö kan du öppna URL: en  `"<test-endpoint>/actuator/env/{toMatch}"` för att visa den.

Om du vill visa alla inbyggda slut punkter, se [exponera slut punkter](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-endpoints-exposing-endpoints)

## <a name="next-steps"></a>Nästa steg

* [Förstå mått för Azure våren Cloud](spring-cloud-concept-metrics.md)
* [Förstå appstatus i Azure Spring Cloud](spring-cloud-concept-app-status.md)

