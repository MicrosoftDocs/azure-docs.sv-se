---
title: Introduktion till Microsoft Spark-verktyg
description: 'Självstudie: MSSparkutils i Azure Synapse Analytics-anteckningsböcker'
author: ruixinxu
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 09/10/2020
ms.author: ruxu
ms.reviewer: ''
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 58672bd68d9a2ea85f58b3761f3b89098b9f5afc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368667"
---
# <a name="introduction-to-microsoft-spark-utilities"></a>Introduktion till Microsoft Spark-verktyg

Microsoft Spark-verktyg (MSSparkUtils) är ett inbyggt paket som hjälper dig att enkelt utföra vanliga uppgifter. Du kan använda MSSparkUtils för att arbeta med fil system, för att hämta miljövariabler och för att arbeta med hemligheter. MSSparkUtils är tillgängliga i `PySpark (Python)` , `Scala` och `.NET Spark (C#)` antecknings böcker och Synapse-pipeliner.

## <a name="pre-requisites"></a>Förutsättningar

### <a name="configure-access-to-azure-data-lake-storage-gen2"></a>Konfigurera åtkomst till Azure Data Lake Storage Gen2 

Synapse Notebooks använder Azure Active Directory (Azure AD) genom strömning för att få åtkomst till ADLS Gen2-konton. Du måste vara en **Blob Storage data deltagare** för att få åtkomst till ADLS Gen2-kontot (eller mappen). 

Synapse pipelines använder arbets ytans identitet (MSI) för att komma åt lagrings kontona. Om du vill använda MSSparkUtils i dina pipeline-aktiviteter måste din arbets ytans identitet **Blob Storage data deltagare** för att få åtkomst till ADLS Gen2-kontot (eller mappen).

Följ dessa steg för att se till att din Azure AD-och Workspace-MSI har åtkomst till det ADLS Gen2 kontot:
1. Öppna [Azure Portal](https://portal.azure.com/) och det lagrings konto som du vill få åtkomst till. Du kan gå till den angivna behållaren som du vill komma åt.
2. Välj **åtkomst kontroll (IAM)** i den vänstra panelen.
3. Tilldela **ditt Azure AD-konto** och **din arbets yta identitet** (samma som din arbets yta namn) till rollen **Storage BLOB data Contributor** på lagrings kontot om det inte redan har tilldelats. 
4. Välj **Spara**.

Du kan komma åt data på ADLS Gen2 med Synapse Spark via följande URL:

<code>abfss://<container_name>@<storage_account_name>.dfs.core.windows.net/<path></code>

### <a name="configure-access-to-azure-blob-storage"></a>Konfigurera åtkomst till Azure Blob Storage  

Synapse utnyttjar **signaturen för delad åtkomst (SAS)** för att få åtkomst till Azure Blob Storage. För att undvika att exponera SAS-nycklar i koden rekommenderar vi att du skapar en ny länkad tjänst i Synapse-arbetsytan till det Azure Blob Storage-konto som du vill ha åtkomst till.

Följ dessa steg om du vill lägga till en ny länkad tjänst för ett Azure Blob Storage-konto:

1. Öppna [Azure Synapse Studio](https://web.azuresynapse.net/).
2. Välj **Hantera** i den vänstra panelen och välj **länkade tjänster** under de **externa anslutningarna**.
3. Sök i **Azure Blob Storage** i den **nya länkade tjänst** panelen till höger.
4. Välj **Fortsätt**.
5. Välj Azure Blob Storage-kontot för att komma åt och konfigurera namnet på den länkade tjänsten. Föreslå Använd **konto nyckel** för **autentiseringsmetoden**.
6. Kontrol lera att inställningarna är korrekta genom att välja **Testa anslutning** .
7. Välj **skapa** först och klicka på **publicera alla** för att spara ändringarna. 

Du kan komma åt data på Azure Blob Storage med Synapse Spark via följande URL:

<code>wasb[s]://<container_name>@<storage_account_name>.blob.core.windows.net/<path></code>

Här är ett kod exempel:

:::zone pivot = "programming-language-python"

```python
from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val blob_account_name = "" // replace with your blob name
val blob_container_name = "" //replace with your container name
val blob_relative_path = "/" //replace with your relative folder path
val linked_service_name = "" //replace with your linked service name


val blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

val wasbs_path = f"wasbs://$blob_container_name@$blob_account_name.blob.core.windows.net/$blob_relative_path"
spark.conf.set(f"fs.azure.sas.$blob_container_name.$blob_account_name.blob.core.windows.net",blob_sas_token)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var blob_account_name = "";  // replace with your blob name
var blob_container_name = "";     // replace with your container name
var blob_relative_path = "";  // replace with your relative folder path
var linked_service_name = "";    // replace with your linked service name
var blob_sas_token = Credentials.GetConnectionStringOrCreds(linked_service_name);

spark.SparkContext.GetConf().Set($"fs.azure.sas.{blob_container_name}.{blob_account_name}.blob.core.windows.net", blob_sas_token);

var wasbs_path = $"wasbs://{blob_container_name}@{blob_account_name}.blob.core.windows.net/{blob_relative_path}";

Console.WriteLine(wasbs_path);

```

::: zone-end 
 
###  <a name="configure-access-to-azure-key-vault"></a>Konfigurera åtkomst till Azure Key Vault

Du kan lägga till en Azure Key Vault som en länkad tjänst för att hantera dina autentiseringsuppgifter i Synapse. Följ dessa steg om du vill lägga till en Azure Key Vault som en länkad Synapse-tjänst:
1. Öppna [Azure Synapse Studio](https://web.azuresynapse.net/).
2. Välj **Hantera** i den vänstra panelen och välj **länkade tjänster** under de **externa anslutningarna**.
3. Sök **Azure Key Vault** i den **nya länkade tjänst** panelen till höger.
4. Välj det Azure Key Vault kontot för att komma åt och konfigurera namnet på den länkade tjänsten.
5. Kontrol lera att inställningarna är korrekta genom att välja **Testa anslutning** .
6. Välj **skapa** först och klicka på **publicera alla** för att spara ändringen. 

Synapse Notebooks använder Azure Active Directory (Azure AD) genom strömning för att få åtkomst till Azure Key Vault. Synapse pipelines använder arbets ytans identitet (MSI) för att få åtkomst till Azure Key Vault. För att se till att din kod fungerar både i den bärbara datorn och i Synapse pipeline rekommenderar vi att du beviljar behörigheten hemlig åtkomst för både ditt Azure AD-konto och din arbets ytans identitet.

Följ de här stegen för att ge hemlig åtkomst till din arbets yta identitet:
1. Öppna [Azure Portal](https://portal.azure.com/) och Azure Key Vault som du vill komma åt. 
2. Välj **åtkomst principer** i den vänstra panelen.
3. Välj **Lägg till åtkomst princip**: 
    - Välj **nyckel, hemlighet, & certifikat hantering** som config-mall.
    - Välj **ditt Azure AD-konto** och **din arbets ytans identitet** (samma som din arbets ytans namn) i Välj huvud konto eller kontrol lera att den redan är tilldelad. 
4. Välj **Välj** och **Lägg till**.
5. Välj knappen **Spara** för att genomföra ändringarna.  

## <a name="file-system-utilities"></a>Verktyg för fil system

`mssparkutils.fs` innehåller verktyg för att arbeta med olika fil system, inklusive Azure Data Lake Storage Gen2 (ADLS Gen2) och Azure-Blob Storage. Se till att du konfigurerar åtkomst till [Azure Data Lake Storage Gen2](#configure-access-to-azure-data-lake-storage-gen2) och [Azure Blob Storage](#configure-access-to-azure-blob-storage) på lämpligt sätt.

Kör följande kommandon för en översikt över tillgängliga metoder:

:::zone pivot = "programming-language-python"

```python
from notebookutils import mssparkutils
mssparkutils.fs.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Notebook.MSSparkUtils;
FS.Help()
```

::: zone-end

Resultat i:
```
mssparkutils.fs provides utilities for working with various FileSystems.

Below is overview about the available methods:

cp(from: String, to: String, recurse: Boolean = false): Boolean -> Copies a file or directory, possibly across FileSystems
mv(from: String, to: String, recurse: Boolean = false): Boolean -> Moves a file or directory, possibly across FileSystems
ls(dir: String): Array -> Lists the contents of a directory
mkdirs(dir: String): Boolean -> Creates the given directory if it does not exist, also creating any necessary parent directories
put(file: String, contents: String, overwrite: Boolean = false): Boolean -> Writes the given String out to a file, encoded in UTF-8
head(file: String, maxBytes: int = 1024 * 100): String -> Returns up to the first 'maxBytes' bytes of the given file as a String encoded in UTF-8
append(file: String, content: String, createFileIfNotExists: Boolean): Boolean -> Append the content to a file
rm(dir: String, recurse: Boolean = false): Boolean -> Removes a file or directory

Use mssparkutils.fs.help("methodName") for more info about a method.

```

### <a name="list-files"></a>Lista filer
Visa en lista med innehållet i en katalog.


:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.ls('Your directory path')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.ls("Your directory path")
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Ls("Your directory path")
```

::: zone-end


### <a name="view-file-properties"></a>Visa fil egenskaper
Returnerar fil egenskaper, inklusive fil namn, fil Sök väg, fil storlek och om det är en katalog och en fil.

:::zone pivot = "programming-language-python"

```python
files = mssparkutils.fs.ls('Your directory path')
for file in files:
    print(file.name, file.isDir, file.isFile, file.path, file.size)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val files = mssparkutils.fs.ls("/")
files.foreach{
    file => println(file.name,file.isDir,file.isFile,file.size)
}
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var Files = FS.Ls("/");
foreach(var File in Files) {
    Console.WriteLine(File.Name+" "+File.IsDir+" "+File.IsFile+" "+File.Size);
}
```

::: zone-end

### <a name="create-new-directory"></a>Skapa ny katalog

Skapar den aktuella katalogen om den inte finns och eventuella nödvändiga överordnade kataloger.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mkdirs('new directory name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mkdirs("new directory name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mkdirs("new directory name")
```

::: zone-end

### <a name="copy-file"></a>Kopiera fil

Kopierar en fil eller katalog. Stöder kopiering i fil system.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.cp('source file or directory', 'destination file or directory', True)# Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```

::: zone-end

### <a name="preview-file-content"></a>Förhandsgranska fil innehåll

Returnerar upp till de första "maxBytes" byte för den aktuella filen som en sträng som kodats i UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.head('file path', maxBytes to read)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.head("file path", maxBytes to read)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Head("file path", maxBytes to read)
```

::: zone-end

### <a name="move-file"></a>Flytta fil

Flyttar en fil eller katalog. Stöder flyttning i fil system.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mv('source file or directory', 'destination directory', True) # Set the last parameter as True to firstly create the parent directory if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mv("source file or directory", "destination directory", true) // Set the last parameter as True to firstly create the parent directory if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mv("source file or directory", "destination directory", true)
```

::: zone-end

### <a name="write-file"></a>Skriv fil

Skriver den aktuella strängen ut till en fil som kodats i UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.put("file path", "content to write", True) # Set the last parameter as True to overwrite the file if it existed already
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

### <a name="append-content-to-a-file"></a>Lägg till innehåll i en fil

Lägger till den strängen i en fil som kodats i UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.append('file path','content to append',True) # Set the last parameter as True to create the file if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

### <a name="delete-file-or-directory"></a>Ta bort fil eller katalog

Tar bort en fil eller en katalog.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.rm('file path', True) # Set the last parameter as True to remove all files and directories recursively 
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end


## <a name="credentials-utilities"></a>Verktyg för autentiseringsuppgifter

Du kan använda MSSparkUtils för autentiseringsuppgifter för att hämta åtkomsttoken för länkade tjänster och hantera hemligheter i Azure Key Vault. 

Kör följande kommando för att få en översikt över tillgängliga metoder:

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.Help()
```

::: zone-end

Få resultat:

```
getToken(audience, name): returns AAD token for a given audience, name (optional)
isValidToken(token): returns true if token hasn't expired
getConnectionStringOrCreds(linkedService): returns connection string or credentials for linked service
getSecret(akvName, secret, linkedService): returns AKV secret for a given AKV linked service, akvName, secret key
getSecret(akvName, secret): returns AKV secret for a given akvName, secret key
putSecret(akvName, secretName, secretValue, linkedService): puts AKV secret for a given akvName, secretName
putSecret(akvName, secretName, secretValue): puts AKV secret for a given akvName, secretName
```

### <a name="get-token"></a>Hämta token

Returnerar Azure AD-token för en specifik mål grupp, namn (valfritt). I tabellen nedan visas alla tillgängliga mål grupps typer: 

|Audience-typ|Mål grupps nyckel|
|--|--|
|Typ av matchning av mål grupp|Filmen|
|Lagrings mål resurs|Lagrings|
|Audience-resurs för informations lager|DW|
|Data Lake Audience-resurs|'AzureManagement'|
|Mål grupps resurs|DataLakeStore|
|Azure OSSDB-målgrupps resurs|'AzureOSSDB'|
|Azure Synapse-resurs|'Synapse'|
|Azure Data Factory resurs|AUTOMATISK|

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getToken('audience Key')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getToken("audience Key")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetToken("audience Key")
```

::: zone-end


### <a name="validate-token"></a>Verifiera token

Returnerar true om token inte har gått ut.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.isValidToken('your token')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.isValidToken("your token")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.IsValidToken("your token")
```

::: zone-end


### <a name="get-connection-string-or-credentials-for-linked-service"></a>Hämta anslutnings sträng eller autentiseringsuppgifter för den länkade tjänsten

Returnerar anslutnings sträng eller autentiseringsuppgifter för den länkade tjänsten. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getConnectionStringOrCreds('linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getConnectionStringOrCreds("linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetConnectionStringOrCreds("linked service name")
```

::: zone-end


### <a name="get-secret-using-workspace-identity"></a>Hämta hemlighet med arbets ytans identitet

Returnerar Azure Key Vault hemlighet för ett angivet Azure Key Vault namn, hemligt namn och länkat tjänst namn med hjälp av arbets ytans identitet. Se till att du konfigurerar åtkomst till [Azure Key Vault](#configure-access-to-azure-key-vault) på lämpligt sätt.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name","linked service name")
```

::: zone-end


### <a name="get-secret-using-user-credentials"></a>Hämta hemlighet med användarautentiseringsuppgifter

Returnerar Azure Key Vault hemlighet för ett angivet Azure Key Vault namn, ett hemligt namn och ett länkat tjänst namn som använder användarautentiseringsuppgifter. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name")
```

::: zone-end

<!-- ### Put secret using workspace identity

Puts Azure Key Vault secret for a given Azure Key Vault name, secret name, and linked service name using workspace identity. Make sure you configure the access to [Azure Key Vault](#configure-access-to-azure-key-vault) appropriately. -->

:::zone pivot = "programming-language-python"

### <a name="put-secret-using-workspace-identity"></a>Lägg till hemlighet med arbets ytans identitet

Placerar Azure Key Vault hemlighet för ett angivet Azure Key Vault namn, hemligt namn och länkat tjänst namn med hjälp av arbets ytans identitet. Se till att du konfigurerar åtkomsten till [Azure Key Vault](#configure-access-to-azure-key-vault) på lämpligt sätt.

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-workspace-identity"></a>Lägg till hemlighet med arbets ytans identitet

Placerar Azure Key Vault hemlighet för ett angivet Azure Key Vault namn, hemligt namn och länkat tjänst namn med hjälp av arbets ytans identitet. Se till att du konfigurerar åtkomsten till [Azure Key Vault](#configure-access-to-azure-key-vault) på lämpligt sätt.

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value","linked service name")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


<!-- ### Put secret using user credentials

Puts Azure Key Vault secret for a given Azure Key Vault name, secret name, and linked service name using user credentials.  -->

:::zone pivot = "programming-language-python"

### <a name="put-secret-using-user-credentials"></a>Lägg till hemlighet med användarautentiseringsuppgifter

Placerar Azure Key Vault hemlighet för ett angivet Azure Key Vault namn, hemligt namn och länkat tjänst namn med hjälp av användarautentiseringsuppgifter. 

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-user-credentials"></a>Lägg till hemlighet med användarautentiseringsuppgifter

Placerar Azure Key Vault hemlighet för ett angivet Azure Key Vault namn, hemligt namn och länkat tjänst namn med hjälp av användarautentiseringsuppgifter. 

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


## <a name="environment-utilities"></a>Miljö verktyg 

Kör följande kommandon för att få en översikt över tillgängliga metoder:

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.Help()
```

::: zone-end

Få resultat:
```
GetUserName(): returns user name
GetUserId(): returns unique user id
GetJobId(): returns job id
GetWorkspaceName(): returns workspace name
GetPoolName(): returns Spark pool name
GetClusterId(): returns cluster id
```

### <a name="get-user-name"></a>Hämta användar namn

Returnerar aktuellt användar namn.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserName()
```

::: zone-end

### <a name="get-user-id"></a>Hämta användar-ID

Returnerar aktuellt användar-ID.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserId()
```

::: zone-end

### <a name="get-job-id"></a>Hämta jobb-ID

Returnerar jobb-ID.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getJobId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getJobId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetJobId()
```

::: zone-end

### <a name="get-workspace-name"></a>Hämta arbets ytans namn

Returnerar namn på arbets yta.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getWorkspaceName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getWorkspaceName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetWorkspaceName()
```

::: zone-end

### <a name="get-pool-name"></a>Hämta poolnamn

Returnerar namn på Spark-pool.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getPoolName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getPoolName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetPoolName()
```

::: zone-end

### <a name="get-cluster-id"></a>Hämta kluster-ID

Returnerar aktuellt kluster-ID.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getClusterId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getClusterId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetClusterId()
```

::: zone-end

## <a name="next-steps"></a>Nästa steg

- [Kolla Synapse-exempel Notebooks](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Snabb start: skapa en Apache Spark pool i Azure Synapse Analytics med hjälp av webb verktyg](../quickstart-apache-spark-notebook.md)
- [Vad är Apache Spark i Azure Synapse Analytics](apache-spark-overview.md)
- [Azure Synapse Analytics](../index.yml)
