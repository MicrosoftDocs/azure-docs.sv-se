---
title: Självstudie för att kopiera data till Azure Data Box Heavy via NFS | Microsoft Docs
description: I den här självstudien får du lära dig hur du ansluter till och kopierar data från värddatorn till Azure Data Box Heavy med hjälp av NFS med det lokala webb gränssnittet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: a8199039ea803d5919e168edccaaa03c18985725
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87921035"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-nfs"></a>Självstudie: kopiera data till Azure Data Box Heavy via NFS

I den här självstudien beskrivs hur du ansluter till och kopierar data från värddatorn med hjälp av det lokala webb gränssnittet till din Azure Data Box Heavy.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Krav
> * Ansluta till Data Box Heavy
> * Kopiera data till Data Box Heavy

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har slutfört [självstudien: konfigurera Azure Data Box Heavy](data-box-heavy-deploy-set-up.md).
2. Du har fått din Data Box Heavy och order statusen i portalen har **levererats**.
3. Du har en värddator som har de data som du vill kopiera över till Data Box Heavy. Värddatorn måste
    - Köra ett [operativsystem som stöds](data-box-heavy-system-requirements.md).
    - Vara ansluten till en höghastighetsnätverk. För snabbast kopieringshastighet kan två 40-GbE-anslutningar (en per nod) användas parallellt. Om du inte har någon tillgänglig 40-GbE-anslutning rekommenderar vi att du har minst två 10 GbE-anslutningar (en per nod). 

## <a name="connect-to-data-box-heavy"></a>Ansluta till Data Box Heavy

Baserat på det lagringskonto du valt skapar Data Box Heavy upp till:
- Tre resurser för varje associerat lagringskonto för GPv1 och GPv2.
- En resurs för premiumlagring.
- En resurs för bloblagringskonto.

Dessa resurser skapas på båda noderna på enheten.

Under blockblob- och sidblobsresurser:
- Enheter på den första nivån är containrar.
- Enheter på den andra nivån är blobar.

Under resurser för Azure Files:
- Enheter på den första nivån är resurser.
- Enheter på den andra nivån är filer.

I följande tabell visas UNC-sökvägen till filresurser på din Data Box Heavy och Azure Storage-sökvägens URL som data har överförts till. URL:en till den sista Azure Storage-sökvägen kan härledas från sökvägen till UNC-resursen.
 
| Storage           | UNC-sökväg                                                                       |
|-------------------|--------------------------------------------------------------------------------|
| Azure Block blobs | <li>UNC-sökväg till resurser: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-sidblobar  | <li>UNC-sökväg till resurser: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-sökväg till resurser: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Om du använder en Linux-värddator utför du följande steg för att konfigurera enheten för att tillåta åtkomst till NFS-klienter.

1. Ange IP-adresserna för de tillåtna klienterna som har åtkomst till resursen. I det lokala webbgränssnittet går du till sidan **Anslut och kopiera**. Under **NFS-inställningar** klickar du på **NFS-klientåtkomst**. 

    ![Konfigurera NFS-klientåtkomst 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. Ange NFS-klientens IP-adress och klicka på **Add**. Du kan konfigurera åtkomst för flera NFS genom att upprepa det här steget. Klicka på **OK**.

    ![Konfigurera NFS-klientåtkomst 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Kontrollera att Linux-värddatorn har en NFS-klient av en [version som stöds](data-box-system-requirements.md) installerad. Använd den specifika versionen för din Linux-distribution. 

3. När NFS-klienten har installerats använder du följande kommando för att montera NFS-resursen på Data Box-enheten:

    `sudo mount <Data Box Heavy device IP>:/<NFS share on Data Box Heavy device> <Path to the folder on local Linux computer>`

    I följande exempel visas hur du ansluter via NFS till en Data Box Heavy-resurs. Den Data Box Heavy IP-adressen är `10.161.23.130` , resursen `Mystoracct_Blob` är monterad på ubuntuVM, monterings punkt `/home/databoxheavyubuntuhost/databoxheavy` .

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`
    
    För Mac-klienter måste du lägga till ytterligare ett alternativ på följande sätt: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`

    **Skapa alltid en mapp för de filer som du vill kopiera under resursen och kopiera sedan filerna till den mappen**. Mappen som skapas under blockblob- och sidblobresurser representerar en container som data laddas upp som blobar till. Du kan inte kopiera filer direkt till *root*-mappen i lagringskontot.

## <a name="copy-data-to-data-box-heavy"></a>Kopiera data till Data Box Heavy

När du är ansluten till Data Box Heavy resurserna är nästa steg att kopiera data. Granska följande innan du kopierar data:

- Se till att du kopierar data till resurser som motsvarar lämplig dataformat. Kopiera exempelvis blockblobdata till resursen för blockblobobjekt. Kopiera virtuella hårddiskar till sidblobar. Om dataformatet inte matchar lämplig resurstyp misslyckas datauppladdningen till Azure i ett senare skede.
-  När du kopierar data måste du se till att data storleken överensstämmer med storleks begränsningarna som beskrivs i [Azure Storage och data Box Heavy gränser](data-box-heavy-limits.md). 
- Om data som laddas upp av Data Box Heavy samtidigt överförs av andra program utanför Data Box Heavy, kan detta resultera i att uppladdningsjobbet misslyckas samt att data skadas.
- Vi rekommenderar att du inte använda både SMB och NFS samtidigt eller kopierar samma data till samma mål i slutet på Azure. I sådana fall kan slutresultatet inte fastställas.
- **Skapa alltid en mapp för de filer som du vill kopiera under resursen och kopiera sedan filerna till den mappen**. Mappen som skapas under blockblob- och sidblobresurser representerar en container som data laddas upp som blobar till. Du kan inte kopiera filer direkt till *root*-mappen i lagringskontot.
- Om du matar in Skift läges känslig katalog och fil namn från en NFS-resurs till NFS på Data Box Heavy: 
    - Ärendet bevaras i namnet.
    - Filerna är inte Skift läges känsliga.
    
    Om du t. ex. kopierar `SampleFile.txt` och så `Samplefile.Txt` bevaras ärendet i namnet när det kopieras till enheten, men den andra filen kommer att skriva över den första som dessa betraktas som en fil.


Om du använder en Linux-värddator använder du en kopieringsverktyg som liknar Robocopy. Några av alternativen som är tillgängliga i Linux är [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) eller [Ultracopier](https://ultracopier.first-world.info/).  

Kommandot `cp` är ett av de bästa alternativen för att kopiera en katalog. Mer information om användningen finns på [cp man-sidorna](http://man7.org/linux/man-pages/man1/cp.1.html).

Om du använder rsync-alternativet för en flertrådig kopia följer du dessa riktlinjer:

 - Installera **CIFS Utils**- eller **NFS Utils**-paketet, beroende på vilket filsystem din Linux-klient använder.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

 -  Installera **Rsync** och **Parallel** (varierar beroende på distribuerad Linux-version).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

 - Skapa en monteringspunkt.

    `sudo mkdir /mnt/databoxheavy`

 - Montera volymen.

    `sudo mount -t NFS4  //Databox-heavy-IP-Address/share_name /mnt/databoxheavy` 

 - Spegla mappkatalogstrukturen.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databoxheavy`

 - Kopiera filerna. 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databoxheavy/{}`

     där j anger antal parallelliseringar, X = antal parallella kopior

     Vi rekommenderar att du börjar med 16 parallella kopior och öka antalet trådar beroende på tillgängliga resurser.

> [!IMPORTANT]
> Följande Linux-filtyper stöds inte: symboliska länkar, paketfiler, blockera filer, Sockets och pipes. Dessa filtyper resulterar i problem under **Förbered för att skicka** steget.

Öppna målmappen för att visa och verifiera de kopierade filerna. Om det uppstod fel under kopieringsprocessen laddar du ned felfilerna för felsökning. Mer information finns i [Visa felloggar under datakopiering till Data Box Heavy](data-box-logs.md#view-error-log-during-data-copy). En detaljerad lista över fel i samband med datakopieringen finns i [Felsöka problem med Data Box Heavy](data-box-troubleshoot.md).

För att säkerställa dataintegriteten beräknas kontrollsumman infogat när data kopieras. När kopieringen är klar kontrollerar du det använda utrymmet och det lediga utrymmet på enheten.
    
   ![Kontrollera ledigt och använt utrymme på instrumentpanelen](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudien om Azure Data Box Heavy har du bland annat lärt dig att:

> [!div class="checklist"]
> * Krav
> * Ansluta till Data Box Heavy
> * Kopiera data till Data Box Heavy


Gå vidare till nästa självstudie och lär dig hur du skickar tillbaka din Data Box-enhet till Microsoft.

> [!div class="nextstepaction"]
> [Skicka Azure Data Box Heavy till Microsoft](./data-box-heavy-deploy-picked-up.md)

