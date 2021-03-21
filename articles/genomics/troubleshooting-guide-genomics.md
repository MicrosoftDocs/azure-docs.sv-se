---
title: Felsökningsguide
titleSuffix: Microsoft Genomics
description: Lär dig mer om fel söknings strategier för att använda Microsoft Genomics, inklusive fel meddelanden och hur du löser dem.
keywords: fel sökning, fel, fel sökning
services: genomics
author: ruchir
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.openlocfilehash: c508c10d619cde1a16d89b446c5cfd1a3ce81daf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023831"
---
# <a name="troubleshooting-guide"></a>Felsökningsguide

Här följer några fel söknings tips för några av de vanliga problem som kan uppstå när du använder tjänsten Microsoft Genomics, MSGEN.

 Vanliga frågor och svar som inte är relaterade till fel sökning finns i [vanliga frågor](frequently-asked-questions-genomics.md).
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>Steg 1: hitta felkoder kopplade till arbets flödet

Du kan hitta de fel meddelanden som är kopplade till arbets flödet genom att:

1. Använda kommando raden och skriva i  `msgen status`
2. Undersöka innehållet i standardoutput.txt.

### <a name="1-using-the-command-line-msgen-status"></a>1. använda kommando raden `msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Det finns tre obligatoriska argument:

* URL – bas-URI för API: et
* NYCKEL – åtkomst nyckeln för ditt genomik-konto
    * Om du vill hitta din URL och nyckel går du till Azure Portal och öppnar din Microsoft Genomics konto-sida. Under **hanterings** rubriken väljer du **åtkomst nycklar**. Där hittar du både API-URL: en och dina åtkomst nycklar.

  
* ID – arbets flödes-ID
    * För att hitta din arbets flödes-ID-typ i `msgen list` kommandot. Om du antar att konfigurations filen innehåller URL: en och dina åtkomst nycklar och finns på samma plats som din msgen-exe, ser kommandot ut så här: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        Utdata från det här kommandot kommer att se ut så här:
        
        ```bash
            Microsoft Genomics command-line client v0.7.4
                Copyright (c) 2018 Microsoft. All rights reserved.
                
                Workflow List
                -------------
                Total Count  : 1
                
                Workflow ID     : 10001
                Status          : Completed successfully
                Message         :
                Process         : snapgatk-20180730_1
                Description     :
                Created Date    : Mon, 27 Aug 2018 20:27:24 GMT
                End Date        : Mon, 27 Aug 2018 20:55:12 GMT
                Wall Clock Time : 0h 27m 48s
                Bases Processed : 1,348,613,600 (1 GBase)
        ```

  > [!NOTE]
  >  Du kan också inkludera sökvägen till konfigurations filen i stället för att direkt ange URL: en och nyckeln. Om du inkluderar dessa argument i kommando raden och i konfigurations filen har kommando rads argumenten företräde.  

För arbets flödes-ID 1001, och config.txt fil som placerats i samma sökväg som den körbara filen msgen, ser kommandot ut så här:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2. granska innehållet i standardoutput.txt 
Leta upp behållaren utdata för arbets flödet i fråga. MSGEN skapar en   `[workflowfilename].logs.zip` mapp efter varje arbets flödes körning. Zippa upp mappen för att visa dess innehåll:

* outputFileList.txt en lista med utdatafiler som producerats under arbets flödet
* standarderror.txt-filen är tom.
* standardoutput.txt-loggar alla status meddelanden på den översta nivån inklusive fel som uppstod när arbets flödet kördes.
* ANVÄNDAS-loggfiler – alla andra filer i `logs` mappen

För fel sökning, granska innehållet i standardoutput.txt och notera eventuella fel meddelanden som visas.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>Steg 2: prova rekommenderade steg för vanliga fel

Det här avsnittet beskriver kortfattat vanliga fel utdata av Microsoft Genomics service (msgen) och de strategier du kan använda för att lösa dem. 

Microsoft Genomicss tjänsten (msgen) kan utlösa följande två typer av fel:

1. Interna tjänst fel: fel som är interna för tjänsten och som kanske inte kan lösas genom att åtgärda parametrar eller indatafiler. Ibland kan det lösa felen genom att skicka om arbets flödet.
2. Indatafel: fel som kan lösas med hjälp av rätt argument eller korrigering av fil format.

### <a name="1-internal-service-errors"></a>1. interna tjänst fel

Det går inte att vidta ett internt tjänst fel. Du kan skicka in arbets flödet igen, men om det inte fungerar kontaktar du Microsoft Genomics support

| Felmeddelande                                                                                                                            | Rekommenderade steg för felsökning                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ett internt fel har inträffat. Försök att skicka arbets flödet igen. Om du ser det här felet igen kontaktar du Microsoft Genomics support för hjälp | Skicka arbets flödet igen. Kontakta Microsoft Genomics support för hjälp om problemet kvarstår genom att skapa ett support [ärende](file-support-ticket-genomics.md ). |

### <a name="2-input-errors"></a>2. indatafel

De här felen är användare åtgärds bara. Baserat på filtypen, och felkod, Microsoft Genomics tjänsten utvärderar felkoder som är distinkta. Följ de rekommenderade fel söknings stegen som anges nedan.

| Typ av fil | Felkod | Felmeddelande                                                                           | Rekommenderade steg för felsökning                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Valfri          | 701        | Läsning [beredskap] har [numberOfBases] basers, men gränsen är [maxReadLength]           | Den vanligaste orsaken till det här felet är att filen skadas, vilket leder till sammanfogning av två läsningar. Kontrol lera dina indatafiler. |
| BAM          | 200        |   Det gick inte att läsa filen [yourFileName].                                                                                       | Kontrol lera formatet på BAM-filen. Skicka arbets flödet igen med en korrekt formaterad fil.                                                                           |
| BAM          | 201        |  Det gick inte att läsa BAM-filen [File_name].                                                                                      |Kontrol lera formatet på BAM-filen.  Skicka arbets flödet med en korrekt formaterad fil.                                                                            |
| BAM          | 202        | Det gick inte att läsa BAM-filen [File_name]. Filen är för liten och saknar sidhuvud.                                                                                        | Kontrol lera formatet på BAM-filen.  Skicka arbets flödet med en korrekt formaterad fil.                                                                            |
| BAM          | 203        |   Det gick inte att läsa BAM-filen [File_name]. Fil huvudet var skadat.                                                                                      |Kontrol lera formatet på BAM-filen.  Skicka arbets flödet med en korrekt formaterad fil.                                                                           |
| BAM          | 204        |    Det gick inte att läsa BAM-filen [File_name]. Fil huvudet var skadat.                                                                                     | Kontrol lera formatet på BAM-filen.  Skicka arbets flödet med en korrekt formaterad fil.                                                                           |
| BAM          | 205        |    Det gick inte att läsa BAM-filen [File_name]. Fil huvudet var skadat.                                                                                     | Kontrol lera formatet på BAM-filen.  Skicka arbets flödet med en korrekt formaterad fil.                                                                            |
| BAM          | 206        |   Det gick inte att läsa BAM-filen [File_name]. Fil huvudet var skadat.                                                                                      | Kontrol lera formatet på BAM-filen.  Skicka arbets flödet med en korrekt formaterad fil.                                                                            |
| BAM          | 207        |  Det gick inte att läsa BAM-filen [File_name]. Filen trunkerades nära offset [offset].                                                                                       | Kontrol lera formatet på BAM-filen.  Skicka arbets flödet med en korrekt formaterad fil.                                                                            |
| BAM          | 208        |   Ogiltig BAM-fil. [Read_Id] har ingen sekvens i filen [File_name].                                                                                      | Kontrol lera formatet på BAM-filen.  Skicka arbets flödet med en korrekt formaterad fil.                                                                             |
| FASTQ        | 300        |  Det gick inte att läsa FASTQ-filen. [File_name] slutar inte med en rad matning.                                                                                     | Korrigera formatet för FASTQ-filen och skicka arbets flödet igen.                                                                           |
| FASTQ        | 301        |   Det gick inte att läsa FASTQ-filen [File_name]. FASTQ-posten är större än buffertstorleken vid förskjutning: [_offset]                                                                                      | Korrigera formatet för FASTQ-filen och skicka arbets flödet igen.                                                                         |
| FASTQ        | 302        |     FASTQ-syntaxfel. Filen [File_name] har en tom rad.                                                                                    | Korrigera formatet för FASTQ-filen och skicka arbets flödet igen.                                                                         |
| FASTQ        | 303        |       FASTQ-syntaxfel. Filen [File_name] har ett ogiltigt start värde vid förskjutning: [_offset], typ av rad: [line_type], Character: [_char]                                                                                  | Korrigera formatet för FASTQ-filen och skicka arbets flödet igen.                                                                         |
| FASTQ        | 304      |  FASTQ syntaxfel vid beredskap [_ReadID].  Den första läsningen av batch har inte kunnat sluta i/1 i filen [File_name]                                                                                       | Korrigera formatet för FASTQ-filen och skicka arbets flödet igen.                                                                         |
| FASTQ        | 305        |  FASTQ syntaxfel vid beredskap [_readID]. Den andra läsningen av batch har inte kunnat sluta i/2 i filen [File_name]                                                                                      | Korrigera formatet för FASTQ-filen och skicka arbets flödet igen.                                                                          |
| FASTQ        | 306        |  FASTQ syntaxfel vid beredskap [_ReadID]. Den första läsningen av paret har inget ID som slutar i/1 i filen [File_name]                                                                                       | Korrigera formatet för FASTQ-filen och skicka arbets flödet igen.                                                                          |
| FASTQ        | 307        |   FASTQ syntaxfel vid beredskap [_ReadID]. Det går inte att sluta med/1 eller/2. Det går inte att använda filen [File_name] som en kopplad FASTQ-fil.                                                                                      |Korrigera formatet för FASTQ-filen och skicka arbets flödet igen.                                                                          |
| FASTQ        | 308        |  FASTQ Read-fel. Läsningar av båda ändar har svarat på olika sätt. Valde du rätt FASTQ-filer?                                                                                       | Korrigera formatet för FASTQ-filen och skicka arbets flödet igen.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>Steg 3: kontakta Microsoft Genomics support

Om du fortsätter att ha jobb haverier eller om du har andra frågor kan du kontakta Microsoft Genomics support från Azure Portal. Mer information om hur du skickar en support förfrågan finns [här](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du felsöker och löser vanliga problem med tjänsten Microsoft Genomics. Mer information och vanliga frågor och svar finns i [vanliga frågor](frequently-asked-questions-genomics.md). 
