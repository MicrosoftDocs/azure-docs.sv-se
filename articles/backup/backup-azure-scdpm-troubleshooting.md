---
title: Felsöka System Center Data Protection Manager
description: I den här artikeln hittar du lösningar på problem som kan uppstå när du använder System Center Data Protection Manager.
ms.topic: troubleshooting
ms.date: 01/30/2019
ms.openlocfilehash: 84646f34d905b570855f655465529eb19b717cab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86513820"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Felsöka System Center Data Protection Manager

I den här artikeln beskrivs lösningar för problem som kan uppstå när du använder Data Protection Manager.

Information om den senaste versionen av System Center Data Protection Manager finns i [System Center-dokumentationen](/system-center/dpm/dpm-release-notes). Du kan lära dig mer om stöd för Data Protection Manager i [den här matrisen](/system-center/dpm/dpm-protection-matrix).

## <a name="error-replica-is-inconsistent"></a>Fel: repliken är inkonsekvent

En replik kan vara inkonsekvent av följande orsaker:

- Det gick inte att skapa repliken.
- Det finns problem med ändrings journalen.
- Filtret på volym nivå innehåller fel.
- Käll datorn stängs av på oväntad sätt.
- Synkroniseringsloggen är i flödes loggen.
- Repliken är inkonsekvent.

Lös problemet genom att utföra följande åtgärder:

- Om du vill ta bort inkonsekvent status kör du konsekvens kontrollen manuellt eller schemalägger en daglig konsekvens kontroll.
- Se till att du använder den senaste versionen av Microsoft Azure Backup Server och Data Protection Manager.
- Se till att inställningen för **Automatisk konsekvens** är aktive rad.
- Försök att starta om tjänsterna från kommando tolken. Använd `net stop dpmra` kommandot följt av `net start dpmra` .
- Se till att du uppfyller kraven för nätverks anslutning och bandbredd.
- Kontrol lera att käll datorn stängdes av på oväntad sätt.
- Kontrol lera att disken är felfri och att det finns tillräckligt med utrymme för repliken.
- Se till att det inte finns några dubbla säkerhets kopierings jobb som körs samtidigt.

## <a name="error-online-recovery-point-creation-failed"></a>Fel: det gick inte att skapa en onlineåterställningspunkt

Lös problemet genom att utföra följande åtgärder:

- Se till att du använder den senaste versionen av Azure Backup agenten.
- Försök att skapa en återställnings punkt manuellt i skydds aktivitets avsnittet.
- Se till att du kör en konsekvens kontroll på data källan.
- Se till att du uppfyller kraven för nätverks anslutning och bandbredd.
- När replik data är i ett inkonsekvent tillstånd skapar du en disk återställnings punkt för den här data källan.
- Kontrol lera att repliken finns och att den inte saknas.
- Se till att repliken har tillräckligt med utrymme för att skapa USN-journalen (Update Sequence Number).

## <a name="error-unable-to-configure-protection"></a>Fel: det gick inte att konfigurera skydd

Felet uppstår när Data Protection Manager servern inte kan kontakta den skyddade servern.

Lös problemet genom att utföra följande åtgärder:

- Se till att du använder den senaste versionen av Azure Backup agenten.
- Se till att det finns anslutning (nätverk/brand vägg/proxy) mellan Data Protection Manager-servern och den skyddade servern.
- Om du skyddar en SQL-Server kontrollerar du att inställningen sysadmin är aktive rad i egenskapen för **inloggnings egenskaper**  >  **NT instans\system** . 

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Fel: servern är inte registrerad enligt vad som anges i valvets loggfil

Det här felet uppstår under återställnings processen för Data Protection Manager/Azure Backup-Server data. Valv referens filen som används i återställnings processen tillhör inte Recovery Services valvet för Data Protection Manager/Azure Backup-servern.

Lös problemet genom att utföra följande steg:

1. Ladda ned valvet för valvet från Recovery Services valvet som Data Protection Manager/Azure Backup-servern är registrerad på.
2. Försök att registrera servern med valvet genom att använda den senast nedladdade filen för valvets autentiseringsuppgifter.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Fel: inga återställnings bara data eller den valda servern är inte en Data Protection Manager-Server

Felet uppstår av följande orsaker:

- Inga andra Data Protection Manager/Azure Backup-servrar registreras i Recovery Services-valvet.
- Servrarna har inte överfört metadata.
- Den valda servern är inte en Data Protection Manager/Azure Backup-Server.

När andra Data Protection Manager/Azure Backup-servrar registreras i Recovery Services-valvet utför du följande steg för att lösa problemet:

1. Se till att den senaste Azure Backups agenten är installerad.
2. När du har säkerställt att den senaste agenten är installerad väntar du en dag innan du startar återställnings processen. Jobbet natt säkerhets kopiering överför metadata för alla skyddade säkerhets kopieringar till molnet. Säkerhets kopierings data är sedan tillgängliga för återställning.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Fel: den angivna krypterings lösen frasen matchar inte lösen frasen för servern

Det här felet uppstår under krypterings processen när du återställer Data Protection Manager/Azure Backup-Server data. Krypterings lösen frasen som används i återställnings processen matchar inte serverns krypterings lösen fras. Därför kan agenten inte dekryptera data och återställningen Miss lyckas.

> [!IMPORTANT]
> Om du glömmer bort eller förlorar krypterings lösen frasen finns det inga andra metoder för att återskapa data. Det enda alternativet är att återskapa lösen frasen. Använd den nya lösen frasen för att kryptera framtida säkerhets kopierings data.
>
> När du återställer data ska du alltid ange samma krypterings lösen fras som är kopplad till Data Protection Manager/Azure Backup-servern.
>
