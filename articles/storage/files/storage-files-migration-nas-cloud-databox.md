---
title: Lokal NAS-migrering till Azure-filresurser
description: Lär dig hur du migrerar filer från en lokal NAS-plats (Network Attached Storage) till Azure-filresurser med Azure data.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: a8420d23c8bda29290722975ada2acca6733f0e7
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491700"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-azure-file-shares"></a>Använd dataattribut för att migrera från nätverksansluten lagring (NAS) till Azure-filresurser

Den här migrations artikeln är en av flera som omfattar nyckelorden NAS och Azure data. Kontrol lera om den här artikeln gäller ditt scenario:

> [!div class="checklist"]
> * Data Källa: nätverksansluten lagring (NAS)
> * Migrations väg: NAS- &rArr; &rArr; datafilens Azure-filresurs
> * Cachelagrar filer lokalt: Nej, det slutliga målet är att använda Azure-filresurser direkt i molnet. Det finns ingen plan för att använda Azure File Sync.

Om ditt scenario skiljer sig kan du titta igenom [tabellen med migrations guider](storage-files-migration-overview.md#migration-guides).

Den här artikeln vägleder dig genom de konfigurationer för planering, distribution och nätverk som krävs för att migrera från NAS-installationen till funktionella Azure-filresurser. Den här guiden använder Azure Data-dataskydd (data transport offline).

## <a name="migration-goals"></a>Migreringsmål

Målet är att flytta resurserna som du har på din NAS-installation till Azure och få dem att bli interna Azure-filresurser som du kan använda utan att behöva använda Windows Server. Migreringen måste göras på ett sätt som garanterar integriteten för produktions data och tillgänglighet under migreringen. Den sistnämnda lösningen kräver att drift stoppen är minimal, så att den får plats i eller endast något högre än vanliga underhålls perioder.

## <a name="migration-overview"></a>Översikt över migrering

Migreringsprocessen består av flera faser. Du måste Distribuera Azure Storage-konton och fil resurser, konfigurera nätverk, migrera med Azure data, komma i drift med ändringar via RoboCopy och slutligen klippa över dina användare till de nyligen skapade Azure-filresurserna. I följande avsnitt beskrivs de olika faserna i migreringsprocessen i detalj.

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

## <a name="phase-4-provision-a-temporary-windows-server"></a>Fas 4: etablera en tillfällig Windows Server

Medan du väntar på att dina Azure-data ska komma fram kan du redan distribuera en eller flera Windows-servrar som du behöver för att köra RoboCopy-jobb. 

- Den första användningen av dessa servrar är att kopiera filer till data rutan.
- Den andra användningen av dessa servrar kommer att bli infångad med ändringar som har inträffat på NAS-installationen när data har transporter ATS. Den här metoden håller stillestånds tiden på käll sidan till ett minimum.

Hastigheten i vilken dina RoboCopy-jobb fungerar beror huvudsakligen på följande faktorer:

* IOPS på käll-och mål lagrings platsen
* den tillgängliga nätverks bandbredden mellan dem </br> Mer information finns i fel söknings avsnittet: [IOPS och bandbredds överväganden](#iops-and-bandwidth-considerations)
* möjlighet att snabbt bearbeta filer och mappar i ett namn område </br> Mer information finns i fel söknings avsnittet: [bearbetnings hastighet](#processing-speed)
* antalet ändringar mellan RoboCopy körs </br> Mer information finns i fel söknings avsnittet: [Undvik onödigt arbete](#avoid-unnecessary-work)

Det är viktigt att du behåller den refererade informationen i åtanke när du bestämmer dig för RAM-och tråd antalet som du kommer att ange för dina tillfälliga Windows-servrar.

## <a name="phase-5-preparing-to-use-azure-file-shares"></a>Fas 5: förbereda för användning av Azure-filresurser

För att spara tid bör du fortsätta med den här fasen medan du väntar på att din data ska komma. Med informationen i den här fasen kommer du att kunna bestämma hur dina servrar och användare i Azure och utanför Azure ska aktive ras för att använda dina Azure-filresurser. De mest kritiska besluten är:

- **Nätverk:** Aktivera dina nätverk för att dirigera SMB-trafik.
- **Autentisering:** Konfigurera Azure Storage-konton för Kerberos-autentisering. AdConnect och domän som ansluter till ditt lagrings konto gör att dina appar och användare kan använda sina AD-identiteter för autentisering
- **Auktorisering:** Åtkomst kontrol listor på resurs nivå för varje Azure-filresurs gör att AD-användare och-grupper får åtkomst till en specifik resurs och inom en Azure-filresurs, och interna NTFS ACL: er tar över. Auktorisering baserat på ACL: er för filer och mappar fungerar sedan som för lokala SMB-resurser.
- **Verksamhets kontinuitet:** Integrering av Azure-filresurser i en befintlig miljö är ofta att bevara befintliga resurs adresser. Om du inte redan använder DFS-namnområden bör du överväga att upprätta det i din miljö. Du skulle kunna behålla delnings adresser som användarna och skripten använder, oförändrade. Du skulle använda DFS-N som en namn områdes Dirigerings tjänst för SMB genom att omdirigera DFS-Namespace mål till Azure-filresurser efter migreringen.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Den här videon är en guide och en demonstration av hur du på ett säkert sätt kan exponera Azure-filresurser direkt till informations arbetare och appar i fem enkla steg.</br>
        En dedikerad dokumentation om video referenser för vissa ämnen:

* [Identitetsöversikt](storage-files-active-directory-overview.md)
* [Så här ansluter du till ett lagrings konto](storage-files-identity-auth-active-directory-enable.md)
* [Översikt över nätverk för Azure-filresurser](storage-files-networking-overview.md)
* [Konfigurera offentliga och privata slut punkter](storage-files-networking-endpoints.md)
* [Så här konfigurerar du en S2S VPN](storage-files-configure-s2s-vpn.md)
* [Så här konfigurerar du en Windows P2S VPN](storage-files-configure-p2s-vpn-windows.md)
* [Så här konfigurerar du en Linux P2S VPN](storage-files-configure-p2s-vpn-linux.md)
* [Så här konfigurerar du DNS-vidarebefordran](storage-files-networking-dns.md)
* [Konfigurera DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

## <a name="phase-6-copy-files-onto-your-databox"></a>Fas 6: kopiera filer till din data

När din data tas emot måste du konfigurera din DataForm i en rad med detaljerad information till din NAS-enhet. Följ installations dokumentationen för den typ av data som du beställde.

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

```console
Robocopy /MT:32 /NP /NFL /NDL /B /MIR /IT /COPY:DATSO /DCOPY:DAT /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```
* Mer information om de enskilda RoboCopy-flaggorna finns i tabellen i det kommande [Robocopy-avsnittet](#robocopy).
* Om du vill veta mer om hur du rätt storlek på antalet trådar `/MT:n` , optimerar Robocopy-hastigheten och gör Robocopy till en lämplig granne i ditt data Center kan du titta närmare på [avsnittet Robocopy fel sökning](#troubleshoot).


## <a name="phase-7-catch-up-robocopy-from-your-nas"></a>Fas 7: fånga upp RoboCopy från din NAS

När data rutan rapporterar att alla filer och mappar har placerats i de planerade Azure-filresurserna kan du fortsätta med den här fasen.
En Catch-RoboCopy behövs bara om data på NAS kan ha ändrats sedan data kopieringen startades. I vissa scenarier där du använder en resurs för arkivering kan du eventuellt stoppa ändringarna i resursen på din NAS tills migreringen är klar. Du kan också ha möjlighet att uppfylla dina affärs krav genom att ställa in NAS-resurser till skrivskyddad under migreringen.

I de fall där du behöver en resurs som ska skrivas till läsning under migreringen och bara kan absorbera ett litet stillestånds fönster, är det här steget för att komma igång med RoboCopy viktigt att slutföras innan användaren får åtkomst direkt till Azure-filresursen.

I det här steget ska du köra RoboCopy-jobb för att fånga upp moln resurser med de senaste ändringarna på din NAS sedan den tid som du förgrenade dina resurser till data rutan.
Den här RoboCopy kan slutföras snabbt eller ta en stund, beroende på mängden omsättning som har inträffat på dina NAS-resurser.

Kör den första lokala kopian till din Windows Server-målmapp:

1. Identifiera den första platsen på NAS-enheten.
1. Identifiera den matchande Azure-filresursen.
1. Montera Azure-filresursen som en lokal nätverks enhet på den tillfälliga Windows-servern
1. Starta kopieringen med RoboCopy enligt beskrivningen

### <a name="mounting-an-azure-file-share"></a>Montera en Azure-filresurs

Innan du kan använda RoboCopy måste du göra Azure-filresursen tillgänglig via SMB. Det enklaste sättet är att montera resursen som en lokal nätverks enhet till den Windows Server som du planerar att använda för RoboCopy. 

> [!IMPORTANT]
> Innan du kan montera en Azure-filresurs på en lokal Windows Server måste du ha slutfört fasen: förbereder att använda Azure-filresurser!

När du är klar kan du läsa [artikeln Använd en Azure-filresurs med Windows instruktions artikel](storage-how-to-use-files-windows.md) och montera den Azure-filresurs som du vill starta NAS-Robocopy för.

### <a name="robocopy"></a>RoboCopy

Följande RoboCopy-kommando kopierar endast skillnaderna (uppdaterade filer och mappar) från din NAS-lagring till Azure-filresursen. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

> [!TIP]
> [Titta närmare på fel söknings avsnittet](#troubleshoot) om Robocopy påverkar produktions miljön, rapporterar massor av fel eller förlopps indikatorn inte så snabbt som förväntat.

### <a name="user-cut-over"></a>Användaren klipps över

När du kör RoboCopy-kommandot för första gången kommer dina användare och program fortfarande att komma åt filer på NAS och eventuellt ändra dem. Det är möjligt att RoboCopy har bearbetat en katalog, flyttar till nästa och sedan en användare på käll platsen (NAS) lägger till, ändrar eller tar bort en fil som nu inte kommer att bearbetas i den aktuella RoboCopy-körningen. Det här beteendet är förväntat.

Den första körningen är att flytta Mass delningen av de indelade data till Azure-filresursen. Den första kopian kan ta en stund. Kolla in [avsnittet fel sökning](#troubleshoot) för mer information om vad som kan påverka Robocopy-hastigheter.

När den första körningen har slutförts kör du kommandot igen.

En andra gång som du kör RoboCopy för samma resurs går det snabbare, eftersom det bara behöver transporter av ändringar som har skett sedan den senaste körningen. Du kan köra upprepade jobb för samma resurs.

När du anser att avbrotts tiden är godtagbar måste du ta bort användar åtkomst till dina NAS-baserade resurser. Du kan göra det genom alla steg som förhindrar användare från att ändra fil-och mappstruktur och innehåll. Ett exempel är att peka DFS-Namespace till en plats som inte är en befintlig plats eller ändra rot-ACL: erna på resursen.

Kör en sista RoboCopy-avrundning. De kommer att hämta alla ändringar som kan ha missats.
Hur lång tid det sista steget tar är beroende av hastigheten på RoboCopy-genomsökningen. Du kan uppskatta tiden (som är lika med din stillestånds tid) genom att mäta hur lång tid den senaste körningen tog.

Skapa en resurs i Windows Server-mappen och eventuellt ändra DFS-N-distributionen så att den pekar på den. Se till att ange samma behörigheter på resurs nivå som på NAS SMB-resursen. Om du har en domän ansluten till en domän med företags klass matchas användarens sid automatiskt när användarna finns i Active Directory och RoboCopy kopierar filer och metadata vid full åter givning. Om du har använt lokala användare på din NAS måste du återskapa dessa användare som Windows Server lokala användare och mappa de befintliga sid-RoboCopy som flyttas över till din Windows-Server till de nya lokala Windows Server-användarna.

Du har avslutat migreringen av en resurs/grupp av resurser till en gemensam rot eller volym. (Beroende på din mappning från fas 1)

Du kan försöka att köra några av dessa kopior parallellt. Vi rekommenderar att du bearbetar omfånget för en Azure-filresurs i taget.

## <a name="troubleshoot"></a>Felsöka

[!INCLUDE [storage-files-migration-robocopy-optimize](../../../includes/storage-files-migration-robocopy-optimize.md)]

## <a name="next-steps"></a>Nästa steg

Det finns mer att identifiera om Azure-filresurser. I följande artiklar får du hjälp att förstå avancerade alternativ, bästa praxis och även innehålla fel söknings hjälp. De här artiklarna länkar till [dokumentationen för Azure-filresursen](storage-files-introduction.md) efter behov.

* [Migreringsöversikt](storage-files-migration-overview.md)
* [Övervaka, diagnostisera och felsök Microsoft Azure Storage](../common/storage-monitoring-diagnosing-troubleshooting.md)
* [Nätverksöverväganden för direkt åtkomst](storage-files-networking-overview.md)
* [Säkerhets kopiering: ögonblicks bilder av Azure-filresurser](storage-snapshots-files.md)
