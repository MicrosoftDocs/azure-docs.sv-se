---
title: Tilldela roller till Azure Enterprise-avtal tjänstens huvud namn
description: 'Den här artikeln hjälper dig att tilldela roller till tjänstens huvud namn med PowerShell-och REST-API: er.'
author: bandersmsft
ms.reviewer: ruturajd
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/07/2021
ms.author: banders
ms.openlocfilehash: 0f30c90bf81a837b1e78ca5f91450cf085cc91bc
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102495146"
---
# <a name="assign-roles-to-azure-enterprise-agreement-service-principal-names"></a>Tilldela roller till Azure Enterprise-avtal tjänstens huvud namn

Du kan hantera din Enterprise-avtal (EA)-registrering i [Azure Enterprise Portal](https://ea.azure.com/). Du kan skapa olika roller för att hantera din organisation, Visa kostnader och skapa prenumerationer. Den här artikeln hjälper dig att automatisera vissa av dessa uppgifter med hjälp av Azure PowerShell och REST-API: er med tjänstens huvud namn (SPN).

Innan du börjar ska du se till att du är bekant med följande artiklar:

- [Enterprise Agreement-roller](understand-ea-roles.md)
- [Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-5.5.0&preserve-view=true)
- [Så här anropar du REST-API: er med Postman](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

## <a name="create-and-authenticate-your-service-principal"></a>Skapa och autentisera ditt tjänst huvud namn

Om du vill automatisera EA-åtgärder med hjälp av ett SPN måste du skapa ett Azure Active Directory-program (Azure AD). Den kan autentiseras på ett automatiserat sätt. Läs följande artiklar och följ stegen i dem för att skapa och autentisera tjänstens huvud namn.

1. [Skapa ett huvudnamn för tjänsten](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. [Hämta klient-och app-ID-värden för inloggning](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)

Här är ett exempel på en skärm bild som visar program registrering.

:::image type="content" source="./media/assign-roles-azure-service-principals/register-an-application.png" alt-text="Skärm bild som visar registrera ett program." lightbox="./media/assign-roles-azure-service-principals/register-an-application.png" :::

### <a name="find-your-spn-and-tenant-id"></a>Hitta ditt SPN och klient-ID

Du behöver också objekt-ID för SPN och klient-ID för appen. Du behöver information om behörighets tilldelnings åtgärder i senare avsnitt.

Du hittar klient-ID: t för Azure AD-appen på översikts sidan för programmet. Du hittar den i Azure Portal genom att gå till Azure Active Directory och välja **företags program**. Sök efter appen.

:::image type="content" source="./media/assign-roles-azure-service-principals/enterprise-application.png" alt-text="Skärm bild som visar ett exempel på ett företags program." lightbox="./media/assign-roles-azure-service-principals/enterprise-application.png" :::

Välj appen. Här är ett exempel som visar program-ID och objekt-ID.

:::image type="content" source="./media/assign-roles-azure-service-principals/application-id-object-id.png" alt-text="Skärm bild som visar ett program-ID och objekt-ID för ett företags program." lightbox="./media/assign-roles-azure-service-principals/application-id-object-id.png" :::

Du hittar klient-ID: t på sidan Microsoft Azure AD översikt.

:::image type="content" source="./media/assign-roles-azure-service-principals/tenant-id.png" alt-text="Skärm bild som visar var du kan visa klient-ID: t." lightbox="./media/assign-roles-azure-service-principals/tenant-id.png" :::

Ditt huvud klient-ID kallas även huvud-ID, SPN och objekt-ID på olika platser. Värdet för ditt Azure AD-klient-ID ser ut som ett GUID med följande format: `11111111-1111-1111-1111-111111111111` .

## <a name="permissions-that-can-be-assigned-to-the-spn"></a>Behörigheter som kan tilldelas SPN

I nästa steg ger du behörighet till Azure AD-appen för att utföra åtgärder med hjälp av en EA-roll. Du kan endast tilldela följande roller till SPN. Roll Definitions-ID: t, exakt som det visas, används senare i tilldelnings stegen.

| Roll | Tillåtna åtgärder | Roll Definitions-ID |
| --- | --- | --- |
| EnrollmentReader | Kan visa användning och avgifter för alla konton och prenumerationer. Kan visa Azure-förskotts betalning (tidigare kallat betalnings åtagande) som är kopplad till registreringen. | 24f8edb6-1668-4659-b5e2-40bb5f3a7d7e |
| DepartmentReader | Hämta användnings informationen för den avdelning som de administrerar. Kan visa användning och avgifter som är kopplade till deras avdelning. | db609904-a47f-4794-9be8-9bd86fbffd8a |
| SubscriptionCreator | Skapa nya prenumerationer inom det aktuella konto omfånget. | a0bcee42-bf30-4d1b-926a-48d21664ef71 |

- En registrerings läsare kan bara tilldelas ett tjänst huvud namn av en användare med rollen registrerings skrivare.
- En avdelnings läsare kan bara tilldelas ett SPN av en användare som har rollen som registrerings skrivare eller rollen som avdelnings skrivare.
- En prenumerations skapare roll kan bara tilldelas ett tjänst huvud namn av en användare som är konto ägare till registrerings kontot.

## <a name="assign-enrollment-account-role-permission-to-the-spn"></a>Tilldela behörighet för registrerings konto rollen till SPN

Läs REST API [-artikeln om roll tilldelningar](/rest/api/billing/2019-10-01-preview/roleassignments/put) .

När du läser artikeln väljer du **försök** att komma igång med SPN.

:::image type="content" source="./media/assign-roles-azure-service-principals/put-try-it.png" alt-text="Skärm bild som visar alternativet prova i artikel." lightbox="./media/assign-roles-azure-service-principals/put-try-it.png" :::

Logga in med ditt konto i den klient som har åtkomst till den registrering där du vill tilldela åtkomst.

Ange följande parametrar som en del av API-begäran.

**billingAccountName**

Parametern är fakturerings kontots ID. Du hittar den i Azure Portal på sidan för Cost Management + fakturerings översikt.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Skärm bild som visar fakturerings konto-ID." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

Parametern är ett unikt GUID som du måste ange. Du kan generera en GUID med PowerShell [-kommandot New-GUID](/powershell/module/microsoft.powershell.utility/new-guid?view=powershell-7.1&preserve-view=true) .

Du kan också använda webbplatsen [online-GUID/UUID-Generator](https://guidgenerator.com/) för att generera ett unikt GUID.

**API-version**

Använd **2019-10-01-Preview-** versionen.

Begär ande texten innehåller en JSON-kod som du måste använda.

Använd exempel texten för begäran i [roll tilldelningar – Lägg-exempel](/rest/api/billing/2019-10-01-preview/roleassignments/put#examples).

Det finns tre parametrar som du måste använda som en del av JSON.

| Parameter | Var du hittar den |
| --- | --- |
| egenskaper. principalId | Se [hitta ditt SPN och klient-ID](#find-your-spn-and-tenant-id). |
| egenskaper. principalTenantId | Se [hitta ditt SPN och klient-ID](#find-your-spn-and-tenant-id). |
| egenskaper. roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/24f8edb6-1668-4659-b5e2-40bb5f3a7d7e" |

Namnet på fakturerings kontot är samma parameter som du använde i API-parametrarna. Det är registrerings-ID: t som du ser i EA-portalen och Azure Portal.

Observera att `24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` är ett Definitions-ID för en fakturerings roll för en EnrollmentReader.

Starta kommandot genom att välja **Kör** .

:::image type="content" source="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" alt-text="Skärm bild som visar ett exempel på roll tilldelningen prova med exempel information som är redo att köras." lightbox="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" :::

Ett `200 OK` svar visar att SPN har lagts till.

Nu kan du använda SPN (Azure AD App med objekt-ID) för att få åtkomst till EA-API: er på ett automatiserat sätt. SPN har rollen EnrollmentReader.

## <a name="assign-the-department-reader-role-to-the-spn"></a>Tilldela rollen som avdelnings läsare till SPN

Innan du börjar läser du [roll tilldelningarna för registrerings avdelningen – lägg](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put) REST API artikel.

När du läser artikeln väljer du **prova**.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Skärm bild som visar alternativet prova på registrerings avdelnings roll tilldelningar Lägg till artikel." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

Logga in med ditt konto i den klient som har åtkomst till den registrering där du vill tilldela åtkomst.

Ange följande parametrar som en del av API-begäran.

**billingAccountName**

Det är fakturerings kontots ID. Du hittar den i Azure Portal på sidan för Cost Management + fakturerings översikt.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Skärm bild som visar fakturerings konto-ID." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

Parametern är ett unikt GUID som du måste ange. Du kan generera en GUID med PowerShell [-kommandot New-GUID](/powershell/module/microsoft.powershell.utility/new-guid?view=powershell-7.1&preserve-view=true) .

Du kan också använda webbplatsen [online-GUID/UUID-Generator](https://guidgenerator.com/) för att generera ett unikt GUID.

**departmentName**

Det är avdelnings-ID: t. Du kan se avdelnings-ID: n i Azure Portal. Navigera till Cost Management + fakturering > **avdelningar**.

I det här exemplet använde vi ACE-avdelningen. ID: t för exemplet är `84819` .

:::image type="content" source="./media/assign-roles-azure-service-principals/department-id.png" alt-text="Skärm bild som visar ett exempel på avdelnings-ID." lightbox="./media/assign-roles-azure-service-principals/department-id.png" :::

**API-version**

Använd **2019-10-01-Preview-** versionen.

Begär ande texten innehåller en JSON-kod som du måste använda.

Använd exemplet vid [registrering av avdelnings roll tilldelningar – Lägg](/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put)till. Det finns tre parametrar som du måste använda som en del av JSON.

| Parameter | Var du hittar den |
| --- | --- |
| egenskaper. principalId | Se [hitta ditt SPN och klient-ID](#find-your-spn-and-tenant-id). |
| egenskaper. principalTenantId | Se [hitta ditt SPN och klient-ID](#find-your-spn-and-tenant-id). |
| egenskaper. roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/db609904-a47f-4794-9be8-9bd86fbffd8a" |

Namnet på fakturerings kontot är samma parameter som du använde i API-parametrarna. Det är registrerings-ID: t som du ser i EA-portalen och Azure Portal.

Definitions-ID för fakturerings roll för `db609904-a47f-4794-9be8-9bd86fbffd8a` en avdelnings läsare.

Starta kommandot genom att välja **Kör** .

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" alt-text="Skärm bild som visar ett exempel på registrering av avdelnings roll tilldelningar – Lägg resten prova med exempel information som är redo att köra." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" :::

Ett `200 OK` svar visar att SPN har lagts till.

Nu kan du använda SPN (Azure AD App med objekt-ID) för att få åtkomst till EA-API: er på ett automatiserat sätt. SPN har rollen DepartmentReader.

## <a name="assign-the-subscription-creator-role-to-the-spn"></a>Tilldela rollen för prenumerations skapare till SPN

Läs artikeln om [roll tilldelningar för registrerings konto – Lägg](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put) till.

När du läser det väljer du **prova** att tilldela prenumerationens skapare rollen till SPN.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Skärm bild som visar alternativet prova i Lägg till roll tilldelningar för registrerings kontot." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

Logga in med ditt konto i den klient som har åtkomst till den registrering där du vill tilldela åtkomst.

Ange följande parametrar som en del av API-begäran. Läs artikeln på [roll tilldelningar för registrerings konto – Lägg till URI-parametrar](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put#uri-parameters).

**billingAccountName**

Parametern är fakturerings kontots ID. Du hittar den i Azure Portal på sidan för Cost Management + fakturerings översikt.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Skärm bild som visar fakturerings konto-ID." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

Parametern är ett unikt GUID som du måste ange. Du kan generera en GUID med PowerShell [-kommandot New-GUID](/powershell/module/microsoft.powershell.utility/new-guid?view=powershell-7.1&preserve-view=true) .

Du kan också använda webbplatsen [online-GUID/UUID-Generator](https://guidgenerator.com/) för att generera ett unikt GUID.
**enrollmentAccountName**

Parametern är konto-ID. Hitta konto-ID: t för konto namnet i Azure Portal i Cost Management + fakturering i omfånget för registrering och avdelning.

I det här exemplet använde vi test kontot GTM. ID: t är `196987` .

:::image type="content" source="./media/assign-roles-azure-service-principals/account-id.png" alt-text="Skärm bild som visar konto-ID." lightbox="./media/assign-roles-azure-service-principals/account-id.png" :::

**API-version**

Använd **2019-10-01-Preview-** versionen.

Begär ande texten innehåller en JSON-kod som du måste använda.

Använd exemplet vid [registrerings avdelningen roll tilldelningar – Lägg-exempel](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put#putenrollmentdepartmentadministratorroleassignment).

Det finns tre parametrar som du måste använda som en del av JSON.

| Parameter | Var du hittar den |
| --- | --- |
| egenskaper. principalId | Se [hitta ditt SPN och klient-ID](#find-your-spn-and-tenant-id). |
| egenskaper. principalTenantId | Se [hitta ditt SPN och klient-ID](#find-your-spn-and-tenant-id). |
| egenskaper. roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountID}/enrollmentAccounts/196987/billingRoleDefinitions/a0bcee42-bf30-4d1b-926a-48d21664ef71" |

Namnet på fakturerings kontot är samma parameter som du använde i API-parametrarna. Det är registrerings-ID: t som du ser i EA-portalen och Azure Portal.

Definitions-ID för fakturerings roll för `a0bcee42-bf30-4d1b-926a-48d21664ef71` prenumerationens skapare roll.

Starta kommandot genom att välja **Kör** .

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" alt-text="Skärm bild som visar alternativet prova i roll tilldelningar för registrerings konto – Lägg till artikel" lightbox="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" :::

Ett `200 OK` svar visar att SPN har lagts till.

Nu kan du använda SPN (Azure AD App med objekt-ID) för att få åtkomst till EA-API: er på ett automatiserat sätt. SPN har rollen SubscriptionCreator.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Administration av Azure EA-portalen](ea-portal-administration.md).