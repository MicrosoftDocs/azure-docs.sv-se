---
title: Automatisera tjänst registret och identifiering
description: Lär dig hur du automatiserar tjänst identifiering och registrering med hjälp av vår moln tjänst register
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: e2ad6d8e3d81497eb7ebe612d01e60415790faea
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878669"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Identifiera och registrera dina våren Cloud-tjänster

Tjänst identifiering är ett nyckel krav för en mikroservad arkitektur.  Det tar tid att konfigurera varje klient manuellt och vi presenterar risken för mänskligt fel.  Azure våren Cloud Service-registret löser det här problemet.  När den har kon figurer ATS kommer en tjänst register server att styra registreringen och identifieringen av programmets mikrotjänster. Tjänst register servern hanterar ett register med de distribuerade mikrotjänster, aktiverar belastnings utjämning på klient sidan och tar bort tjänst leverantörer från klienter utan att behöva använda DNS.

::: zone pivot="programming-language-csharp"
Information om hur du konfigurerar tjänst registrering för en Steeltoe-app finns i [förbereda ett Java våren-program för distribution i Azure våren Cloud](how-to-prepare-app-deployment.md).
::: zone-end
::: zone pivot="programming-language-java"

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Registrera ditt program med vår moln tjänst register

Innan ditt program kan hantera registrering och identifiering av tjänster med hjälp av våren Cloud Service Registry måste flera beroenden ingå i programmets *pom.xml* -fil.
Inkludera beroenden för *våren-Cloud-starter-Netflix-Eureka-client* och *våren-Cloud-starter-Azure-våren-Cloud-client* till din *pom.xml*

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>Uppdatera den översta nivån-klassen

Slutligen lägger vi till en anteckning till den översta klassen i programmet

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

Källan till moln tjänstens register server kommer att matas in som en miljö variabel i ditt program.  Mikrotjänster kommer nu att kunna registrera sig själva med tjänstens register Server och identifiera andra beroende mikrotjänster.

Observera att det kan ta några minuter innan ändringarna sprids från servern till alla mikrotjänster.
::: zone-end
