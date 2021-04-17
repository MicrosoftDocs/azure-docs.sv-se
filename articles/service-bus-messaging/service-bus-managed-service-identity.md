---
title: Hanterade identiteter för Azure-resurser med Service Bus
description: Den här artikeln beskriver hur du använder hanterade identiteter för att komma åt Azure Service Bus entiteter (köer, ämnen och prenumerationer).
ms.topic: article
ms.date: 01/21/2021
ms.openlocfilehash: cac254ef6b57f1878620b1e3ca30e757d7f39a88
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529460"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Autentisera en hanterad identitet med Azure Active Directory åtkomst till Azure Service Bus resurser
[Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) är en funktion mellan Azure som gör att du kan skapa en säker identitet som är associerad med distributionen som programkoden körs under. Du kan sedan associera identiteten med åtkomstkontrollroller som beviljar anpassade behörigheter för åtkomst till specifika Azure-resurser som ditt program behöver.

Med hanterade identiteter hanterar Azure-plattformen denna körningsidentitet. Du behöver inte lagra och skydda åtkomstnycklar i din programkod eller konfiguration, antingen för själva identiteten eller för de resurser som du behöver komma åt. En Service Bus-klientapp som körs i ett Azure App Service-program eller i en virtuell dator med aktiverade hanterade entiteter för Azure-resurser behöver inte hantera SAS-regler och -nycklar eller andra åtkomsttoken. Klientappen behöver bara slutpunktsadressen för Service Bus för meddelanden. När appen ansluter, Service Bus den hanterade entitetens kontext till klienten i en åtgärd som visas i ett exempel senare i den här artikeln. När den har associerats med en hanterad identitet kan Service Bus-klienten göra alla auktoriserade åtgärder. Auktorisering beviljas genom att associera en hanterad entitet med Service Bus roller. 

## <a name="overview"></a>Översikt
När ett säkerhetsobjekt (en användare, grupp eller ett program) försöker komma åt en Service Bus entitet, måste begäran auktoriserats. Med Azure AD är åtkomsten till en resurs en tvåstegsprocess. 

 1. Först autentiseras säkerhetsobjekts identitet och en OAuth 2.0-token returneras. Resursnamnet för att begära en token är `https://servicebus.azure.net` .
 1. Därefter skickas token som en del av en begäran till Service Bus för att ge åtkomst till den angivna resursen.

Autentiseringssteget kräver att en programbegäran innehåller en OAuth 2.0-åtkomsttoken vid körning. Om ett program körs i en Azure-entitet, till exempel en virtuell Azure-dator, en VM-skalningsuppsättning eller en Azure-funktionsapp, kan den använda en hanterad identitet för att få åtkomst till resurserna. 

Auktoriseringssteget kräver att en eller flera Azure-roller tilldelas till säkerhetsobjekt. Azure Service Bus tillhandahåller Azure-roller som omfattar uppsättningar med behörigheter för Service Bus resurser. De roller som tilldelas till ett säkerhetsobjekt avgör vilka behörigheter som huvudnamn kommer att ha. Mer information om hur du tilldelar Azure-roller Azure Service Bus finns i [Azures inbyggda roller för Azure Service Bus](#azure-built-in-roles-for-azure-service-bus). 

Interna program och webbprogram som gör begäranden till Service Bus kan också auktorisera med Azure AD. Den här artikeln visar hur du begär en åtkomsttoken och använder den för att auktorisera begäranden för Service Bus resurser. 


## <a name="assigning-azure-roles-for-access-rights"></a>Tilldela Azure-roller för åtkomstbehörigheter
Azure Active Directory (Azure AD) auktoriserar åtkomsträttigheter till skyddade resurser via [rollbaserad åtkomstkontroll i Azure (Azure RBAC).](../role-based-access-control/overview.md) Azure Service Bus definierar en uppsättning inbyggda Azure-roller som omfattar vanliga uppsättningar med behörigheter som används för att komma åt Service Bus-entiteter och du kan också definiera anpassade roller för åtkomst till data.

När en Azure-roll tilldelas till ett Azure AD-säkerhetsobjekt beviljar Azure åtkomst till dessa resurser för det säkerhetsobjekt. Åtkomsten kan vara begränsad till prenumerationsnivån, resursgruppen eller Service Bus namnområdet. Ett Azure AD-säkerhetsobjekt kan vara en användare, en grupp, ett programtjänsthuvudnamn eller en hanterad identitet för Azure-resurser.

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Inbyggda Azure-roller för Azure Service Bus
Till Azure Service Bus är hanteringen av namnområden och alla relaterade resurser via Azure Portal och Azure-resurshanterings-API:et redan skyddad med hjälp av Azure RBAC-modellen. Azure tillhandahåller de inbyggda Azure-rollerna nedan för auktorisering av åtkomst Service Bus en namnrymd:

- [Azure Service Bus dataägare:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)Ger dataåtkomst till Service Bus och dess entiteter (köer, ämnen, prenumerationer och filter)
- [Azure Service Bus Data Sender : Använd](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender)den här rollen för att ge åtkomst till Service Bus och dess entiteter.
- [Azure Service Bus datamottagare: Använd](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver)den här rollen för att ge mottagande åtkomst Service Bus och dess entiteter. 

## <a name="resource-scope"></a>Resursomfång 
Innan du tilldelar en Azure-roll till ett säkerhetsobjekt ska du bestämma vilket åtkomstomfång säkerhetsobjekt som ska ha. Bästa praxis kräver att det alltid är bäst att endast bevilja det smalaste möjliga omfånget.

I följande lista beskrivs de nivåer där du kan begränsa åtkomsten till Service Bus resurser, från och med det smalaste omfånget:

- **Kö,** **ämne** eller prenumeration: **Rolltilldelning** gäller för den specifika Service Bus entiteten. För närvarande stöder Azure Portal inte tilldelning av användare/grupper/hanterade identiteter till Service Bus Azure-roller på prenumerationsnivå. Här är ett exempel på hur du använder Azure [CLI-kommandot az-role-assignment-create](/cli/azure/role/assignment?#az-role-assignment-create) för att tilldela en identitet till en Service Bus Azure-roll: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Service Bus namnområde:** Rolltilldelningen omfattar hela topologin för Service Bus under namnområdet och till konsumentgruppen som är associerad med den.
- **Resursgrupp:** Rolltilldelningen gäller för alla Service Bus under resursgruppen.
- **Prenumeration:** Rolltilldelning gäller för alla Service Bus i alla resursgrupper i prenumerationen.

> [!NOTE]
> Tänk på att azure-rolltilldelningar kan ta upp till fem minuter att spridas. 

Mer information om hur inbyggda roller definieras finns i Förstå [rolldefinitioner.](../role-based-access-control/role-definitions.md#management-and-data-operations) Information om hur du skapar anpassade Azure-roller finns i [Anpassade Roller i Azure.](../role-based-access-control/custom-roles.md)

## <a name="enable-managed-identities-on-a-vm"></a>Aktivera hanterade identiteter på en virtuell dator
Innan du kan använda hanterade identiteter för Azure-resurser för att auktorisera Service Bus från den virtuella datorn måste du först aktivera hanterade identiteter för Azure-resurser på den virtuella datorn. Information om hur du aktiverar hanterade identiteter för Azure-resurser finns i någon av följande artiklar:

- [Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mall](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager klientbibliotek](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Bevilja behörigheter till en hanterad identitet i Azure AD
Om du vill auktorisera en begäran till Service Bus-tjänsten från en hanterad identitet i ditt program måste du först konfigurera Azure-inställningar för rollbaserad åtkomstkontroll (Azure RBAC) för den hanterade identiteten. Azure Service Bus definierar Azure-roller som omfattar behörigheter för att skicka och läsa från Service Bus. När Azure-rollen tilldelas till en hanterad identitet beviljas den hanterade identiteten åtkomst till Service Bus entiteter i lämpligt omfång.

Mer information om hur du tilldelar Azure-roller finns i Autentisera och auktorisera [med Azure Active Directory för åtkomst till Service Bus resurser.](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Använda Service Bus hanterade identiteter för Azure-resurser
Om du Service Bus med hanterade identiteter måste du tilldela identiteten rollen och lämpligt omfång. Proceduren i det här avsnittet använder ett enkelt program som körs under en hanterad identitet och som har åtkomst Service Bus resurser.

Här använder vi ett exempelwebbprogram som finns i [Azure App Service](https://azure.microsoft.com/services/app-service/). Stegvisa instruktioner för hur du skapar en webbapp finns i [Skapa en ASP.NET Core-webbapp i Azure](../app-service/quickstart-dotnetcore.md)

Följ dessa steg när programmet har skapats: 

1. Gå till **Inställningar** och välj **Identitet.** 
1. Välj **Status** som **På.** 
1. Spara inställningen genom att välja **Spara**. 

    ![Hanterad identitet för en webbapp](./media/service-bus-managed-service-identity/identity-web-app.png)

När du har aktiverat den här inställningen skapas en ny tjänstidentitet i din Azure Active Directory (Azure AD) och konfigureras i App Service värden.

> [!NOTE]
> När du använder en hanterad identitet ska anslutningssträngen ha formatet: `Endpoint=sb://<NAMESPACE NAME>.servicebus.windows.net/;Authentication=ManagedIdentity` .

Tilldela nu den här tjänstidentiteten till en roll i det omfång som krävs i Service Bus resurser.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Tilldela Azure-roller med hjälp av Azure Portal
Om du vill tilldela en roll Service Bus ett namnområde navigerar du till namnområdet i Azure Portal. Visa Access Control (IAM) för resursen och följ dessa instruktioner för att hantera rolltilldelningar:

> [!NOTE]
> Följande steg tilldelar en tjänstidentitetsroll till Service Bus namnområden. Du kan följa samma steg för att tilldela en roll i andra omfång som stöds (resursgrupp och prenumeration). 
> 
> [Skapa ett Service Bus för meddelanden](service-bus-create-namespace-portal.md) om du inte har ett. 

1. I Azure Portal navigerar du till Service Bus namnområdet och visar **Översikt** för namnområdet. 
1. Välj **Access Control (IAM)** på den vänstra menyn för att visa åtkomstkontrollinställningar för Service Bus namnområdet.
1.  Välj fliken **Rolltilldelningar** för att se listan över rolltilldelningar.
3.  Välj **Lägg till** och sedan Lägg till **rolltilldelning.**
4.  På sidan **Lägg till rolltilldelning** följer du dessa steg:
    1. För **Roll** väljer du Service Bus roll som du vill tilldela. I det här exemplet är det **Azure Service Bus dataägare**.
    1. I fältet **Tilldela åtkomst till väljer** du App Service under System  **tilldelad hanterad identitet**. 
    1. Välj den **prenumeration** där den hanterade identiteten för webbappen skapades.
    1. Välj den **hanterade identiteten** för webbappen som du skapade. Standardnamnet för identiteten är samma som namnet på webbappen. 
    1. Välj sedan **Spara.**
        
        ![Sidan Lägg till rolltilldelning](./media/service-bus-managed-service-identity/add-role-assignment-page.png)

    När du har tilldelat rollen får webbappen åtkomst till de Service Bus under det definierade omfånget. 

    > [!NOTE]
    > En lista över tjänster som stöder hanterade identiteter finns i [Tjänster som stöder hanterade identiteter för Azure-resurser.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

### <a name="run-the-app"></a>Kör appen
Ändra nu standardsidan för det ASP.NET som du skapade. Du kan använda webbprogramkoden från den här [GitHub-lagringsplatsen.](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)  

Sidan Default.aspx är din landningssida. Koden finns i filen Default.aspx.cs. Resultatet är ett minimalt webbprogram med några  inmatningsfält och med knappar för att skicka och ta emot som ansluter till Service Bus för att antingen skicka eller ta emot meddelanden. 

Observera hur [MessagingFactory-objektet](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) initieras. I stället för att använda tokenprovidern för delad åtkomst (SAS) skapar koden en tokenprovider för den hanterade identiteten med `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` -anropet. Därför finns det inga hemligheter att behålla och använda. Flödet för den hanterade identitetskontexten till Service Bus och auktoriseringshandskakningen hanteras automatiskt av tokenprovidern. Det är en enklare modell än att använda SAS.

När du har gjort dessa ändringar publicerar och kör du programmet. Du kan enkelt hämta rätt publiceringsdata genom att ladda ned och sedan importera en publiceringsprofil i Visual Studio:

![Hämta publiceringsprofil](./media/service-bus-managed-service-identity/msi3.png)
 
Om du vill skicka eller ta emot meddelanden anger du namnet på namnområdet och namnet på den entitet som du skapade. Klicka sedan på Skicka **eller** ta **emot**.


> [!NOTE]
> - Den hanterade identiteten fungerar bara i Azure-miljön på App Services, virtuella Azure-datorer och skalningsuppsättningar. För .NET-program tillhandahåller Microsoft.Azure.Services.AppAuthentication-biblioteket, som används av Service Bus NuGet-paketet, en abstraktion över det här protokollet och stöder en lokal utvecklingsupplevelse. Med det här biblioteket kan du också testa koden lokalt på utvecklingsdatorn med ditt användarkonto från Visual Studio, Azure CLI 2.0 eller Active Directory-integrerad autentisering. Mer information om lokala utvecklingsalternativ med det här biblioteket finns i [Tjänst-till-tjänst-autentisering för att Azure Key Vault med .NET](/dotnet/api/overview/azure/service-to-service-authentication).  

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
