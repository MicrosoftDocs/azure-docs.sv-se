---
title: Autentiseringsmekanismer med COPY-instruktionen
description: Beskriver autentiseringsmekanismerna för massinläsning av data
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 07/10/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 510f2556fba42176817b782fe48d01d76eaa3fd7
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568462"
---
# <a name="securely-load-data-using-synapse-sql"></a>Läs in data säkert med Synapse SQL

Den här artikeln visar och innehåller exempel på säkra autentiseringsmekanismer för [COPY-instruktionen](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true). COPY-instruktionen är det mest flexibla och säkra sättet att massinläsning av data i Synapse SQL.
## <a name="supported-authentication-mechanisms"></a>Autentiseringsmekanismer som stöds

I följande matris beskrivs de autentiseringsmetoder som stöds för varje filtyp och lagringskonto. Detta gäller källlagringsplatsen och platsen för felfilen.

|                          |                CSV                |                      Parquet                       |                        Orc                         |
| :----------------------: | :-------------------------------: | :------------------------------------------------: | :------------------------------------------------: |
|  **Azure Blob Storage**  | SAS/MSI/TJÄNSTENS HUVUDNAMN/NYCKEL/AAD |                      SAS/NYCKEL                       |                      SAS/NYCKEL                       |
| **Azure Data Lake Gen2** | SAS/MSI/TJÄNSTENS HUVUDNAMN/NYCKEL/AAD | SAS (blob<sup>1</sup>)/MSI (dfs<sup>2</sup>)/TJÄNSTENS HUVUDNAMN/NYCKEL/AAD | SAS (blob<sup>1</sup>)/MSI (dfs<sup>2</sup>)/TJÄNSTENS HUVUDNAMN/NYCKEL/AAD |

1: .blob-slutpunkten (**.blob**.core.windows.net) i sökvägen till den externa platsen krävs för den här autentiseringsmetoden.

2: .dfs-slutpunkten (**.dfs**.core.windows.net) i sökvägen till den externa platsen krävs för den här autentiseringsmetoden.

## <a name="a-storage-account-key-with-lf-as-the-row-terminator-unix-style-new-line"></a>A. Lagringskontonyckel med LF som radterminator (ny rad i Unix-format)


```sql
--Note when specifying the column list, input field numbers start from 1
COPY INTO target_table (Col_one default 'myStringDefault' 1, Col_two default 1 3)
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='<Your_Account_Key>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='x6RWv4It5F2msnjelv3H4DA80n0QW0daPdw43jM0nyetx4c6CpDkdj3986DX5AHFMIf/YN4y6kkCnU8lb+Wx0Pj+6MDw=='),
    ,ROWTERMINATOR='0x0A' --0x0A specifies to use the Line Feed character (Unix based systems)
)
```
> [!IMPORTANT]
>
> - Använd det hexadecimala värdet (0x0A) för att ange tecknet Radflöde/Ny rad. Observera att COPY-instruktionen tolkar strängen \n som \r\n (vagnretur ny rad).

## <a name="b-shared-access-signatures-sas-with-crlf-as-the-row-terminator-windows-style-new-line"></a>B. Signaturer för delad åtkomst (SAS) med CRLF som radterminator (ny rad i Windows-format)
```sql
COPY INTO target_table
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<Your_SAS_Token>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='?sv=2018-03-28&ss=bfqt&srt=sco&sp=rl&st=2016-10-17T20%3A14%3A55Z&se=2021-10-18T20%3A19%3A00Z&sig=IEoOdmeYnE9%2FKiJDSFSYsz4AkNa%2F%2BTx61FuQ%2FfKHefqoBE%3D'),
    ,ROWTERMINATOR='\n'-- COPY command automatically prefixes the \r character when \n (newline) is specified. This results in carriage return newline (\r\n) for Windows based systems.
)
```

> [!IMPORTANT]
>
> - Ange inte ROWTERMINATOR som \r\n, vilket tolkas som \r\r\n och kan resultera i parsningsproblem

## <a name="c-managed-identity"></a>C. Hanterad identitet

Autentisering med hanterad identitet krävs när ditt lagringskonto är kopplat till ett VNet. 

### <a name="prerequisites"></a>Förutsättningar

1. Installera Azure PowerShell med hjälp av den här [guiden](/powershell/azure/install-az-ps?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Om du har ett konto av typen generell användning v1 eller bloblagring måste du först uppgradera till generell användning v2 med hjälp av den här [guiden](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
3. Du måste ha **Tillåt betrodda Microsoft-tjänster** åtkomst till det här **lagringskontot** aktiverat under inställningsmenyn Azure Storage brandväggar och virtuella nätverk. Mer information finns i den här [guiden](../../storage/common/storage-network-security.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#exceptions).

#### <a name="steps"></a>Steg

1. Om du har en fristående dedikerad SQL-pool registrerar du din SQL-server med Azure Active Directory (AAD) med hjälp av PowerShell: 

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Det här steget krävs inte för dedikerade SQL-pooler i en Synapse-arbetsyta.

1. Om du har en Synapse-arbetsyta registrerar du arbetsytans system hanterade identitet:

   1. Gå till Synapse-arbetsytan i Azure Portal
   2. Gå till bladet Hanterade identiteter 
   3. Kontrollera att alternativet "Tillåt pipelines" är aktiverat
   
   ![Registrera arbetsytesystemet msi](./media/quickstart-bulk-load-copy-tsql-examples/msi-register-example.png)

1. Skapa ett **v2-lagringskonto för generell användning med hjälp** av den här [guiden.](../../storage/common/storage-account-create.md)

   > [!NOTE]
   >
   > - Om du har ett V1- eller Blob Storage-konto för generell användning måste **du först uppgradera till v2 med** hjälp av den här [guiden.](../../storage/common/storage-account-upgrade.md)
   > - Information om kända problem Azure Data Lake Storage Gen2 finns i den här [guiden.](../../storage/blobs/data-lake-storage-known-issues.md)

1. Under ditt lagringskonto går du **till Access Control (IAM)** och väljer Lägg **till rolltilldelning.** Tilldela **Azure-rollen Storage Blob Data-deltagare** till den server eller arbetsyta som är värd för din dedikerade SQL-pool som du har registrerat med Azure Active Directory (AAD).

   > [!NOTE]
   > Endast medlemmar med ägarbehörighet kan utföra det här steget. Information om olika inbyggda Roller i Azure finns i den här [guiden.](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
   
    > [!IMPORTANT]
    > Ange **Azure-rollen** **Storage Blob Data-ägare,** Deltagare eller Läsare. De här rollerna skiljer sig från de inbyggda Rollerna Ägare, Deltagare och Läsare i Azure. 

    ![Bevilja Azure RBAC behörighet att läsa in](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

4. Nu kan du köra COPY-instruktionen med "Hanterad identitet":

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV',
        CREDENTIAL = (IDENTITY = 'Managed Identity'),
    )
    ```

## <a name="d-azure-active-directory-authentication"></a>D. Azure Active Directory-autentisering
#### <a name="steps"></a>Steg

1. Under ditt lagringskonto går du **till Access Control (IAM)** och väljer Lägg **till rolltilldelning**. Tilldela **Azure-rollen Storage Blob Data-ägare, -deltagare eller** -läsare till din Azure AD-användare. 

    > [!IMPORTANT]
    > Ange **Azure-rollen** **Storage Blob Data-ägare,** -deltagare eller -läsare. De här rollerna skiljer sig från de inbyggda Rollerna Ägare, Deltagare och Läsare i Azure.

    ![Bevilja Azure RBAC behörighet att läsa in](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

2. Konfigurera Azure AD-autentisering genom att gå igenom följande [dokumentation](../../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell). 

3. Anslut till SQL-poolen med Hjälp av Active Directory där du nu kan köra COPY-instruktionen utan att ange några autentiseringsuppgifter:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
    )
    ```


## <a name="e-service-principal-authentication"></a>E. Autentisering av tjänstens huvudnamn
#### <a name="steps"></a>Steg

1. [Skapa ett Azure Active Directory program](../..//active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. [Hämta program-ID](../..//active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)
3. [Hämta autentiseringsnyckeln](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)
4. [Hämta V1 OAuth 2.0-tokenslutpunkten](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications)
5. [Tilldela läs-, skriv- och körningsbehörigheter till Azure AD-programmet](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder) på ditt lagringskonto
6. Nu kan du köra COPY-instruktionen:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder0/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
        ,CREDENTIAL=(IDENTITY= '<application_ID>@<OAuth_2.0_Token_EndPoint>' , SECRET= '<authentication_key>')
        --CREDENTIAL should look something like this:
        --,CREDENTIAL=(IDENTITY= '92761aac-12a9-4ec3-89b8-7149aef4c35b@https://login.microsoftonline.com/72f714bf-86f1-41af-91ab-2d7cd011db47/oauth2/token', SECRET='juXi12sZ6gse]woKQNgqwSywYv]7A.M')
    )
    ```

> [!IMPORTANT]
>
> - Använda **V1-versionen** av OAuth 2.0-tokenslutpunkten

## <a name="next-steps"></a>Nästa steg

- Den detaljerade [syntaxen finns i](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) artikeln COPY-instruktion
- Läs [översiktsartikeln om dataläsning](./design-elt-data-loading.md#what-is-elt) för att läsa in metodtips
