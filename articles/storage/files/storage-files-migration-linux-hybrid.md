---
title: Linux-migrering till Azure File Sync
description: Lär dig hur du migrerar filer från en Linux-server plats till en hybrid moln distribution med Azure File Sync och Azure-filresurser.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: f95585237bbee743083b855dd78cc850c4daffe8
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202696"
---
# <a name="migrate-from-linux-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migrera från Linux till en hybrid moln distribution med Azure File Sync

Azure File Sync fungerar på Windows Server-instanser med direktansluten lagring (DAS). Den stöder inte synkronisering till och från Linux-klienter, eller en SMB-resurs (Server Message Block) eller NFS-resurser (Network File System).

Det innebär att du måste omvandla dina fil tjänster till en hybrid distribution och göra en migrering till Windows Server. Den här artikeln vägleder dig genom planeringen och körningen av en sådan migrering.

## <a name="migration-goals"></a>Migreringsmål

Målet är att flytta de resurser som du har på din Linux-Samba server till en Windows Server-instans. Använd sedan Azure File Sync för en hybrid moln distribution. Migreringen måste göras på ett sätt som garanterar integriteten för produktions data samt tillgänglighet under migreringen. Den sistnämnda lösningen kräver att drift stoppen är minimal, så att den får plats i eller endast något högre än vanliga underhålls perioder.

## <a name="migration-overview"></a>Översikt över migrering

Som vi nämnt i [artikeln Azure Files migrerings översikt](storage-files-migration-overview.md)är det viktigt att använda rätt kopierings verktyg och metod. Din Linux-Samba server exponerar SMB-resurser direkt i det lokala nätverket. Robocopy, som är inbyggt i Windows Server, är det bästa sättet att flytta filer i det här scenariot för migrering.

Om du inte kör Samba på Linux-servern och hellre vill migrera mappar till en hybrid distribution på Windows Server, kan du använda Linux-sökverktyg i stället för Robocopy. Om du gör det bör du vara medveten om åter givningen av funktionerna i fil kopierings verktyget. Läs [avsnittet grundläggande om migreringar](storage-files-migration-overview.md#migration-basics) i översikts artikeln om migreringen för att lära dig vad du ska titta efter i ett kopierings verktyg.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fas 1: identifiera hur många Azure-filresurser du behöver

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-instance-on-premises"></a>Fas 2: etablera en lämplig Windows Server-instans lokalt

* Skapa en Windows Server 2019-instans som en virtuell dator eller fysisk server. Windows Server 2012 R2 är minimi kravet. Det finns också stöd för ett Windows Server-redundanskluster.
* Etablera eller Lägg till direktansluten lagring (DAS). NAS (Network Attached Storage) stöds inte.

  Mängden lagrings utrymme som du etablerar kan vara mindre än det som du för närvarande använder på din lokala Linux-server, om du använder funktionen Azure File Sync [moln nivåer](storage-sync-cloud-tiering-overview.md) . Men när du kopierar dina filer från det större Linux-Samba server utrymmet till den mindre Windows Server-volymen i en senare fas måste du arbeta i batchar:

  1. Flytta en uppsättning filer som passar på disken.
  2. Låt filsynkronisering och moln nivåer engagera.
  3. När mer ledigt utrymme skapas på volymen fortsätter du med nästa batch med filer. 
    
  Du kan undvika den här batch-metoden genom att tillhandahålla motsvarande utrymme på den Windows Server-instans som filerna upptar på Linux-servern. Överväg att aktivera deduplicering i Windows. Om du inte vill spara den här stora mängden lagrings utrymme på en Windows Server-instans permanent kan du minska volym storleken efter migreringen och innan du justerar principerna för moln nivå. Detta skapar en mindre lokal cache för dina Azure-filresurser.

Resurs konfigurationen (beräkning och RAM) för Windows Server-instansen som du distribuerar beror främst på antalet objekt (filer och mappar) som du kommer att synkronisera. Vi rekommenderar att du fortsätter med en konfiguration med högre prestanda om du har problem.

[Lär dig hur du ändrar storlek på en Windows Server-instans baserat på antalet objekt (filer och mappar) som du behöver synkronisera.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> Den tidigare länkade artikeln visar en tabell med ett intervall för server minne (RAM). Du kan orientera dig mot det mindre antalet för servern, men vi tror att den första synkroniseringen kan ta betydligt längre tid.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Fas 3: Distribuera Azure File Sync moln resurs

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Fas 4: Distribuera Azure Storage-resurser

I den här fasen, se mappnings tabellen från fas 1 och Använd den för att etablera rätt antal Azure Storage-konton och fil resurser i dem.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Fas 5: Distribuera Azure File Sync agent

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server-deployment"></a>Fas 6: Konfigurera Azure File Sync på Windows Server-distributionen

Din registrerade lokala Windows Server-instans måste vara klar och ansluten till Internet för den här processen.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Moln nivåer är Azure File Sync funktion som gör det möjligt för den lokala servern att ha mindre lagrings kapacitet än vad som lagras i molnet, men som har det fullständiga namn området tillgängligt. Lokalt intressanta data cachelagras också lokalt för snabb åtkomst. Moln nivåer är en valfri funktion för varje Azure File Sync Server-slutpunkt.

> [!WARNING]
> Om du har allokerat mindre lagrings utrymme på dina Windows Server-volymer än dina data som används på den Samba-servern i Linux är moln nivå obligatorisk. Om du inte aktiverar moln nivåer frigör inte servern utrymme för att lagra alla filer. Ange din nivå princip tillfälligt för migreringen till 99 procent ledigt utrymme för en volym. Se till att gå tillbaka till inställningarna för moln nivåer när migreringen är klar och ange en mer användbar nivå för lång sikt.

Upprepa stegen i skapa synkroniseringskoppling och addition av matchande servermapp som en server slut punkt för alla Azure-filresurser och server platser som måste konfigureras för synkronisering.

När alla Server slut punkter har skapats fungerar synkroniseringen. Du kan skapa en testfil och se den synkroniseras från din server plats till den anslutna Azure-filresursen (som beskrivs av moln slut punkten i synkroniseringsresursen).

Både platser, servermappar och Azure-filresurser är i övrigt tomma och väntar på data. I nästa steg ska du börja kopiera filer till Windows Server-instansen för Azure File Sync att flytta dem till molnet. Om du har aktiverat moln skiktning kommer servern att börja på nivå-filer om du får slut på kapacitet på de lokala volymerna.

## <a name="phase-7-robocopy"></a>Fas 7: Robocopy

Den grundläggande migrations metoden är att använda Robocopy för att kopiera filer och använda Azure File Sync för att synkronisera.

Kör den första lokala kopian till din Windows Server-målmapp:

1. Identifiera den första platsen på din Linux-Samba server.
1. Identifiera matchande mapp på den Windows Server-instans som redan har Azure File Sync konfigurerat.
1. Starta kopian med hjälp av Robocopy.

Följande Robocopy-kommando kommer att kopiera filer från din servers lagring för Linux-Samba till din Windows Server-målmapp. Windows Server synkroniserar det med Azure-filresurserna. 

Om du har allokerat mindre lagrings utrymme på Windows Server-instansen än vad filerna tar upp på den Samba-servern för Linux, har du konfigurerat moln nivåer. När den lokala Windows Server-volymen blir full startar [moln skiktning](storage-sync-cloud-tiering-overview.md) och filer på nivån som redan har synkroniserats. Moln skiktet genererar tillräckligt med utrymme för att kunna fortsätta med kopian från den Samba-server som används av Linux. Moln nivåer utförs en gång i timmen för att se vad som har synkroniserats och för att frigöra disk utrymme för att uppnå principen på 99 procent ledigt utrymme för en volym.

Det är möjligt att Robocopy flyttar filer snabbare än att du kan synkronisera till molnet och nivån lokalt, vilket gör att du får slut på det lokala disk utrymmet. Robocopy kommer sedan att Miss sen. Vi rekommenderar att du arbetar genom resurserna i en sekvens som förhindrar problemet. Överväg till exempel att inte starta Robocopy-jobb för alla resurser på samma gång. Eller Överväg att flytta resurser som passar på den aktuella mängden ledigt utrymme på Windows Server-instansen. Om ditt Robocopy-jobb Miss Miss kan du alltid köra kommandot igen så länge du använder följande alternativ för spegling/rensning:

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Lägg

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Tillåter att Robocopy körs med flera trådar. Standardvärdet är 8, maximum är 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:\<file name\>
   :::column-end:::
   :::column span="1":::
      Matar ut status till en loggfil som Unicode (skriver över den befintliga loggen).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Utdata till ett konsol fönster. Används tillsammans med utdata i en loggfil.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Kör Robocopy i samma läge som ett säkerhets kopierings program använder. Det gör att Robocopy kan flytta filer som den aktuella användaren inte har behörighet till.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Tillåter körning av detta Robocopy-kommando flera gånger, i turordning, på samma mål/mål. Den identifierar och utelämnar det som har kopierats tidigare. Endast ändringar, tillägg och borttagningar som har gjorts sedan den senaste körningen bearbetades. Om kommandot inte kördes förut utelämnas inget. **/Mir** -flaggan är ett utmärkt alternativ för käll platser som fortfarande används aktivt och ändras.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      Åter givningen av fil kopian (standard är/COPY: DAT). Kopierings flaggor är: D = data, A = attribut, T = tidsstämplar, S = Security = NTFS ACL: er, O = ägar information, U = gransknings information.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      Kopiera ALL fil information (motsvarande/COPY: DATSOU).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      Åter givning för kopian av kataloger (standard är/DCOPY: DA). Kopierings flaggor är: D = data, A = attribut, T = tidsstämplar.
   :::column-end:::
:::row-end:::

## <a name="phase-8-user-cut-over"></a>Fas 8: överhuggen av användare

När du kör Robocopy-kommandot för första gången kommer dina användare och program fortfarande att komma åt filer på Linux-Samba-servern och eventuellt ändra dem. Det är möjligt att Robocopy har bearbetat en katalog och flyttar till nästa, och att en användare på käll platsen (Linux) lägger till, ändrar eller tar bort en fil som nu inte bearbetas i den aktuella Robocopy-körningen. Det här beteendet är förväntat.

Den första körningen är att flytta data mängden till din Windows Server-instans och till molnet via Azure File Sync. Den första kopian kan ta lång tid, beroende på:

* Din nedladdnings bandbredd.
* Överförings bandbredden.
* Den lokala nätverks hastigheten och antalet optimala trådar som matchar antalet Robocopy-trådar.
* Antalet objekt (filer och mappar) som Robocopy och Azure File Sync måste bearbeta.

När den första körningen har slutförts kör du kommandot igen.

Det går snabbare den andra gången, eftersom det bara behöver transportera ändringar som skett sedan den senaste körningen. Under den här sekunden kan nya ändringar ändå ackumuleras.

Upprepa den här processen tills du är nöjd med hur lång tid det tar att slutföra en Robocopy-åtgärd för en bestämd plats i ett acceptabelt fönster för stillestånds tid.

När du anser att det är acceptabelt och du är redo att ta Linux-platsen offline, kan du ändra ACL: er på resurs roten så att användarna inte längre kan komma åt platsen. Eller så kan du vidta andra lämpliga steg som förhindrar att innehåll ändras i den här mappen på Linux-servern.

Kör en sista Robocopy-avrundning. De kommer att hämta alla ändringar som kan ha missats. Hur lång tid det sista steget tar beror på hastigheten på Robocopy-genomsökningen. Du kan uppskatta tiden (som är lika med din stillestånds tid) genom att mäta hur lång tid den senaste körningen tog.

Skapa en resurs i Windows Server-mappen och eventuellt ändra DFS-N-distributionen så att den pekar på den. Se till att ange samma behörigheter på resurs nivå som på dina Linux-Samba server SMB-resurser. Om du har använt lokala användare på din server för Linux-Samba måste du återskapa dessa användare som Windows Server lokala användare. Du måste också mappa befintliga sid som Robocopy flyttas över till din Windows Server-instans till sid för dina nya lokala Windows Server-användare. Om du använde Active Directory konton och åtkomst kontrol listor flyttar Robocopy dem som de är och ingen ytterligare åtgärd krävs.

Du har avslutat migreringen av en resurs eller en grupp med resurser till en gemensam rot eller volym (beroende på mappningen från fas 1).

Du kan försöka att köra några av dessa kopior parallellt. Vi rekommenderar att du bearbetar omfånget för en Azure-filresurs i taget.

> [!WARNING]
> När du har flyttat alla data från din Linux-Samba-server till Windows Server-instansen och migreringen är klar, återgår du till *alla*  Sync-grupper i Azure Portal. Justera procent andelen ledigt utrymme på moln lagrings volym till något som passar bättre för cachelagring, till exempel 20 procent. 

Principen för ledigt utrymme i moln skikts volym fungerar på en volym nivå med eventuellt flera Server slut punkter som synkroniseras från den. Om du glömmer att justera det lediga utrymmet på en server slut punkt fortsätter synkroniseringen att tillämpa den mest restriktiva regeln och försöker att behålla ledigt disk utrymme på 99 procent. Den lokala cachen kanske inte fungerar som förväntat. Prestandan kan vara acceptabel om målet är att ha namn området för en volym som bara innehåller arkiverade data som sällan används och du reserverar resten av lagrings utrymmet för ett annat scenario.

## <a name="troubleshoot"></a>Felsöka

Det vanligaste problemet är att Robocopy-kommandot Miss lyckas med **volymen full** på Windows Server-sidan. Moln nivåer fungerar en gång per timme för att evakuera innehåll från den lokala Windows Server-disken som har synkroniserats. Målet är att uppnå ledigt utrymme på 99 procent på volymen.

Låt synkroniseringen fortskrida och moln nivån frigör disk utrymme. Du kan titta på det i Utforskaren på Windows Server.

När Windows Server-instansen har tillräckligt med tillgänglig kapacitet kommer det att lösa problemet genom att köra kommandot igen. Ingenting bryts när du får den här situationen och du kan gå vidare med förtroende. Besväret med att köra kommandot igen är den enda konsekvensen.

Kontrol lera länken i följande avsnitt för att felsöka Azure File Sync problem.

## <a name="next-steps"></a>Nästa steg

Det finns mer att identifiera om Azure-filresurser och Azure File Sync. Följande artiklar innehåller avancerade alternativ, metod tips och fel söknings hjälp. De här artiklarna länkar till [dokumentationen för Azure-filresursen](storage-files-introduction.md) efter behov.

* [Översikt över Azure File Sync](./storage-sync-files-planning.md)
* [Azure File Sync distributions guide](./storage-how-to-create-file-share.md)
* [Felsöka Azure File Sync](storage-sync-files-troubleshoot.md)