---
title: Självstudie för att kopiera data från Azure Data Box via NFS | Microsoft Docs
description: Lär dig hur du kopierar data från din Azure Data Box via NFS
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 64bb5e94c4b18626d1f85d7e61252aae74202eb9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680625"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-nfs"></a>Självstudie: kopiera data från Azure Data Box via NFS

I den här självstudien beskrivs hur du ansluter till och kopierar data från det lokala webb gränssnittet för din Data Box-enhet till en lokal data server via NFS. Data på din Data Box-enhet exporteras från ditt Azure Storage-konto.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Krav
> * Ansluta till Data Box
> * Kopiera data från Data Box-enhet

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har beställt Azure Data Box.
    - Information om importbeställningar finns i [Självstudie: Beställ Azure Data Box](data-box-deploy-ordered.md).
    - Information om exportbeställningar finns i [Självstudie: Beställ Azure Data Box](data-box-deploy-export-ordered.md).
2. Du har fått din Data Box-enhet och orderstatusen i portalen är **Levererad**.
3. Du har en värddator som du vill kopiera data från Data Box-enhet till. Värddatorn måste
   * Köra ett [operativsystem som stöds](data-box-system-requirements.md).
   * Vara ansluten till en höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en 10 GbE anslutning inte är tillgänglig kan en 1 GbE datalänk användas, men kopieringshastigheten påverkas.

## <a name="connect-to-data-box"></a>Ansluta till Data Box

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Om du använder en Linux-värddator utför du stegen nedan för att konfigurera Data Box att tillåta åtkomst till NFS-klienter. Data Box-enhet kan ansluta upp till fem NFS-klienter i taget.

1. Ange IP-adresserna för de tillåtna klienter som har åtkomst till resursen:

    1.  Gå till sidan **Anslut och kopiera** i det lokala webb gränssnittet. Under **NFS-inställningar** klickar du på **NFS-klientåtkomst**. 

        ![Öppna NFS-klient åtkomst](media/data-box-deploy-export-copy-data/nfs-client-access-1.png)

    1. Om du vill lägga till en NFS-klient anger du klientens IP-adress och klickar på **Lägg till**. Data Box-enhet kan ansluta upp till fem NFS-klienter i taget. Klicka på **OK** när du är klar.

         ![Lägg till en NFS-klient](media/data-box-deploy-export-copy-data/nfs-client-access-2.png)

2. Kontrollera att Linux-värddatorn har en NFS-klient av en [version som stöds](data-box-system-requirements.md) installerad. Använd den specifika versionen för din Linux-distribution. 

3. När NFS-klienten har installerats använder du följande kommando för att montera NFS-resursen på Data Box-enheten:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    I följande exempel visas hur du ansluter via NFS till en Data Box-resurs. Data Box-enhetens IP-adress är `10.161.23.130`, resursen `Mystoracct_Blob` är monterad på ubuntuVM, och monteringspunkten är `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    För Mac-klienter måste du lägga till ytterligare ett alternativ på följande sätt: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Skapa alltid en mapp för de filer som du vill kopiera under resursen och kopiera sedan filerna till den mappen**. Mappen som skapas under blockblob- och sidblobresurser representerar en container som data laddas upp som blobar till. Du kan inte kopiera filer direkt till *root*-mappen i lagringskontot.

## <a name="copy-data-from-data-box"></a>Kopiera data från Data Box-enhet

När du är ansluten till Data Box-resurser är nästa steg att kopiera data.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]

 Nu kan du börja kopiera data. Om du använder en Linux-värddator använder du en kopieringsverktyg som liknar Robocopy. Några av de alternativ som är tillgängliga i Linux är [`rsync`](https://rsync.samba.org/) , [FreeFileSync](https://www.freefilesync.org/), [dem samtidigt](https://www.cis.upenn.edu/~bcpierce/unison/)eller [ultracopier](https://ultracopier.first-world.info/).  

Kommandot `cp` är ett av de bästa alternativen för att kopiera en katalog. Mer information om användningen finns på [cp man-sidorna](http://man7.org/linux/man-pages/man1/cp.1.html).

`rsync`Följ dessa rikt linjer om du använder alternativet för en flertrådad kopia:

* Installera **CIFS Utils**- eller **NFS Utils**-paketet, beroende på vilket filsystem din Linux-klient använder.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

* Installation `rsync` och **parallell** (varierar beroende på den distribuerade Linux-versionen).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

* Skapa en monteringspunkt.

    `sudo mkdir /mnt/databox`

* Montera volymen.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

* Spegla mappkatalogstrukturen.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

* Kopiera filerna.

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     där j anger antal parallelliseringar, X = antal parallella kopior

     Vi rekommenderar att du börjar med 16 parallella kopior och öka antalet trådar beroende på tillgängliga resurser.

> [!IMPORTANT]
> Följande Linux-filtyper stöds inte: symboliska länkar, paketfiler, blockera filer, Sockets och pipes. Dessa filtyper resulterar i problem under **Förbered för att skicka** steget.

När kopieringen är klar går du till **Instrumentpanel** och kontrollerar använt och ledigt utrymme på enheten.

Nu kan du gå vidare och leverera Data Box-enheten till Microsoft.

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
>
> * Krav
> * Ansluta till Data Box
> * Kopiera data från Data Box-enhet

Gå vidare till nästa självstudie och lär dig hur du skickar tillbaka din Data Box-enhet till Microsoft.

> [!div class="nextstepaction"]
> [Skicka din Azure Data Box till Microsoft](./data-box-deploy-export-picked-up.md)
