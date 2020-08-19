---
title: Tilldela katalog läsare rollen till en Azure AD-grupp och hantera roll tilldelningar
description: Den här artikeln vägleder dig genom att aktivera rollen katalog läsare med Azure AD-grupper för att hantera Azure AD-roll tilldelningar med Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: ca330357e88ff6f4824c74a6048769638542cc29
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556458"
---
# <a name="tutorial-assign-directory-readers-role-to-an-azure-ad-group-and-manage-role-assignments"></a>Självstudie: tilldela katalog läsare rollen till en Azure AD-grupp och hantera roll tilldelningar

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Roll tilldelningen **katalog läsare** till en grupp i den här artikeln är i **offentlig för hands version**. 

Den här artikeln vägleder dig genom att skapa en grupp i Azure Active Directory (Azure AD) och tilldela den gruppen rollen som [**katalog läsare**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) . Behörigheterna för katalog läsare gör det möjligt för grupp ägarna att lägga till ytterligare medlemmar till gruppen, till exempel en [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) för [Azure SQL Database](sql-database-paas-overview.md), [Azure SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md)och [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Detta kringgår behovet av att en [Global administratör](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) eller en [privilegie rad roll administratör](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) ska tilldela rollen katalog läsare direkt för varje Azure SQL-logisk server identitet i klienten.

I den här självstudien används funktionen som introducerades i [använda moln grupper för att hantera roll tilldelningar i Azure Active Directory (för hands version)](../../active-directory/users-groups-roles/roles-groups-concept.md). 

Mer information om fördelarna med att tilldela katalog läsaren rollen till en Azure AD-grupp för Azure SQL finns i [rollen katalog läsare i Azure Active Directory för Azure SQL](authentication-aad-directory-readers-role.md).

## <a name="prerequisites"></a>Förutsättningar

- En Azure AD-instans. Mer information finns i [Konfigurera och hantera Azure AD-autentisering med Azure SQL](authentication-aad-configure.md).
- En SQL Database, SQL-hanterad instans eller Azure-Synapse.

## <a name="directory-readers-role-assignment-using-the-azure-portal"></a>Roll tilldelning för katalog läsare som använder Azure Portal

### <a name="create-a-new-group-and-assign-owners-and-role"></a>Skapa en ny grupp och tilldela ägare och roll

1. En användare med administratörs behörighet som [Global administratör](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) eller [privilegie rad administratör](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) krävs för den här inledande installationen.
1. Få den privilegierade användaren att logga in på [Azure Portal](https://portal.azure.com).
1. Gå till **Azure Active Directory** resursen. Gå till **grupper**under **hanterad**. Välj **ny grupp** för att skapa en ny grupp.
1. Välj **säkerhet** som grupptyp och fyll i resten av fälten. Kontrol lera att inställningen för **Azure AD-roller kan tilldelas gruppen (förhands granskning)** växlas till **Ja**. Tilldela sedan rollen Azure AD- **katalog läsare** till gruppen.
1. Tilldela Azure AD-användare som ägare (n) till den grupp som skapades. En grupp ägare kan vara en vanlig AD-användare utan att någon administrativ roll för Azure AD tilldelats. Ägaren bör vara en användare som hanterar din SQL Database, SQL-hanterade instans eller Azure-Synapse.

   :::image type="content" source="media/authentication-aad-directory-readers-role/new-group.png" alt-text="AAD-ny-grupp":::

1. Välj **Skapa**

### <a name="checking-the-group-that-was-created"></a>Kontrollerar att gruppen har skapats

> [!NOTE]
> Kontrol lera att **grupp typen** är **säkerhet**. *Microsoft 365* grupper stöds inte för Azure SQL.

Om du vill kontrol lera och hantera gruppen som skapades går du tillbaka till fönstret **grupper** i Azure Portal och söker efter ditt grupp namn. Du kan lägga till ytterligare ägare och medlemmar under menyn **ägare** och **medlemmar** i **hanterings** inställningen när du har valt gruppen. Du kan också granska de **tilldelade rollerna** för gruppen.

:::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-group-created.png" alt-text="Azure-AD-Group-created":::

### <a name="add-azure-sql-managed-identity-to-the-group"></a>Lägg till Azure SQL-hanterad identitet i gruppen

> [!NOTE]
> Vi använder SQL-hanterad instans för det här exemplet, men liknande steg kan användas för SQL Database eller Azure-Synapse för att uppnå samma resultat.

För efterföljande steg behövs inte längre den globala administratören eller administratörs användaren för den privilegierade rollen.

1. Logga in på Azure Portal som användaren hanterar SQL-hanterad instans och är ägare till den grupp som skapades tidigare.

1. Hitta namnet på din **SQL-hanterade instans** resurs i Azure Portal.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance.png" alt-text="Azure-AD-hanterad instans":::

   När din SQL-hanterade instans skapas skapades en Azure-identitet för din instans. Den skapade identiteten har samma namn som prefixet för ditt SQL-hanterade instans namn. Du kan hitta tjänstens huvud namn för din SQL-hanterade instans identitet som skapats som ett Azure AD-program genom att följa dessa steg:

    - Gå till **Azure Active Directory** resursen. Under inställningen **Hantera** väljer du **företags program**. **Objekt-ID** är instansens identitet.
    
    :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance-service-principal.png" alt-text="Azure-AD-hanterad instans-service-huvud konto":::

1. Gå till **Azure Active Directory** resursen. Gå till **grupper**under **hanterad**. Välj den grupp som du har skapat. Under den **hanterade** inställningen för gruppen väljer du **medlemmar**. Välj **Lägg till medlemmar** och Lägg till SQL Managed instance service-huvudobjektet som medlem i gruppen genom att söka efter namnet som finns ovan.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-add-managed-instance-service-principal.png" alt-text="Azure-AD – Lägg till-hanterad instans-tjänst-huvud konto":::

> [!NOTE]
> Det kan ta några minuter att sprida behörigheter för tjänstens huvud namn via Azure-systemet och tillåta åtkomst till Azure AD-Graph API. Du kan behöva vänta några minuter innan du etablerar en Azure AD-administratör för SQL-hanterad instans.

### <a name="remarks"></a>Kommentarer

För SQL Database och Azure-Synapse kan Server identiteten skapas när du skapar en logisk Azure SQL-Server eller när servern har skapats. Mer information om hur du skapar eller anger Server identitet i SQL Database eller Azure-Synapse finns i [Aktivera tjänstens huvud namn för att skapa Azure AD-användare](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users).

För SQL-hanterad instans måste rollen **katalog läsare** tilldelas till en hanterad instans identitet innan du kan [Konfigurera en Azure AD-administratör för den hanterade instansen](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

Det krävs inte att tilldela rollen **katalog läsare** till Server identiteten för SQL Database eller Azure-Synapse när du konfigurerar en Azure AD-administratör för den logiska servern. Men om du vill aktivera en Azure AD-objekts skapande i SQL Database eller Azure-Synapse för ett Azure AD-program, krävs rollen **katalog läsare** . Om rollen inte är tilldelad till den logiska SQL-serverns identitet kommer det inte att gå att skapa Azure AD-användare i Azure SQL. Mer information finns i [Azure Active Directory tjänstens huvud namn med Azure SQL](authentication-aad-service-principal.md).

## <a name="directory-readers-role-assignment-using-powershell"></a>Roll tilldelning för katalog läsare med PowerShell

> [!IMPORTANT]
> En [Global administratör](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) eller en [privilegie rad roll administratör](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) måste köra dessa inledande steg. Förutom PowerShell erbjuder Azure AD Microsoft Graph API för att [skapa en roll tilldelnings bara grupp i Azure AD](../../active-directory/users-groups-roles/roles-groups-create-eligible.md#using-microsoft-graph-api).

1. Ladda ned Azure AD Preview PowerShell-modulen med hjälp av följande kommandon. Du kan behöva köra PowerShell som administratör.

    ```powershell
    Install-Module azureadpreview
    Import-Module azureadpreview
    #To verify that the module is ready to use, use the following command:
    Get-Module azureadpreview
    ```

1. Anslut till din Azure AD-klient.

    ```powershell
    Connect-AzureAD
    ```

1. Skapa en säkerhets grupp för att tilldela **katalog läsar** rollen.

    - `DirectoryReaderGroup`, `Directory Reader Group` och `DirRead` kan ändras enligt dina önskemål.

    ```powershell
    $group = New-AzureADMSGroup -DisplayName "DirectoryReaderGroup" -Description "Directory Reader Group" -MailEnabled $False -SecurityEnabled $true -MailNickName "DirRead" -IsAssignableToRole $true
    $group
    ```

1. Tilldela rollen **katalog läsare** till gruppen.

    ```powershell
    # Displays the Directory Readers role information
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Directory Readers'" 
    $roleDefinition
    ```

    ```powershell
    # Assigns the Directory Readers role to the group
    $roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id
    $roleAssignment
    ```

1. Tilldela ägare till gruppen.

    - Ersätt `<username>` med den användare som du vill äga den här gruppen. Du kan lägga till flera ägare genom att upprepa de här stegen.

    ```powershell
    $RefObjectID = Get-AzureADUser -ObjectId "<username>"
    $RefObjectID
    ```

    ```powershell
    $GrOwner = Add-AzureADGroupOwner -ObjectId $group.ID -RefObjectId $RefObjectID.ObjectID
    ```

    Kontrol lera ägare av gruppen med hjälp av följande kommando:

    ```powershell
    Get-AzureADGroupOwner -ObjectId $group.ID
    ```

    Du kan också kontrol lera ägare av gruppen i [Azure Portal](https://portal.azure.com). Följ stegen i [kontrol lera vilken grupp som skapades](#checking-the-group-that-was-created).

### <a name="assigning-the-service-principal-as-a-member-of-the-group"></a>Tilldela tjänstens huvud namn som medlem i gruppen

För efterföljande steg behövs inte längre den globala administratören eller administratörs användaren för den privilegierade rollen.

1. Genom att använda en ägare av gruppen, som även hanterar Azure SQL-resursen, kör du följande kommando för att ansluta till din Azure AD.

    ```powershell
    Connect-AzureAD
    ```

1. Tilldela tjänstens huvud namn som medlem i gruppen som skapades.

    - Ersätt `<ServerName>` med namnet på den logiska Azure SQL-servern eller namnet på den hanterade instansen. Mer information finns i avsnittet [lägga till Azure SQL-tjänsteidentitet i gruppen](#add-azure-sql-managed-identity-to-the-group)

    ```powershell
    # Returns the service principal of your Azure SQL resource
    $miIdentity = Get-AzureADServicePrincipal -SearchString "<ServerName>"
    $miIdentity
    ```

    ```powershell
    # Adds the service principal to the group as a member
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId 
    ```

    Följande kommando returnerar det objekt-ID för tjänstens huvud namn som anger att det har lagts till i gruppen:

    ```powershell
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId
    ```

## <a name="next-steps"></a>Nästa steg

- [Katalog läsar roll i Azure Active Directory för Azure SQL](authentication-aad-directory-readers-role.md)
- [Självstudie: Skapa Azure AD-användare med hjälp av Azure AD-program](authentication-aad-service-principal-tutorial.md)
- [Konfigurera och hantera Azure AD-autentisering med Azure SQL](authentication-aad-configure.md)