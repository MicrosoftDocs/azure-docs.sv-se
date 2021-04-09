---
title: Lokal NAS-migrering till Azure File Sync
description: Lär dig hur du migrerar filer från en lokal NAS-plats (Network Attached Storage) till en hybrid moln distribution med Azure File Sync och Azure-filresurser.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 86e79302716fa502d8562dd563b0a5c5fb220a67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102547567"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migrera från nätverksansluten lagring (NAS) till en hybrid moln distribution med Azure File Sync

Den här migreringen är en av flera som inbegriper nyckelorden NAS och Azure File Sync. Kontrol lera om den här artikeln gäller ditt scenario:

> [!div class="checklist"]
> * Data Källa: nätverksansluten lagring (NAS)
> * Migreringsjobb: NAS &rArr; Windows Server &rArr; upload och Sync med Azure-filresurs (er)
> * Cachelagra filer lokalt: Ja, det slutliga målet är en Azure File Sync distribution.

Om ditt scenario skiljer sig kan du titta igenom [tabellen med migrations guider](storage-files-migration-overview.md#migration-guides).

Azure File Sync fungerar på DAS-platser (Direct Attached Storage) och stöder inte synkronisering till NAS-platser (Network Attached Storage).
Detta innebär en migrering av dina filer som behövs och den här artikeln vägleder dig genom planering och körning av en sådan migrering.

## <a name="migration-goals"></a>Migreringsmål

Målet är att flytta de resurser som du har på din NAS-installation till en Windows-Server. Använd sedan Azure File Sync för en hybrid moln distribution. I allmänhet måste migreringar göras på ett sätt som Guaranty integriteten för produktions data och dess tillgänglighet under migreringen. Den sistnämnda lösningen kräver att drift stoppen är minimal, så att den får plats i eller endast något högre än vanliga underhålls perioder.

## <a name="migration-overview"></a>Översikt över migrering

Som vi nämnt i [artikeln Azure Files migrerings översikt](storage-files-migration-overview.md)är det viktigt att använda rätt kopierings verktyg och metod. NAS-enheten exponerar SMB-resurser direkt i det lokala nätverket. RoboCopy, inbyggda Windows Server, är det bästa sättet att flytta filer i det här scenariot för migrering.

- Fas 1: [identifiera hur många Azure-filresurser du behöver](#phase-1-identify-how-many-azure-file-shares-you-need)
- Fas 2: [etablera en lämplig Windows Server lokalt](#phase-2-provision-a-suitable-windows-server-on-premises)
- Fas 3: [distribuera Azure File Sync moln resurs](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- Fas 4: [Distribuera Azure Storage-resurser](#phase-4-deploy-azure-storage-resources)
- Fas 5: [distribuera Azure File Sync agent](#phase-5-deploy-the-azure-file-sync-agent)
- Fas 6: [konfigurera Azure File Sync på Windows Server](#phase-6-configure-azure-file-sync-on-the-windows-server)
- Fas 7: [Robocopy](#phase-7-robocopy)
- Fas 8: [överhuggen av användare](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fas 1: identifiera hur många Azure-filresurser du behöver

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>Fas 2: etablera en lämplig Windows Server lokalt

* Skapa en Windows Server 2019 – minst 2012R2 – som en virtuell dator eller fysisk server. Ett kluster för växling vid fel i Windows Server stöds också.
* Etablera eller Lägg till direktansluten lagring (DAS jämfört med NAS, vilket inte stöds).

    Mängden lagrings utrymme som du etablerar kan vara mindre än det som du för närvarande använder på din NAS-enhet. Det här konfigurations valet kräver att du också använder Azure File syncs-funktionen för [moln nivåer](storage-sync-cloud-tiering-overview.md) .
    Men när du kopierar dina filer från det större NAS-utrymmet till den mindre Windows Server-volymen i en senare fas måste du arbeta i batchar:

    1. Flytta en uppsättning filer som passar på disken
    2. Låt filsynkronisering och moln nivå engagera
    3. När mer ledigt utrymme skapas på volymen fortsätter du med nästa batch med filer. Du kan också granska RoboCopy-kommandot i [avsnittet kommande Robocopy](#phase-7-robocopy) för att använda den nya `/LFSM` växeln. Med `/LFSM` kan du förenkla dina Robocopy-jobb avsevärt, men det är inte kompatibelt med andra Robocopy-växlar som du kan bero på.
    
    Du kan undvika den här batch-metoden genom att tillhandahålla motsvarande utrymme på Windows-servern som dina filer använder på NAS-enheten. Överväg deduplicering på NAS/Windows. Om du inte vill spara den här stora mängden lagrings utrymme på Windows Server permanent kan du minska volym storleken efter migreringen och innan du justerar principerna för moln nivå. Detta skapar en mindre lokal cache för dina Azure-filresurser.

Resurs konfigurationen (beräkning och RAM) för den Windows Server som du distribuerar beror främst på antalet objekt (filer och mappar) som du kommer att synkronisera. Vi rekommenderar en högre prestanda konfiguration om du har problem.

[Lär dig hur du ändrar storlek på en Windows-Server baserat på antalet objekt (filer och mappar) som du behöver synkronisera.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> Den tidigare länkade artikeln visar en tabell med ett intervall för server minne (RAM). Du kan orientera dig mot det mindre antalet för servern, men vi tror att den inledande synkroniseringen kan ta betydligt längre tid.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Fas 3: Distribuera Azure File Sync moln resurs

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Fas 4: Distribuera Azure Storage-resurser

I den här fasen, se mappnings tabellen från fas 1 och Använd den för att etablera rätt antal Azure Storage-konton och fil resurser i dem.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Fas 5: Distribuera Azure File Sync agent

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>Fas 6: Konfigurera Azure File Sync på Windows Server

Din registrerade lokala Windows Server måste vara klar och ansluten till Internet för den här processen.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Moln lagring är en AFS-funktion som gör det möjligt för den lokala servern att ha mindre lagrings kapacitet än vad som lagras i molnet, men det fullständiga namn området är tillgängligt. Lokalt intressanta data cachelagras också lokalt för snabb åtkomst. Moln nivåer är en valfri funktion per Azure File Sync Server slut punkt.

> [!WARNING]
> Om du har upprättat mindre lagrings utrymme på dina Windows Server-volymer än dina data som används på NAS-enheten är moln nivå obligatorisk. Om du inte aktiverar moln nivåer frigör inte servern utrymme för att lagra alla filer. Ange din nivå princip tillfälligt för migreringen till 99% ledigt utrymme på volymen. Se till att gå tillbaka till inställningarna för moln nivåer när migreringen är klar och ange en mer långsiktig användbar nivå.

Upprepa stegen för att skapa och lägga till matchande servermapp som en server slut punkt för alla Azure-filresurser/server platser som måste konfigureras för synkronisering.

När alla Server slut punkter har skapats fungerar synkroniseringen. Du kan skapa en testfil och se den synkroniseras från din server plats till den anslutna Azure-filresursen (som beskrivs av moln slut punkten i synkroniseringsresursen).

På båda platserna är servermappar och Azure-filresurserna annars tomma och väntar på data på någon av platserna. I nästa steg ska du börja kopiera filer till Windows Server för Azure File Sync för att flytta dem till molnet. Om du har aktiverat moln nivåer kommer servern sedan att börja på nivå-filer, om du får slut på kapacitet på den eller de lokala volymerna.

## <a name="phase-7-robocopy"></a>Fas 7: RoboCopy

Den grundläggande migrations metoden är en RoboCopy från NAS-installationen till Windows Server och Azure File Sync till Azure-filresurser.

Kör den första lokala kopian till din Windows Server-målmapp:

* Identifiera den första platsen på NAS-enheten.
* Identifiera matchande mapp på den Windows Server som redan har Azure File Sync konfigurerat.
* Starta kopieringen med RoboCopy

Följande RoboCopy-kommando kommer att kopiera filer från NAS-lagringen till din Windows Server-målmapp. Windows Server synkroniserar den med Azure-filresursen (erna). 

Om du har allokerat mindre lagrings utrymme på Windows Server än vad filerna tar upp på NAS-enheten har du konfigurerat moln nivå. När den lokala Windows Server-volymen är full, kommer [moln nivån](storage-sync-cloud-tiering-overview.md) att sätta igång och på filer som redan har synkroniserats. Moln skiktet genererar tillräckligt med utrymme för att kunna fortsätta med kopian från NAS-enheten. Moln nivåer utförs en gång i timmen för att se vad som har synkroniserats och för att frigöra disk utrymme för att uppnå det lediga utrymmet på 99%-volymen.
Det är möjligt att RoboCopy flyttar filer snabbare än att du kan synkronisera till molnet och nivån lokalt, så att det lokala disk utrymmet börjar ta slut. RoboCopy kommer inte att fungera. Vi rekommenderar att du arbetar genom resurserna i en följd som förhindrar detta. Du kan till exempel inte starta RoboCopy-jobb för alla resurser på samma gång, eller bara flytta resurser som passar på den aktuella mängden ledigt utrymme på Windows Server, så att du kan nämna några.

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-8-user-cut-over"></a>Fas 8: överhuggen av användare

När du kör RoboCopy-kommandot för första gången kommer dina användare och program fortfarande att komma åt filer på NAS och eventuellt ändra dem. Det är möjligt att RoboCopy har bearbetat en katalog, flyttar till nästa och sedan en användare på käll platsen (NAS) lägger till, ändrar eller tar bort en fil som nu inte kommer att bearbetas i den aktuella RoboCopy-körningen. Det här beteendet är förväntat.

Den första körningen är att flytta data mängden till din Windows-Server och till molnet via Azure File Sync. Den första kopian kan ta lång tid, beroende på:

* din nedladdnings bandbredd
* överförings bandbredden
* den lokala nätverks hastigheten och antalet hur optimalt antalet RoboCopy-trådar matchar den
* antalet objekt (filer och mappar) som måste bearbetas av RoboCopy och Azure File Sync

När den första körningen har slutförts kör du kommandot igen.

Den andra gången kommer den att slutföras snabbare, eftersom det bara behöver transporteras ändringar som har hänt sedan den senaste körningen. Under den här andra körningen kan nya ändringar ackumuleras.

Upprepa den här processen tills du är nöjd med hur lång tid det tar att slutföra en RoboCopy för en angiven plats är inom ett godkänt fönster för stillestånds tid.

När du anser att avbrotts tiden är godtagbar måste du ta bort användar åtkomst till dina NAS-baserade resurser. Du kan göra det genom alla steg som förhindrar användare från att ändra fil-och mappstruktur och innehåll. Ett exempel är att peka DFS-Namespace till en plats som inte är en befintlig plats eller ändra rot-ACL: erna på resursen.

Kör en sista RoboCopy-avrundning. De kommer att hämta alla ändringar som kan ha missats.
Hur lång tid det sista steget tar är beroende av hastigheten på RoboCopy-genomsökningen. Du kan uppskatta tiden (som är lika med din stillestånds tid) genom att mäta hur lång tid den senaste körningen tog.

Skapa en resurs i Windows Server-mappen och eventuellt ändra DFS-N-distributionen så att den pekar på den. Se till att ange samma behörigheter på resurs nivå som på NAS SMB-resursen. Om du har en domän ansluten till en domän med företags klass matchas användarens sid automatiskt när användarna finns i Active Directory och RoboCopy kopierar filer och metadata vid full åter givning. Om du har använt lokala användare på din NAS måste du återskapa dessa användare som Windows Server lokala användare och mappa de befintliga sid-RoboCopy som flyttas över till din Windows-Server till de nya lokala Windows Server-användarna.

Du har avslutat migreringen av en resurs/grupp av resurser till en gemensam rot eller volym. (Beroende på din mappning från fas 1)

Du kan försöka att köra några av dessa kopior parallellt. Vi rekommenderar att du bearbetar omfånget för en Azure-filresurs i taget.

> [!WARNING]
> När du har flyttat alla data från NAS till Windows Server, och migreringen är slutförd: gå tillbaka till ***alla***  Sync-grupper i Azure Portal och justera moln skiktet volym ledigt utrymme i procent till något som passar bättre för användningen av cacheminnet, t. ex. 20%. 

Lagrings principen för ledigt utrymme i molnet fungerar på en volym nivå med eventuellt flera Server slut punkter som synkroniseras från den. Om du glömmer att justera det lediga utrymmet på en server slut punkt fortsätter synkroniseringen att tillämpa den mest restriktiva regeln och försöker behålla 99% ledigt disk utrymme, vilket gör att den lokala cachen inte fungerar som förväntat. Om det inte är målet att bara ha namn området för en volym som bara innehåller data som används sällan, och du reserverar resten av lagrings utrymmet för ett annat scenario.

## <a name="troubleshoot"></a>Felsöka

Det mest sannolika problemet som du kan köra i är att kommandot RoboCopy Miss lyckas med *"Volume full"* på Windows Server-sidan. Moln nivåer fungerar en gång per timme för att evakuera innehåll från den lokala Windows Server-disken som har synkroniserats. Målet är att uppnå det lediga utrymmet på 99% på volymen.

Låt synkroniseringen fortskrida och moln nivån frigör disk utrymme. Du kan titta på det i Utforskaren på Windows-servern.

När Windows Server har tillräckligt med tillgänglig kapacitet löser problemet problemet genom att köra kommandot igen. Ingenting bryts när du får den här situationen och du kan gå vidare med förtroende. Besvär att köra kommandot igen är den enda konsekvensen.

Kontrol lera länken i följande avsnitt för att felsöka Azure File Sync problem.

## <a name="next-steps"></a>Nästa steg

Det finns mer att identifiera om Azure-filresurser och Azure File Sync. I följande artiklar får du hjälp att förstå avancerade alternativ, bästa praxis och även innehålla fel söknings hjälp. De här artiklarna länkar till [dokumentationen för Azure-filresursen](storage-files-introduction.md) efter behov.

* [AFS-översikt](./storage-sync-files-planning.md)
* [Distributions guide för AFS](./storage-how-to-create-file-share.md)
* [AFS-felsökning](storage-sync-files-troubleshoot.md)