---
title: Registrera en kund i Azure Lighthouse
description: Lär dig hur du kan publicera en kund i Azure Lighthouse, så att deras resurser kan nås och hanteras via din egen klient med Azure-delegerad resurs hantering.
ms.date: 02/08/2021
ms.topic: how-to
ms.openlocfilehash: c0a886b692b99156cbd53e5f0f5953047560c5b9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372152"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>Registrera en kund i Azure Lighthouse

Den här artikeln förklarar hur du, som en tjänst leverantör, kan publicera en kund till Azure Lighthouse. När du gör det kan delegerade resurser (prenumerationer och/eller resurs grupper) i kundens Azure Active Directory-klient (Azure AD) hanteras via din egen klient med hjälp av [Azure-delegerad resurs hantering](../concepts/azure-delegated-resource-management.md).

> [!TIP]
> Även om vi refererar till tjänst leverantörer och kunder i det här avsnittet kan [företag som hanterar flera klienter](../concepts/enterprise.md) använda samma process för att konfigurera Azure-Lighthouse och konsolidera sina hanterings upplevelser.

Du kan upprepa onboarding-processen för flera kunder. När en användare med rätt behörighet loggar in till din hanterings klient kan den användaren auktoriseras mellan kundens hyres omfång för att utföra hanterings åtgärder, utan att behöva logga in på varje enskild kund klient.

Om du vill spåra din påverkan på kund engagemang och få erkännande, associerar du ditt Microsoft Partner Network (MPN) ID med minst ett användar konto som har åtkomst till var och en av dina inbyggda prenumerationer. Du måste utföra den här associationen i din tjänst leverantörs klient. Vi rekommenderar att du skapar ett tjänst huvud konto i din klient som är associerat med ditt MPN-ID och sedan inkluderar tjänstens huvud namn varje gång du registrerar en kund. Mer information finns i [Länka ditt partner-ID för att aktivera intjänad partner kredit på delegerade resurser](partner-earned-credit.md).

> [!NOTE]
> Kunder kan alternativt registreras i Azure Lighthouse när de köper ett hanterat tjänst erbjudande (offentligt eller privat) som du [publicerar på Azure Marketplace](publish-managed-services-offers.md). Du kan också använda onboarding-processen som beskrivs här tillsammans med erbjudanden som publicerats på Azure Marketplace.

Onboarding-processen kräver att åtgärder tas från både tjänst leverantörens klient organisation och kundens klient organisation. Alla dessa steg beskrivs i den här artikeln.

## <a name="gather-tenant-and-subscription-details"></a>Samla in klient-och prenumerations information

För att kunna publicera en kunds klient måste den ha en aktiv Azure-prenumeration. Du måste känna till följande:

- Klient-ID för tjänst leverantörens klient organisation (där du kommer att hantera kundens resurser)
- Klient-ID för kundens klient organisation (som kommer att ha resurser som hanteras av tjänst leverantören)
- Prenumerations-ID: na för varje specifik prenumeration i kundens klient organisation som ska hanteras av tjänst leverantören (eller som innehåller de resurs grupper som ska hanteras av tjänst leverantören).

Om du inte redan har dessa ID-värden kan du hämta dem på något av följande sätt. Se till att du använder de här exakta värdena i distributionen.

### <a name="azure-portal"></a>Azure Portal

Du kan se ditt klient-ID genom att hovra över ditt konto namn längst upp till höger i Azure Portal, eller genom att välja **Växla katalog**. Om du vill välja och kopiera ditt klient-ID söker du efter "Azure Active Directory" i portalen och väljer sedan **Egenskaper** och kopierar värdet som visas i fältet **katalog-ID** . Om du vill hitta ID: t för en prenumeration i kund klienten söker du efter "prenumerationer" och väljer sedan lämpligt prenumerations-ID.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> När du registrerar en prenumeration (eller en eller flera resurs grupper i en prenumeration) med hjälp av processen som beskrivs här, registreras **Microsoft. ManagedServices** Resource Provider för den prenumerationen.

## <a name="define-roles-and-permissions"></a>Definiera roller och behörigheter

Som tjänst leverantör kanske du vill utföra flera uppgifter för en enskild kund, vilket kräver olika åtkomst för olika omfång. Du kan definiera så många auktoriseringar som du behöver för att kunna tilldela lämpliga [inbyggda Azure-roller](../../role-based-access-control/built-in-roles.md). Varje auktorisering innehåller en **principalId** som refererar till en Azure AD-användare, en grupp eller ett tjänst huvud namn i hanterings klienten.

> [!NOTE]
> Om inget annat anges kan referenser till en "användare" i Azure Lighthouse-dokumentationen gälla för en Azure AD-användare, en grupp eller ett tjänst huvud namn i en auktorisering.

För att förenkla hanteringen rekommenderar vi att du använder Azure AD-användargrupper för varje roll närhelst det är möjligt, i stället för enskilda användare. Detta ger dig flexibiliteten att lägga till eller ta bort enskilda användare i gruppen som har åtkomst, så att du inte behöver upprepa onboarding-processen för att göra ändringar i användaren. Du kan också tilldela roller till ett huvud namn för tjänsten, vilket kan vara användbart för automatiserings scenarier.

> [!IMPORTANT]
> För att du ska kunna lägga till behörigheter för en Azure AD-grupp måste **grupp typen** anges till **säkerhet**. Det här alternativet väljs när gruppen skapas. Mer information finns i [Skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

När du definierar dina auktoriseringar måste du kontrol lera att du följer principen om minsta behörighet så att användarna bara har de behörigheter som krävs för att utföra sitt jobb. Information om vilka roller och bästa metoder som stöds finns i [klienter, användare och roller i Azure Lighthouse-scenarier](../concepts/tenants-users-roles.md).

För att definiera auktorisering måste du känna till ID-värdena för varje användare, användar grupp eller tjänstens huvud namn i den tjänst leverantörs klient organisation som du vill bevilja åtkomst till. Du behöver också roll Definitions-ID: t för varje inbyggd roll som du vill tilldela. Om du inte redan har gjort det kan du hämta dem genom att köra kommandona nedan inifrån tjänst leverantörens klient organisation.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>' | Get-AzADServicePrincipal).Id

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```

> [!TIP]
> Vi rekommenderar att du tilldelar [borttagnings rollen för hanterade tjänster för registrering av hanterade tjänster](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) när du registrerar en kund, så att användare i din klient kan [ta bort åtkomst till delegeringen](remove-delegation.md) senare om det behövs. Om den här rollen inte är tilldelad kan delegerade resurser bara tas bort av en användare i kundens klient organisation.

## <a name="create-an-azure-resource-manager-template"></a>Skapa en Azure Resource Manager mall

För att publicera kunden måste du skapa en [Azure Resource Manager](../../azure-resource-manager/index.yml) mall för ditt erbjudande med följande information. Värdena för **mspOfferName** och **mspOfferDescription** visas för kunden på [sidan tjänst leverantörer](view-manage-service-providers.md) i Azure Portal.

|Field  |Definition  |
|---------|---------|
|**mspOfferName**     |Ett namn som beskriver den här definitionen. Det här värdet visas för kunden som rubriken på erbjudandet och måste vara ett unikt värde.        |
|**mspOfferDescription**     |En kort beskrivning av ditt erbjudande (till exempel "contoso VM Management-erbjudande").      |
|**managedByTenantId**     |Ditt klient-ID.          |
|**auktoriseringar**     |**PrincipalId** -värdena för användare/grupper/SPN från din klient, var och en med en **principalIdDisplayName** för att hjälpa kunden att förstå syftet med auktoriseringen och mappas till ett inbyggt **roleDefinitionId** -värde för att ange åtkomst nivå.      |

Onboarding-processen kräver en Azure Resource Manager-mall (tillhandahålls i våra [exempel lagrings platsen](https://github.com/Azure/Azure-Lighthouse-samples/)) och en motsvarande parameter fil som du ändrar för att matcha konfigurationen och definiera dina auktoriseringar.

> [!IMPORTANT]
> Processen som beskrivs här kräver en separat distribution för varje prenumeration som registreras, även om du registrerar prenumerationer i samma kund klient organisation. Separata distributioner krävs också om du registrerar flera resurs grupper inom olika prenumerationer i samma kund klient organisation. Att registrera flera resurs grupper i en enda prenumeration kan dock göras i en distribution.
>
> Separata distributioner krävs också för att flera erbjudanden ska tillämpas på samma prenumeration (eller resurs grupper inom en prenumeration). Varje erbjudande som tillämpas måste använda en annan **mspOfferName**.

Vilken mall du väljer beror på om du registrerar en hel prenumeration, en resurs grupp eller flera resurs grupper i en prenumeration. Vi tillhandahåller också en mall som kan användas för kunder som har köpt ett hanterat tjänst erbjudande som du har publicerat på Azure Marketplace, om du föredrar att publicera deras prenumerationer på det här sättet.

|För att publicera detta  |Använd den här Azure Resource Manager mallen  |Och ändra den här parameter filen |
|---------|---------|---------|
|Prenumeration   |[delegatedResourceManagement.jspå](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.jspå](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resursgrupp   |[rgDelegatedResourceManagement.jspå](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.jspå](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Flera resursgrupper i en prenumeration   |[multipleRgDelegatedResourceManagement.jspå](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.jspå](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Prenumeration (när du använder ett erbjudande som publicerats på Azure Marketplace)   |[marketplaceDelegatedResourceManagement.jspå](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.jspå](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!TIP]
> Även om du inte kan publicera en hel hanterings grupp i en distribution kan du [distribuera en princip på hanterings grupps nivå](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups). Principen kontrollerar om varje prenumeration i hanterings gruppen har delegerats till den angivna hanterings klienten, och om den inte skapas skapas tilldelningen utifrån de värden du anger.

I följande exempel visas en ändrad **delegatedResourceManagement.parameters.jspå** en fil som kan användas för att publicera en prenumeration. Resurs gruppens parameter-filer (som finns i mappen [RG-delegerad-resurs hantering](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) ) liknar varandra, men innehåller även en **rgName** -parameter för att identifiera de enskilda resurs grupper som ska publiceras.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```

Den senaste auktoriseringen i exemplet ovan lägger till en **principalId** med rollen administratör för användar åtkomst (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). När du tilldelar den här rollen måste du inkludera egenskapen **delegatedRoleDefinitionIds** och en eller flera inbyggda Azure-roller som stöds. Användaren som skapas i detta tillstånd kommer att kunna tilldela dessa roller till [hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md) i kund klienten, vilket krävs för att [distribuera principer som kan åtgärdas](deploy-policy-remediation.md).  Användaren kan också skapa support incidenter. Inga andra behörigheter som är associerade med rollen administratör för användar åtkomst gäller för den här **principalId**.

## <a name="deploy-the-azure-resource-manager-templates"></a>Distribuera Azure Resource Manager mallar

När du har uppdaterat parameter filen måste en användare i kundens klient organisation Distribuera Azure Resource Manager-mallen i sin klient organisation. En separat distribution krävs för varje prenumeration som du vill publicera (eller för varje prenumeration som innehåller resurs grupper som du vill publicera).

> [!IMPORTANT]
> Den här distributionen måste göras av ett konto som inte är gäst i kundens klient organisation som har den [inbyggda rollen som ägare](../../role-based-access-control/built-in-roles.md#owner) för den prenumeration som registreras (eller som innehåller de resurs grupper som har registrerats). Om du vill se alla användare som kan delegera prenumerationen kan en användare i kundens klient välja prenumerationen i Azure Portal, öppna **åtkomst kontroll (IAM)** och [Visa alla användare med ägar rollen](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription). 
>
> Om prenumerationen skapades via [Cloud Solution Provider (CSP)-programmet](../concepts/cloud-solution-provider.md), kan alla användare som har rollen [Administratörs agent](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) i din tjänst leverantörs klient utföra distributionen.

Distributionen kan göras i Azure Portal, med hjälp av PowerShell eller med hjälp av Azure CLI, som du ser nedan.

### <a name="azure-portal"></a>Azure Portal

1. I vår [GitHub-lagrings platsen](https://github.com/Azure/Azure-Lighthouse-samples/)väljer du knappen **distribuera till Azure** som visas bredvid den mall som du vill använda. Mallen öppnas på Azure-portalen.
1. Ange dina värden för **namnet på MSP-erbjudandet**, en **Beskrivning av MSP-erbjudandet**, **hanteras av klient-ID** och **auktoriseringar**. Om du vill kan du välja **Redigera parametrar** för att ange värden för `mspOfferName` , `mspOfferDescription` , `managedbyTenantId` och `authorizations` direkt i parameter filen. Se till att uppdatera dessa värden i stället för att använda standardvärdena från mallen.
1. Välj **Granska och skapa** och välj sedan **skapa**.

Efter några minuter bör du se ett meddelande om att distributionen har slutförts.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-file <pathToTemplateFile> \
                         --parameters <parameters/parameterFile> \
                         --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-uri <templateUri> \
                         --parameters <parameterFile> \
                         --verbose
```

## <a name="confirm-successful-onboarding"></a>Bekräfta lyckad onboarding

När en kund prenumeration har publicerats till Azure-Lighthouse kommer användare i tjänste leverantörens klient organisation att kunna se prenumerationen och dess resurser (om de har beviljats åtkomst till den genom processen ovan, antingen individuellt eller som medlem i en Azure AD-grupp med rätt behörigheter). Bekräfta detta genom att kontrol lera att prenumerationen visas på något av följande sätt.  

### <a name="azure-portal"></a>Azure Portal

I tjänst leverantörens klient organisation:

1. Gå till [sidan mina kunder](view-manage-customers.md).
2. Välj **Kunder**.
3. Bekräfta att du kan se prenumerationerna med det erbjudande namn som du angav i Resource Manager-mallen.

> [!IMPORTANT]
> För att kunna se den delegerade prenumerationen i [Mina kunder](view-manage-customers.md)måste användare i tjänste leverantörens klient organisation ha beviljats rollen [läsare](../../role-based-access-control/built-in-roles.md#reader) (eller en annan inbyggd roll som inkluderar läsar åtkomst) när prenumerationen har registrerats.

I kundens klient organisation:

1. Gå till [sidan tjänst leverantörer](view-manage-service-providers.md).
2. Och sedan välja **Tjänstleverantörserbjudanden**.
3. Bekräfta att du kan se prenumerationerna med det erbjudande namn som du angav i Resource Manager-mallen.

> [!NOTE]
> Det kan ta upp till 15 minuter innan distributionen är klar innan uppdateringarna återspeglas i Azure Portal. Du kanske kan se uppdateringarna tidigare om du uppdaterar din Azure Resource Manager-token genom att uppdatera webbläsaren, logga in och ut eller begära en ny token.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext

# Confirm successful onboarding for Azure Lighthouse

Get-AzManagedServicesDefinition
Get-AzManagedServicesAssignment
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

Om du behöver göra ändringar när kunden har publicerats kan du [Uppdatera delegeringen](update-delegation.md). Du kan också [ta bort åtkomsten till delegeringen](remove-delegation.md) helt.

## <a name="troubleshooting"></a>Felsökning

Om du inte kan publicera kunden eller om dina användare har problem med att komma åt de delegerade resurserna kontrollerar du följande tips och krav och försöker igen.

- `managedbyTenantId`Värdet får inte vara samma som klient-ID: t för den prenumeration som registreras.
- Det går inte att ha flera tilldelningar i samma definitions område `mspOfferName` .
- **Microsoft. ManagedServices** -resurs leverantören måste vara registrerad för den delegerade prenumerationen. Detta bör ske automatiskt under distributionen, men om inte kan du [registrera den manuellt](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).
- Auktoriseringar får inte innehålla några användare med den inbyggda rollen [ägare](../../role-based-access-control/built-in-roles.md#owner) eller någon inbyggd roll med [DataActions](../../role-based-access-control/role-definitions.md#dataactions).
- Grupper måste skapas med en [**grupp typ**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md#group-types) som **säkerhet** och inte **Microsoft 365**.
- Det kan finnas ytterligare fördröjning innan åtkomst har Aktiver ATS för [kapslade grupper](../..//active-directory/fundamentals/active-directory-groups-membership-azure-portal.md).
- Användare som behöver visa resurser i Azure Portal måste ha rollen [läsare](../../role-based-access-control/built-in-roles.md#reader) (eller en annan inbyggd roll som inkluderar läsar åtkomst).
- De [inbyggda Azure-rollerna](../../role-based-access-control/built-in-roles.md) som du inkluderar i auktoriseringar får inte innehålla föråldrade roller. Om en inbyggd Azure-roll blir föråldrad kommer alla användare som har varit registrerade med rollen att förlora åtkomsten och du kan inte publicera ytterligare delegeringar. Du kan åtgärda detta genom att uppdatera mallen så att den endast använder inbyggda roller som stöds och sedan utföra en ny distribution.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanterings upplevelser mellan flera innehavare](../concepts/cross-tenant-management-experience.md).
- [Visa och hantera kunder](view-manage-customers.md) genom att gå till **mina kunder** i Azure Portal.
- Lär dig hur du [uppdaterar](update-delegation.md) eller [tar bort](remove-delegation.md) en delegering.
