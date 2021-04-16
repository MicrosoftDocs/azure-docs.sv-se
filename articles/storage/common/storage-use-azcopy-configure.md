---
title: Hitta fel & återuppta jobb med loggar i AzCopy (Azure Storage) | Microsoft Docs
description: Lär dig hur du använder loggar för att diagnostisera fel och för att återuppta jobb som pausas med hjälp av planfiler.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: d3b956803e9a796c49288f90873e88c3b69f1c7b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502903"
---
# <a name="find-errors-and-resume-jobs-by-using-log-and-plan-files-in-azcopy"></a>Hitta fel och återuppta jobb med hjälp av logg- och planfiler i AzCopy

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobar eller filer till eller från ett lagringskonto. Den här artikeln hjälper dig att använda loggar för att diagnostisera fel och sedan använda planfiler för att återuppta jobb. Den här artikeln visar också hur du konfigurerar logg- och planfiler genom att ändra deras detaljnivå och standardplatsen där de lagras.

> [!NOTE]
> Om du letar efter innehåll som hjälper dig att komma igång med AzCopy kan du gå [till Kom igång med AzCopy.](storage-use-azcopy-v10.md)

## <a name="log-and-plan-files"></a>Logg- och planfiler

AzCopy skapar *logg-* *och planfiler* för varje jobb. Du kan använda dessa loggar för att undersöka och felsöka eventuella problem. 

Loggarna innehåller statusen för felet ( `UPLOADFAILED` , och ), den fullständiga `COPYFAILED` `DOWNLOADFAILED` sökvägen och orsaken till felet.

Som standard finns logg- och planfilerna i katalogen i Windows eller katalogen på Mac och `%USERPROFILE%\.azcopy` `$HOME$\.azcopy` Linux, men du kan ändra den platsen. 

Det relevanta felet är inte nödvändigtvis det första felet som visas i filen. För fel som nätverksfel, tidsgränser och upptagen server försöker AzCopy igen upp till 20 gånger och vanligtvis lyckas återförsöksprocessen.  Det första felet som visas kan vara något ofarligt som har lyckats.  I stället för att titta på det första felet i filen letar du efter fel som är nära `UPLOADFAILED` `COPYFAILED` , eller `DOWNLOADFAILED` . 

> [!IMPORTANT]
> När du skickar en begäran Microsoft Support (eller felsöker problemet som rör tredje part) ska du dela den version av kommandot som du vill köra. Detta säkerställer att SAS inte delas av misstag med någon. Du hittar den redacted-versionen i början av loggfilen.

## <a name="review-the-logs-for-errors"></a>Granska loggarna och se om det finns fel

Följande kommando hämtar alla fel med `UPLOADFAILED` status från `04dc9ca9-158f-7945-5933-564021086c79` loggen:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

## <a name="view-and-resume-jobs"></a>Visa och återuppta jobb

Varje överföringsåtgärd skapar ett AzCopy-jobb. Använd följande kommando för att visa jobbhistoriken:

```
azcopy jobs list
```

Om du vill visa jobbstatistiken använder du följande kommando:

```
azcopy jobs show <job-id>
```

Om du vill filtrera överföringarna efter status använder du följande kommando:

```
azcopy jobs show <job-id> --with-status=Failed
```

Använd följande kommando för att återuppta ett misslyckat/avbrutet jobb. Det här kommandot använder sin identifierare tillsammans med SAS-token eftersom den inte är beständig av säkerhetsskäl:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> Omge sökvägsargument som SAS-token med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt utom Windows-kommandogränssnittet (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe) omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ('').

När du återupptar ett jobb tittar AzCopy på jobbplanfilen. Planfilen visar en lista över alla filer som identifierades för bearbetning när jobbet först skapades. När du återupptar ett jobb försöker AzCopy överföra alla filer som anges i planfilen som inte redan har överförts.

## <a name="change-the-location-of-plan-files"></a>Ändra platsen för planfiler

Använd något av dessa kommandon.

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | Powershell:`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> I en kommandotolk använder du:: `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Använd för `azcopy env` att kontrollera det aktuella värdet för den här variabeln. Om värdet är tomt skrivs planfilerna till standardplatsen.

## <a name="change-the-location-of-log-files"></a>Ändra platsen för loggfiler

Använd något av dessa kommandon.

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | Powershell:`$env:AZCOPY_LOG_LOCATION="<value>"` <br> I en kommandotolk använder du:: `set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Använd för `azcopy env` att kontrollera det aktuella värdet för den här variabeln. Om värdet är tomt skrivs loggarna till standardplatsen.

## <a name="change-the-default-log-level"></a>Ändra standardloggnivå

Som standard är AzCopy-loggnivån inställd på `INFO` . Om du vill minska loggens verbositet för att spara diskutrymme skriver du över den här inställningen med hjälp av ``--log-level`` alternativet . 

Tillgängliga loggnivåer är: `NONE` , , , , , och `DEBUG` `INFO` `WARNING` `ERROR` `PANIC` `FATAL` .

## <a name="remove-plan-and-log-files"></a>Ta bort plan- och loggfiler

Om du vill ta bort alla plan- och loggfiler från den lokala datorn för att spara diskutrymme använder du `azcopy jobs clean` kommandot .

Om du vill ta bort planen och loggfilerna som endast är associerade med ett jobb använder du `azcopy jobs rm <job-id>` . Ersätt `<job-id>` platshållaren i det här exemplet med jobbets jobb-ID.

## <a name="see-also"></a>Se även

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
