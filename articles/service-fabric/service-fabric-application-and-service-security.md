---
title: Lär dig mer om Azure Service Fabric program säkerhet
description: En översikt över hur du på ett säkert sätt kör mikrotjänster-program på Service Fabric. Lär dig hur du kör tjänster och start skript under olika säkerhets konton, autentiserar och auktoriserar användare, hanterar program hemligheter, säker tjänst kommunikation, använder en API-gateway och skyddar program data i vila.
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: f17840f31d2a4c12a1d4618bd16e81dcc2cc8a14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86256585"
---
# <a name="service-fabric-application-and-service-security"></a>Service Fabric program-och tjänst säkerhet
En arkitektur för mikrotjänster kan få [många fördelar](service-fabric-overview-microservices.md). Att hantera säkerheten för mikrotjänster är dock en utmaning och skiljer sig från att hantera traditionell monolitisk program säkerhet. 

Med en monolit körs programmet vanligt vis på en eller flera servrar i ett nätverk och det är lättare att identifiera exponerade portar och API: er och IP-adresser. Det finns ofta en perimeter eller gräns och en databas som ska skyddas. Om systemet komprometteras på grund av en säkerhets överträdelse eller en attack, är det troligt att allting i systemet är tillgängligt för angriparen. Systemet är mer komplext med mikrotjänster.  Tjänsterna är decentraliserade och distribueras på många värdar och migreras från värden till värden.  Med rätt säkerhet begränsar du behörigheterna som en angripare kan få och mängden data som är tillgängliga i ett enda angrepp genom att bryta en tjänst.  Kommunikationen är inte intern, men sker över ett nätverk och det finns många exponerade portar och interaktioner mellan tjänsterna. Du vet vad dessa service interaktioner är och när de inträffar är avgörande för din program säkerhet.

Den här artikeln är inte en guide till säkerhet för mikrotjänster, det finns många sådana resurser som är tillgängliga online, men beskriver hur olika säkerhets aspekter kan utföras i Service Fabric.

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering
Det är ofta nödvändigt att resurser och API: er som exponeras av en tjänst är begränsade till vissa betrodda användare eller klienter. Autentisering är en process där en användares identitet tillförlitligt bevaras.  Auktorisering är den process som gör API: er eller tjänster tillgängliga för vissa autentiserade användare, men inte andra.

### <a name="authentication"></a>Autentisering
Det första steget för att fatta beslut om förtroende för API-nivå är autentisering. Autentisering är en process där en användares identitet tillförlitligt bevaras.  I scenarier med mikrotjänster hanteras autentisering vanligt vis centralt. Om du använder en API-Gateway kan du [avlasta autentisering](/azure/architecture/patterns/gateway-offloading) till gatewayen. Om du använder den här metoden ser du till att de enskilda tjänsterna inte kan nås direkt (utan API-Gateway) om inte ytterligare säkerhet finns för att autentisera meddelanden oavsett om de kommer från gatewayen eller inte.

Om tjänsterna kan nås direkt kan en autentiseringstjänst som Azure Active Directory eller en särskild autentiserings-mikrotjänst som fungerar som en säkerhetstokentjänst användas för att autentisera användare. Förtroende beslut delas mellan tjänster med säkerhetstoken eller cookies. 

För ASP.NET Core är den primära mekanismen för att [autentisera användare](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) det ASP.net Core identitets medlemskaps systemet. ASP.NET Core identitet lagrar användar information (inklusive inloggnings information, roller och anspråk) i ett data lager som kon figurer ATS av utvecklaren. ASP.NET Core identiteten stöder tvåfaktorautentisering.  Externa autentiseringsproviders stöds också, så att användarna kan logga in med befintliga autentiseringar från leverantörer som Microsoft, Google, Facebook eller Twitter.

### <a name="authorization"></a>Auktorisering
Efter autentiseringen måste tjänster auktorisera användar åtkomst eller bestämma vad en användare kan göra. Med den här processen kan en tjänst göra API: er tillgängliga för vissa autentiserade användare, men inte till alla. Auktorisering är rätvinkligt och oberoende av autentisering, vilket är en process för att fastställa vem en användare är. Autentisering kan skapa en eller flera identiteter för den aktuella användaren.

[ASP.net Core auktorisering](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) kan göras baserat på användarnas roller eller baserat på en anpassad princip som kan omfatta att inspektera anspråk eller andra heuristik.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Begränsa och skydda åtkomst med hjälp av en API-Gateway
Molnprogram behöver ofta en klientdelsgateway som enda åtkomstpunkt för ingång för användare, enheter och andra program. En [API-Gateway](/azure/architecture/microservices/gateway) är placerad mellan klienter och tjänster och är start punkten för alla tjänster som ditt program tillhandahåller. Den fungerar som en omvänd proxy och dirigerar begär Anden från klienter till tjänster. Det kan också utföra olika aktiviteter, till exempel autentisering och auktorisering, TLS-avslutning och hastighets begränsning. Om du inte distribuerar en gateway måste klienterna skicka begär Anden direkt till klient dels tjänster.

I Service Fabric kan en gateway vara en tillstånds lös tjänst, till exempel ett [ASP.net Core program](service-fabric-reliable-services-communication-aspnetcore.md)eller en annan tjänst som har utformats för trafik ingångar, till exempel [Traefik](https://docs.traefik.io/), [Event Hubs](../event-hubs/index.yml), [IoT Hub](../iot-hub/index.yml)eller [Azure API Management](../api-management/index.yml).

API Management integreras direkt med Service Fabric, så att du kan publicera API: er med en omfattande uppsättning regler för routning till Server delens Service Fabric tjänster.  Du kan skydda åtkomsten till backend-tjänster, förhindra DOS-attacker genom att använda begränsning eller verifiera API-nycklar, JWT-token, certifikat och andra autentiseringsuppgifter. Läs mer i [Service Fabric med Azure API Management-översikt](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Hantera programhemligheter
Hemligheter kan vara vilken känslig information som helst, till exempel lagrings anslutnings strängar, lösen ord eller andra värden som inte ska hanteras i oformaterad text. Den här artikeln använder Azure Key Vault för att hantera nycklar och hemligheter. Att *använda* hemligheter i ett program är dock Cloud Platform-oberoende för att tillåta att program distribueras till ett kluster som finns var som helst.

Det rekommenderade sättet att hantera tjänst konfigurations inställningar är via [tjänst konfigurations paket][config-package]. Konfigurations paket är versioner och kan uppdateras via hanterade löpande uppgraderingar med hälso verifiering och automatisk återställning. Detta föredras för global konfiguration eftersom det minskar risken för ett globalt avbrott i tjänsten. Krypterade hemligheter är inget undantag. Service Fabric har inbyggda funktioner för att kryptera och dekryptera värden i ett konfigurations paket Settings.xml fil med hjälp av certifikat kryptering.

Följande diagram illustrerar det grundläggande flödet för hemlig hantering i ett Service Fabric program:

![Översikt över hemliga hantering][overview]

Det finns fyra huvudsakliga steg i det här flödet:

1. Hämta ett certifikat för data kryptering.
2. Installera certifikatet i klustret.
3. Kryptera hemliga värden när du distribuerar ett program med certifikatet och mata in dem i en tjänsts Settings.xml konfigurations fil.
4. Läs krypterade värden från Settings.xml genom att dekryptera med samma certifikat för dekryptering. 

[Azure Key Vault][key-vault-get-started] används här som en säker lagrings plats för certifikat och som ett sätt att hämta certifikat som är installerade på Service Fabric kluster i Azure. Om du inte distribuerar till Azure behöver du inte använda Key Vault för att hantera hemligheter i Service Fabric program.

Ett exempel finns i [hantera program hemligheter](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Skydda värd miljön
Genom att använda Azure Service Fabric kan du skydda program som körs i klustret under olika användar konton. Service Fabric skyddar också resurserna som används av program vid tidpunkten för distributionen under användar konton, till exempel filer, kataloger och certifikat. Detta gör att program körs, även i en delad värd miljö, säkrare från varandra.

Applikations manifestet deklarerar de säkerhets objekt (användare och grupper) som krävs för att köra tjänsterna och säkra resurser.  Dessa säkerhets objekt refereras till i principer, till exempel körnings-som-slutpunkt-bindning, paket delning eller säkerhets åtkomst principer.  Principer tillämpas sedan på tjänst resurser i **service manifest import** -avsnittet i applikations manifestet.

När du deklarerar huvud konton kan du också definiera och skapa användar grupper så att en eller flera användare kan läggas till i varje grupp som ska hanteras tillsammans. Detta är användbart när det finns flera användare för olika tjänst start punkter och de måste ha vissa gemensamma behörigheter som är tillgängliga på grupp nivå.

Som standard körs Service Fabric-program under det konto som Fabric.exe processen körs under. Service Fabric ger också möjlighet att köra program under ett lokalt användar konto eller lokalt system konto, som anges i applikations manifestet. Mer information finns i [köra en tjänst som ett lokalt användar konto eller lokalt system konto](service-fabric-application-runas-security.md).  Du kan också [köra ett start skript för tjänsten som ett lokalt användar-eller system konto](service-fabric-run-script-at-service-startup.md).

När du kör Service Fabric i ett fristående Windows-kluster kan du köra en tjänst under [Active Directory domän konton](service-fabric-run-service-as-ad-user-or-group.md) eller [grupphanterade tjänst konton](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Säkra container
Service Fabric tillhandahåller en mekanism för tjänster i en behållare för att få åtkomst till ett certifikat som är installerat på noderna i ett Windows-eller Linux-kluster (version 5,7 eller senare). Detta PFX-certifikat kan användas för att autentisera programmet eller tjänsten eller säker kommunikation med andra tjänster. Mer information finns i [Importera ett certifikat till en behållare](service-fabric-securing-containers.md).

Dessutom stöder Service Fabric även gMSA (grupphanterade tjänst konton) för Windows-behållare. Mer information finns i [Konfigurera gMSA för Windows-behållare](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Säker tjänst kommunikation
I Service Fabric körs en tjänst någonstans i ett Service Fabric kluster, vanligt vis distribuerat över flera virtuella datorer. Service Fabric innehåller flera alternativ för att skydda tjänst kommunikationen.

Du kan aktivera HTTPS-slutpunkter i [ASP.net Core-eller Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) -webbtjänster.

Du kan upprätta en säker anslutning mellan omvänd proxy och tjänster och på så sätt aktivera en säker kanal från slut punkt till slut punkt. Det går bara att ansluta till säkra tjänster när omvänd proxy har kon figurer ATS för att lyssna på HTTPS. Information om hur du konfigurerar den omvända proxyn finns [i omvänd proxy i Azure Service Fabric](service-fabric-reverseproxy.md).  [Anslut till en säker tjänst](service-fabric-reverseproxy-configure-secure-communication.md) beskriver hur du upprättar en säker anslutning mellan omvänd proxy och tjänster.

Reliable Services Application Framework innehåller några inbyggda kommunikations stackar och verktyg som du kan använda för att förbättra säkerheten. Lär dig hur du kan förbättra säkerheten när du använder tjänstens fjärr kommunikation (i [C#](service-fabric-reliable-services-secure-communication.md) eller [Java](service-fabric-reliable-services-secure-communication-java.md)) eller med [WCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Kryptera program data i vila
Varje [nodtyp](service-fabric-cluster-nodetypes.md) i ett Service Fabric kluster som körs i Azure backas upp av en [skalnings uppsättning för virtuella datorer](../virtual-machine-scale-sets/overview.md). Genom att använda en Azure Resource Manager-mall, kan du ansluta datadiskar till skalningsuppsättningen som utgör Service Fabric-klustret.  Om dina tjänster sparar data till en ansluten data disk kan du [kryptera dessa data diskar](../virtual-machine-scale-sets/disk-encryption-powershell.md) för att skydda dina program data.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
* [Kör ett installations skript när tjänsten startas](service-fabric-run-script-at-service-startup.md)
* [Ange resurser i ett tjänst manifest](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)
* [Lär dig mer om kluster säkerhet](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png
