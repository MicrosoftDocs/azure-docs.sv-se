---
title: Vanliga frågor och | om Azure API Management Microsoft Docs
description: Lär dig svaren på vanliga frågor och svar, mönster och metodtips i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1b6a138317d0cc2e10e893d1969f9d5452064d8f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813748"
---
# <a name="azure-api-management-faqs"></a>Vanliga frågor API Management Azure-tjänster
Få svar på vanliga frågor, mönster och metodtips för Azure API Management.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
* [Vad betyder det när en funktion är i förhandsversion?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Hur kan jag skydda anslutningen mellan API Management-gatewayen och mina backend-tjänster?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Hur gör jag för att kopiera min API Management-tjänstinstans till en ny instans?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Kan jag hantera min API Management-instans programmatiskt?](#can-i-manage-my-api-management-instance-programmatically)
* [Hur gör jag för att lägga till en användare i gruppen Administratörer?](#how-do-i-add-a-user-to-the-administrators-group)
* [Varför är den princip som jag vill lägga till inte tillgänglig i principredigeraren?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Hur gör jag för att konfigurera flera miljöer i ett enda API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Kan jag använda SOAP med API Management?](#can-i-use-soap-with-api-management)
* [Kan jag konfigurera en OAuth 2.0-auktoriseringsserver med AD FS säkerhet?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Vilken routningsmetod API Management i distributioner till flera geografiska platser?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Kan jag använda en Azure Resource Manager mall för att skapa en API Management-tjänstinstans?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Kan jag använda ett själv signerat TLS/SSL-certifikat för en server?](#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)
* [Varför får jag ett autentiseringsfel när jag försöker klona en GIT-lagringsplats?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Fungerar API Management med Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Varför kräver vi ett dedikerat undernät Resource Manager virtuella nätverk i API Management distribueras till dem?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Vilken är den minsta undernätsstorlek som krävs när du distribuerar API Management till ett VNET?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Kan jag flytta en API Management-tjänst från en prenumeration till en annan?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Finns det begränsningar eller kända problem med att importera mitt API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Vad betyder det när en funktion är i förhandsversion?
När en funktion är i förhandsversion innebär det att vi aktivt vill ha feedback om hur funktionen fungerar för dig. En funktion i förhandsversionen är funktionellt komplett, men det är möjligt att vi gör en stor förändring som svar på kundfeedback. Vi rekommenderar att du inte är beroende av en funktion som är i förhandsversion i produktionsmiljön.

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Hur kan jag skydda anslutningen mellan API Management-gatewayen och mina backend-tjänster?
Du har flera alternativ för att skydda anslutningen mellan API Management gateway och dina backend-tjänster. Du kan:

* Använd grundläggande HTTP-autentisering. Mer information finns i Importera [och publicera ditt första API.](import-and-publish.md)
* Använd ömsesidig TLS-autentisering enligt beskrivningen i Skydda servertjänster med hjälp [av klientcertifikatautentisering i Azure API Management](api-management-howto-mutual-certificates.md).
* Använd IP-filtrering på din backend-tjänst. I alla nivåer API Management med undantag för förbrukningsnivån förblir gatewayens IP-adress konstant, med några varningar som beskrivs i [ARTIKELN OM IP-dokumentation.](api-management-howto-ip-addresses.md)
* Anslut din API Management-instans till en Azure-Virtual Network.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Hur gör jag för att kopiera min API Management-tjänstinstans till en ny instans?
Du har flera alternativ om du vill kopiera en API Management instans till en ny instans. Du kan:

* Använd funktionen säkerhetskopiering och återställning i API Management. Mer information finns i Så [här implementerar du haveriberedskap med hjälp av säkerhetskopiering och återställning av tjänster i Azure API Management](api-management-howto-disaster-recovery-backup-restore.md).
* Skapa din egen funktion för säkerhetskopiering och återställning med hjälp [av API Management REST API](/rest/api/apimanagement/). Använd den REST API att spara och återställa entiteterna från den tjänstinstans som du vill använda.
* Ladda ned tjänstkonfigurationen med hjälp av Git och ladda sedan upp den till en ny instans. Mer information finns i Så [här sparar och konfigurerar du din API Management tjänstkonfiguration med hjälp av Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Kan jag hantera min API Management-instans programmatiskt?
Ja, du kan hantera API Management programmässigt med hjälp av:

* Den [API Management REST API](/rest/api/apimanagement/).
* Den [Microsoft Azure APIManagement Service Management Library SDK](https://aka.ms/apimsdk).
* [PowerShell-cmdlets](/powershell/module/wds) för [tjänstdistribution](/powershell/azure/servicemanagement/overview) och tjänsthantering.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Hur gör jag för att lägga till en användare i gruppen Administratörer?
Administratörsgrupper är en oföränderlig systemgrupp. Azure-prenumerationsadministratörer är medlemmar i den här gruppen. Du kan inte lägga till en användare i den här gruppen. Mer [information finns i Skapa och använda grupper för att hantera utvecklarkonton i Azure API Management](./api-management-howto-create-groups.md) mer information.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Varför är den princip som jag vill lägga till inte tillgänglig i principredigeraren?
Om den princip som du vill lägga till visas nedtonad eller skuggad i principredigeraren kontrollerar du att du har rätt omfång för principen. Varje principutdrag är utformat för att du ska kunna använda i specifika omfång och principavsnitt. Om du vill granska principavsnitten och omfången för en princip går du till avsnittet Användning för principen i [API Management principer](./api-management-policies.md).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Hur gör jag för att konfigurera flera miljöer i ett enda API?
Om du vill konfigurera flera miljöer, till exempel en testmiljö och en produktionsmiljö, har du två alternativ i ett enda API. Du kan:

* Ha olika API:er i samma klientorganisation.
* Vara värd för samma API:er på olika klienter.

### <a name="can-i-use-soap-with-api-management"></a>Kan jag använda SOAP med API Management?
[SOAP-direktsupport](https://azure.microsoft.com/blog/soap-pass-through/) är nu tillgängligt. Administratörer kan importera WSDL för soap-tjänsten, så skapar Azure API Management SOAP-frontend. Utvecklarportalens dokumentation, testkonsol, principer och analys är alla tillgängliga för SOAP-tjänster.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Kan jag konfigurera en OAuth 2.0-auktoriseringsserver med AD FS säkerhet?
Information om hur du konfigurerar en OAuth 2.0-auktoriseringsserver med Active Directory Federation Services (AD FS)-säkerhet (AD FS) finns i Använda [ADFS i API Management](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Vilken routningsmetod API Management i distributioner till flera geografiska platser?
API Management [trafikroutningsmetoden för prestanda](../traffic-manager/traffic-manager-routing-methods.md#performance) i distributioner till flera geografiska platser. Inkommande trafik dirigeras till den närmaste API-gatewayen. Om en region går offline dirigeras inkommande trafik automatiskt till nästa närmaste gateway. Läs mer om routningsmetoder i [Traffic Manager routningsmetoder](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Kan jag använda en Azure Resource Manager för att skapa en API Management-tjänstinstans?
Ja. Se [snabbstartsmallarna för Azure API Management Service.](https://aka.ms/apimtemplate)

### <a name="can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end"></a>Kan jag använda ett själv signerat TLS/SSL-certifikat för en server?
Ja. Detta kan göras via PowerShell eller genom att skicka direkt till API:et. Detta inaktiverar verifiering av certifikatkedjan och gör att du kan använda själv signerade eller privat signerade certifikat vid kommunikation från API Management till servertjänster.

#### <a name="powershell-method"></a>PowerShell-metod ####
Använd [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) PowerShell-cmdletarna (för ny backend) eller (för befintlig backend) och ange `-SkipCertificateChainValidation` parametern till `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Metod för direkt API-uppdatering ####
1. Skapa en [entitet i backend](/rest/api/apimanagement/) med hjälp API Management.     
2. Ange egenskapen **skipCertificateChainValidation** till **true**.     
3. Om du inte längre vill tillåta själv signerade certifikat tar du bort entiteten Backend eller anger **egenskapen skipCertificateChainValidation** till **false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Varför visas ett autentiseringsfel när jag försöker klona en Git-databas?
Om du använder Git Autentiseringshanteraren, eller om du försöker klona en Git-lagringsplats med hjälp av Visual Studio, kan du få ett känt problem med dialogrutan Windows-autentiseringsuppgifter. Dialogrutan begränsar lösenordslängden till 127 tecken och trunkerar det Microsoft-genererade lösenordet. Vi arbetar på att förkorta lösenordet. För tillfället använder du Git Bash för att klona Din Git-lagringsplats.

### <a name="does-api-management-work-with-azure-expressroute"></a>Fungerar API Management med Azure ExpressRoute?
Ja. API Management fungerar med Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Varför kräver vi ett dedikerat undernät Resource Manager virtuella nätverk i API Management distribueras till dem?
Kravet på dedikerat undernät för API Management kommer från det faktum att det bygger på den klassiska distributionsmodellen (PAAS V1-lager). Även om vi kan distribuera till Resource Manager VNET (V2-lager) finns det konsekvenser för det. Den klassiska distributionsmodellen i Azure är inte nära kopplad till Resource Manager-modellen. Om du skapar en resurs i V2-lagret känner inte V1-skiktet till det och problem kan uppstå, till exempel API Management försöker använda en IP-adress som redan har allokerats till ett nätverkskort (byggt på V2).
Mer information om skillnaden mellan klassiska och Resource Manager i Azure finns i [skillnaden i distributionsmodeller.](../azure-resource-manager/management/deployment-models.md)

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Vilken är den minsta undernätsstorlek som krävs när du distribuerar API Management till ett VNET?
Den minsta undernätsstorlek som krävs för API Management är [/29](../virtual-network/virtual-networks-faq.md#configuration), vilket är den minsta undernätsstorlek som Azure stöder.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Kan jag flytta en API Management-tjänst från en prenumeration till en annan?
Ja. Mer information finns i Flytta [resurser till en ny resursgrupp eller prenumeration.](../azure-resource-manager/management/move-resource-group-and-subscription.md)

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Finns det begränsningar eller kända problem med att importera mitt API?
[Kända problem och begränsningar för](api-management-api-import-restrictions.md) Open API(Swagger), WSDL- och WADL-format.
