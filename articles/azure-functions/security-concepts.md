---
title: Skydda Azure Functions
description: Lär dig mer om hur du gör funktions koden igång i Azure säkrare från vanliga attacker.
ms.date: 4/13/2020
ms.topic: conceptual
ms.openlocfilehash: 351bdca7ff94b6c058b5ab62fd9c16d707e7dc78
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368497"
---
# <a name="securing-azure-functions"></a>Skydda Azure Functions

På många sätt är planeringen av säker utveckling, distribution och drift av Server lös funktioner i stort sett densamma som för alla webbaserade eller program som körs i molnet. [Azure App Service](../app-service/index.yml) tillhandahåller värd infrastrukturen för dina funktions appar. Den här artikeln innehåller säkerhets strategier för att köra funktions koden och hur App Service kan hjälpa dig att skydda dina funktioner. 

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

En uppsättning säkerhets rekommendationer som följer [Azures säkerhets benchmark](../security/benchmarks/overview.md)finns i [Azures säkerhets bas linje för Azure Functions](security-baseline.md).

## <a name="secure-operation"></a>Säker åtgärd 

I det här avsnittet får du hjälp med att konfigurera och köra din Function-app så säkert som möjligt. 

### <a name="security-center"></a>Security Center

Security Center integreras med din Function-app i portalen. Det ger kostnads fri en snabb utvärdering av potentiella konfigurations problem som rör säkerhets risker. Funktions appar som körs i en dedikerad plan kan också använda real tids säkerhetsfunktioner i Security Center, för en ytterligare kostnad. Mer information finns i [skydda dina Azure App Service-webbappar och API: er](../security-center/defender-for-app-service-introduction.md). 

### <a name="log-and-monitor"></a>Logga och övervaka

Ett sätt att identifiera attacker är genom aktivitets övervakning och loggnings analys. Funktioner integreras med Application Insights för att samla in logg-, prestanda-och fel data för din Function-app. Application Insights identifierar automatiskt prestanda avvikelser och innehåller kraftfulla analys verktyg som hjälper dig att diagnostisera problem och förstå hur dina funktioner används. Mer information finns i [övervaka Azure Functions](functions-monitoring.md).

Funktioner integreras också med Azure Monitor loggar så att du kan konsolidera funktions program loggar med system händelser för enklare analys. Du kan använda diagnostikinställningar för att konfigurera strömnings export av plattforms loggar och mått för dina funktioner till önskat mål, till exempel en logganalys-arbetsyta. Mer information finns i [övervaknings Azure Functions med Azure Monitor loggar](functions-monitor-log-analytics.md). 

För automatisering av hot och svar på företags nivå, strömma dina loggar och händelser till en logs Analytics-arbetsyta. Sedan kan du ansluta Azure Sentinel till den här arbets ytan. Läs mer i [Vad är Azure Sentinel](../sentinel/overview.md).  

Mer säkerhets rekommendationer för att se om det finns [Azure Functions i Azures säkerhets bas linje](security-baseline.md#logging-and-monitoring). 

### <a name="require-https"></a>Kräv HTTPS

Som standard kan klienter ansluta till funktions slut punkter med både HTTP eller HTTPS. Du bör Omdirigera HTTP till HTTPs eftersom HTTPS använder SSL/TLS-protokollet för att tillhandahålla en säker anslutning, som både är krypterad och autentiserad. Mer information finns i [FRAMTVINGA https](../app-service/configure-ssl-bindings.md#enforce-https).

När du behöver HTTPS bör du även kräva den senaste TLS-versionen. Mer information finns i [FRAMTVINGA TLS-versioner](../app-service/configure-ssl-bindings.md#enforce-tls-versions).

Mer information finns i [Secure Connections (TLS)](../app-service/overview-security.md#https-and-certificates).

### <a name="function-access-keys"></a>Funktions åtkomst nycklar

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

#### <a name="system-key"></a>System nyckel 

Vissa tillägg kan kräva en Systemhanterad nyckel för åtkomst till webhook-slutpunkter. System nycklar är avsedda för tilläggsfunktioner som kan anropas av interna komponenter. [Event Grid-utlösaren](functions-bindings-event-grid-trigger.md) kräver till exempel att prenumerationen använder en system nyckel när utlösaren anropas. Durable Functions använder också system nycklar för att anropa [varaktiga API: er för aktivitets tillägg](durable/durable-functions-http-api.md). 

System nycklarnas omfattning bestäms av tillägget, men det gäller vanligt vis hela Function-appen. System nycklar kan bara skapas med vissa tillägg och du kan inte uttryckligen ange deras värden. Precis som med andra nycklar kan du generera ett nytt värde för nyckeln från portalen eller genom att använda nyckel-API: er.

#### <a name="keys-comparison"></a>Jämförelse av nycklar

I följande tabell jämförs användningen av olika typer av åtkomst nycklar:

| Action                                        | Omfång                    | Giltiga nycklar         |
|-----------------------------------------------|--------------------------|--------------------|
| Köra en funktion                            | Speciell funktion        | Funktion           |
| Köra en funktion                            | Alla funktioner             | Funktion eller värd   |
| Anropa en administratörs slut punkt                        | Funktionsapp             | Värd (endast Master) |
| Anropa beständiga API: er för aktivitets tillägg              | Function app<sup>1</sup> | System<sup>2</sup> |
| Anropa en tilläggs-/regionsspecifika webhook (intern) | Function app<sup>1</sup> | system<sup>2</sup> |

<sup>1</sup> Omfattning som fastställs av tillägget.  
<sup>2</sup> Vissa namn som anges av tillägget.

Mer information om åtkomst nycklar finns i artikeln om [bindning av http-utlösare](functions-bindings-http-webhook-trigger.md#obtaining-keys).


#### <a name="secret-repositories"></a>Hemliga databaser

Som standard lagras nycklar i en Blob Storage-behållare i kontot som anges av `AzureWebJobsStorage` inställningen. Du kan använda specifika program inställningar för att åsidosätta det här beteendet och lagra nycklar på en annan plats.

|Location  |Inställning | Värde | Beskrivning  |
|---------|---------|---------|---------|
|Annat lagrings konto     |  `AzureWebJobsSecretStorageSas`       | `<BLOB_SAS_URL` | Lagrar nycklar i Blob Storage för ett andra lagrings konto, baserat på den angivna SAS-webbadressen. Nycklar krypteras innan de lagras med en hemlighet som är unik för din Function-app. |
|Filsystem   | `AzureWebJobsSecretStorageType`   |  `files`       | Nycklar sparas i fil systemet, krypteras före lagring med hjälp av en hemlighet som är unik för din Function-app. |
|Azure Key Vault  | `AzureWebJobsSecretStorageType`<br/>`AzureWebJobsSecretStorageKeyVaultName` | `keyvault`<br/>`<VAULT_NAME>` | Valvet måste ha en åtkomst princip som motsvarar den systemtilldelade hanterade identiteten för värd resursen. Åtkomst principen ska ge identiteten följande hemliga behörigheter: `Get` , `Set` , `List` och `Delete` . <br/>När du kör lokalt används utvecklarens identitet, och inställningarna måste finnas i [local.settings.jspå filen](functions-run-local.md#local-settings-file). | 
|Kubernetes-hemligheter  |`AzureWebJobsSecretStorageType`<br/>`AzureWebJobsKubernetesSecretName` (valfritt) | `kubernetes`<br/>`<SECRETS_RESOURCE>` | Stöds endast när Functions-körningen körs i Kubernetes. När `AzureWebJobsKubernetesSecretName` inte är inställt anses lagrings platsen vara skrivskyddad. I det här fallet måste värdena genereras före distributionen. Azure Functions Core Tools genererar värdena automatiskt när du distribuerar till Kubernetes.|

### <a name="authenticationauthorization"></a>Autentisering/auktorisering

Även om funktions nycklar kan ge viss minskning av oönskad åtkomst, är det enda sättet att verkligen skydda funktions slut punkterna genom att implementera positiv autentisering av klienter som har åtkomst till dina funktioner. Du kan sedan fatta auktoriseringsbeslut baserat på identitet.  

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

### <a name="permissions"></a>Behörigheter

Precis som med alla program och tjänster, kör målet din Function-app med lägsta möjliga behörighet. 

#### <a name="user-management-permissions"></a>Användar hanterings behörigheter

Functions stöder inbyggd [Azure-rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md). Azure-roller som stöds av Functions är [deltagare](../role-based-access-control/built-in-roles.md#contributor), [ägare](../role-based-access-control/built-in-roles.md#owner)och [läsare](../role-based-access-control/built-in-roles.md#owner). 

Behörigheter är effektiva på funktionens app-nivå. Deltagar rollen krävs för att utföra de flesta funktioner på program nivå. Endast ägar rollen kan ta bort en Function-app. 

#### <a name="organize-functions-by-privilege"></a>Ordna funktioner efter privilegium 

Anslutnings strängar och andra autentiseringsuppgifter som lagras i program inställningar ger alla funktioner i Function-appen samma uppsättning behörigheter i den associerade resursen. Överväg att minimera antalet funktioner med åtkomst till särskilda autentiseringsuppgifter genom att flytta funktioner som inte använder dessa autentiseringsuppgifter till en separat Function-app. Du kan alltid använda metoder som [funktions länkning](/learn/modules/chain-azure-functions-data-using-bindings/) för att skicka data mellan funktioner i olika Function-appar.  

#### <a name="managed-identities"></a>Hanterade identiteter

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

Hanterade identiteter kan användas i stället för hemligheter för anslutningar från vissa utlösare och bindningar. Se [identitetsbaserade anslutningar](#identity-based-connections).

Mer information finns i [så här använder du hanterade identiteter för app service och Azure Functions](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json).

#### <a name="restrict-cors-access"></a>Begränsa CORS-åtkomst

[Resurs delning mellan ursprung (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) är ett sätt att tillåta att webbappar körs i en annan domän för att göra förfrågningar till dina HTTP trigger-slutpunkter. App Service tillhandahåller inbyggt stöd för att kunna tillhandahålla nödvändiga CORS-huvuden i HTTP-begäranden. CORS-regler definieras på en Function-app-nivå.  

Det är frestande att använda ett jokertecken som gör att alla webbplatser får åtkomst till din slut punkt. Men detta bevarar syftet med CORS, som hjälper till att förhindra skript angrepp över flera webbplatser. Lägg i stället till en separat CORS-post för domänen för varje webbapp som måste ha åtkomst till din slut punkt. 

### <a name="managing-secrets"></a>Hantera hemligheter 

För att kunna ansluta till de olika tjänsterna och resurserna för att kunna köra din kod måste funktions apparna kunna komma åt hemligheter, till exempel anslutnings strängar och tjänst nycklar. I det här avsnittet beskrivs hur du lagrar hemligheter som krävs av dina funktioner.

Lagra aldrig hemligheter i funktions koden. 

#### <a name="application-settings"></a>Programinställningar

Som standard lagrar du anslutnings strängar och hemligheter som används av din Function-app och bindningar som program inställningar. Detta gör dessa autentiseringsuppgifter tillgängliga för både funktions koden och de olika bindningar som används av funktionen. Namnet på program inställningen (nyckel) används för att hämta det faktiska värdet, vilket är hemligheten. 

Till exempel kräver varje Function-app ett associerat lagrings konto som används av körnings miljön. Som standard lagras anslutningen till det här lagrings kontot i en program inställning med namnet `AzureWebJobsStorage` .

App-inställningar och anslutnings strängar lagras krypterade i Azure. De dekrypteras bara innan de matas in i appens process minne när appen startas. Krypterings nycklarna roteras regelbundet. Om du hellre vill hantera den säkra lagringen av dina hemligheter bör du i stället ange referenser till Azure Key Vault. 

Du kan också kryptera inställningar som standard i local.settings.jsi filen när du utvecklar funktioner på den lokala datorn. Mer information finns i `IsEncrypted` egenskapen i den [lokala inställnings filen](functions-run-local.md#local-settings-file).  

#### <a name="key-vault-references"></a>Key Vault referenser

Även om program inställningarna är tillräckliga för de flesta funktioner kanske du vill dela samma hemligheter över flera tjänster. I det här fallet resulterar redundant lagring av hemligheter på fler potentiella säkerhets risker. En säkrare metod är till en central hemlighet Storage-tjänst och använder referenser till den här tjänsten i stället för själva hemligheterna.      

[Azure Key Vault](../key-vault/general/overview.md) är en tjänst som tillhandahåller centraliserad hemligheter-hantering med fullständig kontroll över åtkomst principer och gransknings historik. Du kan använda en Key Vault referens i stället för en anslutnings sträng eller nyckel i dina program inställningar. Läs mer i [använda Key Vault referenser för app service och Azure Functions](../app-service/app-service-key-vault-references.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### <a name="identity-based-connections"></a>Identitetsbaserade anslutningar

Identiteter kan användas i stället för hemligheter för att ansluta till vissa resurser. Detta har fördelen att inte kräva hantering av en hemlighet och ger mer detaljerad åtkomst kontroll och granskning. 

När du skriver kod som skapar anslutningen till Azure- [tjänster som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), kan du välja att använda en identitet i stället för en hemlig eller anslutnings sträng. Information om båda anslutnings metoderna beskrivs i dokumentationen för varje tjänst.

Vissa Azure Functions-utlösare och bindnings tillägg kan konfigureras med en identitets baserad anslutning. Idag inkluderar detta [Azure Blob](./functions-bindings-storage-blob.md) och [Azure Queue](./functions-bindings-storage-queue.md) Extensions. Information om hur du konfigurerar dessa tillägg så att de använder en identitet finns i [så här använder du identitetsbaserade anslutningar i Azure Functions](./functions-reference.md#configure-an-identity-based-connection).

### <a name="set-usage-quotas"></a>Ange användnings kvoter

Överväg att ange en användnings kvot för funktioner som körs i en förbruknings plan. När du ställer in en daglig GB-SEC-gräns på summan av den totala körningen av funktioner i din Function-app stoppas körningen när gränsen nås. Detta kan eventuellt hjälpa till att minimera skadlig kod som kör dina funktioner. Information om hur du uppskattar förbrukningen för dina funktioner finns i [uppskatta förbruknings plan kostnader](functions-consumption-costs.md). 

### <a name="data-validation"></a>Datavalidering

Utlösare och bindningar som används av dina funktioner ger ingen ytterligare data verifiering. Din kod måste verifiera alla data som tas emot från en utlösare eller en indata-bindning. Om en överordnad tjänst komprometteras vill du inte att inte verifierade indata flödar genom dina funktioner. Om din funktion t. ex. lagrar data från en Azure Storage kö i en Relations databas måste du verifiera data och Parameterisera dina kommandon för att undvika SQL-inmatnings attacker. 

Anta inte att de data som kommer in i funktionen redan har verifierats eller är sanerade. Det är också en bra idé att kontrol lera att data som skrivs till utgående bindningar är giltiga. 

### <a name="handle-errors"></a>Hantera fel

Även om det verkar Basic är det viktigt att skriva bra fel hantering i dina funktioner. Fel som inte hanteras bubbeldiagram till värden och hanteras av körnings miljön. Olika bindningar hanterar bearbetning av fel på olika sätt. Mer information finns i [Azure Functions fel hantering](functions-bindings-error-pages.md).

### <a name="disable-remote-debugging"></a>Inaktivera fjärrfelsökning

Kontrol lera att fjärrfelsökning är inaktive rad, förutom när du aktivt felsöker dina funktioner. Du kan inaktivera fjärrfelsökning på fliken **allmänna inställningar** i din funktion app- **konfiguration** i portalen. 

### <a name="restrict-cors-access"></a>Begränsa CORS-åtkomst

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

Använd inte jokertecken i listan över tillåtna ursprung. I stället anger du de domäner från vilka du förväntar dig att få förfrågningar.

### <a name="store-data-encrypted"></a>Lagra data krypterade

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="secure-deployment"></a>Säker distribution

Azure Functions verktyg för en integrering är det enkelt att publicera lokal projekt kod till Azure. Det är viktigt att förstå hur distributionen fungerar när du överväger säkerheten för en Azure Functions topologi.   

### <a name="deployment-credentials"></a>Autentiseringsuppgifter

För App Service distributioner krävs en uppsättning autentiseringsuppgifter för distribution. De här autentiseringsuppgifterna för distribution används för att skydda funktions program distributioner. Autentiseringsuppgifterna för distribution hanteras av App Service plattformen och är krypterade i vila. 

Det finns två typer av autentiseringsuppgifter för distribution:

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

För tillfället stöds Key Vault inte för autentiseringsuppgifter för distribution. Mer information om hur du hanterar distributions information finns i [Konfigurera autentiseringsuppgifter för distribution för Azure App Service](../app-service/deploy-configure-credentials.md).

### <a name="disable-ftp"></a>Inaktivera FTP

Som standard har varje Function-app en FTP-slutpunkt aktive rad. FTP-slutpunkten nås med hjälp av autentiseringsuppgifter för distribution. 

FTP rekommenderas inte för att distribuera funktions koden. FTP-distributioner är manuella och kräver att du synkroniserar utlösare. Mer information finns i [FTP-distribution](functions-deployment-technologies.md#ftp). 

När du inte planerar att använda FTP bör du inaktivera det i portalen. Om du väljer att använda FTP bör du [FRAMTVINGA FTPS](../app-service/deploy-ftp.md#enforce-ftps).

### <a name="secure-the-scm-endpoint"></a>Skydda SCM-slutpunkten

Varje Function-app har en motsvarande `scm` tjänst slut punkt som används av kudu-tjänsten (Advanced tools) för distributioner och andra App Service [plats tillägg](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions). SCM-slutpunkten för en Function-app är alltid en URL i formuläret `https://<FUNCTION_APP_NAME.scm.azurewebsites.net>` . När du använder nätverks isolering för att skydda dina funktioner måste du också ta hänsyn till den här slut punkten. 

Genom att ha en separat SCM-slutpunkt kan du styra distributioner och andra avancerade verktyg-funktioner för Function-appar som är isolerade eller som körs i ett virtuellt nätverk. SCM-slutpunkten stöder både grundläggande autentisering (med autentiseringsuppgifter för distribution) och enkel inloggning med dina Azure Portal autentiseringsuppgifter. Mer information finns i [komma åt kudu-tjänsten](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). 

### <a name="continuous-security-validation"></a>Kontinuerlig säkerhets validering

Eftersom säkerheten måste betraktas som ett steg i utvecklings processen, är det viktigt att även implementera säkerhets valideringar i en kontinuerlig distributions miljö. Detta kallas ibland för DevSecOps. Med Azure DevOps för din distributions pipeline integrerar du verifieringen i distributions processen. Mer information finns i [Lär dig hur du lägger till kontinuerlig säkerhets validering till din CI/CD-pipeline](/azure/devops/migrate/security-validation-cicd-pipeline).  

## <a name="network-security"></a>Nätverkssäkerhet

Genom att begränsa nätverks åtkomsten till din Function-app kan du kontrol lera vem som har åtkomst till dina funktions slut punkter. Functions utnyttjar App Service-infrastrukturen för att ge dina funktioner åtkomst till resurser utan att använda Internet-dirigerbart adresser eller för att begränsa Internet åtkomst till en funktions slut punkt. Mer information om de här nätverks alternativen finns [Azure Functions nätverks alternativ](functions-networking-options.md).

### <a name="set-access-restrictions"></a>Ange åtkomst begränsningar

Med åtkomst begränsningar kan du definiera listor över tillåtna/neka-regler för att styra trafik till din app. Reglerna utvärderas i prioritetsordning. Om inga regler har definierats accepterar appen trafik från vilken adress som helst. Läs mer i [Azure App Service åtkomst begränsningar #](../app-service/app-service-ip-restrictions.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### <a name="private-site-access"></a>Åtkomst till privat plats

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

### <a name="deploy-your-function-app-in-isolation"></a>Distribuera din Function-app i isolering

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

### <a name="use-a-gateway-service"></a>Använd en gateway-tjänst

Gateway-tjänster, till exempel [Azure Application Gateway](../application-gateway/overview.md) och [Azure-frontend](../frontdoor/front-door-overview.md) , gör att du kan konfigurera en brand vägg för webbaserade program (WAF). WAF-regler används för att övervaka eller blockera identifierade attacker, vilket ger en extra skydds nivå för dina funktioner. Om du vill konfigurera en WAF måste din funktions app köras i en ASE eller med hjälp av privata slut punkter (för hands version). Mer information finns i [använda privata slut punkter](../app-service/networking/private-endpoint.md).    

## <a name="next-steps"></a>Nästa steg

+ [Azures säkerhets bas linje för Azure Functions](security-baseline.md)
+ [Azure Functions diagnostik](functions-diagnostics.md)
