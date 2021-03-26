---
title: Konfigurera, optimera och Felsök AzCopy med Azure Storage | Microsoft Docs
description: Konfigurera, optimera och Felsök AzCopy med Azure Storage. Ändra platsen för eller ta bort plan-och loggfilerna. Ändra standard logg nivån.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 9c699cd865746bf430193eba38ec6fa79575692e
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543456"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurera, optimera och felsöka AzCopy

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobar eller filer till eller från ett lagringskonto. Den här artikeln hjälper dig att utföra avancerade konfigurationsåtgärder och felsöka problem som kan uppstå när du använder AzCopy.

> [!NOTE]
> Om du letar efter innehåll som hjälper dig att komma igång med AzCopy kan du läsa följande artiklar:
> - [Kom igång med AzCopy](storage-use-azcopy-v10.md)
> - [Överföra data med AzCopy och Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
> - [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
> - [Överföra data med AzCopy och Amazon S3-buckets](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Konfigurera proxyinställningar

Om du vill konfigurera proxyinställningarna för AzCopy anger du `HTTPS_PROXY` miljövariabeln. Om du kör AzCopy i Windows identifierar AzCopy automatiskt proxyinställningar, så du behöver inte använda den här inställningen i Windows. Om du väljer att använda den här inställningen i Windows kommer den att åsidosätta automatisk identifiering.

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | Använd följande i en kommando tolk: `set HTTPS_PROXY=<proxy IP>:<proxy port>`<br> I PowerShell använder du: `$env:HTTPS_PROXY="<proxy IP>:<proxy port>"`|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **macOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

För närvarande stöder AzCopy inte proxyservrar som kräver autentisering med NTLM eller Kerberos.

### <a name="bypassing-a-proxy"></a>Kringgå en proxy ###

Om du kör AzCopy på Windows och vill att den inte ska använda _någon_ proxy alls (i stället för att automatiskt identifiera inställningarna) använder du dessa kommandon. Med de här inställningarna kommer AzCopy inte att leta upp eller försöka använda någon proxy.

| Operativsystem | Miljö | Kommandon  |
|--------|-----------|----------|
| **Windows** | Kommando tolk (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

På andra operativ system lämnar du bara HTTPS_PROXY Variable unset om du inte vill använda någon proxy.

## <a name="optimize-performance"></a>Optimera prestanda

Du kan använda prestanda mätning och sedan använda kommandon och miljövariabler för att hitta en optimal kompromiss mellan prestanda och resursförbrukning.

Det här avsnittet hjälper dig att utföra följande optimerings aktiviteter:

> [!div class="checklist"]
> * Kör benchmark-tester
> * Optimera data flödet
> * Optimera minnes användning 
> * Optimera filsynkronisering

### <a name="run-benchmark-tests"></a>Kör benchmark-tester

Du kan köra ett prestandatest på vissa BLOB-behållare eller fil resurser för att visa allmänna prestanda statistik och för att identifiera Flask halsar i identiteter. Du kan köra testet genom att ladda upp eller hämta genererade test data. 

Använd följande kommando för att köra ett prestandatest.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Exempel** | `azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> I det här exemplet omges Sök vägs argument med enkla citat tecken (' '). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

Det här kommandot kör prestanda mätning genom att överföra test data till ett angivet mål. Test data genereras i minnet, överförs till målet och tas sedan bort från målet när testet har slutförts. Du kan ange hur många filer som ska genereras och vilken storlek du vill att de ska vara med hjälp av valfria kommando parametrar.

Om du vill köra det här testet genom att hämta data anger du `mode` parametern till `download` . Detaljerade referens dokument finns i [AzCopy benchmark](storage-ref-azcopy-bench.md). 

### <a name="optimize-throughput"></a>Optimera data flödet

Du kan använda `cap-mbps` flaggan i dina kommandon för att placera ett tak på data flödets data hastighet. Följande kommando återupptar till exempel ett jobb-och Caps-genomflöde till `10` megabit (MB) per sekund. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

Dataflödet kan minska vid överföring av små filer. Du kan öka data flödet genom att ställa in `AZCOPY_CONCURRENCY_VALUE` miljövariabeln. Den här variabeln anger antalet samtidiga begäranden som kan utföras.  

Om datorn har färre än 5 processorer anges värdet för den här variabeln till `32` . Annars är standardvärdet lika med 16 multiplicerat med antalet processorer. Det maximala standardvärdet för den här variabeln är `3000` , men du kan manuellt ange det här värdet högre eller lägre. 

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Använd `azcopy env` för att kontrol lera det aktuella värdet för den här variabeln. Om värdet är tomt kan du läsa vilket värde som används genom att titta i början av en AzCopy logg fil. Det valda värdet, och orsaken till det valdes, rapporteras där.

Innan du anger den här variabeln rekommenderar vi att du kör ett benchmark-test. Test processen för benchmark rapporterar det rekommenderade samtidiga värdet. Om ditt nätverks villkor och dina nytto laster varierar kan du ange den här variabeln till ordet `AUTO` i stället för ett visst tal. Detta gör att AzCopy alltid kör samma automatiska justerings process som används i benchmark-tester.

### <a name="optimize-memory-use"></a>Optimera minnes användning

Ange `AZCOPY_BUFFER_GB` miljövariabeln för att ange den maximala mängd system minne som du vill att AzCopy ska använda för buffring vid hämtning och uppladdning av filer. Express detta värde i gigabyte (GB).

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

> [!NOTE]
> Jobb spårningen debiteras alltid ytterligare kostnader för minnes användning. Mängden varierar beroende på antalet överföringar i ett jobb. Buffertar är den största delen av minnes användningen. Du kan kontrol lera omkostnader genom `AZCOPY_BUFFER_GB` att använda så att det är så stort som möjligt, men ingen flagga för att enbart täcka den totala minnes användningen är tillgänglig.

### <a name="optimize-file-synchronization"></a>Optimera filsynkronisering

[Sync](storage-ref-azcopy-sync.md) -kommandot identifierar alla filer vid målet och jämför sedan fil namn och senast ändrade tidsstämplar innan synkroniseringen startades. Om du har ett stort antal filer kan du förbättra prestandan genom att ta bort klient bearbetningen. 

För att åstadkomma detta använder du i stället [AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot och anger `--overwrite` flaggan till `ifSourceNewer` . AzCopy kommer att jämföra filer när de kopieras utan att utföra några startgenomsökningar och jämförelser. Detta ger en prestanda gräns i fall där det finns ett stort antal filer att jämföra.

[AzCopy Copy](storage-ref-azcopy-copy.md) -kommandot tar inte bort filer från målet, så om du vill ta bort filer på målet när de inte längre finns på källan använder du kommandot [AzCopy Sync](storage-ref-azcopy-sync.md) med `--delete-destination` flaggan inställd på värdet `true` eller `prompt` . 

## <a name="troubleshoot-issues"></a>Felsöka problem

AzCopy skapar logg- och planfiler för varje jobb. Du kan undersöka och felsöka eventuella problem med hjälp av loggarna. 

Loggarna innehåller status för felen ( `UPLOADFAILED` , `COPYFAILED` , och `DOWNLOADFAILED` ), den fullständiga sökvägen och orsaken till problemet.

Som standard finns logg-och plan-filerna i `%USERPROFILE%\.azcopy` katalogen på Windows eller i en `$HOME$\.azcopy` katalog på Mac och Linux, men du kan ändra platsen om du vill.

Det relevanta felet är inte nödvändigt vis det första fel som visas i filen. För fel som nätverks fel, timeout-fel och Server upptaget, kommer AzCopy att försöka igen till 20 gånger, och normalt lyckas processen igen.  Det första fel du ser kan vara något ofarligt som har gjorts om.  Så i stället för att titta på det första felet i filen söker du efter de fel som finns nära `UPLOADFAILED` , `COPYFAILED` eller `DOWNLOADFAILED` . 

> [!IMPORTANT]
> När du skickar en begäran till Microsoft Support (eller fel sökning av problemet som berör tredje part) delar du den avvisade versionen av kommandot som du vill köra. Detta säkerställer att SAS inte delas av misstag med vem. Du kan hitta den avvisade versionen i början av logg filen.

### <a name="review-the-logs-for-errors"></a>Granska loggarna för fel

Följande kommando får alla fel med `UPLOADFAILED` status från `04dc9ca9-158f-7945-5933-564021086c79` loggen:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Visa och återuppta jobb

Varje överförings åtgärd kommer att skapa ett AzCopy-jobb. Använd följande kommando för att visa jobb historiken:

```
azcopy jobs list
```

Om du vill visa jobb statistiken använder du följande kommando:

```
azcopy jobs show <job-id>
```

Använd följande kommando för att filtrera överföringarna efter status:

```
azcopy jobs show <job-id> --with-status=Failed
```

Använd följande kommando för att återuppta ett misslyckat/avbrutet jobb. Det här kommandot använder sitt ID tillsammans med SAS-token eftersom det inte är beständigt av säkerhets skäl:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> Omge Sök vägs argument som SAS-token med enkla citat tecken (). Använd enkla citat tecken i alla kommando gränssnitt utom Windows Command Shell (cmd.exe). Om du använder ett Windows Command Shell (cmd.exe), omger Sök vägs argument med dubbla citat tecken ("") i stället för enkla citat tecken ().

När du återupptar ett jobb tittar AzCopy på jobb Plans filen. Plan filen visar en lista över alla filer som identifierades för bearbetning när jobbet först skapades. När du återupptar ett jobb kommer AzCopy att försöka överföra alla filer som anges i den plan fil som inte redan har överförts.

## <a name="change-the-location-of-the-plan-and-log-files"></a>Ändra placeringen av plan-och loggfilerna

Som standard finns plan-och loggfiler i `%USERPROFILE%\.azcopy` katalogen på Windows eller i `$HOME/.azcopy` katalogen på Mac och Linux. Du kan ändra den här platsen.

### <a name="change-the-location-of-plan-files"></a>Ändra placeringen av plan filer

Använd något av dessa kommandon.

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | PowerShell`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> Använd följande i en kommando tolk: `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Använd `azcopy env` för att kontrol lera det aktuella värdet för den här variabeln. Om värdet är tomt skrivs planera filer till standard platsen.

### <a name="change-the-location-of-log-files"></a>Ändra platsen för loggfiler

Använd något av dessa kommandon.

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | PowerShell`$env:AZCOPY_LOG_LOCATION="<value>"` <br> Använd följande i en kommando tolk: `set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Använd `azcopy env` för att kontrol lera det aktuella värdet för den här variabeln. Om värdet är tomt skrivs loggar till standard platsen.

## <a name="change-the-default-log-level"></a>Ändra standard logg nivån

Som standard är logg nivån för AzCopy inställd på `INFO` . Om du vill minska loggens utförlighet för att spara disk utrymme skriver du över den här inställningen med hjälp av ``--log-level`` alternativet. 

Tillgängliga logg nivåer är: `NONE` ,,,,, `DEBUG` `INFO` `WARNING` `ERROR` `PANIC` och `FATAL` .

## <a name="remove-plan-and-log-files"></a>Ta bort plan-och loggfiler

Om du vill ta bort alla plan-och loggfiler från den lokala datorn för att spara disk utrymme, använder du `azcopy jobs clean` kommandot.

Om du vill ta bort plan-och loggfilerna som är associerade med endast ett jobb använder du `azcopy jobs rm <job-id>` . Ersätt `<job-id>` plats hållaren i det här exemplet med jobb-ID: t för jobbet.
