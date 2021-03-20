---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: 05673885336dbfb9a70843bd0ccc4e700091ad7e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67188012"
---
|     Felkod     |      Felbeskrivning     |
|--------------------|--------------------------|
|    100             | Namnet på containern eller resursen måste innehålla mellan 3 och 63 tecken.|
|    101             | Namnet på containern eller resursen måste bestå av endast bokstäver, siffror eller bindestreck.|
|    102             | Namnet på containern eller resursen måste bestå av endast bokstäver, siffror eller bindestreck.|
|    103             | Namnet på blobben eller filen innehåller kontrolltecken som inte stöds.|
|    104             | Namnet på blobben eller filen innehåller tecken som inte stöds.|
|    105             | Namnet på blobben eller filen innehåller för många segment (varje segment avgränsas med ett snedstreck-/).|
|    106             | Namnet på blobben eller filen är för långt.|
|    107             | Ett av segmenten i blobb- eller filnamnet är för långt. |
|    108             | Filstorleken överskrider den maximala filstorleken för uppladdning.    |
|    109             | Blobben eller filen är felaktigt justerad.  |
|    110             | Det Unicode-kodade fil- eller blobbnamnet är inte giltigt.|
|    111             | Namnet eller prefixet för filen eller blobben är ett reserverat namn som inte stöds (till exempel COM1).|
|    2000            | En etag-felmatchning indikerar att det finns en konflikt mellan en blockblob i molnet och på enheten. Om du vill lösa konflikten kan du ta bort en av dessa filer – antingen versionen i molnet eller versionen på enheten.    |
|    2001            | Ett oväntat fel uppstod vid bearbetning av en fil när filen laddades upp.    Om du får det här felmeddelandet och felet kvarstår efter 24 timmar kontaktar du supporten. |
|    2002            | Filen är redan är öppen i en annan process och kan inte laddas upp förrän referensen stängts.|
|    2003            | Det gick inte att öppna filen för uppladdning. Om du ser det här felmeddelandet kontaktar du Microsoft Support.|
|    2004            | Det gick inte att ansluta till containern för att ladda upp data till den.|
|    2005            | Det gick inte att ansluta till containern eftersom kontobehörigheterna är felaktiga eller inaktuella. Kontrollera din åtkomst.|
|    2006            | Det gick inte att ladda upp data till kontot eftersom kontot eller resursen har inaktiverats.|
|    2007            | Det gick inte att ansluta till containern eftersom kontobehörigheterna är felaktiga eller inaktuella. Kontrollera din åtkomst.|
|    2008            | Det gick inte att lägga till nya data eftersom containern är full. Kontrollera Azure specifikationerna för vilka typer av containerstorlekar som stöds. Azure File stöder till exempel bara en maximal filstorlek på 5 TB.|
|    2009            | Det gick inte att ladda upp data eftersom den container som är kopplad till resursen inte finns.|    
|    2997            | Det uppstod ett oväntat fel. Det här är ett tillfälligt fel som kommer att åtgärdas automatiskt.|
|    2998            | Det uppstod ett oväntat fel. Felet kan lösas av sig självt men om det finns kvar i mer än 24 timmar kontaktar du Microsoft Support.|
|    16000           | Det gick inte att hämta den här filen.|
|    16001           | Det gick inte att hämta den här filen eftersom den redan finns i det lokala systemet.|
|    16002           |Det gick inte att uppdatera den här filen eftersom den inte är fullständigt uppladdad.|

