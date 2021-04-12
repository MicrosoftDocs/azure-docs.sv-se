---
title: Migrera Azure Cloud Services (klassisk) till Azure Cloud Services (utökad support)
description: Översikt över migrering från Cloud Services (klassisk) till moln tjänsten (utökad support)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 96315899f80d0bd02ac3d2108b7cd76876025218
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286999"
---
# <a name="migrate-azure-cloud-services-classic-to-azure-cloud-services-extended-support"></a>Migrera Azure Cloud Services (klassisk) till Azure Cloud Services (utökad support)

Den här artikeln innehåller en översikt över Migreringsverktyg för plattformar som stöds och hur du använder det för att migrera [azure Cloud Services (klassisk)](../cloud-services/cloud-services-choose-me.md) till [Azure Cloud Services (utökad support)](overview.md).

Migreringsverktyget använder samma API: er och har samma erfarenhet som den [virtuella datorn (klassisk) migrering](https://docs.microsoft.com/azure/virtual-machines/migration-classic-resource-manager-overview). 

> [!IMPORTANT]
> Migrering från Cloud Services (klassisk) till Cloud Services (utökad support) med migreringsverktyget är för närvarande en offentlig för hands version. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se följande resurser om du behöver hjälp med migreringen: 

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html): Microsoft och community-support för migrering.
- [Stöd för Azure migration](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%22pesId%22:%22e79dcabe-5f77-3326-2112-74487e1e5f78%22,%22supportTopicId%22:%22fca528d2-48bd-7c9f-5806-ce5d5b1d226f%22%7D): dedikerat support team för teknisk hjälp under migreringen. Kunder utan teknisk support kan använda [kostnads fri support](https://aka.ms/cs-migration-errors) som är specifikt för migreringen.
- Om företaget/organisationen har samarbetat med Microsoft eller fungerar med Microsoft-representanter som moln lösnings arkitekter eller tekniska konto hanterare kan du kontakta dem för att få mer resurser för migreringen.
- Slutför [den här undersökningen](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR--AgudUMwJKgRGMO84rHQtUQzZYNklWUk4xOTFXVFBPOFdGOE85RUIwVC4u) för att ge feedback eller generera problem med produkt teamet för Cloud Services (utökad support). 

## <a name="migration-benefits"></a>Fördelar med migrering
Den plattform som stöds migreringen ger följande viktiga fördelar:
- Migreringen är helt dirigerad av plattformen, och flyttar hela distributionen och de associerade resurserna till Azure Resource Manager.
- Ingen migrering av nedtid.
- Enkel och snabb migrering jämfört med andra sökvägar genom att minimera manuella uppgifter. 
- Behåller Cloud Services IP-adress och DNS-etikett som en del av migreringen. 

För andra förmåner och varför du bör migrera, se [Cloud Services (utökad support)](overview.md) och [Azure Resource Manager](../azure-resource-manager/management/overview.md). 

## <a name="setup-access-for-migration"></a>Konfigurera åtkomst för migrering

För att utföra den här migreringen måste du läggas till som en medadministratör för prenumerationen och registrera de leverantörer som behövs. 

1. Logga in på Azure-portalen.
3. På menyn hubb väljer du prenumeration. Om du inte ser det väljer du alla tjänster.
3. Hitta rätt prenumerations post och titta sedan i fältet min roll. För en-administratör ska värdet vara konto administratör. Om du inte kan lägga till en medadministratör kan du kontakta en tjänst administratör eller delad administratör för prenumerationen för att få till gång till dig själv.

4. Registrera din prenumeration för Microsoft. ClassicInfrastructureMigrate-namnrymden med hjälp av [portalen](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) eller [CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate 
    ```
 
5. Registrera din prenumeration för för hands versionen av Cloud Services migrering med hjälp av [portalen](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) eller [CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)

    ```powershell
    Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

6. Kontrol lera status för registreringen. Registreringen kan ta några minuter att slutföra. 

    ```powershell
    Get-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

## <a name="how-is-migration-for-cloud-services-classic-different-from-virtual-machines-classic"></a>Hur skiljer sig migreringen för Cloud Services (klassisk) från Virtual Machines (klassisk)?
Azure Service Manager har stöd för två olika beräknings produkter, [Azure Virtual Machines (klassisk)](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/tutorial-classic) och [Azure-Cloud Services (klassisk)](../cloud-services/cloud-services-choose-me.md) eller webb-och arbets roller. De två produkterna skiljer sig åt beroende på vilken distributions typ som ligger i moln tjänsten. Azure Cloud Services (klassisk) använder moln tjänsten som innehåller distributioner med webb-och arbets roller. Azure Virtual Machines (klassisk) använder en moln tjänst som innehåller distributioner med virtuella IaaS-datorer.

Listan över scenarier som stöds skiljer sig mellan Cloud Services (klassisk) och Virtual Machines (klassisk) på grund av skillnader i distributions typerna.

## <a name="migration-steps"></a>Migreringsanvisningar
 
Kunder kan migrera sina Cloud Services (klassiska) distributioner med samma fyra åtgärder som används för att migrera Virtual Machines (klassisk). 

1. **Verifiera migrering** – validerar att migreringen inte kommer att förhindras av vanliga scenarier som inte stöds.
2. **Förbereda migrering** – duplicerar resursens metadata i Azure Resource Manager. Alla resurser är låsta för att skapa/uppdatera/ta bort för att säkerställa att resursens metadata synkroniseras mellan Azure Serverhanteraren och Azure Resource Manager. Alla Läs åtgärder fungerar med både Cloud Services (klassiska) och Cloud Services (utökade support) API: er.
3. **Avbryt migrering** – tar bort resursens metadata från Azure Resource Manager. Låser upp alla resurser för åtgärder för att skapa/uppdatera/ta bort.
4. **Genomför migrering** – tar bort resursens metadata från Azure Service Manager. Låser upp resursen för åtgärder för att skapa/uppdatera/ta bort. Abort tillåts inte längre när commit har gjorts.

>[!NOTE]
> Förbereda, avbryta och bekräfta är idempotenta och därför bör ett nytt försök lösa problemet om det Miss lyckas.

:::image type="content" source="media/in-place-migration-1.png" alt-text="Bilden visar diagram över steg som är kopplade till migreringen.":::

Mer information finns i [Översikt över migrering av plattformar som stöds av IaaS-resurser från klassisk till Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)

## <a name="supported-resources-and-features-available-for-migration-associated-with-cloud-services-classic"></a>Resurser som stöds och funktioner som är tillgängliga för migrering som är associerade med Cloud Services (klassisk)
-   Lagringskonton
-   Virtuella nätverk
-   Nätverkssäkerhetsgrupper
-   Reserverade offentliga IP-adresser
-   Slut punkts Access Control listor
-   Användardefinierade vägar
-   Intern lastbalanserare
-   Migrera certifikat till Key Vault
-   Plugin-program och tillägg (XML och JSON-baserad)
-   Åtgärder vid start/vid stopp
-   Distributioner med accelererat nätverk
-   Distributioner med hjälp av en eller flera roller
-   Basic Load Balancer
-   Ininformation, instans Indatatyp, interna slut punkter
-   Dynamiska offentliga IP-adresser
-   DNS-namn 
-   Regler för nätverks trafik
-   Hypernet virtuellt nätverk

## <a name="supported-configurations--migration-scenarios"></a>Konfigurationer/migrations scenarier som stöds
Detta är de viktigaste scenarierna med kombinationer av resurser, funktioner och Cloud Services. Den här listan är inte fullständig.

| Tjänst | Konfiguration | Kommentarer | 
|---|---|---|
| [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet) | Virtuella nätverk som innehåller Azure Active Directory domän tjänster. | Virtuella nätverk som innehåller både moln tjänst distribution och Azure AD Domain Services stöds. Kunden måste först migrera Azure AD Domain Services separat och sedan migrera det virtuella nätverket endast till vänster med moln tjänst distributionen |
| Molntjänst | Moln tjänst med en distribution endast på en enda plats. | Cloud Services som innehåller distribution av en produktions plats eller mellanlagringsplats kan migreras |
| Molntjänst | Distribution inte i ett offentligt synligt virtuellt nätverk (standard distribution av virtuella nätverk) | En moln tjänst kan vara i ett offentligt synligt virtuellt nätverk, i ett dolt virtuellt nätverk eller inte i något virtuellt nätverk.  Cloud Services i ett dolt virtuellt nätverk och offentligt synliga virtuella nätverk stöds för migrering. Kunden kan använda validerings-API: et för att se om en distribution finns i ett virtuellt standard nätverk eller inte, och därmed avgöra om den kan migreras. |
|Molntjänst | XML-tillägg (BGInfo, Visual Studio-felsökning, webb distribution och fjärrfelsökning). | Alla XML-tillägg stöds för migrering 
| Virtual Network | Virtuellt nätverk som innehåller flera Cloud Services. | Virtuella nätverk innehåller flera moln tjänster som stöds för migrering. Det virtuella nätverket och alla Cloud Services i det kommer att migreras tillsammans till Azure Resource Manager. |
| Virtual Network | Migrering av virtuella nätverk som skapats via portalen (kräver användning av "grupp resurs-grupp-namn VNet-namn" i. cscfg-fil)  | Som en del av migreringen kommer det virtuella nätverks namnet i cscfg att ändras till att använda Azure Resource Manager-ID för det virtuella nätverket. (prenumeration/prenumeration-ID/resurs-grupp/resurs-grupp-namn/resurs/VNet-namn) <br><br>Om du vill hantera distributionen efter migreringen uppdaterar du den lokala kopian av. cscfg-filen för att börja använda Azure Resource Manager-ID i stället för namnet på det virtuella nätverket. <br><br>En. cscfg-fil som använder det gamla namngivnings schemat kommer inte att klara verifiering. 
| Virtual Network | Migrering av distribution med roller i olika undernät. | En moln tjänst med olika roller i olika undernät stöds för migrering. |
    

## <a name="resources-and-features-not-available-for-migration"></a>Resurser och funktioner som inte är tillgängliga för migrering
Detta är de viktigaste scenarierna med kombinationer av resurser, funktioner och Cloud Services. Den här listan är inte fullständig. 

| Resurs | Nästa steg/arbete – runt | 
|---|---|
| Regler för automatisk skalning | Migreringen går igenom men reglerna tas bort. [Återskapa reglerna](https://docs.microsoft.com/azure/cloud-services-extended-support/configure-scaling) efter migrering på Cloud Services (utökad support). | 
| Aviseringar | Migreringen går igenom men aviseringar tas bort. [Återskapa reglerna](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-alerts) efter migrering på Cloud Services (utökad support). | 
| VPN Gateway | Ta bort VPN Gateway innan du påbörjar migreringen och återskapa sedan VPN Gateway när migreringen är klar. | 
| Express Route-Gateway (endast i samma prenumeration som Virtual Network) | Ta bort Express Route-gatewayen innan du påbörjar migreringen och återskapa sedan gatewayen när migreringen är klar. | 
| Kvot  | Kvoten har inte migrerats. [Begär en ny kvot](https://docs.microsoft.com/azure/azure-resource-manager/templates/error-resource-quota#solution) på Azure Resource Manager innan migreringen ska lyckas. | 
| Tillhörighetsgrupper | Stöds inte. Ta bort eventuella tillhörighets grupper innan migreringen.  | 
| Virtuella nätverk med [peering för virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)| Innan du migrerar ett virtuellt nätverk som är peer-kopplat till ett annat virtuellt nätverk, tar du bort peeringen, migrerar det virtuella nätverket till Resource Manager och återskapar peering. Detta kan orsaka stillestånds tid beroende på arkitekturen. | 
| Virtuella nätverk som innehåller App Service miljöer | Stöds inte | 
| Virtuella nätverk som innehåller HDInsight-tjänster | Stöds inte. 
| Virtuella nätverk som innehåller Azure API Management-distributioner | Stöds inte. <br><br> Om du vill migrera det virtuella nätverket ändrar du det virtuella nätverket för API Management-distributionen. Detta är ingen drift avbrott. | 
| Klassiska ExpressRoute-kretsar | Stöds inte. <br><br>Dessa kretsar måste migreras till Azure Resource Manager innan du påbörjar migreringen av PaaS. Läs mer i [Flytta ExpressRoute-kretsar från den klassiska distributions modellen till Resource Manager](../expressroute/expressroute-howto-move-arm.md). |  
| Rollbaserad Access Control | Efter migreringen måste URI: n för resurs ändringarna från `Microsoft.ClassicCompute` till `Microsoft.Compute` RBAC-principer uppdateras efter migreringen. | 
| Application Gateway | Stöds inte. <br><br> Ta bort Application Gateway innan du påbörjar migreringen och återskapa sedan Application Gateway när migreringen är klar till Azure Resource Manager | 

## <a name="unsupported-configurations--migration-scenarios"></a>Konfigurationer/migrations scenarier som inte stöds

| Konfiguration/scenario  | Nästa steg/arbete – runt | 
|---|---|
| Migrering av vissa äldre distributioner som inte är i ett virtuellt nätverk | Vissa moln tjänst distributioner som inte finns i ett virtuellt nätverk stöds inte för migrering. <br><br> 1. Använd validate API för att kontrol lera om distributionen är tillgänglig för migrering. <br> 2. om det är berättigat kommer distributionerna att flyttas till Azure Resource Manager under ett virtuellt nätverk med prefixet "DefaultRdfeVnet" | 
| Migrering av distributioner som innehåller både produktion och mellanlagrings plats distribution med dynamiska IP-adresser | Migrering av en moln tjänst på två platser kräver att mellanlagrings platsen tas bort. När du har tagit bort mellanlagringsplatsen migrerar du produktions platsen som en oberoende moln tjänst (utökad support) i Azure Resource Manager. Distribuera sedan om mellanlagrings miljön som en ny moln tjänst (utökad support) och gör den utbytbar med den första. | 
| Migrering av distributioner som innehåller både produktions-och mellanlagrings plats distribution med hjälp av Reserverad IP adresser | Stöds inte. | 
| Migrering av produktions-och mellanlagrings distribution i olika virtuella nätverk|Migrering av en moln tjänst på två platser kräver att du tar bort mellanlagrings platsen. När du har tagit bort mellanlagringsplatsen migrerar du produktions platsen som en oberoende moln tjänst (utökad support) i Azure Resource Manager. En ny Cloud Services-distribution (utökad support) kan sedan länkas till den migrerade distributionen med växlings bara egenskapen aktive rad. Distributioner av den gamla distributions plats distributionen kan återanvändas för att skapa den nya växlings bara distributionen. | 
| Migrering av tomma moln tjänster (moln tjänster utan distribution) | Stöds inte. | 
| Migrering av distribution som innehåller plugin-programmet för fjärr skrivbord och fjärr skrivbords tillägg | Alternativ 1: ta bort plugin-programmet för fjärr skrivbord innan migreringen. Detta kräver ändringar i distributions filen. Migreringen fortsätter sedan. <br><br> Alternativ 2: ta bort fjärr skrivbords tillägget och migrera distributionen. Efter migreringen tar du bort plugin-programmet och installerar tillägget. Detta kräver ändringar i distributions filen. <br><br> Ta bort plugin-programmet och tillägget innan du migrerar. [Plugin-program rekommenderas inte](https://docs.microsoft.com/azure/cloud-services-extended-support/deploy-prerequisite#required-service-definition-file-csdef-updates) för användning på Cloud Services (utökad support).| 
| Virtuella nätverk med både PaaS-och IaaS-distribution |Stöds inte <br><br> Flytta antingen PaaS-eller IaaS-distributionerna till ett annat virtuellt nätverk. Detta leder till drift stopp. | 
Moln tjänst distributioner med äldre roll storlekar (till exempel små eller ExtraLarge). | Migreringen kommer att slutföras, men roll storlekarna kommer att uppdateras för att använda moderna roll storlekar. Det finns ingen ändring i Cost-eller SKU-egenskaperna och den virtuella datorn kommer inte att startas om för den här ändringen. Uppdatera alla distributions artefakter för att referera till dessa nya modern roll storlekar. Mer information finns i [tillgängliga VM-storlekar](available-sizes.md)|
| Migrering av moln tjänst till ett annat virtuellt nätverk | Stöds inte <br><br> 1. flytta distributionen till ett annat klassiskt virtuellt nätverk innan du migrerar. Detta leder till drift stopp. <br> 2. migrera det nya virtuella nätverket till Azure Resource Manager. <br><br> Eller <br><br> 1. migrera det virtuella nätverket till Azure Resource Manager <br>2. flytta moln tjänsten till ett nytt virtuellt nätverk. Detta leder till drift stopp. | 
| Moln tjänst i ett virtuellt nätverk men har inget explicit tilldelat undernät | Stöds inte. Minskning innebär att flytta rollen till ett undernät, vilket kräver en roll omstart (nedtid) | 


## <a name="post-migration-changes"></a>Ändringar efter migrering
Den Cloud Services (klassiska) distributionen konverteras till en moln tjänst (utökad support) distribution. Mer information finns i [dokumentationen för Cloud Services (utökad support)](deploy-prerequisite.md) .  

### <a name="changes-to-deployment-files"></a>Ändringar i distributions filer 

Mindre ändringar görs i kundens. csdef-och. cscfg-fil för att göra distributions filerna förenliga med Azure Resource Manager och Cloud Services (utökade support) krav. Post-migreringen hämtar dina nya operativsystemfiler eller uppdaterar befintliga filer. Detta krävs för Update/Delete-åtgärder.  

- Virtual Network använder fullständig Azure Resource Manager resurs-ID i stället för bara resurs namnet i avsnittet NetworkConfiguration i. cscfg-filen. Till exempel `/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Network/virtualNetworks/vnet-name`. För virtuella nätverk som tillhör samma resurs grupp som moln tjänsten kan du välja att uppdatera. cscfg-filen tillbaka till att bara använda det virtuella nätverks namnet.  

- Klassiska storlekar som små, stora, ExtraLarge ersätts med deras nya storlek, Standard_A *. Storleks namnen måste ändras till deras nya namn i. csdef-filen. Mer information finns i [Cloud Services (utökad support) distributions krav](deploy-prerequisite.md#required-service-definition-file-csdef-updates)

- Använd Hämta API för att hämta den senaste kopian av distributions filerna. 
    - Hämta mallen med hjälp av [Portal](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-powershell#export-resource-groups-to-templates), [CLI](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-cli#export-resource-groups-to-templates)och [REST API](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate) 
    - Hämta. csdef-filen med [PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) eller [REST API](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-get-package). 
    - Hämta. cscfg-filen med [PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) eller [REST API](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-get-package). 
    
 

### <a name="changes-to-customers-automation-cicd-pipeline-custom-scripts-custom-dashboards-custom-tooling-etc"></a>Ändringar av kundens automatisering, CI/CD-pipeline, anpassade skript, anpassade instrument paneler, anpassat verktyg osv.  

Kunderna måste uppdatera sin verktyg och automatisering för att kunna börja använda de nya API: erna/kommandona för att hantera distributionen. Kunden kan enkelt införa nya funktioner i Azure Resource Manager/Cloud Services (utökad support) som en del av den här ändringen. 

- Ändringar av resurs-och resurs grupp namn efter migrering
    - Som en del av migreringen är namnen på få resurser som moln tjänsten, offentliga IP-adresser osv. Dessa ändringar kan behöva avspeglas i distributions filer innan uppdateringen av moln tjänsten. [Läs mer om namnen på resurser som ändras](in-place-migration-technical-details.md#translation-of-resources-and-naming-convention-post-migration).  

- Återskapa regler och principer som krävs för att hantera och skala moln tjänster 
    - [Regler för automatisk skalning](configure-scaling.md) migreras inte. Återskapa reglerna för automatisk skalning efter migreringen.  
    - [Aviseringar](enable-alerts.md) migreras inte. Återskapa aviseringarna efter migreringen.
    - Key Vault skapas utan några åtkomst principer. [Skapa lämpliga principer](../key-vault/general/assign-access-policy-portal.md) på Key Vault för att visa eller hantera dina certifikat. Certifikaten visas under Inställningar på fliken hemligheter.

## <a name="next-steps"></a>Nästa steg
- [Översikt över migrering av plattformar som stöds av IaaS-resurser från klassisk till Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)
- Migrera till Cloud Services (utökad support) med hjälp av [Azure Portal](in-place-migration-portal.md)
- Migrera till Cloud Services (utökad support) med [PowerShell](in-place-migration-powershell.md)
