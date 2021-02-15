---
title: Transformera data med U-SQL-skript
description: Lär dig hur du bearbetar eller transformerar data genom att köra U-SQL-skript på Azure Data Lake Analytics Compute service.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2018
ms.openlocfilehash: 94b584f0bea01754a4e955d1418a7a9a2e9aeace
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373920"
---
# <a name="process-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Bearbeta data genom att köra U-SQL-skript på Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-usql-activity.md)
> * [Aktuell version](transform-data-using-data-lake-analytics.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En pipeline i en Azure Data Factory bearbetar data i länkade lagrings tjänster med hjälp av länkade Compute-tjänster. Den innehåller en sekvens med aktiviteter där varje aktivitet utför en speciell bearbetnings åtgärd. I den här artikeln beskrivs **data Lake Analytics u-SQL-aktivitet** som kör ett **U-SQL** -skript i en **Azure Data Lake Analytics** Compute-länkad tjänst. 

Skapa ett Azure Data Lake Analytics konto innan du skapar en pipeline med en Data Lake Analytics U-SQL-aktivitet. Mer information om Azure Data Lake Analytics finns i [Kom igång med Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics länkad tjänst
Du skapar en **Azure Data Lake Analytics** länkad tjänst för att länka en Azure Data Lake Analytics Compute-tjänst till en Azure Data Factory. Data Lake Analytics U-SQL-aktiviteten i pipelinen refererar till den här länkade tjänsten. 

Följande tabell innehåller beskrivningar av de allmänna egenskaper som används i JSON-definitionen. 

| Egenskap                 | Beskrivning                              | Krävs                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **bastyp**                 | Egenskapen Type ska anges till: **AzureDataLakeAnalytics**. | Ja                                      |
| **Konto**          | Azure Data Lake Analytics konto namn.  | Ja                                      |
| **dataLakeAnalyticsUri** | Azure Data Lake Analytics-URI.           | Inga                                       |
| **subscriptionId**       | ID för Azure-prenumeration                    | Inga                                       |
| **resourceGroupName**    | Azure-resursgruppsnamn                | Inga                                       |

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn
Den Azure Data Lake Analytics länkade tjänsten kräver en tjänstens huvud namns autentisering för att ansluta till Azure Data Lake Analyticss tjänsten. Om du vill använda tjänstens huvud namns autentisering registrerar du en programentitet i Azure Active Directory (Azure AD) och ger den åtkomst till både Data Lake Analytics och Data Lake Store den använder. Detaljerade anvisningar finns i [tjänst-till-tjänst-autentisering](../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

* Program-ID
* Program nyckel 
* Klient-ID:t

Bevilja tjänstens huvud namn behörighet till din Azure Data Lake-Anatlyics med hjälp av [guiden Lägg till användare](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user).

Använd tjänstens huvud namns autentisering genom att ange följande egenskaper:

| Egenskap                | Beskrivning                              | Krävs |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Ange programmets klient-ID.     | Ja      |
| **servicePrincipalKey** | Ange programmets nyckel.           | Ja      |
| **tenant**              | Ange den klient information (domän namn eller klient-ID) som programmet finns under. Du kan hämta det genom att hovra musen i det övre högra hörnet av Azure Portal. | Ja      |

**Exempel: autentisering av tjänstens huvud namn**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "<azure data lake analytics URI>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

Mer information om den länkade tjänsten finns i [Compute-länkade tjänster](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-aktivitet
Följande JSON-kodfragment definierar en pipeline med en Data Lake Analytics U-SQL-aktivitet. Aktivitets definitionen har en referens till den Azure Data Lake Analytics länkade tjänsten som du skapade tidigare. Om du vill köra ett Data Lake Analytics U-SQL-skript Data Factory skickar det skript som du har angett till Data Lake Analytics och nödvändiga indata och utdata definieras i skriptet för Data Lake Analytics för hämtning och utdata. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "<linked service name of Azure Data Lake Analytics>",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "<linked service name of Azure Data Lake Store or Azure Storage which contains the U-SQL script>",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

I följande tabell beskrivs namn och beskrivningar av egenskaper som är unika för den här aktiviteten. 

| Egenskap            | Beskrivning                              | Obligatorisk |
| :------------------ | :--------------------------------------- | :------- |
| name                | Namn på aktiviteten i pipelinen     | Ja      |
| beskrivning         | Text som beskriver vad aktiviteten gör.  | Inga       |
| typ                | För Data Lake Analytics U-SQL-aktivitet är aktivitets typen  **DataLakeAnalyticsU-SQL**. | Ja      |
| linkedServiceName   | Länkad tjänst till Azure Data Lake Analytics. Mer information om den här länkade tjänsten finns i artikeln [Compute-länkade tjänster](compute-linked-services.md) .  |Ja       |
| scriptPath          | Sökväg till mapp som innehåller U-SQL-skriptet. Filens namn är Skift läges känsligt. | Ja      |
| scriptLinkedService | Länkad tjänst som länkar **Azure Data Lake Store** eller **Azure Storage** som innehåller skriptet till data fabriken | Ja      |
| degreeOfParallelism | Det maximala antalet noder som används samtidigt för att köra jobbet. | Inga       |
| prioritet            | Bestämmer vilka jobb som inte är i kö som ska väljas för att köras först. Ju lägre siffra, desto högre prioritet. | Inga       |
| parametrar          | Parametrar för att skicka till U-SQL-skriptet.    | Inga       |
| runtimeVersion      | Körnings version av U-SQL-motorn som ska användas. | Inga       |
| compilationMode     | <p>Compiler-läge för U-SQL. Måste vara något av följande värden: **semantik:** utför endast semantiska kontroller och nödvändiga Sanity-kontroller, **fullständig:** utför fullständig kompilering, inklusive syntaxkontroll, optimering, kodgenerering för kod osv., **enkel:** utföra fullständig kompilering med TargetType-inställning till en enda. Om du inte anger något värde för den här egenskapen, fastställer servern det optimala kompilerings läget. | Inga |

Se [SearchLogProcessing.txt](#sample-u-sql-script) för skript definitionen. 

## <a name="sample-u-sql-script"></a>Exempel på U-SQL-skript

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

I ovanstående skript exempel definieras indata och utdata i skriptet i parametrarna **\@ in** och **\@ out** . Värdena för **\@ in** -och **\@ out** -parametrarna i U-SQL-skriptet skickas dynamiskt av Data Factory med hjälp av avsnittet Parameters. 

Du kan ange andra egenskaper som degreeOfParallelism och prioritet även i din pipeline-definition för de jobb som körs på Azure Data Lake Analyticss tjänsten.

## <a name="dynamic-parameters"></a>Dynamiska parametrar
I exempel definitionen för pipeline kan in-och out-parametrarna tilldelas med hårdkodade värden. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Du kan använda dynamiska parametrar i stället. Exempel: 

```json
"parameters": {
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

I det här fallet hämtas indatafilerna fortfarande från mappen/datalake/input och utdatafilerna skapas i mappen/datalake/output. Fil namnen är dynamiska baserat på Start tiden för fönstret som skickas i när pipelinen utlöses.  

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omformar data på andra sätt: 

* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Aktivitet i gris](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Azure Machine Learning Studio (klassisk) batch execution Activity](transform-data-using-machine-learning.md)
* [Lagrad procedur aktivitet](transform-data-using-stored-procedure.md)