---
title: AzCopy v10-konfigurationsinställning (Azure Storage) | Microsoft Docs
description: Den här artikeln innehåller referensinformation för AzCopy V10-konfigurationsinställningar.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4fcbcf145dc417d2a7f78913e70429c3929cd902
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509121"
---
# <a name="azcopy-v10-configuration-settings-azure-storage"></a>Konfigurationsinställningar för AzCopy v10 (Azure Storage)

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobar eller filer till eller från ett lagringskonto. Den här artikeln innehåller en lista över miljövariabler som du kan använda för att konfigurera AzCopy v10.

> [!NOTE]
> Om du letar efter innehåll som hjälper dig att komma igång med AzCopy kan du gå [till Kom igång med AzCopy.](storage-use-azcopy-v10.md)

## <a name="azcopy-v10-environment-variables"></a>Miljövariabler för AzCopy v10

I följande tabell beskrivs varje miljövariabel och innehåller länkar till innehåll som kan hjälpa dig att använda variabeln.

| Miljövariabel | Beskrivning |
|--|--|
| AWS_ACCESS_KEY_ID | Amazon Web Services åtkomstnyckel. Tillhandahåller en nyckel för att auktorisera med Amazon Web Services. [Kopiera data från Amazon S3 till Azure Storage med hjälp av AzCopy](storage-use-azcopy-s3.md) |
| AWS_SECRET_ACCESS_KEY | Amazon Web Services en hemlig åtkomstnyckel Ger en hemlig nyckel för auktorisera med Amazon Web Services. [Kopiera data från Amazon S3 till Azure Storage med hjälp av AzCopy](storage-use-azcopy-s3.md) |
| AZCOPY_ACTIVE_DIRECTORY_ENDPOINT | Den Azure Active Directory slutpunkt som ska användas. Den här variabeln används bara för automatisk inloggning. Använd kommandoradsflaggan i stället när du till exempel kör inloggningskommandot. |
| AZCOPY_AUTO_LOGIN_TYPE | Ange variabeln `DEVICE` till , eller `MSI` `SPN` . Den här variabeln ger möjlighet att auktorisera utan att använda `azcopy login` kommandot . Den här mekanismen är användbar i fall där operativsystemet inte har ett hemligt arkiv, till exempel en *Linux-nyckelring.* Se [Auktorisera utan ett hemligt arkiv](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store). |
| AZCOPY_BUFFER_GB | Ange den maximala mängden systemminne som du vill att AzCopy ska använda när du laddar ned och laddar upp filer. Uttryck det här värdet i gigabyte (GB). Se [Optimera minnesanvändning](storage-use-azcopy-optimize.md#optimize-memory-use) |
| AZCOPY_CACHE_PROXY_LOOKUP | Som standard cachelagrar AzCopy på Windows proxyserveruppslag på värdnamnsnivå (utan att ta hänsyn till URL-sökvägen). Ange till ett annat värde än "true" för att inaktivera cachen. |
| AZCOPY_CONCURRENCY_VALUE | Anger antalet samtidiga begäranden som kan ske. Du kan använda den här variabeln för att öka dataflödet. Om datorn har färre än 5 processorer anges värdet för den här variabeln till `32` . Annars är standardvärdet lika med 16 multiplicerat med antalet processorer. Det maximala standardvärdet för den här variabeln `3000` är , men du kan manuellt ange det här värdet högre eller lägre. Se [Öka samtidighet](storage-use-azcopy-optimize.md#increase-concurrency) |
| AZCOPY_CONCURRENT_FILES | Åsidosätter det (ungefärliga) antalet filer som pågår samtidigt, genom att kontrollera hur många filer som vi initierar överföringar samtidigt för. |
| AZCOPY_CONCURRENT_SCAN | Styr den (högsta) grad av parallellitet som används vid genomsökning. Påverkar endast parallelliserade uppräkningar, som omfattar Azure Files/blobar och lokala filsystem. |
| AZCOPY_DEFAULT_SERVICE_API_VERSION | Åsidosätter tjänst-API-versionen så att AzCopy kan hantera anpassade miljöer, till exempel Azure Stack. |
| AZCOPY_DISABLE_HIERARCHICAL_SCAN | Gäller endast när Azure Blobs är källan. Samtidig genomsökning går snabbare men använder api:et för hierarkisk lista, vilket kan resultera i mer I/O/kostnad. Ange "true" för att minska prestandan men spara på kostnaden. |
| AZCOPY_JOB_PLAN_LOCATION | Åsidosätter var jobbplanfilerna (används för förloppsspårning och återupptar) lagras för att undvika att fylla en disk. |
| AZCOPY_LOG_LOCATION | Åsidosätter var loggfilerna lagras för att undvika att fylla en disk. |
| AZCOPY_MSI_CLIENT_ID | Klient-ID för en användar tilldelad hanterad identitet. Använd när `AZCOPY_AUTO_LOGIN_TYPE` är inställt på `MSI` . Se [Auktorisera utan ett hemligt arkiv](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_MSI_OBJECT_ID | Objekt-ID för den användar tilldelade hanterade identiteten. Använd när `AZCOPY_AUTO_LOGIN_TYPE` är inställt på `MSI` . Se [Auktorisera utan ett hemligt arkiv](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_MSI_RESOURCE_STRING | Resurs-ID för den användar tilldelade hanterade identiteten. Se [Auktorisera utan ett hemligt arkiv](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_PACE_PAGE_BLOBS | Ska dataflödet för sidblobar justeras automatiskt för att matcha tjänstbegränsningar? Standardvärdet är true. Ställ in på "false" för att inaktivera |
| AZCOPY_PARALLEL_STAT_FILES | Gör så att AzCopy söker efter filegenskaper på parallella "trådar" när det lokala filsystemet genomsöks.  Trådarna hämtas från poolen som definieras av AZCOPY_CONCURRENT_SCAN.  Om detta är sant kan du förbättra genomsökningsprestanda i Linux.  Behövs inte eller rekommenderas inte i Windows. |
| AZCOPY_SHOW_PERF_STATES | Om det är inställt på något så inkluderar utdata på skärmen antalet segment efter tillstånd |
| AZCOPY_SPA_APPLICATION_ID | Program-ID:t för appregistreringen för tjänstens huvudnamn. Använd när `AZCOPY_AUTO_LOGIN_TYPE` är inställt på `SPN` . Se [Auktorisera utan ett hemligt arkiv](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CERT_PASSWORD | Lösenordet för ett certifikat. Använd när `AZCOPY_AUTO_LOGIN_TYPE` är inställt på `SPN` . Se [Auktorisera utan ett hemligt arkiv](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CERT_PATH | Den relativa eller fullständigt kvalificerade sökvägen till en certifikatfil. Använd när `AZCOPY_AUTO_LOGIN_TYPE` är inställt på `SPN` . Se [Auktorisera utan ett hemligt arkiv](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CLIENT_SECRET | Klienthemligheten. Använd när `AZCOPY_AUTO_LOGIN_TYPE` är inställt på `SPN` . Se [Auktorisera utan ett hemligt arkiv](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_TENANT_ID | Det Azure Active Directory klientorganisations-ID som ska användas för interaktiv OAuth-enhetsinloggning. Den här variabeln används bara för automatisk inloggning. Använd kommandoradsflaggan i stället när du till exempel kör inloggningskommandot. |
| AZCOPY_TUNE_TO_CPU | Ställ in på false för att förhindra att AzCopy tar hänsyn till CPU-användningen när samtidighetsnivån automatiskt kan ställas in (till exempel i benchmark-kommandot). |
| AZCOPY_USER_AGENT_PREFIX | Lägg till ett prefix till azcopy-standardanvändaragenten, som används i telemetrisyfte. Ett blanksteg infogas automatiskt. |
| GOOGLE_APPLICATION_CREDENTIALS | Den absoluta sökvägen till nyckelfilen för tjänstkontot Innehåller en nyckel för att auktorisera med Google Cloud Storage. [Kopiera data från Google Cloud Storage till Azure Storage hjälp av AzCopy (förhandsversion)](storage-ref-azcopy-configuration-settings.md) |
| HTTPS_PROXY | Konfigurerar proxyinställningar för AzCopy. Ange den här variabeln till proxyns IP-adress och proxyportnummer. Till exempel `xx.xxx.xx.xxx:xx`. Om du kör AzCopy i Windows identifierar AzCopy automatiskt proxyinställningar, så du behöver inte använda den här inställningen i Windows. Om du väljer att använda den här inställningen i Windows kommer den att åsidosätta automatisk identifiering. Se [Konfigurera proxyinställningar](#configure-proxy-settings) |


## <a name="configure-proxy-settings"></a>Konfigurera proxyinställningar

Konfigurera proxyinställningarna för AzCopy genom att ange `HTTPS_PROXY` miljövariabeln . Om du kör AzCopy i Windows identifierar AzCopy automatiskt proxyinställningar, så du behöver inte använda den här inställningen i Windows. Om du väljer att använda den här inställningen i Windows kommer den att åsidosätta automatisk identifiering.

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | I en kommandotolk använder du: `set HTTPS_PROXY=<proxy IP>:<proxy port>`<br> I PowerShell använder du: `$env:HTTPS_PROXY="<proxy IP>:<proxy port>"`|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **macOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

AzCopy stöder för närvarande inte proxyserver som kräver autentisering med NTLM eller Kerberos.

### <a name="bypassing-a-proxy"></a>Kringgå en proxy

Om du kör AzCopy i Windows och vill be den att inte använda någon _proxy_ alls (i stället för att automatiskt identifiera inställningarna) använder du dessa kommandon. Med de här inställningarna kommer AzCopy inte att leta upp eller försöka använda någon proxyserver.

| Operativsystem | Miljö | Kommandon  |
|--------|-----------|----------|
| **Windows** | Kommandotolk (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

På andra operativsystem lämnar du helt enkelt HTTPS_PROXY variabeln om du inte vill använda någon proxy.

## <a name="see-also"></a>Se även

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Optimera prestanda för AzCopy v10 med Azure Storage](storage-use-azcopy-optimize.md)
- [Felsöka AzCopy V10-problem i Azure Storage med hjälp av loggfiler](storage-use-azcopy-configure.md)
