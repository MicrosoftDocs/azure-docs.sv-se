---
title: Autentisera ett program för att få åtkomst till Azure SignalR-tjänsten
description: Den här artikeln innehåller information om hur du autentiserar ett program med Azure Active Directory för att få åtkomst till Azure SignalR-tjänsten
author: terencefan
ms.author: tefa
ms.service: signalr
ms.topic: conceptual
ms.date: 08/03/2020
ms.openlocfilehash: 597b69c1180ea1fb2a6812d648f8b8ad37707d66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092613"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-azure-signalr-service"></a>Autentisera ett program med Azure Active Directory för att få åtkomst till Azure SignalR-tjänsten
Microsoft Azure ger integrerad åtkomst kontroll hantering för resurser och program baserat på Azure Active Directory (Azure AD). En viktig fördel med att använda Azure AD med Azure SignalR service är att du inte behöver lagra dina autentiseringsuppgifter i koden längre. I stället kan du begära en OAuth 2,0-åtkomsttoken från Microsoft Identity Platform. Resurs namnet för att begära en token är `https://signalr.azure.com/` . Azure AD autentiserar säkerhets objekt (ett program, en resurs grupp eller ett huvud namn för tjänsten) som kör programmet. Om autentiseringen lyckas returnerar Azure AD en åtkomsttoken till programmet och programmet kan sedan använda åtkomsttoken för att auktorisera begäran till Azure SignalR service-resurser.

När en roll tilldelas till ett säkerhets objekt i Azure AD ger Azure åtkomst till dessa resurser för säkerhets objekt. Åtkomst kan begränsas till prenumerations nivån, resurs gruppen eller Azure SignalR-resursen. En Azure AD-säkerhet kan tilldela roller till en användare, en grupp, ett huvud namn för program tjänsten eller en [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md). 

> [!NOTE]
> En rolldefinition är en samling behörigheter. Rollbaserad åtkomst kontroll (RBAC) styr hur dessa behörigheter tillämpas via roll tilldelning. En rolltilldelning består av tre delar: säkerhetsobjekt, rolldefinition och omfång. Mer information finns i [förstå de olika rollerna](../role-based-access-control/overview.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrera ditt program med en Azure AD-klient
Det första steget i att använda Azure AD för att auktorisera Azure SignalR service-resurser är att registrera ditt program med en Azure AD-klient från [Azure Portal](https://portal.azure.com/). När du registrerar ditt program anger du information om programmet till AD. Azure AD tillhandahåller sedan ett klient-ID (kallas även ett program-ID) som du kan använda för att associera ditt program med Azure AD Runtime. Mer information om klient-ID finns i [program-och tjänst huvud objekt i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Följande bilder visar steg för att registrera ett webb program:

![Registrera ett program](./media/authenticate/app-registrations-register.png)

> [!Note]
> Om du registrerar ditt program som ett internt program kan du ange en giltig URI för omdirigerings-URI: n. För interna program behöver det här värdet inte vara en riktig URL. För webb program måste omdirigerings-URI: n vara en giltig URI, eftersom den anger den URL till vilken tokens anges.

När du har registrerat ditt program visas **program-ID: t (klient)** under **Inställningar**:

![Program-ID för det registrerade programmet](./media/authenticate/application-id.png)

Mer information om hur du registrerar ett program med Azure AD finns i [integrera program med Azure Active Directory](../active-directory/develop/quickstart-register-app.md).


### <a name="create-a-client-secret"></a>Skapa en klient hemlighet   
Programmet behöver en klient hemlighet för att bevisa sin identitet när en token begärs. Följ dessa steg om du vill lägga till klient hemligheten.

1. Navigera till din app-registrering i Azure Portal.
1. Välj inställningen för **certifikat & hemligheter** .
1. Under **klient hemligheter** väljer du **ny klient hemlighet** för att skapa en ny hemlighet.
1. Ange en beskrivning av hemligheten och välj önskat utgångs intervall.
1. Kopiera omedelbart värdet för den nya hemligheten till en säker plats. Fill-värdet visas bara en gång.

![Skapa en klient hemlighet](./media/authenticate/client-secret.png)

### <a name="upload-a-certificate"></a>Ladda upp ett certifikat

Du kan också ladda upp en certifiering i stället för att skapa en klient hemlighet.

![Ladda upp en certifiering](./media/authenticate/certification.png)

## <a name="assign-azure-roles-using-the-azure-portal"></a>Tilldela Azure-roller med hjälp av Azure Portal  
Mer information om hur du hanterar åtkomst till Azure-resurser med hjälp av Azure RBAC och Azure Portal finns i [den här artikeln](..//role-based-access-control/role-assignments-portal.md). 

När du har bestämt lämplig omfattning för en roll tilldelning navigerar du till den resursen i Azure Portal. Visa inställningarna för åtkomst kontroll (IAM) för resursen och följ de här anvisningarna för att hantera roll tilldelningar:

1. I [Azure Portal](https://portal.azure.com/)navigerar du till din signal resurs.
1. Välj **Access Control (IAM)** om du vill visa inställningar för åtkomst kontroll för Azure-signaleraren. 
1. Välj fliken **roll tilldelningar** om du vill se en lista över roll tilldelningar. Välj knappen **Lägg till** i verktygsfältet och välj sedan **Lägg till roll tilldelning**. 

    ![Knappen Lägg till i verktygsfältet](./media/authenticate/role-assignments-add-button.png)

1. Utför följande steg på sidan **Lägg till roll tilldelning** :
    1. Välj den **Azure SignalR-roll** som du vill tilldela. 
    1. Sök efter **säkerhets objekt** (användare, grupp, tjänstens huvud namn) som du vill tilldela rollen.
    1. Spara roll tilldelningen genom att välja **Spara** . 

        ![Tilldela en roll till ett program](./media/authenticate/assign-role-to-application.png)

    1. Identiteten som du har tilldelat rollen visas i listan under den rollen. Följande bild visar till exempel programmet `signalr-dev` och `signalr-service` finns i App Server-rollen för signalerare. 
        
        ![Roll tilldelnings lista](./media/authenticate/role-assignment-list.png)

Du kan följa liknande steg för att tilldela en roll som är begränsad till resurs grupp eller prenumeration. När du definierar rollen och dess omfattning kan du testa det här beteendet med exempel [på den här GitHub-platsen](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac).

## <a name="sample-codes-while-configuring-your-app-server"></a>Exempel koder när du konfigurerar din app server.

Lägg till följande alternativ när `AddAzureSignalR` :

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>";
});
```

Eller konfigurera bara `ConnectionString` i `appsettings.json` filen.

```json
{
"Azure": {
    "SignalR": {
      "Enabled": true,
      "ConnectionString": "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>"
    }
  },
}
```

När du använder `certificate` ändrar du `clientSecret` till så `clientCert` här:

```C#
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientCert=<clientCertFilepath>;tenantId=<tenantId>";
```

## <a name="next-steps"></a>Nästa steg
- Mer information om RBAC finns i [Vad är Azures rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md)?
- Information om hur du tilldelar och hanterar roll tilldelningar i Azure med Azure PowerShell, Azure CLI eller REST API finns i följande artiklar:
    - [Hantera rollbaserad åtkomst kontroll (RBAC) med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Hantera rollbaserad åtkomst kontroll (RBAC) med Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Hantera rollbaserad åtkomst kontroll (RBAC) med REST API](../role-based-access-control/role-assignments-rest.md)
    - [Hantera rollbaserad åtkomst kontroll (RBAC) med Azure Resource Manager mallar](../role-based-access-control/role-assignments-template.md)

Se följande relaterade artiklar:
- [Autentisera en hanterad identitet med Azure Active Directory åtkomst till Azure SignalR service](authenticate-managed-identity.md)
- [Ge åtkomst till Azure SignalR service med hjälp av Azure Active Directory](authorize-access-azure-active-directory.md)