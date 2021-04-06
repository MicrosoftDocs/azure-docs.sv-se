---
title: Felsöka långa svarstider med hjälp av loggar i Lagringsanalys
description: Identifiera och Felsök latens problem med Azure Storage analytiska loggar och optimera klient programmet.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 1e6033f9a8f4cecd2429eca67a3d58e54d7ae1f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99221116"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Felsöka långa svarstider med hjälp av loggar i Lagringsanalys

Diagnostisering och fel sökning är en viktig kunskap för att skapa och stödja klient program med Azure Storage.

På grund av den distribuerade typen av ett Azure-program kan det vara mer komplicerat än i traditionella miljöer att diagnostisera och felsöka både fel och prestanda problem.

Följande steg visar hur du kan identifiera och felsöka latens problem med hjälp av Azure Storage analytiska loggar och optimera klient programmet.

## <a name="recommended-steps"></a>Rekommenderade åtgärder

1. Ladda ned [Lagringsanalys loggar](./manage-storage-analytics-logs.md#download-storage-logging-log-data).

2. Använd följande PowerShell-skript för att konvertera RAW-formatet loggar till tabell format:

   ```Powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv “REPLACE THIS WITH FILE PATH” -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. Skriptet startar ett GUI-fönster där du kan filtrera informationen efter kolumner, som du ser nedan.

   ![Fönstret lagrings analys logg tolkare](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. Begränsa logg posterna baserat på "åtgärds typ" och leta efter logg posten som skapades under utfärdarens tidsram.

   ![Logg poster för åtgärds typ](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. Under den tid då problemet uppstod är följande värden viktiga:

   * Åtgärds typ = GetBlob
   * Request-status = SASNetworkError
   * Slut punkt till slut punkt – i-MS = 8453
   * Server-svars tid – in-MS = 391

   Svars tid från slut punkt till slut punkt beräknas med hjälp av följande ekvation:

   * Svars tid från slut punkt till slut punkt = Server-Latency + klient svars tid

   Beräkna klient fördröjningen med hjälp av logg posten:

   * Klient svars tid = svars tid från slut punkt till slut punkt – Server-Latency

        Exempel: 8453 – 391 = 8062ms

   Följande tabell innehåller information om OperationType-och RequestStatus-resultaten med hög latens:

   | Blob-typ |RequestStatus =<br>Klart|RequestStatus =<br>SÄKERHETS NetworkError|Rekommendation|
   |---|---|---|---|
   |GetBlob|Ja|Inga|[**GetBlob-åtgärd:** RequestStatus = lyckades](#getblob-operation-requeststatus--success)|
   |GetBlob|Inga|Ja|[**GetBlob-åtgärd:** RequestStatus = (SAS) NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|Ja|Inga|[**Placerings åtgärd:** RequestStatus = lyckades](#put-operation-requeststatus--success)|
   |PutBlob|Inga|Ja|[**Placerings åtgärd:** RequestStatus = (SAS) NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Status resultat

### <a name="getblob-operation-requeststatus--success"></a>GetBlob-åtgärd: RequestStatus = lyckades

Kontrol lera följande värden som anges i steg 5 i avsnittet "rekommenderade steg":

* Svars tid från slut punkt till slut punkt
* Server-Latency
* Client-Latency

I en **GetBlob-åtgärd** med **RequestStatus = lyckades**, om den **maximala tiden** används i **klient svars** tiden, betyder det att Azure Storage kostar en stor mängd tid när data skrivs till klienten. Den här fördröjningen indikerar ett Client-Side problem.

**Rekommenderade**

* Undersök koden i klienten.
* Använd wireshark, Microsoft Message Analyzer eller TCPing för att undersöka problem med nätverks anslutningen från klienten. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>GetBlob-åtgärd: RequestStatus = (SAS) NetworkError

Kontrol lera följande värden som anges i steg 5 i avsnittet "rekommenderade steg":

* Svars tid från slut punkt till slut punkt
* Server-Latency
* Client-Latency

I en **GetBlob-åtgärd** med **REQUESTSTATUS = (SAS) NetworkError**, om den **maximala tiden** används i **klient svars** tiden, är det vanligaste problemet att klienten kopplas från innan en tids gräns går ut i lagrings tjänsten.

**Rekommenderade**

* Undersök koden i klienten för att förstå varför och när klienten kopplas från lagrings tjänsten.
* Använd wireshark, Microsoft Message Analyzer eller TCPing för att undersöka problem med nätverks anslutningen från klienten. 

### <a name="put-operation-requeststatus--success"></a>Placerings åtgärd: RequestStatus = lyckades

Kontrol lera följande värden som anges i steg 5 i avsnittet "rekommenderade steg":

* Svars tid från slut punkt till slut punkt
* Server-Latency
* Client-Latency

I en **Placera-åtgärd** med **RequestStatus = lyckades**, om den **maximala tiden** används i **klient svars** tiden, indikerar detta att klienten tar längre tid att skicka data till Azure Storage. Den här fördröjningen indikerar ett Client-Side problem.

**Rekommenderade**

* Undersök koden i klienten.
* Använd wireshark, Microsoft Message Analyzer eller TCPing för att undersöka problem med nätverks anslutningen från klienten. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Placerings åtgärd: RequestStatus = (SAS) NetworkError

Kontrol lera följande värden som anges i steg 5 i avsnittet "rekommenderade steg":

* Svars tid från slut punkt till slut punkt
* Server-Latency
* Client-Latency

I en **PutBlob-åtgärd** med **REQUESTSTATUS = (SAS) NetworkError**, om den **maximala tiden** används i **klient svars** tiden, är det vanligaste problemet att klienten kopplas från innan en tids gräns går ut i lagrings tjänsten.

**Rekommenderade**

* Undersök koden i klienten för att förstå varför och när klienten kopplas från lagrings tjänsten.
* Använd wireshark, Microsoft Message Analyzer eller TCPing för att undersöka problem med nätverks anslutningen från klienten.