---
title: Autentisera ett program för att få åtkomst till Azure Service Bus entiteter
description: Den här artikeln innehåller information om att autentisera ett program med Azure Active Directory åtkomst till Azure Service Bus entiteter (köer, ämnen osv.)
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: c4e19c0ab26d491ba0b95159e274383431aefaee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518236"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Autentisera och auktorisera ett program med Azure Active Directory åtkomst till Azure Service Bus entiteter
Azure Service Bus stöder användning av Azure Active Directory (Azure AD) för att auktorisera begär anden till Service Bus entiteter (köer, ämnen, prenumerationer eller filter). Med Azure AD kan du använda rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att ge behörighet till ett säkerhets objekt, som kan vara en användare, grupp eller ett program tjänst objekt. Mer information om roller och roll tilldelningar finns i [förstå de olika rollerna](../role-based-access-control/overview.md).

## <a name="overview"></a>Översikt
När ett säkerhets objekt (en användare, grupp eller ett program) försöker få åtkomst till en Service Bus entitet måste begäran vara auktoriserad. Med Azure AD är åtkomst till en resurs en två stegs process. 

 1. Först autentiseras säkerhets objektets identitet och en OAuth 2,0-token returneras. Resurs namnet för att begära en token är `https://servicebus.azure.net` .
 1. Därefter skickas token som en del av en begäran till tjänsten Service Bus för att ge åtkomst till den angivna resursen.

Autentiserings steget kräver att en programbegäran innehåller en OAuth 2,0-åtkomsttoken vid körning. Om ett program körs i en Azure-entitet, till exempel en virtuell Azure-dator, en skalnings uppsättning för virtuella datorer eller en Azure Function-app, kan den använda en hanterad identitet för att få åtkomst till resurserna. Information om hur du autentiserar begär Anden som görs av en hanterad identitet i Service Bus-tjänsten finns i [autentisera åtkomst till Azure Service Bus resurser med Azure Active Directory och hanterade identiteter för Azure-resurser](service-bus-managed-service-identity.md). 

Auktoriserings steget kräver att en eller flera Azure-roller tilldelas säkerhets objekt. Azure Service Bus tillhandahåller Azure-roller som omfattar uppsättningar med behörigheter för Service Bus resurser. Rollerna som tilldelas ett säkerhets objekt bestämmer vilka behörigheter som huvud kontot ska ha. Mer information om hur du tilldelar Azure-roller till Azure Service Bus finns i [inbyggda roller i Azure för Azure Service Bus](#azure-built-in-roles-for-azure-service-bus). 

Interna program och webb program som gör förfrågningar till Service Bus kan också auktoriseras med Azure AD. Den här artikeln visar hur du begär en åtkomsttoken och använder den för att auktorisera begär Anden för Service Bus resurser. 


## <a name="assigning-azure-roles-for-access-rights"></a>Tilldela Azure-roller för åtkomst rättigheter
Azure Active Directory (Azure AD) tillåter åtkomst rättigheter till skyddade resurser via [Azure RBAC](../role-based-access-control/overview.md). Azure Service Bus definierar en uppsättning inbyggda Azure-roller som omfattar vanliga uppsättningar med behörigheter som används för att komma åt Service Bus entiteter och du kan också definiera anpassade roller för åtkomst till data.

När en Azure-roll tilldelas till ett säkerhets objekt för Azure AD ger Azure åtkomst till dessa resurser för säkerhets objekt. Åtkomst kan begränsas till prenumerations nivån, resurs gruppen eller Service Bus namn området. Ett säkerhets objekt i Azure AD kan vara en användare, en grupp, ett huvud namn för program tjänsten eller en [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Inbyggda Azure-roller för Azure Service Bus
För Azure Service Bus är hanteringen av namn rymder och alla relaterade resurser via Azure Portal och Azure Resource Management-API: t redan skyddade med Azure RBAC-modellen. Azure tillhandahåller de följande inbyggda Azure-rollerna för att auktorisera åtkomst till en Service Bus namnrymd:

- [Azure Service Bus data ägare](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): aktiverar data åtkomst till Service Bus namnrymd och dess entiteter (köer, ämnen, prenumerationer och filter)
- [Azure Service Bus data avsändare](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Använd den här rollen för att ge Send access till Service Bus namnrymd och dess entiteter.
- [Azure Service Bus data mottagare](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Använd den här rollen för att ge åtkomst till Service Bus namnrymd och dess entiteter. 

## <a name="resource-scope"></a>Resursomfång 
Innan du tilldelar en Azure-roll till ett säkerhets objekt bör du bestämma omfattningen av åtkomsten som säkerhets objekt ska ha. Bästa praxis är att bestämma att det alltid är bäst att bara bevilja det begränsande möjliga omfånget.

I följande lista beskrivs de nivåer där du kan begränsa åtkomsten till Service Bus resurser, från och med det smala omfång:

- **Kö**, **ämne** eller **prenumeration**: roll tilldelningen gäller för den angivna Service Bus entiteten. För närvarande har Azure Portal inte stöd för att tilldela användare/grupper/hanterade identiteter till Service Bus Azure-roller på prenumerations nivån. 
- **Service Bus namnrymd**: roll tilldelningen sträcker sig över hela topologin av Service Bus under namn området och till den konsument grupp som är kopplad till den.
- **Resurs grupp**: roll tilldelningen gäller för alla Service Bus resurser under resurs gruppen.
- **Prenumeration**: roll tilldelningen gäller för alla Service Bus resurser i alla resurs grupper i prenumerationen.

> [!NOTE]
> Tänk på att det kan ta upp till fem minuter för Azure Role-tilldelningar att spridas. 

Mer information om hur inbyggda roller definieras finns i [förstå roll definitioner](../role-based-access-control/role-definitions.md#management-and-data-operations). Information om hur du skapar anpassade Azure-roller finns i [Azure-anpassade roller](../role-based-access-control/custom-roles.md).


## <a name="assign-azure-roles-using-the-azure-portal"></a>Tilldela Azure-roller med hjälp av Azure Portal  
Mer information om hur du hanterar åtkomst till Azure-resurser med hjälp av Azure RBAC och Azure Portal finns i [den här artikeln](..//role-based-access-control/role-assignments-portal.md). 

När du har bestämt lämplig omfattning för en roll tilldelning navigerar du till den resursen i Azure Portal. Visa inställningarna för åtkomst kontroll (IAM) för resursen och följ de här anvisningarna för att hantera roll tilldelningar:

> [!NOTE]
> De steg som beskrivs nedan tilldelar din Service Bus-namnrymd en roll. Du kan följa samma steg för att tilldela en roll till andra områden som stöds (resurs grupp, prenumeration osv.).

1. I [Azure Portal](https://portal.azure.com/)navigerar du till Service Bus namn området. Välj **Access Control (IAM)** på den vänstra menyn för att Visa inställningar för åtkomst kontroll för namn området. Om du behöver skapa ett Service Bus namnrum följer du anvisningarna i den här artikeln: [skapa ett namn område för en Service Bus-meddelande tjänst](service-bus-create-namespace-portal.md).

    ![Välj Access Control på den vänstra menyn](./media/authenticate-application/select-access-control-menu.png)
1. Välj fliken **roll tilldelningar** om du vill se en lista över roll tilldelningar. Välj knappen **Lägg till** i verktygsfältet och välj sedan **Lägg till roll tilldelning**. 

    ![Knappen Lägg till i verktygsfältet](./media/authenticate-application/role-assignments-add-button.png)
1. Utför följande steg på sidan **Lägg till roll tilldelning** :
    1. Välj den **Service Bus roll** som du vill tilldela. 
    1. Sök efter **säkerhets objekt** (användare, grupp, tjänstens huvud namn) som du vill tilldela rollen.
    1. Spara roll tilldelningen genom att välja **Spara** . 

        ![Tilldela en roll till en användare](./media/authenticate-application/assign-role-to-user.png)
    4. Identiteten som du har tilldelat rollen visas i listan under den rollen. Följande bild visar till exempel att Azure-användare är i rollen Azure Service Bus data ägare. 
        
        ![Användare i listan](./media/authenticate-application/user-in-list.png)

Du kan följa liknande steg för att tilldela en roll som är begränsad till en resurs grupp eller en prenumeration. När du definierar rollen och dess omfattning kan du testa det här beteendet med [exemplen på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).


## <a name="authenticate-from-an-application"></a>Autentisera från ett program
En viktig fördel med att använda Azure AD med Service Bus är att dina autentiseringsuppgifter inte längre behöver lagras i din kod. I stället kan du begära en OAuth 2,0-åtkomsttoken från Microsoft Identity Platform. Azure AD autentiserar säkerhets objekt (en användare, en grupp eller tjänstens huvud namn) som kör programmet. Om autentiseringen lyckas returnerar Azure AD åtkomsttoken till programmet och programmet kan sedan använda åtkomsttoken för att auktorisera begär anden till Azure Service Bus.

I följande avsnitt beskrivs hur du konfigurerar ditt egna program eller webb program för autentisering med Microsoft Identity Platform 2,0. Mer information om Microsoft Identity Platform 2,0 finns i [Översikt över Microsoft Identity Platform (v 2.0)](../active-directory/develop/v2-overview.md).

En översikt över OAuth 2,0 Code Granting Flow finns i bevilja [åtkomst till Azure Active Directory webb program med hjälp av OAuth-kod för kod beviljande i 2,0 OAuth](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrera ditt program med en Azure AD-klient
Det första steget i att använda Azure AD för att auktorisera Service Bus entiteter registrerar ditt klient program med en Azure AD-klient från [Azure Portal](https://portal.azure.com/). När du registrerar klient programmet anger du information om programmet till AD. Azure AD tillhandahåller sedan ett klient-ID (kallas även ett program-ID) som du kan använda för att associera ditt program med Azure AD Runtime. Mer information om klient-ID finns i [program-och tjänst huvud objekt i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Följande bilder visar steg för att registrera ett webb program:

![Registrera ett program](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Om du registrerar ditt program som ett internt program kan du ange en giltig URI för omdirigerings-URI: n. För interna program behöver det här värdet inte vara en riktig URL. För webb program måste omdirigerings-URI: n vara en giltig URI, eftersom den anger den URL till vilken tokens anges.

När du har registrerat ditt program visas **program-ID: t (klient)** under **Inställningar**:

![Program-ID för det registrerade programmet](./media/authenticate-application/application-id.png)

Mer information om hur du registrerar ett program med Azure AD finns i [integrera program med Azure Active Directory](../active-directory/develop/quickstart-register-app.md).

> [!IMPORTANT]
> Anteckna **TenantId** och **ApplicationId**. Du behöver dessa värden för att köra programmet.

### <a name="create-a-client-secret"></a>Skapa en klient hemlighet   
Programmet behöver en klient hemlighet för att bevisa sin identitet när en token begärs. Följ dessa steg om du vill lägga till klient hemligheten.

1. Navigera till din app-registrering i Azure Portal om du inte redan är på sidan.
1. Välj **certifikat & hemligheter** på den vänstra menyn.
1. Under **klient hemligheter** väljer du **ny klient hemlighet** för att skapa en ny hemlighet.

    ![Ny klient hemlighet – knapp](./media/authenticate-application/new-client-secret-button.png)
1. Ange en beskrivning av hemligheten och välj önskat utgångs intervall och välj sedan **Lägg till**.

    ![Sidan Lägg till klient hemlighet](./media/authenticate-application/add-client-secret-page.png)
1. Kopiera omedelbart värdet för den nya hemligheten till en säker plats. Fill-värdet visas bara en gång.

    ![Klienthemlighet](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Behörigheter för Service Bus API
Om ditt program är ett konsol program måste du registrera ett inbyggt program och lägga till API-behörigheter för **Microsoft. Service Bus** i de **behörigheter som krävs** . Inbyggda program behöver också en **omdirigerings-URI** i Azure AD, som fungerar som en identifierare. URI: n behöver inte vara ett nätverks mål. Används `https://servicebus.microsoft.com` i det här exemplet, eftersom exempel koden redan använder denna URI.

### <a name="client-libraries-for-token-acquisition"></a>Klient bibliotek för hämtning av token  
När du har registrerat ditt program och gett IT-behörighet att skicka/ta emot data i Azure Service Bus kan du lägga till kod i programmet för att autentisera ett säkerhets objekt och hämta OAuth 2,0-token. Om du vill autentisera och hämta token kan du använda antingen en av [Microsofts identitets plattforms bibliotek för autentisering](../active-directory/develop/reference-v2-libraries.md) eller ett annat bibliotek med öppen källkod som stöder OpenID eller Connect 1,0. Ditt program kan sedan använda åtkomsttoken för att auktorisera en begäran mot Azure Service Bus.

En lista över scenarier där det finns stöd för att hämta token finns i avsnittet [scenarier](https://aka.ms/msal-net-scenarios) i [Microsoft Authentication Library (MSAL) för .net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub-lagringsplatsen.

## <a name="sample-on-github"></a>Exempel på GitHub
Se följande exempel på GitHub: [rollbaserad åtkomst kontroll i Azure för Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl). 

Använd **inloggnings alternativet för klient hemlighet** , inte alternativet **interaktiv användar inloggning** . När du använder alternativet klient hemlighet visas inte ett popup-fönster. Programmet använder klient-ID och app-ID för autentisering. 

### <a name="run-the-sample"></a>Kör exemplet

Innan du kan köra exemplet redigerar du **app.config** -filen och, beroende på ditt scenario, anger du följande värden:

- `tenantId`: Ange till **TenantId** -värde.
- `clientId`: Ange som **ApplicationId** -värde.
- `clientSecret`: Om du vill logga in med hjälp av klient hemligheten skapar du den i Azure AD. Använd också en webbapp eller ett API i stället för en inbyggd app. Lägg också till appen under **Access Control (IAM)** i namn området som du skapade tidigare.
- `serviceBusNamespaceFQDN`: Ange till det fullständiga DNS-namnet för den nyligen skapade Service Bus namn området; till exempel `example.servicebus.windows.net` .
- `queueName`: Ange till namnet på kön som du skapade.
- Den omdirigerings-URI som du angav i din app i föregående steg.

När du kör konsol programmet uppmanas du att välja ett scenario. Välj **interaktiv användar inloggning** genom att skriva in dess nummer och trycka på RETUR. Programmet visar ett inloggnings fönster, ber om tillåtelse att komma åt Service Bus och använder sedan tjänsten för att köra via scenariot skicka/ta emot med inloggnings identiteten.


## <a name="next-steps"></a>Nästa steg
- Om du vill veta mer om Azure RBAC, se [Vad är Azures rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md)?
- Information om hur du tilldelar och hanterar roll tilldelningar i Azure med Azure PowerShell, Azure CLI eller REST API finns i följande artiklar:
    - [Lägga till eller ta bort Azure-rolltilldelningar med hjälp av Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Lägga till eller ta bort Azure-rolltilldelningar med hjälp av Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Lägga till eller ta bort Azure-rolltilldelningar med hjälp av REST API](../role-based-access-control/role-assignments-rest.md)
    - [Lägga till eller ta bort Azure Role-tilldelningar med Azure Resource Manager mallar](../role-based-access-control/role-assignments-template.md)

I följande ämnen kan du lära dig mer om Service Bus-meddelanden.

- [Service Bus Azure RBAC-exempel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
- [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
- [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)