---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cbc7c82803115f71db233be94d62a857ba050b63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86050204"
---
#### <a name="to-download-hotfixes"></a>Ladda ned snabbkorrigerar

Utför följande steg för att hämta programuppdateringen från Microsoft Update Catalog.

1. Starta Internet Explorer och gå till [http://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .
2. Om det här är första gången du använder Microsoft Update Catalog på den här datorn klickar du på **Installera** när du uppmanas att installera tillägget för Microsoft Update Catalog.

    ![Installera katalog](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. I rutan Sök i Microsoft Updates katalogen anger du Knowledge Base-numret för den snabb korrigering som du vill ladda ned, till exempel **4037264**, och klickar sedan på **Sök**.
   
    Listan med snabb korrigeringar visas till exempel **kumulativa program varu paket uppdatering 5,0 för StorSimple 8000-serien**.
   
    ![Sökkatalog](./media/storsimple-install-update5-hotfix/update-catalog-search.png)

4. Klicka på **Hämta**. Ange eller **Bläddra** till en lokal plats där du vill att nedladdningarna ska läggas. Klicka på de filer som ska laddas ned till den angivna platsen och mappen. Mappen kan också kopieras till en nätverksresurs som kan nås från enheten.
5. Sök efter ytterligare snabb korrigeringar som anges i tabellen ovan (**4037266**) och ladda ned motsvarande filer till de mappar som anges i tabellen ovan.

> [!NOTE]
> Snabb korrigeringarna måste vara tillgängliga från båda styrenheterna för att kunna identifiera eventuella fel meddelanden från peer-styrenheten.
>
> Snabbkorrigeringarna måste kopieras till tre separata mappar. Uppdatering av enhetens program vara/CIS/MDS-agent kan till exempel kopieras i _FirstOrderUpdate_ -mappen, alla andra icke-störande uppdateringar kan kopieras i mappen _SecondOrderUpdate_ och uppdatering av underhålls läge kopieras i mappen _ThirdOrderUpdate_ .

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>Installera och verifiera snabbkorrigeringar i normalläge

Utför följande steg för att installera och verifiera snabbkorrigeringar i normalläge. Om du redan har installerat dem med hjälp av Azure Portal kan du gå vidare till [Installera och verifiera snabb korrigeringar för underhålls läge](#to-install-and-verify-maintenance-mode-hotfixes).

1. Gå in i Windows PowerShell-gränssnittet på din StorSimple-enhets seriekonsol för att installera snabbkorrigerarna. Följ de detaljerade instruktionerna i [Använd PuTTY för att ansluta till enhetens seriekonsol](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). Tryck på **Retur** i kommandotolken.
2. Välj alternativ 1, **Logga in med fullständig åtkomst**. Vi rekommenderar att du installerar snabbkorrigeringen på den passiva styrenheten först.
3. Ange följande i kommandotolken för att installera snabbkorrigeringen:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Använd IP i stället för DNS i resurssökvägen i kommandot ovan. Parametern för autentiseringsuppgifter används bara om du ansluter till en autentiserad resurs.
   
    Vi rekommenderar att du använder parametern för autentiseringsuppgifter för att få åtkomst till resurser. Även resurser som är öppna för ”alla” är vanligtvis inte öppna för icke-autentiserade användare.
   
4. Ange lösenordet när du uppmanas att göra så. Ett exempel på utdata för att installera första orderns uppdateringar visas nedan. För den första order uppdateringen måste du peka på den aktuella filen.

    >[!NOTE] 
    > Du bör installera _HcsSoftwareUpdate.exe_ först. När installationen är klar installerar du _CisMdsAgentUpdate.exe_.
   
    ```output
    Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
    \FirstOrderUpdate\HcsSoftwareUpdate.exe -Credential contoso\John
   
    Confirm
   
    This operation starts the hotfix installation and could reboot one or
    both of the controllers. If the device is serving I/Os, these will not
    be disrupted. Are you sure you want to continue?
    [Y] Yes [N] No [?] Help (default is "Y"): Y
   
    ```
5. Skriv **Y** när du uppmanas att bekräfta installationen av snabbkorrigeringen.
6. Övervaka uppdateringen med hjälp av `Get-HcsUpdateStatus`-cmdlet. Uppdateringen slutförs först på den passiva styrenheten. När den passiva styrenheten har uppdaterats sker en redundans och uppdateringen tillämpas sedan på den andra styrenheten. Uppdateringen har slutförts när båda styrenheterna har uppdateras.
   
    Följande exempel på utdata visar att uppdateringen pågår. `RunInprogress`Är `True` när uppdateringen pågår.

    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp :
    LastUpdateTimestamp : 07/28/2017 2:04:02 AM
    Controller0Events   :
    Controller1Events   :
    ```
   
     Följande exempel på utdata visar att uppdateringen är färdig. `RunInProgress`Är `False` när uppdateringen har slutförts.
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : False
    LastHotfixTimestamp : 07/28/2017 9:15:55 AM
    LastUpdateTimestamp : 07/28/2017 9:06:07 AM
    Controller0Events   :
    Controller1Events   :
    ```

    > [!NOTE]
    > I vissa fall rapporterar cmdlet `False` när uppdateringen fortfarande pågår. Om du vill kontrollera att snabbkorrigeringen har slutförts väntar du några minuter och sedan kör du det här kommandot igen och kontrollerar att `RunInProgress` är `False`. Om det har det har snabbkorrigeringen slutförts.

7. När programuppdateringen har slutförts kan du kontrollera systemprogramversionerna. Ange:
   
    `Get-HcsSystem`
   
    Du bör se följande versioner:
   
   * `FriendlySoftwareVersion: StorSimple 8000 Series Update 5.0`
   * `HcsSoftwareVersion: 6.3.9600.17845`
   
     Om versionsnumret inte ändras efter att uppdateringen har tillämpats indikerar det att snabbkorrigeringen har misslyckats. Kontakta [Microsoft Support](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) för ytterligare hjälp om du ser det här.
     
     > [!IMPORTANT]
     > Du måste starta om den aktiva styrenheten via `Restart-HcsController` cmdleten innan du använder nästa uppdatering.
     
8. Upprepa steg 3-6 för att installera _CisMDSAgentupdate.exe_ -agenten som hämtats till _FirstOrderUpdate_ -mappen.
8. Upprepa steg 3-6 för att installera de andra order uppdateringarna. 

    > [!NOTE] 
    > För andra order uppdateringar kan flera uppdateringar installeras genom att du kör `Start-HcsHotfix cmdlet` och pekar på den mapp där andra order uppdateringar finns. Cmdleten kör alla tillgängliga uppdateringar i mappen. Om en uppdatering redan är installerad identifierar uppdateringslogiken det och tillämpar inte uppdateringen.

    När alla snabbkorrigeringar har installerats använder du `Get-HcsSystem`-cmdleten. Versionerna bör vara:
    
    * `CisAgentVersion:  1.0.9724.0`
    * `MdsAgentVersion: 35.2.2.0`
    * `Lsisas2Version: 2.0.78.00`


#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>Installera och verifiera snabbkorrigeringar i underhållsläge

Använd KB4037263 om du vill installera uppdateringar av inbyggd disk. Det här är störande uppdateringar och tar cirka 30 minuter för att slutföra. Du kan välja att installera dem i ett planerat underhållsfönster genom att ansluta till enhetens seriekonsol.

> [!NOTE] 
> Om diskens inbyggda program vara redan är uppdaterad behöver du inte installera dessa uppdateringar. Kör `Get-HcsUpdateAvailability`-cmdleten från enhetens seriekonsol för att kontrollera om det finns tillgängliga uppdateringar och om uppdateringarna är störande (underhållsläge) eller avbrottsfria (standardläget).

Följ anvisningarna nedan om du vill installera uppdateringarna för den inbyggda programvaran för disken.

1. Sätt enheten i underhållsläge. 

    > [!NOTE] 
    > Använd inte Windows PowerShell-fjärrkommunikation när du ansluter till en enhet i underhålls läge. I stället kan du köra den här cmdleten på enhetens styrenhet när du är ansluten via enhetens seriekonsol.

    Om du vill placera kontrollanten i underhålls läge, skriver du:
   
    `Enter-HcsMaintenanceMode`
   
    Ett exempel på utdata visas nedan.

    ```output
    Controller0>Enter-HcsMaintenanceMode
    Checking device state...
   
    In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y
   
    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8600
    Name: Update4-8600-mystorsimple
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------
   
    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>
    ```

    Båda styrenheterna och starta sedan om i underhållsläge.
2. Ange följande för att installera uppdateringen av inbyggd programvara för disk:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Ett exempel på utdata visas nedan.

    ```output
    Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\ThirdOrderUpdates\ -Credential contoso\john
    Enter Password:
    WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
    Confirm
    This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
    [Y] Yes [N] No (Default is "Y"): Y
    WARNING: Installation is currently in progress. This operation can take several minutes to complete.
    ```

3. Övervaka installationsförloppet med `Get-HcsUpdateStatus`-kommandot. Uppdateringen är slutförd när `RunInProgress` ändras till `False`.
4. När installationen är färdig startas styrenheten som snabbkorrigeringen i underhållsläge installerades på om. Logga in som alternativ 1, **Logga in med fullständig åtkomst** och kontrol lera den inbyggda diskens version. Ange:
   
   `Get-HcsFirmwareVersion`
   
   De förväntade versionerna för den inbyggda programvaran för disken är:
   
   `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`
   
   Ett exempel på utdata visas nedan.

    ```output
       -----------------------MAINTENANCE MODE------------------------
       Microsoft Azure StorSimple Appliance Model 8600
       Name: Update4-8600-mystorsimple
       Software Version: 6.3.9600.17845
       Copyright (C) 2014 Microsoft Corporation. All rights reserved.
       You are connected to Controller1
       ---------------------------------------------------------------
   
       Controller1>Get-HcsFirmwareVersion
   
       Controller0 : TalladegaFirmware
        ActiveBIOS:0.45.0010
           BackupBIOS:0.45.0006
           MainCPLD:17.0.000b
           ActiveBMCRoot:2.0.001F
           BackupBMCRoot:2.0.001F
           BMCBoot:2.0.0002
           LsiFirmware:20.00.04.00
           LsiBios:07.37.00.00
           Battery1Firmware:06.2C
           Battery2Firmware:06.2C
           DomFirmware:X231600
           CanisterFirmware:3.5.0.56
           CanisterBootloader:5.03
           CanisterConfigCRC:0x9134777A
           CanisterVPDStructure:0x06
           CanisterGEMCPLD:0x19
           CanisterVPDCRC:0x142F7DC2
           MidplaneVPDStructure:0x0C
           MidplaneVPDCRC:0xA6BD4F64
           MidplaneCPLD:0x10
           PCM1Firmware:1.00|1.05
           PCM1VPDStructure:0x05
           PCM1VPDCRC:0x41BEF99C
           PCM2Firmware:1.00|1.05
           PCM2VPDStructure:0x05
           PCM2VPDCRC:0x41BEF99C

        EbodFirmware
           CanisterFirmware:3.5.0.56
           CanisterBootloader:5.03
           CanisterConfigCRC:0xB23150F8
           CanisterVPDStructure:0x06
           CanisterGEMCPLD:0x14
           CanisterVPDCRC:0xBAA55828
           MidplaneVPDStructure:0x0C
           MidplaneVPDCRC:0xA6BD4F64
           MidplaneCPLD:0x10
           PCM1Firmware:3.11
           PCM1VPDStructure:0x03
           PCM1VPDCRC:0x6B58AD13
           PCM2Firmware:3.11
           PCM2VPDStructure:0x03
           PCM2VPDCRC:0x6B58AD13

        DisksFirmware
           SmrtStor:TXA2D20800GA6XYR:KZ50
           SmrtStor:TXA2D20800GA6XYR:KZ50
           SmrtStor:TXA2D20800GA6XYR:KZ50
           SmrtStor:TXA2D20800GA6XYR:KZ50
           SmrtStor:TXA2D20800GA6XYR:KZ50
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
           WD:WD4001FYYG-01SL3:VR08
    ```
   
    Kör `Get-HcsFirmwareVersion`-kommandot på den andra styrenheten för att verifiera att programvaruversionen har uppdaterats. Du kan sedan avsluta underhållsläget. För att göra det anger du följande kommando för varje enhetsstyrenhet:
   
   `Exit-HcsMaintenanceMode`

5. Styrenheterna startas om när du avslutar underhållsläget. När diskens inbyggda uppdateringar har tillämpats och enheten har avslutat underhålls läget går du tillbaka till Azure Portal. Observera att portalen kanske inte visar att du har installerat uppdateringarna i underhållsläge på 24 timmar.

