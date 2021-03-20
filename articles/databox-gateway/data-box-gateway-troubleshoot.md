---
title: Använda Azure-portalen för att felsöka Azure Data Box Gateway | Microsoft Docs
description: Lär dig hur du felsöker problem på Azure Data Box Gateway. Du kan köra diagnostik, samla in information för support och använda loggar för att felsöka.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: troubleshooting
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: e5280805090a83245e287c0b15a1735ee41e934e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96583151"
---
# <a name="troubleshoot-your-azure-data-box-gateway-issues"></a>Felsöka problem med din Azure Data Box Gateway

Den här artikeln beskriver hur du felsöker problem i din Azure Data Box Gateway.
I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> * Kör diagnostik
> * Hämta supportpaket
> * Använd loggar för att felsöka

## <a name="run-diagnostics"></a>Kör diagnostik

Du kan köra de diagnostiska testerna för att diagnostisera och felsöka eventuella enhetsfel. Utför följande steg i det lokala webbgränssnittet på din enhet för att köra diagnostiska tester.

1. I det lokala webbgränssnittet går du till **Felsökning > Diagnostiska tester**. Välj det test du vill köra och klicka på **Kör test**. Tester för att diagnostisera möjliga problem med nätverk, enhet, webbproxy, tid eller molninställningar körs då. Du får ett meddelande om att enheten kör testerna.

    ![Klicka på lägg till användare](media/data-box-gateway-troubleshoot/run-diag-1.png)
 
2. När testerna har slutförts visas resultatet. Om testet inte kan genomföras visas en URL för rekommenderad åtgärd. Du kan klicka på URL:en om du vill visa den rekommenderade åtgärden. 
 
    ![Klicka på Lägg till användare 2](media/data-box-gateway-troubleshoot/run-diag-2.png)


## <a name="collect-support-package"></a>Hämta supportpaket

Ett loggpaket består av alla relevanta loggar som kan hjälpa Microsoft Support att felsöka eventuella enhetsproblem. Du kan generera ett loggpaket via det lokala webbgränssnittet. 

Utför följande steg för att hämta ett supportpaket. 

1. I det lokala webbgränssnittet går du till **Felsökning > Support**. Klicka på **Skapa supportpaket**. Systemet börjar samla in supportpaket. Paketinsamlingen kan ta flera minuter.

    ![Klicka på Lägg till användare 3](media/data-box-gateway-troubleshoot/collect-logs-1.png)
 
2. När du har skapat supportpaketet klickar du på **Ladda ned supportpaket**. Ett komprimerat paket har laddats ned till den sökväg som du har valt. Du kan packa upp paketet.

    ![Klicka på Lägg till användare 4](media/data-box-gateway-troubleshoot/collect-logs-2.png)

## <a name="use-logs-to-troubleshoot"></a>Använd loggar för att felsöka

Eventuella fel som uppstod under uppladdnings- och uppdateringsprocesserna ingår i respektive felfiler.

1. Om du vill visa felfilerna öppnar du resursen och klickar på den för att visa innehållet. 

      ![Klicka på Lägg till användare 5](media/data-box-gateway-troubleshoot/troubleshoot-logs-1.png)

2. Klicka på _mappen Microsoft Data Box Gateway_. Den här mappen innehåller två undermappar:

   - Ladda upp mappen som innehåller loggfiler för uppladdningsfelen.
   - Uppdatera mappen för fel som uppstått under uppdateringen.

     Här är en exempelloggfil för uppdatering.

     ```
     <root container="brownbag1" machine="VM15BS020663" timestamp="07/18/2018 00:11:10" />
     <file item="test.txt" local="False" remote="True" error="16001" />
     <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
     ``` 

3. När du ser ett fel i den här filen (markerat i exemplet), noterar du felkoden, vilken i det här fallet är 16001. Leta upp beskrivningen av den här felkoden mot följande felkodsreferens.

    |     Felkod     |     Undantagsnamn                                         |     Felbeskrivning                                                                                                                                                                                                                     |
    |--------------------|------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    |    100             |    ERROR_CONTAINER_OR_SHARE_NAME_LENGTH                    |    Namnet på containern eller resursen måste innehålla mellan 3 och 63 tecken.                                                                                                                                                                     |
    |    101             |    ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH        |    Namnet på containern eller resursen måste bestå av endast bokstäver, siffror eller bindestreck.                                                                                                                                                       |
    |    102             |    ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH             |    Namnet på containern eller resursen måste bestå av endast bokstäver, siffror eller bindestreck.                                                                                                                                                       |
    |    103             |    ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL               |    Namnet på blobben eller filen innehåller kontrolltecken som inte stöds.                                                                                                                                                                       |
    |    104             |    ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL               |    Namnet på blobben eller filen innehåller tecken som inte stöds.                                                                                                                                                                                   |
    |    105             |    ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT                   |    Namnet på blobben eller filen innehåller för många segment (varje segment avgränsas med ett snedstreck-/).                                                                                                                                              |
    |    106             |    ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH                |    Namnet på blobben eller filen är för långt.                                                                                                                                                                                                     |
    |    107             |    ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH                |    Ett av segmenten i blobb- eller filnamnet är för långt.                                                                                                                                                                            |
    |    108             |    ERROR_BLOB_OR_FILE_SIZE_LIMIT                           |    Filstorleken överskrider den maximala filstorleken för uppladdning.                                                                                                                                                                              |
    |    109             |    ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT                       |    Blobben eller filen är felaktigt justerad.                                                                                                                                                                                               |
    |    110             |    ERROR_NAME_NOT_VALID_UNICODE                            |    Det Unicode-kodade fil- eller blobbnamnet är inte giltigt.                                                                                                                                                                                  |
    |    111             |    ERROR_RESERVED_NAME_NOT_ALLOWED                         |    Namnet eller prefixet för filen eller blobben är ett reserverat namn som inte stöds (till exempel COM1).                                                                                                                             |
    |    2000            |    ERROR_ETAG_MISMATCH                                     |    En etag-felmatchning indikerar att det finns en konflikt mellan en blockblob i molnet och på enheten. Om du vill lösa konflikten kan du ta bort en av dessa filer – antingen versionen i molnet eller versionen på enheten.    |
    |    2001            |    ERROR_UNEXPECTED_FINALIZE_FAILURE                       |    Ett oväntat fel uppstod vid bearbetning av en fil när filen laddades upp.    Om du får det här felmeddelandet och felet kvarstår efter 24 timmar kontaktar du supporten.                                                      |
    |    2002            |    ERROR_ALREADY_OPEN                                      |    Filen är redan är öppen i en annan process och kan inte laddas upp förrän referensen stängts.                                                                                                                                       |
    |    2003            |    ERROR_UNABLE_TO_OPEN                                    |    Det gick inte att öppna filen för uppladdning. Om du ser det här felmeddelandet kontaktar du Microsoft Support.                                                                                                                                                |
    |    2004            |    ERROR_UNABLE_TO_CONNECT                                 |    Det gick inte att ansluta till containern för att ladda upp data till den.                                                                                                                                                                             |
    |    2005            |    ERROR_INVALID_CLOUD_CREDENTIALS                         |    Det gick inte att ansluta till containern eftersom kontobehörigheterna är felaktiga eller inaktuella. Kontrollera din åtkomst.                                                                                                               |
    |    2006            |    ERROR_CLOUD_ACCOUNT_DISABLED                            |    Det gick inte att ladda upp data till kontot eftersom kontot eller resursen har inaktiverats.                                                                                                                                                            |
    |    2007            |    ERROR_CLOUD_ACCOUNT_PERMISSIONS                         |    Det gick inte att ansluta till containern eftersom kontobehörigheterna är felaktiga eller inaktuella. Kontrollera din åtkomst.                                                                                                               |
    |    2008            |    ERROR_CLOUD_CONTAINER_SIZE_LIMIT_REACHED                |    Det gick inte att lägga till nya data eftersom containern är full. Kontrollera Azure specifikationerna för vilka typer av containerstorlekar som stöds. Azure File stöder till exempel bara en maximal filstorlek på 5 TB.                                     |
    |    2009            |    ERROR_CLOUD_CONTAINER_MISSING                |     Det gick inte att ladda upp data eftersom den container som är kopplad till resursen inte finns.                                     |    
    |    2997            |    ERROR_ITEM_CANCELED                                     |    Det uppstod ett oväntat fel. Det här är ett tillfälligt fel som kommer att åtgärdas automatiskt.                                                                           |
    |    2998            |    ERROR_UNMAPPED_FAILURE                                  |    Det uppstod ett oväntat fel. Felet kan lösas av sig självt men om det finns kvar i mer än 24 timmar kontaktar du Microsoft Support.                                                                                                     |
    |    16000           |    RefreshException                                        |    Det gick inte att hämta den här filen.                                                                                                                                                                                                        |
    |    16001           |    RefreshAlreadyExistsException                           |    Det gick inte att hämta den här filen eftersom den redan finns i det lokala systemet.                                                                                                                                                         |
    |    16002           |    RefreshWorkNeededException                              |    Det gick inte att uppdatera den här filen eftersom den inte är fullständigt uppladdad.                                                                                                                                                                          | 


## <a name="next-steps"></a>Nästa steg

- Läs mer om [kända problem i den här versionen](data-box-gateway-release-notes.md).
