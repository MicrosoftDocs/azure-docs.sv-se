---
title: Funktioner för operativsystemet
description: Lär dig mer om OS-funktionerna i Azure App Service i Windows. Ta reda på vilka typer av fil-, nätverks-och register åtkomst appen får.
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.topic: article
ms.date: 10/30/2018
ms.custom: seodec18
ms.openlocfilehash: 949e408544e25cb55622cf2a1b1d2dddb92350a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001515"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Operativ system funktioner på Azure App Service
I den här artikeln beskrivs vanliga funktioner för bas linje operativ system som är tillgängliga för alla Windows-appar som körs på [Azure App Service](./overview.md). Den här funktionen omfattar fil-, nätverks-och register åtkomst samt diagnostikloggar och händelser. 

> [!NOTE] 
> [Linux-appar](overview.md#app-service-on-linux) i App Service köras i sina egna behållare. Ingen åtkomst till värd operativ systemet tillåts, du har rot åtkomst till behållaren. På samma sätt har du administrativ åtkomst till behållaren, men ingen åtkomst till värd operativ systemet för [appar som körs i Windows-behållare](quickstart-custom-container.md?pivots=container-windows). 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>App Service plan-nivåer
App Service kör kund program i en värd miljö för flera innehavare. Appar som distribueras på nivån **kostnads fri** och **delad** körs i arbets processer på delade virtuella datorer, medan appar som distribueras på **standard** -och **Premium** -nivåerna körs på virtuella datorer som är dedikerade särskilt för de appar som är kopplade till en enskild kund.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Eftersom App Service stöder en sömlös skalnings upplevelse mellan olika nivåer, är säkerhets konfigurationen som tillämpas för App Service appar oförändrad. Detta säkerställer att appar plötsligt inte fungerar annorlunda, vilket inte kan uppstå på oväntade sätt när App Service plan växlar från en nivå till en annan.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Utvecklings ramverk
App Service pris nivåer styr mängden data bearbetnings resurser (CPU, disk lagring, minne och utgående nätverk) som är tillgängliga för appar. Däremot är de olika Ramverks funktionerna som är tillgängliga för apparna desamma oavsett skalnings nivåerna.

App Service stöder flera olika utvecklings ramverk, inklusive ASP.NET, klassisk ASP, node.js, PHP och python – alla som körs som tillägg i IIS. För att förenkla och normalisera säkerhets konfigurationen kör App Service appar vanligt vis de olika utvecklings ramverken med standardinställningarna. En metod för att konfigurera appar kan ha varit att anpassa API-ytan och funktionaliteten för varje enskilt utvecklings ramverk. App Service i stället en mer allmän metod genom att aktivera en gemensam bas linje för operativ systemets funktionalitet, oavsett appens utvecklings ramverk.

I följande avsnitt sammanfattas de allmänna typerna av operativ system funktioner som är tillgängliga för App Service appar.

<a id="FileAccess"></a>

## <a name="file-access"></a>Fil åtkomst
Det finns olika enheter inom App Service, inklusive lokala enheter och nätverks enheter.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Lokala enheter
På grund av detta är App Service en tjänst som körs ovanpå Azure-PaaS (plattform som en tjänst)-infrastruktur. Därför är de lokala enheter som är anslutna till en virtuell dator samma enhets typer som är tillgängliga för alla arbets roller som körs i Azure. Du måste bland annat:

- En operativ system enhet (D:\ kombinationsenhet
- En program enhet som innehåller Azure Package cspkg-filer som används exklusivt av App Service (och inte är tillgänglig för kunder)
- En "användar enhet" (C:\ enhet), vars storlek varierar beroende på den virtuella datorns storlek. 

Det är viktigt att du övervakar disk användningen när programmet växer. Om disk kvoten har nåtts kan den ha skadlig påverkan på ditt program. Exempel: 

- Appen kan utlösa ett fel som anger att det inte finns tillräckligt med utrymme på disken.
- Du kan se disk fel när du bläddrar till kudu-konsolen.
- Distribution från Azure DevOps eller Visual Studio kan Miss lyckas med `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)` .
- Din app kan medföra sämre prestanda.

<a id="NetworkDrives"></a>

### <a name="network-drives-unc-shares"></a>Nätverks enheter (UNC-resurser)
En av de unika aspekterna av App Service som gör det enkelt att distribuera appar och underhålla är att allt användar innehåll lagras på en uppsättning UNC-resurser. Den här modellen mappar väl till det gemensamma mönstret för innehålls lagring som används av lokala webb värd miljöer som har flera belastningsutjämnade servrar. 

I App Service finns ett antal UNC-resurser som skapats i varje data Center. En procent andel av användar innehållet för alla kunder i varje data Center tilldelas till varje UNC-resurs. Dessutom placeras allt fil innehåll för en enskild kunds prenumeration alltid på samma UNC-resurs. 

På grund av hur Azure-tjänster fungerar ändras den aktuella virtuella dator som är ansvarig för att vara värd för en UNC-resurs med tiden. Det garanterar att UNC-resurser monteras av olika virtuella datorer när de tas upp och ned under normal Azure-åtgärder. Därför bör appar aldrig göra hårdkodade antaganden om att dator informationen i en UNC-sökväg förblir stabil över tid. I stället bör de använda den praktiska *faux* -absoluta sökvägen **D:\home\site** som App Service tillhandahåller. Den här faux-absoluta sökvägen ger en portabel, app-och-User-oberoende-metod för att referera till en egen app. Genom att använda **D:\home\site** kan en överföra delade filer från appen till appen utan att behöva konfigurera en ny absolut sökväg för varje överföring.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Typer av fil åtkomst som beviljats till en app
Varje kunds prenumeration har en reserverad katalog struktur på en specifik UNC-resurs i ett Data Center. En kund kan ha flera appar som skapats inom ett visst Data Center, så att alla kataloger som tillhör en enskild kund prenumeration skapas på samma UNC-resurs. Resursen kan innehålla kataloger, till exempel sådana som innehåller information, fel och diagnostiska loggar och tidigare versioner av appen som skapats av käll kontroll. Som förväntat är en kunds app-kataloger tillgängliga för Läs-och skriv åtkomst vid körning av appens program kod.

På de lokala enheter som är kopplade till den virtuella datorn som kör en app reserverar App Service reserverar en del av utrymmet på C:\ enhet för maskinvaruspecifika temporär lokal lagring. Även om en app har fullständig Läs-/Skriv behörighet till sin egen tillfälliga lokala lagring, är lagringen verkligen inte avsedd att användas direkt av program koden. Avsikten är i stället att tillhandahålla temporär fil lagring för IIS och ramverk för webb program. App Service begränsar också mängden temporär lokal lagring som är tillgänglig för varje app för att förhindra att enskilda appar förbrukar stora mängder lokal fil lagring.

Två exempel på hur App Service använder temporär lokal lagring är katalogen för temporära ASP.NET-filer och katalogen för IIS-komprimerade filer. ASP.NET Compilation-systemet använder katalogen "Temporary ASP.NET Files" som en tillfällig plats för cachelagring av kompilering. IIS använder katalogen "temporära komprimerade filer i IIS" för att lagra komprimerade svar på utdata. Båda dessa typer av fil användning (och andra) mappas om i App Service till temporär lokal lagring per app. Den här ommappningen säkerställer att funktionen fortsätter som förväntat.

Varje app i App Service körs som en slumpmässig unik, låg privilegie rad arbets process identitet med namnet "programpoolsidentitet", som beskrivs här: [https://www.iis.net/learn/manage/configuring-security/application-pool-identities](https://www.iis.net/learn/manage/configuring-security/application-pool-identities) . Program koden använder den här identiteten för grundläggande skrivskyddad åtkomst till operativ system enheten (D:\ enhet). Det innebär att program koden kan visa vanliga katalog strukturer och läsa vanliga filer på operativ systemen het. Det kan verka som om det är en mycket stor åtkomst nivå, men samma kataloger och filer är tillgängliga när du etablerar en arbets roll i en Azure-värdbaserad tjänst och läser enhets innehållet. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Fil åtkomst över flera instanser
Hem katalogen innehåller en Apps innehåll och program koden kan skriva till den. Om en app körs på flera instanser delas arbets katalogen mellan alla instanser så att alla instanser ser samma katalog. Om en app exempelvis sparar överförda filer till arbets katalogen, är dessa filer omedelbart tillgängliga för alla instanser. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Nätverksåtkomst
Program koden kan använda TCP/IP-och UDP-baserade protokoll för att göra utgående nätverks anslutningar till tillgängliga slut punkter för Internet som exponerar externa tjänster. Appar kan använda samma protokoll för att ansluta till tjänster i Azure, till exempel genom att upprätta HTTPS-anslutningar till SQL Database.

Det finns också begränsad möjlighet för appar att upprätta en lokal loopback-anslutning och låta en app Lyssna på den lokala loopback-socketen. Den här funktionen finns främst för att aktivera appar som lyssnar på lokala loopback-socketar som en del av deras funktioner. Varje app ser en "privat" loopback-anslutning. Appen "A" kan inte lyssna på en lokal loopback-socket som upprättats av appen "B".

Namngivna pipes stöds också som en metod för kommunikation mellan processer (IPC) mellan olika processer som gemensamt kör en app. IIS FastCGI-modulen är till exempel beroende av namngivna pipes för att koordinera de enskilda processerna som kör PHP-sidor.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Kod körning, processer och minne
Som tidigare nämnts körs appar i låg privilegie rad arbets processer med hjälp av en slumpmässig programpoolsidentitet. Program koden har åtkomst till det minnes utrymme som är associerat med arbets processen, samt eventuella underordnade processer som kan skapas av CGI-processer eller andra program. En app kan dock inte komma åt minnet eller data från en annan app, även om den finns på samma virtuella dator.

Appar kan köra skript eller sidor skrivna med webb utvecklings ramverk som stöds. App Service konfigurerar inte några webb Ramverks inställningar till mer begränsade lägen. Till exempel kan ASP.NET-appar som körs på App Service köras i "fullständigt" förtroende i stället för ett mer begränsat förtroende läge. Web Framework, inklusive både klassisk ASP-och ASP.NET, kan anropa COM-komponenter i processen (men inte av process-COM-komponenter) som ADO (ActiveX Data Objects) som är registrerade som standard i Windows-operativsystemet.

Appar kan starta och köra godtycklig kod. Det går att använda en app för att skapa ett kommando gränssnitt eller köra ett PowerShell-skript. Men även om godtycklig kod och processer kan skapas från en app är körbara program och skript fortfarande begränsade till de behörigheter som beviljas den överordnade programpoolen. En app kan till exempel skapa en körbar fil som gör ett utgående HTTP-anrop, men samma körbara fil kan inte försöka frigöra IP-adressen för en virtuell dator från NÄTVERKSKORTet. Att göra ett utgående nätverks anrop till låg privilegie rad kod, men att försöka omkonfigurera nätverks inställningar på en virtuell dator kräver administratörs behörighet.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Diagnostikloggar och händelser
Logg information är en annan uppsättning data som försöker få åtkomst till vissa appar. De typer av logg information som är tillgängliga för kod som körs i App Service innehåller diagnostik-och logg information som genererats av en app som också är lättillgänglig för appen. 

Till exempel är W3C HTTP-loggar som genereras av en aktiv app tillgängliga antingen på en logg katalog på nätverks resurs platsen som har skapats för appen, eller är tillgänglig i Blob Storage om en kund har konfigurerat loggning av W3C till Storage. Med det senare alternativet kan stora mängder loggar samlas in utan risken att överskrida fil lagrings gränserna som är kopplade till en nätverks resurs.

I en liknande Vein kan information i real tid från .NET-appar också loggas med hjälp av infrastrukturen för .NET-spårning och diagnostik, med alternativ för att skriva spårnings informationen till antingen appens nätverks resurs eller till en Blob Storage-plats.

Områden i diagnostik-loggning och-spårning som inte är tillgängliga för appar är Windows ETW-händelser och vanliga Windows-händelseloggar (till exempel system-, program-och säkerhets händelse loggar). Eftersom ETW-spårnings information kan vara synlig för hela datorn (med rätt ACL: er) blockeras Läs-och skriv åtkomst till ETW-händelser. Utvecklare kan märka att API-anrop för att läsa och skriva ETW-händelser och vanliga Windows-händelseloggar verkar fungera, men det beror på att App Service är "Faking" anropen så att de verkar lyckas. I verkligheten har program koden ingen åtkomst till dessa händelse data.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Register åtkomst
Appar har skrivskyddad åtkomst till mycket (men inte alla) registret på den virtuella datorn som de körs på. I praktiken innebär detta att register nycklar som tillåter skrivskyddad åtkomst till den lokala användar gruppen kan nås av appar. En del av registret som för närvarande inte stöds för antingen Läs-eller skriv åtkomst är den \_ aktuella \_ användar strukturen för HKEY.

Skriv åtkomst till registret blockeras, inklusive åtkomst till alla register nycklar per användare. I appens perspektiv ska skriv åtkomst till registret aldrig förlita sig på i Azure-miljön eftersom appar kan (och göra) migreras mellan olika virtuella datorer. Den enda beständiga skrivbara lagring som kan vara beroende av en app är innehålls katalog strukturen per app som lagras på App Service UNC-resurser. 

## <a name="remote-desktop-access"></a>Åtkomst till fjärr skrivbord

App Service ger inte fjärr skrivbords åtkomst till de virtuella dator instanserna.

## <a name="more-information"></a>Mer information

[Azure App Service sandbox](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) – den mest aktuella informationen om körnings miljön för App Service. Den här sidan underhålls direkt av App Service Development-teamet.