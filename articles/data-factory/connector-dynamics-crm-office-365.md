---
title: Kopiera data i Dynamics (Common Data Service)
description: Lär dig hur du kopierar data från Microsoft Dynamics CRM eller Microsoft Dynamics 365 (Common Data Service) till mottagar data lager eller från käll data lager som stöds till Dynamics CRM eller Dynamics 365 genom att använda en kopierings aktivitet i en Data Factory-pipeline.
ms.service: data-factory
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
ms.custom: seo-lt-2019
ms.date: 02/02/2021
ms.openlocfilehash: d238a232d719c75244e6f9b825272957d2a4a4bc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381009"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Kopiera data från och till Dynamics 365 (Common Data Service) eller Dynamics CRM genom att använda Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder en kopierings aktivitet i Azure Data Factory för att kopiera data från och till Microsoft Dynamics 365 och Microsoft Dynamics CRM. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en allmän översikt över en kopierings aktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här anslutningen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-och mottagar mat ris som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Dynamics 365 (Common Data Service) eller Dynamics CRM till alla mottagar data lager som stöds. Du kan också kopiera data från alla käll data lager som stöds till Dynamics 365 (Common Data Service) eller Dynamics CRM. En lista över data lager som en kopierings aktivitet stöder som källor och mottagare finns i tabellen [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Den här Dynamics Connector stöder Dynamics version 7 till 9 för både online och lokalt. Mer specifikt:

- Version 7 mappar till Dynamics CRM 2015.
- Version 8 mappar till Dynamics CRM 2016 och den tidiga versionen av Dynamics 365.
- Version 9 mappar till den senare versionen av Dynamics 365.

Se följande tabell över typer av autentisering och konfigurationer som stöds för Dynamics-versioner och-produkter.

| Dynamics-versioner | Autentiseringstyper | Länkade tjänst exempel |
|:--- |:--- |:--- |
| Common Data Service <br/><br/> Dynamics 365 online <br/><br/> Dynamics CRM Online | Azure Active Directory (Azure AD) tjänstens huvud namn <br/><br/> Office 365 | [Dynamics Online och Azure AD service – huvud konto eller Office 365-autentisering](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 lokalt med distribution mot Internet (IFD) <br/><br/> Dynamics CRM 2016 lokalt med IFD <br/><br/> Dynamics CRM 2015 lokalt med IFD | IFD | [Dynamics lokalt med IFD och IFD-autentisering](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

För Dynamics 365 är det specifikt att följande program typer stöds:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Den här anslutningen har inte stöd för andra program typer som finans, Operations och personal.

>[!TIP]
>Om du vill kopiera data från ekonomi och åtgärder i Dynamics 365 kan du använda [Dynamics AX-anslutningen](connector-dynamics-ax.md).

Den här Dynamics Connector bygger på [Dynamics XRM-verktyg](/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här anslutningen med Azure AD service – huvudsaklig autentisering måste du konfigurera server-till-Server-autentisering (S2S) i Common Data Service eller Dynamics. Se [den här artikeln](/powerapps/developer/common-data-service/build-web-applications-server-server-s2s-authentication) för detaljerade anvisningar.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Dynamics.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade Dynamics-tjänsten.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 och Dynamics CRM Online

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen måste anges till "Dynamics", "DynamicsCrm" eller "CommonDataServiceForApps". | Ja |
| deploymentType | Dynamics-instansens distributions typ. Värdet måste vara "online" för Dynamics Online. | Ja |
| serviceUri | Tjänst-URL: en för din Dynamics-instans, samma som du har åtkomst till från webbläsaren. Ett exempel är "https:// \<organization-name> . CRM [x]. Dynamics. com". | Ja |
| authenticationType | Autentiseringstypen för att ansluta till en Dynamics-Server. Giltiga värden är "AADServicePrincipal" och "Office365". | Ja |
| servicePrincipalId | Klient-ID för Azure AD-programmet. | Ja när autentiseringen är "AADServicePrincipal" |
| servicePrincipalCredentialType | Den typ av autentiseringsuppgift som ska användas för autentisering av tjänstens huvud namn. Giltiga värden är "ServicePrincipalKey" och "ServicePrincipalCert". | Ja när autentiseringen är "AADServicePrincipal" |
| servicePrincipalCredential | Tjänstens huvud behörighet. <br/><br/>När du använder "ServicePrincipalKey" som autentiseringstyp `servicePrincipalCredential` kan vara en sträng som Azure Data Factory krypteras vid en länkad tjänst distribution. Eller så kan det vara en referens till en hemlighet i Azure Key Vault. <br/><br/>När du använder "ServicePrincipalCert" som autentiseringsuppgift `servicePrincipalCredential` måste vara en referens till ett certifikat i Azure Key Vault. | Ja när autentiseringen är "AADServicePrincipal" |
| användarnamn | Användar namnet för att ansluta till Dynamics. | Ja när autentiseringen är "Office365" |
| password | Lösen ordet för det användar konto som du har angett som användar namn. Markera det här fältet med "SecureString" för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja när autentiseringen är "Office365" |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Om inget värde har angetts använder egenskapen standard Azure integration Runtime. | Inga |

>[!NOTE]
>Dynamics Connector använde tidigare egenskap för **företags namn** för att identifiera Dynamics CRM-eller Dynamics 365 online-instansen. Även om egenskapen fortfarande fungerar föreslår vi att du anger den nya **serviceUri** -egenskapen i stället för att få bättre prestanda för instans identifiering.

#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-key-authentication"></a>Exempel: Dynamics Online med Azure AD service – huvud konto och nyckel autentisering

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication"></a>Exempel: Dynamics Online med Azure AD service – huvud konto och certifikatautentisering

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

#### <a name="example-dynamics-online-using-office-365-authentication"></a>Exempel: Dynamics Online med Office 365-autentisering

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 och Dynamics CRM lokalt med IFD

Ytterligare egenskaper som jämförs med Dynamics Online är **värdnamn** och **port**.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen måste anges till "Dynamics", "DynamicsCrm" eller "CommonDataServiceForApps". | Ja. |
| deploymentType | Dynamics-instansens distributions typ. Värdet måste vara "OnPremisesWithIfd" för Dynamics lokalt med IFD.| Ja. |
| Värdnamn | Värd namnet för den lokala Dynamics-servern. | Ja. |
| port | Porten för den lokala Dynamics-servern. | Nej. Standardvärdet är 443. |
| Databasen | Organisations namnet för Dynamics-instansen. | Ja. |
| authenticationType | Autentiseringstypen för att ansluta till Dynamics-servern. Ange "IFD" för Dynamics lokalt med IFD. | Ja. |
| användarnamn | Användar namnet för att ansluta till Dynamics. | Ja. |
| password | Lösen ordet för det användar konto som du har angett för användar namnet. Du kan markera det här fältet med "SecureString" för att lagra det på ett säkert sätt i Data Factory. Eller så kan du lagra ett lösen ord i Key Vault och låta kopierings aktiviteten ta en stund när den kopierar data. Läs mer [om att lagra autentiseringsuppgifter i Key Vault](store-credentials-in-key-vault.md). | Ja. |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Om inget värde har angetts använder egenskapen standard Azure integration Runtime. | Inga |

#### <a name="example-dynamics-on-premises-with-ifd-using-ifd-authentication"></a>Exempel: Dynamics lokalt med IFD med IFD-autentisering

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Dynamics DataSet.

Följande egenskaper stöds för att kopiera data från och till Dynamics:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till "DynamicsEntity", "DynamicsCrmEntity" eller "CommonDataServiceForAppsEntity". |Ja |
| Entitetsnamnet | Det logiska namnet på den entitet som ska hämtas. | Nej för källa om aktivitets källan har angetts som "fråga" och Ja för mottagare |

#### <a name="example"></a>Exempel

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av typerna Dynamics-källa och mottagare.

### <a name="dynamics-as-a-source-type"></a>Dynamics som typ av källa

För att kopiera data från Dynamics stöder avsnittet för **kopierings aktivitet följande** egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till "DynamicsSource", "DynamicsCrmSource" eller "CommonDataServiceForAppsSource". | Ja |
| DocumentDB | FetchXML är ett patentskyddat frågespråk som används i Dynamics Online och lokalt. Se följande exempel. Mer information finns i [Bygg frågor med FetchXML](/previous-versions/dynamicscrm-2016/developers-guide/gg328332(v=crm.8)). | Nej om `entityName` i data uppsättningen anges |

>[!NOTE]
>Kolumnen PK kommer alltid att kopieras, även om den kolumn projektion som du konfigurerar i FetchXML-frågan inte innehåller den.

> [!IMPORTANT]
>- När du kopierar data från Dynamics är explicit kolumn mappning från Dynamics till Sink valfri. Men vi rekommenderar starkt mappningen för att säkerställa ett deterministiskt kopierings resultat.
>- När Data Factory importerar ett schema i redigerings gränssnittet härleds schemat. Det gör det genom att sampla de översta raderna från Dynamics query-resultatet för att initiera listan med käll kolumner. I så fall utelämnas kolumner utan värden i de översta raderna. Samma sak gäller för kopierings körningar om det inte finns någon explicit mappning. Du kan granska och lägga till fler kolumner i mappningen, som används under kopierings körningen.

#### <a name="example"></a>Exempel

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Exempel på FetchXML-fråga

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Dynamics som mottagar typ

För att kunna kopiera data till Dynamics stöder avsnittet Kopiera aktivitets **mottagare** följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för kopierings aktivitetens Sink måste anges till "DynamicsSink", "DynamicsCrmSink" eller "CommonDataServiceForAppsSink". | Ja. |
| writeBehavior | Åtgärdens Skriv funktion. Värdet måste vara "upsert". | Ja |
| alternateKeyName | Det alternativa nyckel namnet som definierats i entiteten för att göra en upsert. | Nej. |
| writeBatchSize | Rad antalet data som skrivs till Dynamics i varje batch. | Nej. Standardvärdet är 10. |
| ignoreNullValues | Om null-värden ska ignoreras från indata förutom nyckel fält under en Skriv åtgärd.<br/><br/>Giltiga värden är **True** och **false**:<ul><li>**True**: lämna kvar data i målobjektet oförändrade när du gör en upsert-eller uppdaterings åtgärd. Infoga ett definierat standardvärde när du infogar en åtgärd.</li><li>**False**: uppdatera data i målobjektet till ett null-värde när du gör en upsert-eller uppdaterings åtgärd. Infoga ett null-värde när du gör en infognings åtgärd.</li></ul> | Nej. Standardvärdet är **false**. |

>[!NOTE]
>Standardvärdet för både Sink- **writeBatchSize** och kopierings aktiviteten **[parallelCopies](copy-activity-performance-features.md#parallel-copy)** för Dynamics-Sink är 10. Därför skickas 100-poster samtidigt som standard till Dynamics.

För Dynamics 365 online finns det en gräns på [två samtidiga batch-anrop per organisation](/previous-versions/dynamicscrm-2016/developers-guide/jj863631(v=crm.8)#Run-time%20limitations). Om den här gränsen överskrids uppstår ett undantag för "Server upptagen" innan den första begäran körs. Håll **writeBatchSize** på 10 eller mindre för att undvika sådan begränsning av samtidiga anrop.

Den optimala kombinationen av **writeBatchSize** och **parallelCopies** är beroende av schemat för entiteten. Schema elementen innehåller antalet kolumner, rad storlek och antal plugin-program, arbets flöden och arbets flödes aktiviteter som är sammankopplade med dessa anrop. Standardvärdet för **writeBatchSize** (10) &times; **parallelCopies** (10) är rekommendationen enligt Dynamics-tjänsten. Det här värdet fungerar för de flesta Dynamics-entiteter, men det kanske inte ger bästa möjliga prestanda. Du kan justera prestanda genom att justera kombinationen i inställningarna för kopierings aktiviteten.

#### <a name="example"></a>Exempel

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Data typs mappning för Dynamics

När du kopierar data från Dynamics visas mappningar från data typer i Dynamics i följande tabell för att Data Factory interimistiska data typer. Information om hur en kopierings aktivitet mappar till ett käll schema och en datatyp mappar till en mottagare finns i [mappningar för schema och data typer](copy-activity-schema-and-type-mapping.md).

Konfigurera motsvarande Data Factory data typ i en data uppsättnings struktur som baseras på data typen för din käll-Dynamics med hjälp av följande mappnings tabell:

| Data typen Dynamics | Data Factory data typen Interim | Stöds som källa | Stöds som mottagare |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode. BigInt | Lång | ✓ | ✓ |
| AttributeTypeCode. Boolean | Boolesk | ✓ | ✓ |
| AttributeType. kund | GUID | ✓ | ✓ (Se [rikt linjer](#writing-data-to-a-lookup-field)) |
| AttributeType. DateTime | Datumtid | ✓ | ✓ |
| AttributeType. decimal | Decimal | ✓ | ✓ |
| AttributeType. Double | Double | ✓ | ✓ |
| AttributeType. EntityName | Sträng | ✓ | ✓ |
| AttributeType. Integer | Int32 | ✓ | ✓ |
| AttributeType. lookup | GUID | ✓ | ✓ (Se [rikt linjer](#writing-data-to-a-lookup-field)) |
| AttributeType.ManagedProperty | Boolesk | ✓ | |
| AttributeType. PM | Sträng | ✓ | ✓ |
| AttributeType. Money | Decimal | ✓ | ✓ |
| AttributeType. owner | GUID | ✓ | ✓ (Se [rikt linjer](#writing-data-to-a-lookup-field)) |
| AttributeType. listruta | Int32 | ✓ | ✓ |
| AttributeType. uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType. String | Sträng | ✓ | ✓ |
| AttributeType. State | Int32 | ✓ | ✓ |
| AttributeType. status | Int32 | ✓ | ✓ |

> [!NOTE]
> Det finns inte stöd för Dynamics data typerna **AttributeType. CalendarRules**, **AttributeType. MultiSelectPicklist** och **AttributeType. PartyList** .

## <a name="writing-data-to-a-lookup-field"></a>Skriva data till ett uppslags fält

Om du vill skriva data i ett uppslags fält med flera mål som kund och ägare följer du den här vägledningen och exempel:

1. Låt källan innehålla både fältvärdet och motsvarande målnamn.
   - Om alla poster mappar till samma målentitet, se till att något av följande villkor uppfylls:
      - Dina källdata har en kolumn som lagrar namnet på målentiteten.
      - Du har lagt till en ytterligare kolumn i kopierings aktivitets källan för att definiera målentiteten.
   - Om olika poster mappar till olika målentiteten, se till att dina källdata har en kolumn som lagrar motsvarande målentitet.

1. Mappa både värdet och entitetens referens kolumner från källan till Sink. Kolumnen entitets referens måste mappas till en virtuell kolumn med det särskilda namngivnings mönstret `{lookup_field_name}@EntityReference` . Kolumnen finns inte i Dynamics. Den används för att indikera att den här kolumnen är kolumnen metadata i uppslags fältet för det angivna multimålet.

Anta till exempel att källan har följande två kolumner:

- **CustomerField** -kolumn av typen **GUID**, som är värdet för primär nyckel för målentiteten i Dynamics.
- **Mål** kolumn av typen **sträng**, vilket är det logiska namnet på målentiteten.

Anta också att du vill kopiera sådana data till fältet för enhets fältet för mottagar Dynamics **CustomerField** av typen **kund**.

I kolumn mappningen för kopierings aktivitet mappar du de två kolumnerna enligt följande:

- **CustomerField** till **CustomerField**. Den här mappningen är den normala fält mappningen.
- **Rikta** till **CustomerField \@ EntityReference**. Kolumnen Sink är en virtuell kolumn som representerar enhets referensen. Ange sådana fält namn i en mappning, eftersom de inte visas genom att importera scheman.

![Dynamics lookup-kolumn mappning](./media/connector-dynamics-crm-office-365/connector-dynamics-lookup-field-column-mapping.png)

Om alla käll poster mappar till samma målentitet och dina källdata inte innehåller målentiteten, är det här en genväg: i kopierings aktivitets källan lägger du till ytterligare en kolumn. Namnge den nya kolumnen med hjälp av mönstret `{lookup_field_name}@EntityReference` , Ställ in värdet på målentiteten och fortsätt sedan med kolumn mappningen som vanligt. Om käll-och mottagar kolumn namnen är identiska kan du även hoppa över explicit kolumn mappning eftersom kopierings aktiviteten som standard mappar kolumner efter namn.

![Dynamics-sökning – fält lägga till en entitet-referens-kolumn](./media/connector-dynamics-crm-office-365/connector-dynamics-add-entity-reference-column.png)

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Mer information om egenskaperna finns i [Lookup Activity](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista med data som innehåller kopierings aktiviteten i Data Factory stöder som källor och mottagare finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).