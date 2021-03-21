---
title: Vanliga frågor om Azure API Management | Microsoft Docs
description: Lär dig mer om svaren på vanliga frågor, mönster och metod tips i Azure API Management.
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
ms.openlocfilehash: eea3c8525d31a3ca551e9cbc7d21d7dde163b5cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94698010"
---
# <a name="azure-api-management-faqs"></a>Vanliga frågor och svar om Azure API Management
Få svar på vanliga frågor, mönster och metod tips för Azure API Management.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
* [Vad betyder det när en funktion är i för hands version?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Hur kan jag skydda anslutningen mellan API Management-gatewayen och mina backend-tjänster?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Hur gör jag för att kopiera min API Management-tjänstinstans till en ny instans?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Kan jag hantera min API Management-instans program mässigt?](#can-i-manage-my-api-management-instance-programmatically)
* [Hur gör jag för att lägga till en användare i gruppen Administratörer?](#how-do-i-add-a-user-to-the-administrators-group)
* [Varför är den princip som jag vill lägga till otillgänglig i princip redigeraren?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Hur gör jag för att konfigurera flera miljöer i ett enda API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Kan jag använda SOAP med API Management?](#can-i-use-soap-with-api-management)
* [Kan jag konfigurera en OAuth 2,0-Authorization-server med AD FS säkerhet?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Vilken routningsmetod API Management använda vid distributioner till flera geografiska platser?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Kan jag använda en Azure Resource Manager mall för att skapa en API Management tjänst instans?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Kan jag använda ett självsignerat TLS/SSL-certifikat för Server delen?](#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)
* [Varför får jag ett autentiseringsfel när jag försöker klona en GIT-lagringsplats?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Fungerar API Management med Azure-ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Varför kräver vi ett dedikerat undernät i Resource Manager-formatet virtuella nätverk när API Management distribueras till dem?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Vad är den minsta under näts storlek som krävs när du distribuerar API Management till ett virtuellt nätverk?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Kan jag flytta en API Management-tjänst från en prenumeration till en annan?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Finns det begränsningar för eller kända problem med att importera mitt API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Vad betyder det när en funktion är i för hands version?
När en funktion är i för hands version innebär det att vi aktivt söker efter feedback om hur funktionen fungerar. En funktion i förhands granskningen är funktionellt slutförd, men det är möjligt att vi gör en större ändring som svar på kundfeedback. Vi rekommenderar att du inte är beroende av en funktion som är i för hands version i din produktions miljö.

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Hur kan jag skydda anslutningen mellan API Management-gatewayen och mina backend-tjänster?
Du har flera alternativ för att skydda anslutningen mellan API Management Gateway och backend-tjänsterna. Du kan:

* Använd HTTP Basic-autentisering. Mer information finns i [Importera och publicera ditt första API](import-and-publish.md).
* Använd TLS-ömsesidig autentisering enligt beskrivningen i [så här skyddar du backend-tjänster med hjälp av autentisering av klient certifikat i Azure API Management](api-management-howto-mutual-certificates.md).
* Använd IP-filtrering på Server dels tjänsten. På alla nivåer av API Management med undantag av förbruknings nivån, är IP-adressen för gatewayen konstant, med några varningar som beskrivs i [artikeln om IP-dokumentation](api-management-howto-ip-addresses.md).
* Anslut API Management-instansen till en Azure-Virtual Network.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Hur gör jag för att kopiera min API Management-tjänstinstans till en ny instans?
Du har flera alternativ om du vill kopiera en API Management-instans till en ny instans. Du kan:

* Använd funktionen säkerhets kopiering och återställning i API Management. Mer information finns i [så här implementerar du haveri beredskap med hjälp av säkerhets kopiering och återställning av tjänsten i Azure API Management](api-management-howto-disaster-recovery-backup-restore.md).
* Skapa en egen säkerhets kopierings-och återställnings funktion med hjälp av [API Management REST API](/rest/api/apimanagement/). Använd REST API för att spara och återställa entiteter från den tjänst instans du vill använda.
* Hämta tjänst konfigurationen med hjälp av Git och ladda sedan upp den till en ny instans. Mer information finns i [så här sparar och konfigurerar du API Management tjänst konfigurationen med hjälp av Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Kan jag hantera min API Management-instans program mässigt?
Ja, du kan hantera API Management program mässigt genom att använda:

* [API Management REST API](/rest/api/apimanagement/).
* [Microsoft Azure API Management Service Management Library SDK](https://aka.ms/apimsdk).
* [Distributions-](/powershell/module/wds) och [tjänst hanterings](/powershell/azure/servicemanagement/overview) PowerShell-cmdletar.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Hur gör jag för att lägga till en användare i gruppen Administratörer?
Administratörs grupper är en system grupp som inte kan ändras. Administratörer för Azure-prenumeration är medlemmar i den här gruppen. Du kan inte lägga till en användare i den här gruppen. Mer information finns i [skapa och använda grupper för att hantera utvecklares konton i Azure API Management](./api-management-howto-create-groups.md) .

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Varför är den princip som jag vill lägga till otillgänglig i princip redigeraren?
Om den princip som du vill lägga till visas nedtonad eller skuggad i princip redigeraren, kontrollerar du att du har rätt omfattning för principen. Varje princips ATS är utformad för att användas i avsnitt med vissa områden och principer. Om du vill granska princip avsnitten och omfattningarna för en princip, se principens användnings avsnitt i [API Management principer](./api-management-policies.md).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Hur gör jag för att konfigurera flera miljöer i ett enda API?
Om du vill konfigurera flera miljöer, till exempel en test miljö och en produktions miljö, i ett enda API, har du två alternativ. Du kan:

* Var värd för olika API: er på samma klient.
* Var värd för samma API: er på olika klienter.

### <a name="can-i-use-soap-with-api-management"></a>Kan jag använda SOAP med API Management?
Stöd för [SOAP-vidarekoppling](https://azure.microsoft.com/blog/soap-pass-through/) är nu tillgängligt. Administratörer kan importera WSDL för sin SOAP-tjänst och Azure API Management skapar en SOAP-klient. Dokumentation om utvecklings portalen, test konsolen, principer och analyser är alla tillgängliga för SOAP-tjänster.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Kan jag konfigurera en OAuth 2,0-Authorization-server med AD FS säkerhet?
Information om hur du konfigurerar en OAuth 2,0-Authorization-server med Active Directory Federation Services (AD FS) (AD FS) säkerhet finns [i använda ADFS i API Management](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Vilken routningsmetod API Management använda vid distributioner till flera geografiska platser?
API Management använder [routningsmetoden för prestanda trafik](../traffic-manager/traffic-manager-routing-methods.md#performance) i distributioner till flera geografiska platser. Inkommande trafik dirigeras till närmaste API-Gateway. Om en region försätts i offlineläge dirigeras inkommande trafik automatiskt till nästa närmaste Gateway. Lär dig mer om routningsmetoder i [Traffic Manager metoder för routning](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Kan jag använda en Azure Resource Manager mall för att skapa en API Management tjänst instans?
Ja. Se snabb starts mallar för [Azure API Management Service](https://aka.ms/apimtemplate) .

### <a name="can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end"></a>Kan jag använda ett självsignerat TLS/SSL-certifikat för Server delen?
Ja. Detta kan göras via PowerShell eller genom att skicka direkt till API: et. Detta inaktiverar verifiering av certifikat kedjan och gör att du kan använda självsignerade eller privat signerade certifikat när du kommunicerar från API Management till Server dels tjänsterna.

#### <a name="powershell-method"></a>PowerShell-metod ####
Använd [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) (för ny server del) eller [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (för befintlig server del) PowerShell-cmdletar och ange `-SkipCertificateChainValidation` parametern till `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Uppdaterings metod för direkt-API ####
1. Skapa en [Server dels](/rest/api/apimanagement/) enhet med hjälp av API Management.     
2. Ange egenskapen **skipCertificateChainValidation** till **True**.     
3. Om du inte längre vill tillåta självsignerade certifikat, ta bort backend-entiteten eller ange egenskapen **skipCertificateChainValidation** till **false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Varför visas ett autentiseringsfel när jag försöker klona en Git-databas?
Om du använder git Credential Manager, eller om du försöker klona en git-lagringsplats med hjälp av Visual Studio, kan du stöta på ett känt problem i dialog rutan Windows-autentiseringsuppgifter. Dialog rutan begränsar lösen ordets längd till 127 tecken och den trunkerar det Microsoft-genererade lösen ordet. Vi arbetar med att förkorta lösen ordet. För närvarande kan du använda Git bash för att klona git-lagringsplatsen.

### <a name="does-api-management-work-with-azure-expressroute"></a>Fungerar API Management med Azure-ExpressRoute?
Ja. API Management fungerar med Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Varför kräver vi ett dedikerat undernät i Resource Manager-formatet virtuella nätverk när API Management distribueras till dem?
Det dedikerade under näts kravet för API Management kommer från faktumet, att det bygger på den klassiska distributions modellen (PAAS v1 Layer). Även om vi kan distribuera till ett Resource Manager VNET (v2-lager), finns det konsekvenser för det. Den klassiska distributions modellen i Azure är inte nära kopplad till Resource Manager-modellen och om du skapar en resurs i v2-skiktet vet inte v1-lagret om det och problem kan inträffa, till exempel API Management försöker använda en IP-adress som redan har tilldelats till ett nätverkskort (byggd på v2).
Om du vill veta mer om skillnaderna mellan klassiska modeller och Resource Manager-modeller i Azure kan du se [skillnaden i distributions modeller](../azure-resource-manager/management/deployment-models.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Vad är den minsta under näts storlek som krävs när du distribuerar API Management till ett virtuellt nätverk?
Den minsta under näts storlek som krävs för att distribuera API Management är [/29](../virtual-network/virtual-networks-faq.md#configuration), vilket är den minsta under näts storlek som stöds av Azure.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Kan jag flytta en API Management-tjänst från en prenumeration till en annan?
Ja. Mer information finns i [Flytta resurser till en ny resurs grupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Finns det begränsningar för eller kända problem med att importera mitt API?
[Kända problem och begränsningar](api-management-api-import-restrictions.md) för Open API (Swagger), WSDL-och WADL-format.
