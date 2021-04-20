---
title: Utvecklarportalen – Vanliga frågor och svar
titleSuffix: Azure API Management
description: Vanliga frågor och svar om utvecklarportalen i API Management. Utvecklarportalen är en anpassningsbar webbplats där API-användare kan utforska dina API:er.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.openlocfilehash: b4413bc53cf5c86c311d049046db790582737ca4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741834"
---
# <a name="api-management-developer-portal---frequently-asked-questions"></a>API Management utvecklarportalen – vanliga frågor och svar

## <a name="what-if-i-need-functionality-that-isnt-supported-in-the-portal"></a>Vad händer om jag behöver funktioner som inte stöds i portalen?

Du kan öppna en funktionsbegäran på [GitHub-lagringsplatsen](https://github.com/Azure/api-management-developer-portal) [eller implementera de funktioner som saknas själv.](developer-portal-implement-widgets.md) Läs mer om utökningsbarhet [för utvecklarportalen.](api-management-howto-developer-portal.md#managed-vs-self-hosted)


## <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>Kan jag ha flera utvecklarportaler i en API Management tjänst?

Du kan ha en hanterad portal och flera lokala portaler. Innehållet i alla portaler lagras i samma tjänst API Management, så de är identiska. Om du vill särskilja portalers utseende och funktioner kan du använda egna värdar för dem med egna anpassade widgetar som dynamiskt anpassar sidor vid körning, till exempel baserat på URL:en.

## <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Stöder portalen Azure Resource Manager mallar och/eller är den kompatibel med API Management DevOps Resource Kit?

Nej.

## <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>Sparas portalens innehåll med säkerhetskopierings-/återställningsfunktionen i API Management?

Nej.

## <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Måste jag aktivera ytterligare VNet-anslutning för de hanterade portalberoendena?

I de flesta fall – nej.

Om din API Management-tjänst finns i ett internt VNet är utvecklarportalen endast tillgänglig från nätverket. Hanteringsslutpunktens värdnamn måste matcha tjänstens interna VIP från den dator som du använder för att få åtkomst till portalens administrativa gränssnitt. Kontrollera att hanteringsslutpunkten är registrerad i DNS. Om konfigurationsfel uppstår visas ett fel: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

Om din API Management-tjänst finns i ett internt VNet och du kommer åt den via Application Gateway från Internet ska du se till att aktivera anslutning till utvecklarportalen och hanteringsslutpunkterna för API Management. Du kan behöva inaktivera Web Application Firewall regler. Mer [information finns i den](api-management-howto-integrate-internal-vnet-appgateway.md) här dokumentationsartikeln.

## <a name="i-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Jag har tilldelat en API Management domän och den publicerade portalen fungerar inte

När du har uppdaterat domänen måste du [publicera om portalen för](api-management-howto-developer-portal-customize.md#publish) att ändringarna ska gälla.

## <a name="i-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Jag har lagt till en identitetsprovider och kan inte se den i portalen

När du har konfigurerat en identitetsprovider (till exempel Azure [](api-management-howto-developer-portal-customize.md#publish) AD Azure AD B2C) måste du publicera om portalen för att ändringarna ska gälla. Kontrollera att utvecklarportalens sidor innehåller OAuth-knappwidgeten.

## <a name="i-set-up-delegation-and-the-portal-doesnt-use-it"></a>Jag har ställt in delegering och portalen använder den inte

När du har ställt in delegering måste [du publicera om portalen](api-management-howto-developer-portal-customize.md#publish) för att ändringarna ska gälla.

## <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Mina andra API Management konfigurationsändringar har inte spridits i utvecklarportalen

De flesta konfigurationsändringar (till exempel VNet, inloggning, produktvillkor) [kräver att portalen publiceras på nytt.](api-management-howto-developer-portal-customize.md#publish)

## <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> Jag får ett CORS-fel när jag använder den interaktiva konsolen

Den interaktiva konsolen gör en API-begäran på klientsidan från webbläsaren. Lös CORS-problemet genom att lägga [till en CORS-princip](api-management-cross-domain-policies.md#CORS) på dina API:er.

Du kan kontrollera statusen för CORS-principen i portalens översiktsavsnitt i API Management-tjänsten i Azure Portal.  En varningsruta visar en princip som saknas eller är felkonfigurerad.

> [!NOTE]
> 
> Endast en CORS-princip körs. Om du har angett flera CORS-principer (till exempel på API-nivå och på nivån för alla API:er) kanske den interaktiva konsolen inte fungerar som förväntat.

![Skärmbild som visar var du kan kontrollera status för CORS-principen.](media/developer-portal-faq/cors-azure-portal.png)

Tillämpa CORS-principen automatiskt genom att klicka på **knappen Aktivera CORS.**

Du kan också aktivera CORS manuellt.

1. Välj länken **Tillämpa den manuellt på global nivå för att** se den genererade principkoden.
2. Gå till **Alla API:er** i **avsnittet API:er** i API Management-tjänsten i Azure Portal.
3. Välj ikonen **</>** i **avsnittet Inkommande** bearbetning.
4. Infoga principen i avsnittet **<inbound>** i XML-filen. Kontrollera att **<origin>** värdet matchar utvecklarportalens domän.

> [!NOTE]
> 
> Om du tillämpar CORS-principen i produktomfånget fungerar inte konsolen i stället för API:erna och api:et använder autentisering med prenumerationsnyckel via ett sidhuvud.
>
> Webbläsaren utfärdar automatiskt en `OPTIONS` HTTP-begäran som inte innehåller något huvud med prenumerationsnyckeln. På grund av den saknade prenumerationsnyckeln kan API Management inte associera anropet med en Produkt, så den `OPTIONS` kan inte tillämpa CORS-principen.
>
> Som en lösning kan du skicka prenumerationsnyckeln i en frågeparameter.

## <a name="what-is-the-cors-proxy-feature-and-when-should-i-use-it"></a>Vad är CORS-proxyfunktionen och när ska jag använda den?

Välj alternativet **Använd CORS-proxy** i konfigurationen av widgeten för API-åtgärdsinformation för att dirigera DEN interaktiva konsolens API-anrop via portalens server i API Management-tjänsten. I den här konfigurationen behöver du inte längre tillämpa en CORS-princip för dina API:er, och det krävs ingen anslutning till gatewayslutpunkten från den lokala datorn. Om API:erna exponeras via en gateway med egen värd eller om din tjänst finns i ett virtuellt nätverk, krävs anslutning från API Management:s servertjänst till gatewayen. Om du använder portalen med egen värd anger du portalens serverslutpunkt med hjälp `backendUrl` av alternativet i konfigurationsfilerna. I annat fall är den egna portalen inte medveten om platsen för backend-tjänsten.

## <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Vilka behörigheter behöver jag för att redigera utvecklarportalen?

Om du ser felet när du öppnar portalen i administrativt läge kanske du saknar de behörigheter som krävs `Oops. Something went wrong. Please try again later.` (Azure RBAC).

De äldre portalerna krävde `Microsoft.ApiManagement/service/getssotoken/action` behörigheten i tjänstomfånget ( `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>` ) för att ge användaradministratören åtkomst till portalerna. Den nya portalen kräver `Microsoft.ApiManagement/service/users/token/action` behörigheten i omfånget `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` .

Du kan använda följande PowerShell-skript för att skapa en roll med den behörighet som krävs. Kom ihåg att ändra `<subscription-id>` parametern. 

```powershell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
När rollen har skapats kan den beviljas till alla användare **från avsnittet Access Control (IAM)** i Azure Portal. Om du tilldelar den här rollen till en användare tilldelas behörigheten i tjänstomfånget. Användaren kommer att kunna generera SAS-token åt alla *användare* i tjänsten. Den här rollen måste minst tilldelas till administratören för tjänsten. Följande PowerShell-kommando visar hur du tilldelar rollen till en användare med det lägsta omfånget för att undvika att `user1` användaren beviljas onödiga behörigheter: 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

När behörigheterna har beviljats till en användare måste användaren logga ut och logga in igen till Azure Portal de nya behörigheterna ska gälla.

## <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Jag ser `Unable to start the portal. See if settings are specified correctly (...)` felet

Det här felet visas när ett `GET` anrop till `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` misslyckas. Anropet utfärdas från webbläsaren via portalens administrativa gränssnitt.

Om din API Management tjänst finns i ett VNet kan du gå till [VNet-anslutningsfrågan](#do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies).

Anropsfelet kan också orsakas av ett TLS/SSL-certifikat som har tilldelats en anpassad domän och som inte är betrott av webbläsaren. Som en åtgärd kan du ta bort hanteringsslutpunktens anpassade domän och API Management till standardslutpunkten med ett betrott certifikat.

## <a name="whats-the-browser-support-for-the-portal"></a>Vad har webbläsaren för stöd för portalen?

| Webbläsare                     | Stöds       |
|-----------------------------|-----------------|
| Apple Safari                | Ja<sup>1</sup> |
| Google Chrome               | Ja<sup>1</sup> |
| Microsoft Edge              | Ja<sup>1</sup> |
| Microsoft Internet Explorer | No              |
| Mozilla Firefox             | Ja<sup>1</sup> |

 <small><sup>1</sup> Stöds i de två senaste produktionsversionerna.</small>

## <a name="local-development-of-my-self-hosted-portal-is-no-longer-working"></a>Lokal utveckling av min lokala portal fungerar inte längre

Om din lokala version av utvecklarportalen inte kan spara eller hämta information från lagringskontot eller API Management instansen kan SAS-token ha upphört att gälla. Du kan åtgärda det genom att generera nya token. Instruktioner finns i självstudien för [utvecklarportalen med egen värd.](developer-portal-self-host.md#step-2-configure-json-files-static-website-and-cors-settings)

## <a name="how-can-i-remove-the-developer-portal-content-provisioned-to-my-api-management-service"></a>Hur tar jag bort innehållet på utvecklarportalen som etablerats i min API Management tjänst?

Ange de obligatoriska parametrarna i `scripts.v3/cleanup.bat` skriptet på GitHub-lagringsplatsen [för utvecklarportalen](https://github.com/Azure/api-management-developer-portal)och kör skriptet

```sh
cd scripts.v3
.\cleanup.bat
cd ..
```

## <a name="how-do-i-enable-single-sign-on-sso-authentication-to-self-hosted-developer-portal"></a>Hur gör jag för att aktivera autentisering med enkel inloggning (SSO) till utvecklarportalen med egen värd?

Utvecklarportalen har bland annat stöd för enkel inloggning (SSO). För att autentisera med den här metoden måste du göra ett anrop till `/signin-sso` med token i frågeparametern:

```html
https://contoso.com/signin-sso?token=[user-specific token]
```
### <a name="generate-user-tokens"></a>Generera användartoken
Du kan generera *användarspecifika token (inklusive administratörstoken)* med åtgärden [Hämta](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) token för delad åtkomst för [API Management REST API](/rest/api/apimanagement/apimanagementrest/api-management-rest).

> [!NOTE]
> Token måste vara URL-kodad.

## <a name="next-steps"></a>Nästa steg

Läs mer om den nya utvecklarportalen:

- [Få åtkomst till och anpassa den hanterade utvecklarportalen](api-management-howto-developer-portal-customize.md)
- [Konfigurera en egen version av portalen](developer-portal-self-host.md)
- [Implementera din egen widget](developer-portal-implement-widgets.md)

Bläddra bland andra resurser:

- [GitHub-lagringsplats med källkoden](https://github.com/Azure/api-management-developer-portal)

