---
title: Översikt över Developer-portalen i Azure API Management
titleSuffix: Azure API Management
description: 'Lär dig mer om Developer-portalen i API Management-en anpassningsbar webbplats där API-konsumenter kan utforska dina API: er.'
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: 30487218fc95be75d22b5a9ea5a6dbc224ffd025
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93074805"
---
# <a name="overview-of-the-developer-portal"></a>Översikt över Developer-portalen

Developer Portal är en automatiskt genererad, helt anpassningsbar webbplats med dokumentationen för dina API: er. Det är där API-konsumenter kan identifiera dina API: er, lära sig hur de används, begära åtkomst och testa dem.

I den här artikeln beskrivs skillnaderna mellan egna värdbaserade och hanterade versioner av Developer-portalen i API Management. Den innehåller också svar på vanliga frågor.

![API Management Developer-portalen](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>Migrering från den äldre portalen

> [!IMPORTANT]
> Den äldre Developer-portalen är nu inaktuell och kommer endast att få säkerhets uppdateringar. Du kan fortsätta att använda det, enligt vanligt, tills det upphör att gälla i oktober 2023 när det tas bort från alla API Management tjänster.

Migrering till den nya Developer-portalen beskrivs i [artikeln om dedikerade dokumentation](developer-portal-deprecated-migration.md).

## <a name="customization-and-styling"></a>Anpassning och formatering

Developer-portalen kan anpassas och formateras genom den inbyggda, dra och släpp-visuella redigeraren. Mer information finns i [den här självstudien](api-management-howto-developer-portal-customize.md) .

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> Utöknings barhet

Din API Management-tjänst innehåller en inbyggd, alltid uppdaterad, **hanterad** utvecklings Portal. Du kan komma åt den från Azure Portal-gränssnittet.

Om du behöver utöka den med anpassad logik, som inte stöds direkt, kan du ändra dess kodbas. Portalens kodbas är [tillgänglig i en GitHub-lagringsplats][1]. Du kan till exempel implementera en ny widget som integreras med ett support system från tredje part. När du implementerar nya funktioner kan du välja något av följande alternativ:

- **Självbetjäning** av den resulterande portalen utanför din API Management-tjänst. När du själv är värd för portalen blir du underhållaren och ansvarar för uppgraderingen. Supporten för Azure-supporten är begränsad till den grundläggande konfigurationen av egna portaler, enligt beskrivningen i [wiki-avsnittet i lagrings platsen][2].
- Öppna en pull-begäran för API Managements teamet för att slå samman nya funktioner till den **hanterade** portalens kodbas.

Information om utöknings information och instruktioner finns i [GitHub-lagringsplatsen][1] och [självstudierna för att implementera en widget][3]. [Självstudien för att anpassa den hanterade portalen](api-management-howto-developer-portal-customize.md) vägleder dig genom portalens administrations panel, som är vanligt för **hanterade** och **egna värdbaserade** versioner.

## <a name="frequently-asked-questions"></a><a name="faq"></a> Vanliga frågor och svar

I det här avsnittet svarar vi på vanliga frågor om Developer-portalen, som är av allmän natur. För frågor som är speciella för den självbetjänings versionen, se [wiki-avsnittet i GitHub-lagringsplatsen](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a> Hur kan jag migrera från för hands versionen av portalen?

När du först startade för hands versionen av Developer-portalen etablerade du för hands versionen av dess standard innehåll i API Managements tjänsten. Standard innehållet har ändrats avsevärt i den allmänt tillgängliga versionen. Till exempel innehåller för hands versionen av standard innehållet inte OAuth-knappar på inloggnings sidorna, den använder olika widgetar för att Visa API: er och förlitar sig på begränsade funktioner för att strukturera utvecklings portalens sidor. Även om det finns skillnader i innehållet uppdateras portalens motor (inklusive underliggande widgetar) automatiskt varje gång du publicerar din Developer-Portal.

Om du har anpassat din portal baserat på för hands versionen av innehållet kan du fortsätta att använda den som den är och placera nya widgetar manuellt på portalens sidor. Annars rekommenderar vi att du ersätter portalens innehåll med det nya standard innehållet.

Om du vill återställa innehållet i en hanterad Portal väljer du **Återställ innehåll** i menyn **åtgärder** . Den här åtgärden tar bort allt innehåll i portalen och etablerar nytt standard innehåll. Du kommer att förlora alla anpassningar och ändringar i utvecklings portalen. Det **går inte att ångra den här åtgärden**.

![Återställ Portal innehåll](media/api-management-howto-developer-portal/reset-content.png)

Om du använder den själv värdbaserade versionen kör `scripts.v2/cleanup.bat` och `scripts.v2/generate.bat` skript från GitHub-lagringsplatsen för att ta bort befintligt innehåll och etablera nytt innehåll. Se till att uppgradera din portal kod till den senaste versionen från GitHub-databasen i förväg.

Om du först ansluter till portalen efter det allmänna tillgänglighets meddelandet i november 2019 bör det redan vara en funktion som det nya standard innehållet och ingen ytterligare åtgärd krävs.

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>Funktioner jag behöver stöds inte i portalen

Du kan öppna en funktions förfrågan i [GitHub-lagringsplatsen][1] eller [implementera de funktioner som saknas själv][3]. Mer information finns i avsnittet om **utökningen** ovan.

### <a name="how-can-i-automate-portal-deployments"></a><a id="automate"></a> Hur kan jag automatisera Portal distributioner?

Du kan program mässigt komma åt och hantera Developer portalens innehåll via REST API, oavsett om du använder en hanterad eller en egen version.

API: et finns dokumenterat i [GitHub-lagringsplatsens wiki-avsnitt][2]. Den kan användas för att automatisera migrering av Portal innehåll mellan miljöer, till exempel från en test miljö till produktions miljön. Du kan lära dig mer om den här processen [i den här dokumentations artikeln](https://aka.ms/apimdocs/migrateportal) på GitHub.

### <a name="how-do-i-move-from-the-managed-to-the-self-hosted-version"></a>Hur gör jag för att flytta från hanterad till den själv värdbaserade versionen?

Se den detaljerade artikeln i [wiki-avsnittet i databasen för Developer-portalen på GitHub][2].

### <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>Kan jag ha flera utvecklares portaler i en API Management-tjänst?

Du kan ha en hanterad Portal och flera egen värdbaserade portaler. Innehållet i alla portaler lagras i samma API Management tjänst, så att de blir identiska. Om du vill skilja ut portalernas utseende och funktioner kan du själv vara värd för dem med dina egna anpassade widgetar som dynamiskt anpassar sidor på körning, till exempel baserat på URL: en.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Stöder portalen Azure Resource Manager mallar och/eller är den kompatibel med API Management DevOps Resource Kit?

Nej.

### <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>Sparas portalens innehåll med säkerhets kopierings-/återställnings funktionen i API Management?

Nej.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Måste jag aktivera ytterligare VNet-anslutning för de hanterade portalernas beroenden?

I de flesta fall – nej.

Om din API Management-tjänst finns i ett internt virtuellt nätverk är din utvecklings Portal bara tillgänglig i nätverket. Värd namnet för hanterings slut punkten måste matcha den interna VIP för tjänsten från den dator som du använder för att få åtkomst till portalens administrations gränssnitt. Kontrol lera att hanterings slut punkten är registrerad i DNS. I händelse av fel konfiguration visas ett fel meddelande: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

Om din API Management-tjänst finns i ett internt virtuellt nätverk och du har åtkomst till den via Application Gateway från Internet, så se till att aktivera anslutningen till Developer-portalen och hanterings slut punkterna för API Management. Du kan behöva inaktivera brand Väggs regler för webb program. Mer information finns i [den här dokumentations artikeln](api-management-howto-integrate-internal-vnet-appgateway.md) .

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Jag har tilldelat en anpassad API Management domän och den publicerade portalen fungerar inte

När du har uppdaterat domänen måste du [publicera portalen](api-management-howto-developer-portal-customize.md#publish) igen för att ändringarna ska börja gälla.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Jag har lagt till en identitets leverantör och jag kan inte se den i portalen

När du har konfigurerat en identitets leverantör (till exempel Azure AD, Azure AD B2C) måste du [publicera portalen](api-management-howto-developer-portal-customize.md#publish) igen för att ändringarna ska börja gälla. Se till att dina Developer Portal-sidor innehåller widgeten OAuth-knappar.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Jag har konfigurerat delegering och portalen använder den inte

När du har konfigurerat delegering måste du [publicera portalen](api-management-howto-developer-portal-customize.md#publish) igen för att ändringarna ska börja gälla.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Mina andra API Management konfigurations ändringar har inte spridits i Developer-portalen

De flesta konfigurations ändringar (till exempel VNet, inloggning, produkt villkor) kräver [att portalen publiceras om](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> Jag får ett CORS-fel när jag använder den interaktiva konsolen

Den interaktiva konsolen gör en API-begäran på klient sidan från webbläsaren. Lös CORS-problemet genom att lägga till [en CORS-princip](api-management-cross-domain-policies.md#CORS) på dina API: er.

Du kan kontrol lera status för CORS-principen i avsnittet **Portal översikt** i API Management tjänsten i Azure Portal. En varnings ruta anger att en princip saknas eller är felkonfigurerad.

![Skärm bild som visar var du kan kontrol lera status för din CORS-princip.](media/api-management-howto-developer-portal/cors-azure-portal.png)

Tillämpa CORS-principen automatiskt genom att klicka på knappen **Aktivera CORS** .

Du kan också aktivera CORS manuellt.

1. Välj **tillämpa den manuellt på länken global nivå** för att se den genererade princip koden.
2. Gå till **alla API:** er i avsnittet **API: er** i din API Management-tjänst i Azure Portal.
3. Välj **</>** ikonen i avsnittet **inkommande bearbetning** .
4. Infoga principen i **<inbound>** avsnittet i XML-filen. Se till att **<origin>** värdet matchar din utvecklares Portal domän.

> [!NOTE]
> 
> Om du använder CORS-principen i produkt omfånget, i stället för API: erna och ditt API använder prenumerations nyckel autentisering via en rubrik, fungerar inte konsolen.
>
> Webbläsaren utfärdar automatiskt ett alternativ HTTP-begäran, som inte innehåller något sidhuvud med prenumerations nyckeln. På grund av den saknade prenumerations nyckeln kan API Management inte associera alternativ anropet med en produkt, så det går inte att använda CORS-principen.
>
> Som en lösning kan du skicka prenumerations nyckeln i en frågeparameter.

> [!NOTE]
> 
> Endast en CORS-princip körs. Om du har angett flera CORS-principer (till exempel på API-nivå och på nivån alla-API: er) kanske den interaktiva konsolen inte fungerar som förväntat.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Vilka behörigheter behöver jag för att redigera Developer-portalen?

Om du ser `Oops. Something went wrong. Please try again later.` felet när du öppnar portalen i det administrativa läget kan du sakna de nödvändiga behörigheterna (Azure RBAC).

De äldre portalerna krävde behörigheten för `Microsoft.ApiManagement/service/getssotoken/action` tjänst omfånget ( `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>` ) för att ge användar administratörs åtkomst till portalerna. Den nya portalen kräver behörighet `Microsoft.ApiManagement/service/users/token/action` i omfånget `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` .

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
 
När rollen har skapats kan den beviljas till alla användare från avsnittet **Access Control (IAM)** i Azure Portal. Om du tilldelar den här rollen till en användare tilldelas behörigheten på tjänstens omfång. Användaren kommer att kunna generera SAS-token för *en* användares räkning i tjänsten. Rollen måste ha tilldelats rollen som administratör för tjänsten. Följande PowerShell-kommando visar hur du tilldelar rollen till en användare `user1` vid det lägsta omfånget för att undvika att ge användaren onödiga behörigheter: 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

När behörigheterna har beviljats till en användare måste användaren logga ut och logga in igen till Azure Portal för att de nya behörigheterna ska börja gälla.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Jag ser `Unable to start the portal. See if settings are specified correctly (...)` felet

Det här felet visas när ett `GET` anrop `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` Miss lyckas. Anropet utfärdas från webbläsaren av portalens administrations gränssnitt.

Om din API Management-tjänst finns i ett VNet – hänvisa till den VNet-anslutningens fråga ovan.

Anrops felen kan också orsakas av ett TLS/SSL-certifikat, som är tilldelat en anpassad domän och som inte är betrodd av webbläsaren. Som en minskning kan du ta bort den anpassade domänen för hanterings slut punkten – API Management kommer att återgå till standard slut punkten med ett betrott certifikat.

### <a name="whats-the-browser-support-for-the-portal"></a>Vad är webb läsar stöd för portalen?

| Webbläsare                     | Stöds       |
|-----------------------------|-----------------|
| Apple Safari                | Ja<sup>1</sup> |
| Google Chrome               | Ja<sup>1</sup> |
| Microsoft Edge              | Ja<sup>1</sup> |
| Microsoft Internet Explorer | Inga              |
| Mozilla Firefox             | Ja<sup>1</sup> |

 <small><sup>1</sup> stöds i de två senaste produktions versionerna.</small>

## <a name="next-steps"></a>Nästa steg

Läs mer om den nya Developer-portalen:

- [Komma åt och anpassa Managed Developer-portalen](api-management-howto-developer-portal-customize.md)
- [Konfigurera en egen värd version av portalen][2]
- [Implementera din egen widget][3]

Bläddra bland andra resurser:

- [GitHub-lagringsplats med käll koden][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend
