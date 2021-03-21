---
title: Förbered för format ändring till Azure Monitor resurs loggar
description: Azures resurs loggar har flyttats till Använd tilläggs-blobbar den 1 november 2018.
author: johnkemnetz
services: monitoring
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.openlocfilehash: d4be3983d70a1ca78d849e231b8cc55e2b5895d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033205"
---
# <a name="prepare-for-format-change-to-azure-monitor-platform-logs-archived-to-a-storage-account"></a>Förbered för format ändring till Azure Monitor plattforms loggar arkiverade på ett lagrings konto

> [!WARNING]
> Om du skickar [Azures resurs loggar eller mått till ett lagrings konto med hjälp av diagnostikinställningar](./resource-logs.md#send-to-azure-storage) eller [aktivitets loggar till ett lagrings konto med hjälp av logg profiler](./resource-logs.md#send-to-azure-storage), ändrades formatet för data i lagrings kontot till JSON-linjer på nov. 1 2018. Anvisningarna nedan beskriver effekten och hur du uppdaterar ditt verktyg för att hantera det nya formatet.
>

## <a name="what-changed"></a>Vad som ändrats

Azure Monitor erbjuder en funktion som gör att du kan skicka resurs loggar och aktivitets loggar till ett Azure Storage-konto, Event Hubs namnrymd eller till en Log Analytics arbets yta i Azure Monitor. För att kunna åtgärda ett system prestanda problem, den **1 November 2018 vid 12:00 midnatt UTC** , har formatet för loggdata ändrats till Blob Storage. Om du har verktyg som läser data från Blob Storage måste du uppdatera verktyget för att förstå det nya data formatet.

* På torsdag 1 november 2018 vid 12:00 midnatt UTC, ändrades BLOB-formatet till JSON- [linjer](http://jsonlines.org/). Det innebär att varje post avgränsas med en ny rad, utan matris för yttre poster och inga kommatecken mellan JSON-poster.
* BLOB-formatet har ändrats för alla diagnostikinställningar i alla prenumerationer på samma gång. Den första PT1H.jspå fil som har spridits för 1 november använde detta nya format. Blob-och container namnen förblir desamma.
* Ställer in en diagnostisk inställning mellan före 1 november fortsatte att generera data i det aktuella formatet fram till 1 november.
* Den här ändringen inträffade samtidigt i alla offentliga moln regioner. Ändringen görs inte i Microsoft Azure som drivs av 21Vianet, Azure Germany eller Azure Government moln ännu.
* Den här ändringen påverkar följande data typer:
  * [Azure resurs loggar](./resource-logs.md#send-to-azure-storage) ([Se lista över resurser här](./resource-logs-schema.md))
  * [Azure-resursens mått exporteras av diagnostikinställningar](../essentials/diagnostic-settings.md)
  * [Azures aktivitets logg data exporteras av logg profiler](./activity-log.md)
* Den här ändringen påverkar inte:
  * Nätverks flödes loggar
  * Azures tjänst loggar är inte tillgängliga via Azure Monitor än (till exempel Azure App Service resurs loggar, lagrings analys loggar)
  * Routning av Azures resurs loggar och aktivitets loggar till andra destinationer (Event Hubs, Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Så här ser du om du påverkas

Du påverkas bara av den här ändringen om du:
1. Skickar loggdata till ett Azure Storage-konto med hjälp av en diagnostisk inställning och
2. Har verktyg som är beroende av JSON-strukturen för dessa loggar i lagring.
 
För att identifiera om du har diagnostikinställningar som skickar data till ett Azure Storage-konto, kan du gå till **övervaknings** avsnittet i portalen, klicka på **diagnostikinställningar** och identifiera alla resurser som har **diagnostisk status** inställt på **aktive rad**:

![Bladet Azure Monitor diagnostikinställningar](media/resource-logs-blob-format/portal-diag-settings.png)

Om diagnostisk status är aktive rad har du en aktiv diagnostisk inställning för den resursen. Klicka på resursen för att se om några diagnostikinställningar skickar data till ett lagrings konto:

![Lagrings konto aktiverat](media/resource-logs-blob-format/portal-storage-enabled.png)

Om du har resurser som skickar data till ett lagrings konto med hjälp av dessa resurs diagnostikinställningar, kommer formatet på data i det lagrings kontot att påverkas av den här ändringen. Om du inte har anpassat verktyg som påverkar dessa lagrings konton påverkas inte format ändringen.

### <a name="details-of-the-format-change"></a>Information om format ändringen

Det aktuella formatet för PT1H.jspå filen i Azure Blob Storage använder en JSON-matris med poster. Här är ett exempel på en logg fil för nyckel valvet:

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

Det nya formatet använder [JSON-linjer](http://jsonlines.org/), där varje händelse är en rad och rad matnings tecknet indikerar en ny händelse. Så här ser exemplet ovan ut i PT1H.jspå filen efter ändringen:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Det nya formatet gör det möjligt Azure Monitor att skicka loggfiler med hjälp av [bifogade blobbar](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs), vilket är mer effektivt för att kontinuerligt lägga till nya händelse data.

## <a name="how-to-update"></a>Så här uppdaterar du

Du behöver bara göra uppdateringar om du har anpassat verktyg som matar in dessa loggfiler för ytterligare bearbetning. Om du använder ett externt logg analys-eller SIEM-verktyg rekommenderar vi att du [använder Event Hub för att mata in dessa data i stället](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). Integrering av Event Hub är enklare när det gäller bearbetning av loggar från många tjänster och bok märkes plats i en viss logg.

Anpassade verktyg bör uppdateras för att hantera både det aktuella formatet och JSON-linje formatet som beskrivs ovan. Detta säkerställer att dina verktyg inte försvinner när data börjar visas i det nya formatet.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [att arkivera resurs resurs loggar till ett lagrings konto](./resource-logs.md#send-to-azure-storage)
* Lär dig mer om [att arkivera aktivitets logg data till ett lagrings konto](./activity-log.md#legacy-collection-methods)
