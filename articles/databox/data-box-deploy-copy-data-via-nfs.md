---
title: Självstudie för att kopiera data till Azure Data Box via NFS | Microsoft Docs
description: I den här självstudien får du lära dig hur du ansluter till och kopierar data från värddatorn till Azure Data Box med hjälp av NFS med det lokala webb gränssnittet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: d53a619dc6ca5fb0f43f6097664f50bf22943928
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97678887"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-nfs"></a>Självstudie: kopiera data till Azure Data Box via NFS

I den här självstudien beskrivs hur du ansluter till och kopierar data från värddatorn med det lokala webbgränssnittet.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Krav
> * Ansluta till Data Box
> * Kopiera data till Data Box

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har slutfört [Självstudie: Konfigurera Azure Data Box](data-box-deploy-set-up.md).
2. Du har fått din Data Box och att orderstatusen i portalen är **Levererad**.
3. Du har en värddator som har de data du vill kopiera över till Data Box. Värddatorn måste
    - Köra ett [operativsystem som stöds](data-box-system-requirements.md).
    - Vara ansluten till en höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en anslutning på 10 GbE inte är tillgänglig kan en datalänk på 1 GbE användas, men i så fall påverkas kopieringshastigheten. 

## <a name="connect-to-data-box"></a>Ansluta till Data Box

Utifrån det lagringskontot som väljs skapar Data Box upp till:
- Tre resurser för varje associerat lagringskonto för GPv1 och GPv2.
- En resurs för premiumlagring. 
- En resurs för bloblagringskonto. 

Under blockblob- och sidblobresurser är entiteter på första nivån containrar och entiteter på andra nivån är blobar. Under resurser för Azure Files är entiteter på första nivån resurser och entiteter på andra nivån är filer.

I följande tabell visas UNC-sökvägen till filresurser på din Data Box och Azure Storage-sökvägens URL som data har överförts till. URL:en till den sista Azure Storage-sökvägen kan härledas från sökvägen till UNC-resursen.
 
| Azure Storage-typ| Data Box-resurser                                       |
|-------------------|--------------------------------------------------------------------------------|
| Azure Block blobs | <li>UNC-sökväg till resurser: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-sidblobar  | <li>UNC-sökväg till resurser: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-sökväg till resurser: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Om du använder en Linux-värddator utför du stegen nedan för att konfigurera Data Box att tillåta åtkomst till NFS-klienter.

1. Ange IP-adresserna för de tillåtna klienterna som har åtkomst till resursen. I det lokala webbgränssnittet går du till sidan **Anslut och kopiera**. Under **NFS-inställningar** klickar du på **NFS-klientåtkomst**. 

    ![Konfigurera åtkomst till NFS-klienter](media/data-box-deploy-copy-data/nfs-client-access-1.png)

2. Ange NFS-klientens IP-adress och klicka på **Add**. Du kan konfigurera åtkomst för flera NFS genom att upprepa det här steget. Klicka på **OK**.

    ![Konfigurera IP-adressen för en NFS-klient](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Kontrollera att Linux-värddatorn har en NFS-klient av en [version som stöds](data-box-system-requirements.md) installerad. Använd den specifika versionen för din Linux-distribution. 

3. När NFS-klienten har installerats använder du följande kommando för att montera NFS-resursen på Data Box-enheten:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    I följande exempel visas hur du ansluter via NFS till en Data Box-resurs. Data Box-enhetens IP-adress är `10.161.23.130`, resursen `Mystoracct_Blob` är monterad på ubuntuVM, och monteringspunkten är `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    För Mac-klienter måste du lägga till ytterligare ett alternativ på följande sätt: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Skapa alltid en mapp för de filer som du vill kopiera under resursen och kopiera sedan filerna till den mappen**. Mappen som skapas under blockblob- och sidblobresurser representerar en container som data laddas upp som blobar till. Du kan inte kopiera filer direkt till *root*-mappen i lagringskontot.

## <a name="copy-data-to-data-box"></a>Kopiera data till Data Box

När du är ansluten till Data Box-resurser är nästa steg att kopiera data. Granska följande innan du kopierar data:

* Se till att du kopierar data till resurser som motsvarar lämplig dataformat. Kopiera exempelvis blockblobdata till resursen för blockblobobjekt. Kopiera virtuella hårddiskar till sidblobar. Om dataformatet inte matchar lämplig resurstyp misslyckas datauppladdningen till Azure i ett senare skede.
*  När du kopierar data måste du se till att data storleken överensstämmer med storleks begränsningarna som beskrivs i [storleks gränserna för Azure Storage-kontot](data-box-limits.md#azure-storage-account-size-limits).
* Om data som laddas upp av Data Box samtidigt överförs av andra program utanför Data Box, kan detta resultera i att uppladdningsjobbet misslyckas samt att data skadas.
* Vi rekommenderar att du inte använda både SMB och NFS samtidigt eller kopierar samma data till samma mål i slutet på Azure. I sådana fall kan slutresultatet inte fastställas.
* **Skapa alltid en mapp för de filer som du vill kopiera under resursen och kopiera sedan filerna till den mappen**. Mappen som skapas under blockblob- och sidblobresurser representerar en container som data laddas upp som blobar till. Du kan inte kopiera filer direkt till *root*-mappen i lagringskontot.
* Om du matar in Skift läges känslig katalog och fil namn från en NFS-resurs till NFS på Data Box-enhet:
  * Ärendet bevaras i namnet.
  * Filerna är inte Skift läges känsliga.

    Om du t. ex. kopierar `SampleFile.txt` och så `Samplefile.Txt` bevaras ärendet i namnet när det kopieras till data Box-enhet, men den andra filen kommer att skriva över den första som dessa betraktas som en fil.

> [!IMPORTANT]
> Se till att du behåller en kopia av dina källdata tills du kan bekräfta att Data Box-enheten har överfört dina data till Azure Storage.

Om du använder en Linux-värddator använder du en kopieringsverktyg som liknar Robocopy. Några av de alternativ som är tillgängliga i Linux är [`rsync`](https://rsync.samba.org/) , [FreeFileSync](https://www.freefilesync.org/), [dem samtidigt](https://www.cis.upenn.edu/~bcpierce/unison/)eller [ultracopier](https://ultracopier.first-world.info/).  

Kommandot `cp` är ett av de bästa alternativen för att kopiera en katalog. Mer information om användningen finns på [cp man-sidorna](http://man7.org/linux/man-pages/man1/cp.1.html).

`rsync`Följ dessa rikt linjer om du använder alternativ för en flertrådad kopia:

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

Ett meddelande visas om det uppstår fel under kopieringen.

![Ladda ned och visa fel från Anslut och kopiera](media/data-box-deploy-copy-data/view-errors-1.png)

Välj **Ladda ned lista med ärenden**.

![Hämta problem listan för ett kopierings fel](media/data-box-deploy-copy-data/view-errors-2.png)

Öppna listan för att visa information om felet och välj lösnings-URL:en för att visa den rekommenderade lösningen.

![Problem i en lista med kopierings fel problem](media/data-box-deploy-copy-data/view-errors-3.png)

Mer information finns i [Visa felloggar under datakopiering till Data Box](data-box-logs.md#view-error-log-during-data-copy). En detaljerad lista över fel i samband med datakopieringen finns i [Felsöka problem med Data Box](data-box-troubleshoot.md).

För att säkerställa dataintegriteten beräknas kontrollsumman infogat när data kopieras. När kopieringen är klar kontrollerar du det använda utrymmet och det lediga utrymmet på enheten.

   ![Kontrollera ledigt och använt utrymme på instrumentpanelen](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
>
> * Krav
> * Ansluta till Data Box
> * Kopiera data till Data Box

Gå vidare till nästa självstudie och lär dig hur du skickar tillbaka din Data Box-enhet till Microsoft.

> [!div class="nextstepaction"]
> [Skicka din Azure Data Box till Microsoft](./data-box-deploy-picked-up.md)
