---
title: Optimera prestanda för AzCopy v10 med Azure Storage | Microsoft Docs
description: Den här artikeln hjälper dig att optimera prestanda för AzCopy v10 med Azure Storage.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: b60fc4b1fc20c455c2c409f544a8af16f1dbf8d1
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509137"
---
# <a name="optimize-the-performance-of-azcopy-with-azure-storage"></a>Optimera prestanda för AzCopy med Azure Storage

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobar eller filer till eller från ett lagringskonto. Den här artikeln hjälper dig att optimera prestanda.

> [!NOTE]
> Om du letar efter innehåll som hjälper dig att komma igång med AzCopy kan du gå [till Kom igång med AzCopy](storage-use-azcopy-v10.md)

Du kan prestandatesta och sedan använda kommandon och miljövariabler för att hitta en optimal kompromiss mellan prestanda och resursförbrukning.

## <a name="run-benchmark-tests"></a>Köra benchmark-tester

Du kan köra ett prestandatest på specifika blobcontainrar eller filresurser för att visa allmän prestandastatistik och för att identifiera prestandaflaskhalsar. Du kan köra testet genom att ladda upp eller ladda ned genererade testdata. 

Använd följande kommando för att köra ett prestandatest.

**Syntax**

`azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'`

**Exempel**

```azcopy
azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

> [!TIP]
> Det här exemplet omsluter sökvägsargument med enkla citattecken (''). Använd enkla citattecken i alla kommandogränssnitt utom Windows-kommandogränssnittet (cmd.exe). Om du använder ett Windows-kommandogränssnitt (cmd.exe) omsluter du sökvägsargument med dubbla citattecken ("") i stället för enkla citattecken ('').

Det här kommandot kör ett prestandatest genom att ladda upp testdata till ett angivet mål. Testdata genereras i minnet, överförs till målet och tas sedan bort från målet när testet har slutförts. Du kan ange hur många filer som ska genereras och vilken storlek du vill att de ska vara med hjälp av valfria kommandoparametrar.

Om du föredrar att köra det här testet genom att ladda ned data anger du `mode` parametern till `download` . Detaljerade referensdokument finns i [azcopy benchmark.](storage-ref-azcopy-bench.md) 

## <a name="optimize-for-large-numbers-of-small-files"></a>Optimera för ett stort antal små filer

Dataflödet kan minska vid överföring av små filer, särskilt vid överföring av ett stort antal av dem. Maximera prestandan genom att minska storleken på varje jobb. För nedladdnings- och uppladdningsåtgärder ökar du samtidigheten, minskar loggaktiviteten och inaktiverar funktioner som medför höga prestandakostnader.

#### <a name="reduce-the-size-of-each-job"></a>Minska storleken på varje jobb

För att uppnå optimala prestanda bör du se till att varje jobb överför färre än 10 miljoner filer. Jobb som överför fler än 50 miljoner filer kan prestera dåligt eftersom spårningsmekanismen för AzCopy-jobb medför betydande omkostnader. Överväg att dela upp stora jobb i mindre jobb för att minska kostnaderna. 

Ett sätt att minska storleken på ett jobb är att begränsa antalet filer som påverkas av ett jobb. Du kan använda kommandoparametrar för att göra det. Ett jobb kan till exempel bara kopiera en delmängd kataloger med hjälp av `include path` parametern som en del av [kommandot azcopy copy.](storage-ref-azcopy-copy.md) 

Använd `include-pattern` parametern för att kopiera filer som har ett specifikt tillägg (till exempel: `*.pdf` ). I ett separat jobb använder du `exclude-pattern` parametern för att kopiera alla filer som inte har `*.pdf` tillägget . Exempel [finns i Ladda](storage-use-azcopy-blobs-upload.md#upload-specific-files) upp specifika filer och Ladda ned specifika [blobar.](storage-use-azcopy-blobs-download.md#download-specific-blobs)

När du har bestämt dig för att dela upp stora jobb i mindre kan du överväga att köra jobb på mer än en virtuell dator (VM).

#### <a name="increase-concurrency"></a>Öka samtidighet

Om du laddar upp eller ned filer använder du miljövariabeln för att öka antalet samtidiga begäranden `AZCOPY_CONCURRENCY_VALUE` som kan ske på datorn. Ange den här variabeln så hög som möjligt utan att påverka datorns prestanda. Mer information om den här variabeln finns i [avsnittet Öka antalet samtidiga begäranden](#increase-the-number-of-concurrent-requests) i den här artikeln.

Om du kopierar blobar mellan lagringskonton bör du överväga att ange värdet för `AZCOPY_CONCURRENCY_VALUE` miljövariabeln till ett värde som är större än `1000` . Du kan ange den här variabeln högt eftersom AzCopy använder server-till-server-API:er, så att data kopieras direkt mellan lagringsservrar och inte använder datorns bearbetningskraft.  

#### <a name="decrease-the-number-of-logs-generated"></a>Minska antalet genererade loggar

Du kan förbättra prestandan genom att minska antalet loggposter som AzCopy skapar när en åtgärd slutförs. Som standard loggar AzCopy all aktivitet relaterad till en åtgärd. För att uppnå optimala prestanda bör du överväga att `log-level` ange parametern för ditt kopierings-, synkroniserings- eller borttagningskommando till `ERROR` . På så sätt loggar AzCopy endast fel. Som standard är värdeloggnivån inställd på `INFO` . 

#### <a name="turn-off-length-checking"></a>Stänga av längdkontroll 

Om du laddar upp eller ned filer bör du överväga att ange för `--check-length` dina kopierings- och synkroniseringskommandon till `false` . Detta förhindrar att AzCopy verifierar längden på en fil efter en överföring. Som standard kontrollerar AzCopy längden för att säkerställa att käll- och målfilerna matchar när en överföring har slutförts. AzCopy utför den här kontrollen efter varje filöverföring. Den här kontrollen kan försämra prestanda när jobb överför ett stort antal små filer. 

#### <a name="turn-on-concurrent-local-scanning-linux"></a>Aktivera samtidig lokal genomsökning (Linux)

Filgenomsökningar på vissa Linux-system körs inte tillräckligt snabbt för att mätta alla parallella nätverksanslutningar. I dessa fall kan du ange `AZCOPY_CONCURRENT_SCAN` till `true` . 

## <a name="increase-the-number-of-concurrent-requests"></a>Öka antalet samtidiga begäranden

Du kan öka dataflödet genom att ange `AZCOPY_CONCURRENCY_VALUE` miljövariabeln . Den här variabeln anger antalet samtidiga begäranden som kan utföras.  

Om datorn har färre än 5 processorer anges värdet för den här variabeln till `32` . Annars är standardvärdet lika med 16 multiplicerat med antalet processorer. Det maximala standardvärdet för den här variabeln `3000` är , men du kan manuellt ange det här värdet högre eller lägre. 

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Använd för `azcopy env` att kontrollera det aktuella värdet för den här variabeln. Om värdet är tomt kan du läsa vilket värde som används genom att titta i början av en AzCopy-loggfil. Det valda värdet och orsaken till att det valdes rapporteras där.

Innan du anger den här variabeln rekommenderar vi att du kör ett benchmark-test. Benchmark-testprocessen rapporterar det rekommenderade samtidighetsvärdet. Om nätverksvillkoren och nyttolasten varierar kan du också ange den här variabeln till ordet `AUTO` i stället för till ett visst tal. Det gör att AzCopy alltid kör samma automatiska justeringsprocess som används i benchmark-tester.

## <a name="limit-the-throughput-data-rate"></a>Begränsa dataflödets hastighet

Du kan använda `cap-mbps` flaggan i dina kommandon för att skapa ett tak för dataflödeshastigheten. Följande kommando återupptar till exempel ett jobb och kapslar in dataflödet till `10` megabit (Mb) per sekund. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

## <a name="optimize-memory-use"></a>Optimera minnesanvändning

Ange miljövariabeln för att ange den maximala mängden systemminne som du vill att AzCopy ska använda för `AZCOPY_BUFFER_GB` buffring vid nedladdning och uppladdning av filer. Uttrycka det här värdet i gigabyte (GB).

| Operativsystem | Kommando  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

> [!NOTE]
> Jobbspårning medför alltid ytterligare omkostnader i minnesanvändningen. Mängden varierar beroende på antalet överföringar i ett jobb. Buffertar är den största komponenten i minnesanvändningen. Du kan hjälpa till att kontrollera omkostnaderna genom att använda för att ungefär uppfylla dina krav, men det finns ingen tillgänglig flagga för att strikt `AZCOPY_BUFFER_GB` reglera den totala minnesanvändningen.

## <a name="optimize-file-synchronization"></a>Optimera filsynkronisering

[Synkroniseringskommandot](storage-ref-azcopy-sync.md) identifierar alla filer på målet och jämför sedan filnamn och senast ändrade tidsstämplar innan synkroniseringsåtgärden startas. Om du har ett stort antal filer kan du förbättra prestandan genom att eliminera den här direktbearbetningen. 

För att åstadkomma detta använder du [kommandot azcopy copy](storage-ref-azcopy-copy.md) i stället och anger `--overwrite` flaggan till `ifSourceNewer` . AzCopy jämför filer när de kopieras utan att utföra några direktgenomsökningar och jämförelser. Detta ger en prestandafördel i fall där det finns ett stort antal filer att jämföra.

Kommandot [azcopy copy](storage-ref-azcopy-copy.md) tar inte bort filer från målet, så om du vill ta bort filer på målet när de inte längre finns vid källan använder du [kommandot azcopy sync](storage-ref-azcopy-sync.md) med flaggan inställd på värdet eller `--delete-destination` `true` `prompt` .

## <a name="see-also"></a>Se även

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)