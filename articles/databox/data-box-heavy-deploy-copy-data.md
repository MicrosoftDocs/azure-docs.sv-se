---
title: Självstudie för att kopiera data via SMB på Azure Data Box Heavy | Microsoft Docs
description: I den här självstudien lär du dig hur du ansluter till och kopierar data från din värddator till Azure Data Box Heavy med hjälp av SMB från det lokala webbgränssnittet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 6c88a1eaee50676cfe6bb9589779d7156b805e30
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87920984"
---
::: zone target = "docs"

# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb"></a>Självstudier: Kopiera data till Azure Data Box Heavy via SMB

::: zone-end

::: zone target = "chromeless"

## <a name="copy-data-to-azure-data-box-heavy"></a>Kopiera data till Azure Data Box Heavy

::: zone-end

::: zone target = "docs"

I den här självstudien beskrivs hur du ansluter till och kopierar data från värddatorn med det lokala webbgränssnittet.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ansluta till Data Box Heavy
> * Kopiera data till Data Box Heavy

::: zone-end

::: zone target = "chromeless"

Du kan kopiera data från källservern till din Data Box-enhet via SMB, NFS, REST, datakopieringstjänsten eller till hanterade diskar.

Kontrollera att resurs- och mappnamnen och datastorleken följer riktlinjerna som beskrivs i [tjänstbegränsningarna för Azure Storage och Data Box Heavy](data-box-heavy-limits.md).

::: zone-end

::: zone target = "docs"

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har slutfört självstudien [: Konfigurera Azure Data Box Heavy](data-box-deploy-set-up.md).
2. Du har fått din Data Box Heavy-enhet och orderstatusen på portalen är **Levererad**.
3. Du har en värddator som har de data som du vill kopiera över till Data Box Heavy. Värddatorn måste
    - Köra ett [operativsystem som stöds](data-box-system-requirements.md).
    - Vara ansluten till en höghastighetsnätverk. För snabbast kopieringshastighet kan två 40-GbE-anslutningar (en per nod) användas parallellt. Om du inte har någon tillgänglig 40-GbE-anslutning rekommenderar vi att du har minst två 10 GbE-anslutningar (en per nod).
   

## <a name="connect-to-data-box-heavy-shares"></a>Ansluta till Data Box Heavy-resurser

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
| Azure Block blobs | <li>UNC-sökväg till resurser: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-sidblobar  | <li>UNC-sökväg till resurser: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-sökväg till resurser: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>URL för Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Stegen för att ansluta med en Windows- eller Linux-klient skiljer sig åt.

> [!NOTE]
> Följ samma steg för att ansluta till båda noderna på enheten parallellt.

### <a name="connect-on-a-windows-system"></a>Ansluta i ett Windows-system

Om du använder en Windows Server-värddator följer du stegen nedan för att ansluta till Data Box Heavy.

1. Det första steget är att autentisera och starta en session. Gå till **Anslut och kopiera**. Klicka på **Hämta autentiseringsuppgifter** för att få autentiseringsuppgifter för de resurser som är associerade med ditt lagringskonto.

    ![Hämta resursautentiseringsuppgifter 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. I dialogrutan Få åtkomst till resursen och kopiera data kopierar du **användarnamnet** och **lösenordet** som motsvarar resursen. Klicka på **OK**.
    
    ![Hämta resursautentiseringsuppgifter 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. För att komma åt resurser som är associerade med ditt lagringskonto (*databoxe2etest* i följande exempel) från värddatorn öppnar du ett kommandofönster. Skriv följande i kommandotolken:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Beroende på ditt dataformat är resursssökvägarna följande:
    - Azure-blockblob – `\\10.100.10.100\databoxe2etest_BlockBlob`
    - Azure-sidblob – `\\10.100.10.100\databoxe2etest_PageBlob`
    - Azure Files – `\\10.100.10.100\databoxe2etest_AzFile`
    
4. Ange lösenordet för resursen när du tillfrågas. Följande exempel visar hur du ansluter till en resurs via kommandot ovan.

    ```
    C:\Users\Databoxuser>net use \\10.100.10.100\databoxe2etest_BlockBlob /u:databoxe2etest
    Enter the password for 'databoxe2etest' to connect to '10.100.10.100':
    The command completed successfully.
    ```

4. Tryck på Windows + R. I fönstret **Kör** anger du `\\<device IP address>`. Öppna Utforskaren genom att klicka på **OK**.
    
    ![Ansluta till resursen via Utforskaren 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-1.png)

    Du bör nu se resurserna som mappar.
    
    ![Ansluta till resursen via Utforskaren 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-2.png)

    **Skapa alltid en mapp för de filer som du vill kopiera under resursen och kopiera sedan filerna till den mappen**. Mappen som skapas under blockblob- och sidblobresurser representerar en container som data laddas upp som blobar till. Du kan inte kopiera filer direkt till *root*-mappen i lagringskontot.
    
### <a name="connect-on-a-linux-system"></a>Ansluta i ett Linux-system

Om du använder en Linux-klient använder du följande kommando för att montera SMB-resursen.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

`vers`-parametern är den version av SMB som din Linux-värd stöder. Ange korrekt version i kommandot ovan.

Versioner av SMB som Data Box Heavy har stöd för finns i avsnittet om [filsystem som stöds för Linux-klienter](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients).

## <a name="copy-data-to-data-box-heavy"></a>Kopiera data till Data Box Heavy

När du har anslutit till Data Box Heavy-resurserna är nästa steg att kopiera data.

### <a name="copy-considerations"></a>Att tänka på när du kopierar

Granska följande innan du kopierar data:

- Se till att du kopierar data till resurser som motsvarar lämplig dataformat. Kopiera exempelvis blockblobdata till resursen för blockblobobjekt. Kopiera de virtuella hårddiskarna till en sidblob.

    Om dataformatet inte matchar lämplig resurstyp misslyckas datauppladdningen till Azure i ett senare skede.
-  När du kopierar data ser du till att datastorleken överensstämmer med storleksbegränsningarna som beskrivs i avsnittet om [Azure Storage- och Data Box Heavy-gränser](data-box-heavy-limits.md).
- Om data som laddas upp av Data Box Heavy samtidigt överförs av andra program utanför Data Box Heavy, kan detta resultera i att uppladdningsjobbet misslyckas samt att data skadas.
- Vi rekommenderar att du:
    - Inte använder SMB och NFS samtidigt.
    - Kopierar samma data till samma mål på Azure.
     
  I sådana fall kan slutresultatet inte fastställas.
- Skapa alltid en mapp för de filer som du vill kopiera under resursen och kopiera sedan filerna till den mappen. Mappen som skapas under blockblob- och sidblobresurser representerar en container som data laddas upp som blobar till. Du kan inte kopiera filer direkt till *root*-mappen i lagringskontot.

När du har anslutit till SMB-resursen kan du påbörja en datakopiering.

1. Du kan använda valfritt SMB-kompatibelt filkopieringsverktyg, till exempel Robocopy, för att kopiera data. Flera kopieringsjobb kan initieras med hjälp av Robocopy. Ange följande kommando:
    
    ```
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
    ```
    Attributen beskrivs i följande tabell.
    
    |Attribut  |Beskrivning  |
    |---------|---------|
    |/e      |Kopierar underkataloger, inklusive tomma kataloger.         |
    |/r:     |Anger antalet återförsök vid misslyckade kopieringar.         |
    |/w:     |Anger väntetiden mellan återförsök i sekunder.         |
    |/is     |Innehåller samma filer.         |
    |/nfl    |Anger att filnamn inte ska loggas.         |
    |/ndl    |Anger att katalognamn inte ska loggas.        |
    |/np     |Anger att förloppet för kopieringsåtgärden (antalet filer eller kataloger som kopierats hittills) inte visas. Att visa förloppet ger betydligt läge prestanda.         |
    |/MT     | Använd flertrådsteknik, 32 eller 64 trådar rekommenderas. Det här alternativet används inte med krypterade filer. Du kan behöva separera krypterade och okrypterade filer. Men enkeltrådig kopiering ger betydligt lägre prestanda.           |
    |/fft    | Används för att minska tidsstämpelkornighet för alla filsystem.        |
    |/b      | Kopierar filer i säkerhetskopieringsläge.        |
    |/z      | Kopierar filer i omstartsläge, används om miljön är instabil. Det här alternativet minskar dataflödet på grund av ytterligare loggning.      |
    | /zb    | Använder omstartsläge. Om åtkomst nekas använder det här alternativet omstartsläge. Det här alternativet minskar dataflödet på grund av kontrollpunkter.         |
    |/efsraw | Kopierar alla krypterade filer i EFS RAW-läge. Används bara med krypterade filer.         |
    |log+:\<LogFile>| Lägger till utdata till den befintliga loggfilen.|
    
 
    Följande exempel visar utdata från robocopy-kommandot för filkopiering till Data Box Heavy.

    ```   
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.100.10.100\devicemanagertest1_AzFile\templates /MT:24
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
        Started : Thursday, April 4, 2019 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
        Dest : \\10.100.10.100\devicemanagertest1_AzFile\templates\
        Files : *.*
        Options : *.* /DCOPY:DA /COPY:DAT /MT:24 /R:5 /W:60
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

2. Du kan optimera prestanda med hjälp av följande robocopy-parametrar när du kopierar data. (Siffrorna nedan representerar ”bästa fall”-scenarier.)

    | Plattform    | Främst små filer < 512 KB    | Främst medelstora filer 512 kB–1 MB  | Främst stora filer > 1 MB                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box – tung | 6 Robocopy-sessioner <br> 24 trådar per session | 6 Robocopy-sessioner <br> 16 trådar per session | 6 Robocopy-sessioner <br> 16 trådar per session |


    Mer information om Robocopy-kommandon finns i [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy och några exempel).

3. Öppna målmappen för att visa och verifiera de kopierade filerna.

    ![Visa kopierade filer](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. När data kopieras:

    - Filnamnen, storlekarna och formatet verifieras för att säkerställa att de uppfyller objekt- och lagringsgränserna i Azure och namngivningskonventionerna för Azure-filer och -containrar.
    - För att säkerställa dataintegriteten beräknas även kontrollsummorna på samma nivå.

    Om det uppstod fel under kopieringsprocessen laddar du ned felfilerna för felsökning. Välj pilikonen för att ladda ned felfilerna.

    ![Ladda ned felfiler](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    Mer information finns i [Visa felloggar under datakopiering till Data Box Heavy](data-box-logs.md#view-error-log-during-data-copy). En detaljerad lista över fel i samband med datakopieringen finns i [Felsöka problem med Data Box Heavy](data-box-troubleshoot.md).

5. Öppna felfilen i Anteckningar. Följande felfil anger att data inte justeras korrekt.

    ![Öppna felfilen](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    För en sidblob måste data vara justerade för 512 byte. När dessa data har tagits bort åtgärdas felet, som du ser i följande skärmbild.

    ![Åtgärdat fel](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. När kopieringen är klar går du till sidan **Visa instrumentpanel**. Kontrollera det använda utrymmet och det lediga utrymmet på enheten.
    
    ![Kontrollera ledigt och använt utrymme på instrumentpanelen](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Upprepa stegen ovan för att kopiera data till den andra noden på enheten.

## <a name="next-steps"></a>Nästa steg

I den här självstudien om Azure Data Box Heavy har du bland annat lärt dig att:

> [!div class="checklist"]
> * Ansluta till Data Box Heavy
> * Kopiera data till Data Box Heavy


Gå vidare till nästa självstudie och lär dig hur du skickar tillbaka din Data Box Heavy till Microsoft.

> [!div class="nextstepaction"]
> [Skicka Azure Data Box Heavy till Microsoft](./data-box-heavy-deploy-picked-up.md)

::: zone-end

::: zone target = "chromeless"

### <a name="copy-data-via-smb"></a>Kopiera data via SMB

1. Om du använder en Windows-värd använder du följande kommando för att ansluta till SMB-resurserna:

    `\\<IP address of your device>\ShareName`

2. Du kan hämta autentiseringsuppgifterna för åtkomst till resursen på sidan **Connect & copy** (Anslut och kopiera) i det lokala webbgränssnittet i Data Box.

3. Använd ett SMB-kompatibelt filkopieringsverktyg som Robocopy för att kopiera data till resurser.

Stegvisa instruktioner finns i [Självstudier: Kopiera data till Azure Data Box via SMB](data-box-heavy-deploy-copy-data.md).

### <a name="copy-data-via-nfs"></a>Kopiera data via NFS

1. Om du använder en NFS-värd använder du följande kommando för att montera NFS-resurserna:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. Du kan hämta autentiseringsuppgifterna för åtkomst till resursen på sidan **Connect & copy** (Anslut och kopiera) i det lokala webbgränssnittet i Data Box Heavy.
3. Använd kommandot `cp` eller `rsync` för att kopiera dina data. 
4. Upprepa de här stegen för att ansluta och kopiera data till den andra noden på Data Box Heavy-enheten.

Stegvisa instruktioner finns i [Självstudier: Kopiera data till Azure Data Box via NFS](data-box-heavy-deploy-copy-data-via-nfs.md).

### <a name="copy-data-via-rest"></a>Kopiera data via REST

1. Om du vill kopiera data med Data Box-bloblagring via REST-API:er kan du ansluta via *http* eller *https*.
2. Om du vill kopiera data till Data Box-blogglagring kan du använda AzCopy.
3. Upprepa de här stegen för att ansluta och kopiera data till den andra noden på Data Box Heavy-enheten.

Stegvisa instruktioner finns i [Självstudier: Kopiera data till Azure Data Box-bloblagring via REST-API:er](data-box-heavy-deploy-copy-data-via-rest.md).

### <a name="copy-data-via-data-copy-service"></a>Kopiera data via datakopieringstjänsten

1. Om du ska kopiera data med hjälp av datakopieringstjänsten måste du skapa ett jobb. I det lokala webbgränssnittet på din Data Box Heavy går du till **Hantera > Kopiera data > Skapa**.
2. Fyll i parametrarna och skapa ett jobb.
3. Upprepa de här stegen för att ansluta och kopiera data till den andra noden på Data Box Heavy-enheten.

Stegvisa instruktioner finns i [Självstudier: Använd datakopieringstjänsten för att kopiera data till Azure Data Box Heavy](data-box-heavy-deploy-copy-data-via-copy-service.md).

### <a name="copy-data-to-managed-disks"></a>Kopiera data till hanterade diskar

1. När du beställer Data Box Heavy bör du ha valt hanterade diskar som lagringsmål.
2. Du kan ansluta till Data Box Heavy via SMB- eller NFS-resurser.
3. Sedan kan du kopiera data via SMB- eller NFS-verktyg.
4. Upprepa de här stegen för att ansluta och kopiera data till den andra noden på Data Box Heavy-enheten.

Stegvisa instruktioner finns i [Självstudier: Använd Data Box Heavy för att importera data som hanterade diskar i Azure](data-box-heavy-deploy-copy-data-from-vhds.md).

::: zone-end


