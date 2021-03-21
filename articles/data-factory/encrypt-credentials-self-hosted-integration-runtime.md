---
title: Kryptera autentiseringsuppgifter i Azure Data Factory
description: Lär dig hur du krypterar och lagrar autentiseringsuppgifter för dina lokala data lager på en dator med integration runtime med egen värd.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 59d177aa3baf25f185201f1b6c4738cfce9c25a3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392654"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Kryptera autentiseringsuppgifter för lokala data lager i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Du kan kryptera och lagra autentiseringsuppgifter för dina lokala data lager (länkade tjänster med känslig information) på en dator med integration runtime med egen värd. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du skickar en JSON-definitions fil med autentiseringsuppgifter till <br/>Cmdlet: en [**New-AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) för att skapa en definitions fil för utdata-JSON med de krypterade autentiseringsuppgifterna. Använd sedan den uppdaterade JSON-definitionen för att skapa de länkade tjänsterna.

## <a name="author-sql-server-linked-service"></a>Redigera SQL Server länkad tjänst
Skapa en JSON-fil med namnet **SqlServerLinkedService.js** i en mapp med följande innehåll:  

Ersätt `<servername>` , `<databasename>` , `<username>` och `<password>` med värden för din SQL Server innan du sparar filen. Och Ersätt `<integration runtime name>` med namnet på din integration Runtime. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Kryptera autentiseringsuppgifter
Om du vill kryptera känsliga data från JSON-nyttolasten på en lokal lokal integration runtime kör du **New-AzDataFactoryV2LinkedServiceEncryptedCredential** och skickar den till JSON-nyttolasten. Denna cmdlet säkerställer att autentiseringsuppgifterna krypteras med DPAPI och lagras på den lokala integration runtime-noden lokalt. Nytto lasten för utdata som innehåller den krypterade referensen till autentiseringsuppgiften kan omdirigeras till en annan JSON-fil (i det här fallet encryptedLinkedService.jspå).

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Använd JSON med krypterade autentiseringsuppgifter
Använd nu den utgående JSON-filen från föregående kommando som innehåller krypterade autentiseringsuppgifter för att konfigurera **SqlServerLinkedService**.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Nästa steg
Information om säkerhets överväganden för data förflyttning finns i [säkerhets överväganden för data förflyttning](data-movement-security-considerations.md).

