---
title: Säkerhet
description: Lär dig mer om hur App Service skyddar din app och hur du kan låsa din app ytterligare.
keywords: Azure App Service, webbapp, mobilapp, API-app, Function-app, säkerhet, säker, säker, efterlevnad, kompatibel, certifikat, certifikat, https, FTPS, TLS, förtroende, kryptering, kryptera, krypterad, IP-begränsning, autentisering, auktorisering, autentisering, autentisering, MSI, hanterad tjänst identitet, hanterad identitet, hemligheter, hemlighet, korrigering, korrigering, korrigeringar, version, isolering, nätverks isolering
ms.topic: article
ms.date: 08/24/2018
ms.custom: seodec18
ms.openlocfilehash: 61bffcfa8b98ed666e450c344023258b752e4880
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736113"
---
# <a name="security-in-azure-app-service"></a>Säkerhet i Azure App Service

Den här artikeln visar hur [Azure App Service](overview.md) skyddar din webbapp, Server del för mobilapp, API-app och Function- [appen](../azure-functions/index.yml). Det visar också hur du kan skydda din app ytterligare med de inbyggda App Service funktionerna.

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

I följande avsnitt visas hur du skyddar din App Service-app ytterligare från hot.

## <a name="https-and-certificates"></a>HTTPS och certifikat

Med App Service kan du skydda dina appar med [https](https://wikipedia.org/wiki/HTTPS). När din app skapas, är dess standard domän namn ( \<app_name> . azurewebsites.net) redan tillgängligt med https. Om du [konfigurerar en anpassad domän för din app](app-service-web-tutorial-custom-domain.md)bör du även [skydda den med ett TLS/SSL-certifikat](configure-ssl-bindings.md) så att klient webbläsare kan göra säkra HTTPS-anslutningar till din anpassade domän. Det finns flera typer av certifikat som stöds av App Service:

- App Service – kostnadsfri hanterat certifikat
- App Service certifikat
- Certifikat från tredje part
- Certifikat importerat från Azure Key Vault

Mer information finns i [lägga till ett TLS/SSL-certifikat i Azure App Service](configure-ssl-certificate.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Osäkra protokoll (HTTP, TLS 1,0, FTP)

För att skydda din app mot alla okrypterade anslutningar (HTTP), App Service tillhandahålla konfiguration med ett klick för att använda HTTPS. Oskyddade förfrågningar är inaktiverade innan de når program koden. Mer information finns i [FRAMTVINGA https](configure-ssl-bindings.md#enforce-https).

[TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1,0 anses inte längre vara säkert av bransch standarder, t. ex. [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Med App Service kan du inaktivera inaktuella protokoll genom att [FRAMTVINGA TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions).

App Service stöder både FTP-och FTPS för att distribuera dina filer. FTPS bör dock användas i stället för FTP, om det är möjligt. När ett eller båda av dessa protokoll inte används bör du [inaktivera dem](deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Statiska IP-begränsningar

Som standard godkänner din App Service-app begär Anden från alla IP-adresser från Internet, men du kan begränsa åtkomsten till en liten delmängd av IP-adresser. App Service i Windows kan du definiera en lista med IP-adresser som får åtkomst till din app. Listan över tillåtna kan innehålla enskilda IP-adresser eller ett intervall med IP-adresser som definieras av en under nät mask. Mer information finns i [Azure App Service statiska IP-begränsningar](app-service-ip-restrictions.md).

För App Service i Windows kan du också begränsa IP-adresser dynamiskt genom att konfigurera _web.config_. Mer information finns i [dynamisk IP-säkerhet \<dynamicIpSecurity>](/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Klientautentisering och-auktorisering

Azure App Service tillhandahåller autentisering med turn-och auktorisering av användare eller klient program. När den är aktive rad kan den logga in användare och klient program med lite eller ingen program kod. Du kan implementera en egen lösning för autentisering och auktorisering, eller låta App Service hantera den åt dig i stället. Modulen för autentisering och auktorisering hanterar webb förfrågningar innan de skickas till program koden och nekas obehöriga begär Anden innan de når din kod.

App Service autentisering och auktorisering stöder flera autentiseringsproviders, inklusive Azure Active Directory, Microsoft-konton, Facebook, Google och Twitter. Mer information finns i [Autentisering och auktorisering i Azure App Service](overview-authentication-authorization.md).

## <a name="service-to-service-authentication"></a>Tjänst-till-tjänst-autentisering

Vid autentisering mot en backend-tjänst tillhandahåller App Service två olika metoder beroende på dina behov:

- **Tjänst identitet** – logga in på fjär resursen med appens identitet. Med App Service kan du enkelt skapa en [hanterad identitet](overview-managed-identity.md), som du kan använda för att autentisera med andra tjänster, till exempel [Azure SQL Database](/azure/sql-database/) eller [Azure Key Vault](../key-vault/index.yml). En fullständig själv studie kurs om den här metoden finns i [säker Azure SQL Database anslutning från App Service med hjälp av en hanterad identitet](app-service-web-tutorial-connect-msi.md).
- **På uppdrag av (OBO)** – gör delegerad åtkomst till fjär resurser åt användaren. Med Azure Active Directory som autentiseringsprovider kan App Service-appen utföra delegerad inloggning till en fjärrtjänst, till exempel [Microsoft Graph API](../active-directory/develop/microsoft-graph-intro.md) eller en fjärr-API-App i App Service. En fullständig själv studie kurs om den här metoden finns i [autentisera och auktorisera användare från slut punkt till slut punkt i Azure App Service](tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Anslutning till fjär resurser

Det finns tre typer av fjär resurser som din app kan behöva åtkomst till: 

- [Azure-resurser](#azure-resources)
- [Resurser i en Azure-Virtual Network](#resources-inside-an-azure-virtual-network)
- [Lokala resurser](#on-premises-resources)

I vart och ett av dessa fall är App Service ett sätt för dig att skapa säkra anslutningar, men du bör fortfarande se rekommenderade säkerhets metoder. Använd till exempel alltid krypterade anslutningar även om Server dels resursen tillåter okrypterade anslutningar. Kontrol lera också att Azure-tjänsten för Server delen tillåter den lägsta uppsättningen IP-adresser. Du kan hitta de utgående IP-adresserna för din app på [inkommande och utgående IP-adresser i Azure App Service](overview-inbound-outbound-ips.md).

### <a name="azure-resources"></a>Azure-resurser

När appen ansluter till Azure-resurser, till exempel [SQL Database](https://azure.microsoft.com/services/sql-database/) och [Azure Storage](../storage/index.yml), stannar anslutningen i Azure och korsar inte några nätverks gränser. Anslutningen går dock igenom det delade nätverket i Azure, så kontrol lera alltid att anslutningen är krypterad. 

Om din app finns i en [app Services miljö](environment/intro.md)bör du [ansluta till Azure-tjänster som stöds med hjälp av Virtual Network tjänst slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Resurser i en Azure-Virtual Network

Din app kan komma åt resurser i en [Azure-Virtual Network](../virtual-network/index.yml) via Virtual Network- [integrering](web-sites-integrate-with-vnet.md). Integrationen upprättas med en Virtual Network med hjälp av en punkt-till-plats-VPN. Appen kan sedan komma åt resurserna i Virtual Network med hjälp av sina privata IP-adresser. Punkt-till-plats-anslutningen går dock fortfarande igenom de delade nätverken i Azure. 

Skapa din app i en [App Service-miljö](environment/intro.md)för att isolera resurs anslutningen helt från de delade nätverken i Azure. Eftersom en App Services miljö alltid distribueras till en dedikerad Virtual Network, är anslutningen mellan appen och resurserna i Virtual Network helt isolerad. Andra aspekter av nätverks säkerheten i en App Service-miljö finns i [nätverks isolering](#network-isolation).

### <a name="on-premises-resources"></a>Lokala resurser

Du kan få säker åtkomst till lokala resurser, till exempel databaser, på tre sätt: 

- [Hybrid anslutningar](app-service-hybrid-connections.md) – upprättar en punkt-till-punkt-anslutning till fjär resursen via en TCP-tunnel. TCP-tunneln upprättas med hjälp av TLS 1,2 med nycklar för signatur för delad åtkomst (SAS).
- [Virtual Network integrering](web-sites-integrate-with-vnet.md) med plats-till-plats-VPN – enligt beskrivningen i [resurser i en Azure-Virtual Network](#resources-inside-an-azure-virtual-network), men Virtual Network kan anslutas till ditt lokala nätverk via en [plats-till-plats-VPN](../vpn-gateway/tutorial-site-to-site-portal.md). I den här nätverk sto pol Ogin kan din app ansluta till lokala resurser som andra resurser i Virtual Network.
- [App Service miljö](environment/intro.md) med plats-till-plats-VPN – enligt beskrivningen i [resurser i en Azure-Virtual Network](#resources-inside-an-azure-virtual-network), men Virtual Network kan anslutas till ditt lokala nätverk via en [plats-till-plats-VPN](../vpn-gateway/tutorial-site-to-site-portal.md). I den här nätverk sto pol Ogin kan din app ansluta till lokala resurser som andra resurser i Virtual Network.

## <a name="application-secrets"></a>Program hemligheter

Lagra inte program hemligheter, t. ex. autentiseringsuppgifter för databaser, API-token och privata nycklar i koden eller konfigurationsfilerna. Den vanligaste metoden är att få åtkomst till dem som [miljövariabler](https://wikipedia.org/wiki/Environment_variable) som använder standard mönstret på det språk som du väljer. I App Service kan du definiera miljövariabler genom att ange [appinställningar](configure-common.md#configure-app-settings) (och särskilt för .NET-program, [anslutnings strängar](configure-common.md#configure-connection-strings)). App-inställningar och anslutnings strängar lagras krypterade i Azure och de dekrypteras bara innan de matas in i appens process minne när appen startas. Krypterings nycklarna roteras regelbundet.

Du kan också integrera din App Service-app med [Azure Key Vault](../key-vault/index.yml) för hantering av avancerade hemligheter. Genom att [komma åt Key Vault med en hanterad identitet](../key-vault/general/tutorial-net-create-vault-azure-web-app.md)kan din app service-app på ett säkert sätt komma åt de hemligheter du behöver.

## <a name="network-isolation"></a>Nätverksisolering

Förutom den **isolerade** pris nivån kör alla nivåer dina appar i den delade nätverks infrastrukturen i App Service. Till exempel delas offentliga IP-adresser och klient dels belastnings utjämning med andra klienter. Den **isolerade** nivån ger dig fullständig nätverks isolering genom att köra apparna i en dedikerad [app Services miljö](environment/intro.md). En App Service-miljö körs i din egen instans av [Azure Virtual Network](../virtual-network/index.yml). Det gör att du kan: 

- Hantera dina appar via en dedikerad offentlig slut punkt, med dedikerade klient delar.
- Hantera internt program med hjälp av en intern belastningsutjämnare (ILB), som endast tillåter åtkomst från i Azure-Virtual Network. ILB har en IP-adress från ditt privata undernät, vilket ger en fullständig isolering av dina appar från Internet.
- [Använd en ILB bakom en brand vägg för webbaserade program (WAF)](environment/integrate-with-application-gateway.md). WAF erbjuder skydd på företags nivå för dina offentliga program, till exempel DDoS-skydd, URI-filtrering och SQL-injektering.

Mer information finns i [Introduktion till Azure App Service miljöer](environment/intro.md).