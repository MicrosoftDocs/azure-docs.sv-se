---
title: Hanterade identiteter för Azure-resurser med Service Bus
description: Den här artikeln beskriver hur du använder hanterade identiteter för att få åtkomst till Azure Service Bus entiteter (köer, ämnen och prenumerationer).
ms.topic: article
ms.date: 01/21/2021
ms.openlocfilehash: 22be57a0108b6a8511a64165ad365675d006fb8f
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98808252"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Autentisera en hanterad identitet med Azure Active Directory för att få åtkomst till Azure Service Bus resurser
[Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) är en funktion i Azure som gör att du kan skapa en säker identitet som är kopplad till den distribution som program koden körs under. Du kan sedan associera identiteten med åtkomst kontroll roller som ger anpassade behörigheter för åtkomst till specifika Azure-resurser som ditt program behöver.

Med hanterade identiteter hanterar Azure-plattformen den här körnings identiteten. Du behöver inte lagra och skydda åtkomst nycklar i din program kod eller konfiguration, antingen för själva identiteten eller för de resurser som du behöver komma åt. En Service Bus-klient som körs i ett Azure App Service program eller på en virtuell dator med aktiverade hanterade entiteter för Azure-resurser behöver inte hantera SAS-regler och-nycklar eller andra åtkomsttoken. Klient programmet behöver bara slut punkts adressen för Service Bus meddelande namn rymden. När appen ansluter binder Service Bus den hanterade entitetens kontext till klienten i en åtgärd som visas i ett exempel senare i den här artikeln. När den är kopplad till en hanterad identitet kan din Service Bus-klient utföra alla behöriga åtgärder. Auktorisering beviljas genom att associera en hanterad entitet med Service Bus roller. 

## <a name="overview"></a>Översikt
När ett säkerhets objekt (en användare, grupp eller ett program) försöker få åtkomst till en Service Bus entitet måste begäran vara auktoriserad. Med Azure AD är åtkomst till en resurs en två stegs process. 

 1. Först autentiseras säkerhets objektets identitet och en OAuth 2,0-token returneras. Resurs namnet för att begära en token är `https://servicebus.azure.net` .
 1. Därefter skickas token som en del av en begäran till tjänsten Service Bus för att ge åtkomst till den angivna resursen.

Autentiserings steget kräver att en programbegäran innehåller en OAuth 2,0-åtkomsttoken vid körning. Om ett program körs i en Azure-entitet, till exempel en virtuell Azure-dator, en skalnings uppsättning för virtuella datorer eller en Azure Function-app, kan den använda en hanterad identitet för att få åtkomst till resurserna. 

Auktoriserings steget kräver att en eller flera Azure-roller tilldelas säkerhets objekt. Azure Service Bus tillhandahåller Azure-roller som omfattar uppsättningar med behörigheter för Service Bus resurser. Rollerna som tilldelas ett säkerhets objekt bestämmer vilka behörigheter som huvud kontot ska ha. Mer information om hur du tilldelar Azure-roller till Azure Service Bus finns i [inbyggda roller i Azure för Azure Service Bus](#azure-built-in-roles-for-azure-service-bus). 

Interna program och webb program som gör förfrågningar till Service Bus kan också auktoriseras med Azure AD. Den här artikeln visar hur du begär en åtkomsttoken och använder den för att auktorisera begär Anden för Service Bus resurser. 


## <a name="assigning-azure-roles-for-access-rights"></a>Tilldela Azure-roller för åtkomst rättigheter
Azure Active Directory (Azure AD) tillåter åtkomst rättigheter till skyddade resurser via [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md). Azure Service Bus definierar en uppsättning inbyggda Azure-roller som omfattar vanliga uppsättningar med behörigheter som används för att komma åt Service Bus entiteter och du kan också definiera anpassade roller för åtkomst till data.

När en Azure-roll tilldelas till ett säkerhets objekt för Azure AD ger Azure åtkomst till dessa resurser för säkerhets objekt. Åtkomst kan begränsas till prenumerations nivån, resurs gruppen eller Service Bus namn området. Ett säkerhets objekt i Azure AD kan vara en användare, en grupp, ett huvud namn för program tjänsten eller en hanterad identitet för Azure-resurser.

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Inbyggda Azure-roller för Azure Service Bus
För Azure Service Bus är hanteringen av namn rymder och alla relaterade resurser via Azure Portal och Azure Resource Management-API: t redan skyddade med Azure RBAC-modellen. Azure tillhandahåller de följande inbyggda Azure-rollerna för att auktorisera åtkomst till en Service Bus namnrymd:

- [Azure Service Bus data ägare](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): aktiverar data åtkomst till Service Bus namnrymd och dess entiteter (köer, ämnen, prenumerationer och filter)
- [Azure Service Bus data avsändare](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Använd den här rollen för att ge Send access till Service Bus namnrymd och dess entiteter.
- [Azure Service Bus data mottagare](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Använd den här rollen för att ge åtkomst till Service Bus namnrymd och dess entiteter. 

## <a name="resource-scope"></a>Resursomfång 
Innan du tilldelar en Azure-roll till ett säkerhets objekt bör du bestämma omfattningen av åtkomsten som säkerhets objekt ska ha. Bästa praxis är att bestämma att det alltid är bäst att bara bevilja det begränsande möjliga omfånget.

I följande lista beskrivs de nivåer där du kan begränsa åtkomsten till Service Bus resurser, från och med det smala omfång:

- **Kö**, **ämne** eller **prenumeration**: roll tilldelningen gäller för den angivna Service Bus entiteten. För närvarande har Azure Portal inte stöd för att tilldela användare/grupper/hanterade identiteter till Service Bus Azure-roller på prenumerations nivån. Här är ett exempel på hur du använder Azure CLI-kommandot: [AZ-Role-Assignment-Create](/cli/azure/role/assignment?#az-role-assignment-create) för att tilldela en identitet till en Service Bus Azure-roll: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Service Bus namnrymd**: roll tilldelningen sträcker sig över hela topologin av Service Bus under namn området och till den konsument grupp som är kopplad till den.
- **Resurs grupp**: roll tilldelningen gäller för alla Service Bus resurser under resurs gruppen.
- **Prenumeration**: roll tilldelningen gäller för alla Service Bus resurser i alla resurs grupper i prenumerationen.

> [!NOTE]
> Tänk på att det kan ta upp till fem minuter för Azure Role-tilldelningar att spridas. 

Mer information om hur inbyggda roller definieras finns i [förstå roll definitioner](../role-based-access-control/role-definitions.md#management-and-data-operations). Information om hur du skapar anpassade Azure-roller finns i [Azure-anpassade roller](../role-based-access-control/custom-roles.md).

## <a name="enable-managed-identities-on-a-vm"></a>Aktivera hanterade identiteter på en virtuell dator
Innan du kan använda hanterade identiteter för Azure-resurser för att auktorisera Service Bus resurser från din virtuella dator måste du först aktivera hanterade identiteter för Azure-resurser på den virtuella datorn. Information om hur du aktiverar hanterade identiteter för Azure-resurser finns i någon av följande artiklar:

- [Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mall](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager klient bibliotek](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Bevilja behörighet till en hanterad identitet i Azure AD
Om du vill auktorisera en begäran till Service Bus tjänsten från en hanterad identitet i ditt program måste du först konfigurera inställningar för Azure-rollbaserad åtkomst kontroll (Azure RBAC) för den hanterade identiteten. Azure Service Bus definierar Azure-roller som omfattar behörigheter för att skicka och läsa från Service Bus. När Azure-rollen tilldelas till en hanterad identitet beviljas den hanterade identiteten åtkomst till Service Bus entiteter i lämplig omfattning.

Mer information om hur du tilldelar Azure-roller finns i [autentisera och auktorisera med Azure Active Directory för åtkomst till Service Bus resurser](authenticate-application.md#azure-built-in-roles-for-azure-service-bus).

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Använda Service Bus med hanterade identiteter för Azure-resurser
Om du vill använda Service Bus med hanterade identiteter måste du tilldela identiteten rollen och lämplig omfattning. I proceduren i det här avsnittet används ett enkelt program som körs under en hanterad identitet och som har åtkomst till Service Bus resurser.

Här ska vi använda ett exempel på ett webb program som finns i [Azure App Service](https://azure.microsoft.com/services/app-service/). Stegvisa instruktioner för hur du skapar ett webb program finns i [skapa en ASP.net Core webbapp i Azure](../app-service/quickstart-dotnetcore.md)

Följ dessa steg när programmet har skapats: 

1. Gå till **Inställningar** och välj **identitet**. 
1. Välj den **status** som ska **användas.** 
1. Spara inställningen genom att välja **Spara**. 

    ![Hanterad identitet för en webbapp](./media/service-bus-managed-service-identity/identity-web-app.png)

När du har aktiverat den här inställningen skapas en ny tjänst identitet i din Azure Active Directory (Azure AD) och konfigureras i App Service-värden.

> [!NOTE]
> När du använder en hanterad identitet ska anslutnings strängen ha formatet: `Endpoint=sb://<NAMESPACE NAME>.servicebus.windows.net/;Authentication=Managed Identity` .

Tilldela nu den här tjänst identiteten till en roll i det begärda omfånget i Service Bus-resurser.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Så här tilldelar du Azure-roller med hjälp av Azure Portal
Om du vill tilldela en roll till en Service Bus-namnrymd navigerar du till namn området i Azure Portal. Visa inställningarna för Access Control (IAM) för resursen och följ de här anvisningarna för att hantera roll tilldelningar:

> [!NOTE]
> Följande steg tilldelar en tjänst identitets roll till dina Service Bus namnrum. Du kan följa samma steg för att tilldela en roll i andra omfattningar som stöds (resurs grupp och prenumeration). 
> 
> [Skapa ett namn område för Service Bus meddelande tjänst](service-bus-create-namespace-portal.md) om du inte har något. 

1. I Azure Portal navigerar du till Service Bus namn området och visar **översikten** för namn området. 
1. Välj **Access Control (IAM)** på den vänstra menyn för att Visa inställningar för åtkomst kontroll för Service Bus namn området.
1.  Välj fliken **roll tilldelningar** om du vill se en lista över roll tilldelningar.
3.  Välj **Lägg till** och välj sedan **Lägg till roll tilldelning**.
4.  På sidan **Lägg till roll tilldelning** följer du dessa steg:
    1. För **roll** väljer du den Service Bus roll som du vill tilldela. I det här exemplet är det **Azure Service Bus data ägare**.
    1. I fältet **tilldela åtkomst till väljer du** **App Service** under **systemtilldelad hanterad identitet**. 
    1. Välj den **prenumeration** där den hanterade identiteten för webbappen skapades.
    1. Välj den **hanterade identiteten** för den webbapp som du skapade. Standard namnet för identiteten är samma som namnet på webbappen. 
    1. Välj sedan **Spara**.
        
        ![Sidan Lägg till roll tilldelning](./media/service-bus-managed-service-identity/add-role-assignment-page.png)

    När du har tilldelat rollen får webb programmet åtkomst till Service Bus entiteter under det definierade omfånget. 

    > [!NOTE]
    > En lista över tjänster som stöder hanterade identiteter finns i [tjänster som stöder hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

### <a name="run-the-app"></a>Kör appen
Ändra nu standard sidan för det ASP.NET-program som du skapade. Du kan använda webb program koden från [den här GitHub-lagringsplatsen](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

Sidan default. aspx är din landnings sida. Du hittar koden i Default.aspx.cs-filen. Resultatet är ett minimalt webb program med några fält och med knapparna **Skicka** och **ta emot** som ansluter till Service Bus för att antingen skicka eller ta emot meddelanden.

Observera hur [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) -objektet initieras. I stället för att använda token Provider för delad åtkomst (SAS) skapar koden en token-Provider för den hanterade identiteten med `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` anropet. Det finns därför inga hemligheter att kvarhålla och använda. Flödet av den hanterade identitets kontexten till Service Bus och handskakningen för auktoriseringen hanteras automatiskt av token-providern. Det är en enklare modell än att använda SAS.

När du har gjort dessa ändringar ska du publicera och köra programmet. Du kan enkelt få rätt publicerings data genom att ladda ned och importera en publicerings profil i Visual Studio:

![Hämta publicerings profil](./media/service-bus-managed-service-identity/msi3.png)
 
Om du vill skicka eller ta emot meddelanden anger du namnet på namn området och namnet på entiteten som du skapade. Klicka sedan på antingen **Skicka** eller **ta emot**.


> [!NOTE]
> - Den hanterade identiteten fungerar bara i Azure-miljön, på App Services, i virtuella Azure-datorer och i skalnings uppsättningar. För .NET-program ger Microsoft. Azure. Services. AppAuthentication-biblioteket, som används av Service Bus NuGet-paketet, en abstraktion över det här protokollet och har stöd för en lokal utvecklings miljö. Med det här biblioteket kan du också testa din kod lokalt på din utvecklings dator med ditt användar konto från Visual Studio, Azure CLI 2,0 eller Active Directory integrerad autentisering. Mer information om lokala utvecklings alternativ med det här biblioteket finns i [tjänst-till-tjänst-autentisering för att Azure Key Vault med .net](../key-vault/general/service-to-service-authentication.md).  
> 
> - För närvarande fungerar inte hanterade identiteter med App Service distributions fack.

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)