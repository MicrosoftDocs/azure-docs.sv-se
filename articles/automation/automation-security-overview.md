---
title: Azure Automation översikt över autentisering av Azure Automation-konto
description: Den här artikeln innehåller en översikt över Azure Automation-kontoautentisering.
keywords: automation security, secure automation; automation authentication
services: automation
ms.subservice: process-automation
ms.date: 04/08/2021
ms.topic: conceptual
ms.openlocfilehash: b52fa3083dc5c42fa71e720e9a3991cb7aa5afec
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501577"
---
# <a name="azure-automation-account-authentication-overview"></a>Azure Automation översikt över kontoautentisering

Med Azure Automation kan du automatisera åtgärder mot resurser i Azure, lokalt och med andra molnproviders, till exempel Amazon Web Services (AWS). Du kan använda runbooks för att automatisera dina uppgifter eller en Hybrid Runbook Worker om du har affärs- eller driftprocesser att hantera utanför Azure. För att arbeta i någon av dessa miljöer krävs behörigheter för att få säker åtkomst till resurserna med de minimala rättigheter som krävs.

Den här artikeln beskriver autentiseringsscenarier som stöds av Azure Automation och beskriver hur du kommer igång baserat på den miljö eller de miljöer som du behöver hantera.

## <a name="automation-account"></a>Automation-konto

När du startar Azure Automation för första gången måste du skapa minst ett Automation-konto. Med Automation-konton kan du isolera dina Automation-resurser, runbooks, tillgångar och konfigurationer från andra kontons resurser. Du kan använda Automation-konton för att separera resurser i separata logiska miljöer eller delegerade ansvarsområden. Du kan exempelvis använda ett konto för utveckling, ett annat för produktion och ett annat för din lokala miljö. Eller så kan du dedikera ett Automation-konto för att hantera uppdateringar av operativsystemet på alla dina datorer med [Uppdateringshantering](update-management/overview.md). 

Ett Azure Automation-konto skiljer sig från ditt eller dina Microsoft-konton som skapats i Azure-prenumerationen. En introduktion till att skapa ett Automation-konto finns i [Skapa ett Automation-konto.](automation-quickstart-create-account.md)

## <a name="automation-resources"></a>Automation-resurser

Automation-resurserna för varje Automation-konto är associerade med en enda Azure-region, men kontot kan hantera alla resurser i din Azure-prenumeration. Det främsta skälet till att skapa Automation-konton i olika regioner är om du har principer som kräver att data och resurser isoleras till en specifik region.

Alla uppgifter som du skapar mot resurser med hjälp av Azure Resource Manager och PowerShell-cmdlets i Azure Automation måste autentisera till Azure med autentiseringsuppgifterna för organisationsidentiteter i Azure Active Directory (Azure AD) i Azure.

## <a name="managed-identities-preview"></a>Hanterade identiteter (förhandsversion)

En hanterad identitet från Azure Active Directory (Azure AD) gör att din runbook enkelt kan komma åt andra Azure AD-skyddade resurser. Identiteten hanteras av Azure-plattformen och kräver inte att du etablerar eller roterar några hemligheter. Mer information om hanterade identiteter i Azure AD finns i [Hanterade identiteter för Azure-resurser.](/azure/active-directory/managed-identities-azure-resources/overview)

Här är några av fördelarna med att använda hanterade identiteter:

- Du kan använda hanterade identiteter för att autentisera till alla Azure-tjänster som stöder Azure AD-autentisering.

- Hanterade identiteter kan användas utan extra kostnad.

- Du behöver inte förnya certifikatet som används av Automation Kör som-kontot.

- Du behöver inte ange Anslutningsobjektet Kör som i din Runbook-kod. Du kan komma åt resurser med hjälp av automationskontots hanterade identitet från en Runbook utan att skapa certifikat, anslutningar, Kör som-konton osv.

Ett Automation-konto kan beviljas två typer av identiteter:

- En system tilldelad identitet är kopplad till ditt program och tas bort om din app tas bort. En app kan bara ha en system tilldelad identitet.

- En användar tilldelad identitet är en fristående Azure-resurs som kan tilldelas till din app. En app kan ha flera användar tilldelade identiteter.

>[!NOTE]
> Användar tilldelade identiteter stöds inte ännu.

Mer information om hur du använder hanterade identiteter finns [i Aktivera hanterad identitet för Azure Automation (förhandsversion)](enable-managed-identity-for-automation.md).

## <a name="run-as-accounts"></a>Kör som-konton

Kör som-konton i Azure Automation autentisering för att hantera Azure Resource Manager resurser eller resurser som distribueras på den klassiska distributionsmodellen. Det finns två typer av Kör som-konton i Azure Automation:

* Kör som-konto i Azure: Gör att du kan hantera Azure-resurser baserat på Azure Resource Manager och hanteringstjänst för Azure.
* Klassiskt Kör som-konto i Azure: Gör att du kan hantera klassiska Azure-resurser baserat på den klassiska distributionsmodellen.

Mer information om de Azure Resource Manager och klassiska distributionsmodellerna finns i [Resource Manager och klassisk distribution.](../azure-resource-manager/management/deployment-models.md)

>[!NOTE]
>Azure-molnlösningsleverantör prenumerationer (CSP) stöder endast Azure Resource Manager modellen. Icke-Azure Resource Manager tjänster är inte tillgängliga i programmet. När du använder en CSP-prenumeration skapas inte det klassiska Kör som-kontot i Azure, men Azure Kör som-kontot skapas. Mer information om CSP-prenumerationer finns i [Tillgängliga tjänster i CSP-prenumerationer.](/azure/cloud-solution-provider/overview/azure-csp-available-services)

När du skapar ett Automation-konto skapas Kör som-kontot som standard samtidigt. Om du väljer att inte skapa det tillsammans med Automation-kontot kan det skapas individuellt vid ett senare tillfälle. Ett klassiskt Kör som-konto i Azure är valfritt och skapas separat om du behöver hantera klassiska resurser.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWwtF3]

### <a name="run-as-account"></a>Kör som-konto

När du skapar ett Kör som-konto utförs följande uppgifter:

* Skapar ett Azure AD-program med ett själv signerat certifikat, skapar ett tjänsthuvudnamnskonto för programmet i Azure AD och tilldelar rollen Deltagare för kontot i din aktuella prenumeration. [](../role-based-access-control/built-in-roles.md#contributor) Du kan ändra certifikatinställningen till [Läsare eller](../role-based-access-control/built-in-roles.md#reader) någon annan roll. Mer information finns i [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).

* Skapar en Automation-certifikattillgång med namnet `AzureRunAsCertificate` i det angivna Automation-kontot. Certifikattillgången innehåller den privata certifikatnyckel som används av Azure AD-programmet.

* Skapar en Automation-anslutningstillgång med namnet `AzureRunAsConnection` i det angivna Automation-kontot. Anslutningstillgången innehåller program-ID, klientorganisations-ID, prenumerations-ID och tumavtryck för certifikat.

### <a name="azure-classic-run-as-account"></a>Klassiskt Kör som-konto i Azure

När du skapar ett klassiskt Kör som-konto i Azure utförs följande uppgifter:

> [!NOTE]
> Du måste vara medadministratör för prenumerationen för att kunna skapa eller förnya den här typen av Kör som-konto.

* Skapar ett hanteringscertifikat i prenumerationen.

* Skapar en Automation-certifikattillgång med namnet `AzureClassicRunAsCertificate` i det angivna Automation-kontot. Certifikattillgången innehåller den privata nyckelns certifikat som används av hanteringscertifikatet.

* Skapar en Automation-anslutningstillgång med namnet `AzureClassicRunAsConnection` i det angivna Automation-kontot. Anslutningstillgången innehåller prenumerationsnamnet, prenumerations-ID:t och certifikatets tillgångsnamn.

## <a name="service-principal-for-run-as-account"></a>Tjänstens huvudnamn för Kör som-konto

Tjänstens huvudnamn för ett Kör som-konto har inte behörighet att läsa Azure AD som standard. Om du vill lägga till behörigheter för att läsa eller hantera Azure AD måste du bevilja behörigheter för tjänstens huvudnamn under **API-behörigheter**. Mer information finns i Lägga [till behörigheter för åtkomst till ditt webb-API.](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Kör som-kontobehörigheter

Det här avsnittet definierar behörigheter för både vanliga Kör som-konton och klassiska Kör som-konton.

* Om du vill skapa eller uppdatera ett Kör som-konto Programadministratör en Azure Active Directory och en ägare i prenumerationen slutföra alla uppgifter.
* Om du vill konfigurera eller förnya klassiska Kör som-konton måste du ha rollen som medadministratör på prenumerationsnivå. Mer information om behörigheter för klassiska prenumerationer finns i [Administratörer för klassiska Azure-prenumerationer.](../role-based-access-control/classic-administrators.md#add-a-co-administrator)

I en situation där du har uppdelning av uppgifter visar följande tabell en lista över uppgifterna, motsvarande cmdlet och de behörigheter som krävs:

|Uppgift|Cmdlet  |Minsta behörigheter  |Där du anger behörigheterna|
|---|---------|---------|---|
|Skapa Azure AD-program|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Programutvecklarroll<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > appregistreringar |
|Lägg till en autentiseringsidentifiering i programmet.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Programadministratör eller global administratör<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > appregistreringar|
|Skapa och hämta tjänstens huvudnamn för Azure AD|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Programadministratör eller global administratör<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > appregistreringar|
|Tilldela eller hämta Azure-rollen för det angivna huvudbeloppet|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Administratör eller ägare för användaråtkomst eller har följande behörigheter:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Prenumeration](../role-based-access-control/role-assignments-portal.md)</br>Start > Prenumerationer > \<subscription name\> – Access Control (IAM)|
|Skapa eller ta bort ett Automation-certifikat|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Deltagare i resursgrupp         |Resursgrupp för Automation-konto|
|Skapa eller ta bort en Automation-anslutning|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Deltagare i resursgrupp |Automation-kontoresursgrupp|

<sup>1</sup> Icke-administratörsanvändare i din Azure AD-klientorganisation kan  registrera [AD-program](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) om  alternativet Användare kan registrera program för Azure AD-klienten på sidan Användarinställningar är inställt på **Ja.** Om programregistreringsinställningen är **Nej** måste användaren som utför den här åtgärden vara enligt definitionen i den här tabellen.

Om du inte är medlem i prenumerationens Active Directory-instans innan du läggs till i rollen Global administratör för prenumerationen läggs du till som gäst. I så fall får du en `You do not have permissions to create…` varning på sidan Lägg till **Automation-konto.**

Så här kontrollerar du att situationen som producerar felmeddelandet har åtgärdats:

1. I fönstret Azure Active Directory i Azure Portal väljer du **Användare och grupper**.
2. Välj **Alla användare.**
3. Välj ditt namn och välj sedan **Profil.**
4. Kontrollera att värdet för **attributet Användartyp** under användarens profil inte är inställt på **Gäst**.

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Rollbaserad åtkomstkontroll är tillgänglig med Azure Resource Manager att bevilja tillåtna åtgärder till ett Azure AD-användarkonto och Kör som-konto, samt autentisera tjänstens huvudnamn. Mer information om hur du utvecklar din modell för att hantera Automation-behörigheter finns i artikeln [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).

Om du har strikta säkerhetskontroller för behörighetstilldelning i resursgrupper måste du tilldela Kör som-kontomedlemskap **till rollen** Deltagare i resursgruppen.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Runbook-autentisering med Hybrid Runbook Worker

Runbooks som körs på en Hybrid Runbook Worker i ditt datacenter eller mot databehandlingstjänster i andra molnmiljöer som AWS kan inte använda samma metod som vanligtvis används för runbooks som autentiserar till Azure-resurser. Detta beror på att resurserna körs utanför Azure och därför kräver sina egna säkerhetsreferenser i Automation för att autentisera mot resurser som de ska komma åt lokalt. Mer information om runbook-autentisering med Runbook Workers finns [i Run runbooks on a Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

För runbooks som använder Hybrid Runbook Workers på virtuella Azure-datorer kan du använda [runbook-autentisering](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) med hanterade identiteter i stället för Kör som-konton för att autentisera till dina Azure-resurser.

## <a name="next-steps"></a>Nästa steg

* Information om hur du skapar ett Automation-Azure Portal finns i [Skapa ett fristående Azure Automation konto.](automation-create-standalone-account.md)
* Om du vill skapa ett konto med hjälp av en mall kan du gå [till Skapa ett Automation-konto med en Azure Resource Manager mall](quickstart-create-automation-account-template.md).
* Information om autentisering med Amazon Web Services finns [i Autentisera runbooks med Amazon Web Services](automation-config-aws-account.md).
* En lista över Azure-tjänster som stöder funktionen Hanterade identiteter för Azure-resurser finns i [Tjänster som stöder hanterade identiteter för Azure-resurser](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities).
