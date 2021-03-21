---
title: Lokal NAS-migrering till Azure File Sync via Azure Data Center
description: Lär dig hur du migrerar filer från en lokal NAS-plats (Network Attached Storage) till en hybrid moln distribution med Azure File Sync via Azure data.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/5/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 144b2f23e40f315441c3de2482ae8aeffe77ec75
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584219"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Använd data för att migrera från nätverksansluten lagring (NAS) till en hybrid moln distribution med Azure File Sync

Den här migrations artikeln är en av flera som rör nyckelorden NAS, Azure File Sync och Azure data. Kontrol lera om den här artikeln gäller ditt scenario:

> [!div class="checklist"]
> * Data Källa: nätverksansluten lagring (NAS)
> * Migrations väg: NAS- &rArr; &rArr; dataskydd Azure-filresurs &rArr; synkroniseras med Windows Server
> * Cachelagra filer lokalt: Ja, det slutliga målet är en Azure File Sync distribution.

Om ditt scenario skiljer sig kan du titta igenom [tabellen med migrations guider](storage-files-migration-overview.md#migration-guides).

Azure File Sync fungerar på DAS-platser (Direct Attached Storage) och stöder inte synkronisering till NAS-platser (Network Attached Storage).
Detta innebär en migrering av dina filer som behövs och den här artikeln vägleder dig genom planering och körning av en sådan migrering.

## <a name="migration-goals"></a>Migreringsmål

Målet är att flytta de resurser som du har på din NAS-installation till en Windows-Server. Använd sedan Azure File Sync för en hybrid moln distribution. Migreringen måste göras på ett sätt som garanterar integriteten för produktions data och tillgänglighet under migreringen. Den sistnämnda lösningen kräver att drift stoppen är minimal, så att den får plats i eller endast något högre än vanliga underhålls perioder.

## <a name="migration-overview"></a>Översikt över migrering

Migreringsprocessen består av flera faser. Du måste Distribuera Azure Storage-konton och-fil resurser, distribuera en lokal Windows Server, konfigurera Azure File Sync, migrera med RoboCopy och sedan göra om styckningen. I följande avsnitt beskrivs de olika faserna i migreringsprocessen i detalj.

> [!TIP]
> Om du kommer tillbaka till den här artikeln använder du navigeringen på höger sida för att gå till fasen för migreringen där du slutade.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Fas 1: identifiera hur många Azure-filresurser du behöver

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>Fas 2: Distribuera Azure Storage-resurser

I den här fasen, se mappnings tabellen från fas 1 och Använd den för att etablera rätt antal Azure Storage-konton och fil resurser i dem.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-databox-appliances-you-need"></a>Fas 3: ta reda på hur många Azure Data Center-enheter du behöver

Starta bara det här steget när du har slutfört föregående fas. Dina Azure Storage-resurser (lagrings konton och fil resurser) bör skapas just nu. Under din ordning för data sortering måste du ange vilka lagrings konton som data ska flytta data till.

I den här fasen måste du mappa resultatet av migreringsprocessen från föregående fas till gränserna för de tillgängliga data alternativen. Dessa överväganden hjälper dig att skapa en plan för vilka data alternativ du bör välja och hur många av dem du behöver för att flytta dina NAS-resurser till Azure-filresurser.

Ta reda på hur många enheter av vilken typ du behöver genom att tänka på följande viktiga begränsningar:

* En Azure-DataForm kan flytta data till upp till 10 lagrings konton. 
* Varje dataruta får en egen användbar kapacitet. Se [alternativ för data](#databox-options).

Kontakta migrerings planen för antalet lagrings konton som du har valt att skapa och resurserna i var och en. Titta sedan på storleken på varje resurs på din NAS. Genom att kombinera den här informationen kan du optimera och bestämma vilken apparat som ska skicka data till vilka lagrings konton. Du kan använda två dataattribut för att flytta filer till samma lagrings konto, men dela inte upp innehållet i en enskild fil resurs över 2 DataBoxes.

### <a name="databox-options"></a>Data alternativ

För en standard-migrering ska en eller en kombination av dessa tre data alternativ väljas: 

* Dataforms-diskar Microsoft skickar dig en och upp till fem SSD-diskar med en kapacitet på 8 TiB per, för ett maximalt antal 40-TiB. Den användbara kapaciteten är cirka 20% mindre, på grund av kryptering och fil systemets omkostnader. Mer information finns i [dokumentationen om data diskar](../../databox/data-box-disk-overview.md).
* Data rutan är det vanligaste alternativet. En robust data apparat som fungerar ungefär som en NAS skickas till dig. Den har en användbar kapacitet på 80 TiB. Mer information finns i [dokumentationen för data](../../databox/data-box-overview.md).
* Datarutas extrafet det här alternativet innehåller en robust data apparat på hjul, som fungerar ungefär som en NAS, med en kapacitet på 1 PiB. Den användbara kapaciteten är cirka 20% mindre, på grund av kryptering och fil systemets omkostnader. Mer information finns i [extrafet dokumentation](../../databox/data-box-heavy-overview.md).

## <a name="phase-4-provision-a-suitable-windows-server-on-premises"></a>Fas 4: etablera en lämplig Windows Server lokalt

Medan du väntar på att dina Azure-data ska komma fram kan du redan börja granska behoven hos en eller flera Windows-servrar som du kommer att använda med Azure File Sync.

* Skapa en Windows Server 2019 – minst 2012R2 – som en virtuell dator eller fysisk server. Ett kluster för växling vid fel i Windows Server stöds också.
* Etablera eller Lägg till direktansluten lagring (DAS jämfört med NAS, vilket inte stöds).

Resurs konfigurationen (beräkning och RAM) för den Windows Server som du distribuerar beror främst på antalet objekt (filer och mappar) som du kommer att synkronisera. En högre prestanda konfiguration rekommenderas om du har problem.

[Lär dig hur du ändrar storlek på en Windows-Server baserat på antalet objekt (filer och mappar) som du behöver synkronisera.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> Den tidigare länkade artikeln visar en tabell med ett intervall för server minne (RAM). Du kan orientera dig mot det mindre antalet för servern, men vi tror att den inledande synkroniseringen kan ta betydligt längre tid.

## <a name="phase-5-copy-files-onto-your-databox"></a>Fas 5: kopiera filer till din data

När din data tas emot måste du ställa in din DataForm på din NAS-apparat. Följ installations dokumentationen för den typ av data som du beställde.

* [Konfigurera Data Box](../../databox/data-box-quickstart-portal.md)
* [Konfigurera Data Box Disk](../../databox/data-box-disk-quickstart-portal.md)
* [Konfigurera Data Box Heavy](../../databox/data-box-heavy-quickstart-portal.md)

Beroende på typ av data kan det finnas data kopierings verktyg som är tillgängliga för dig. I detta läge rekommenderas de inte för migrering till Azure-filresurser eftersom de inte kopierar dina filer med fullständig åter givning av data rutan. Använd RoboCopy i stället.

När din data tas emot har den företablerade SMB-resurser som är tillgängliga för varje lagrings konto som du har angett vid tidpunkten för beställningen.

* Om filerna ingår i en Premium Azure-filresurs finns det en SMB-resurs per Premium lagrings konto för fil lagring.
* Om filerna ingår i ett standard lagrings konto finns det tre SMB-resurser per standard (GPv1 och GPv2) lagrings konto. Endast de fil resurser som slutar med `_AzFiles` är relevanta för migreringen. Ignorera eventuella block-och sid-BLOB-resurser.

Följ stegen i dokumentationen för Azure Data Center:

1. [Ansluta till Data Box](../../databox/data-box-deploy-copy-data.md)
1. Kopiera data till Data Box
1. [Förbered din data för avresa till Azure](../../databox/data-box-deploy-picked-up.md)

Dokumentationen för den länkade data rutan anger ett RoboCopy-kommando. Kommandot är dock inte lämpligt för att bevara den fullständiga åter givningen av filer och mappar. Använd det här kommandot i stället:

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]


## <a name="phase-6-deploy-the-azure-file-sync-cloud-resource"></a>Fas 6: Distribuera Azure File Sync moln resurs

Innan du fortsätter med den här guiden väntar du tills alla filer har anlänt till rätt Azure-filresurser. Processen för att leverera och mata in data tar tid.

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-7-deploy-the-azure-file-sync-agent"></a>Fas 7: Distribuera Azure File Sync agent

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-8-configure-azure-file-sync-on-the-windows-server"></a>Fas 8: Konfigurera Azure File Sync på Windows Server

Din registrerade lokala Windows Server måste vara klar och ansluten till Internet för den här processen.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

Aktivera funktionen för moln nivå och välj "endast namn område" i det inledande nedladdnings avsnittet.

> [!IMPORTANT]
> Moln lagring är en AFS-funktion som gör det möjligt för den lokala servern att ha mindre lagrings kapacitet än vad som lagras i molnet, men det fullständiga namn området är tillgängligt. Lokalt intressanta data cachelagras också lokalt för snabb åtkomst. Moln nivåer är en valfri funktion per Azure File Sync Server slut punkt. Du måste använda den här funktionen om du inte har tillräckligt med lokal disk kapacitet på Windows Server för att lagra alla moln data och om du vill undvika att ladda ned alla data från molnet!

Upprepa stegen för att skapa och lägga till matchande servermapp som en server slut punkt för alla Azure-filresurser/server platser som måste konfigureras för synkronisering. Vänta tills synkroniseringen av namn området har slutförts. I följande avsnitt får du information om hur du kan se till att.

> [!NOTE]
> När en server slut punkt har skapats fungerar synkroniseringen. Synkronisering måste dock räkna upp (identifiera) de filer och mappar som du har flyttat via data till Azure-filresursen. Beroende på storleken på namn området kan detta ta lång tid innan namn området för molnet börjar visas på servern.

## <a name="phase-9-wait-for-the-namespace-to-fully-appear-on-the-server"></a>Fas 9: vänta tills namn området visas fullständigt på servern

Vi rekommenderar att du väntar på nästa steg i din migrering att servern har hämtat namn området från moln resursen fullständigt. Om du börjar flytta filer för tidigt till-servern kan du riskera onödiga överföringar och till och med fil synkroniseringskonflikter.

Om du vill kontrol lera om servern har slutfört den inledande hämtningen av synkronisering öppnar du Loggboken på Windows Server-synkronisering och använder händelse loggen Azure File Sync telemetri.
Händelse loggen för telemetri finns i Loggboken under program och Services\Microsoft\FileSync\Agent.

Sök efter den senaste 9102-händelsen. Händelse-ID 9102 loggas när en Sync-session har slutförts. I händelse texten där, är ett fält för hämtningens synkroniserings riktning. ( `HResult` måste vara noll och filerna hämtas också).
 
Du vill se två efterföljande händelser av den här typen och innehållet för att kontrol lera att servern har hämtat namn området. Det är OK om det uppstår olika händelser mellan 2 9102 händelser.

## <a name="phase-10-catch-up-robocopy-from-your-nas"></a>Fas 10: fånga upp RoboCopy från din NAS

När servern har slutfört den första synkroniseringen av hela namn området från moln resursen kan du fortsätta med det här steget. Det är absolut nödvändigt att det här steget är slutfört innan du fortsätter med det här steget. Mer information finns i föregående avsnitt.

I det här steget ska du köra RoboCopy-jobb för att fånga upp moln resurser med de senaste ändringarna på din NAS sedan den tid som du förgrenade dina resurser till data rutan.
Den här RoboCopy kan slutföras snabbt eller ta en stund, beroende på mängden omsättning som har inträffat på dina NAS-resurser.

> [!WARNING]
> På grund av ett försämrat RoboCopy-beteende i Windows Server 2019 är/MIR switch of RoboCopy inte kompatibel med en nivå mål katalog. Du får inte använda Windows Server 2019 eller Windows 10-klienten för den här fasen av migreringen. Använd RoboCopy på en mellanliggande Windows Server 2016.

Den grundläggande migrations metoden är en RoboCopy från NAS-installationen till Windows Server och Azure File Sync till Azure-filresurser.

Kör den första lokala kopian till din Windows Server-målmapp:

1. Identifiera den första platsen på NAS-enheten.
1. Identifiera matchande mapp på den Windows Server som redan har Azure File Sync konfigurerat.
1. Starta kopieringen med RoboCopy

Följande RoboCopy-kommando kopierar endast skillnaderna (uppdaterade filer och mappar) från din NAS-lagring till din Windows Server-målmapp. Windows Server kommer sedan att synkronisera dem till Azure-filresursen. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

Om du har allokerat mindre lagrings utrymme på Windows Server än vad filerna tar upp på NAS-enheten har du konfigurerat moln nivå. När den lokala Windows Server-volymen är full, kommer [moln nivån](storage-sync-cloud-tiering-overview.md) att sätta igång och på filer som redan har synkroniserats. Moln skiktet genererar tillräckligt med utrymme för att kunna fortsätta med kopian från NAS-enheten. Moln nivåer utförs en gång i timmen för att se vad som har synkroniserats och för att frigöra disk utrymme för att uppnå det lediga utrymmet på 99%-volymen.
Det är möjligt att RoboCopy måste flytta flera filer, mer än du har lokal lagring för på Windows Server. I genomsnitt kan du vänta RoboCopy att flytta mycket snabbare än Azure File Sync kan ladda upp dina filer över och ta bort dem från den lokala volymen. RoboCopy kommer inte att fungera. Vi rekommenderar att du arbetar genom resurserna i en följd som förhindrar detta. Du kan till exempel inte starta RoboCopy-jobb för alla resurser på samma gång, eller bara flytta resurser som passar på den aktuella mängden ledigt utrymme på Windows Server, så att du kan nämna några. Den goda nyheten är att/MIR-växeln bara kommer att flytta delta och när en delta har flyttats, behöver ett omstartat jobb inte flytta filen igen.

### <a name="user-cut-over"></a>Användaren klipps över

När du kör RoboCopy-kommandot för första gången kommer dina användare och program fortfarande att komma åt filer på NAS och eventuellt ändra dem. Det är möjligt att RoboCopy har bearbetat en katalog, flyttar till nästa och sedan en användare på käll platsen (NAS) lägger till, ändrar eller tar bort en fil som nu inte kommer att bearbetas i den aktuella RoboCopy-körningen. Det här beteendet är förväntat.

Den första körningen är att flytta den stora delen av de indelade data till din Windows-Server och till molnet via Azure File Sync. Den första kopian kan ta lång tid, beroende på:

* överförings bandbredden
* den lokala nätverks hastigheten och antalet hur optimalt antalet RoboCopy-trådar matchar den
* antalet objekt (filer och mappar) som måste bearbetas av RoboCopy och Azure File Sync

När den första körningen har slutförts kör du kommandot igen.

En andra gång som du kör RoboCopy för samma resurs går det snabbare, eftersom det bara behöver transporter av ändringar som har skett sedan den senaste körningen. Du kan köra upprepade jobb för samma resurs.

När du anser att avbrotts tiden är godtagbar måste du ta bort användar åtkomst till dina NAS-baserade resurser. Du kan göra det genom alla steg som förhindrar användare från att ändra fil-och mappstruktur och innehåll. Ett exempel är att peka DFS-Namespace till en plats som inte är en befintlig plats eller ändra rot-ACL: erna på resursen.

Kör en sista RoboCopy-avrundning. De kommer att hämta alla ändringar som kan ha missats.
Hur lång tid det sista steget tar är beroende av hastigheten på RoboCopy-genomsökningen. Du kan uppskatta tiden (som är lika med din stillestånds tid) genom att mäta hur lång tid den senaste körningen tog.

Skapa en resurs i Windows Server-mappen och eventuellt ändra DFS-N-distributionen så att den pekar på den. Se till att ange samma behörigheter på resurs nivå som på NAS SMB-resursen. Om du har en domän ansluten till en domän med företags klass matchas användarens sid automatiskt när användarna finns i Active Directory och RoboCopy kopierar filer och metadata vid full åter givning. Om du har använt lokala användare på din NAS måste du återskapa dessa användare som Windows Server lokala användare och mappa de befintliga sid-RoboCopy som flyttas över till din Windows-Server till de nya lokala Windows Server-användarna.

Du har avslutat migreringen av en resurs/grupp av resurser till en gemensam rot eller volym. (Beroende på din mappning från fas 1)

Du kan försöka att köra några av dessa kopior parallellt. Vi rekommenderar att du bearbetar omfånget för en Azure-filresurs i taget.

## <a name="troubleshoot"></a>Felsöka

Det mest sannolika problemet som du kan köra i är att kommandot RoboCopy Miss lyckas med *"Volume full"* på Windows Server-sidan. Moln nivåer fungerar en gång per timme för att evakuera innehåll från den lokala Windows Server-disken som har synkroniserats. Målet är att uppnå det lediga utrymmet på 99% på volymen.

Låt synkroniseringen fortskrida och moln nivån frigör disk utrymme. Du kan titta på det i Utforskaren på Windows-servern.

När Windows Server har tillräckligt med tillgänglig kapacitet löser problemet problemet genom att köra kommandot igen. Ingenting bryts när du får den här situationen och du kan gå vidare med förtroende. Besvär att köra kommandot igen är den enda konsekvensen.

Kontrol lera länken i följande avsnitt för att felsöka Azure File Sync problem.

## <a name="next-steps"></a>Nästa steg

Det finns mer att identifiera om Azure-filresurser och Azure File Sync. I följande artiklar får du hjälp att förstå avancerade alternativ, bästa praxis och även innehålla fel söknings hjälp. De här artiklarna länkar till [dokumentationen för Azure-filresursen](storage-files-introduction.md) efter behov.

* [Migreringsöversikt](storage-files-migration-overview.md)
* [AFS-översikt](./storage-sync-files-planning.md)
* [Distributions guide för AFS](./storage-how-to-create-file-share.md)
* [AFS-felsökning](storage-sync-files-troubleshoot.md)
