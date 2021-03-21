---
title: Paradigm för program mässig åtkomst
description: 'Förstå det övergripande flödet av API-anropet för att få programmerings analys. API: erna för åtkomst till analys rapporter i Microsofts kommersiella marknads plats omfattas också.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8e0b94a46e96dd8ba16040e16b421520eb67de19
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584227"
---
# <a name="programmatic-access-paradigm"></a>Paradigm för program mässig åtkomst

Det här diagrammet visar det API-anrops mönster som används för att skapa en ny rapportmall, schemalägga den anpassade rapporten och hämta felaktiga data.

[ ![ Illustrerar API-anropet som används för att skapa en ny rapportmall, schemalägga den anpassade rapporten och hämta felaktiga data.](./media/analytics-programmatic-access/analytics-high-level-flow.png)](./media/analytics-programmatic-access/analytics-high-level-flow.png#lightbox) 
 ***Bild 1: flöde på hög nivå av API-anropets mönster***

Den här listan innehåller mer information om bild 1.

1. Klient programmet kan definiera den anpassade rapportens schema/mall genom att anropa [API: et för att skapa rapport frågor](#create-report-query-api). Alternativt kan du använda en rapportmall ( `QueryId` ) från [listan över system frågor](analytics-system-queries.md).
1. Vid lyckad returnerar API: et för att skapa rapport mal len `QueryId` .
1. Klient programmet anropar sedan [Skapa rapport-API](#create-report-api) med hjälp av `QueryID` tillsammans med rapportens start datum, upprepnings intervall, upprepning och en valfri återanrops-URI.
1. Vid lyckad returnerar [API: et Create Report](#create-report-api) `ReportID` .
1. Klient programmet får ett meddelande vid återanrops-URI så snart rapport data är klara att laddas ned.
1. Klient programmet använder sedan [API: t Hämta rapport körningar](#get-report-executions-api) för att fråga efter status för rapporten med `Report ID` datum intervallet och.
1. Vid lyckad återkommer hämtnings länken för rapporten och programmet kan initiera hämtning av data.

## <a name="report-query-language-specification"></a>Rapportera språk specifikation för frågor

Vi tillhandahåller [system frågor](analytics-system-queries.md) som du kan använda för att skapa rapporter, men du kan också skapa egna frågor utifrån dina affärs behov. Mer information om anpassade frågor finns i [anpassad fråga specifikation](analytics-custom-query-specification.md).

## <a name="create-report-query-api"></a>Skapa rapport frågas-API

Det här API: et hjälper till att skapa anpassade frågor som definierar data uppsättningen som kolumner och mått måste exporteras från. API: et ger flexibiliteten att skapa en ny mall för rapportering utifrån dina affärs behov.

Du kan också använda [system frågor](analytics-system-queries.md) som vi tillhandahåller. När anpassade rapportmallar inte behövs kan du anropa [API: et för att skapa rapporter](#create-report-api) direkt med [QueryIds](analytics-system-queries.md) i system frågorna som vi tillhandahåller.

I följande exempel visas hur du skapar en anpassad fråga för att få _normaliserad användning och uppskattade finansiella kostnader för betalda SKU: er_ från [ISVUsage](analytics-make-your-first-api-call.md#programmatic-api-call) -datauppsättningen under den senaste månaden.

*Syntax för begäran*

| Metod | URI för förfrågan |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries` |
|||

*Begär ande huvud*

| Huvud | Typ | Beskrivning |
| ------------- | ------------- | ------------- |
| Auktorisering | sträng | Krävs. Azure Active Directory (Azure AD)-åtkomsttoken. Formatet är `Bearer <token>`. |
| Content-Type | `string` | `application/JSON` |
||||

*Sök vägs parameter*

Inget

*Frågeparameter*

Inget

*Exempel på begäran om nytto Last*

```json
{
    "Name": "ISVUsageQuery",
    "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
    "Query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"
}
```

*Ordlista*

Den här tabellen innehåller nyckel definitionerna för element i nytto lasten för begäran.

| Parameter | Krävs | Beskrivning | Tillåtna värden |
| ------------ | ------------- | ------------- | ------------- |
| `Name` | Ja | Eget namn på frågan | sträng |
| `Description` | No | Beskrivning av vad frågan returnerar | sträng |
| `Query` | Ja | Frågesträng för rapport | Datatyp: sträng<br>[Anpassad fråga](analytics-sample-queries.md) baserat på affärs behov |
|||||

> [!NOTE]
> Exempel på anpassade fråge exempel finns i [exempel frågor](analytics-sample-queries.md).

*Exempel svar*

Svarets nytto Last struktureras enligt följande:

Svars koder: 200, 400, 401, 403, 500

Exempel på svars nytto last:

```json
{
  "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": " ISVUsageQuery",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs”,
            "query": " SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34Z"
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

*Ordlista*

Den här tabellen innehåller nyckel definitionerna för element i svaret.

| Parameter | Beskrivning |
| ------------ | ------------- |
| `QueryId` | Globalt unik identifierare (UUID) för frågan du skapade |
| `Name` | Eget namn som angetts för frågan i nytto lasten för begäran |
| `Description` | Beskrivning som angavs när frågan skapades |
| `Query` | Rapport frågan skickades som ininformation när frågan skapades |
| `Type` | Ange till `userDefined` |
| `User` | Användar-ID som användes för att skapa frågan |
| `CreatedTime` | UTC-tid som frågan skapades i det här formatet: ÅÅÅÅ-MM-ddTHH: mm: ssZ |
| `TotalCount` | Antal data uppsättningar i värde mat ris |
| `StatusCode` | Resultatkod<br>De möjliga värdena är 200, 400, 401, 403, 500 |
| `message` | Status meddelande från API-körningen |
|||

## <a name="create-report-api"></a>Skapa rapport-API

`QueryID`Detta API kan anropas för att schemalägga en fråga som ska köras med jämna mellanrum när du skapar en anpassad rapportmall och tar emot den som en del av fråge svaret för att [skapa en rapport](#create-report-query-api) . Du kan ange en frekvens och ett schema för rapporten som ska levereras. För system frågor som vi tillhandahåller kan skapa rapport-API: et också anropas med [fråge](analytics-sample-queries.md).

*Syntax för begäran*

| Metod | URI för förfrågan |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport` |
|||

*Begär ande huvud*

| Huvud | Typ | Beskrivning |
| ------ | ---- | ----------- |
| Auktorisering | sträng | Krävs. Azure Active Directory (Azure AD)-åtkomsttoken. Formatet är `Bearer <token>`. |
| Innehållstyp | sträng | `application/JSON` |
||||

*Sök vägs parameter*

Inget

*Frågeparameter*

Inget

*Exempel på begäran om nytto Last*

```json
{
  "ReportName": "ISVUsageReport",
  "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
  "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c ",
  "StartTime": "2021-01-06T19:00:00Z ",
  "RecurrenceInterval": 48,
  "RecurrenceCount": 20,
  "Format": "csv",
  "CallbackUrl": "https://<SampleCallbackUrl>"
}
```

*Ordlista*

Den här tabellen innehåller nyckel definitionerna för element i nytto lasten för begäran.

| Parameter | Krävs | Beskrivning | Tillåtna värden |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Ja | Namn som tilldelats rapporten | sträng |
| `Description` | No | Beskrivning av den skapade rapporten | sträng |
| `QueryId` | Ja | Fråge-ID för rapport | sträng |
| `StartTime` | Ja | UTC-tidsstämpeln då rapportgenerering ska börja.<br>Formatet ska vara: ÅÅÅÅ-MM-ddTHH: mm: ssZ | sträng |
| `RecurrenceInterval` | Ja | Frekvens i timmar då rapporten ska genereras.<br>Det minsta värdet är 4 och det maximala värdet är 90. | heltal |
| `RecurrenceCount` | Inga | Antal rapporter som ska genereras. | heltal |
| `Format` | Inga | Den exporterade filens fil format.<br>Standardvärdet är. SKV. | CSV/TSV |
| `CallbackUrl` | Inga | Offentligt nåbar URL som kan konfigureras som återanrops mål. | Sträng (http-URL) |
| `ExecuteNow` | Inga | Den här parametern ska användas för att skapa en rapport som bara ska köras en gång. `StartTime`, `RecurrenceInterval` och `RecurrenceCount` ignoreras om detta är inställt på `true` . Rapporten körs omedelbart i asynkron miljö. | True/false |
| `QueryStartTime` | Inga | Du kan också ange start tiden för frågan som extraherar data. Den här parametern gäller bara för en tids körnings rapport som har `ExecuteNow` angetts till `true` . Formatet ska vara ÅÅÅÅ-MM-ddTHH: mm: ssZ | Timestamp som sträng |
| `QueryEndTime` | Inga | Alternativt anger slut tiden för frågan som extraherar data. Den här parametern gäller bara för en tids körnings rapport som har `ExecuteNow` angetts till `true` . Formatet ska vara ÅÅÅÅ-MM-ddTHH: mm: ssZ | Timestamp som sträng |
|||||

*Exempelsvar*

Svarets nytto Last struktureras enligt följande:

Svars kod: 200, 400, 401, 403, 404, 500

Nytto last för svar:

```json
{
  "Value": [
    {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVUsageReport",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": "https://<SampleCallbackUrl>",
            "format": "csv"    
    }
  ],
  "TotalCount": 1,
  "Message": "Report created successfully",
  "StatusCode": 200
}
```

*Ordlista*

Den här tabellen innehåller nyckel definitionerna för element i svaret.

| Parameter | Beskrivning |
| ------------ | ------------- |
| `ReportId` | Universell unik identifierare (UUID) för rapporten du skapade |
| `ReportName` | Namn angivet för rapporten i nytto lasten för begäran |
| `Description` | Beskrivning som angavs när rapporten skapades |
| `QueryId` | Fråge-ID skickades vid den tidpunkt då du skapade rapporten |
| `Query` | Frågetext som ska utföras för den här rapporten |
| `User` | Användar-ID som används för att skapa rapporten |
| `CreatedTime` | UTC-tid då rapporten skapades i det här formatet: ÅÅÅÅ-MM-ddTHH: mm: ssZ |
| `ModifiedTime` | UTC-tid då rapporten senast ändrades i det här formatet: ÅÅÅÅ-MM-ddTHH: mm: ssZ |
| `StartTime` | UTC-tid då rapport körningen börjar i formatet: ÅÅÅÅ-MM-ddTHH: mm: ssZ |
| `ReportStatus` | Rapport körningens status. De möjliga värdena är **pausade**, **aktiva** och **inaktiva**. |
| `RecurrenceInterval` | Upprepnings intervall som anges när rapporten skapas |
| `RecurrenceCount` | Antal upprepningar som angavs när rapporten skapas |
| `CallbackUrl` | Återanrops-URL som angetts i begäran |
| `Format` | Rapport filens format. Möjliga värden är CSV eller TSV. |
| `TotalCount` | Antal data uppsättningar i värde mat ris |
| `StatusCode` | Resultatkod<br>De möjliga värdena är 200, 400, 401, 403, 500 |
| `message` | Status meddelande från API-körningen |
|||

## <a name="get-report-executions-api"></a>Hämta API för rapport körningar

Du kan använda den här metoden för att fråga status för en rapport körning med hjälp av den som `ReportId` tas emot från [Skapa rapport-API](#create-report-api). Metoden returnerar rapport nedladdnings länken om rapporten är klar att laddas ned. Annars returnerar metoden statusen. Du kan också använda det här API: et för att hämta alla körningar som har hänt för en specifik rapport.

> [!IMPORTANT]
> Det här API: et har standard parametrarna som har angetts för `executionStatus=Completed` och  `getLatestExecution=true` . Därför kommer att anropa API: et innan den första lyckade körningen av rapporten returnerar 404. Väntande körningar kan erhållas genom inställningen `executionStatus=Pending` .

*Syntax för begäran*

| Metod | URI för förfrågan |
| ------------ | ------------- |
| Hämta | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/{reportId}?executionId={executionId}&executionStatus={executionStatus}&getLatestExecution={getLatestExecution}` |
|||

*Begär ande huvud*

| Huvud | Typ | Beskrivning |
| ------ | ------ | ------ |
| Auktorisering | sträng | Krävs. Azure Active Directory (Azure AD)-åtkomsttoken. Formatet är `Bearer <token>`. |
| Innehållstyp | sträng | `application/json` |
||||

*Sök vägs parameter*

Inget

*Frågeparameter*

| Parameternamn | Krävs | Typ | Beskrivning |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Ja | sträng | Filtrera för att få körnings information om endast rapporter med `reportId` angivet i det här argumentet. Flera `reportIds` kan anges genom att avgränsa dem med semikolon ";". |
| `executionId` | Inga | sträng | Filtrera för att hämta information om enbart rapporter med `executionId` angivet i det här argumentet. Flera `executionIds` kan anges genom att avgränsa dem med semikolon ";". |
| `executionStatus` | Inga | sträng/Enum | Filtrera för att hämta information om enbart rapporter med `executionStatus` angivet i det här argumentet.<br>Giltiga värden är: `Pending` , `Running` ,, `Paused` och `Completed` <br>Standardvärdet är `Completed`. Du kan ange flera status värden genom att avgränsa dem med semikolon ";". |
| `getLatestExecution` | Inga | boolean | API: et kommer att returnera information om den senaste rapport körningen.<br>Den här parametern är som standard inställd på `true` . Om du väljer att skicka värdet för den här parametern som `false` , returnerar API: et de senaste 90 dagarna-körnings instanserna. |
|||||

*Begär nytto Last*

Inget

*Exempelsvar*

Svarets nytto Last struktureras enligt följande:

Svars koder: 200, 400, 401, 403, 404, 500

Exempel på svars nytto last:

```json
{
    "value": [
        {
            "executionId": "a0bd78ad-1a05-40fa-8847-8968b718d00f",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 4,
            "recurrenceCount": 10,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Completed",
            "reportAccessSecureLink": "https://<path to report for download>",
            "reportExpiryTime": null,
            "reportGeneratedTime": "2021-01-13T14:40:46Z"
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

När rapport körningen är klar visas körnings statusen `Completed` . Du kan ladda ned rapporten genom att välja URL: en i `reportAccessSecureLink` .

*Ordlista*

Nyckel definitioner för element i svaret.

| Parameter | Beskrivning |
| ------------ | ------------- |
| `ExecutionId` | Universal Unique Identifier (UUID) för körnings instansen |
| `ReportId` | Rapport-ID som är kopplat till körnings instansen |
| `RecurrenceInterval` | Upprepnings intervall som anges när rapporten skapas |
| `RecurrenceCount` | Antal upprepningar som angavs när rapporten skapas |
| `CallbackUrl` | Callback-URL som är associerad med körnings instansen |
| `Format` | Formatet på den genererade filen i slutet av körningen |
| `ExecutionStatus` | Status för rapport körnings instans.<br>Giltiga värden är: `Pending` , `Running` ,, `Paused` och `Completed` |
| `ReportAccessSecureLink` | Länk genom vilken rapporten kan nås på ett säkert sätt |
| `ReportExpiryTime` | UTC-tid efter vilken rapport länken upphör att gälla i det här formatet: ÅÅÅÅ-MM-ddTHH: mm: ssZ |
| `ReportGeneratedTime` | UTC-tid då rapporten genererades i det här formatet: ÅÅÅÅ-MM-ddTHH: mm: ssZ |
| `TotalCount` | Antal data uppsättningar i värde mat ris |
| `StatusCode` | Resultatkod <br>De möjliga värdena är 200, 400, 401, 403, 404 och 500 |
| `message` | Status meddelande från API-körningen |
|||

## <a name="next-steps"></a>Nästa steg
- Du kan prova API: erna via [URL: en för Swagger-API](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html)
- Kom igång med programmatisk åtkomst till analys data
