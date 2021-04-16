---
title: Registrera en kund i Azure Lighthouse
description: Lär dig hur du kan registrera en Azure Lighthouse, så att deras resurser kan nås och hanteras via din egen klientorganisation med hjälp av Azure-delegerad resurshantering.
ms.date: 03/29/2021
ms.topic: how-to
ms.openlocfilehash: d8ad448ac022b07ecdea6b68c4544b8c955814b1
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497973"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>Registrera en kund i Azure Lighthouse

Den här artikeln förklarar hur du som tjänstleverantör kan registrera en kund för att Azure Lighthouse. När du gör det kan delegerade resurser (prenumerationer och/eller resursgrupper) i kundens Azure Active Directory-klient (Azure AD) hanteras via din egen klientorganisation med hjälp av [Azure-delegerad resurshantering.](../concepts/azure-delegated-resource-management.md)

> [!TIP]
> Även om vi refererar till tjänstleverantörer och kunder i det här avsnittet [kan](../concepts/enterprise.md) företag som hanterar flera klienter använda samma process för att konfigurera Azure Lighthouse och konsolidera sina hanteringsupplevelser.

Du kan upprepa registreringsprocessen för flera kunder. När en användare med rätt behörighet loggar in på din hanterande klientorganisation kan den användaren auktoriseras i kundens klientorganisationsomfång för att utföra hanteringsåtgärder, utan att behöva logga in på varje enskild kundklientorganisation.

Om du vill spåra din inverkan på kundengagemang och ta emot igenkänning associerar du ditt Microsoft Partner Network-ID (MPN) med minst ett användarkonto som har åtkomst till var och en av dina inbyggda prenumerationer. Du måste utföra den här associationen i tjänstleverantörens klientorganisation. Vi rekommenderar att du skapar ett konto för tjänstens huvudnamn i din klientorganisation som är associerat med ditt MPN-ID och sedan inkluderar tjänstens huvudnamn varje gång du skapar en kund. Mer information finns i Länka ditt [partner-ID för att aktivera partner-intjänad kredit på delegerade resurser.](partner-earned-credit.md)

> [!NOTE]
> Kunder kan alternativt registrera sig för att Azure Lighthouse när de köper ett erbjudande för hanterad tjänst (offentligt eller privat) som du publicerar [till Azure Marketplace](publish-managed-services-offers.md). Du kan också använda onboarding-processen som beskrivs här tillsammans med erbjudanden som publicerats för Azure Marketplace.

Registreringsprocessen kräver att åtgärder vidtas inifrån både tjänstleverantörens klientorganisation och från kundens klientorganisation. Alla dessa steg beskrivs i den här artikeln.

## <a name="gather-tenant-and-subscription-details"></a>Samla in information om klientorganisation och prenumeration

För att kunna registrera en kunds klientorganisation måste den ha en aktiv Azure-prenumeration. Du behöver veta följande:

- Klientorganisations-ID för tjänstleverantörens klientorganisation (där du ska hantera kundens resurser)
- Klientorganisations-ID för kundens klientorganisation (som kommer att ha resurser som hanteras av tjänstleverantören)
- Prenumerations-ID:n för varje specifik prenumeration i kundens klientorganisation som ska hanteras av tjänstleverantören (eller som innehåller de resursgrupper som ska hanteras av tjänstleverantören).

Om du inte redan har dessa ID-värden kan du hämta dem på något av följande sätt. Var noga med att använda dessa exakta värden i distributionen.

### <a name="azure-portal"></a>Azure Portal

Ditt klientorganisations-ID kan visas genom att hovra över ditt kontonamn längst upp till höger i Azure Portal eller genom att välja **Växla katalog.** Om du vill välja och kopiera ditt klientorganisations-ID söker du  efter "Azure Active Directory" från portalen. Välj sedan Egenskaper och kopiera värdet som visas i **fältet Katalog-ID.** Om du vill hitta ID för en prenumeration i kundens klientorganisation söker du efter ”Prenumerationer” och väljer sedan lämpligt prenumerations-ID.

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
> Vid registrering av en prenumeration (eller en eller flera resursgrupper i en prenumeration) med hjälp av den process som beskrivs här registreras resursprovidern **Microsoft.ManagedServices** för den prenumerationen.

## <a name="define-roles-and-permissions"></a>Definiera roller och behörigheter

Som tjänstleverantör kanske du vill utföra flera uppgifter för en enda kund, vilket kräver olika åtkomst för olika omfång. Du kan definiera så många auktoriseringar som du behöver för att tilldela lämpliga [inbyggda Azure-roller.](../../role-based-access-control/built-in-roles.md) Varje auktorisering innehåller **ett principalId** som refererar till en Azure AD-användare, grupp eller tjänstens huvudnamn i den hanterande klientorganisationen.

> [!NOTE]
> Om det inte uttryckligen anges kan referenser till en "användare" i Azure Lighthouse-dokumentationen gälla för en Azure AD-användare, grupp eller tjänstens huvudnamn i en auktorisering.

För att underlätta hanteringen rekommenderar vi att du använder Azure AD-användargrupper för varje roll när det är möjligt, i stället för för enskilda användare. Detta ger dig flexibiliteten att lägga till eller ta bort enskilda användare i gruppen som har åtkomst, så att du inte behöver upprepa registreringsprocessen för att göra användarändringar. Du kan också tilldela roller till ett huvudnamn för tjänsten, vilket kan vara användbart för automatiseringsscenarier.

> [!IMPORTANT]
> För att du ska kunna lägga till behörigheter för en Azure **AD-grupp måste grupptypen** vara inställd på **Säkerhet.** Det här alternativet väljs när gruppen skapas. Mer information finns i [Skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

När du definierar dina auktoriseringar måste du följa principen om minsta behörighet så att användarna bara har de behörigheter som krävs för att slutföra sitt jobb. Information om vilka roller och metodtips som stöds finns [i Klienter, användare och roller i Azure Lighthouse scenarier.](../concepts/tenants-users-roles.md)

För att definiera auktoriseringar måste du känna till ID-värdena för varje användare, användargrupp eller tjänstens huvudnamn i tjänstproviderns klientorganisation som du vill bevilja åtkomst till. Du behöver även rolldefinitions-ID:t för varje inbyggd roll som du vill tilldela. Om du inte redan har dem kan du hämta dem genom att köra kommandona nedan från tjänstleverantörens klientorganisation.

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
> Vi rekommenderar att [](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) du tilldelar rollen Ta bort tilldelning för registrering av hanterade tjänster vid registrering av en kund, så att användare i din klientorganisation kan ta bort åtkomsten till [delegeringen](remove-delegation.md) senare om det behövs. Om den här rollen inte har tilldelats kan delegerade resurser endast tas bort av en användare i kundens klientorganisation.

## <a name="create-an-azure-resource-manager-template"></a>Skapa en Azure Resource Manager mall

För att registrera kunden behöver du skapa en [Azure Resource Manager](../../azure-resource-manager/index.yml)-mall för ditt erbjudande med följande information. Värdena **mspOfferName** och **mspOfferDescription** är synliga för kunden på [sidan Tjänstleverantörer](view-manage-service-providers.md) i Azure Portal.

|Field  |Definition  |
|---------|---------|
|**mspOfferName**     |Ett namn som beskriver den här definitionen. Det här värdet visas för kunden som rubrik för erbjudandet och måste vara ett unikt värde.        |
|**mspOfferDescription**     |En kort beskrivning av ditt erbjudande (till exempel "Erbjudande om hantering av virtuella Contoso-datorer").      |
|**managedByTenantId**     |Ditt klientorganisations-ID.          |
|**Tillstånd**     |**PrincipalId-värdena** för användare/grupper/SPN:er från din klientorganisation, var och en med **ett principalIdDisplayName** som hjälper kunden att förstå syftet med auktoriseringen och mappad till ett inbyggt **roleDefinitionId-värde** för att ange åtkomstnivån.      |

Onboarding-processen kräver en Azure Resource Manager (finns i [](https://github.com/Azure/Azure-Lighthouse-samples/)vår exempeldatabas) och en motsvarande parameterfil som du ändrar för att matcha din konfiguration och definiera dina auktoriseringar.

> [!IMPORTANT]
> Processen som beskrivs här kräver en separat distribution för varje prenumeration som publiceras, även om du registrera prenumerationer i samma kundklientorganisation. Separata distributioner krävs också om du registrera flera resursgrupper inom olika prenumerationer i samma kundklientorganisation. Registrering av flera resursgrupper inom en enda prenumeration kan dock göras i en distribution.
>
> Separata distributioner krävs också för flera erbjudanden som tillämpas på samma prenumeration (eller resursgrupper i en prenumeration). Varje erbjudande som tillämpas måste använda olika **mspOfferName**.

Vilken mall du väljer beror på om du skapar en hel prenumeration, en resursgrupp eller flera resursgrupper i en prenumeration. Vi tillhandahåller också en mall som kan användas för kunder som har köpt ett erbjudande om hanterade tjänster som du har publicerat till Azure Marketplace, om du föredrar att registrera deras prenumerationer på det här sättet.

|Så här gör du för att publicera  |Använd den här Azure Resource Manager mallen  |Och ändra den här parameterfilen |
|---------|---------|---------|
|Prenumeration   |[delegatedResourceManagement.jspå](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.jspå](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resursgrupp   |[rgDelegatedResourceManagement.jspå](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.jspå](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Flera resursgrupper i en prenumeration   |[multipleRgDelegatedResourceManagement.jspå](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.jspå](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Prenumeration (när du använder ett erbjudande som publicerats till Azure Marketplace)   |[marketplaceDelegatedResourceManagement.jspå](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.jspå](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!TIP]
> Du kan inte publicera en hel hanteringsgrupp i en distribution, men du kan [distribuera en princip på hanteringsgruppsnivå.](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) Principen använder effekten [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) för att kontrollera om varje prenumeration i hanteringsgruppen har delegerats till den angivna hanteringsklienten. Annars skapas tilldelningen baserat på de värden som du anger. Du kommer sedan att ha åtkomst till alla prenumerationer i hanteringsgruppen, även om du måste arbeta med dem som enskilda prenumerationer (i stället för att vidta åtgärder på hanteringsgruppen som helhet).

I följande exempel visas en ändrad **delegatedResourceManagement.parameters.jsfil** som kan användas för att registrera en prenumeration. Parameterfilerna för resursgruppen (som finns i mappen [rg-delegated-resource-management)](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) liknar varandra, men innehåller även en **rgName-parameter** för att identifiera de specifika resursgrupper som ska publiceras.

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

Den sista auktoriseringen i exemplet ovan lägger till **ett principalId** med rollen Administratör för användaråtkomst (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). När du tilldelar den här rollen måste du inkludera **egenskapen delegatedRoleDefinitionIds** och en eller flera inbyggda Azure-roller som stöds. Den användare som skapas i den här [](../../active-directory/managed-identities-azure-resources/overview.md) auktoriseringen kommer att kunna tilldela dessa roller till hanterade identiteter i kundens klientorganisation, vilket krävs för att distribuera principer som [kan åtgärdas.](deploy-policy-remediation.md)  Användaren kan också skapa supportärenden. Inga andra behörigheter som vanligtvis associeras med rollen Administratör för användaråtkomst gäller för detta **principalId.**

## <a name="deploy-the-azure-resource-manager-templates"></a>Distribuera Azure Resource Manager mallar

När du har uppdaterat parameterfilen måste en användare i kundens klientorganisation distribuera Azure Resource Manager i klientorganisationen. En separat distribution krävs för varje prenumeration som du vill registrera (eller för varje prenumeration som innehåller resursgrupper som du vill publicera).

> [!IMPORTANT]
> Den här distributionen måste göras av ett icke-gästkonto i kundens klientorganisation som har en roll med behörigheten, till exempel Ägare , för prenumerationen som publiceras (eller som innehåller de resursgrupper som `Microsoft.Authorization/roleAssignments/write` publiceras). [](../../role-based-access-control/built-in-roles.md#owner) För att hitta användare som kan delegera prenumerationen kan en användare i kundens klientorganisation välja prenumerationen i Azure Portal, öppna Åtkomstkontroll **(IAM)** och visa alla användare med [ägarrollen](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription). 
>
> Om prenumerationen skapades via [programmet Molnlösningsleverantör (CSP)](../concepts/cloud-solution-provider.md)kan alla [](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) användare som har rollen Administratörsagent i din tjänstleverantörsklientorganisation utföra distributionen.

Distributionen kan göras i Azure Portal, med hjälp av PowerShell eller med hjälp av Azure CLI, enligt nedan.

### <a name="azure-portal"></a>Azure Portal

1. I vår [GitHub-lagringsplatsen](https://github.com/Azure/Azure-Lighthouse-samples/) **väljer du knappen Distribuera** till Azure som visas bredvid den mall som du vill använda. Mallen öppnas på Azure-portalen.
1. Ange dina värden för **Msp-erbjudandenamn,** **beskrivning av Msp-erbjudande,** **hanterat av klientorganisations-ID** och **auktoriseringar.** Om du vill kan du välja **Redigera parametrar för** att ange värden för , , och direkt i `mspOfferName` `mspOfferDescription` `managedbyTenantId` `authorizations` parameterfilen. Se till att uppdatera dessa värden i stället för att använda standardvärdena från mallen.
1. Välj **Granska och skapa** och välj sedan **Skapa.**

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

När en kundprenumeration har tagits med i Azure Lighthouse kan användare i tjänstleverantörens klientorganisation se prenumerationen och dess resurser (om de har beviljats åtkomst till den via processen ovan, antingen individuellt eller som medlem i en Azure AD-grupp med rätt behörigheter). Kontrollera att prenumerationen visas på något av följande sätt för att bekräfta detta.  

### <a name="azure-portal"></a>Azure Portal

I tjänstleverantörens klientorganisation:

1. Gå till sidan [Mina kunder.](view-manage-customers.md)
2. Välj **Kunder**.
3. Bekräfta att du kan se prenumerationerna med det erbjudandenamn som du angav i Resource Manager mallen.

> [!IMPORTANT]
> För att kunna se [](view-manage-customers.md)den delegerade prenumerationen i Mina kunder måste [](../../role-based-access-control/built-in-roles.md#reader) användare i tjänstleverantörens klientorganisation ha beviljats rollen Läsare (eller en annan inbyggd roll som omfattar läsaråtkomst) när prenumerationen har publiceras.

I kundens klientorganisation:

1. Gå till sidan [Tjänstleverantörer.](view-manage-service-providers.md)
2. Och sedan välja **Tjänstleverantörserbjudanden**.
3. Bekräfta att du kan se prenumerationerna med det erbjudandenamn som du angav i Resource Manager mallen.

> [!NOTE]
> Det kan ta upp till 15 minuter efter att distributionen är klar innan uppdateringarna visas i Azure Portal. Du kanske kan se uppdateringarna tidigare om du uppdaterar din Azure Resource Manager token genom att uppdatera webbläsaren, logga in och ut eller begära en ny token.

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

# Confirm successful onboarding for Azure Lighthouse

az managedservices definition list
az managedservices assignment list
```

Om du behöver göra ändringar när kunden har registrerats kan du uppdatera [delegeringen](update-delegation.md). Du kan också [ta bort åtkomsten till delegeringen](remove-delegation.md) helt.

## <a name="troubleshooting"></a>Felsökning

Om du inte kan registrera din kund, eller om användarna har problem med att komma åt de delegerade resurserna, kan du läsa följande tips och krav och försöka igen.

- Värdet `managedbyTenantId` får inte vara samma som klientorganisations-ID:t för prenumerationen som publiceras.
- Du kan inte ha flera tilldelningar i samma omfång med samma `mspOfferName` .
- **Resursprovidern Microsoft.ManagedServices** måste vara registrerad för den delegerade prenumerationen. Detta bör ske automatiskt under distributionen, men om inte kan du [registrera det manuellt.](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)
- Auktoriseringar får inte innehålla några användare [med](../../role-based-access-control/built-in-roles.md#owner) den inbyggda rollen Ägare eller några inbyggda roller med [DataActions](../../role-based-access-control/role-definitions.md#dataactions).
- Grupper måste skapas med [**Grupptyp inställt**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md#group-types) på **Säkerhet** och inte **Microsoft 365**.
- Det kan uppstå ytterligare en fördröjning innan åtkomst aktiveras för [kapslade grupper](../..//active-directory/fundamentals/active-directory-groups-membership-azure-portal.md).
- Användare som behöver visa resurser i Azure Portal måste ha rollen [Läsare](../../role-based-access-control/built-in-roles.md#reader) (eller en annan inbyggd roll som omfattar läsaråtkomst).
- De [inbyggda Azure-roller](../../role-based-access-control/built-in-roles.md) som du inkluderar i auktoriseringar får inte innehålla inaktuella roller. Om en inbyggd Azure-roll blir inaktuell förlorar alla användare som har den rollen åtkomst och du kommer inte att kunna registrera ytterligare delegeringar. Du kan åtgärda detta genom att uppdatera mallen så att den endast använder inbyggda roller som stöds och sedan utföra en ny distribution.

## <a name="next-steps"></a>Nästa steg

- Lär dig [mer om hantering av flera klientorganisationsupplevelser.](../concepts/cross-tenant-management-experience.md)
- [Visa och hantera kunder](view-manage-customers.md) genom att gå **till Mina kunder** i Azure Portal.
- Lär dig hur du [uppdaterar eller](update-delegation.md) tar [bort en](remove-delegation.md) delegering.
