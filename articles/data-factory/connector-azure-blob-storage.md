---
title: Kopiera och transformera data i Azure Blob Storage
description: Lär dig hur du kopierar data till och från Blob Storage och transformerar data i Blob Storage med hjälp av Data Factory.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/18/2020
ms.openlocfilehash: e9561c0b54d256d5f24dc02c6f46d84821b9708c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548454"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Kopiera och transformera data i Azure Blob Storage med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-azure-blob-connector.md)
> * [Aktuell version](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från och till Azure Blob Storage. Det beskriver också hur du använder data flödes aktiviteten för att transformera data i Azure Blob Storage. Läs den [inledande artikeln](introduction.md)om du vill veta mer om Azure Data Factory.

>[!TIP]
>Information om ett migreringsjobb för ett data Lake eller ett informations lager finns i [använda Azure Data Factory för att migrera data från data Lake eller data lager till Azure](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Blob Storage-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)

För kopierings aktiviteten stöder denna Blob Storage Connector:

- Kopiera blobbar till och från allmänna Azure Storage-konton och varmt/cool Blob Storage. 
- Kopiera blobbar med hjälp av en konto nyckel, en signatur för delad åtkomst för tjänsten (SAS), ett huvud namn för tjänsten eller hanterade identiteter för Azure-resurs-autentiseringar.
- Kopiera blobbar från block-, tilläggs-eller sid-blobar och kopiera data till endast block-blobar.
- Kopiera blobbar som är eller parsa eller generera blobbar med [fil format och komprimerings-codec som stöds](supported-file-formats-and-compression-codecs.md).
- [Sparar metadata för filen under kopieringen](#preserving-metadata-during-copy).

>[!IMPORTANT]
>Om du aktiverar alternativet **Tillåt betrodda Microsoft-tjänster för att komma åt det här lagrings kontot** i Azure Storage brand Väggs inställningar och vill använda Azure integration runtime för att ansluta till Blob Storage måste du använda [hanterad identitets autentisering](#managed-identity).

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Blob Storage.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Denna Blob Storage-anslutning har stöd för följande typer av autentisering. Se motsvarande avsnitt för mer information.

- [Autentisering av konto nyckel](#account-key-authentication)
- [Autentisering av signatur för delad åtkomst](#shared-access-signature-authentication)
- [Autentisering av tjänstens huvud namn](#service-principal-authentication)
- [Hanterade identiteter för Azure-resurs-autentisering](#managed-identity)

>[!NOTE]
>När du använder PolyBase för att läsa in data i Azure SQL Data Warehouse, om din källa eller mellanlagring av blob-lagring har kon figurer ATS med en Azure Virtual Network-slutpunkt, måste du använda hanterad identitetsautentisering som krävs av PolyBase. Du måste också använda den egen värdbaserade integrerings körningen med version 3,18 eller senare. Mer konfigurations krav finns i avsnittet [hanterad identitets autentisering](#managed-identity) .

>[!NOTE]
>Azure HDInsight och Azure Machine Learning-aktiviteter stöder endast autentisering som använder Azure Blob Storage-konto nycklar.

### <a name="account-key-authentication"></a>Autentisering av konto nyckel

Data Factory stöder följande egenskaper för autentisering av lagrings konto nyckel:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typ** egenskapen måste anges till **AzureBlobStorage** (föreslagen) eller **AzureStorage** (se följande anmärkningar). |Yes |
| Begär | Ange den information som behövs för att ansluta till lagringen för egenskapen **ConnectionString** . <br/> Du kan också ställa in konto nyckeln i Azure Key Vault och hämta `accountKey` konfigurationen från anslutnings strängen. Mer information finns i följande exempel och [Store-autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikeln. |Yes |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure integration runtime eller den lokala integrerings körningen (om ditt data lager finns i ett privat nätverk). Om den här egenskapen inte anges använder tjänsten standard Azure integration Runtime. |No |

>[!NOTE]
>Det finns inte stöd för en sekundär Blob Service-slutpunkt när du använder autentisering med konto nycklar. Du kan använda andra typer av autentisering.

>[!NOTE]
>Om du använder den länkade tjänsten "AzureStorage"-typ, stöds den fortfarande som den är. Men vi rekommenderar att du använder den nya länkade tjänst typen "AzureBlobStorage" som går framåt.

**Exempel:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: lagra konto nyckeln i Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>Autentisering av signatur för delad åtkomst

En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt lagrings konto. Du kan använda en signatur för delad åtkomst för att ge en klient begränsad behörighet till objekt i ditt lagrings konto under en angiven tid. 

Du behöver inte dela dina konto åtkomst nycklar. Signaturen för delad åtkomst är en URI som omfattar den information som krävs för autentiserad åtkomst till en lagrings resurs i dess frågeparametrar. För att få åtkomst till lagrings resurser med signaturen för delad åtkomst måste klienten bara skicka in signaturen för delad åtkomst till lämplig konstruktor eller metod. 

Mer information om signaturer för delad åtkomst finns i signaturer för delad åtkomst [: förstå signatur modellen för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- Data Factory stöder nu både signaturer för *delad åtkomst för tjänsten* och *kontots delade åtkomst-signaturer*. Mer information om signaturer för delad åtkomst finns i [bevilja begränsad åtkomst till Azure Storage resurser med hjälp av signaturer för delad åtkomst](../storage/common/storage-sas-overview.md).
>- I senare data uppsättnings konfigurationer är mappsökvägen den absoluta sökvägen som börjar från behållar nivån. Du måste konfigurera en anpassad med sökvägen i SAS-URI: n.

Data Factory stöder följande egenskaper för att använda autentisering med delad åtkomst-signatur:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typ** egenskapen måste anges till **AzureBlobStorage** (föreslagen) eller **AzureStorage** (se följande anmärkning). |Yes |
| sasUri | Ange signatur-URI för delad åtkomst till lagrings resurserna, till exempel BLOB eller container. <br/>Markera det här fältet som **SecureString** för att lagra det på ett säkert sätt i Data Factory. Du kan också ställa in SAS-token i Azure Key Vault att använda automatisk rotation och ta bort token-delen. Mer information finns i följande exempel och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure integration runtime eller den lokala integrerings körningen (om ditt data lager finns i ett privat nätverk). Om den här egenskapen inte anges använder tjänsten standard Azure integration Runtime. |No |

>[!NOTE]
>Om du använder den länkade tjänsten "AzureStorage"-typ, stöds den fortfarande som den är. Men vi rekommenderar att du använder den nya länkade tjänst typen "AzureBlobStorage" som går framåt.

**Exempel:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<accountname>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: lagra konto nyckeln i Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<accountname>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

När du skapar en signatur-URI för delad åtkomst bör du tänka på följande:

- Ange lämpliga Läs-/skriv behörigheter för objekt baserat på hur den länkade tjänsten (läsa, skriva, läsa/skriva) används i din data fabrik.
- Ange **förfallo tid** på lämpligt sätt. Se till att åtkomsten till lagrings objekt upphör att gälla inom den aktiva perioden för pipelinen.
- URI: n måste skapas i rätt behållare eller BLOB baserat på behovet. En signatur-URI för delad åtkomst till en BLOB ger Data Factory åtkomst till just denna blob. En signatur-URI för delad åtkomst till en Blob Storage-behållare gör att Data Factory kan iterera igenom blobbar i den behållaren. Kom ihåg att uppdatera den länkade tjänsten med den nya URI: n för att ge åtkomst till fler eller färre objekt senare, eller för att uppdatera signatur-URI för delad åtkomst.

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Allmän information om Azure Storage tjänstens huvud namns autentisering finns i [autentisera åtkomst till Azure Storage med Azure Active Directory](../storage/common/storage-auth-aad.md).

Följ dessa steg om du vill använda autentisering av tjänstens huvud namn:

1. Registrera en program enhet i Azure Active Directory (Azure AD) genom [att följa registrera ditt program med en Azure AD-klient](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anteckna de här värdena, som du använder för att definiera den länkade tjänsten:

    - Program-ID
    - Program nyckel
    - Klientorganisations-ID

2. Bevilja tjänstens huvud namn rätt behörighet i Azure Blob Storage. Mer information om rollerna finns i [Hantera åtkomst rättigheter för att Azure Storage data med RBAC](../storage/common/storage-auth-aad-rbac.md).

    - **Som källa**, beviljar du minst rollen **Storage BLOB data Reader** i **åtkomst kontroll (IAM)**.
    - **Som mottagare**, i **åtkomst kontroll (IAM)**, beviljar du minst rollen **Storage BLOB data Contributor** .

Dessa egenskaper stöds för en länkad Azure Blob Storage-tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** måste anges till **AzureBlobStorage**. |Yes |
| serviceEndpoint | Ange Azure Blob Storage-tjänstens slut punkt med mönstret för `https://<accountName>.blob.core.windows.net/` . |Yes |
| servicePrincipalId | Ange programmets klient-ID. | Yes |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som **SecureString** för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| tenant | Ange den klient information (domän namn eller klient-ID) som programmet finns under. Hämta det genom att hovra över det övre högra hörnet av Azure Portal. | Yes |
| azureCloudType | För tjänstens huvud namn anger du den typ av moln miljö i Azure som Azure Active Directory programmet har registrerats för. <br/> Tillåtna värden är **AzurePublic**, **AzureChina**, **azureusgovernment eller**och **AzureGermany**. Som standard används data fabrikens moln miljö. | No |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure integration runtime eller den lokala integrerings körningen (om ditt data lager finns i ett privat nätverk). Om den här egenskapen inte anges använder tjänsten standard Azure integration Runtime. |No |

>[!NOTE]
>Autentisering av tjänstens huvud namn stöds endast av den länkade tjänsten "AzureBlobStorage", inte den tidigare länkade tjänsten av typen "AzureStorage".

**Exempel:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resource-authentication"></a><a name="managed-identity"></a> Hanterade identiteter för Azure-resurs-autentisering

En data fabrik kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md)som representerar den aktuella data fabriken. Du kan använda den här hanterade identiteten direkt för Blob Storage-autentisering, vilket liknar att använda ditt eget tjänst huvud namn. Det gör att den här fabriken kan komma åt och kopiera data från eller till Blob Storage.

Allmän information om Azure Storage autentisering finns i [autentisera åtkomst till Azure Storage med hjälp av Azure Active Directory](../storage/common/storage-auth-aad.md). Följ dessa steg om du vill använda hanterade identiteter för Azure Resource Authentication:

1. [Hämta data Factory hanterad identitets information](data-factory-service-identity.md#retrieve-managed-identity) genom att kopiera värdet för det hanterade ID-objekt-ID: t som genererats tillsammans med din fabrik.

2. Bevilja behörigheten hanterad identitet i Azure Blob Storage. Mer information om rollerna finns i [Hantera åtkomst rättigheter för att Azure Storage data med RBAC](../storage/common/storage-auth-aad-rbac.md).

    - **Som källa**, beviljar du minst rollen **Storage BLOB data Reader** i **åtkomst kontroll (IAM)**.
    - **Som mottagare**, i **åtkomst kontroll (IAM)**, beviljar du minst rollen **Storage BLOB data Contributor** .

>[!IMPORTANT]
>Om du använder PolyBase för att läsa in data från Blob Storage (som en källa eller som mellanlagring) till SQL Data Warehouse, när du använder hanterad identitetsautentisering för Blob Storage, måste du följa steg 1 och 2 i [den här vägledningen](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). De här stegen registrerar servern med Azure AD och tilldelar rollen Storage BLOB data Contributor till servern. Data Factory hanterar resten. Om du har konfigurerat Blob Storage med en Azure Virtual Network-slutpunkt för att använda PolyBase för att läsa in data från den, måste du använda hanterad identitetsautentisering som krävs av PolyBase.

Dessa egenskaper stöds för en länkad Azure Blob Storage-tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** måste anges till **AzureBlobStorage**. |Yes |
| serviceEndpoint | Ange Azure Blob Storage-tjänstens slut punkt med mönstret för `https://<accountName>.blob.core.windows.net/` . |Yes |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure integration runtime eller den lokala integrerings körningen (om ditt data lager finns i ett privat nätverk). Om den här egenskapen inte anges använder tjänsten standard Azure integration Runtime. |No |

> [!NOTE]
> Hanterade identiteter för Azure-autentisering stöds endast av den länkade tjänsten "AzureBlobStorage", inte den tidigare länkade tjänsten av typen "AzureStorage".

**Exempel:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Azure Blob Storage under `location` Inställningar i en formaterad data uppsättning:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Egenskapen **Type** för platsen i data uppsättningen måste anges till **AzureBlobStorageLocation**. | Yes      |
| container  | BLOB-behållaren.                                          | Yes      |
| folderPath | Sökvägen till mappen under den angivna behållaren. Om du vill använda ett jokertecken för att filtrera mappen, hoppar du över den här inställningen och anger den i aktivitets källans inställningar. | No       |
| fileName   | Fil namnet under den angivna behållaren och sökvägen till mappen. Om du vill använda jokertecken för att filtrera filer, hoppar du över den här inställningen och anger det i aktivitets källans inställningar. | No       |

**Exempel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Blob Storage-källan och Sink.

### <a name="blob-storage-as-a-source-type"></a>Blob-lagring som käll typ

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Azure Blob Storage under `storeSettings` Inställningar i en format-baserad kopierings Källa:

| Egenskap                 | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | **Typ** egenskapen under `storeSettings` måste anges till **AzureBlobStorageReadSettings**. | Yes                                           |
| ***Leta upp de filer som ska kopieras:*** |  |  |
| ALTERNATIV 1: statisk sökväg<br> | Kopiera från den angivna behållaren eller mappen/fil Sök vägen som anges i data uppsättningen. Om du vill kopiera alla blobbar från en behållare eller mapp, anger du också `wildcardFileName` som `*` . |  |
| ALTERNATIV 2: BLOB-prefix<br>-prefix | Prefix för BLOB-namnet under den angivna behållaren som kon figurer ATS i en data uppsättning för att filtrera käll-blobar. Blobbar vars namn börjar med `container_in_dataset/this_prefix` väljs. Det använder filter på tjänst sidan för Blob Storage, vilket ger bättre prestanda än ett Wildcard-filter. | No                                                          |
| ALTERNATIV 3: jokertecken<br>- wildcardFolderPath | Mappsökvägen med jokertecken under den angivna behållaren som kon figurer ATS i en data uppsättning för att filtrera källmappen. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Används `^` för att kringgå om mappnamnet har jokertecken eller detta escape-tecken i. <br>Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | No                                            |
| ALTERNATIV 3: jokertecken<br>- wildcardFileName | Fil namnet med jokertecken under den angivna behållaren och mappens sökväg (eller mappsökväg för jokertecken) för att filtrera källfiler. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Används `^` för att kringgå om ditt mappnamn har ett jokertecken eller detta escape-tecken inuti. Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Yes |
| ALTERNATIV 4: en lista över filer<br>- fileListPath | Anger om du vill kopiera en angiven fil uppsättning. Peka på en textfil som innehåller en lista över filer som du vill kopiera, en fil per rad, som är den relativa sökvägen till den sökväg som kon figurer ATS i data uppsättningen.<br/>När du använder det här alternativet ska du inte ange ett fil namn i data uppsättningen. Se fler exempel i [fil List exempel](#file-list-examples). |No |
| ***Ytterligare inställningar:*** |  | |
| rekursiva | Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. Observera att när **rekursivt** har angetts till **True** och sinken är en filbaserad lagring, kopieras inte en tom mapp eller undermapp till mottagaren. <br>Tillåtna värden är **True** (standard) och **false**.<br>Den här egenskapen gäller inte när du konfigurerar `fileListPath` . |No |
| deleteFilesAfterCompletion | Anger om de binära filerna kommer att tas bort från käll arkivet efter att du har flyttat till mål lagret. Filen som ska tas bort är per fil, så när kopierings aktiviteten Miss lyckas visas några filer som redan har kopierats till målet och tagits bort från källan, medan andra fortfarande är kvar på käll arkivet. <br/>Den här egenskapen är endast giltig i ett binärt kopierings scenario där data källor är BLOB, ADLS Gen1, ADLS Gen2, S3, Google Cloud Storage, File, Azure File, SFTP eller FTP. Standardvärdet: false. |No |
| modifiedDatetimeStart    | Filerna filtreras baserat på attributet: senast ändrad. <br>Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd` . Tiden tillämpas på en UTC-tidszon i formatet "2018-12-01T05:00:00Z". <br> Egenskaperna kan vara **Null**, vilket innebär att inget attribut filter används för data uppsättningen.  När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är **Null**väljs de filer vars senast ändrade attribut är större än eller lika med värdet för DateTime.  När `modifiedDatetimeEnd` har ett datetime-värde `modifiedDatetimeStart` , men är **Null**, väljs filerna vars senast ändrade attribut är mindre än värdet för DateTime.<br/>Den här egenskapen gäller inte när du konfigurerar `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | No                                            |
| maxConcurrentConnections | Antalet samtidiga anslutningar till lagringen. Ange bara när du vill begränsa samtidiga anslutningar till data lagret. | No                                            |

> [!NOTE]
> För Parquet/avgränsat text format stöds inte typen av **BlobSource** för kopierings aktivitets källan som finns i nästa avsnitt som är för bakåtkompatibilitet. Vi rekommenderar att du använder den nya modellen tills gränssnittet för Data Factory redigering har växlat till att generera dessa nya typer.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="blob-storage-as-a-sink-type"></a>Blob-lagring som mottagar typ

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)] 

Följande egenskaper stöds för Azure Blob Storage under `storeSettings` Inställningar i en format-baserad kopierings mottagare:

| Egenskap                 | Beskrivning                                                  | Krävs |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | **Typ** egenskapen under `storeSettings` måste anges till **AzureBlobStorageWriteSettings**. | Yes      |
| copyBehavior             | Definierar kopierings beteendet när källan är filer från ett filbaserat data lager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: bevarar filens hierarki i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><b>-FlattenHierarchy</b>: alla filer från källmappen är på den första nivån i målmappen. Filerna har automatiskt genererade namn. <br/><b>-MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om filen eller BLOB-namnet anges, är det sammanslagna fil namnet det angivna namnet. Annars är det ett automatiskt genererat fil namn. | No       |
| blockSizeInMB | Ange block storleken, i megabyte, som används för att skriva data för att blockera blobbar. Läs mer [om block-blobar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Det tillåtna värdet är *mellan 4 MB och 100 MB*. <br/>Som standard bestämmer Data Factory automatiskt block storleken baserat på källans lagrings typ och data. För en binär kopia till Blob Storage är standard block storleken 100 MB så att den får plats i (högst) 4,95 TB data. Det kanske inte är optimalt om dina data inte är stora, särskilt när du använder den egen värdbaserade integrerings körningen med dåliga nätverks anslutningar som leder till åtgärds-timeout eller prestanda problem. Du kan uttryckligen ange en block storlek och se till att den `blockSizeInMB*50000` är tillräckligt stor för att lagra data. Annars går det inte att köra kopierings aktiviteten. | No |
| maxConcurrentConnections | Antalet samtidiga anslutningar till lagringen. Ange bara när du vill begränsa samtidiga anslutningar till data lagret. | No       |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobStorageWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exempel på mapp-och fil filter

I det här avsnittet beskrivs det resulterande beteendet hos mappsökvägen och fil namnet med filter för jokertecken.

| folderPath | fileName | rekursiva | Källans mappstruktur och filter resultat (filer i **fetstil** hämtas)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (tom, Använd standard) | falskt | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.jspå**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (tom, Använd standard) | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.jspå**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jspå**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | falskt | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Exempel på fil listor

I det här avsnittet beskrivs det resulterande beteendet för att använda en fil List Sök väg i kopierings aktivitets källan.

Antag att du har följande mappstruktur och vill kopiera filerna i fetstil:

| Exempel på käll struktur                                      | Innehåll i FileListToCopy.txt                             | Data Factory konfiguration                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| container<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadatatjänst<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **I data uppsättning:**<br>Fönster `container`<br>– Mappsökväg: `FolderA`<br><br>**I kopierings aktivitets Källa:**<br>– Sökväg till fil lista: `container/Metadata/FileListToCopy.txt` <br><br>Sökvägen till fil listan pekar på en textfil i samma data lager som innehåller en lista över filer som du vill kopiera, en fil per rad, med den relativa sökvägen till den sökväg som kon figurer ATS i data uppsättningen. |

### <a name="some-recursive-and-copybehavior-examples"></a>Några rekursiva och copyBehavior-exempel

I det här avsnittet beskrivs det resulterande beteendet för kopierings åtgärden för olika kombinationer av **rekursiva** och **copyBehavior** värden.

| rekursiva | copyBehavior | Källans mappstruktur | Resulterande mål |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målmappen, Mapp1, skapas med samma struktur som källan:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målmappen, Mapp1, skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File5 |
| true |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målmappen, Mapp1, skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + Fil2 + File3 + File4 + File5-innehåll sammanfogas till en fil med ett automatiskt genererat fil namn. |
| falskt |preserveHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målmappen, Mapp1, skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |
| falskt |flattenHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målmappen, Mapp1, skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för Fil2<br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |
| falskt |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Målmappen, Mapp1, skapas med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + Fil2-innehåll sammanfogas till en fil med ett automatiskt genererat fil namn. automatiskt genererat namn för fil1<br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |

## <a name="preserving-metadata-during-copy"></a>Bevara metadata under kopiering

När du kopierar filer från Amazon S3, Azure Blob Storage eller Azure Data Lake Storage Gen2 till Azure Data Lake Storage Gen2 eller Azure Blob Storage, kan du välja att bevara filens metadata tillsammans med data. Läs mer om att [bevara metadata](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

När du transformerar data i mappnings data flöden kan du läsa och skriva filer från Azure Blob Storage i följande format:
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Avgränsad text](format-delimited-text.md#mapping-data-flow-properties)
* [Delta](format-delta.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

Formatera vissa inställningar finns i dokumentationen för det formatet. Mer information finns i [käll omvandling i konvertering av data flöde](data-flow-source.md) och [omvandling av mottagare i data flödet](data-flow-sink.md).

### <a name="source-transformation"></a>Käll omvandling

I käll omvandling kan du läsa från en behållare, mapp eller en enskild fil i Azure Blob Storage. Använd fliken **käll alternativ** för att hantera hur filerna läses. 

![Käll alternativ](media/data-flow/sourceOptions1.png "Käll alternativ")

**Jokertecken sökvägar:** Om du använder ett mönster med jokertecken instrueras Data Factory att loopa igenom varje matchande mapp och fil i en enda käll omvandling. Detta är ett effektivt sätt att bearbeta flera filer i ett enda flöde. Lägg till flera matchnings mönster för jokertecken med plus tecknet som visas när du hovrar över ditt befintliga mönster för jokertecken.

Från din käll behållare väljer du en serie filer som matchar ett mönster. Det går bara att ange en behållare i data uppsättningen. Sökvägen till jokertecken måste därför även innehålla sökvägen till din mapp från rotmappen.

Jokertecken exempel:

* ```*``` Representerar en uppsättning tecken.
* ```**``` Representerar rekursiv katalog kapsling.
* ```?``` Ersätter ett Character.
* ```[]``` Matchar ett eller flera tecken inom hakparenteserna.

* ```/data/sales/**/*.csv``` Hämtar alla CSV-filer under/data/Sales.
* ```/data/sales/20??/**/``` Hämtar alla filer i 20-talet.
* ```/data/sales/*/*/*.csv``` Hämtar. csv-filer två nivåer under/data/Sales.
* ```/data/sales/2004/*/12/[XY]1?.csv``` Hämtar alla CSV-filer i december 2004 med X eller Y som föregås av ett tvåsiffrigt tal.

**Partitionens rot Sök väg:** Om du har partitionerade mappar i din fil källa med ett ```key=value``` format (till exempel `year=2019` ), kan du tilldela den översta nivån i det partitionens mappträd till ett kolumn namn i data flödets data ström.

Ange först ett jokertecken för att inkludera alla sökvägar som är de partitionerade mapparna plus de löv-filer som du vill läsa.

![Inställningar för partitionens källfil](media/data-flow/partfile2.png "Fil inställning för partition")

Använd inställningen för **partitionens rot Sök väg** för att definiera vad mappstrukturen är. När du visar innehållet i dina data via en data förhands granskning ser du att Data Factory lägger till de matchade partitionerna som finns i var och en av dina mappnivå.

![Partitionens rot Sök väg](media/data-flow/partfile1.png "För hands version av partitionens rot Sök väg")

**Lista över filer:** Detta är en fil uppsättning. Skapa en textfil som innehåller en lista över relativa Sök vägs filer som ska bearbetas. Peka på den här text filen.

**Kolumn att lagra fil namn på:** Lagra namnet på käll filen i en kolumn i dina data. Ange ett nytt kolumn namn här för att lagra fil namn strängen.

**Efter slut för ande:** Välj att inte göra något med käll filen när data flödet körts, ta bort käll filen eller flytta käll filen. Sök vägarna för flytten är relativa.

Om du vill flytta källfilerna till en annan plats efter bearbetning väljer du först flytta för fil åtgärd. Ange sedan "från"-katalogen. Om du inte använder jokertecken för sökvägen, kommer inställningen från att vara samma mapp som källmappen.

Om du har en käll Sök väg med jokertecken kommer din syntax att se ut så här:

```/data/sales/20??/**/*.csv```

Du kan ange "från" som:

```/data/sales```

Och du kan ange "till" som:

```/backup/priorSales```

I det här fallet flyttas alla filer som har ursprung under/data/Sales till/backup/priorSales.

> [!NOTE]
> Fil åtgärder körs bara när du startar data flödet från en pipeline-körning (en pipeline för pipeline-fel eller körning) som använder aktiviteten kör data flöde i en pipeline. Fil åtgärder körs *inte* i fel söknings läge för data flöde.

**Filtrera efter senast ändrad:** Du kan filtrera vilka filer du bearbetar genom att ange ett datum intervall när de senast ändrades.  Alla datum/tid-värden anges i UTC-tid. 

### <a name="sink-properties"></a>Egenskaper för mottagare

I omvandling av mottagare kan du skriva till antingen en behållare eller en mapp i Azure Blob Storage. Använd fliken **Inställningar** för att hantera hur filerna skrivs.

![Mottagar alternativ](media/data-flow/file-sink-settings.png "mottagar alternativ")

**Rensa mappen:** Anger om målmappen ska rensas innan data skrivs.

**Fil namns alternativ:** Anger hur målfiler namnges i målmappen. Fil namns alternativen är:
   * **Standard**: Tillåt Spark att namnge filer baserat på del standarder.
   * **Mönster**: Ange ett mönster som räknar upp dina utdatafiler per partition. Till exempel kommer **lån [n]. csv** att skapa loans1.csv, loans2.csv och så vidare.
   * **Per partition**: Ange ett fil namn per partition.
   * **Som data i kolumnen**: Ange utdatafilen till värdet för en kolumn. Sökvägen är relativ i förhållande till data uppsättnings behållaren, inte målmappen. Om du har en mappsökväg i din data uppsättning kommer den att åsidosättas.
   * **Utdata till en enda fil**: kombinera de partitionerade utdatafilerna till en enda namngiven fil. Sökvägen är relativ i förhållande till dataset-mappen. Tänk på att sammanslagnings åtgärden kan sluta att fungera baserat på Node-storlek. Vi rekommenderar inte det här alternativet för stora data uppsättningar.

**Offert alla:** Anger om alla värden ska anges inom citat tecken.

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Egenskaper för GetMetadata-aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [getMetaData-aktiviteten](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Ta bort aktivitets egenskaper

Om du vill veta mer om egenskaperna kontrollerar du [ta bort aktivitet](delete-activity.md).

## <a name="legacy-models"></a>Äldre modeller

>[!NOTE]
>Följande modeller stöds fortfarande som är för bakåtkompatibilitet. Vi rekommenderar att du använder den nya modellen som beskrivs ovan. Redigerings gränssnittet för Data Factory har växlat till att generera den nya modellen.

### <a name="legacy-dataset-model"></a>Äldre data uppsättnings modell

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens **typ** -egenskap måste anges till **AzureBlob**. |Yes |
| folderPath | Sökväg till behållaren och mappen i Blob Storage. <br/><br/>Ett Wildcard-filter stöds för sökvägen, exklusive container namn. Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Används `^` för att kringgå om ditt mappnamn har ett jokertecken eller detta escape-tecken inuti. <br/><br/>Ett exempel är: myblobcontainer/myblobfolder/. Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). |Ja för kopierings-eller söknings aktiviteten, nej för GetMetadata-aktiviteten |
| fileName | Namn eller Wildcard-filter för Blobbarna under det angivna **folderPath** -värdet. Om du inte anger ett värde för den här egenskapen pekar data uppsättningen på alla blobbar i mappen. <br/><br/>För filtret tillåts tillåtna jokertecken: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken).<br/>– Exempel 1: `"fileName": "*.csv"`<br/>– Exempel 2: `"fileName": "???20180427.txt"`<br/>Används `^` för att kringgå om fil namnet har ett jokertecken eller detta escape-tecken i.<br/><br/>När **fil namnet** inte har angetts för en data uppsättning för utdata och **preserveHierarchy** inte har angetts i aktivitets mottagaren genererar kopierings aktiviteten automatiskt BLOB-namnet med följande mönster: "*data. [ aktivitetens körnings-ID GUID]. [GUID om FlattenHierarchy]. [format om det är konfigurerat]. [komprimering om konfigurerad]*". Till exempel: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. gz". <br/><br/>Om du kopierar från en tabell källa med ett tabell namn i stället för en fråga, är namn mönstret "*[tabell namn]. [ format]. [komprimering om konfigurerad]*". Till exempel: "MyTable.csv". |No |
| modifiedDatetimeStart | Filerna filtreras baserat på attributet: senast ändrad. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd` . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Tänk på att om du aktiverar den här inställningen kommer den övergripande prestandan för data förflyttning att påverkas när du vill filtrera enorma mängder filer. <br/><br/> Egenskaperna kan vara **Null**, vilket innebär att inget attribut filter används för data uppsättningen.  När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är **Null**väljs de filer vars senast ändrade attribut är större än eller lika med värdet för DateTime.  När `modifiedDatetimeEnd` har ett datetime-värde `modifiedDatetimeStart` , men är **Null**, väljs filerna vars senast ändrade attribut är mindre än värdet för DateTime.| No |
| modifiedDatetimeEnd | Filerna filtreras baserat på attributet: senast ändrad. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd` . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Tänk på att om du aktiverar den här inställningen kommer den övergripande prestandan för data förflyttning att påverkas när du vill filtrera enorma mängder filer. <br/><br/> Egenskaperna kan vara **Null**, vilket innebär att inget attribut filter används för data uppsättningen.  När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är **Null**väljs de filer vars senast ändrade attribut är större än eller lika med värdet för DateTime.  När `modifiedDatetimeEnd` har ett datetime-värde `modifiedDatetimeStart` , men är **Null**, väljs filerna vars senast ändrade attribut är mindre än värdet för DateTime.| No |
| format | Om du vill kopiera filer som är mellan filbaserade butiker (binär kopia) hoppar du över avsnittet format i både indata och utdata-datauppsättnings definitionerna.<br/><br/>Om du vill parsa eller generera filer med ett särskilt format stöds följande fil format **typer: text**format, **JsonFormat**, **AvroFormat**, **OrcFormat**och **ParquetFormat**. Ange egenskapen **Type** under **format** till något av dessa värden. Mer information finns i avsnitten [text format](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)och [Parquet format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nej (endast för binär kopierings scenario) |
| komprimering | Ange typ och nivå för komprimeringen för data. Mer information finns i [fil format och komprimerings-codecar som stöds](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Typer som stöds är **gzip**, **DEFLATE**, **BZip2**och **ZipDeflate**.<br/>De nivåer som stöds är **optimala** och **snabbaste**. |No |

>[!TIP]
>Om du vill kopiera alla blobbar under en mapp anger du endast **folderPath** .<br>Om du vill kopiera en enda BLOB med ett visst namn anger du **folderPath** som del av **och fil** namnet för fil namnet.<br>Om du vill kopiera en delmängd av blobbar under en mapp anger du **folderPath** för mappen del och **filename** med ett Wildcard-filter. 

**Exempel:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-source-model-for-the-copy-activity"></a>Äldre käll modell för kopierings aktiviteten

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typ** egenskapen för kopierings aktivitets källan måste anges till **BlobSource**. |Yes |
| rekursiva | Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. Observera att när **rekursivt** har angetts till **True** och sinken är en filbaserad lagring, kopieras inte en tom mapp eller undermapp till mottagaren.<br/>Tillåtna värden är **True** (standard) och **false**. | No |
| maxConcurrentConnections | Antalet samtidiga anslutningar till lagringen. Ange bara när du vill begränsa samtidiga anslutningar till data lagret. | No |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-sink-model-for-the-copy-activity"></a>Äldre mottagar modell för kopierings aktiviteten

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** för kopierings aktivitetens Sink måste anges till **BlobSink**. |Yes |
| copyBehavior | Definierar kopierings beteendet när källan är filer från ett filbaserat data lager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: bevarar filens hierarki i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><b>-FlattenHierarchy</b>: alla filer från källmappen är på den första nivån i målmappen. Filerna har automatiskt genererade namn. <br/><b>-MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om filen eller BLOB-namnet anges, är det sammanslagna fil namnet det angivna namnet. Annars är det ett automatiskt genererat fil namn. | No |
| maxConcurrentConnections | Antalet samtidiga anslutningar till lagringen. Ange bara när du vill begränsa samtidiga anslutningar till data lagret. | No |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Nästa steg

En lista över data lager som kopierings aktiviteten i Data Factory stöder som källor och mottagare finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
