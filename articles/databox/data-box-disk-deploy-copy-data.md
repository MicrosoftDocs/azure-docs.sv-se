---
title: Självstudie för att kopiera data till Azure Data Box Disk | Microsoft Docs
description: I den här självstudien lär du dig hur du kopierar data från din värddator till Azure Data Box Disk och hur du genererar kontrollsummor för att verifiera dataintegriteten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: d33f53ef3d6ea0ef6a3040a82ec17b3089075949
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927121"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>Självstudier: Kopiera data till Azure Data Box Disk och verifiera

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-azure-data-box-disk-and-validate"></a>Kopiera data till Azure Data Box Disk och verifiera

När diskarna har anslutits och låsts upp kan du kopiera data från källdataservern till diskarna. När datakopieringen är klar bör du verifiera dina data för att säkerställa att de kan laddas upp till Azure.

::: zone-end

::: zone target="docs"

Den här självstudien beskriver hur du kopierar data från värddatorn och genererar kontrollsummor för att kontrollera dataintegriteten.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kopiera data till en Data Box-disk
> * Verifiera data

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:
- Du har slutfört självstudien [: Installera och konfigurera Azure Data Box Disk](data-box-disk-deploy-set-up.md).
- Diskarna låses upp och ansluts till en klientdator.
- Klientdatorn som används till att kopiera data till diskarna måste köra ett [operativsystem som stöds](data-box-disk-system-requirements.md#supported-operating-systems-for-clients).
- Se till att rätt lagringstyp för dina data matchar [lagringstyper som stöds](data-box-disk-system-requirements.md#supported-storage-types-for-upload).
- Kontrollera [gränserna för hanterade diskar i avsnittet om storleksgränser för Azure-objekt](data-box-disk-limits.md#azure-object-size-limits).


## <a name="copy-data-to-disks"></a>Kopiera data till diskar

Ha följande i åtanke när du kopierar data till diskarna:

- Det är ditt ansvar att se till att du kopierar data till mappar som matchar lämpligt dataformat. Kopiera exempelvis blockblobdata till mappen för blockblobobjekt. Om dataformatet inte matchar mappen (lagringstyp) misslyckas datauppladdningen till Azure i ett senare skede.
- När du kopierar data ser du till att datastorleken överensstämmer med storleksbegränsningarna som beskrivs i avsnittet om [Azure Storage- och Data Box Disk-gränser](data-box-disk-limits.md).
- Om data som laddas upp av Data Box Disk samtidigt överförs av andra program utanför Data Box Disk, kan detta resultera i att uppladdningsjobbet misslyckas samt att data skadas.

   > [!IMPORTANT]
   >  Läs detta avsnitt om du angav hanterade diskar som ett av lagringsmålen när ordern skapades.

- En hanterad disk i en resursgrupp måste ha ett unikt namn i de förskapade mapparna och på hela Data Box Disk. Det betyder att de virtuella hårddiskarna som laddats upp till de förskapade mapparna måste ha unika namn. Kontrollera att namnet inte matchar en redan befintlig hanterad disk i en resurs grupp. Om de virtuella hårddiskarna har samma namn, konverteras bara en virtuell hårddisk till den hanterade disken med det namnet. De andra virtuella hårddiskarna laddas upp som sidblobar till mellanlagringskontot.
- Kopiera alltid de virtuella hårddiskarna till en av de förskapade mapparna. Om du kopierar de virtuella hårddiskarna utanför dessa mappar eller i en mapp som du har skapat, laddas de virtuella hårddiskarna upp till Azure Storage-kontot som sidblobar i stället för som hanterade diskar.
- Endast fasta virtuella hårddiskar kan laddas upp för att skapa hanterade diskar. Dynamiska virtuella hårddiskar och differentierande VHD- eller VHDX-filer stöds inte.


Utför stegen nedan för att ansluta och kopiera data från din dator till Data Box-disken.

1. Visa innehållet på den upplåsta enheten. Listan över de förskapade mapparna och undermapparna på enheten varierar beroende på vilka alternativ du valde när du beställde Data Box Disk.

    |Valt lagringsmål  |Storage Account-typ|Typ av mellanlagringskonto |Mappar och undermappar  |
    |---------|---------|---------|------------------|
    |Lagringskonto     |GPv1 eller GPv2                 | Ej tillämpligt | BlockBlob <br> PageBlob <br> AzureFile        |
    |Lagringskonto     |Bloblagringskonto         | Ej tillämpligt | BlockBlob        |
    |Hanterade diskar     |Ej tillämpligt | GPv1 eller GPv2         | ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>        |
    |Lagringskonto <br> Hanterade diskar     |GPv1 eller GPv2 | GPv1 eller GPv2         |BlockBlob <br> PageBlob <br> AzureFile <br> ManagedDisk<ul> <li> PremiumSSD </li><li>StandardSSD</li><li>StandardHDD</li></ul>         |
    |Lagringskonto <br> Hanterade diskar    |Bloblagringskonto | GPv1 eller GPv2         |BlockBlob <br> ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>         |

    Ett exempel på en skärmbild av en beställning där ett GPv2-lagringskonto har angetts visas nedan:

    ![Innehåll på diskenheten](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. Kopiera de data som ska importeras som blockblobar till mappen *BlockBlob*. Kopiera data, t.ex. VHD/VHDX, till *PageBlob*-mappen och data till *AzureFile*-mappen.

    En container skapas i Azure Storage-kontot för varje undermapp under mapparna BlockBlob och PageBlob. Alla filer under mapparna BlockBlob och PageBlob kopieras till standardcontainern `$root` under Azure Storage-kontot. Filer i `$root`-containern laddas alltid upp som blockblobar.

   Kopiera filer till en mapp i *AzureFile*-mappen. En undermapp i *AzureFile*-mappen skapar en filresurs. Filer som kopieras direkt till *AzureFile*-mappen misslyckas och laddas upp som blockblobar.

    Om det finns filer och mappar i rotkatalogen måste du flytta dem till en annan mapp innan du börjar kopiera data.

    > [!IMPORTANT]
    > Alla containrar, blobar och filnamn måste följa [namngivningskonventionerna för Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Om dessa regler inte uppfylls misslyckas datauppladdningen till Azure.

3. När du kopierar filer ser du till att filerna inte överskrider ~4,7 TiB för blockblobar, ~8 TiB för sidblobar och ~1 TiB för Azure Files. 
4. Du kan använda ”dra och släpp” med Utforskaren för att kopiera data. Du kan också använda valfritt SMB-kompatibelt filkopieringsverktyg, till exempel Robocopy, för att kopiera data. Flera kopieringsjobb kan initieras med hjälp av följande Robocopy-kommando:

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    Parametrarna och alternativen för kommandot förklaras i tabellen nedan:
    
    |Parametrar/alternativ  |Beskrivning |
    |--------------------|------------|
    |Källa            | Anger sökvägen till källkatalogen.        |
    |Mål       | Anger sökvägen till målkatalogen.        |
    |/E                  | Kopierar underkataloger, inklusive tomma kataloger. |
    |/MT[:N]             | Skapar flertrådiga kopior med N trådar, där N är ett heltal mellan 1 och 128. <br>Standardvärdet för N är 8.        |
    |/R: \<N>             | Anger antalet återförsök vid misslyckade kopieringar. Standardvärdet för N är 1 000 000 (en miljon nya försök).        |
    |/W: \<N>             | Anger väntetiden mellan återförsök i sekunder. Standardvärdet för N är 30 (väntetid på 30 sekunder).        |
    |/NFL                | Anger att filnamn inte ska loggas.        |
    |/NDL                | Anger att katalognamn inte ska loggas.        |
    |/FFT                | Förutsätter FAT-filtider (två sekunders precision).        |
    |/Log:\<Log File>     | Skriver statusutdata till loggfilen (skriver över den befintliga loggfilen).         |

    Flera diskar kan användas parallellt med flera jobb som körs på varje disk.

6. Kontrollera kopieringsstatusen när jobbet pågår. Följande exempel visar utdata från robocopy-kommandot för filkopiering till Data Box-disken.

    ```
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
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
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
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    Du kan optimera prestanda med hjälp av följande robocopy-parametrar när du kopierar data.

    |    Plattform    |    Främst små filer < 512 KB                           |    Främst medelstora filer 512 KB–1 MB                      |    Främst stora filer > 1 MB                             |   
    |----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
    |    Data Box Disk        |    4 Robocopy-sessioner* <br> 16 trådar per session    |    2 Robocopy-sessioner* <br> 16 trådar per session    |    2 Robocopy-sessioner* <br> 16 trådar per session    |
    
    **En enskild Robocopy-session kan omfatta upp till högst 7 000 kataloger och 150 miljoner filer.*
    
    >[!NOTE]
    > De parametrar som föreslås ovan baseras på den miljö som används vid intern testning.
    
    Mer information om Robocopy-kommandon finns i [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy och några exempel).

6. Öppna målmappen för att visa och verifiera de kopierade filerna. Om det uppstod fel under kopieringsprocessen laddar du ned loggfilerna för felsökning. Loggfilerna finns på den plats som anges i kommandot robocopy.
 
### <a name="split-and-copy-data-to-disks"></a>Dela upp och kopiera data till diskar

Den här valfria proceduren kan användas när du använder flera diskar och har en stor datamängd som måste delas upp och kopieras på alla diskarna. Med verktyget Data Box Split Copy kan du dela upp och kopiera data på en Windows-dator.

>[!IMPORTANT]
> Verktyget Data Box Split Copy verifierar också dina data. Om du använder Data Box Split Copy för att kopiera data kan du hoppa över [verifieringssteget](#validate-data).
> Uppdelnings- och kopieringsverktyget stöds inte med hanterade diskar.

1. Se till att verktyget Data Box Split Copy har laddats ned och extraherats i en lokal mapp. Verktyget laddades ned när du ladda ned Data Box Disk-verktygen för Windows.
2. Öppna Utforskaren. Anteckna datakällans enhet och enhetsbokstäver som tilldelats till Data Box Disk. 

     ![Dela upp och kopiera data](media/data-box-disk-deploy-copy-data/split-copy-1.png)
 
3. Identifiera de källdata som ska kopieras. I det här fallet:
    - Följande blockblobdata har identifierats.

         ![Dela upp och kopiera data](media/data-box-disk-deploy-copy-data/split-copy-2.png)    

    - Följande sidblobdata har identifierats.

         ![Dela upp och kopiera data](media/data-box-disk-deploy-copy-data/split-copy-3.png)
 
4. Gå till den mapp där programvaran har extraherats. Leta upp filen `SampleConfig.json` i den mappen. Det här är en skrivskyddad fil som du kan ändra och spara.

   ![Dela upp och kopiera data](media/data-box-disk-deploy-copy-data/split-copy-4.png)
 
5. Ändra filen `SampleConfig.json`.
 
   - Ange ett jobbnamn. Det skapar en mapp på Data Box Disk och blir så småningom containern på Azure Storage-kontot som associeras med dessa diskar. Jobbnamnet måste följa namngivningskonventionerna för Azure-containrar. 
   - Ange en källsökväg och notera sökvägsformatet i `SampleConfigFile.json`. 
   - Ange enhetsbokstäverna som motsvarar måldiskarna. Data tas från källsökvägen och kopieras över flera diskar.
   - Ange en sökväg för loggfilerna. Som standard skickas den till den aktuella katalogen där `.exe` finns.

     ![Dela upp och kopiera data](media/data-box-disk-deploy-copy-data/split-copy-5.png)

6. Verifiera filformatet genom att gå till `JSONlint`. Spara filen som `ConfigFile.json`. 

     ![Dela upp och kopiera data](media/data-box-disk-deploy-copy-data/split-copy-6.png)
 
7. Öppna ett kommandotolksfönster. 

8. Kör `DataBoxDiskSplitCopy.exe`. Typ

    `DataBoxDiskSplitCopy.exe PrepImport /config:<Your-config-file-name.json>`

     ![Dela upp och kopiera data](media/data-box-disk-deploy-copy-data/split-copy-7.png)
 
9. Ange för att fortsätta skriptet.

    ![Dela upp och kopiera data](media/data-box-disk-deploy-copy-data/split-copy-8.png)
  
10. När datamängden har delats upp och kopierats visas sammanfattningen för Split Copy-verktyget för kopieringssessionen. Ett exempel på utdata visas nedan.

    ![Dela upp och kopiera data](media/data-box-disk-deploy-copy-data/split-copy-9.png)
 
11. Verifiera att data delas upp på måldiskarna. 
 
    ![Dela upp och kopiera data](media/data-box-disk-deploy-copy-data/split-copy-10.png)
    ![Dela upp och kopiera data](media/data-box-disk-deploy-copy-data/split-copy-11.png)
     
    Om du undersöker innehållet på enhet `n:` vidare ser du att två undermappar har skapats som motsvarar blockblob- och sidblob-formatdata.
    
     ![Dela upp och kopiera data](media/data-box-disk-deploy-copy-data/split-copy-12.png)

12. Om kopieringssessionen misslyckas använder du följande kommando för att återställa och återuppta:

    `DataBoxDiskSplitCopy.exe PrepImport /config:<configFile.json> /ResumeSession`

Om uppdelnings- och kopieringsverktyget returnerar fel läser du avsnittet [Felsöka problem med uppdelnings- och kopieringsverktyget](data-box-disk-troubleshoot-data-copy.md).

När datakopieringen är klar kan du fortsätta att verifiera dina data. Om du använde Split Copy-verktyget hoppar du över verifieringen (det här verktyget utför även en verifiering) och går vidare till nästa självstudie.


## <a name="validate-data"></a>Verifiera data

Om du inte använde Split Copy-verktyget för att kopiera data måste du verifiera dina data. Verifiera data med hjälp av följande steg.

1. Kör `DataBoxDiskValidation.cmd` för validering av kontrollsumma i mappen *DataBoxDiskImport* för din enhet. Detta är endast tillgängligt för Windows-miljön. Linux-användare måste verifiera att källdata som kopieras till disken uppfyller [förhandskraven](https://docs.microsoft.com/azure/databox/data-box-disk-limits).
    
    ![Utdata för Data Box Disk-valideringsverktyget](media/data-box-disk-deploy-copy-data/data-box-disk-validation-tool-output.png)

2. Välj lämpligt alternativ. **Vi rekommenderar att du alltid validerar filerna och genererar kontrollsummor genom att välja alternativ 2**. Beroende på datastorleken kan det här steget ta en stund. När skriptet är klart stänger du kommandofönstret. Om det uppstår fel vid valideringen och genereringen av kontrollsumma meddelas du och du får även en länk till felloggarna.

    ![Checksum-utdata](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

    > [!TIP]
    > - Återställ verktyget mellan två körningar.
    > - Använd alternativ 1 om du hanterar stora datamängder som innehåller små filer (~ kB). Det här alternativet validerar bara filerna eftersom genereringen av kontrollsummor kan ta mycket lång tid och prestanda kan vara mycket långsamma.

3. Om du använder flera diskar kör du kommandot för varje disk.

Om verifieringen returnerar fel läser du avsnittet [Felsöka verifieringsfel](data-box-disk-troubleshoot.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudien om Azure Data Box Disk har du bland annat lärt dig att:

> [!div class="checklist"]
> * Kopiera data till en Data Box-disk
> * Verifiera dataintegriteten

Gå vidare till nästa självstudie och lär dig hur du returnerar Data Box-disken och verifierar dataöverföringen till Azure.

> [!div class="nextstepaction"]
> [Skicka tillbaka din Azure Data Box till Microsoft](./data-box-disk-deploy-picked-up.md)

::: zone-end

::: zone target="chromeless"

### <a name="copy-data-to-disks"></a>Kopiera data till diskar

Utför stegen nedan för att ansluta och kopiera data från din dator till Data Box-disken.

1. Visa innehållet på den upplåsta enheten. Listan över de förskapade mapparna och undermapparna på enheten varierar beroende på vilka alternativ du valde när du beställde Data Box Disk.
2. Kopiera data till mappar som har lämpligt dataformat. Kopiera till exempel ostrukturerade data till mappen för *BlockBlob*-mappen, VHD- eller VHDX-data till *PageBlob*-mappen och filer till *AzureFile*. Om dataformatet inte matchar mappen (lagringstyp) misslyckas datauppladdningen till Azure i ett senare skede.

    - Se till att alla containrar, blobar och filer följer [Azures namngivningskonventioner](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) och [storleksgränserna för Azure-objekt](data-box-disk-limits.md#azure-object-size-limits). Om dessa regler eller gränser inte uppfylls misslyckas datauppladdningen till Azure.     
    - Om hanterade diskar är ett av lagringsmålen i din beställning kontrollerar du namngivningskonventionerna för [hanterade diskar](data-box-disk-limits.md#managed-disk-naming-conventions).
    - En container skapas i Azure Storage-kontot för varje undermapp under mapparna BlockBlob och PageBlob. Alla filer under mapparna *BlockBlob* och *PageBlob* kopieras till en förvald $root-container under Azure Storage-kontot. Filer i $root-containern laddas alltid upp som blockblobar.
    - Skapa en underordnad mapp i *AzureFile*-mappen. Den här undermappen mappar till en filresurs i molnet. Kopiera filer till undermappen. Filer som kopieras direkt till *AzureFile*-mappen misslyckas och laddas upp som blockblobar.
    - Om det finns filer och mappar i rotkatalogen måste du flytta dem till en annan mapp innan du börjar kopiera data.

3. Använd dra och släpp med Utforskaren eller ett SMB-kompatibelt filkopieringsverktyg som Robocopy för att kopiera dina data. Flera kopieringsjobb kan initieras med hjälp av följande kommando:

    ```
    Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt
    ```
4. Öppna målmappen för att visa och verifiera de kopierade filerna. Om det uppstod fel under kopieringsprocessen laddar du ned loggfilerna för felsökning. Loggfilerna finns på den plats som anges i kommandot robocopy.

Använd den här valfria [uppdelnings- och kopieringsproceduren](data-box-disk-deploy-copy-data.md#split-and-copy-data-to-disks) när du använder flera diskar och har en stor mängd data som måste delas upp och kopieras på alla diskarna.

### <a name="validate-data"></a>Verifiera data

Utför följande steg för att verifiera dina data.

1. Kör `DataBoxDiskValidation.cmd` för validering av kontrollsumma i mappen *DataBoxDiskImport* för din enhet.
2. Använd alternativ 2 för att validera dina filer och generera kontrollsummor. Beroende på datastorleken kan det här steget ta en stund. Om det uppstår fel vid valideringen och genereringen av kontrollsumma meddelas du och du får även en länk till felloggarna.

    Mer information om dataverifiering finns i [Verifiera data](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-copy-data#validate-data). Om det uppstår fel under verifieringen läser du [Felsöka verifieringsfel](data-box-disk-troubleshoot.md).

::: zone-end
