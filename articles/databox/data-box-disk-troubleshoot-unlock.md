---
title: Azure Data Box Disk Felsöka problem med att låsa upp disken | Microsoft Docs
description: Lär dig mer om arbets flöden för fel sökning av problem med upplåsnings verktyget med Azure Data Box Disk. Se fel i Data Box Disk Unlock-verktyget.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 08/05/2020
ms.author: alkohli
ms.openlocfilehash: 866cf01243983863292ada0b086f8f5b2f94e412
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87925569"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>Felsök problem med att låsa upp diskar i Azure Data Box Disk

Den här artikeln gäller för Microsoft Azure Data Box Disk och beskriver de arbets flöden som används för att felsöka eventuella problem när du använder Unlock-verktyget. 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>Fel med verktyget Data Box Disk Unlock


| Felmeddelande/verktygsbeteende      | Rekommendationer                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| Aktuellt .NET Framework stöds inte. De versioner som stöds är 4.5 och senare.<br><br>Verktyget avslutas med ett meddelande.  | .NET 4.5 är inte installerat. Installera .NET 4.5 eller senare på den värddator som kör upplåsningsverktyget för Data Box-disk.                                                                            |
| Det gick inte att låsa upp eller verifiera några volymer. Kontakta Microsoft-supporten.  <br><br>Verktyget misslyckas med att låsa upp eller verifiera låsta enheter. | Verktyget kunde inte låsa upp någon av de låsta enheterna med den angivna nyckeln. Kontakta Microsoft-supporten om du vill ha hjälp.                                                |
| Följande volymer är olåsta och verifierade. <br>Volymenhetsbeteckningar: E:<br>Det gick inte att låsa upp alla volymer med följande nycklar: werwerqomnf, qwerwerqwdfda <br><br>Verktyget låser upp vissa enheter och visar lyckade och misslyckade enhetsbeteckningar.| Slutfördes delvis. Det gick inte att låsa upp några av enheterna med den angivna nyckeln. Kontakta Microsoft-supporten om du vill ha hjälp. |
| Det gick inte att hitta låsta volymer. Kontrollera att den disk som mottogs från Microsoft är korrekt ansluten och är låst.          | Verktyget kan inte hitta några låsta enheter. Antingen är enheterna redan upplåsta eller så identifieras de inte. Kontrollera att enheterna är anslutna och att de är låsta. <br> <br>Du kan också se det här felet om du har formaterat diskarna. Om du har formaterat diskarna är de nu oanvändbara. Kontakta Microsoft-supporten om du vill ha hjälp.                                                          |
| Oåterkalleligt fel: Ogiltig parameter<br>Parameternamn: invalid_arg<br>ANVÄNDNING:<br>DataBoxDiskUnlock /PassKeys:<lista_över_nycklar_separerade_med_semikolon><br><br>Exempel: DataBoxDiskUnlock /PassKeys:nyckel1;nyckel2;nyckel3<br>Exempel: DataBoxDiskUnlock /SystemCheck<br>Exempel: DataBoxDiskUnlock /Help<br><br>/PassKeys:       Hämta den här nyckeln från Azure DataBox-diskbeställning. Nyckeln låser upp dina diskar.<br>/Help:           Det här alternativet ger hjälp om cmdlet-användning och exempel.<br>/SystemCheck:    Det här alternativet kontrollerar om datorn uppfyller kraven för att köra verktyget.<br><br>Tryck på valfri tangent för att avsluta. | En ogiltig parameter har angetts. De enda tillåtna parametrarna är/SystemCheck,/PassKey och/Help.|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>Låsa upp problem för diskar när du använder en Windows-klient

I det här avsnittet beskrivs några av de vanligaste problemen vid distribution av Data Box Disk när du använder en Windows-klient för data kopiering.

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>Problem: det gick inte att låsa upp enheten från BitLocker
 
**Orsak** 

Du har använt lösen ordet i BitLocker-dialogrutan och försöker låsa upp disken via dialog rutan för att låsa upp BitLocker-enheter. Detta fungerar inte.

**Lösning**

Om du vill låsa upp Data Box-enhet diskarna måste du använda verktyget Data Box Disk upplåsning och ange lösen ordet från Azure Portal. Mer information finns i [Självstudier: packa upp, Anslut och lås upp Azure Data Box disk](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey).
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>Problem: det gick inte att låsa upp eller verifiera vissa volymer. Kontakta Microsoft-supporten.
 
**Orsak**

Du kan se följande fel i fel loggen och kan inte låsa upp eller verifiera vissa volymer.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
Det tyder på att du troligen saknar rätt version av Windows PowerShell på Windows-klienten.

**Lösning**

Du kan installera [Windows PowerShell v 5,0](https://www.microsoft.com/download/details.aspx?id=54616) och försöka utföra åtgärden igen.
 
Om du fortfarande inte kan låsa upp volymerna kopierar du loggarna från den mapp som har Data Box Disk Unlock-verktyget och [kontaktar Microsoft Support](data-box-disk-contact-microsoft-support.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [felsöker validerings problem](data-box-disk-troubleshoot.md).
