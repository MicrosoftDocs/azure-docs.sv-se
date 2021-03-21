---
title: Skapa ett support paket för StorSimple 8000-serien
description: Lär dig hur du skapar, dekrypterar och redigerar ett support paket för din StorSimple 8000-serie enhet.
author: alkohli
ms.service: storsimple
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 4a847b273472ecc9d2aaa3993ec9d88aa46f2e7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017176"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Skapa och hantera ett support paket för StorSimple 8000-serien

## <a name="overview"></a>Översikt

Ett StorSimple-stödpaket är en lättanvänd mekanism som samlar in alla relevanta loggar för att hjälpa Microsoft Support med fel sökning av problem med StorSimple-enheter. De insamlade loggarna är krypterade och komprimerade.

Den här självstudien innehåller stegvisa instruktioner för att skapa och hantera support paketet för din StorSimple 8000-serie enhet. Om du arbetar med en virtuell StorSimple-matris går du till [skapa ett logg paket](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Skapa ett support paket

I vissa fall måste du skapa ett support paket manuellt via Windows PowerShell för StorSimple. Exempel:

* Om du behöver ta bort känslig information från dina loggfiler innan du delar med Microsoft Support.
* Om du har problem med att ladda upp paketet på grund av anslutnings problem.

Du kan dela ditt manuellt genererade support paket med Microsoft Support via e-post. Utför följande steg för att skapa ett support paket i Windows PowerShell för StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Så här skapar du ett support paket i Windows PowerShell för StorSimple

1. Starta en Windows PowerShell-session som administratör på den fjärrdator som används för att ansluta till din StorSimple-enhet genom att ange följande kommando:
   
    `Start PowerShell`
2. I Windows PowerShell-sessionen ansluter du till SSAdmin-konsolen för enheten:
   
   1. Skriv följande i kommandotolken:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. I dialog rutan som öppnas anger du enhetens administratörs lösen ord. Standard lösen ordet är _Password1_.
     
      ![Dialog rutan PowerShell-autentiseringsuppgift](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Välj **OK**.
   4. Skriv följande i kommandotolken:
     
      `Enter-PSSession $MS`
3. I sessionen som öppnas anger du lämpligt kommando.
   
   * För nätverks resurser som är lösenordsskyddade anger du:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Du uppmanas att ange ett lösen ord och en krypterings lösen fras (eftersom support paketet är krypterat). Ett support paket skapas sedan i standardmappen (enhets namn som lagts till med aktuellt datum och aktuell tid).
   * För resurser som inte är lösenordsskyddade behöver du inte `-Credential` parametern. Ange följande:
     
       `Export-HcsSupportPackage`
     
       Support paketet skapas för båda styrenheterna i standardmappen. Paketet är en krypterad komprimerad fil som kan skickas till Microsoft Support för fel sökning. Mer information finns i [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Parametrarna för Export-HcsSupportPackage cmdlet

Du kan använda följande parametrar med Export-HcsSupportPackage-cmdleten.

| Parameter | Obligatorisk/valfri | Beskrivning |
| --- | --- | --- |
| `-Path` |Krävs |Används för att ange platsen för den delade nätverksmapp där support paketet placeras. |
| `-EncryptionPassphrase` |Obligatorisk |Används för att ge en lösen fras för att kryptera support paketet. |
| `-Credential` |Valfritt |Använd för att ange autentiseringsuppgifter för den delade nätverksmappen. |
| `-Force` |Valfritt |Används för att hoppa över bekräftelse steget för kryptering av lösen fras. |
| `-PackageTag` |Valfritt |Används för att ange en katalog under *sökvägen* där support paketet placeras. Standardvärdet är [enhets namn]-[aktuellt datum och tid: ÅÅÅÅ-MM-dd-HH-mm-SS]. |
| `-Scope` |Valfritt |Ange som **kluster** (standard) om du vill skapa ett support paket för båda styrenheterna. Om du bara vill skapa ett paket för den aktuella kontrollanten anger du **kontrollant**. |

## <a name="edit-a-support-package"></a>Redigera ett support paket

När du har genererat ett support paket kan du behöva redigera paketet för att ta bort känslig information. Detta kan inkludera volym namn, enhets-IP-adresser och namn på säkerhets kopior från loggfilerna.

> [!IMPORTANT]
> Du kan bara redigera ett support paket som genererades via Windows PowerShell för StorSimple. Du kan inte redigera ett paket som skapats i Azure Portal med tjänsten StorSimple Enhetshanteraren.

Om du vill redigera ett support paket innan du laddar upp det på Microsoft Support-platsen måste du först dekryptera support paketet, redigera filerna och sedan kryptera det igen. Utför följande steg.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Så här redigerar du ett support paket i Windows PowerShell för StorSimple

1. Generera ett support paket enligt beskrivningen ovan i [för att skapa ett support paket i Windows PowerShell för StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Ladda ned skriptet](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokalt på klienten.
3. Importera Windows PowerShell-modulen. Ange sökvägen till den lokala mappen där du laddade ned skriptet. För att importera modulen, ange:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Alla filer är *AES* -filer som är komprimerade och krypterade. För att expandera och dekryptera filer, ange:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Observera att de faktiska fil namns tilläggen nu visas för alla filer.
   
    ![Redigera support paket](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. När du uppmanas att ange lösen frasen för kryptering anger du den lösen fras som du använde när du skapade support paketet.
   
    ```powershell
    cmdlet Open-HcsSupportPackage at command pipeline position 1

    Supply values for the following parameters:EncryptionPassphrase: ****
    ```
6. Bläddra till den mapp som innehåller loggfilerna. Eftersom loggfilerna nu expanderas och dekrypteras har de ursprungliga fil namns tillägg. Ändra dessa filer om du vill ta bort all kundspecifik information, till exempel volym namn och IP-adresser för enheten, och spara filerna.
7. Stäng filerna för att komprimera dem med gzip och kryptera dem med AES-256. Detta är för snabbhet och säkerhet vid överföring av support paketet över ett nätverk. Om du vill komprimera och kryptera filer anger du följande:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Redigera support paket 2](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. När du uppmanas anger du en krypterings lösen fras för det ändrade support paketet.
   
    ```powershell
    cmdlet Close-HcsSupportPackage at command pipeline position 1
    Supply values for the following parameters:EncryptionPassphrase: ****
    ```
9. Skriv ner den nya lösen frasen så att du kan dela den med Microsoft Support när den begärs.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Exempel: redigera filer i ett support paket på en lösenordsskyddad resurs

I följande exempel visas hur du dekrypterar, redigerar och omkrypterar ett support paket.

```powershell
PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

cmdlet Open-HcsSupportPackage at command pipeline position 1

Supply values for the following parameters:

EncryptionPassphrase: ****

PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

cmdlet Close-HcsSupportPackage at command pipeline position 1

Supply values for the following parameters:

EncryptionPassphrase: ****

PS C:\WINDOWS\system32>
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om den [information som samlas in i support paketet](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Lär dig hur du [använder support paket och enhets loggar för att felsöka enhets distributionen](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Lär dig hur du [använder tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

