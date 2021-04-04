---
title: Använda en Azure-filresurs med Windows | Microsoft Docs
description: Lär dig att använda Azure-filresurser med Windows och Windows Server. Använd Azure-filresurser med SMB 3,0 på Windows-installationer som körs lokalt eller på virtuella Azure-datorer.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e64b7efdd430287a7a3a969c5bf62b0c0e2aec9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94626902"
---
# <a name="use-an-azure-file-share-with-windows"></a>Använda en Azure-filresurs med Windows
[Azure Files](storage-files-introduction.md) är Microsofts lättanvända filsystem i molnet. Azure-filresurser kan användas smidigt i Windows och Windows Server. Den här artikeln beskriver överväganden för att använda en Azure-filresurs med Windows och Windows Server.

För att kunna använda en Azure-filresurs utanför den Azure-region som den finns i, till exempel lokalt eller i en annan Azure-region, krävs det att operativsystemet har stöd för SMB 3.0. 

Du kan använda Azure-filresurser i en Windows-installation som körs antingen i en virtuell Azure-dator eller lokalt. Tabellen nedan visar vilka operativsystemversioner som har stöd för användning av filresurser och i vilken miljö:

| Windows-version        | SMB-version | Monteras i Azure VM | Monteras lokalt |
|------------------------|-------------|-----------------------|-----------------------|
| Windows Server 2019 | SMB 3.0 | Ja | Ja |
| Windows 10<sup>1</sup> | SMB 3.0 | Ja | Ja |
| Windows Server, halvårskanal<sup>2</sup> | SMB 3.0 | Ja | Ja |
| Windows Server 2016 | SMB 3.0 | Ja | Ja |
| Windows 8,1 | SMB 3.0 | Ja | Ja |
| Windows Server 2012 R2 | SMB 3.0 | Ja | Ja |
| Windows Server 2012 | SMB 3.0 | Ja | Ja |
| Windows 7<sup>3</sup> | SMB 2.1 | Ja | Inga |
| Windows Server 2008 R2<sup>3</sup> | SMB 2.1 | Ja | Inga |

<sup>1</sup> Windows 10, version 1507, 1607, 1803, 1809, 1903, 1909 och 2004.  
<sup>2</sup> Windows Server, version 1809, 1903, 1909, 2004.  
<sup>3</sup> Normalt Microsoft-Support för Windows 7 och Windows Server 2008 R2 har avslut ATS. Det går bara att köpa ytterligare stöd för säkerhets uppdateringar via [ESU-programmet (Extended Security Update)](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates). Vi rekommenderar starkt att du migrerar dessa operativ system.

> [!Note]  
> Vi rekommenderar alltid den senaste uppdateringen för din version av Windows.

## <a name="prerequisites"></a>Förutsättningar 

Se till att port 445 är öppen: SMB-protokollet kräver att TCP-port 445 är öppen; anslutningar misslyckas om port 445 är blockerad. Du kan kontrol lera om brand väggen blockerar port 445 med `Test-NetConnection` cmdleten. Information om hur du kan kringgå en blockerad 445-port finns i [Orsak 1: port 445 är blockerat](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) i vår guide för Windows fel sökning.

## <a name="using-an-azure-file-share-with-windows"></a>Använda en Azure-filresurs med Windows
Om du vill använda en Azure-filresurs med Windows måste du antingen montera den, vilket innebär att tilldela den en enhetsbeteckning eller en sökväg för monteringspunkt, eller komma åt den via dess [UNC-sökväg](/windows/win32/fileio/naming-a-file). 

I den här artikeln används lagrings konto nyckeln för att komma åt fil resursen. En lagrings konto nyckel är en administratörs nyckel för ett lagrings konto, inklusive administratörs behörighet till alla filer och mappar i fil resursen som du ansluter till, samt för alla fil resurser och andra lagrings resurser (blobbar, köer, tabeller osv.) som finns i ditt lagrings konto. Om detta inte är tillräckligt för din arbets belastning kan [Azure File Sync](storage-sync-files-planning.md) användas, eller så kan du använda [Identity-baserad autentisering över SMB](storage-files-active-directory-overview.md).

Ett vanligt mönster för lyftning och skiftande av verksamhetsspecifika program som förväntar sig en SMB-filresurs till Azure är att använda en Azure-filresurs som ett alternativ till att köra en dedikerad Windows-filserver i en Azure-dator. En viktig aspekt för en lyckad migrering av ett verksamhetsspecifikt program till att använda en Azure-filresurs är att många verksamhetsspecifika program kör i kontexten för ett dedikerat tjänstkonto med begränsade systembehörigheter i stället för den virtuella datorns administratörskonto. Därför måste du se till att du monterar/sparar autentiseringsuppgifterna för Azure-filresursen från kontexten för tjänstkontot i stället för ditt administratörskonto.

### <a name="mount-the-azure-file-share"></a>Montera Azure-filresursen

Azure Portal ger dig ett skript som du kan använda för att montera fil resursen direkt på en värd. Vi rekommenderar att du använder det här angivna skriptet.

Så här hämtar du skriptet:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Navigera till lagrings kontot som innehåller den fil resurs du vill montera.
1. Välj **Filresurser**.
1. Välj den fil resurs som du vill montera.

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="exempel":::

1. Välj **Anslut**.

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="Skärm bild av anslutnings ikonen för din fil resurs.":::

1. Välj den enhets beteckning som resursen ska monteras på.
1. Kopiera det medföljande skriptet.

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="Exempel text":::

1. Klistra in skriptet i ett gränssnitt på den värd som du vill montera fil resursen på och kör den.

Nu har du monterat Azure-filresursen.

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Montera Azure-filresursen med Utforskaren
> [!Note]  
> Observera att följande instruktioner visas på Windows 10 och kan skilja sig en aning på äldre versioner. 

1. Öppna Utforskaren. Du kan göra detta genom att öppna från startmenyn eller använda tangentbordsgenvägen Win+E.

1. Gå till **den här datorn** till vänster i fönstret. Detta ändrar menyerna i menyfliksområdet. Under menyn Dator väljer du **Anslut nätverksenhet**.
    
    ![En skärmbild av den nedrullningsbara menyn "Anslut nätverksenhet"](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. Välj enhets bokstaven och ange UNC-sökvägen. UNC-sökvägarna är `\\<storageAccountName>.file.core.windows.net\<fileShareName>` . Exempel: `\\anexampleaccountname.file.core.windows.net\example-share-name`.
    
    ![En skärmbild av dialogrutan "Anslut nätverksenhet"](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. Använd det lagringskontonamn som börjar med `AZURE\` som användarnamn och en lagringskontonyckel som lösenord.
    
    ![En skärmbild av dialogrutan med nätverksautentiseringsuppgifter](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. Använd Azure-filresurser som du vill.
    
    ![Azure-filresursen är nu monterad](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. När du är redo att demontera Azure-filresursen kan du göra det genom att högerklicka på posten för resursen under **Nätverksplatser** i Utforskaren och välja **Koppla från**.

### <a name="accessing-share-snapshots-from-windows"></a>Komma åt ögonblicksbilder av resurser från Windows
Om du har tagit en resursögonblicksbild, antingen manuellt eller automatiskt via ett skript eller en tjänst som Azure Backup, kan du visa tidigare versioner av en resurs, en katalog eller en viss fil från filresursen i Windows. Du kan ta en ögonblicks bild av en resurs med hjälp av [Azure PowerShell](storage-how-to-use-files-powershell.md), [Azure CLI](storage-how-to-use-files-cli.md)eller [Azure Portal](storage-how-to-use-files-portal.md).

#### <a name="list-previous-versions"></a>Lista över tidigare versioner
Bläddra till det objekt eller överordnade objekt som behöver återställas. Dubbelklicka för att gå till den önskade katalogen. Högerklicka och välj **Egenskaper** på menyn.

![Snabbmeny för en vald katalog](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

Välj **Tidigare versioner** för att visa en lista över resursögonblicksbilder för den här katalogen. Det kan ta några sekunder att läsa in listan, beroende på nätverkshastigheten och antalet resursögonblicksbilder i katalogen.

![Fliken Tidigare versioner](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

Du kan välja **Öppna** om du vill öppna en viss ögonblicksbild. 

![Öppnad ögonblicksbild](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Återställa från en tidigare version
Välj **Återställ** för att rekursivt kopiera innehållet i hela katalogen vid tidpunkten då resursögonblicksbilden skapades till den ursprungliga platsen.

 ![Återställningsknapp i varningsmeddelande](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>Skydda Windows/Windows Server
För att du ska kunna montera en Azure-filresurs i Windows måste port 445 vara tillgänglig. Många organisationer blockerar port 445 på grund av säkerhetsrisker med SMB 1. SMB 1, även kallat CIFS (Common Internet File System), är ett äldre filsystemprotokoll som medföljer Windows och Windows Server. SMB 1 är ett inaktuellt, ineffektivt och framför allt oskyddat protokoll. Den goda nyheten är att Azure Files inte har stöd för SMB 1, och alla versioner av Windows och Windows Server som stöds gör det möjligt att ta bort eller inaktivera SMB 1. Vi [rekommenderar starkt](https://aka.ms/stopusingsmb1) att du alltid tar bort eller inaktiverar SMB 1-klienten och -servern i Windows innan du använder Azure-filresurser i produktion.

I följande tabell finns detaljerad information om status för SMB 1 i varje version av Windows:

| Windows-version                           | SMB 1-standardstatus | Inaktivera/ta bort metod       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019                       | Inaktiverad             | Ta bort med Windows-funktionen |
| Windows Server, versioner 1709+            | Inaktiverad             | Ta bort med Windows-funktionen |
| Windows 10, versioner 1709+                | Inaktiverad             | Ta bort med Windows-funktionen |
| Windows Server 2016                       | Enabled              | Ta bort med Windows-funktionen |
| Windows 10, versionerna 1507, 1607 och 1703 | Enabled              | Ta bort med Windows-funktionen |
| Windows Server 2012 R2                    | Enabled              | Ta bort med Windows-funktionen | 
| Windows 8,1                               | Enabled              | Ta bort med Windows-funktionen | 
| Windows Server 2012                       | Enabled              | Inaktivera med registret       | 
| Windows Server 2008 R2                    | Enabled              | Inaktivera med registret       |
| Windows 7                                 | Enabled              | Inaktivera med registret       | 

### <a name="auditing-smb-1-usage"></a>Granskning av SMB 1-användning
> Gäller för Windows Server 2019, Windows Server halvårs kanal (version 1709 och 1803), Windows Server 2016, Windows 10 (version 1507, 1607, 1703, 1709 och 1803), Windows Server 2012 R2 och Windows 8,1

Innan du tar bort SMB 1 i din miljö kan det vara bra att granska användning av SMB 1 för att se om några klienter kommer att störas av ändringen. Om alla begäranden görs mot SMB-resurser med SMB 1 loggas en granskningshändelse i händelseloggen i `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit`. 

> [!Note]  
> Om du vill aktivera stöd för granskning på Windows Server 2012 R2 och Windows 8.1 installerar du minst [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720).

Du aktiverar granskning genom att köra följande cmdlet från en upphöjd PowerShell-session:

```powershell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>Ta bort SMB 1 från Windows Server
> Gäller för Windows Server 2019, halvårs kanal för Windows Server (version 1709 och 1803), Windows Server 2016, Windows Server 2012 R2

Om du vill ta bort SMB 1 från en Windows Server-instans kör du följande cmdlet från en upphöjd PowerShell-session:

```powershell
Remove-WindowsFeature -Name FS-SMB1
```

Starta om servern för att slutföra borttagningsprocessen. 

> [!Note]  
> Från och med Windows 10 och Windows Server version 1709 installeras inte SMB 1 som standard, och det har separata Windows-funktioner för SMB 1-klienten och SMB 1-servern. Vi rekommenderar att du alltid lämnar både SMB 1-servern (`FS-SMB1-SERVER`) och SMB 1-klienten (`FS-SMB1-CLIENT`) avinstallerade.

### <a name="removing-smb-1-from-windows-client"></a>Ta bort SMB 1 från Windows-klient
> Gäller för Windows 10 (versionerna 1507, 1607, 1703, 1709 och 1803) samt Windows 8.1

Om du vill ta bort SMB 1 från din Windows-klient kör du följande cmdlet från en upphöjd PowerShell-session:

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

Starta om datorn för att slutföra borttagningsprocessen.

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>Inaktivera SMB 1 på äldre versioner av Windows/Windows Server
> Gäller för Windows Server 2012, Windows Server 2008 R2 och Windows 7

SMB 1 kan inte tas bort helt och hållet i äldre versioner av Windows/Windows Server, men det kan inaktiveras via registret. Om du vill inaktivera SMB 1 skapar du en ny registernyckel `SMB1` av typen `DWORD` med värdet `0` i `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters`.

Det är även enkelt att göra detta med följande PowerShell-cmdlet:

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

När du har skapat den här registernyckeln måste du starta om servern för att inaktivera SMB 1.

### <a name="smb-resources"></a>SMB-resurser
- [Sluta använda SMB 1](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/)
- [Clearinghouse för SMB 1-produkt](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/)
- [Identifiera SMB 1 i din miljö med DSCEA](/archive/blogs/ralphkyttle/discover-smb1-in-your-environment-with-dscea)
- [Inaktivera SMB 1 via en grupprincip](/archive/blogs/secguide/disabling-smbv1-through-group-policy)

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Files finns på följande länkar:
- [Planera för en Azure Files-distribution](storage-files-planning.md)
- [Vanliga frågor och svar](./storage-files-faq.md)
- [Felsökning i Windows](storage-troubleshoot-windows-file-connection-problems.md)