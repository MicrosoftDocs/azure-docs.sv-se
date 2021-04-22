---
title: Överföra en Azure-prenumeration till en annan Azure AD-katalog
description: Lär dig hur du överför en Azure-prenumeration och kända relaterade resurser till en Azure Active Directory -katalog (Azure AD).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.workload: identity
ms.date: 04/06/2021
ms.author: rolyon
ms.openlocfilehash: 72dc92ae211034e2a49bc77f60880f17ab15dec7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868185"
---
# <a name="transfer-an-azure-subscription-to-a-different-azure-ad-directory"></a>Överföra en Azure-prenumeration till en annan Azure AD-katalog

Organisationer kan ha flera Azure-prenumerationer. Varje prenumeration är associerad med en viss Azure Active Directory -katalog (Azure AD). För att förenkla hanteringen kanske du vill överföra en prenumeration till en annan Azure AD-katalog. När du överför en prenumeration till en annan Azure AD-katalog överförs inte vissa resurser till målkatalogen. Till exempel tas alla rolltilldelningar och anpassade roller i rollbaserad åtkomstkontroll i Azure (Azure RBAC) **bort permanent** från källkatalogen och överförs inte till målkatalogen.

I den här artikeln beskrivs de grundläggande stegen som du kan följa för att överföra en prenumeration till en annan Azure AD-katalog och skapa om några av resurserna efter överföringen.

> [!NOTE]
> För CSP-prenumerationer (Azure Cloud Solution Providers) stöds inte ändring av Azure AD-katalogen för prenumerationen.

## <a name="overview"></a>Översikt

Överföring av en Azure-prenumeration till en annan Azure AD-katalog är en komplex process som måste planeras och köras noggrant. Många Azure-tjänster kräver säkerhetsobjekt (identiteter) för att fungera normalt eller till och med hantera andra Azure-resurser. Den här artikeln handlar om de flesta Azure-tjänster som är starkt beroende av säkerhetsobjekt, men som inte är heltäckande.

> [!IMPORTANT]
> I vissa fall kan överföring av en prenumeration kräva driftstopp för att slutföra processen. Noggrann planering krävs för att utvärdera om det krävs stilleståndstid för överföringen.

I följande diagram visas de grundläggande steg som du måste följa när du överför en prenumeration till en annan katalog.

1. Förbereda för överföringen

1. Överföra Azure-prenumerationen till en annan katalog

1. Skapa om resurser i målkatalogen, till exempel rolltilldelningar, anpassade roller och hanterade identiteter

    ![Diagram över överföringsprenumeration](./media/transfer-subscription/transfer-subscription.png)

### <a name="deciding-whether-to-transfer-a-subscription-to-a-different-directory"></a>Bestämma om du vill överföra en prenumeration till en annan katalog

Följande är några orsaker till varför du kanske vill överföra en prenumeration:

- På grund av ett företagssammanslagning eller ett förvärv vill du hantera en förvärvad prenumeration i din primära Azure AD-katalog.
- Någon i din organisation har skapat en prenumeration och du vill konsolidera hanteringen till en viss Azure AD-katalog.
- Du har program som är beroende av ett visst prenumerations-ID eller en viss URL och det är inte lätt att ändra programkonfigurationen eller koden.
- En del av din verksamhet har delats upp i ett separat företag och du behöver flytta några av dina resurser till en annan Azure AD-katalog.
- Du vill hantera vissa av dina resurser i en annan Azure AD-katalog i säkerhetsisoleringssyfte.

### <a name="alternate-approaches"></a>Alternativa metoder

Överföring av en prenumeration kräver stilleståndstid för att slutföra processen. Beroende på ditt scenario kan du överväga följande alternativa metoder:

- Skapa resurserna på nytt och kopiera data till målkatalogen och prenumerationen.
- Anta en arkitektur med flera kataloger och lämna prenumerationen i källkatalogen. Använd Azure Lighthouse att delegera resurser så att användare i målkatalogen kan komma åt prenumerationen i källkatalogen. Mer information finns i Azure Lighthouse [i företagsscenarier.](../lighthouse/concepts/enterprise.md)

### <a name="understand-the-impact-of-transferring-a-subscription"></a>Förstå effekten av att överföra en prenumeration

Flera Azure-resurser är beroende av en prenumeration eller en katalog. Beroende på din situation visar följande tabell den kända effekten av att överföra en prenumeration. Genom att utföra stegen i den här artikeln kan du skapa några av de resurser som fanns innan prenumerationsöverföringen.

> [!IMPORTANT]
> I det här avsnittet visas de kända Azure-tjänster eller -resurser som är beroende av din prenumeration. Eftersom resurstyper i Azure ständigt utvecklas kan det finnas ytterligare beroenden som inte anges här som kan orsaka en stor förändring i din miljö. 

| Tjänst eller resurs | Påverkas | Ersättningsgilla | Påverkas du? | Det här kan du göra |
| --------- | --------- | --------- | --------- | --------- |
| Rolltilldelningar | Ja | Ja | [Visa lista över rolltilldelningar](#save-all-role-assignments) | Alla rolltilldelningar tas bort permanent. Du måste mappa användare, grupper och tjänstens huvudnamn till motsvarande objekt i målkatalogen. Du måste skapa rolltilldelningarna på nytt. |
| Anpassade roller | Ja | Ja | [Lista anpassade roller](#save-custom-roles) | Alla anpassade roller tas bort permanent. Du måste skapa om de anpassade rollerna och eventuella rolltilldelningar. |
| System tilldelade hanterade identiteter | Ja | Ja | [Lista hanterade identiteter](#list-role-assignments-for-managed-identities) | Du måste inaktivera och återaktivera de hanterade identiteterna. Du måste skapa rolltilldelningarna på nytt. |
| Användar tilldelade hanterade identiteter | Ja | Ja | [Lista hanterade identiteter](#list-role-assignments-for-managed-identities) | Du måste ta bort, skapa om och koppla de hanterade identiteterna till lämplig resurs. Du måste skapa rolltilldelningarna på nytt. |
| Azure Key Vault | Ja | Ja | [Lista Key Vault åtkomstprinciper](#list-key-vaults) | Du måste uppdatera det klientorganisations-ID som är associerat med nyckelvalven. Du måste ta bort och lägga till nya åtkomstprinciper. |
| Azure SQL databaser med Azure AD-autentiseringsintegrering aktiverat | Ja | Inga | [Kontrollera Azure SQL databaser med Azure AD-autentisering](#list-azure-sql-databases-with-azure-ad-authentication) | Du kan inte överföra Azure SQL databas med Azure AD-autentisering aktiverat till en annan katalog. Mer information finns i Använda [Azure Active Directory autentisering.](../azure-sql/database/authentication-aad-overview.md) | 
| Azure Storage och Azure Data Lake Storage Gen2 | Ja | Ja |  | Du måste skapa om alla ACL:er. |
| Azure Data Lake Storage Gen1 | Ja | Ja |  | Du måste skapa om alla ACL:er. |
| Azure Files | Ja | Ja |  | Du måste skapa om alla ACL:er. |
| Azure File Sync | Ja | Ja |  | Tjänsten för synkronisering av lagring och/eller lagringskontot kan flyttas till en annan katalog. Mer information finns i [Vanliga frågor och svar om Azure Files](../storage/files/storage-files-faq.md#azure-file-sync) |
| Azure Managed Disks | Ja | Ja |  |  Om du använder diskkrypteringsuppsättningar för att kryptera Managed Disks med kund hanterade nycklar måste du inaktivera och återaktivera de systemtilldeerade identiteter som är associerade med diskkrypteringsuppsättningar. Och du måste skapa rolltilldelningarna igen, vilket innebär att du återigen måste bevilja nödvändiga behörigheter till diskkrypteringsuppsättningarna i nyckelvalven. |
| Azure Kubernetes Service | Ja | Inga |  | Du kan inte överföra ditt AKS-kluster och dess associerade resurser till en annan katalog. Mer information finns i [Vanliga frågor och svar om Azure Kubernetes Service (AKS)](../aks/faq.md) |
| Azure Policy | Ja | Inga | Alla Azure Policy objekt, inklusive anpassade definitioner, tilldelningar, undantag och efterlevnadsdata. | Du måste [exportera,](../governance/policy/how-to/export-resources.md)importera och tilldela om definitioner. Skapa sedan nya principtilldelningar och eventuella nödvändiga [principundantag.](../governance/policy/concepts/exemption-structure.md) |
| Azure Active Directory Domain Services | Ja | Inga |  | Du kan inte överföra en Azure AD Domain Services hanterad domän till en annan katalog. Mer information finns i [Vanliga frågor och svar om Azure Active Directory (AD) Domain Services](../active-directory-domain-services/faqs.md) |
| Appregistreringar | Ja | Ja |  |  |

> [!WARNING]
> Om du använder kryptering i vila för en resurs, till exempel ett lagringskonto eller  en SQL-databas, som är beroende av ett nyckelvalv som inte finns i samma prenumeration som överförs, kan det leda till ett oåterkalleligt scenario. Om du har den här situationen bör du vidta åtgärder för att använda ett annat nyckelvalv eller tillfälligt inaktivera kund hanterade nycklar för att undvika det här oåterkalleliga scenariot.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra de här stegen behöver du:

- [Bash i Azure Cloud Shell](../cloud-shell/overview.md) eller [Azure CLI](/cli/azure)
- Kontoadministratör för den prenumeration som du vill överföra i källkatalogen
- [Ägarroll](built-in-roles.md#owner) i målkatalogen

## <a name="step-1-prepare-for-the-transfer"></a>Steg 1: Förbered för överföringen

### <a name="sign-in-to-source-directory"></a>Logga in på källkatalogen

1. Logga in på Azure som administratör.

1. Hämta en lista över dina prenumerationer med [kommandot az account list.](/cli/azure/account#az_account_list)

    ```azurecli
    az account list --output table
    ```

1. Använd [az account set för](/cli/azure/account#az_account_set) att ange den aktiva prenumeration som du vill överföra.

    ```azurecli
    az account set --subscription "Marketing"
    ```

### <a name="install-the-azure-resource-graph-extension"></a>Installera Azure Resource Graph tillägget

 Med Azure CLI-tillägget [för Azure Resource Graph](../governance/resource-graph/index.yml), *resource-graph*, kan du använda [kommandot az graph](/cli/azure/graph) för att köra frågor mot resurser som hanteras av Azure Resource Manager. Du använder det här kommandot i senare steg.

1. Använd [az extension list](/cli/azure/extension#az_extension_list) för att se om du har *resource-graph-tillägget* installerat.

    ```azurecli
    az extension list
    ```

1. Om inte installerar du *resource-graph-tillägget.*

    ```azurecli
    az extension add --name resource-graph
    ```

### <a name="save-all-role-assignments"></a>Spara alla rolltilldelningar

1. Använd [az role assignment list för](/cli/azure/role/assignment#az_role_assignment_list) att lista alla rolltilldelningar (inklusive ärvda rolltilldelningar).

    Om du vill göra det enklare att granska listan kan du exportera utdata som JSON, TSV eller en tabell. Mer information finns i List [role assignments using Azure RBAC and Azure CLI (Lista rolltilldelningar med Azure RBAC och Azure CLI).](role-assignments-list-cli.md)

    ```azurecli
    az role assignment list --all --include-inherited --output json > roleassignments.json
    az role assignment list --all --include-inherited --output tsv > roleassignments.tsv
    az role assignment list --all --include-inherited --output table > roleassignments.txt
    ```

1. Spara listan över rolltilldelningar.

    När du överför en prenumeration tas alla rolltilldelningar **bort permanent,** så det är viktigt att spara en kopia.

1. Granska listan över rolltilldelningar. Det kan finnas rolltilldelningar som du inte behöver i målkatalogen.

### <a name="save-custom-roles"></a>Spara anpassade roller

1. Använd [az role definition list för att](/cli/azure/role/definition#az_role_definition_list) lista dina anpassade roller. Mer information finns i Skapa [eller uppdatera anpassade Azure-roller med Hjälp av Azure CLI.](custom-roles-cli.md)

    ```azurecli
    az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
    ```

1. Spara varje anpassad roll som du behöver i målkatalogen som en separat JSON-fil.

    ```azurecli
    az role definition list --name <custom_role_name> > customrolename.json
    ```

1. Gör kopior av de anpassade rollfilerna.

1. Ändra varje kopia så att den använder följande format.

    Du kommer att använda de här filerna senare för att skapa om de anpassade rollerna i målkatalogen.

    ```json
    {
      "Name": "",
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": []
    }
    ```

### <a name="determine-user-group-and-service-principal-mappings"></a>Fastställa mappningar av användare, grupper och tjänstens huvudnamn

1. Baserat på din lista över rolltilldelningar bestämmer du vilka användare, grupper och tjänsthuvudnamn som du ska mappa till i målkatalogen.

    Du kan identifiera typen av huvudnamn genom att titta på `principalType` egenskapen i varje rolltilldelning.

1. Om det behövs skapar du alla användare, grupper eller tjänstens huvudnamn som du behöver i målkatalogen.

### <a name="list-role-assignments-for-managed-identities"></a>Visa en lista över rolltilldelningar för hanterade identiteter

Hanterade identiteter uppdateras inte när en prenumeration överförs till en annan katalog. Därför kommer alla befintliga system-tilldelade eller användar tilldelade hanterade identiteter att brytas. Efter överföringen kan du återaktivera alla system tilldelade hanterade identiteter. För användar tilldelade hanterade identiteter måste du skapa dem igen och koppla dem i målkatalogen.

1. Granska listan [över Azure-tjänster som stöder hanterade identiteter](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) och notera var du kan använda hanterade identiteter.

1. Använd [az ad sp list för](/cli/azure/ad/sp#az_ad_sp_list) att lista dina system-tilldelade och användar-tilldelade hanterade identiteter.

    ```azurecli
    az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
    ```

1. I listan över hanterade identiteter bestämmer du vilka som är system-tilldelade och vilka som är användar tilldelade. Du kan använda följande kriterier för att fastställa typen.

    | Kriterie | Hanterad identitetstyp |
    | --- | --- |
    | `alternativeNames` egenskapen innehåller `isExplicit=False` | System-tilldelad |
    | `alternativeNames` egenskapen inkluderar inte `isExplicit` | System-tilldelad |
    | `alternativeNames` egenskapen innehåller `isExplicit=True` | Användar tilldelad |

    Du kan också använda [az identity list för](/cli/azure/identity#az_identity_list) att bara lista användar-tilldelade hanterade identiteter. Mer information finns i [Skapa, visa eller ta bort en användar tilldelad hanterad identitet med hjälp av Azure CLI.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

    ```azurecli
    az identity list
    ```

1. Hämta en lista över värdena `objectId` för dina hanterade identiteter.

1. Sök i listan över rolltilldelningar för att se om det finns några rolltilldelningar för dina hanterade identiteter.

### <a name="list-key-vaults"></a>Visa en lista över nyckelvalv

När du skapar ett nyckelvalv är det automatiskt kopplat till Azure Active Directory klientorganisations-ID för prenumerationen som det skapas i. Alla åtkomstprincipposter knyts också till detta klient-ID. Mer information finns i Flytta [en Azure Key Vault till en annan prenumeration.](../key-vault/general/move-subscription.md)

> [!WARNING]
> Om du använder kryptering i vila för en resurs, till exempel ett lagringskonto eller  en SQL-databas, som är beroende av ett nyckelvalv som inte finns i samma prenumeration som överförs, kan det leda till ett oåterkalleligt scenario. Om du har den här situationen bör du vidta åtgärder för att använda ett annat nyckelvalv eller tillfälligt inaktivera kund hanterade nycklar för att undvika det här oåterkalleliga scenariot.

- Om du har ett nyckelvalv använder du [az keyvault show för](/cli/azure/keyvault#az_keyvault_show) att lista åtkomstprinciperna. Mer information finns i Tilldela [en Key Vault åtkomstprincip.](../key-vault/general/assign-access-policy-cli.md)

    ```azurecli
    az keyvault show --name MyKeyVault
    ```

### <a name="list-azure-sql-databases-with-azure-ad-authentication"></a>Lista Azure SQL databaser med Azure AD-autentisering

- Använd [az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) och az [graph-tillägget](/cli/azure/graph) för att se om du använder Azure SQL med Azure AD-autentiseringsintegrering aktiverat. Mer information finns i Konfigurera [och hantera Azure Active Directory autentisering med SQL](../azure-sql/database/authentication-aad-configure.md).

    ```azurecli
    az sql server ad-admin list --ids $(az graph query -q 'resources | where type == "microsoft.sql/servers" | project id' -o tsv | cut -f1)
    ```

### <a name="list-acls"></a>Lista ACL:er

1. Om du använder Azure Data Lake Storage Gen1 listar du de ACL:er som tillämpas på en fil med hjälp av Azure Portal eller PowerShell.

1. Om du använder en Azure Data Lake Storage Gen2 listar du de ACL:er som tillämpas på en fil med hjälp av Azure Portal eller PowerShell.

1. Om du använder en Azure Files lista de ACL:er som tillämpas på alla filer.

### <a name="list-other-known-resources"></a>Lista andra kända resurser

1. Använd [az account show för](/cli/azure/account#az_account_show) att hämta ditt prenumerations-ID.

    ```azurecli
    subscriptionId=$(az account show --query id | sed -e 's/^"//' -e 's/"$//')
    ```

1. Använd [tillägget az graph för](/cli/azure/graph) att lista andra Azure-resurser med kända Azure AD-katalogberoenden.

    ```azurecli
    az graph query -q \
    'resources | where type != "microsoft.azureactivedirectory/b2cdirectories" | where  identity <> "" or properties.tenantId <> "" or properties.encryptionSettingsCollection.enabled == true | project name, type, kind, identity, tenantId, properties.tenantId' \
    --subscriptions $subscriptionId --output table
    ```

## <a name="step-2-transfer-the-subscription"></a>Steg 2: Överför prenumerationen

I det här steget överför du prenumerationen från källkatalogen till målkatalogen. Stegen skiljer sig beroende på om du även vill överföra faktureringsägarskapet.

> [!WARNING]
> När du överför prenumerationen tas alla rolltilldelningar i **källkatalogen bort permanent** och kan inte återställas. Du kan inte gå tillbaka när du överför prenumerationen. Se till att du slutför föregående steg innan du utför det här steget.

1. Avgör om du även vill överföra faktureringsägarskapet till ett annat konto.

1. Överför prenumerationen till en annan katalog.

    - Om du vill behålla det aktuella faktureringsägarskapet följer du stegen i Associera eller lägga till en Azure-prenumeration till [din Azure Active Directory klientorganisation.](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
    - Om du även vill överföra faktureringsägarskapet följer du stegen i Överföra [faktureringsägarskap för en Azure-prenumeration till ett annat konto.](../cost-management-billing/manage/billing-subscription-transfer.md) Om du vill överföra prenumerationen till en annan katalog måste du markera kryssrutan **Prenumeration på Azure AD-klientorganisation.**

1. När du har överfört prenumerationen återgår du till den här artikeln för att skapa resurserna i målkatalogen igen.

## <a name="step-3-re-create-resources"></a>Steg 3: Skapa resurser på nytt

### <a name="sign-in-to-target-directory"></a>Logga in på målkatalogen

1. I målkatalogen loggar du in som den användare som godkände överföringsbegäran.

    Endast den användare i det nya kontot som godkände överföringsbegäran har åtkomst till att hantera resurserna.

1. Hämta en lista över dina prenumerationer med [kommandot az account list.](/cli/azure/account#az_account_list)

    ```azurecli
    az account list --output table
    ```

1. Använd [az account set för](/cli/azure/account#az_account_set) att ange den aktiva prenumeration som du vill använda.

    ```azurecli
    az account set --subscription "Contoso"
    ```

### <a name="create-custom-roles"></a>Skapa anpassade roller
        
- Använd [az role definition create för](/cli/azure/role/definition#az_role_definition_create) att skapa varje anpassad roll från de filer som du skapade tidigare. Mer information finns i Skapa [eller uppdatera anpassade Azure-roller med Hjälp av Azure CLI.](custom-roles-cli.md)

    ```azurecli
    az role definition create --role-definition <role_definition>
    ```

### <a name="assign-roles"></a>Tilldela roller

- Använd [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) för att tilldela roller till användare, grupper och tjänstens huvudnamn. Mer information finns i Tilldela [Azure-roller med hjälp av Azure CLI.](role-assignments-cli.md)

    ```azurecli
    az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
    ```

### <a name="update-system-assigned-managed-identities"></a>Uppdatera system tilldelade hanterade identiteter

1. Inaktivera och återaktivera system tilldelade hanterade identiteter.

    | Azure-tjänst | Mer information | 
    | --- | --- |
    | Virtuella datorer | [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med hjälp av Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#system-assigned-managed-identity) |
    | Skalningsuppsättningar för virtuella datorer | [Konfigurera hanterade identiteter för Azure-resurser på en VM-skalningsuppsättning med hjälp av Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#system-assigned-managed-identity) |
    | Övriga tjänster | [Tjänster som stöder hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) |

1. Använd [az role assignment create för](/cli/azure/role/assignment#az_role_assignment_create) att tilldela roller till system tilldelade hanterade identiteter. Mer information finns i Tilldela [en hanterad identitet åtkomst till en resurs med hjälp av Azure CLI.](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-user-assigned-managed-identities"></a>Uppdatera användar tilldelade hanterade identiteter

1. Ta bort, skapa om och bifoga användar tilldelade hanterade identiteter.

    | Azure-tjänst | Mer information | 
    | --- | --- |
    | Virtuella datorer | [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med Hjälp av Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) |
    | Skalningsuppsättningar för virtuella datorer | [Konfigurera hanterade identiteter för Azure-resurser på en VM-skalningsuppsättning med Hjälp av Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#user-assigned-managed-identity) |
    | Övriga tjänster | [Tjänster som stöder hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)<br/>[Skapa, visa eller ta bort en användar tilldelad hanterad identitet med hjälp av Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |

1. Använd [az role assignment create för](/cli/azure/role/assignment#az_role_assignment_create) att tilldela roller till användartilldelning av hanterade identiteter. Mer information finns i Tilldela [en hanterad identitet åtkomst till en resurs med hjälp av Azure CLI.](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-key-vaults"></a>Uppdatera nyckelvalv

I det här avsnittet beskrivs de grundläggande stegen för att uppdatera dina nyckelvalv. Mer information finns i Flytta [en Azure Key Vault till en annan prenumeration.](../key-vault/general/move-subscription.md)

1. Uppdatera det klient-ID som är associerat med alla befintliga nyckelvalv i prenumerationen till målkatalogen.

1. Ta bort alla åtkomstprincipposter.

1. Lägg till nya åtkomstprincipposter som är associerade med målkatalogen.

### <a name="update-acls"></a>Uppdatera ACL:er

1. Om du använder en Azure Data Lake Storage Gen1 tilldelar du lämpliga ACL:er. Mer information finns i Skydda [data som lagras i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

1. Om du använder en Azure Data Lake Storage Gen2 tilldelar du lämpliga ACL:er. Mer information finns i [Åtkomstkontroll i Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

1. Om du använder en Azure Files tilldelar du lämpliga ACL:er.

### <a name="review-other-security-methods"></a>Granska andra säkerhetsmetoder

Även om rolltilldelningar tas bort under överföringen kan användare i det ursprungliga ägarkontot fortsätta att ha åtkomst till prenumerationen via andra säkerhetsmetoder, inklusive:

- Åtkomstnycklar för tjänster såsom Storage.
- [Hanteringscertifikat](../cloud-services/cloud-services-certs-create.md) som ger användaradministratören åtkomst till prenumerationsresurser.
- Autentiseringsuppgifter för fjärråtkomst för tjänster såsom Azure Virtual Machines.

Om din avsikt är att ta bort åtkomst från användare i källkatalogen så att de inte har åtkomst i målkatalogen bör du överväga att rotera eventuella autentiseringsuppgifter. Tills autentiseringsuppgifterna har uppdaterats fortsätter användarna att ha åtkomst efter överföringen.

1. Rotera åtkomstnycklar för lagringskonto. Mer information finns i Hantera [åtkomstnycklar för lagringskonto.](../storage/common/storage-account-keys-manage.md)

1. Om du använder åtkomstnycklar för andra tjänster, till exempel Azure SQL Database eller Azure Service Bus-meddelanden, roterar du åtkomstnycklar.

1. För resurser som använder hemligheter öppnar du inställningarna för resursen och uppdaterar hemligheten.

1. Uppdatera certifikatet för resurser som använder certifikat.

## <a name="next-steps"></a>Nästa steg

- [Överföra faktureringsägarskap för en Azure-prenumeration till ett annat konto](../cost-management-billing/manage/billing-subscription-transfer.md)
- [Överför Azure-prenumerationer mellan prenumeranter och molnlösningsleverantörer](../cost-management-billing/manage/transfer-subscriptions-subscribers-csp.md)
- [Associera eller lägga till en Azure-prenumeration till Azure Active Directory-klienten](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
- [Azure Lighthouse i företagsscenarier](../lighthouse/concepts/enterprise.md)
