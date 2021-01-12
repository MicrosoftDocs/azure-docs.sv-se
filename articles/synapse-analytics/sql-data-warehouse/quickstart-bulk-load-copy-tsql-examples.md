---
title: Autentiseringsmekanismer med KOPIERINGs instruktionen
description: Beskriver autentiseringsmekanismer för Mass inläsning av data
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 07/10/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 25c692ea9a2dce4723472f6812ac46d82b2b318d
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120996"
---
# <a name="securely-load-data-using-synapse-sql"></a>Läs in data på ett säkert sätt med Synapse SQL

Den här artikeln beskriver och innehåller exempel på mekanismer för säker autentisering för [kopierings instruktionen](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). KOPIERINGs instruktionen är det mest flexibla och säkra sättet för Mass inläsning av data i Synapse SQL.
## <a name="supported-authentication-mechanisms"></a>Autentiseringsmekanismer som stöds

I följande matris beskrivs de autentiseringsmetoder som stöds för varje filtyp och lagrings konto. Detta gäller för käll lagrings platsen och fel filens plats.

|                          |                CSV                |                      Parquet                       |                        ORC                         |
| :----------------------: | :-------------------------------: | :------------------------------------------------: | :------------------------------------------------: |
|  **Azure Blob Storage**  | SAS/MSI/TJÄNSTENS HUVUD NAMN/NYCKEL/AAD |                      SAS/NYCKEL                       |                      SAS/NYCKEL                       |
| **Azure Data Lake Gen2** | SAS/MSI/TJÄNSTENS HUVUD NAMN/NYCKEL/AAD | SAS (BLOB<sup>1</sup>)/MSI (DFS<sup>2</sup>)/service-huvud/nyckel/AAD | SAS (BLOB<sup>1</sup>)/MSI (DFS<sup>2</sup>)/service-huvud/nyckel/AAD |

1:. blob-slutpunkten (**. blob**. Core.Windows.net) i sökvägen till den externa platsen krävs för den här autentiseringsmetoden.

2:. DFS-slutpunkten (**. DFS**. Core.Windows.net) i sökvägen till den externa platsen krävs för den här autentiseringsmetoden.

## <a name="a-storage-account-key-with-lf-as-the-row-terminator-unix-style-new-line"></a>A. Lagrings konto nyckel med LF som rad avgränsare (UNIX-typ ny rad)


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
> - Använd det hexadecimala värdet (0x0A) för att ange rad matnings tecknet. Obs! COPY-instruktionen tolkar strängen "\n" som "\r\n" (rad matnings retur).

## <a name="b-shared-access-signatures-sas-with-crlf-as-the-row-terminator-windows-style-new-line"></a>B. Signaturer för delad åtkomst (SAS) med CRLF som rad avslutning (Windows-formatmall, ny rad)
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
> - Ange inte ROWTERMINATOR som ' \r\n ' som ska tolkas som ' \r\r\n ' och kan resultera i tolknings problem

## <a name="c-managed-identity"></a>C. Hanterad identitet

Hanterad identitets autentisering krävs när ditt lagrings konto är kopplat till ett VNet. 

### <a name="prerequisites"></a>Förutsättningar

1. Installera Azure PowerShell med hjälp av den här [guiden](/powershell/azure/install-az-ps?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Om du har ett konto av typen generell användning v1 eller bloblagring måste du först uppgradera till generell användning v2 med hjälp av den här [guiden](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
3. Du måste ha **Tillåt att betrodda Microsoft-tjänster har åtkomst till det här lagrings kontot** under Azure Storage konto **brand väggar och inställningar för virtuella nätverk** . Mer information finns i den här [guiden](../../storage/common/storage-network-security.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#exceptions).

#### <a name="steps"></a>Steg

1. Om du har en fristående SQL-pool registrerar du SQL Server med Azure Active Directory (AAD) med hjälp av PowerShell: 

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Det här steget krävs inte för dedikerade SQL-pooler inom en Synapse-arbetsyta.

1. Om du har en Synapse-arbetsyta registrerar du arbets ytans systemhanterade identitet:

   1. Gå till din Synapse-arbetsyta i Azure Portal
   2. Gå till bladet hanterade identiteter 
   3. Kontrol lera att alternativet Tillåt pipeliner är aktiverat
   
   ![Registrera arbets ytans system-MSI](./media/quickstart-bulk-load-copy-tsql-examples/msi-register-example.png)

1. Skapa ett **Allmänt-syfte v2-lagrings konto** med hjälp av den här [guiden](../../storage/common/storage-account-create.md).

   > [!NOTE]
   >
   > - Om du har ett allmänt v1-eller Blob Storage-konto måste du **först uppgradera till v2** med hjälp av den här [guiden](../../storage/common/storage-account-upgrade.md).
   > - Information om kända problem med Azure Data Lake Storage Gen2 finns i den här [hand boken](../../storage/blobs/data-lake-storage-known-issues.md).

1. Under ditt lagrings konto navigerar du till **Access Control (IAM)** och väljer **Lägg till roll tilldelning**. Tilldela Azure-rollen **Storage BLOB data Contributor** till den server eller arbets yta som är värd för din dedikerade SQL-pool som du har registrerat med Azure Active Directory (AAD).

   > [!NOTE]
   > Endast medlemmar med ägar behörighet kan utföra det här steget. Information om olika inbyggda Azure-roller finns i den här [guiden](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
   
    > [!IMPORTANT]
    > Ange **lagrings** **data** ägare, deltagare eller Reader Azure-roll. De här rollerna skiljer sig från de inbyggda Azure-rollerna för ägare, deltagare och läsare. 

    ![Bevilja Azure RBAC-behörighet att läsa in](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

4. Nu kan du köra COPY-instruktionen som anger "hanterad identitet":

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

1. Under ditt lagrings konto navigerar du till **Access Control (IAM)** och väljer **Lägg till roll tilldelning**. Tilldela Azure AD-användaren rollen som **ägare av BLOB-dataägare, deltagare eller läsare** . 

    > [!IMPORTANT]
    > Ange **lagrings** **data** ägare, deltagare eller Reader Azure-roll. De här rollerna skiljer sig från de inbyggda Azure-rollerna för ägare, deltagare och läsare.

    ![Bevilja Azure RBAC-behörighet att läsa in](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

2. Konfigurera Azure AD-autentisering genom att gå igenom följande [dokumentation](../../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell). 

3. Anslut till din SQL-pool med Active Directory där du kan köra COPY-instruktionen utan att ange några autentiseringsuppgifter:

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
3. [Hämta autentiseringsnyckel](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)
4. [Hämta v1 OAuth 2,0-token-slutpunkt](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications)
5. [Tilldela läs-, skriv-och körnings behörighet till ditt Azure AD-program](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder) på ditt lagrings konto
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
> - Använd **v1** -versionen av OAuth 2,0-token-slutpunkt

## <a name="next-steps"></a>Nästa steg

- Mer information finns i artikeln om att [Kopiera instruktionen](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- Läs artikeln [Översikt över data inläsning](./design-elt-data-loading.md#what-is-elt) för att läsa in metod tips
