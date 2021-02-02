---
title: Översikt över Azure Automation konto autentisering
description: Den här artikeln innehåller en översikt över Azure Automation autentisering av konton.
keywords: automation security, secure automation; automation authentication
services: automation
ms.subservice: process-automation
ms.date: 02/01/2021
ms.topic: conceptual
ms.openlocfilehash: e3ed14a0845d90a1b9b951dd5175acf6233f8718
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255007"
---
# <a name="automation-account-authentication-overview"></a>Översikt över autentisering av Automation-konto

Med Azure Automation kan du automatisera åtgärder mot resurser i Azure, lokalt och med andra molnproviders, till exempel Amazon Web Services (AWS). Du kan använda Runbooks för att automatisera dina uppgifter eller en Hybrid Runbook Worker om du har affärs-eller drift processer för att hantera utanför Azure. Att arbeta i någon av dessa miljöer kräver behörighet att säkert komma åt resurserna med minsta möjliga behörighet.

Den här artikeln beskriver de autentiseringsmetoder som stöds av Azure Automation och ger information om hur du kommer igång baserat på miljön eller miljöerna som du behöver hantera.

## <a name="automation-account"></a>Automation-konto

När du startar Azure Automation för första gången måste du skapa minst ett Automation-konto. Automation-konton gör att du kan isolera dina Automation-resurser, Runbooks, till gångar och konfigurationer från resurser för andra konton. Du kan använda Automation-konton för att avgränsa resurser i separata logiska miljöer eller delegerade ansvars områden. Du kan exempelvis använda ett konto för utveckling, ett annat för produktion och ett annat för din lokala miljö. Eller så kan du tilldela ett Automation-konto för att hantera uppdateringar av operativ system på alla dina datorer med [uppdateringshantering](update-management/overview.md). 

Ett Azure Automation-konto skiljer sig från ditt eller dina Microsoft-konton som skapats i Azure-prenumerationen. En introduktion till hur du skapar ett Automation-konto finns i [skapa ett Automation-konto](automation-quickstart-create-account.md).

## <a name="automation-resources"></a>Automation-resurser

Automation-resurserna för varje Automation-konto associeras med en enda Azure-region, men kontot kan hantera alla resurser i din Azure-prenumeration. Huvud anledningen till att skapa Automation-konton i olika regioner är om du har principer som kräver att data och resurser är isolerade i en särskild region.

Alla aktiviteter som du skapar mot resurser med hjälp av Azure Resource Manager och PowerShell-cmdletar i Azure Automation måste autentisera till Azure med hjälp av Azure Active Directory (Azure AD) autentisering baserad på autentiseringsuppgifter.

## <a name="run-as-accounts"></a>Kör som-konton

Kör som-konton i Azure Automation tillhandahålla autentisering för att hantera Azure Resource Manager resurser eller resurser som distribueras i den klassiska distributions modellen. Det finns två typer av kör som-konton i Azure Automation:

* Kör som-konto i Azure: gör att du kan hantera Azure-resurser baserat på Azure Resource Manager distribution och hantering av Azure.
* Azures klassiska kör som-konto: gör att du kan hantera klassiska Azure-resurser baserat på den klassiska distributions modellen.

Mer information om Azure Resource Manager och klassiska distributions modeller finns i [Resource Manager och klassisk distribution](../azure-resource-manager/management/deployment-models.md).

>[!NOTE]
>Azure Cloud Solution Provider (CSP)-prenumerationer stöder endast Azure Resource Managers modellen. Icke-Azure Resource Manager tjänster är inte tillgängliga i programmet. När du använder en CSP-prenumeration skapas inte det klassiska kör som-kontot i Azure, men kör som-kontot i Azure skapas. Mer information om CSP-prenumerationer finns i [tillgängliga tjänster i CSP-prenumerationer](/azure/cloud-solution-provider/overview/azure-csp-available-services).

När du skapar ett Automation-konto skapas kör som-kontot som standard på samma gång. Om du väljer att inte skapa den tillsammans med Automation-kontot kan du skapa en separat vid ett senare tillfälle. Ett klassiskt kör som-konto i Azure är valfritt och skapas separat om du behöver hantera klassiska resurser.

### <a name="run-as-account"></a>Kör som-konto

När du skapar ett Kör som-konto utför det följande uppgifter:

* Skapar ett Azure AD-program med ett självsignerat certifikat, skapar ett tjänst objekts konto för programmet i Azure AD och tilldelar rollen [deltagare](../role-based-access-control/built-in-roles.md#contributor) för kontot i din aktuella prenumeration. Du kan ändra certifikat inställningen till [läsare](../role-based-access-control/built-in-roles.md#reader) eller någon annan roll. Mer information finns i [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).

* Skapar en Automation-certifikat till gång med namnet `AzureRunAsCertificate` i det angivna Automation-kontot. Certifikat till gången innehåller certifikatets privata nyckel som Azure AD-programmet använder.

* Skapar en Automation-anslutning till gång med namnet `AzureRunAsConnection` i det angivna Automation-kontot. Anslutnings till gången innehåller program-ID, klient-ID, prenumerations-ID och tumavtryck för certifikatet.

### <a name="azure-classic-run-as-account"></a>Det klassiska kör som-kontot i Azure

När du skapar ett klassiskt kör som-konto i Azure utförs följande uppgifter:

> [!NOTE]
> Du måste vara en medadministratör i prenumerationen för att kunna skapa eller förnya den här typen av kör som-konto.

* Skapar ett hanterings certifikat i prenumerationen.

* Skapar en Automation-certifikat till gång med namnet `AzureClassicRunAsCertificate` i det angivna Automation-kontot. Certifikattillgången innehåller den privata nyckelns certifikat som används av hanteringscertifikatet.

* Skapar en Automation-anslutning till gång med namnet `AzureClassicRunAsConnection` i det angivna Automation-kontot. Anslutnings till gången innehåller prenumerations namnet, prenumerations-ID och certifikatets till gångs namn.

## <a name="service-principal-for-run-as-account"></a>Tjänstens huvud namn för kör som-konto

Tjänstens huvud namn för ett Kör som-konto har inte behörighet att läsa Azure AD som standard. Om du vill lägga till behörigheter för att läsa eller hantera Azure AD måste du bevilja behörighet för tjänstens huvud namn under **API-behörigheter**. Läs mer i [lägga till behörigheter för åtkomst till ditt webb-API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Behörigheter för kör som-konto

I det här avsnittet definieras behörigheter för både vanliga kör som-konton och klassiska kör som-konton.

* Om du vill skapa eller uppdatera ett Kör som-konto kan en program administratör i Azure Active Directory och en ägare i prenumerationen slutföra alla uppgifter.
* Om du vill konfigurera eller förnya klassiska kör som-konton måste du ha rollen medadministratör på prenumerations nivå. Mer information om klassiska prenumerations behörigheter finns i [Azures klassiska prenumerations administratörer](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

I en situation där du har separering av uppgifter visar följande tabell en lista över aktiviteter, motsvarande cmdlet och behörigheter som krävs:

|Uppgift|Cmdlet  |Lägsta behörighet  |Där du anger behörigheter|
|---|---------|---------|---|
|Skapa Azure AD-program|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Programutvecklare, roll<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > app-registreringar |
|Lägg till en autentiseringsuppgift i programmet.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Program administratör eller global administratör<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > app-registreringar|
|Skapa och hämta en Azure AD-tjänstens huvud namn|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Program administratör eller global administratör<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > app-registreringar|
|Tilldela eller hämta Azure-rollen för det angivna huvudobjektet|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Administratör för användar åtkomst eller ägare eller ha följande behörigheter:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Prenumeration](../role-based-access-control/role-assignments-portal.md)</br>Start > prenumerationer > \<subscription name\> -Access Control (IAM)|
|Skapa eller ta bort ett Automation-certifikat|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Deltagare i resurs gruppen         |Resurs grupp för Automation-konto|
|Skapa eller ta bort en Automation-anslutning|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Deltagare i resurs gruppen |Resurs grupp för Automation-konto|

<sup>1</sup> användare som inte är administratörer i din Azure AD-klient kan [registrera AD-program](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) om alternativet för Azure AD-klientens **användare kan registrera program** på sidan **användar inställningar** är inställt på **Ja**. Om program registrerings inställningen är **Nej** måste användaren som utför den här åtgärden vara som definieras i den här tabellen.

Om du inte är medlem i prenumerationens Active Directory instans innan du lägger till den globala administratörs rollen för prenumerationen läggs du till som gäst. I så fall får du en `You do not have permissions to create…` Varning på sidan **Lägg till Automation-konto** .

För att kontrol lera att den situation som genererar fel meddelandet har åtgärd ATS:

1. I fönstret Azure Active Directory i Azure Portal väljer du **användare och grupper**.
2. Välj **alla användare**.
3. Välj ditt namn och välj sedan **profil**.
4. Se till att värdet för attributet **användar typ** under användarens profil inte är inställt på **gäst**.

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Rollbaserad åtkomst kontroll är tillgänglig med Azure Resource Manager för att bevilja tillåtna åtgärder till ett Azure AD-användarkonto och kör som-konto och autentisera tjänstens huvud namn. Mer information om hur du utvecklar din modell för att hantera Automation-behörigheter finns i artikeln [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).

Om du har strikta säkerhets kontroller för behörighets tilldelning i resurs grupper måste du tilldela medlemskapet kör som-konto till **deltagar** rollen i resurs gruppen.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Runbook-autentisering med Hybrid Runbook Worker

Runbooks som körs på en Hybrid Runbook Worker i ditt data Center eller från data behandlings tjänster i andra moln miljöer som AWS, kan inte använda samma metod som används vanligt vis för Runbooks som autentiseras för Azure-resurser. Detta beror på att resurserna körs utanför Azure och därför kräver sina egna säkerhetsreferenser i Automation för att autentisera mot resurser som de ska komma åt lokalt. Mer information om Runbook-autentisering med Runbook Worker finns i [köra Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md).

För Runbooks som använder hybrid Runbook Worker på virtuella Azure-datorer kan du använda [Runbook-autentisering med hanterade identiteter](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) i stället för kör som-konton för att autentisera till dina Azure-resurser.

## <a name="next-steps"></a>Nästa steg

* Information om hur du skapar ett Automation-konto från Azure Portal finns i [skapa ett fristående Azure Automation-konto](automation-create-standalone-account.md).
* Om du föredrar att skapa ditt konto med hjälp av en mall kan du läsa [skapa ett Automation-konto med hjälp av en Azure Resource Manager-mall](quickstart-create-automation-account-template.md).
* För autentisering med hjälp av Amazon Web Services, se [autentisera Runbooks med Amazon Web Services](automation-config-aws-account.md).