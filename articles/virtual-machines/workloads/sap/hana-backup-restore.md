---
title: HANA-säkerhetskopiering och återställning på SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Utföra HANA-säkerhetskopiering och återställning på SAP HANA på Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3244cbca3df717d3432670a366147408cf2b2c0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669122"
---
# <a name="backup-and-restore-of-sap-hana-on-hana-large-instances"></a>Säkerhets kopiering och återställning av SAP HANA på HANA stora instanser

>[!IMPORTANT]
>Den här artikeln ersätter inte SAP HANA administrations dokumentation eller SAP-anteckningar. Vi förväntar oss att du har en heltäckande förståelse för och expertis i SAP HANA administration och åtgärder, särskilt för säkerhets kopiering, återställning, hög tillgänglighet och haveri beredskap. I den här artikeln visas skärm bilder från SAP HANA Studio. Innehåll, struktur och beskaffenheten hos skärmarna i SAP-administrations verktyg och verktygen kan ändras från SAP HANA version till version.

Det är viktigt att du följer de steg och processer som har vidtagits i din miljö och med dina HANA-versioner och-versioner. Vissa processer som beskrivs i den här artikeln är förenklade för en bättre allmän förståelse. De är inte avsedda att användas som detaljerade steg för att utföra drift handböcker. Om du vill skapa åtgärds handböcker för dina konfigurationer, testa och träna dina processer och dokumentera processerna som är relaterade till dina speciella konfigurationer. 

En av de viktigaste aspekterna av drift databaser är att skydda dem från oåterkalleliga händelser. Orsaken till dessa händelser kan vara allt från natur haverier till enkla användar fel.

Om du säkerhetskopierar en databas kan du återställa den till en viss tidpunkt, till exempel innan någon har tagit bort viktiga data, aktiverar återställning till ett tillstånd som är så nära som möjligt på det sätt som det var innan avbrott.

Två typer av säkerhets kopieringar måste utföras för att få möjlighet att återställa:

- Databas säkerhets kopior: fullständiga, stegvisa eller differentiella säkerhets kopior
- Säkerhets kopior av transaktions logg

Förutom fullständiga säkerhets kopieringar som utförs på en program nivå kan du säkerhetskopiera med lagrings ögonblicks bilder. Ögonblicks bilder av lagring ersätter inte säkerhets kopior av transaktions loggar. Säkerhets kopior av transaktions loggar är viktiga för att återställa databasen till en viss tidpunkt eller tömma loggarna från redan genomförda transaktioner. Ögonblicks bilder av lagring kan påskynda återställningen genom att snabbt tillhandahålla en sammanslagen avbildning av databasen. 

SAP HANA på Azure (stora instanser) erbjuder två alternativ för säkerhets kopiering och återställning:

- **Gör det själv (gör det själv).** När du har säkerställt att det finns tillräckligt med disk utrymme kan du utföra fullständiga säkerhets kopieringar av databaser och loggar genom att använda någon av följande metoder för säkerhets kopiering av disk. Du kan säkerhetskopiera antingen direkt till volymer som är kopplade till de stora instanser av HANA-instanser eller till NFS-resurser som har kon figurer ATS i en virtuell Azure-dator (VM). I det senare fallet ställer kunderna in en virtuell Linux-dator i Azure, ansluter Azure Storage till den virtuella datorn och delar lagringen via en konfigurerad NFS-server i den virtuella datorn. Om du utför säkerhets kopieringen mot volymer som är direkt anslutna till HANA-stora instans enheter kopierar du säkerhets kopiorna till ett Azure Storage-konto. Gör detta när du har konfigurerat en virtuell Azure-dator som exporterar NFS-resurser som baseras på Azure Storage. Du kan också använda antingen ett Azure Backup-valv eller Azure-kall lagring. 

   Ett annat alternativ är att använda ett data skydds verktyg från tredje part för att lagra säkerhets kopiorna när de har kopierats till ett Azure Storage-konto. Alternativet gör det själv säkerhets kopiering kan också vara nödvändigt för data som du behöver lagra under längre tids perioder för efterlevnad och granskning. I samtliga fall kopieras säkerhets kopiorna till NFS-resurser som representeras via en virtuell dator och Azure Storage.

- **Säkerhets kopierings-och återställnings funktioner i infrastrukturen.** Du kan också använda säkerhets kopierings-och återställnings funktionen som den underliggande infrastrukturen i SAP HANA på Azure (stora instanser) tillhandahåller. Det här alternativet uppfyller behovet av säkerhets kopiering och snabb återställning. Resten av det här avsnittet behandlar de säkerhets kopierings-och återställnings funktioner som erbjuds med HANA-stora instanser. Det här avsnittet beskriver också förhållandet att säkerhets kopiering och återställning har till gång till funktionen haveri beredskap som erbjuds av HANA-stora instanser.

> [!NOTE]
>   Ögonblicks bild tekniken som används av den underliggande infrastrukturen i HANA-stora instanser är beroende av SAP HANA ögonblicks bilder. I det här läget fungerar SAP HANA ögonblicks bilder inte tillsammans med flera innehavare av SAP HANA flera klient databas behållare. Om bara en klient distribueras fungerar SAP HANA ögonblicks bilder och du kan använda den här metoden.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Använd lagrings ögonblicks bilder av SAP HANA på Azure (stora instanser)

Lagrings infrastrukturen som är underliggande SAP HANA på Azure (stora instanser) stöder lagrings ögonblicks bilder av volymer. Både säkerhets kopiering och återställning av volymer stöds, med följande överväganden:

- I stället för fullständiga databas säkerhets kopieringar tas ögonblicks bilder av lagrings volymer regelbundet.
- När en ögonblicks bild utlöses över/Hana/data och/Hana/Shared, som innehåller/usr/SAP, volymer, initierar ögonblicks bilds tekniken en SAP HANA ögonblicks bild innan lagrings ögonblicks bilden körs. Den här SAP HANA ögonblicks bilden är installations punkten för eventuell logg återställning efter återställning av lagrings ögonblicks bilden. För att en HANA-ögonblicksbild ska lyckas måste du ha en aktiv HANA-instans. I ett HSR-scenario stöds inte en lagrings ögonblicks bild på en aktuell sekundär nod där en HANA-ögonblicksbild inte kan utföras.
- När ögonblicks bilden av lagringen har körts, tas den SAP HANA ögonblicks bilden bort.
- Säkerhets kopior av transaktions loggar tas ofta och lagras i/Hana/logbackups-volymen eller i Azure. Du kan utlösa/Hana/logbackups-volymen som innehåller säkerhets kopiorna av transaktions loggen för att ta en ögonblicks bild separat. I så fall behöver du inte köra en HANA-ögonblicksbild.
- Om du måste återställa en databas till en viss tidpunkt, för ett produktions avbrott, begär du att Microsoft Azure stöd eller SAP HANA på Azure Restore till en viss lagrings ögonblicks bild. Ett exempel är en planerad återställning av ett begränsat system till dess ursprungliga tillstånd.
- SAP HANA ögonblicks bilden som ingår i ögonblicks bilden av lagringen är en förskjutnings punkt för att tillämpa säkerhets kopior av transaktions loggar som kördes och lagrats efter det att lagrings ögonblicks bilden togs.
- Dessa säkerhets kopior av transaktions loggen utförs för att återställa databasen till en viss tidpunkt.

Du kan utföra lagrings ögonblicks bilder som är riktade till tre klasser med volymer:

- En kombinerad ögonblicks bild över/Hana/data och/Hana/Shared, som innehåller/usr/SAP. Den här ögonblicks bilden kräver att en SAP HANA ögonblicks bild skapas som förberedelse för ögonblicks bilden av lagringen. SAP HANA ögonblicks bilden säkerställer att databasen är i ett konsekvent tillstånd från en lagrings plats i vyn. För återställnings processen är det dags att konfigurera på.
- En separat ögonblicks bild över/Hana/logbackups.
- En partition för operativ system.

Information om hur du hämtar de senaste skripten och dokumentationen för ögonblicks bilder finns i [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). När du hämtar ögonblicks bild skript paketet från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)får du tre filer. En av filerna dokumenteras i en PDF-fil för de funktioner som tillhandahålls. När du har hämtat verktygs uppsättningen följer du anvisningarna i hämta verktyg för ögonblicks bilder.

## <a name="storage-snapshot-considerations"></a>Överväganden vid lagring av ögonblicks bilder

>[!NOTE]
>Lagrings ögonblicks bilder förbrukar lagrings utrymme som allokerats till de stora HANA-instans enheterna. Tänk på följande aspekter på schemaläggning av lagrings ögonblicks bilder och hur många lagrings ögonblicks bilder som ska sparas. 

Den speciella Mechanics av lagrings ögonblicks bilder för SAP HANA på Azure (stora instanser) inkluderar:

- En viss lagrings ögonblicks bild vid tidpunkten när den tar emot lite lagrings utrymme.
- När data innehålls ändringar och innehållet i SAP HANA datafiler ändras på lagrings volymen måste ögonblicks bilden lagra det ursprungliga block innehållet och data ändringarna.
- Därför ökar lagrings ögonblicks bilden i storlek. Ju längre ögonblicks bilden finns, desto större blir lagrings ögonblicks bilden.
- Ju fler ändringar som görs i SAP HANA databas volymen över livs längden för en lagrings ögonblicks bild, desto större utrymmes förbrukning av lagrings ögonblicks bilden.

SAP HANA på Azure (stora instanser) levereras med fasta volym storlekar för de SAP HANA data-och logg volymerna. Ögonblicks bilder av volymerna Eats i volym utrymmet. Du måste:

- Bestäm när lagrings ögonblicks bilder ska schemaläggas.
- Övervaka lagrings volymens utrymmes förbrukning. 
- Hantera antalet ögonblicks bilder som du lagrar. 

Du kan inaktivera ögonblicks bilder av lagring när du importerar massor av data eller utföra andra betydande ändringar i HANA-databasen. 


I följande avsnitt finns information om hur du utför dessa ögonblicks bilder och inkluderar allmänna rekommendationer:

- Även om maskin varan kan hantera 255 ögonblicks bilder per volym, vill du stanna kvar under det här antalet. Rekommendationen är 250 eller mindre.
- Innan du utför ögonblicks bilder av lagring, övervaka och hålla reda på ledigt utrymme.
- Minska antalet lagrings ögonblicks bilder baserat på ledigt utrymme. Du kan minska antalet ögonblicks bilder som du behåller, eller så kan du utöka volymerna. Du kan beställa ytterligare lagrings utrymme i 1 – terabyte-enheter.
- Under aktiviteter som att flytta data till SAP HANA med SAP Platform Migration Tools (R3load) eller återställa SAP HANA-databaser från säkerhets kopior inaktiverar du lagrings ögonblicks bilder på/Hana/data-volymen. 
- Under större organisation av SAP HANA tabeller bör du undvika lagrings ögonblicks bilder om det är möjligt.
- Ögonblicks bilder av lagring är en förutsättning för att kunna dra nytta av Disaster Recovery-funktionerna i SAP HANA på Azure (stora instanser).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Krav för att använda ögonblicks bilder av självbetjänings lagring

Kontrol lera att ögonblicks bild skriptet körs korrekt genom att kontrol lera att perl är installerat på Linux-operativsystemet på servern HANA-stora instanser. Perl är förinstallerat på din HANA-stora instans enhet. Använd följande kommando för att kontrol lera perl-versionen:

`perl -v`

![Den offentliga nyckeln kopieras genom att köra det här kommandot](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Konfigurera lagrings ögonblicks bilder

Följ dessa steg om du vill ställa in ögonblicks bilder av lagrings utrymmen med stora instanser av HANA.
1. Se till att perl är installerat på Linux-operativsystemet på servern HANA Large instances.
1. Ändra/etc/ssh/SSH- \_ konfigurationen för att lägga till raden _Mac HMAC-SHA1_.
1. Skapa ett SAP HANA säkerhets kopierings konto på huvudnoden för varje SAP HANA instans som du kör, om tillämpligt.
1. Installera SAP HANA HDB-klienten på alla SAP HANA – stora instanser-servrar.
1. På den första SAP HANA – stora instanser servern för varje region skapar du en offentlig nyckel för att få åtkomst till den underliggande lagrings infrastrukturen som styr skapandet av ögonblicks bilder.
1. Kopiera skripten och konfigurations filen från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) till **hdbsql** -platsen i SAP HANA installationen.
1. Ändra *HANABackupDetails.txt* -filen efter behov för lämpliga kundspecifikationer.

Hämta de senaste ögonblicks bild skripten och dokumentationen från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). De steg som visas tidigare finns i [Microsoft Snapshot Tools för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

### <a name="consideration-for-mcod-scenarios"></a>Överväganden för MCOD-scenarier
Om du kör ett [MCOD-scenario](https://launchpad.support.sap.com/#/notes/1681092) med flera SAP HANA-instanser på en av en Hana stor instans enhet har du separata lagrings volymer etablerade för var och en av de SAP HANA instanserna. Mer information om MDC och andra överväganden finns i avsnittet viktiga saker att komma ihåg i [verktyg för Microsoft Snapshot för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Steg 1: installera SAP HANA HDB-klienten

Linux-operativsystemet som är installerat på SAP HANA på Azure (stora instanser) innehåller de mappar och skript som behövs för att köra SAP HANA ögonblicks bilder av ögonblicks bilder för säkerhets kopiering och haveri beredskap. Sök efter fler nyare versioner i [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). 

Det är ditt ansvar att installera SAP HANA HDB-klienten i de stora instanser i HANA-enheter medan du installerar SAP HANA.

### <a name="step-2-change-the-etcsshssh_config"></a>Steg 2: ändra/etc/ssh/SSH- \_ konfigurationen

Det här steget beskrivs i "Aktivera kommunikation med lagring" i [verktyg för Microsoft-ögonblicksbilder för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-3-create-a-public-key"></a>Steg 3: skapa en offentlig nyckel

Om du vill aktivera åtkomst till lagrings ögonblicks bild gränssnitt för en stor instans av HANA-stor instans, etablerar du en inloggnings procedur via en offentlig nyckel. 

På den första SAP HANA på Azure-servern (stora instanser) i din klient organisation skapar du en offentlig nyckel för att få åtkomst till lagrings infrastrukturen. Med en offentlig nyckel krävs inget lösen ord för att logga in på lagrings ögonblicks bild gränssnitten. Du behöver inte heller ha autentiseringsuppgifter för lösen ord med en offentlig nyckel. 

Information om hur du skapar en offentlig nyckel finns i "Aktivera kommunikation med lagring" i [verktyg för Microsoft-ögonblicksbilder för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-4-create-an-sap-hana-user-account"></a>Steg 4: skapa ett SAP HANA användar konto

Starta skapandet av SAP HANA ögonblicks bilder genom att skapa ett användar konto i SAP HANA som lagrings ögonblicks bilds skripten kan använda. Skapa ett SAP HANA användar konto i SAP HANA Studio för det här ändamålet. Användaren måste skapas under SYSTEMDB och *inte* under sid-databasen för MDC. I den enskilda behållar miljön skapas användaren i klient databasen. Kontot måste ha Läs behörighet för **säkerhets kopierings administratör** och **katalog** . 

Information om hur du konfigurerar och använder ett användar konto finns i "Aktivera kommunikation med SAP HANA" i [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Steg 5: auktorisera SAP HANA-användarkontot

I det här steget godkänner du SAP HANA användar konto som du skapade så att skripten inte behöver skicka in lösen ord vid körning. Med kommandot SAP HANA `hdbuserstore` kan du skapa en SAP HANA användar nyckel. Nyckeln lagras på en eller flera SAP HANA noder. Med användar nyckeln kan användaren komma åt SAP HANA utan att behöva hantera lösen ord i skript processen. Skript processen beskrivs längre fram i den här artikeln.

>[!IMPORTANT]
>Kör dessa konfigurations kommandon med samma användar kontext som ögonblicks bild kommandona körs i. Annars fungerar inte ögonblicks bild kommandon som de ska.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Steg 6: Hämta ögonblicks bild skript, konfigurera ögonblicks bilderna och testa konfigurationen och anslutningen

Hämta den senaste versionen av skripten från [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). Hur skripten installeras ändras med version 4,1 av skripten. Mer information finns i "Aktivera kommunikation med SAP HANA" i [Microsoft Snapshot-verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

De exakta kommando serierna finns i "enkel installation av verktyg för ögonblicks bilder (standard)" i [Microsoft Snapshot Tools för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Vi rekommenderar att du använder standard installationen. 

Information om hur du uppgraderar från version 3. x till 4,1 finns i "uppgradera en befintlig installation" i [Microsoft Snapshot Tools för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Information om hur du avinstallerar verktygs uppsättningen 4,1 finns i "avinstallation av ögonblicks bild verktyg" i [Microsoft Snapshot Tools för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Glöm inte att köra stegen som beskrivs i "slutföra installationen av verktyg för ögonblicks bilder" i [Microsoft Snapshot Tools för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Syftet med de olika skripten och filerna när de installerades beskrivs i avsnittet "Vad är dessa verktyg för ögonblicks bilder?" i [Microsoft Snapshot Tools för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Innan du konfigurerar verktyg för ögonblicks bilder ser du till att du även har konfigurerat HANA-platser och inställningar för säkerhets kopiering på rätt sätt. Mer information finns i "SAP HANA konfiguration" i [Microsoft Snapshot-verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Konfigurationen av verktyget för ögonblicks bild verktyget beskrivs i "konfigurations fil-HANABackupCustomerDetails.txt" i [Microsoft Snapshot-verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-connectivity-with-sap-hana"></a>Testa anslutningen med SAP HANA

När du har lagt till alla konfigurations data i *HANABackupCustomerDetails.txt* -filen kontrollerar du om konfigurationerna stämmer överens med Hana-instansens data. Använd skriptet `testHANAConnection` , som är oberoende av en SAP HANA skala upp-eller nedskalning-konfiguration.

Mer information finns i "kontrol lera anslutningen med SAP HANA-testHANAConnection" i [Microsoft Snapshot-verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-storage-connectivity"></a>Testa lagrings anslutning

Nästa test steg är att kontrol lera anslutningen till lagringen baserat på de data som du har lagt till i *HANABackupCustomerDetails.txt* konfigurations filen. Kör sedan en test ögonblicks bild. Innan du kör `azure_hana_backup` kommandot måste du köra det här testet. En sekvens med kommandon för det här testet finns i "kontrol lera anslutningen med Storage-testStorageSnapshotConnection" "i [verktyg för Microsoft Snapshot för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

När inloggningen till lagrings gränssnittet för virtuella datorer har slutförts fortsätter skriptet med fas 2 och skapar en ögonblicks bild av testet. Utdata visas här för en skalbar konfiguration med tre noder för SAP HANA.

Om test ögonblicks bilden körs med skriptet kan du schemalägga de faktiska lagrings ögonblicks bilderna. Om det inte lyckas bör du undersöka problemen innan du går vidare. Testets ögonblicks bild bör vara kvar tills de första riktiga ögonblicks bilderna är klara.


### <a name="step-7-perform-snapshots"></a>Steg 7: utföra ögonblicks bilder

När förberedelse stegen är klara kan du börja konfigurera och schemalägga de faktiska lagrings ögonblicks bilderna. Skriptet som ska schemaläggas fungerar med SAP HANA skala upp och skala ut konfigurationer. Schemalägg skriptet med hjälp av cron-verktyget för periodisk och regelbunden körning av säkerhets kopierings skriptet. 

Den exakta kommandosyntax och funktioner finns i "skapa säkerhets kopiering av ögonblicks bilder – azure_hana_backup" i [Microsoft Snapshot-verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

När skriptet `azure_hana_backup` körs skapas lagrings ögonblicks bilden i följande tre faser:

1. Den kör en SAP HANA ögonblicks bild.
1. Den kör en lagrings ögonblicks bild.
1. Det tar bort SAP HANA ögonblicks bilden som skapades innan ögonblicks bilden av lagringen kördes.

Kör skriptet genom att anropa det från den körbara HDB-mappen som den kopierades till. 

Kvarhållningsperioden administreras med antalet ögonblicks bilder som skickas som en parameter när du kör skriptet. Hur lång tid som omfattas av lagrings ögonblicks bilderna är en funktion i körnings perioden och antalet ögonblicks bilder som skickas som en parameter när skriptet körs. 

Om antalet ögonblicks bilder som hålls över överskrider det antal som har namngetts som en parameter i skriptets anrop, tas den äldsta lagrings ögonblicks bilden av samma etikett bort innan en ny ögonblicks bild körs. Talet som du anger som sista parameter i anropet är det tal du kan använda för att styra antalet ögonblicks bilder som hålls. Med det här numret kan du också kontrol lera, indirekt, det disk utrymme som används för ögonblicks bilder. 


## <a name="snapshot-strategies"></a>Strategier för ögonblicks bilder
Ögonblicks bilds frekvensen för olika typer beror på om du använder funktionen för haveri beredskap med HANA stor instans. Den här funktionen förlitar sig på lagrings ögonblicks bilder, vilket kan kräva särskilda rekommendationer för lagrings ögonblicks bildernas frekvens och körnings perioder. 

I överväganden och rekommendationerna som följer är antagandet att du *inte* använder funktionen för haveri beredskap som Hana-stora instanser erbjuder. I stället använder du lagrings ögonblicks bilderna för att säkerhetskopiera och kunna tillhandahålla tidpunkts återställning under de senaste 30 dagarna. Med hänsyn till begränsningarna för antalet ögonblicks bilder och utrymme bör följande krav uppfyllas:

- Återställnings tiden för återställning av tidpunkt.
- Det utrymme som används.
- Mål för återställnings punkt och återställnings tid för möjlig återställning från en katastrof.
- Eventuell körning av HANA fullständig säkerhets kopiering av databasen mot diskar. När en fullständig säkerhets kopiering av en databas till diskar eller **backint** -gränssnittet utförs, Miss lyckas körningen av lagrings ögonblicks bilder. Om du planerar att köra fullständig säkerhets kopiering av lagrings utrymmet på lagrings platsen, kontrollerar du att körningen av lagrings ögonblicks bilder är inaktive rad under den här tiden.
- Antal ögonblicks bilder per volym, som är begränsat till 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Om du inte använder funktionen för haveri beredskap hos HANA-stora instanser är ögonblicks bilds perioden mindre frekvent. I sådana fall utför du de kombinerade ögonblicks bilderna på/Hana/data och/Hana/Shared, som innehåller/usr/SAP, i 12-eller 24-timmars perioder. Behåll ögonblicks bilderna för en månad. Samma sak gäller ögonblicks bilderna av logg säkerhets kopierings volymen. Körningen av SAP HANA säkerhets kopior av transaktions loggen mot logg säkerhets kopierings volymen sker i 5 minuter till 15 minuter.

Ögonblicks bilder av schemalagd lagring utförs bäst med hjälp av cron. Använd samma skript för alla behov av säkerhets kopiering och haveri beredskap. Ändra skriptets indata så att de matchar de olika begärda säkerhets kopierings tiderna. Dessa ögonblicks bilder är schemalagda på olika sätt i cron beroende på deras körnings tid. Det kan vara varje timme, var 12: e timme, varje dag eller varje vecka. 

I följande exempel visas ett cron-schema i/etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
I föregående exempel täcker en kombinerad timmes ögonblicks bild de volymer som innehåller/Hana/data och/hana/shared/SID, som innehåller/usr/SAP, platser. Använd den här typen av ögonblicks bild för att få en snabbare återställning vid en viss tidpunkt under de senaste två dagarna. Det finns också en daglig ögonblicks bild på dessa volymer. Därför har du två dagars täckning per timmes ögonblicks bilder plus fyra veckors täckning per dagliga ögonblicks bilder. Säkerhets kopierings volymen för transaktions loggen säkerhets kopie ras också dagligen. Dessa säkerhets kopior sparas i fyra veckor. 

Som du ser i den tredje raden i crontab är säkerhets kopian av HANA-transaktionshanteraren schemalagd att köras var 5: e minut. Start tiderna för de olika cron-jobb som kör lagrings ögonblicks bilder är fördelade. På så sätt körs inte ögonblicks bilderna samtidigt vid en viss tidpunkt. 

I följande exempel utför du en kombinerad ögonblicks bild som täcker volymerna som innehåller/Hana/data och/hana/shared/SID, som innehåller/usr/SAP, platser per timme. Du behåller dessa ögonblicks bilder i två dagar. Ögonblicks bilderna av transaktions loggens säkerhets kopierings volymer körs på 5 minuter och bevaras i fyra timmar. Som tidigare är säkerhets kopian av HANA-transaktionshanteraren schemalagd att köras var 5: e minut. 

Ögonblicks bilden av transaktions loggens säkerhets kopierings volym utförs med en fördröjning på 2 minuter när säkerhets kopieringen av transaktions loggen har påbörjats. Under normala omständigheter slutförs säkerhets kopieringen av SAP HANA transaktions loggen inom två minuter. Som tidigare säkerhets kopie ras den volym som innehåller start-LUN en gång per dag med en lagrings ögonblicks bild och sparas i fyra veckor.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Följande bild illustrerar sekvenser i föregående exempel. Omstarts-LUN undantas.

![Relation mellan säkerhets kopieringar och ögonblicks bilder](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA utför vanliga skrivningar mot/Hana/log-volymen för att dokumentera de genomförda ändringarna i databasen. Med jämna mellanrum skriver SAP HANA en lagrings punkt till/Hana/data-volymen. Som anges i crontab körs en säkerhets kopiering av en SAP HANA transaktions logg var 5: e minut. 

Du ser också att en SAP HANA ögonblicks bild körs varje timme till följd av att en kombinerad lagrings ögonblicks bild utlöses över/Hana/data-och/hana/shared/SID-volymerna. När HANA-ögonblicksbilden har slutförts körs den kombinerade lagrings ögonblicks bilden. Som det instrueras i crontab körs ögonblicks bilden på/Hana/logbackup-volymen var 5: e minut, runt 2 minuter efter säkerhets kopieringen HANA-transaktionshanteraren.

> 

>[!IMPORTANT]
> Användningen av lagrings ögonblicks bilder för SAP HANA säkerhets kopieringar är värdefull endast när ögonblicks bilderna utförs tillsammans med säkerhets kopior av SAP HANA transaktions loggen. Dessa säkerhets kopior av transaktions loggen måste avse tids perioderna mellan lagrings ögonblicks bilderna. 

Om du har angett ett åtagande för användare av en tidpunkts återställning på 30 dagar måste du:

- Få åtkomst till en kombinerad lagrings ögonblicks bild över/Hana/data och/hana/shared/SID som är 30 dagar gammal i extrema fall. 
- Ha sammanhängande säkerhets kopieringar av transaktions loggar som beskriver tiden mellan någon av de kombinerade lagrings ögonblicks bilderna. Den äldsta ögonblicks bilden av transaktions loggens säkerhets kopierings volym måste därför vara 30 dagar gammal. Detta gäller inte om du kopierar säkerhets kopior av transaktions loggar till en annan NFS-resurs som finns på Azure Storage. I så fall kan du hämta gamla säkerhets kopior av transaktions loggen från NFS-resursen.

Om du vill dra nytta av lagrings ögonblicks bilder och eventuell lagrings replikering av transaktions loggens säkerhets kopior, ändrar du platsen till vilken SAP HANA skriver säkerhets kopior av transaktions loggen Du kan göra den här ändringen i HANA Studio. 

Även om SAP HANA säkerhetskopierar fullständiga logg segment automatiskt, anger du ett intervall för logg säkerhets kopiering som ska vara deterministisk. Detta gäller särskilt om du använder alternativet för haveri beredskap eftersom du vanligt vis vill köra logg säkerhets kopior med en deterministisk period. I följande fall anges 15 minuter som logg säkerhets kopierings intervall.

![Schemalägg SAP HANA säkerhets kopierings loggar i SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Du kan också välja säkerhets kopieringar oftare än var 15: e minut. En frekvent inställning används ofta tillsammans med haveri beredskap hos HANA-stora instanser. Vissa kunder utför säkerhets kopieringar av transaktions loggar var 5: e minut.

Om databasen aldrig har säkerhetskopierats är det sista steget att utföra en filbaserad säkerhets kopiering av databasen för att skapa en enskild säkerhets kopierings post som måste finnas i säkerhets kopierings katalogen. Annars kan SAP HANA inte initiera dina angivna logg säkerhets kopior.

![Gör en filbaserad säkerhets kopia för att skapa en enskild säkerhets kopierings post](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


När dina första lyckade lagrings ögonblicks bilder har körts tar du bort test ögonblicks bilden som kördes i steg 6. Mer information finns i "ta bort test ögonblicks bilder-removeTestStorageSnapshot" i [Microsoft Snapshot Tools för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Övervaka antalet och storleken på ögonblicks bilder på disk volymen

På en speciell lagrings volym kan du övervaka antalet ögonblicks bilder och lagrings förbrukningen för dessa ögonblicks bilder. `ls`Kommandot visar inte katalog eller filer för ögonblicks bilder. Linux OS-kommandot `du` visar information om dessa lagrings ögonblicks bilder eftersom de lagras på samma volymer. Använd kommandot med följande alternativ:

- `du –sh .snapshot`: Det här alternativet ger total summan av alla ögonblicks bilder i ögonblicks bild katalogen.
- `du –sh --max-depth=1`: Med det här alternativet visas alla ögonblicks bilder som har sparats i mappen **. snapshot** och storleken på varje ögonblicks bild.
- `du –hc`: Det här alternativet ger Total storlek som används av alla ögonblicks bilder.

Använd de här kommandona för att se till att ögonblicks bilderna som tas och lagras inte förbrukar all lagring på volymerna.

>[!NOTE]
>Ögonblicks bilderna av start-LUN visas inte med föregående kommandon.

### <a name="get-details-of-snapshots"></a>Hämta information om ögonblicks bilder
Om du vill ha mer information om ögonblicks bilder använder du skriptet `azure_hana_snapshot_details` . Du kan köra det här skriptet på någon av platserna om det finns en aktiv server på katastrof återställnings platsen. Skriptet ger följande utdata, uppdelat efter varje volym som innehåller ögonblicks bilder: 
   * Storlek på totalt antal ögonblicks bilder i en volym
   * Följande information i varje ögonblicks bild på volymen: 
      - Namn på ögonblicks bild 
      - Skapa tid 
      - Storlek på ögonblicks bilden
      - Ögonblicks bild frekvens
      - HANA-säkerhets kopierings-ID som är kopplat till ögonblicks bilden

Syntaxen för kommandot och utdata finns i "lista över ögonblicks bilder – azure_hana_snapshot_details" i [Microsoft Snapshot-verktyg för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Minska antalet ögonblicks bilder på en server

Som tidigare förklarat kan du minska antalet vissa etiketter för ögonblicks bilder som du lagrar. De två sista parametrarna i kommandot för att initiera en ögonblicks bild är etiketten och antalet ögonblicks bilder som du vill behålla.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

I föregående exempel är etiketten för ögonblicks bild **dailyhana**. Antalet ögonblicks bilder med den här etiketten som ska sparas är **28**. När du svarar på disk utrymmes förbrukningen kanske du vill minska antalet lagrade ögonblicks bilder. Ett enkelt sätt att minska antalet ögonblicks bilder till 15 är till exempel att köra skriptet med den sista parametern inställd på **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Om du kör skriptet med den här inställningen är antalet ögonblicks bilder, som innehåller den nya ögonblicks bilden, 15. De 15 senaste ögonblicks bilderna hålls kvar och de 15 äldre ögonblicks bilderna tas bort.

 >[!NOTE]
 > Det här skriptet minskar antalet ögonblicks bilder om det finns ögonblicks bilder som är mer än en timme gammal. Skriptet tar inte bort ögonblicks bilder som är mindre än en timme gammal. De här begränsningarna är relaterade till den valfria funktionen för haveri beredskap som erbjuds.

Om du inte längre vill behålla en uppsättning ögonblicks bilder med prefixet **dailyhana** i syntaxen kör du skriptet med **0** som kvarhållningsintervall. Alla ögonblicks bilder som matchar etiketten tas sedan bort. Att ta bort alla ögonblicks bilder kan påverka funktionerna hos HANA-stora instanser av haveri beredskap.

Ett andra alternativ för att ta bort vissa ögonblicks bilder är att använda skriptet `azure_hana_snapshot_delete` . Det här skriptet är utformat för att ta bort en ögonblicks bild eller uppsättning ögonblicks bilder med hjälp av säkerhets kopierings-ID: t i HANA Studio eller genom själva ögonblicks bild namnet. För närvarande är säkerhets kopierings-ID: t bara bundet till ögonblicks bilderna som skapats för typen **Hana** -ögonblicksbild. Ögonblicks bilder av säkerhets kopior av typ **loggar** och **Start** utför inte en SAP HANA ögonblicks bild, så det finns inga säkerhets kopierings-ID för dessa ögonblicks bilder. Om du anger ett namn på ögonblicks bilden söker det efter alla ögonblicks bilder på de olika volymer som matchar det angivna namnet på ögonblicks bilden. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Mer information om skriptet finns i "ta bort en ögonblicks bild-azure_hana_snapshot_delete" i [Microsoft Snapshot Tools för SAP HANA på Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Kör skriptet som användar **rot**.

>[!IMPORTANT]
>Om det finns data som bara finns på den ögonblicks bild som du planerar att ta bort, försvinner dessa data permanent när ögonblicks bilden tas bort.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Återställning på fil nivå från en lagrings ögonblicks bild

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
För ögonblicks bilds typerna **Hana** och **loggar** kan du komma åt ögonblicks bilderna direkt på volymerna i **. snapshot** -katalogen. Det finns en under katalog för varje ögonblicks bild. Kopiera varje fil i det tillstånd som den var i den punkt i ögonblicks bilden från under katalogen till den faktiska katalog strukturen. 

I den aktuella versionen av skriptet finns det *inget* återställnings skript för ögonblicks bild återställningen som självbetjäning. Återställning av ögonblicks bilder kan utföras som en del av återställnings skripten för självbetjäning på haveri beredskaps platsen under redundansväxlingen. Om du vill återställa en önskad ögonblicks bild från befintliga ögonblicks bilder måste du kontakta Microsoft Operations-teamet genom att öppna en tjänstbegäran.

>[!NOTE]
>Enkel fil återställning fungerar inte för ögonblicks bilder av startenheten som är oberoende av typen för de stora instanser av HANA. **. Snapshot** -katalogen visas inte i Start-LUN. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Återställ till den senaste HANA-ögonblicksbilden

I ett produktions scenario kan processen för att komma åt från en lagrings ögonblicks bild startas som en kund incident med Microsoft Azure support. Det är en stor angelägenhet om data har tagits bort i ett produktions system och det enda sättet att hämta det är att återställa produktions databasen.

I en annan situation kan en tidpunkts återställning vara låg angelägenhets grad och planerade dagar i förväg. Du kan planera den här återställningen med SAP HANA på Azure i stället för att öka en flagga med hög prioritet. Du kan till exempel planera att uppgradera SAP-programvaran genom att använda ett nytt förbättrings paket. Du måste sedan återgå till en ögonblicks bild som representerar det tillstånd som krävs för uppgradering av förbättrings paketet.

Innan du skickar begäran måste du förbereda. SAP HANA på Azure-teamet kan sedan hantera begäran och tillhandahålla de återställda volymerna. Efteråt återställer du HANA-databasen baserat på ögonblicks bilderna.

Möjlighet att hämta en ögonblicks bild som återställs med den nya verktygs uppsättningen finns i "så här återställer du en ögonblicks bild" i [guiden för manuell återställning för SAP HANA i Azure från en ögonblicks bild av lagringen](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Följ de här stegen för att förbereda för begäran.

1. Bestäm vilken ögonblicks bild som ska återställas. Endast Hana/data-volymen återställs om du inte anger något annat. 

1. Stäng av HANA-instansen.

   ![Stäng av HANA-instansen](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Demontera data volymerna på varje HANA-databas-nod. Om data volymerna fortfarande monteras i operativ systemet Miss lyckas återställningen av ögonblicks bilden.

   ![Demontera data volymerna på varje HANA-databas-nod](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Öppna en support förfrågan för Azure och ta med anvisningar om återställning av en speciell ögonblicks bild:

   - Under återställningen: SAP HANA på Azure-tjänsten kan du be dig att delta i ett konferens samtal för att koordinera, verifiera och bekräfta att rätt lagrings ögonblicks bild har återställts. 

   - Efter återställningen: SAP HANA på Azure-tjänsten meddelar dig när lagrings ögonblicks bilden återställs.

1. När återställnings processen har slutförts monterar du om alla data volymer.

   ![Montera om alla data volymer](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



En annan möjlighet att hämta, till exempel SAP HANA datafiler som har återställts från en ögonblicks bild av lagring, dokumenteras i steg 7 i [guiden manuell återställning för SAP HANA på Azure från en ögonblicks bild av lagringen](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Om du vill återställa från en ögonblicks bilds säkerhets kopia, se [manuell återställnings guide för SAP HANA på Azure från en ögonblicks bild](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

>[!Note]
>Om din ögonblicks bild har återställts av Microsoft-åtgärder behöver du inte göra steg 7.


### <a name="recover-to-another-point-in-time"></a>Återställ till en annan tidpunkt
Information om hur du återställer till en viss tidpunkt finns i "återställa databasen till följande tidpunkt" i [guiden för manuell återställning för SAP HANA i Azure från en lagrings ögonblicks bild](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 





## <a name="snapcenter-integration-in-sap-hana-large-instances"></a>SnapCenter-integrering i SAP HANA stora instanser

I det här avsnittet beskrivs hur kunder kan använda NetApp SnapCenter-programvara för att ta ögonblicks bilder, säkerhets kopiering och återställning SAP HANA databaser som finns på Microsoft Azure HANA stora instanser (HLI). 

SnapCenter erbjuder lösningar för scenarier som säkerhets kopiering/återställning, haveri beredskap (DR) med asynkron lagrings replikering, systemreplikering och system kloning. Kunder kan nu använda SnapCenter för säkerhets kopierings-och återställnings åtgärder integrerat med SAP HANA – stora instanser på Azure.

Ytterligare referenser finns i NetApp TR-4614 och TR-4646 på SnapCenter.

- [SAP HANA säkerhets kopiering/återställning med SnapCenter (TR-4614)](https://www.netapp.com/us/media/tr-4614.pdf)
- [SAP HANA haveri beredskap med Storage Replication (TR-4646)](https://www.netapp.com/us/media/tr-4646.pdf)
- [SAP HANA HSR med SnapCenter (TR-4719)](https://www.netapp.com/us/media/tr-4719.pdf)
- [SAP-kloning från SnapCenter (TR-4667)](https://www.netapp.com/us/media/tr-4667.pdf)

### <a name="system-requirements-and-prerequisites"></a>System krav och nödvändiga komponenter

För att köra SnapCenter på Azure HLI omfattar system kraven:
* SnapCenter server på Azure Windows 2016 eller senare med 4-vCPU, 16 GB RAM och minst 650 GB hanterad Premium SSD-lagring.
* SAP HANA – stora instanser systemet med 1,5 TB – 24 – TB RAM. Vi rekommenderar att du använder två SAP HANA stora instans system för kloning av åtgärder och tester.

Stegen för att integrera SnapCenter i SAP HANA: 

1. Generera en begäran om support ärende för att kommunicera den användardefinierade offentliga nyckeln till Microsoft Ops-teamet. Detta krävs för att ställa in SnapCenter-användaren för åtkomst till lagrings systemet.
1. Skapa en virtuell dator i ditt VNET som har åtkomst till HLI; den här virtuella datorn används för SnapCenter. 
1. Hämta och installera SnapCenter. 
1. Säkerhets kopierings-och återställnings åtgärder. 

### <a name="create-a-support-ticket-for-user-role-storage-setup"></a>Skapa ett support ärende för konfigurering av användar Rolls lagring

1. Öppna Azure Portal och gå till sidan **prenumerationer** . På sidan prenumerationer väljer du din SAP HANA prenumeration, som beskrivs i rött nedan.

   :::image type="content" source="./media/snapcenter/create-support-case-for-user-role-storage-setup.png" alt-text="Skapa support ärende för installation av användar lagring":::

1. På sidan SAP HANA prenumeration väljer du undersidan **resurs grupper** .

   :::image type="content" source="./media/snapcenter/solution-lab-subscription-resource-groups.png" alt-text="Resurs grupp för lösnings labb prenumeration" lightbox="./media/snapcenter/solution-lab-subscription-resource-groups.png":::

1. Välj en lämplig resurs grupp i en region.

   :::image type="content" source="./media/snapcenter/select-appropriate-resource-group-in-region.png" alt-text="Välj lämplig resurs grupp i regionen" lightbox="./media/snapcenter/select-appropriate-resource-group-in-region.png":::

1. Välj en SKU-post som motsvarar SAP HANA i Azure Storage.

   :::image type="content" source="./media/snapcenter/select-sku-entry-corresponding-to-sap-hana.png" alt-text="Välj den SKU-post som motsvarar SAP HANA" lightbox="./media/snapcenter/select-sku-entry-corresponding-to-sap-hana.png":::

1. Öppna en **ny begäran om support ärende** , som beskrivs i rött.

   :::image type="content" source="./media/snapcenter/open-new-support-ticket-request.png" alt-text="Öppna ny begäran om support ärende":::

1. På fliken **grundläggande** anger du följande information för biljetten:

   * **Typ av problem:** Produkt
   * **Prenumeration:** Din prenumeration
   * **Tjänst:** SAP HANA stor instans
   * **Resurs:** Din resurs grupp
   * **Sammanfattning:** Ange den offentliga nyckeln som skapats av användaren
   * **Problem typ:** Konfiguration och installation
   * **Problem under typ:** Konfigurera SnapCenter för HLI


1. I **beskrivningen** av support ärendet på fliken **information** anger du: 
   
   * Konfigurera SnapCenter för HLI
   * Din offentliga nyckel för SnapCenter-användare (SnapCenter. pem) – se den offentliga nyckeln skapa exempel nedan

     :::image type="content" source="./media/snapcenter/new-support-request-details.png" alt-text="Ny supportbegäran – information fliken" lightbox="./media/snapcenter/new-support-request-details.png":::

1. Välj **Granska + skapa** för att granska ditt support ärende. 

1. Generera ett certifikat för SnapCenter-användarnamnet på den stora instansen HANA eller Linux-servern.

   SnapCenter kräver ett användar namn och lösen ord för att få åtkomst till den virtuella lagrings datorn (SVM) och för att skapa ögonblicks bilder av HANA-databasen. Microsoft använder den offentliga nyckeln för att ge dig (kunden) möjlighet att ange lösen ordet för att komma åt lagrings systemet.

   ```bash
   openssl req -x509 -nodes -days 1095 -newkey rsa:2048 -keyout snapcenter.key -out snapcenter.pem -subj "/C=US/ST=WA/L=BEL/O=NetApp/CN=snapcenter"
   Generating a 2048 bit RSA private key
   ................................................................................................................................................+++++
   ...............................+++++
   writing new private key to 'snapcenter.key'
   -----

   sollabsjct31:~ # ls -l cl25*
   -rw-r--r-- 1 root root 1704 Jul 22 09:59 snapcenter.key
   -rw-r--r-- 1 root root 1253 Jul 22 09:59 snapcenter.pem

   ```

1. Bifoga snapcenter. pem-filen till support ärendet och välj sedan **skapa**

   När certifikatet för den offentliga nyckeln har skickats konfigurerar Microsoft SnapCenter-användarnamnet för din klient tillsammans med SVM IP-adress.   

1. När du har tagit emot IP-SVM anger du ett lösen ord för att få åtkomst till SVM som du styr.

   Följande är ett exempel på REST-ANROPet (dokumentation) från HANA stor instans eller virtuell dator i ett virtuellt nätverk, som har åtkomst till en stor instans miljö i HANA och som används för att ange lösen ordet.

   ```bash
   curl --cert snapcenter.pem --key snapcenter.key -X POST -k "https://10.0.40.11/api/security/authentication/password" -d '{"name":"snapcenter","password":"test1234"}'
   ```

   Se till att ingen proxy-variabel är aktiv i HANA DB-systemet.

   ```bash
   sollabsjct31:/tmp # unset http_proxy
   sollabsjct31:/tmp # unset https_proxy
   ```

### <a name="download-and-install-snapcenter"></a>Hämta och installera SnapCenter
Nu när användar namnet har kon figurer ATS för SnapCenter-åtkomst till lagrings systemet använder du SnapCenter-användarnamnet för att konfigurera SnapCenter när den har installerats. 

Innan du installerar SnapCenter granskar du [SAP HANA säkerhets kopiering/återställning med SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf) för att definiera din strategi för säkerhets kopiering. 

1. Logga in på [NetApp](https://mysupport.netapp.com) för att [Ladda ned](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fmysupport.netapp.com%2Fsite%2Fproducts%2Fall%2Fdetails%2Fsnapcenter%2Fdownloads-tab&data=02%7C01%7Cmadhukan%40microsoft.com%7Ca53f5e2f245a4e36933008d816efbb54%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637284566603265503&sdata=TOANWNYoAr1q5z1opu70%2FUDPHjluvovqR9AKplYpcpk%3D&reserved=0) den senaste versionen av SnapCenter.

1. Installera SnapCenter på den virtuella Windows Azure-datorn.

   Installations programmet kontrollerar kraven för den virtuella datorn. 

   >[!IMPORTANT]
   >Var noga med storleken på den virtuella datorn, särskilt i större miljöer.

1. Konfigurera användarautentiseringsuppgifter för SnapCenter. Som standard fyller den Windows-användarautentiseringsuppgifter som används för att installera programmet. 

   :::image type="content" source="media/snapcenter/installation-user-inputs-dialog.png" alt-text="Dialog rutan installation av användar indata"::: 

1. När du startar-sessionen sparar du säkerhets undantaget och det grafiska användar gränssnittet startar.

1. Logga in på SnapCenter på den virtuella datorn ( https://snapcenter-vm:8146) med Windows-autentiseringsuppgifter för att konfigurera miljön.


### <a name="set-up-the-storage-system"></a>Konfigurera lagrings systemet

1. I SnapCenter väljer du **Storage System** och väljer sedan **+ ny**. 

   :::image type="content" source="./media/snapcenter/snapcenter-storage-connections-window.png" alt-text="SnapCenter lagrings anslutningar" lightbox="./media/snapcenter/snapcenter-storage-connections-window.png":::

   Standardvärdet är en SVM per klient. Om en kund har flera klienter eller HLIs i flera regioner, är rekommendationen att konfigurera alla SVMs i SnapCenter

1. I Lägg till lagrings system anger du informationen för det lagrings system som du vill lägga till, SnapCenter användar namn och lösen ord och väljer sedan **Skicka**.

   :::image type="content" source="./media/snapcenter/new-storage-connection.png" alt-text="Ny lagrings anslutning":::

   >[!NOTE]
   >Standardvärdet är en SVM per klient.  Om det finns flera klienter, är rekommendationen att konfigurera alla SVMs här i SnapCenter. 

1. I SnapCenter väljer du **värdar** och välj **+ Lägg** till för att konfigurera Hana-plugin-programmet och Hana DB-värdarna.  Den senaste versionen av SnapCenter identifierar HANA-databasen automatiskt på värden.

   :::image type="content" source="media/snapcenter/managed-hosts-new-host.png" alt-text="I SnapCenter väljer du värdar och väljer sedan Lägg till." lightbox="media/snapcenter/managed-hosts-new-host.png":::

1. Ange informationen för den nya värden:
   1. Välj operativ system för värd typen.
   1. Ange SnapCenter VM-värdnamn.
   1. Ange de autentiseringsuppgifter som du vill använda.
   1. Välj alternativ för **Microsoft Windows** och **SAP HANA** och välj sedan **Skicka**.

   :::image type="content" source="media/snapcenter/add-new-host-operating-system-credentials.png" alt-text="Information för ny värd":::

   >[!IMPORTANT]
   >Innan du kan installera den första noden tillåter SnapCenter att en icke-root-användare installerar plugin-program på databasen.  Information om hur du aktiverar en icke-root-användare finns i [lägga till en icke-rot användare och konfigurera sudo-privilegier](https://library.netapp.com/ecmdocs/ECMLP2590889/html/GUID-A3EEB5FC-242B-4C2C-B407-510E48A8F131.html).

1. Granska värd informationen och välj **Skicka** för att installera plugin-programmet på SnapCenter-servern.

1. När plugin-programmet har installerats väljer du **värdar** i SnapCenter och väljer sedan **+ Lägg** till för att lägga till en Hana-nod.

   :::image type="content" source="media/snapcenter/add-hana-node.png" alt-text="Lägg till en HANA-nod" lightbox="media/snapcenter/add-hana-node.png":::

1. Ange informationen för HANA-noden:
   1. Välj operativ system för värd typen.
   1. Ange HANA DB-värdnamn eller IP-adress.
   1. Välj **+** om du vill lägga till de autentiseringsuppgifter som kon figurer ATS i värd operativ systemet Hana DB och välj sedan **OK**.
   1. Välj **SAP HANA** och välj sedan **Skicka**.

   :::image type="content" source="media/snapcenter/add-hana-node-details.png" alt-text="Information om SAP HANA nod":::

1. Bekräfta finger avtrycket och välj **Bekräfta och skicka**.

   :::image type="content" source="media/snapcenter/confirm-submit-fingerprint.png" alt-text="Bekräfta och skicka finger avtryck":::

1. På noden Hana, under system-databasen, väljer du **säkerhets**  >  **användare**  >  **SNAPCENTER** för att skapa SNAPCENTER-användaren.

   :::image type="content" source="media/snapcenter/create-snapcenter-user-hana-system-db.png" alt-text="Skapa SnapCenter-användaren i HANA (system dB)":::



### <a name="auto-discovery"></a>Automatisk identifiering
SnapCenter 4,3 aktiverar funktionen för automatisk identifiering som standard.  Automatisk identifiering stöds inte för HANA-instanser med HSR (HANA System Replication) konfigurerat. Du måste manuellt lägga till instansen till SnapCenter-servern.


### <a name="hana-setup-manual"></a>HANA-installation (manuell)
Om du har konfigurerat HSR måste du konfigurera systemet manuellt.  

1. I SnapCenter väljer du **resurser** och **San Hana** (längst upp) och väljer sedan **+ Lägg till SAP HANA databas** (till höger).

   :::image type="content" source="media/snapcenter/manual-hana-setup.png" alt-text="Manuell HANA-installation" lightbox="media/snapcenter/manual-hana-setup.png":::

1. Ange resursinformation för den HANA-administratör som kon figurer ATS på Linux-värden eller på värden där plugin-programmen är installerade. Säkerhets kopian kommer att hanteras från plugin-programmet på Linux-systemet.

   :::image type="content" source="media/snapcenter/provide-resource-details-sap-hana-database.png" alt-text="Ange resurs informationen för den HANA-administratör som kon figurer ATS på Linux-värden.":::

1. Välj den data volym som du vill ta ögonblicks bilder för, Välj **Spara** och välj sedan **Slutför**.

   :::image type="content" source="media/snapcenter/provide-storage-footprint.png" alt-text="Välj den data volym som du vill ta ögonblicks bilder för, Välj Spara och välj sedan Slutför.":::

### <a name="create-a-snapshot-policy"></a>Skapa en ögonblicks bild princip

Innan du använder SnapCenter för att säkerhetskopiera SAP HANA databas resurser måste du skapa en säkerhets kopierings princip för den resurs eller resurs grupp som du vill säkerhetskopiera. Under processen med att skapa en ögonblicks bilds princip får du möjlighet att konfigurera för-och efter-kommandon och särskilda SSL-nycklar. Information om hur du skapar en ögonblicks bilds princip finns i [skapa säkerhets kopierings principer för SAP HANA-databaser](http://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-sap-hana%2FGUID-246C0810-4F0B-4BF7-9A35-B729AD69954A.html).

1. I SnapCenter väljer du **resurser** och väljer sedan en databas.

   :::image type="content" source="media/snapcenter/select-database-create-policy.png" alt-text="I SnapCenter väljer du resurser och väljer sedan en databas.":::

1. Följ arbets flödet i konfigurations guiden för att konfigurera Schemaläggaren för ögonblicks bilder.

   :::image type="content" source="media/snapcenter/follow-workflow-configuration-wizard.png" alt-text="Följ arbets flödet i konfigurations guiden för att konfigurera Schemaläggaren för ögonblicks bilder." lightbox="media/snapcenter/follow-workflow-configuration-wizard.png":::

1. Ange alternativ för att konfigurera för-kommandon och särskilda SSL-nycklar.  I det här exemplet använder vi inga särskilda inställningar.

   :::image type="content" source="media/snapcenter/configuration-options-pre-post-commands.png" alt-text="Ange alternativen för att konfigurera för publicerings kommandon och särskilda SSL-nycklar." lightbox="media/snapcenter/configuration-options-pre-post-commands.png":::

1. Välj **Lägg till** för att skapa en ögonblicks bild princip som också kan användas för andra Hana-databaser. 

   :::image type="content" source="media/snapcenter/select-one-or-more-policies.png" alt-text="Välj Lägg till för att skapa en ögonblicks bild princip som också kan användas för andra HANA-databaser.":::

1. Ange princip namn och en beskrivning.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy.png" alt-text="Ange princip namn och en beskrivning.":::


1. Välj typ av säkerhets kopiering och frekvens.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-settings.png" alt-text="Välj typ av säkerhets kopiering och frekvens.":::

1. Konfigurera **inställningarna för kvarhållning av säkerhets kopior på begäran**.  I vårt exempel ställer vi in kvarhållning till tre ögonblicks bilds kopior som ska behållas.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-retention-settings.png" alt-text="Konfigurera inställningarna för kvarhållning av säkerhets kopior på begäran.":::

1. Konfigurera **inställningarna för kvarhållning av varje timme**. 

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-hourly-retention-settings.png" alt-text="Konfigurera inställningarna för kvarhållning av varje timme.":::

1. Om en SnapMirror-installation har kon figurer ATS väljer du **Uppdatera SnapMirror när du har skapat en lokal ögonblicks bild kopia**.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-snapmirror.png" alt-text="Om det krävs en SnapMirror väljer du uppdatera SnapMirror när du har skapat en lokal ögonblicks bild kopia.":::

1. Klicka på **Slutför** om du vill granska sammanfattningen av den nya säkerhets kopierings principen. 
1. Välj **Lägg till** under **Konfigurera schema**.

   :::image type="content" source="media/snapcenter/configure-schedules-for-selected-policies.png" alt-text="Välj Lägg till under Konfigurera schema.":::

1. Välj **start datum**, **upphör ande** datum och frekvens.

   :::image type="content" source="media/snapcenter/add-schedules-for-policy.png" alt-text="Välj start datum, upphör ande datum och frekvens.":::

1. Ange e-postinformation för meddelanden.

   :::image type="content" source="media/snapcenter/backup-policy-notification-settings.png" alt-text="Ange e-postinformation för meddelanden.":::

1.  Klicka på **Slutför** för att skapa säkerhets kopierings principen.

### <a name="disable-ems-message-to-netapp-autosupport"></a>Inaktivera EMS-meddelande för NetApp-stöd
Som standard aktive ras EMS-datainsamling och körs var sjunde dag efter installations datumet.  Du kan inaktivera data insamling med PowerShell-cmdleten `Disable-SmDataCollectionEms` .

1. Upprätta en session med SnapCenter i PowerShell.

   ```powershell
   Open-SmConnection
   ```

1. Logga in med dina autentiseringsuppgifter.
1. Inaktivera insamling av EMS-meddelanden.

   ```powershell
   Disable-SmCollectionEms
   ```

### <a name="restore-database-after-crash"></a>Återställ databasen efter krasch
Du kan använda SnapCenter för att återställa databasen.  I det här avsnittet ska vi gå igenom de övergripande stegen, men mer information finns i [SAP HANA säkerhets kopiering/återställning med SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf).


1. Stoppa databasen och ta bort alla databasfiler.

   ```
   su - h31adm
   > sapcontrol -nr 00 -function StopSystem
   StopSystem
   OK
   > sapcontrol -nr 00 -function GetProcessList
   OK
   name, description, dispstatus, textstatus, starttime, elapsedtime, pid
   hdbdaemon, HDB Daemon, GRAY, Stopped, , , 35902
 
   ```

1. Demontera databas volymen.

   ```bash
   unmount /hana/data/H31/mnt00001
   ```


1. Återställ databasfilerna via SnapCenter.  Välj databasen och välj sedan **Återställ**.  

   :::image type="content" source="media/snapcenter/restore-database-via-snapcenter.png" alt-text="Välj en databas och välj Återställ." lightbox="media/snapcenter/restore-database-via-snapcenter.png":::

1. Välj återställnings typ.  I vårt exempel återställer vi hela resursen. 

   :::image type="content" source="media/snapcenter/restore-database-select-restore-type.png" alt-text="Välj återställnings typ.":::

   >[!NOTE]
   >Med en standard konfiguration behöver du inte ange kommandon för att göra en lokal återställning från ögonblicks bilden på disken. 

   >[!TIP]
   >Om du vill återställa ett visst LUN inuti volymen väljer du **filnivå**.

1. Följ arbets flödet med hjälp av konfigurations guiden.
   
   SnapCenter återställer data till den ursprungliga platsen så att du kan starta återställnings processen i HANA. Eftersom SnapCenter inte kan ändra säkerhets kopierings katalogen (databasen är nere) visas dessutom en varning.

   :::image type="content" source="media/snapcenter/restore-database-job-details-warning.png" alt-text="Eftersom SnapCenter inte kan ändra säkerhets kopierings katalogen visas en varning. ":::

1. Starta återställnings processen i HANA eftersom alla databasfiler återställs. I Hana Studio, under **system**, högerklickar du på system databasen och väljer **säkerhets kopiering och återställning**  >  **Återställ system databas**.

   :::image type="content" source="media/snapcenter/hana-studio-backup-recovery.png" alt-text="Starta återställnings processen i HANA.":::

1. Välj en återställnings typ.

   :::image type="content" source="media/snapcenter/restore-database-select-recovery-type.png" alt-text="Välj återställnings typ.":::

1. Välj platsen för säkerhets kopierings katalogen.

   :::image type="content" source="media/snapcenter/restore-database-select-location-backup-catalog.png" alt-text="Välj platsen för säkerhets kopierings katalogen.":::

1. Välj en säkerhets kopia för att återställa SAP HANA databasen.

   :::image type="content" source="media/snapcenter/restore-database-select-backup.png" alt-text="Välj en säkerhets kopia för att återställa SAP HANA databasen.":::

   När databasen har återställts visas ett meddelande med en **återställning till tid** och **återställs till loggens befattnings** stämpling.

1. Under **system** högerklickar du på system databasen och väljer **säkerhets kopiering och återställning**  >  **Återställ klient databas**.
1. Följ arbets flödet i guiden för att slutföra återställningen av klient databasen. 

Mer information om hur du återställer en databas finns i [SAP HANA säkerhets kopiering/återställning med SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf).


### <a name="non-database-backups"></a>Säkerhets kopieringar som inte är databaser
Du kan återställa volymer som inte är data, till exempel en nätverks fil resurs (/Hana/Shared) eller en säkerhets kopia av operativ systemet.  Mer information om hur du återställer en icke-datavolym finns i [SAP HANA säkerhets kopiering/återställning med SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf).

### <a name="sap-hana-system-cloning"></a>SAP HANA system kloning

Innan du kan klona måste du ha samma HANA-version installerad som käll databasen. SID och ID kan vara olika. 

:::image type="content" source="media/snapcenter/system-cloning-diagram.png" alt-text="SAP HANA system kloning" lightbox="media/snapcenter/system-cloning-diagram.png" border="false":::

1. Skapa ett HANA-databas användar Arkiv för H34-databasen från/usr/sap/H34/HDB40.

   ```
   hdbuserstore set H34KEY sollabsjct34:34013 system manager
   ```
 
1. Inaktivera brand väggen.

   ```bash
   systemctl disable SuSEfirewall2
   systemctl stop  SuSEfirewall2
   ```

1. Installera Java SDK.

   ```bash
   zypper in java-1_8_0-openjdk
   ```

1. I SnapCenter lägger du till mål värden som klonen ska monteras på. Mer information finns i [lägga till värdar och installera plugin-paket på fjärranslutna värdar](http://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-sap-hana%2FGUID-246C0810-4F0B-4BF7-9A35-B729AD69954A.html).
   1. Ange informationen för de kör som-autentiseringsuppgifter som du vill lägga till. 
   1. Välj värd operativ system och ange värd informationen.
   1. Under **plugin-program att installera** väljer du version, anger installations Sök väg och väljer **SAP HANA**.
   1. Välj **Verifiera** för att köra kontrollerna före installationen.

1. Stoppa HANA och demontera den gamla data volymen.  Du kommer att montera klonen från SnapCenter.  

   ```bash
   sapcontrol -nr 40 -function StopSystem
   umount /hana/data/H34/mnt00001

   ```
 1. Skapa konfigurations-och gränssnitts skriptets filer för målet.
 
    ```bash
    mkdir /NetApp
    chmod 777 /NetApp
    cd NetApp
    chmod 777 sc-system-refresh-H34.cfg
    chmod 777 sc-system-refresh.sh

    ```

    >[!TIP]
    >Du kan kopiera skripten från [SAP-kloning från SnapCenter](https://www.netapp.com/us/media/tr-4667.pdf).

1. Ändra konfigurations filen. 

   ```bash
   vi sc-system-refresh-H34.cfg
   ```

   * HANA_ARCHITECTURE = "MDC_single_tenant"
   * NYCKEL = "H34KEY"
   * TIME_OUT_START = 18
   * TIME_OUT_STOP = 18
   * INSTANCENO = "40"
   * STORAGE = "10.250.101.33"

1. Ändra skal skript filen.

   ```bash
   vi sc-system-refresh.sh
   ```  

   * VERBOSE = nej
   * MY_NAME = " `basename $0` "
   * BASE_SCRIPT_DIR = " `dirname $0` "
   * MOUNT_OPTIONS = "RW, vers = 4, hård, tidsstämpel = 600, rsize = 1048576, wsize = 1048576, intr, noatime, NOLOCK"

1. Starta kloningen från en säkerhets kopierings process. Välj värden för att skapa klonen. 

   >[!NOTE]
   >Mer information finns i [klona från en säkerhets kopia](https://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-cpi%2FGUID-F6E7FF73-0183-4B9F-8156-8D7DA17A8555.html).

1. Under **skript**, anger du följande:

   * **Mount-kommando:** /NetApp/SC-system-Refresh.sh Mount H34% hana_data_h31_mnt00001_t250_vol_Clone
   * **Post-klonings kommando:** /NetApp/SC-system-Refresh.sh Recover H34

1. Inaktivera (lås) den automatiska monteringen i/etc/fstab eftersom data volymen för den förinstallerade databasen inte är nödvändig. 

   ```bash
   vi /etc/fstab
   ```

### <a name="delete-a-clone"></a>Ta bort en klon

Du kan ta bort en klon om den inte längre behövs. Mer information finns i [ta bort kloner](https://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-cpi%2FGUID-F6E7FF73-0183-4B9F-8156-8D7DA17A8555.html).

Kommandona som används för att köra innan klon borttagning är:
* **För klon borttagning:** /NetApp/SC-system-Refresh.sh Stäng H34
* **Demontera:** /NetApp/SC-system-Refresh.sh umount H34

De här kommandona tillåter SnapCenter att showdown databasen, demontera volymen och ta bort posten fstab.  Därefter tas FlexClone bort. 

### <a name="cloning-database-logfile"></a>Kloning av databas logg filen

```   
20190502025323###sollabsjct34###sc-system-refresh.sh: Adding entry in /etc/fstab.
20190502025323###sollabsjct34###sc-system-refresh.sh: 10.250.101.31:/Sc21186309-ee57-41a3-8584-8210297f791d /hana/data/H34/mnt00001 nfs rw,vers=4,hard,timeo=600,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
20190502025323###sollabsjct34###sc-system-refresh.sh: Mounting data volume.
20190502025323###sollabsjct34###sc-system-refresh.sh: mount /hana/data/H34/mnt00001
20190502025323###sollabsjct34###sc-system-refresh.sh: Data volume mounted successfully.
20190502025323###sollabsjct34###sc-system-refresh.sh: chown -R h34adm:sapsys /hana/data/H34/mnt00001
20190502025333###sollabsjct34###sc-system-refresh.sh: Recover system database.
20190502025333###sollabsjct34###sc-system-refresh.sh: /usr/sap/H34/HDB40/exe/Python/bin/python /usr/sap/H34/HDB40/exe/python_support/recoverSys.py --command "RECOVER DATA USING SNAPSHOT CLEAR LOG"
[140278542735104, 0.005] >> starting recoverSys (at Thu May  2 02:53:33 2019)
[140278542735104, 0.005] args: ()
[140278542735104, 0.005] keys: {'command': 'RECOVER DATA USING SNAPSHOT CLEAR LOG'}
recoverSys started: ============2019-05-02 02:53:33 ============
testing master: sollabsjct34
sollabsjct34 is master
shutdown database, timeout is 120
stop system
stop system: sollabsjct34
stopping system: 2019-05-02 02:53:33
stopped system: 2019-05-02 02:53:33
creating file recoverInstance.sql
restart database
restart master nameserver: 2019-05-02 02:53:38
start system: sollabsjct34
2019-05-02T02:53:59-07:00  P010976      16a77f6c8a2 INFO    RECOVERY state of service: nameserver, sollabsjct34:34001, volume: 1, RecoveryPrepared
recoverSys finished successfully: 2019-05-02 02:54:00
[140278542735104, 26.490] 0
[140278542735104, 26.490] << ending recoverSys, rc = 0 (RC_TEST_OK), after 26.485 secs
20190502025400###sollabsjct34###sc-system-refresh.sh: Wait until SAP HANA database is started ....
20190502025400###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025410###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025420###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025430###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025440###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025451###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502025451###sollabsjct34###sc-system-refresh.sh: SAP HANA database is started.
20190502025451###sollabsjct34###sc-system-refresh.sh: Recover tenant database H34.
20190502025451###sollabsjct34###sc-system-refresh.sh: /usr/sap/H34/SYS/exe/hdb/hdbsql -U H34KEY RECOVER DATA FOR H34 USING SNAPSHOT CLEAR LOG
0 rows affected (overall time 69.584135 sec; server time 69.582835 sec)
20190502025600###sollabsjct34###sc-system-refresh.sh: Checking availability of Indexserver for tenant H34.
20190502025601###sollabsjct34###sc-system-refresh.sh: Recovery of tenant database H34 succesfully finished.
20190502025601###sollabsjct34###sc-system-refresh.sh: Status: GREEN
Deleting the DB Clone – Logfile
20190502030312###sollabsjct34###sc-system-refresh.sh: Stopping HANA database.
20190502030312###sollabsjct34###sc-system-refresh.sh: sapcontrol -nr 40 -function StopSystem HDB

02.05.2019 03:03:12
StopSystem
OK
20190502030312###sollabsjct34###sc-system-refresh.sh: Wait until SAP HANA database is stopped ....
20190502030312###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030322###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030332###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030342###sollabsjct34###sc-system-refresh.sh: Status:  GRAY
20190502030342###sollabsjct34###sc-system-refresh.sh: SAP HANA database is stopped.
20190502030347###sollabsjct34###sc-system-refresh.sh: Unmounting data volume.
20190502030347###sollabsjct34###sc-system-refresh.sh: Junction path: Sc21186309-ee57-41a3-8584-8210297f791d
20190502030347###sollabsjct34###sc-system-refresh.sh: umount /hana/data/H34/mnt00001
20190502030347###sollabsjct34###sc-system-refresh.sh: Deleting /etc/fstab entry.
20190502030347###sollabsjct34###sc-system-refresh.sh: Data volume unmounted successfully.

```

### <a name="uninstall-snapcenter-plug-ins-package-for-linux"></a>Avinstallera SnapCenter-plugin-paket för Linux

Du kan avinstallera Linux-plugin-paketet från kommando raden. Eftersom den automatiska distributionen förväntar sig ett nytt system är det enkelt att avinstallera plugin-programmet.  

>[!NOTE]
>Du kan behöva avinstallera en äldre version av plugin-programmet manuellt. 

Avinstallera plugin-programmen.

```bash
cd /opt/NetApp/snapcenter/spl/installation/plugins
./uninstall
```

Nu kan du installera det senaste HANA-plugin-programmet på den nya noden genom att välja **Skicka** i SnapCenter. 




## <a name="next-steps"></a>Nästa steg
- Se [principer för haveri beredskap och förberedelser](hana-concept-preparation.md).
