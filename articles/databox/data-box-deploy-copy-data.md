---
title: Självstudie för att kopiera data via SMB på Azure Data Box | Microsoft Docs
description: I den här självstudien lär du dig hur du ansluter till och kopierar data från din värddator till Azure Data Box med hjälp av SMB med det lokala webbgränssnittet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: a0622c7556896b7ae7201ffa3a7ecac8de1106a4
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053549"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>Självstudier: Kopiera data till Azure Data Box via SMB

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-azure-data-box"></a>Kopiera data till Azure Data Box

::: zone-end

::: zone target="docs"

I den här självstudien beskrivs hur du ansluter till och kopierar data från värddatorn med det lokala webbgränssnittet.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Krav
> * Ansluta till Data Box
> * Kopiera data till Data Box

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har slutfört självstudien [: Konfigurera Azure Data Box](data-box-deploy-set-up.md).
2. Du har fått din Data Box och att orderstatusen i portalen är **Levererad**.
3. Du har en värddator som har de data du vill kopiera över till Data Box. Värddatorn måste
   * Köra ett [operativsystem som stöds](data-box-system-requirements.md).
   * Vara ansluten till en höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en 10 GbE anslutning inte är tillgänglig kan en 1 GbE datalänk användas, men kopieringshastigheten påverkas.

## <a name="connect-to-data-box"></a>Ansluta till Data Box

Utifrån det lagringskontot som väljs skapar Data Box upp till:

* Tre resurser för varje associerat lagringskonto för GPv1 och GPv2.
* En resurs för premiumlagring.
* En resurs för bloblagringskonto.

Under blockblob- och sidblobresurser är entiteter på första nivån containrar och entiteter på andra nivån är blobar. Under resurser för Azure Files är entiteter på första nivån resurser och entiteter på andra nivån är filer.

I följande tabell visas UNC-sökvägen till filresurser på din Data Box och Azure Storage-sökvägens URL som data har överförts till. URL:en till den sista Azure Storage-sökvägen kan härledas från sökvägen till UNC-resursen.
 
|Azure Storage-typer  | Data Box-resurser            |
|-------------------|--------------------------------------------------------------------------------|
| Azure Block blobs | <li>UNC-sökväg till resurser: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-sidblobar  | <li>UNC-sökväg till resurser: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-sökväg till resurser: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Om du använder en Windows Server-värddator följer du stegen nedan för att ansluta till Data Box.

1. Det första steget är att autentisera och starta en session. Gå till **Anslut och kopiera**. Välj **SMB** för att hämta autentiseringsuppgifterna för de resurser som är kopplade till ditt lagringskonto. 

    ![Hämta resursautentiseringsuppgifter 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. I dialogrutan Få åtkomst till resursen och kopiera data kopierar du **användarnamnet** och **lösenordet** som motsvarar resursen. Välj **OK**.
    
    ![Hämta resursautentiseringsuppgifter 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. För att komma åt resurser som är associerade med ditt lagringskonto (*utsac1* i följande exempel) från värddatorn öppnar du ett kommandofönster. Skriv följande i kommandotolken:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Beroende på ditt dataformat är resursssökvägarna följande:
    - Azure-blockblob – `\\10.126.76.138\utSAC1_202006051000_BlockBlob`
    - Azure-sidblob – `\\10.126.76.138\utSAC1_202006051000_PageBlob`
    - Azure Files – `\\10.126.76.138\utSAC1_202006051000_AzFile`

4. Ange lösenordet för resursen när du tillfrågas. Följande exempel visar hur du ansluter till en resurs via kommandot ovan.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.138\utSAC1_202006051000_BlockBlob /u:testuser1
    Enter the password for 'testuser1' to connect to '10.126.76.138':
    The command completed successfully.
    ```

4. Tryck på Windows + R. I fönstret **Kör** anger du `\\<device IP address>`. Öppna Utforskaren genom att välja **OK**.
    
    ![Ansluta till resursen via Utforskaren 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    Du bör nu se resurserna som mappar.
    
    ![Ansluta till resursen via Utforskaren 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png)

    **Skapa alltid en mapp för de filer som du vill kopiera under resursen och kopiera sedan filerna till den mappen**. Mappen som skapas under blockblob- och sidblobresurser representerar en container som data laddas upp som blobar till. Du kan inte kopiera filer direkt till *root*-mappen i lagringskontot.
    
Om du använder en Linux-klient använder du följande kommando för att montera SMB-resursen. Parametern ”vers” nedan är den version av SMB din Linux-värd stödjer. Inför lämplig version i kommandot nedan. Versioner av SMB som Data Box har stöd för finns i avsnittet om [filsystem som stöds för Linux-klienter](https://docs.microsoft.com/azure/databox/data-box-system-requirements#supported-file-systems-for-linux-clients) 

```console
sudo mount -t nfs -o vers=2.1 10.126.76.138:/utSAC1_202006051000_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-to-data-box"></a>Kopiera data till Data Box

När du är ansluten till Data Box-resurser är nästa steg att kopiera data. Granska följande innan du kopierar data:

* Se till att du kopierar data till resurser som motsvarar lämplig dataformat. Kopiera exempelvis blockblobdata till resursen för blockblobobjekt. Kopiera de virtuella hårddiskarna till en sidblob. Om dataformatet inte matchar lämplig resurstyp misslyckas datauppladdningen till Azure i ett senare skede.
* När du kopierar data ser du till att datastorleken överensstämmer med storleksbegränsningarna som beskrivs i avsnittet om [Azure Storage- och Data Box-gränser](data-box-limits.md).
* Om data som laddas upp av Data Box samtidigt överförs av andra program utanför Data Box, kan detta resultera i att uppladdningsjobbet misslyckas samt att data skadas.
* Vi rekommenderar att du:
  * Inte använder SMB och NFS samtidigt.
  * Kopierar samma data till samma mål på Azure.

  I sådana fall kan slutresultatet inte fastställas.
* Skapa alltid en mapp för de filer som du vill kopiera under resursen och kopiera sedan filerna till den mappen. Mappen som skapas under blockblob- och sidblobresurser representerar en container som data laddas upp som blobar till. Du kan inte kopiera filer direkt till *root*-mappen i lagringskontot.

> [!IMPORTANT]
> Se till att du behåller en kopia av dina källdata tills du kan bekräfta att Data Box-enheten har överfört dina data till Azure Storage.

När du har anslutit till SMB-resursen kan du påbörja en datakopiering. Du kan använda valfritt SMB-kompatibelt filkopieringsverktyg, till exempel Robocopy, för att kopiera data. Flera kopieringsjobb kan initieras med hjälp av Robocopy. Ange följande kommando:

```console
robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
```

Attributen beskrivs i följande tabell.
    
|Attribut  |Beskrivning  |
|---------|---------|
|/e     |Kopierar underkataloger, inklusive tomma kataloger.         |
|/r:     |Anger antalet återförsök vid misslyckade kopieringar.         |
|/w:     |Anger väntetiden mellan återförsök i sekunder.         |
|/is     |Innehåller samma filer.         |
|/nfl     |Anger att filnamn inte ska loggas.         |
|/ndl    |Anger att katalognamn inte ska loggas.        |
|/np     |Anger att förloppet för kopieringsåtgärden (antalet filer eller kataloger som kopierats hittills) inte visas. Att visa förloppet ger betydligt läge prestanda.         |
|/MT     | Använd flertrådsteknik, 32 eller 64 trådar rekommenderas. Det här alternativet används inte med krypterade filer. Du kan behöva separera krypterade och okrypterade filer. Men enkeltrådig kopiering ger betydligt lägre prestanda.           |
|/fft     | Används för att minska tidsstämpelkornighet för alla filsystem.        |
|/b     | Kopierar filer i säkerhetskopieringsläge.        |
|/z    | Kopierar filer i omstartsläge, används om miljön är instabil. Det här alternativet minskar dataflödet på grund av ytterligare loggning.      |
| /zb     | Använder omstartsläge. Om åtkomst nekas använder det här alternativet omstartsläge. Det här alternativet minskar dataflödet på grund av kontrollpunkter.         |
|/efsraw     | Kopierar alla krypterade filer i EFS RAW-läge. Används bara med krypterade filer.         |
|log+:\<LogFile>| Lägger till utdata till den befintliga loggfilen.|    
 
Följande exempel visar utdata från robocopy-kommandot för filkopiering till Data Box.

```output
C:\Users>robocopy

    -------------------------------------------------------------------------------
    ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------

        Started : Thursday, March 8, 2018 2:34:53 PM
        Simple Usage :: ROBOCOPY source destination /MIR

        source :: Source Directory (drive:\path or \\server\share\path).
        destination :: Destination Dir  (drive:\path or \\server\share\path).
                /MIR :: Mirror a complete directory tree.

    For more usage information run ROBOCOPY /?

    ****  /MIR can DELETE files as well as copy them !

C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32

    -------------------------------------------------------------------------------
    ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------

        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\

        Files : *.*

        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60

    ------------------------------------------------------------------------------

    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------

                Total    Copied   Skipped  Mismatch    FAILED    Extras
    Dirs :         1         1         1         0         0         0
    Files :        17        17         0         0         0         0
    Bytes :     3.9 k     3.9 k         0         0         0         0
C:\Users>
```

Du kan optimera prestanda med hjälp av följande robocopy-parametrar när du kopierar data.

|    Plattform    |    Främst små filer < 512 KB                           |    Främst medelstora filer 512 KB–1 MB                      |    Främst stora filer > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
|    Data Box         |    2 Robocopy-sessioner <br> 16 trådar per session    |    3 Robocopy-sessioner <br> 16 trådar per session    |    2 Robocopy-sessioner <br> 24 trådar per session    |

Mer information om Robocopy-kommandon finns i [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy och några exempel).

Ett meddelande visas om det uppstår fel under kopieringen.

![Ladda ned och visa fel från Anslut och kopiera](media/data-box-deploy-copy-data/view-errors-1.png)

Välj **Ladda ned lista med ärenden**.

![Ladda ned och visa fel från Anslut och kopiera](media/data-box-deploy-copy-data/view-errors-2.png)

Öppna listan för att visa information om felet och välj lösnings-URL:en för att visa den rekommenderade lösningen.

![Ladda ned och visa fel från Anslut och kopiera](media/data-box-deploy-copy-data/view-errors-3.png)

Mer information finns i [Visa felloggar under datakopiering till Data Box](data-box-logs.md#view-error-log-during-data-copy). En detaljerad lista över fel i samband med datakopieringen finns i [Felsöka problem med Data Box](data-box-troubleshoot.md).

För att säkerställa dataintegriteten beräknas kontrollsumman infogat när data kopieras. När kopieringen är klar kontrollerar du det använda utrymmet och det lediga utrymmet på enheten.

![Kontrollera ledigt och använt utrymme på instrumentpanelen](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

::: zone-end

::: zone target="chromeless"

Du kan kopiera data från källservern till din Data Box-enhet via SMB, NFS, REST, datakopieringstjänsten eller till hanterade diskar.

Kontrollera att resurs- och mappnamnen och datastorleken följer riktlinjerna som beskrivs i [tjänstbegränsningarna för Azure Storage och Data Box](data-box-limits.md).

## <a name="copy-data-via-smb"></a>Kopiera data via SMB

Så här kopierar du data via SMB:

1. Om du använder en Windows-värd använder du följande kommando för att ansluta till SMB-resurserna:

    `\\<IP address of your device>\ShareName`

2. Du kan hämta autentiseringsuppgifterna för åtkomst till resursen på sidan **Connect & copy** (Anslut och kopiera) i det lokala webbgränssnittet i Data Box.
3. Använd ett SMB-kompatibelt filkopieringsverktyg som Robocopy för att kopiera data till resurser. 

Stegvisa instruktioner finns i [Självstudier: Kopiera data till Azure Data Box via SMB](data-box-deploy-copy-data.md).

## <a name="copy-data-via-nfs"></a>Kopiera data via NFS

Så här kopierar du data via NFS:

1. Om du använder en NFS-värd använder du följande kommando för att montera NFS-resurserna på Data Box-enheten:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. Du kan hämta autentiseringsuppgifterna för åtkomst till resursen på sidan **Connect & copy** (Anslut och kopiera) i det lokala webbgränssnittet i Data Box.
3. Använd kommandot `cp` eller `rsync` för att kopiera dina data.

Stegvisa instruktioner finns i [Självstudier: Kopiera data till Azure Data Box via NFS](data-box-deploy-copy-data-via-nfs.md).

## <a name="copy-data-via-rest"></a>Kopiera data via REST

Så här kopierar du data via REST:

1. Om du vill kopiera data med Data Box-bloblagring via REST-API:er kan du ansluta via *http* eller *https*.
2. Om du vill kopiera data till Data Box-blogglagring kan du använda AzCopy.

Stegvisa instruktioner finns i [Självstudier: Kopiera data till Azure Data Box-bloblagring via REST-API:er](data-box-deploy-copy-data-via-nfs.md).

## <a name="copy-data-via-data-copy-service"></a>Kopiera data via datakopieringstjänsten

Så här kopierar du data via datakopieringstjänsten:

1. Om du ska kopiera data med hjälp av datakopieringstjänsten måste du skapa ett jobb. I det lokala webbgränssnittet på din Data Box går du till **Hantera > Kopiera data > Skapa**.
2. Fyll i parametrarna och skapa ett jobb.

Stegvisa instruktioner finns i [Självstudier: Använd datakopieringstjänsten för att kopiera data till Azure Data Box](data-box-deploy-copy-data-via-copy-service.md).

## <a name="copy-data-to-managed-disks"></a>Kopiera data till hanterade diskar

Så här kopierar du data till hanterade diskar:

1. När du beställer Data Box-enheten bör du ha valt hanterade diskar som lagringsmål.
2. Du kan ansluta till Data Box via SMB- eller NFS-resurser.
3. Sedan kan du kopiera data via SMB- eller NFS-verktyg.

Stegvisa instruktioner finns i [Självstudier: Använd Data Box för att importera data som hanterade diskar i Azure](data-box-deploy-copy-data-from-vhds.md).

::: zone-end

::: zone target="docs"

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

::: zone-end
