---
title: Gransknings loggar för Azure Data Box Azure Data Box Heavy händelser | Microsoft Docs
description: Beskriver de fullständiga gransknings loggarna för Data Box-enhet som samlas in i de olika stegen i Azure Data Box och Azure Data Box Heavys ordning.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 50dbe9ab649a708fb36b1c9e4fe89bccadc7ea90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86209979"
---
# <a name="audit-logs-for-your-azure-data-box-and-azure-data-box-heavy"></a>Gransknings loggar för din Azure Data Box och Azure Data Box Heavy

Loggar är oföränderliga, tidsstämplade poster med diskreta händelser som har hänt över tid. Loggarna innehåller diagnostik-, gransknings-och säkerhets information från din enhet.  

En Data Box-enhet-eller Data Box Heavys ordning går igenom följande steg under driften: order, konfiguration, data kopiering, retur, uppladdning till Azure och verifiera och data radering. För vart och ett av dessa steg granskas och loggas alla händelser.

Den här artikeln innehåller information om Data Box-enhet gransknings loggar, inklusive typer av loggar och information som samlas in samt platsen för loggarna. 

Informationen i den här artikeln gäller både för Data Box-enhet och Data Box Heavy. I följande avsnitt gäller alla referenser till Data Box-enhet även för Data Box Heavy. Loggarna som samlas in från Data Box-enhet-tjänsten som körs i Azure omfattas inte av den här artikeln. 


## <a name="about-audit-logs"></a>Om gransknings loggar 

I Data Box-enhet samlas följande loggar in:

- **System loggar** – data Box-enhet en Windows-baserad enhet loggas alla maskin vara, program vara och system händelser. En uppsättning händelser samlas in och rapporteras i system gransknings loggarna. 

- **Säkerhets** -data Box-enhet en Windows-baserad enhet loggas alla säkerhets händelser. En uppsättning händelser samlas in och rapporteras i säkerhets gransknings loggarna. 

- **Program** – dessa loggar är specifika för endast data Box-enhet. Dessa loggar innehåller alla händelser som genereras på enheten som svar på de Data Box-enhet-tjänster som körs.

Var och en av dessa loggar beskrivs i följande avsnitt.

### <a name="system-logs"></a>System loggar

Följande händelse-ID: n för system loggen samlas in som system gransknings loggar på din Data Box-enhet:

|Namn på Händelseprovidern     |Händelse-ID samlades in   |Händelsebeskrivning   |
|-------------------|----------|----------------|
|Microsoft-Windows-kernel-allmänt|12  |UTC-tid när operativ systemet startades om.   |
|                                |13  |UTC-tid när operativ systemet stängdes. |
|    |                              |
|Microsoft-Windows-kernel-power  |41  |Systemet har startats om utan en ren avstängning.| 
|    |                              |
|Microsoft-Windows-BitLocker-driv rutin|Alla|    |

### <a name="security-logs"></a>Säkerhets loggar

Följande händelse-ID: n för säkerhets loggar samlas in som säkerhets gransknings loggar på din Data Box-enhet:

|Namn på Händelseprovidern                   |Händelse-ID samlades in    |Händelsebeskrivning       |
|--------------------------------------|------------|----------|
|Microsoft-Windows-Security-Auditing   |4624        |Lyckad inloggning. |
|                                      |4625        |En konto inloggning misslyckades. Okänt användar namn eller felaktigt lösen ord. |
|                                     

### <a name="application-logs"></a>Program loggar

Följande händelse-ID: n för program loggar samlas in som en del av paket gransknings loggarna på din Data Box-enhet.     

- **Microsoft-Azure-Data-rutan-OOBE-Auditing** -innehåller de händelser som inträffar i det lokala användar gränssnittet. 
- **Microsoft-Azure-Data-reetablerad-audit** -innehåller händelser relaterade till reetablering av den data Box-enhet enheten. Ometableringen av Data Box-enhet inträffar när enheten återställs via det lokala användar gränssnittet. Du väljer det här alternativet om du vill radera de data som du har kopierat genom att ta bort de befintliga resurserna och återskapa resurserna som en del av reetableringen eller återställning av enheten.
- **Microsoft-Azure-Data-HcsMgmt-audit** -innehåller händelser som är relaterade till steget **Förbered för leverans** innan enheten skickas tillbaka till Azure-datacentret. 
- **Microsoft-Azure-Data-IfxAudit** – innehåller meddelanden som loggats av olika enheter av produkten om jobben, loggar som visar mer information om vad som händer i några av flödena.

Här är en tabell som sammanfattar olika händelse leverantörer och motsvarande händelse-ID som samlas in i varje fall.

|Namn på Händelseprovidern    |Händelse-ID    | Kommentarer |
|-----------------|-----------------|-------------------|
|Microsoft-Azure-data--i-OOBE – granskning |4624        |Lyckad inloggning.|
|                                      |4625        |En konto inloggning misslyckades. Okänt användar namn eller felaktigt lösen ord.|
|                                     |4634        |Logga ut-händelse.|
|                                   |  | |
|Microsoft-Azure-Data-reetablerad-Audit    |65001       |En lyckad reetablerings händelse.|
|                                                  |65002       |Det gick inte att etablera om händelsen.|
|                                                  |                 |         |
|Microsoft-Azure-Data-HcsMgmt-Audit        |65003       |Förbered för att skicka tillstånds händelse NotStarted, InProgress, misslyckades, avbruten, lyckades, ScanCompletedWithIssues, SucceededWithWarnings          |
|                                                  |                 |     |
|Microsoft-Azure-Data-IfxAudit    |Alla |Alla händelser loggas med API för gransknings logg i kod |

Här är ett exempel på gransknings loggen för Instrumentation Framework (IFX):

|     Aktivitet/jobb/API                              |     Händelser som loggats                                                                                                              | 
|-----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
|     Rensa                                   |     Händelser som rör Start, slut för ande eller haveriering av ett rensnings jobb loggas. |                                              
|     Förbered enhet för kund leverans    |     Händelser som rör Start, slut för ande eller utebliven jobb för att förbereda enheten för leverans loggas. |
|     Etablera                                 |     Händelser som rör Start, slut för ande eller haveri för ett enhets etablerings jobb loggas.|
|     Granska paket jobb                       |     Händelser som rör Start, slut för ande eller haveriering av ett gransknings paket jobb som skapar en kedja av vårdnads loggar loggas.|
|     Skriv över disk                          |     Det gick inte att skriva över disken loggas.|
|     Aktivera eller inaktivera fjärr-PowerShell     |     Händelser som rör aktivering eller inaktive ring av fjärr-PowerShell på enheten loggas. |
|     Hämta information om installations fasen               |     Händelser som rör Installation av program vara på enheten i faser loggas i Azure-datacentret.|
|     Låsa upp eller Lås BitLocker-volym           |     Händelserna loggas för att ange BitLocker-status för *basevolume* och *hcsdata* -volym.|
|     Sanerad disk                              |     Händelser som rör fel på fysiska diskar som inte kunde raderas och händelser när alla fysiska diskar på enheten har raderats loggas. |
|     Aktivera eller inaktivera lokal användare               |     Händelser som rör aktivering eller inaktive ring av lokala användar konton för StorSimpleAdmin och PodSupportAdminUser loggas.| 
|     Lösen ords återställning                          |     Händelserna som rör lyckade eller misslyckade lösen ords återställning för den lokala StorSimpleAdmin-användaren loggas. |


Förutom gransknings loggarna för IFX samlas även gransknings loggar för vårdnad till Data Box-enhet. Dessa loggar kan inte visas i real tid, men endast efter att jobbet har slutförts och data raderas från Data Box-enhet diskar. Dessa loggar innehåller en delmängd av den information som finns i gransknings loggarna för IFX.

Mer information om kedjan med detalj gransknings loggar finns i [Hämta kedja av vårdnads loggar efter data radering](data-box-logs.md#get-chain-of-custody-logs-after-data-erasure).

<!-- write a few lines about order history and link out to the detailed section on order history-->

## <a name="access-audit-logs"></a>Åtkomst till granskningsloggar

Dessa loggar lagras i Azure och går inte att komma åt direkt. Om du behöver åtkomst till dessa loggar kan du läsa in ett support ärende. Mer information finns i [kontakta Microsoft Support](data-box-disk-contact-microsoft-support.md). 

När support ärendet har arkiverats kommer Microsoft att hämta och ge dig åtkomst till dessa loggar.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [felsöker problem på data Box-enhet och data Box Heavy](data-box-troubleshoot.md).
