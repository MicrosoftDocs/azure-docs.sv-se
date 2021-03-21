---
title: Batch bearbetnings paket för tal behållare
titleSuffix: Azure Cognitive Services
description: Använd batch bearbetnings paketet för att skala tal container begär Anden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: aahi
ms.openlocfilehash: cc6bcef77ca1601b76468586aa6af202836f1438
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632000"
---
# <a name="batch-processing-kit-for-speech-containers"></a>Batch bearbetnings paket för tal behållare

Använd batch bearbetnings paketet för att komplettera och skala ut arbets belastningar på tal behållare. Verktyget är tillgängligt som en behållare och underlättar batch-avskriften för ett stort antal ljudfiler, över valfritt antal lokala och molnbaserade tal behållare slut punkter. 

:::image type="content" source="media/containers/general-diagram.png" alt-text="Ett diagram som visar ett exempel på ett arbets flöde för batch-kit-behållare.":::

Batch kit-behållaren är tillgänglig kostnads fritt på [GitHub](https://github.com/microsoft/batch-processing-kit) och   [Docker Hub](https://hub.docker.com/r/batchkit/speech-batch-kit/tags). Du [debiteras](speech-container-howto.md#billing) bara för de tal behållare du använder.

| Funktion  | Beskrivning  |
|---------|---------|
| Batch-ljudfil för fil distribution     | Skicka automatiskt ett stort antal filer till en lokal eller molnbaserad slut punkt för tal behållare. Filer kan finnas på alla POSIX-kompatibla volymer, inklusive nätverks fil system.       |
| Integrering av tal-SDK | Skicka vanliga flaggor till tal-SDK: n, inklusive: n-bästa Hypotheses, diarization, språk, maskering av svordomar.  |
|Körnings lägen     | Kör batch-klienten en gång, kontinuerligt i bakgrunden eller skapa HTTP-slutpunkter för ljudfiler.         |
| Feltolerans | Försök automatiskt igen och fortsätt avskriften utan att förlora förloppet och Skilj mellan vilka fel som kan och inte kan göras om. |
| Identifiering av slut punkts tillgänglighet | Om en slut punkt blir otillgänglig, fortsätter batch-klienten att skriva med andra behållare slut punkter. Efter att ha blivit tillgänglig igen börjar klienten automatiskt använda slut punkten.   |
| Snabb växling av slut punkt | Lägg till, ta bort eller ändra slut punkter för tal behållare under körning utan att avbryta batch-förloppet. Uppdateringarna är omedelbara. |
| Loggning i real tid | Real tids loggning av försök till förfrågningar, tidsstämpel och orsaker, med loggfiler för tal-SDK för varje ljudfil. |

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållar avbildningen med `docker pull`

Använd [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) -kommandot för att hämta den senaste batch kit-behållaren.

```bash
docker pull docker.io/batchkit/speech-batch-kit:latest
```

## <a name="endpoint-configuration"></a>Slut punkts konfiguration

Batch-klienten tar en yaml konfigurations fil som anger behållarens slut punkter för lokal. Följande exempel kan skrivas till `/mnt/my_nfs/config.yaml` , som används i exemplen nedan. 

```yaml
MyContainer1: 
  concurrency: 5 
  host: 192.168.0.100 
  port: 5000 
  rtf: 3 
MyContainer2: 
  concurrency: 5 
  host: BatchVM0.corp.redmond.microsoft.com 
  port: 5000 
  rtf: 2 
MyContainer3: 
  concurrency: 10 
  host: localhost 
  port: 6001 
  rtf: 4 
```

Det här yaml-exemplet anger tre tal behållare på tre värdar. Den första värden anges av en IPv4-adress, den andra körs på samma virtuella dator som batch-klienten och den tredje behållaren anges av DNS-värdnamnet för en annan virtuell dator. `concurrency`Värdet anger maximalt antal samtidiga fil avskrifter som kan köras på samma behållare. `rtf`Värdet (real tids faktor) är valfritt och kan användas för att justera prestanda.
 
Batch-klienten kan dynamiskt identifiera om en slut punkt blir otillgänglig (till exempel på grund av en behållares omstart eller nätverks problem) och när den blir tillgänglig igen. Avskrifts begär Anden skickas inte till behållare som inte är tillgängliga och klienten fortsätter att använda andra tillgängliga behållare. Du kan lägga till, ta bort eller redigera slut punkter när som helst utan att avbryta förloppet för batchen.


## <a name="run-the-batch-processing-container"></a>Kör bearbetnings behållaren för batchbearbetning
  
> [!NOTE] 
> * I det här exemplet används samma katalog ( `/my_nfs` ) för konfigurations filen och katalogerna indata, utdata och loggar. Du kan använda värdbaserade eller NFS-monterade kataloger för dessa mappar.
> * Om du kör-klienten med `–h` visas tillgängliga kommando rads parametrar och deras standardvärden. 
> * Bearbetnings behållaren för batchbearbetning stöds endast i Linux.

Använd Docker- `run` kommandot för att starta behållaren. Detta kommer att starta ett interaktivt gränssnitt i behållaren.

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs --entrypoint /bin/bash /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest
```

Så här kör du batch-klienten:  

```Docker
run-batch-client -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -file_log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```

Så här kör du batch-klienten och behållaren i ett enda kommando:

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest  -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -file_log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```


Klienten börjar köras. Om en ljudfil redan har tilldelats i en tidigare körning hoppar klienten automatiskt över filen. Filer skickas med ett automatiskt återförsök om tillfälliga fel inträffar, och du kan skilja mellan vilka fel som du vill att klienten ska försöka igen. Vid ett avskrifts fel kommer klienten att fortsätta avskriften och kan försöka igen utan att förlora förloppet.  

## <a name="run-modes"></a>Körnings lägen 

Batch bearbetnings paketet innehåller tre lägen med hjälp av- `--run-mode` parametern.

#### <a name="oneshot"></a>[Oneshot](#tab/oneshot)

`ONESHOT` läget beskrivar en enda batch med ljudfiler (från en inmatnings katalog och valfri fil lista) till en mapp för utdata.

:::image type="content" source="media/containers/batch-oneshot-mode.png" alt-text="Ett diagram som visar bearbetnings filen för batch-kit-behållaren i OneShot-läge.":::

1. Definiera de tal behållare slut punkter som batch-klienten ska använda i `config.yaml` filen. 
2. Placera ljudfiler för avskrift i en indatafil.  
3. Anropa behållaren i katalogen, som kommer att börja bearbeta filerna. Om ljud filen redan har tilldelats i en tidigare körning med samma utmatnings katalog (samma fil namn och kontroll summa) hoppar klienten över filen. 
4. Filerna skickas till behållaren slut punkter från steg 1.
5. Loggar och tal behållarens utdata returneras till den angivna utdatakatalogen. 

#### <a name="daemon"></a>[Program](#tab/daemon)

> [!TIP]
> Om flera filer läggs till i indatafilen samtidigt kan du förbättra prestandan genom att i stället lägga till dem med jämna mellanrum.

`DAEMON` läget beskrivar befintliga filer i en specifik mapp och beskrivar kontinuerligt nya ljudfiler när de läggs till.          

:::image type="content" source="media/containers/batch-daemon-mode.png" alt-text="Ett diagram som visar bearbetnings filen för batch-paket behållare i daemon-läge.":::

1. Definiera de tal behållare slut punkter som batch-klienten ska använda i `config.yaml` filen. 
2. Anropa behållaren i en indatamängds katalog. Batch-klienten börjar övervaka katalogen för inkommande filer. 
3. Konfigurera överföring av kontinuerlig ljud fil till indatafilen. Om ljud filen redan har tilldelats i en tidigare körning med samma utmatnings katalog (samma fil namn och kontroll summa) hoppar klienten över filen. 
4. När en fil skrivnings-eller POSIX-signal identifieras, utlöses behållaren för att svara.
5. Filerna skickas till behållaren slut punkter från steg 1.
6. Loggar och tal behållarens utdata returneras till den angivna utdatakatalogen. 

#### <a name="rest"></a>[REST](#tab/rest)

`REST` mode är ett API-serverinställningar som tillhandahåller en grundläggande uppsättning HTTP-slutpunkter för att skicka ljud filens batch, status kontroll och lång avsökning. Aktiverar även programmatisk användning med hjälp av en python-moduls tillägg eller importera som en undermodul.

:::image type="content" source="media/containers/batch-rest-api-mode.png" alt-text="Ett diagram som visar bearbetnings filen för batch-kit-behållaren i REST-läge.":::

1. Definiera de tal behållare slut punkter som batch-klienten ska använda i `config.yaml` filen. 
2. Skicka en begäran om HTTP-begäran till en av API-serverns slut punkter. 
        
    |Slutpunkt  |Beskrivning  |
    |---------|---------|
    |`/submit`     | Slut punkt för att skapa nya batch-begäranden.        |
    |`/status`     | Slut punkt för att kontrol lera status för en batch-begäran. Anslutningen fortsätter att vara öppen tills batchen har slutförts.       |
    |`/watch`     | Slut punkt för att använda HTTP lång avsökning tills batchen har slutförts.        |

3. Ljudfiler laddas upp från indatafilen. Om ljud filen redan har tilldelats i en tidigare körning med samma utmatnings katalog (samma fil namn och kontroll summa) hoppar klienten över filen. 
4. Om en begäran skickas till `/submit` slut punkten skickas filerna till behållaren slut punkter från steg 1.
5. Loggar och tal behållarens utdata returneras till den angivna utdatakatalogen. 

---

## <a name="logging"></a>Loggning

> [!NOTE]
> Batch-klienten kan skriva över *Kör. log* -filen med jämna mellanrum om den blir för stor.

Klienten skapar en *Kör. log* -fil i den katalog som anges av `-log_folder` argumentet i Docker- `run` kommandot. Loggarna fångas in på fel SÖKNINGs nivå som standard. Samma loggar skickas till `stdout/stderr` och filtreras beroende på `-file_log_level` `console_log_level` argumenten eller. Den här loggen krävs bara för fel sökning eller om du behöver skicka en spårning för support. Mappen loggning innehåller också tal-SDK-loggarna för varje ljudfil.

Den utgående katalogen som anges av `-output_folder` kommer att innehålla en *run_summary.jspå* en   fil, som regelbundet skrivs om var 30: e sekund eller när nya avskrifter är klara. Du kan använda den här filen för att kontrol lera förloppet när batchen fortsätter. Den kommer också att innehålla den slutliga körnings statistiken och den slutliga statusen för varje fil när batchen har slutförts. Batchen slutförs när processen har en ren avslut. 

## <a name="next-steps"></a>Nästa steg

* [Installera och köra behållare](speech-container-howto.md)
