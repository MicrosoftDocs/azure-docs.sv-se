---
title: Migrering av StorSimple 1200 till Azure File Sync
description: Lär dig hur du migrerar en virtuell StorSimple 1200-serie till Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8562d63bf227fff665c70674c7fe66922bce9992
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98882288"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>Migrering av StorSimple 1200 till Azure File Sync

StorSimple 1200-serien är en virtuell installation som körs i ett lokalt Data Center. Det är möjligt att migrera data från den här enheten till en Azure File Sync miljö. Azure File Sync är standard och strategisk Azure-tjänst som StorSimple-apparater kan migreras till.

StorSimple 1200-serien kommer att uppnå [livs längd](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series) i december 2022.  Det är viktigt att börja planera migreringen så snart som möjligt. Den här artikeln innehåller de steg som krävs för att migrera till Azure File Sync. 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> Microsoft strävar efter att hjälpa kunder i sin migrering. E-post AzureFilesMigration@microsoft . com för en anpassad migrations plan samt hjälp vid migreringen.

Azure File Sync är en moln tjänst från Microsoft, baserat på två huvud komponenter:

* Filsynkronisering och moln nivåer.
* Fil resurser som intern lagring i Azure, som kan nås via flera protokoll som SMB och fil REST. En Azure-filresurs är jämförbar med en fil resurs på en Windows-Server, som du kan montera som en nätverks enhet. Det stöder viktiga fil åter givning-aspekter som attribut, behörigheter och tidsstämpel. Till skillnad från StorSimple krävs inget program/tjänst för att tolka filerna och mapparna som lagras i molnet. Den perfekta och mest flexibla metoden för att lagra generella fil Server data och vissa program data i molnet.

Den här artikeln fokuserar på stegen för migreringen. Om du innan du migrerar vill lära dig mer om Azure File Sync rekommenderar vi följande artiklar:

* [Azure File Sync-översikt](./storage-sync-files-planning.md "Översikt")
* [Guide för Azure File Sync distribution](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Migreringsmål

Målet är att garantera integriteten hos produktions data och att garantera tillgängligheten. Den sistnämnda lösningen kräver att drift stoppen är minimal, så att den får plats i eller endast något högre än vanliga underhålls perioder.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>StorSimple 1200-migrering till Azure File Sync

En lokal Windows Server krävs för att köra en Azure File Sync-agent. Windows Server kan vara minst en 2012R2-Server, men det är helst en Windows Server 2019.

Det finns flera, alternativa sökvägar för migrering och det skulle skapa för lång tid för en artikel för att dokumentera alla och illustrera varför de är utsatta för risker eller nack delar som rekommenderas i den här artikeln.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="Översikt över migreringen av stegen nedan i den här artikeln.":::

Föregående bild illustrerar steg som motsvarar avsnitt i den här artikeln.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>Steg 1: etablera din lokala Windows Server och lagring

1. Skapa en Windows Server 2019 – minst 2012R2 – som en virtuell dator eller fysisk server. Ett kluster för växling vid fel i Windows Server stöds också.
2. Etablera eller Lägg till direktansluten lagring (DAS jämfört med NAS, vilket inte stöds). Storleken på Windows Server-lagringen måste vara lika med eller större än storleken på den tillgängliga kapaciteten för den virtuella StorSimple 1200-enheten.

### <a name="step-2-configure-your-windows-server-storage"></a>Steg 2: Konfigurera din Windows Server-lagring

I det här steget mappar du din StorSimple-lagrings struktur (volymer och resurser) till lagrings strukturen i Windows Server.
Om du planerar att göra ändringar i lagrings strukturen, vilket innebär antalet volymer, associationen av datamappar till volymer eller undermappstrukturen ovanför eller under de aktuella SMB/NFS-resurserna, är det nu dags att ta hänsyn till dessa ändringar.
Att ändra din fil-och mappstruktur när Azure File Sync har kon figurer ATS, är besvärlig och bör undvikas.
Den här artikeln förutsätter att du mappar 1:1, så du måste ta dina mappnings ändringar i beaktande när du följer stegen i den här artikeln.

* Ingen av dina produktions data måste avslutas med system volymen för Windows Server. Moln nivåer stöds inte på system volymer. Den här funktionen krävs dock både för migreringen och kontinuerliga åtgärder som en StorSimple-ersättning.
* Etablera samma antal volymer på Windows-servern som du har på din virtuella StorSimple 1200-installation.
* Konfigurera de Windows Server-roller, funktioner och inställningar du behöver. Vi rekommenderar att du väljer att använda Windows Server-uppdateringar för att hålla ditt OS säkert och uppdaterat. På samma sätt rekommenderar vi väljer till Microsoft Update för att hålla Microsoft-program uppdaterade, inklusive Azure File Sync-agenten.
* Konfigurera inte några mappar eller resurser innan du läser följande steg.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>Steg 3: distribuera den första Azure File Sync moln resursen

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>Steg 4: matcha din lokala volym och mappstruktur till Azure File Sync och Azure-filresurs resurser

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>Steg 5: etablera Azure-filresurser

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>Steg 6: Konfigurera målmappar för Windows Server

I föregående steg har du funderat på alla aspekter som avgör komponenterna i dina synkroniserade topologier. Nu är det dags att förbereda servern för att ta emot filer för uppladdning.

Skapa **alla** mappar, som synkroniserar var och en till en egen Azure-filresurs.
Det är viktigt att du följer mappstrukturen som du har dokumenterat tidigare. Om du till exempel har valt att synkronisera flera lokala SMB-resurser tillsammans till en enda Azure-filresurs måste du placera dem under en gemensam rotmapp på volymen. Skapa den här mål-rotmappen på volymen nu.

Antalet Azure-filresurser som du har etablerad bör matcha antalet mappar som du har skapat i det här steget + antalet volymer som du synkroniserar på rotnivån.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>Steg 7: Distribuera Azure File Sync agent

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>Steg 8: Konfigurera synkronisering

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Se till att aktivera moln skiktning!** Detta krävs om den lokala servern inte har tillräckligt med utrymme för att lagra den totala storleken på dina data i moln lagringen i StorSimple. Ange din nivå princip tillfälligt för migreringen till 99% ledigt utrymme på volymen.

Upprepa stegen för att skapa och lägga till matchande servermapp som en server slut punkt för alla Azure-filresurser/server platser som måste konfigureras för synkronisering.

### <a name="step-9-copy-your-files"></a>Steg 9: kopiera dina filer

Den grundläggande migreringen är en RoboCopy från den virtuella StorSimple-enheten till Windows Server och Azure File Sync till Azure-filresurser.

Kör den första lokala kopian till din Windows Server-målmapp:

* Identifiera den första platsen på den virtuella StorSimple-enheten.
* Identifiera matchande mapp på den Windows Server som redan har Azure File Sync konfigurerat.
* Starta kopieringen med RoboCopy

Följande RoboCopy-kommando återställer filer från din StorSimple Azure-lagring till din lokala StorSimple och flyttar dem sedan till Windows Server-målmappen. Windows Server synkroniserar den med Azure-filresursen (erna). När den lokala Windows Server-volymen är full, kommer moln nivån att sätta igång och på filer som redan har synkroniserats. Moln skiktet genererar tillräckligt med utrymme för att kunna fortsätta med kopian från den virtuella StorSimple-enheten. Moln nivåer utförs en gång i timmen för att se vad som har synkroniserats och för att frigöra disk utrymme för att uppnå det lediga utrymmet på 99%-volymen.

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Lägg

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Tillåter att RoboCopy körs med flera trådar. Standardvärdet är 8, Max är 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Matar ut status till logg filen som UNICODE (skriver över befintlig logg).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Utdata till konsol fönstret. Används tillsammans med utdata i en loggfil.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Kör RoboCopy i samma läge som ett säkerhets kopierings program använder. Det gör att RoboCopy kan flytta filer som den aktuella användaren inte har behörighet till.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Tillåter att det här RoboCopy-kommandot körs flera gånger, i turordning på samma mål/mål. Den identifierar vad som har kopierats innan och utelämnar det. Endast ändringar, tillägg och *borttagningar* kommer att bearbetas, som har inträffat sedan den senaste körningen. Om kommandot inte kördes förut utelämnas inget. Det här är ett utmärkt alternativ för käll platser som fortfarande används aktivt och ändras.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      åter givningen av fil kopian (standard är/COPY: DAT), kopierings flaggor: D = data, A = attribut, T = tidsstämplar, S = Security = NTFS ACL, O = ägar information, U = gransknings information
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      Kopiera ALL fil information (motsvarande/COPY: DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      åter givning för kopian av kataloger (standard är/DCOPY: DA), kopierings flaggor: D = data, A = attribut, T = tidsstämplar
   :::column-end:::
:::row-end:::

När du kör RoboCopy-kommandot för första gången kommer dina användare och program fortfarande att komma åt StorSimple-filer och-mappar och eventuellt ändra det. Det är möjligt att RoboCopy har bearbetat en katalog, flyttar till nästa och sedan en användare på käll platsen (StorSimple) lägger till, ändrar eller tar bort en fil som nu inte kommer att bearbetas i den här aktuella RoboCopy-körningen. Det är bra.

Den första körningen är att flytta data mängden tillbaka till lokalt, till Windows Server och säkerhets kopiering till molnet via Azure File Sync. Detta kan ta lång tid, beroende på:

* din nedladdnings bandbredd
* åter kallelse hastigheten för moln tjänsten för StorSimple
* överförings bandbredden
* antalet objekt (filer och mappar) som måste bearbetas av endera tjänsten

När den första körningen har slutförts kör du kommandot igen.

Den andra gången kommer den att slutföras snabbare, eftersom det bara behöver transporteras ändringar som har hänt sedan den senaste körningen. Dessa ändringar är förmodligen lokala för StorSimple redan, eftersom de är aktuella. Detta minskar också tiden eftersom behovet av återkallande från molnet minskas. Under den här andra körningen kan nya ändringar ackumuleras.

Upprepa den här processen tills du är nöjd med hur lång tid det tar att slutföra är en acceptabel stillestånds tid.

När du anser att avbrotts tiden är acceptabel och du är för beredd att ta StorSimple-platsen offline gör du det nu: ta till exempel bort SMB-resursen så att ingen användare kan komma åt mappen eller vidta andra lämpliga steg som förhindrar att innehåll ändras i den här mappen på StorSimple.

Kör en sista RoboCopy-avrundning. Detta hämtar eventuella ändringar som kan ha missats.
Hur lång tid det sista steget tar är beroende av hastigheten på RoboCopy-genomsökningen. Du kan uppskatta tiden (som är lika med din stillestånds tid) genom att mäta hur lång tid den senaste körningen tog.

Skapa en resurs i Windows Server-mappen och eventuellt ändra DFS-N-distributionen så att den pekar på den. Se till att ange samma behörigheter på resurs nivå som på din StorSimple SMB-resurs.

Du har avslutat migreringen av en resurs/grupp av resurser till en gemensam rot eller volym. (Beroende på vad du har mappat och beslutat att du måste gå till samma Azure-filresurs.)

Du kan försöka att köra några av dessa kopior parallellt. Vi rekommenderar att du bearbetar omfånget för en Azure-filresurs i taget.

> [!WARNING]
> När du har flyttat alla data från dig StorSimple till Windows Server och migreringen är slutförd: gå tillbaka till ***alla***  Sync-grupper i Azure Portal och justera moln skiktet volym utrymme för ledigt utrymme i procent till något som passar bättre för användningen av cacheminnet, t. ex. 20%. 

Lagrings principen för ledigt utrymme i molnet fungerar på en volym nivå med eventuellt flera Server slut punkter som synkroniseras från den. Om du glömmer att justera det lediga utrymmet på en server slut punkt fortsätter synkroniseringen att tillämpa den mest restriktiva regeln och försöker behålla 99% ledigt disk utrymme, vilket gör att den lokala cachen inte fungerar som förväntat. Om det inte är målet att bara ha namn området för en volym som bara innehåller data som används sällan, arkiverade data.

## <a name="troubleshoot"></a>Felsöka

Det mest sannolika problemet som du kan köra i är att kommandot RoboCopy Miss lyckas med *"Volume full"* på Windows Server-sidan. Om så är fallet är nedladdnings hastigheten förmodligen bättre än uppladdnings hastigheten. Moln nivåer fungerar en gång per timme för att evakuera innehåll från den lokala Windows Server-disken som har synkroniserats.

Låt synkroniseringen fortskrida och moln nivån frigör disk utrymme. Du kan titta på det i Utforskaren på Windows-servern.

När Windows Server har tillräckligt med tillgänglig kapacitet löser problemet problemet genom att köra kommandot igen. Ingenting bryts när du får den här situationen och du kan gå vidare med förtroende. Besvär att köra kommandot igen är den enda konsekvensen.

Du kan också köra på andra Azure File Sync problem.
Om det händer så sannolikt att de kan ta en titt på **länken Azure File Sync fel söknings guide**.

## <a name="relevant-links"></a>Relevanta länkar

Migrerings innehåll:

* [Migrations guide för StorSimple 8000-serien](storage-files-migration-storsimple-8000.md)

Azure File Sync innehåll:

* [AFS-översikt](./storage-sync-files-planning.md)
* [Distributions guide för AFS](./storage-how-to-create-file-share.md)
* [AFS-felsökning](storage-sync-files-troubleshoot.md)