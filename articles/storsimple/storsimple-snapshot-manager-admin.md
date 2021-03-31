---
title: StorSimple-Snapshot Manager administration | Microsoft Docs
description: Innehåller en översikt och länkar till mer information om StorSimple-Snapshot Manager lösnings administrations uppgifter och arbets flöden.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: alkohli
ms.openlocfilehash: 16d782c6a25d8d2d8e1323b7cb683f41d8c99649
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90054984"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Använd StorSimple-Snapshot Manager för att administrera din StorSimple-lösning

## <a name="overview"></a>Översikt
StorSimple Snapshot Manager är en snapin-modul för Microsoft Management Console (MMC) som fören klar data skydd och säkerhets kopierings hantering i en Microsoft Azure StorSimple miljö. Med StorSimple Snapshot Manager kan du hantera Microsoft Azure StorSimple data i Data Center och i molnet som en enda integrerad lagrings lösning, vilket fören klar säkerhets kopierings processerna och minskar kostnaderna.

Med StorSimple Snapshot Manager Central Management Console kan du skapa konsekventa säkerhets kopior av lokala data och moln data vid olika tidpunkter. Du kan till exempel använda-konsolen för att:

* Konfigurera, säkerhetskopiera och ta bort volymer.
* Konfigurera volym grupper för att säkerställa att säkerhetskopierade data är programkonsekventa.
* Hantera säkerhets kopierings principer så att data säkerhets kopie ras enligt ett förutbestämt schema.
* Skapa oberoende kopior av data som kan lagras i molnet och som används för haveri beredskap.

Den här artikeln innehåller länkar till självstudier som beskriver StorSimple Snapshot Manager och hur du använder den för att slutföra system administrations uppgifter och arbets flöden.

* Mer information om StorSimple Snapshot Manager-komponenter och arkitektur finns i [Vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) 
* Om du vill hämta StorSimple Snapshot Manager går du till [hämtnings sidan för StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).
* Snapshot Manager distributions procedurer för StorSimple går du till [distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> Du kan inte använda StorSimple Snapshot Manager för att hantera Microsoft Azure StorSimple virtuella matriser (även kallade StorSimple lokala virtuella enheter).


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>StorSimple Snapshot Manager uppgifter och arbets flöden
Du kan använda StorSimple-Snapshot Manager för att övervaka och hantera aktuella, schemalagda och slutförda säkerhets kopierings jobb. Dessutom tillhandahåller StorSimple Snapshot Manager en katalog med upp till 64 slutförda säkerhets kopieringar. Du kan använda katalogen för att hitta och återställa volymer eller enskilda filer. 

| OM DU VILL GÖRA DET HÄR... | ANVÄND DEN HÄR SJÄLVSTUDIEN... |
|:--- |:--- |
| Läs mer om StorSimple Snapshot Manager |[Vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) |
| Installera StorSimple Snapshot Manager<br>Installera om StorSimple Snapshot Manager<br>Ta bort StorSimple Snapshot Manager |[Distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md) |
| Använd StorSimple Snapshot Manager menyer och funktioner:<ul><li>Menyrad</li><li>Verktygsfältet</li><li>Omfattnings fönster</li><li>Resultat fönster</li><li>Åtgärds fönstret</li><li>Tangent bords navigering och genvägar</li></ul> |[StorSimple Snapshot Manager användar gränssnitt](storsimple-use-snapshot-manager.md) |
| Använd de vanliga MMC-funktionerna som ingår i StorSimple Snapshot Manager:<ul><li>Visa</li><li>Nytt fönster från här</li><li>Uppdatera</li><li>Exportera lista</li><li>Help</li></ul> |[Använd MMC-meny åtgärderna i StorSimple Snapshot Manager](storsimple-snapshot-manager-mmc-menu.md) |
| Lägga till eller ersätta en enhet<br>Ansluta en enhet<br>Verifiera importerade volym grupper<br>Uppdatera anslutna enheter<br>Autentisera en enhet<br>Visa enhetsinformation<br>Ta bort en enhets konfiguration<br>Ändra ett enhets lösen ord<br>Ersätta en misslyckad enhet<br> |[Använda StorSimple Snapshot Manager för att ansluta och hantera StorSimple-enheter](storsimple-snapshot-manager-manage-devices.md) |
| Montera volymer<br>Visa information om volymer<br>Ta bort en volym<br>Genomsök volymer på nytt<br>Konfigurera och säkerhetskopiera en enkel volym<br>Konfigurera och säkerhetskopiera en dynamisk speglad volym |[Använd StorSimple Snapshot Manager för att visa och hantera volymer](storsimple-snapshot-manager-manage-volumes.md) |
| Visa volym grupper<br>Skapa en volym grupp<br>Säkerhetskopiera en volym grupp<br>Redigera en volym grupp<br>Ta bort en volym grupp |[Använd StorSimple Snapshot Manager för att skapa och hantera volym grupper](storsimple-snapshot-manager-manage-volume-groups.md) |
| Skapa en säkerhetskopieringspolicy <br>Redigera en princip för säkerhets kopiering<br>Ta bort en princip för säkerhets kopiering |[Använd StorSimple Snapshot Manager för att skapa och hantera säkerhets kopierings principer](storsimple-snapshot-manager-manage-backup-policies.md) |
| Visa och hantera schemalagda säkerhets kopierings jobb<br>Visa och hantera de senaste säkerhets kopierings jobben<br>Visa och hantera säkerhets kopierings jobb som körs för närvarande |[Använd StorSimple Snapshot Manager för att visa och hantera säkerhets kopierings jobb](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Återställa en volym<br>Klona en volym eller volym grupp<br>Ta bort en säkerhets kopia<br>Återställa en fil<br>Återställa StorSimple Snapshot Manager-databasen |[Använda StorSimple Snapshot Manager för att hantera säkerhets kopierings katalogen](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Nästa steg
[Hämta StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

