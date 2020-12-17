---
title: Viktig information om Microsoft Azure Storage Explorer
description: Läs viktig information om den senaste versionen av Microsoft Azure Storage Explorer. Viktig information för tidigare versioner visas också.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2018
ms.author: cawa
ms.openlocfilehash: 56654f09d3a92eec5b86d8c8e4cd76fec0639394
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653052"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Viktig information om Microsoft Azure Storage Explorer

Den här artikeln innehåller den senaste versionen av Azure Storage Explorer, samt viktig information för tidigare versioner. 

[Microsoft Azure Storage Explorer](./vs-azure-tools-storage-manage-with-storage-explorer.md) är en fristående app som gör det enkelt att arbeta med Azure Storage data på Windows, MacOS och Linux.

Om du vill hämta tidigare versioner av Storage Explorer kan du besöka [sidan utgåvor](https://github.com/microsoft/AzureStorageExplorer/releases) i vår GitHub-lagrings platsen.

## <a name="version-1110"></a>Version 1.11.0
11/4/2019

### <a name="new"></a>Ny
* Åtgärder för blobbar, ADLS Gen2 och Managed Disks använder den integrerade AzCopy. Mer specifikt görs följande åtgärder med AzCopy:
   * Blobar
      * Öppna för redigering och uppladdning
      * Ladda upp, inklusive dra & släpp
      * Ladda ned
      * Kopiera & klistra in #1249
      * Ta bort
   * ADLS Gen2 blobbar
      * Ladda upp, inklusive dra & släpp
      * Ladda ned
      * Kopiera & klistra in
      * Ta bort, inklusive ta bort mapp
   * Managed Disks
      * Ladda upp
      * Ladda ned
      * Kopiera & klistra in

   Dessutom har flera vanliga begärda funktioner lagts till i den integrerade AzCopy-miljön:
   * Konflikt lösningar – du uppmanas att göra det under överföringarna för att lösa konflikter. #1455
   * Ladda upp som Page blobbar – du kan välja om du vill att AzCopy ska överföra. VHD-och. VHDX-filer som Page blobbar. #1164 och #1601
   * Konfigurerbara AzCopy-parametrar – flera inställningar har lagts till för att justera AzCopy prestanda och Resursanvändning. Se mer information nedan.

* Vi har lagt till följande funktioner för ADLS Gen2-kontona för att aktivera ADLS Gen2 och blobbar för åtkomst med flera protokoll och förbättra ADLS Gen2 upplevelser:
   * Sök med hjälp av egna namn för att ange ACL-behörigheter
   * Visa dolda behållare, till exempel $logs och $web
   * Hämta och Bryt container lån
   * Hämta och Bryt BLOB-lån #848
   * Hantera åtkomst principer för behållare
   * Konfigurera BLOB åtkomst nivåer
   * Kopiera & klistra in blobbar

* I den här versionen har vi förvisat ytterligare 17 språk. Du kan växla till ett valfritt språk på sidan Inställningar under "program" → "nationella inställningar" → "språk (för hands version)". Vi arbetar fortfarande hårt med att översätta ytterligare strängar och förbättra översättnings kvaliteten. Om du har feedback om en översättning, eller om du ser en sträng som inte har översatts än, [öppnar du ett ärende på GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=).
* I varje version försöker vi att publicera några inställningar för att möjliggöra fin aktivering Storage Explorer. I den här versionen har vi lagt till inställningar för att ytterligare konfigurera AzCopy samt för att dölja tjänst noder:
   * AzCopy bandbredds gräns – hjälper dig att styra hur mycket av nätverks-AzCopy som används. Du hittar den här inställningen på "överföringar" → "AzCopy" → "högsta överföringshastighet". #1099
   * AzCopy MD5-kontroll – låter dig konfigurera om och hur strikt AzCopy söker efter MD5-hashar vid hämtning. Du hittar den här inställningen på "överföringar" → "AzCopy" → "kontrol lera MD5".
   * AzCopy samtidighet och minnesbuffert – som standard kommer AzCopy att analysera datorn för att fastställa rimliga standardvärden för de här inställningarna. Men om du stöter på prestanda problem kan dessa avancerade inställningar användas för att ytterligare skräddarsy hur AzCopy körs på din dator. Du kan hitta de här inställningarna under "transfers" → "AzCopy". #994
   * Visa och Dölj tjänstenoder – de här inställningarna ger dig möjlighet att visa eller dölja de Azure-tjänster som Storage Explorer stöder. Du hittar de här inställningarna under avsnittet "tjänster". #1877

* När du skapar en ögonblicks bild av en hanterad disk anges nu ett standard namn. #1847
* Om du bifogar en ADLS Gen2 BLOB-behållare när du ansluter till Azure AD visas "(ADLS Gen2)" bredvid noden. #1861

### <a name="fixes"></a>Korrigeringar
* När du kopierar, laddar upp eller laddar ned stora diskar kan Storage Explorer ibland inte återkalla åtkomsten till diskarna som berörs av åtgärden. Problemet har åtgärdats. #2048
* Tabell statistik kunde inte visas vid visning av en nyckel fråga för partitionen. Problemet har åtgärdats. #1886

### <a name="known-issues"></a>Kända problem
* Storage Explorer 1.11.0 kräver nu en DFS-slutpunkt (t. ex. "myaccount.dfs.core.windows.net") som ska bifogas ADLS Gen2-behållare. Tidigare versioner av Storage Explorer har gett dig möjlighet att använda en BLOB-slutpunkt. Dessa bifogade filer kanske inte fungerar längre efter att du har uppgraderat till 1.11.0. Om det här problemet uppstår ansluter du med hjälp av DFS-slutpunkten.
* Numeriska inställningar kontrol leras inte om de ligger inom ett giltigt intervall. #2140
* Att kopiera BLOB-behållare från ett lagrings konto till ett annat i trädvyn kan Miss lyckas. Vi undersöker problemet. #2124
* Inställningen för automatisk uppdatering påverkar inte alla åtgärder i BLOB Explorer än.
* Hanterade disk funktioner stöds inte i Azure Stack.
* Om en disk uppladdning eller inklistring Miss lyckas och en ny disk har skapats före misslyckandet, kommer Storage Explorer inte att ta bort disken åt dig.
* Beroende på när du avbryter en disk uppladdning eller inklistring, är det möjligt att lämna den nya disken i ett skadat tillstånd. Om detta händer måste du antingen ta bort den nya disken eller manuellt anropa disk-API: erna för att ersätta innehållet på disken så att den inte längre är skadad.
* När du använder RBAC kräver Storage Explorer vissa behörigheter för hanterings lager för att få åtkomst till dina lagrings resurser. Mer information finns i [fel söknings guiden](./storage/common/storage-explorer-troubleshooting.md) .
* Om du kopplar från en resurs som är ansluten via SAS URI, till exempel en BLOB-behållare, kan det orsaka ett fel som förhindrar att andra bilagor visas korrekt. Undvik det här problemet genom att bara uppdatera gruppnoden. Se #537 för mer information.
* Om du använder VS för Mac och har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Undvik problemet genom att ta bort innehållet från ~/. IdentityService/AadConfigurations. Om du gör det kan du kommentera det här problemet.
* Azurite har ännu inte implementerat alla API: er för lagring. Därför kan det uppstå oväntade fel eller beteenden när du använder Azurite för utvecklings lagring.
* I sällsynta fall kan träd fokus bli fastnat i snabb åtkomst. Om du vill avaktivera fokus kan du uppdatera alla.
* Det går inte att ladda upp från din OneDrive-mapp på grund av ett fel i NodeJS. Felet har åtgärd ATS men har ännu inte integrerats i Electron. För att undvika det här problemet när du laddar upp eller laddar ned från en BLOB-behållare kan du använda funktionen experimentell AzCopy.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs här.
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Försök att använda dessa funktioner när du arbetar med Azure Stack-resurser kan resultera i oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
   * ADLS Gen2
   * Managed Disks
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Att köra Storage Explorer på Linux kräver att vissa beroenden installeras först. Mer information finns i [fel söknings guiden](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) för Storage Explorer.

## <a name="previous-releases"></a>Tidigare versioner

* [Version 1.10.1](#version-1101)
* [Version 1.10.0](#version-1100)
* [Version 1.9.0](#version-190)
* [Version 1.8.1](#version-181)
* [Version 1.8.0](#version-180)
* [Version 1.7.0](#version-170)
* [Version 1.6.2](#version-162)
* [Version 1.6.1](#version-161)
* [Version 1.6.0](#version-160)
* [Version 1.5.0](#version-150)
* [Version 1.4.4](#version-144)
* [Version 1.4.3](#version-143)
* [Version 1.4.2](#version-142)
* [Version 1.4.1](#version-141)
* [Version 1.3.0](#version-130)
* [Version 1.2.0](#version-120)
* [Version 1.1.0](#version-110)
* [Version 1.0.0](#version-100)
* [Version 0.9.6](#version-096)
* [Version 0.9.5](#version-095)
* [Version 0.9.4 och 0.9.3](#version-094-and-093)
* [Version 0.9.2](#version-092)
* [Version 0.9.1 till och och 0.9.0](#version-091-and-090)
* [Version 0.8.16](#version-0816)
* [Version 0.8.14](#version-0814)
* [Version 0.8.13](#version-0813)
* [Version 0.8.12 och 0.8.11 och 0.8.10](#version-0812-and-0811-and-0810)
* [Version 0.8.9 och 0.8.8](#version-089-and-088)
* [Version 0.8.7](#version-087)
* [Version 0.8.6](#version-086)
* [Version 0.8.5](#version-085)
* [Version 0.8.4](#version-084)
* [Version 0.8.3](#version-083)
* [Version 0.8.2](#version-082)
* [Version 0.8.0](#version-080)
* [Version 0.7.20160509.0](#version-07201605090)
* [Version 0.7.20160325.0](#version-07201603250)
* [Version 0.7.20160129.1](#version-07201601291)
* [Version 0.7.20160105.0](#version-07201601050)
* [Version 0.7.20151116.0](#version-07201511160)

## <a name="version-1101"></a>Version 1.10.1
9/19/2019

### <a name="hotfix"></a>Löser
* Vissa användare påträffade ett fel i 1.10.0 vid försök att visa sina data i sina ADLS gen 1-konton. Det här felet förhindrade att Explorer-panelen renderas korrekt. Problemet har åtgärdats. #1853 #1865

### <a name="new"></a>Ny
* Storage Explorer har nu ett särskilt inställnings gränssnitt. Du kan komma åt den via redigera → inställningar eller genom att klicka på inställnings ikonen (kugg hjulet) i det vänstra lodräta verktygsfältet. Den här funktionen är det första steget som vi vidtar för att tillhandahålla en rad olika [användare som begär inställningar](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). Från och med den här versionen stöds följande inställningar:
  * Tema
  * Proxy
  * Logga ut vid avslut #6
  * Aktivera inloggning med enhets kod flöde
  * Uppdatera #1526 automatiskt
  * Aktivera AzCopy
  * AzCopy SAS-varaktighet om det finns andra inställningar som du vill lägga till, [öppnar du ett problem på GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=) som beskriver den inställning som du vill se.
* Storage Explorer stöder nu Managed Disks. Du kan:
  * Ladda upp en lokal virtuell hård disk till en ny disk
  * Ladda ned en disk
  * Kopiera och klistra in diskar i resurs grupper och regioner
  * Ta bort diskar
  * Skapa en ögonblicks bild av en disk

Kopiering, hämtning och lagring mellan regioner av diskar drivs av AzCopy v10.
* Storage Explorer kan nu installeras via Snap Store på Linux. När du installerar via Snap Store installeras alla beroenden åt dig, inklusive .NET Core! För närvarande har vi kontrollerat att Storage Explorer fungerar bra på Ubuntu och CentOS. Om du stöter på problem med att installera från snapin Store på andra Linux-distributioner, [öppnar du ett problem på GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Mer information om hur du installerar från Snap Store finns i [guiden komma igång](./vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux). #68
* Två större ändringar har gjorts för att bifoga Azure Active Directory (Azure AD) som är avsedda att göra funktionen mer användbar för ADLS Gen2 användare:
  * Nu väljer du den klient som resursen som du ansluter till finns i. Det innebär att du inte längre behöver ha RBAC-åtkomst till resursens prenumeration.
  * Om du bifogar en ADLS Gen2 BLOB-behållare kan du nu ansluta till en angiven sökväg i behållaren.
* När du hanterar ACL: er för ADLS Gen2 filer och Storage Explorer mappar visas nu de egna namnen för entiteter i ACL: en. #957
* När du lägger till via OID i en ADLS Gen2 ACL, kommer Storage Explorer nu att verifiera att OID tillhör en giltig entitet i din klient organisation. #1603
* Kortkommandon för navigering mellan flikar använder nu fler standard tangentkombinationer. #1018
* Om du klickar på en flik stängs det nu. #1348
* Om en AzCopy Storage Explorer-överföring innehåller hoppa över och inga fel visas nu en varnings ikon för att markera att Skip har inträffat. #1490
* Den integrerade AzCopy har uppdaterats till version 10.2.1. Dessutom kan du nu Visa versionen av AzCopy som är installerad i dialog rutan om. #1343

### <a name="fixes"></a>Korrigeringar
* Många användare har kört i olika "det går inte att läsa versionen av odefinierad" eller "det går inte att läsa en anslutning av odefinierade"-fel när du arbetar med anslutna lagrings konton. Även om vi fortfarande fortsätter att undersöka grund orsaken till problemet i 1.10.0 har vi förbättrat fel hanteringen vid inläsning av anslutna lagrings konton. #1626, #985 och #1532
* Det var möjligt för Explorer-trädet (vänster sida) att hamna i ett tillstånd där fokus skulle gå till den översta noden flera gånger. Problemet har åtgärdats. #1596
* Vid hantering av en BLOB-ögonblicksbilder läser inte skärm läsare den tidsstämpel som är kopplad till ögonblicks bilden. Problemet har åtgärdats. #1202
* Det gick inte att ställa in proxy-inställningen på macOS i tid för att autentiseringsprocessen ska användas. Problemet har åtgärdats. #1567
* Om ett lagrings konto i ett suveränt moln anslöts med hjälp av namn och nyckel skulle AzCopy inte fungera. Problemet har åtgärdats. #1544
* När du ansluter via en anslutnings sträng tar Storage Explorer nu bort efterföljande blank steg. #1387

### <a name="known-issues"></a>Kända problem
* Inställningen för automatisk uppdatering påverkar inte alla åtgärder i BLOB Explorer än.
* Hanterade disk funktioner stöds inte i Azure Stack.
* Om en disk uppladdning eller inklistring Miss lyckas och en ny disk har skapats före misslyckandet, kommer Storage Explorer inte att ta bort disken åt dig.
* Beroende på när du avbryter en disk uppladdning eller inklistring, är det möjligt att lämna den nya disken i ett skadat tillstånd. Om detta händer måste du antingen ta bort den nya disken eller manuellt anropa disk-API: erna för att ersätta innehållet på disken så att den inte längre är skadad.
* Beroende på när du avbryter en disk uppladdning eller inklistring, är det möjligt att lämna den nya disken i ett skadat tillstånd. Om detta händer måste du antingen ta bort den nya disken eller manuellt anropa disk-API: erna för att ersätta innehållet på disken så att den inte längre är skadad.
* När du utför en icke-AzCopy BLOB-hämtning, verifieras inte MD5 för stora filer. Detta beror på ett fel i Storage SDK: n. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* När du använder RBAC kräver Storage Explorer vissa behörigheter för hanterings lager för att få åtkomst till dina lagrings resurser. Mer information finns i [fel söknings guiden](./storage/common/storage-explorer-troubleshooting.md) .
* Om du kopplar från en resurs som är ansluten via SAS URI, till exempel en BLOB-behållare, kan det orsaka ett fel som förhindrar att andra bilagor visas korrekt. Undvik det här problemet genom att bara uppdatera gruppnoden. Se #537 för mer information.
* Om du använder VS för Mac och har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Undvik problemet genom att ta bort innehållet från ~/. IdentityService/AadConfigurations. Om du gör det kan du kommentera det här problemet.
* Azurite har ännu inte implementerat alla API: er för lagring. Därför kan det uppstå oväntade fel eller beteenden när du använder Azurite för utvecklings lagring.
* I sällsynta fall kan träd fokus bli fastnat i snabb åtkomst. Om du vill avaktivera fokus kan du uppdatera alla.
* Det går inte att ladda upp från din OneDrive-mapp på grund av ett fel i NodeJS. Felet har åtgärd ATS men har ännu inte integrerats i Electron. För att undvika det här problemet när du laddar upp eller laddar ned från en BLOB-behållare kan du använda funktionen experimentell AzCopy.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs här.
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Försök att använda dessa funktioner när du arbetar med Azure Stack-resurser kan resultera i oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
   * ADLS Gen2
   * Managed Disks
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Att köra Storage Explorer på Linux kräver att vissa beroenden installeras först. Mer information finns i [fel söknings guiden](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) för Storage Explorer.


## <a name="version-1100"></a>Version 1.10.0
9/12/2019

### <a name="new"></a>Ny

* Storage Explorer har nu ett särskilt inställnings gränssnitt. Du kan komma åt den via redigera → inställningar eller genom att klicka på inställnings ikonen (kugg hjulet) i det vänstra lodräta verktygsfältet. Den här funktionen är det första steget som vi vidtar för att tillhandahålla en rad olika [användare som begär inställningar](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate). Från och med den här versionen stöds följande inställningar:
    * Tema
    * Proxy
    * Logga ut vid avslut [#6](https://www.github.com/Microsoft/AzureStorageExplorer/issues/6)
    * Aktivera inloggning med enhets kod flöde
    * Uppdatera [#1526](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1526) automatiskt
    * Aktivera AzCopy
    * AzCopy SAS-varaktighet

    Om det finns andra inställningar som du vill lägga till, öppnar du [ett ärende på GitHub som beskriver den inställning som du vill se](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=).
* Storage Explorer stöder nu Managed Disks. Du kan:
    * Ladda upp en lokal virtuell hård disk till en ny disk
    * Ladda ned en disk
    * Kopiera och klistra in diskar i resurs grupper och regioner
    * Ta bort diskar
    * Skapa en ögonblicks bild av en disk

    Kopiering, hämtning och lagring mellan regioner av diskar drivs av AzCopy v10.
* Storage Explorer kan nu installeras via Snap Store på Linux. När du installerar via Snap Store installeras alla beroenden åt dig, inklusive .NET Core! För närvarande har vi kontrollerat att Storage Explorer fungerar bra på Ubuntu och CentOS. Om du stöter på problem med att installera från snapin Store på andra Linux-distributioner, [öppnar du ett problem på GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Mer information om hur du installerar från Snap Store finns i [guiden komma igång](./vs-azure-tools-storage-manage-with-storage-explorer.md). [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
* Två större ändringar har gjorts för att bifoga Azure Active Directory (Azure AD) som är avsedda att göra funktionen mer användbar för ADLS Gen2 användare: * nu väljer du den klient som resursen som du lägger till är i. Det innebär att du inte längre behöver ha RBAC-åtkomst till resursens prenumeration.
        * Om du bifogar en ADLS Gen2 BLOB-behållare kan du nu ansluta till en angiven sökväg i behållaren.
* När du hanterar ACL: er för ADLS Gen2 filer och Storage Explorer mappar visas nu de egna namnen för entiteter i ACL: en. [#957](https://www.github.com/Microsoft/AzureStorageExplorer/issues/957)
* När du lägger till via OID i en ADLS Gen2 ACL, kommer Storage Explorer nu att verifiera att OID tillhör en giltig entitet i din klient organisation. [#1603](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1603)
* Kortkommandon för navigering mellan flikar använder nu fler standard tangentkombinationer. [#1018](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1018)
* Om du klickar på en flik stängs det nu. [#1348](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1348)
* Om en AzCopy Storage Explorer-överföring innehåller hoppa över och inga fel visas nu en varnings ikon för att markera att Skip har inträffat. [#1490](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1490)
* Den integrerade AzCopy har uppdaterats till version 10.2.1. Dessutom kan du nu Visa versionen av AzCopy som är installerad i dialog rutan om. [#1343](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1343)

### <a name="fixes"></a>Korrigeringar

* Många användare har kört i olika "det går inte att läsa versionen av odefinierad" eller "det går inte att läsa en anslutning av odefinierade"-fel när du arbetar med anslutna lagrings konton. Även om vi fortfarande fortsätter att undersöka grund orsaken till problemet i 1.10.0 har vi förbättrat fel hanteringen vid inläsning av anslutna lagrings konton. [#1626](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1626), [#985](https://www.github.com/Microsoft/AzureStorageExplorer/issues/985)och [#1532](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1532)
* Det var möjligt för Explorer-trädet (vänster sida) att hamna i ett tillstånd där fokus skulle gå till den översta noden flera gånger. Problemet har åtgärdats. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* Vid hantering av en BLOB-ögonblicksbilder läser inte skärm läsare den tidsstämpel som är kopplad till ögonblicks bilden. Problemet har åtgärdats. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* Det gick inte att ställa in proxy-inställningen på macOS i tid för att autentiseringsprocessen ska användas. Problemet har åtgärdats. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* Om ett lagrings konto i ett suveränt moln anslöts med hjälp av namn och nyckel skulle AzCopy inte fungera. Problemet har åtgärdats. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* När du ansluter via en anslutnings sträng tar Storage Explorer nu bort efterföljande blank steg. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>Kända problem

* Inställningen för automatisk uppdatering påverkar inte alla åtgärder i BLOB Explorer än.
* Hanterade disk funktioner stöds inte i Azure Stack.
* Om en disk uppladdning eller inklistring Miss lyckas och en ny disk har skapats före misslyckandet, kommer Storage Explorer inte att ta bort disken åt dig.
* Beroende på när du avbryter en disk uppladdning eller inklistring, är det möjligt att lämna den nya disken i ett skadat tillstånd. Om detta händer måste du antingen ta bort den nya disken eller manuellt anropa disk-API: erna för att ersätta innehållet på disken så att den inte längre är skadad.
* När du utför en icke-AzCopy BLOB-hämtning, verifieras inte MD5 för stora filer. Detta beror på ett fel i Storage SDK: n. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* När du använder RBAC kräver Storage Explorer vissa behörigheter för hanterings lager för att få åtkomst till dina lagrings resurser. Mer information finns i [fel söknings guiden](./storage/common/storage-explorer-troubleshooting.md) .
* Om du kopplar från en resurs som är ansluten via SAS URI, till exempel en BLOB-behållare, kan det orsaka ett fel som förhindrar att andra bilagor visas korrekt. Undvik det här problemet genom att bara uppdatera gruppnoden. Se #537 för mer information.
* Om du använder VS för Mac och har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Undvik problemet genom att ta bort innehållet från ~/. IdentityService/AadConfigurations. Om du gör det kan du kommentera det här problemet.
* Azurite har ännu inte implementerat alla API: er för lagring. Därför kan det uppstå oväntade fel eller beteenden när du använder Azurite för utvecklings lagring.
* I sällsynta fall kan träd fokus bli fastnat i snabb åtkomst. Om du vill avaktivera fokus kan du uppdatera alla.
* Det går inte att ladda upp från din OneDrive-mapp på grund av ett fel i NodeJS. Felet har åtgärd ATS men har ännu inte integrerats i Electron. För att undvika det här problemet när du laddar upp eller laddar ned från en BLOB-behållare kan du använda funktionen experimentell AzCopy.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs här.
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Försök att använda dessa funktioner när du arbetar med Azure Stack-resurser kan resultera i oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
   * ADLS Gen2
   * Managed Disks
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Att köra Storage Explorer på Linux kräver att vissa beroenden installeras först. Mer information finns i [fel söknings guiden](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) för Storage Explorer.

## <a name="version-190"></a>Version 1.9.0
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>Ladda ned Azure Storage Explorer 1.9.0
- [Azure Storage Explorer 1.9.0 för Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer 1.9.0 för Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.9.0 för Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Ny

* Nu kan du bifoga BLOB-behållare via Azure AD (RBAC eller ACL-behörigheter). Den här funktionen är avsedd att hjälpa användare som har åtkomst till behållare, men inte de lagrings konton som behållarna befinner sig i. Mer information om den här funktionen finns i vår Komma igångs guide.
* Hämta och Bryt lånet fungerar nu med RBAC. [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* Hantering av åtkomst principer och inställning av offentlig åtkomst nivå fungerar nu med RBAC. [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* Att ta bort BLOB-mappar fungerar nu med RBAC. [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* Att ändra BLOB-filnivån fungerar nu med RBAC. [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* Du kan nu snabbt återställa snabb åtkomst via "hjälp" → "Återställ". [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>Förhandsgranskningsfunktioner

* Logga in för enhets kod flöde är nu tillgängligt för förhands granskning. Om du vill aktivera den går du till "Förhandsgranska" → "Använd enhets kod flödes inloggning". Vi uppmuntrar alla användare som har haft problem med tomma inloggnings fönster att testa den här funktionen, eftersom det kan visa sig vara en mer tillförlitlig form av inloggning.
* Storage Explorer integrerad med AzCopy är för närvarande tillgänglig för för hands version. Om du vill aktivera den går du till "Förhandsgranska" → "Använd AzCopy för att få förbättrad BLOB-överföring och nedladdning". BLOB-överföringar som slutförts med AzCopy bör vara snabbare och mer prestera.

### <a name="fixes"></a>Korrigeringar

* Det gick inte att läsa in mer än 50 prenumerationer för ett konto. [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* Åtgärdade knappen "logga in" fungerar inte i informations fältet som visas när en direkt länk Miss lyckas. [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* Fast inte att ladda upp. apps på macOS. [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* Fast "försök alla" fungerar inte för en misslyckad BLOB-namnbyte. [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* Fast "Cancel" fungerar inte när en BLOB öppnas. [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* Åtgärdat flera stavnings-och knapp beskrivnings problem i produkten. Många tack till alla som rapporterade problemen! [#1303](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303), [#1328](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328), [#1329](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329), [#1331](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331), [#1336](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336), [#1352](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352), [#1368](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368), [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395)

### <a name="known-issues"></a>Kända problem

* När du utför en icke-AzCopy BLOB-hämtning, verifieras inte MD5 för stora filer. Detta beror på ett fel i Storage SDK: n. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* När du använder RBAC kräver Storage Explorer vissa behörigheter för hanterings lager för att få åtkomst till dina lagrings resurser. Mer information finns i [fel söknings guiden](./storage/common/storage-explorer-troubleshooting.md) .
* Försök att komma åt ADLS Gen2 blobbar efter att en proxy kan Miss lyckas.
* Om du kopplar från en resurs som är ansluten via SAS URI, till exempel en BLOB-behållare, kan det orsaka ett fel som förhindrar att andra bilagor visas korrekt. Undvik det här problemet genom att bara uppdatera gruppnoden. Se #537 för mer information.
* Om du använder VS för Mac och har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Undvik problemet genom att ta bort innehållet från ~/. IdentityService/AadConfigurations. Om du gör det kan du kommentera det här problemet.
* Azurite har ännu inte implementerat alla API: er för lagring. Därför kan det uppstå oväntade fel eller beteenden när du använder Azurite för utvecklings lagring.
* I sällsynta fall kan träd fokus bli fastnat i snabb åtkomst. Om du vill avaktivera fokus kan du uppdatera alla.
* Det går inte att ladda upp från din OneDrive-mapp på grund av ett fel i NodeJS. Felet har åtgärd ATS men har ännu inte integrerats i Electron. För att undvika det här problemet när du laddar upp eller laddar ned från en BLOB-behållare kan du använda funktionen experimentell AzCopy.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs här.
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Försök att använda dessa funktioner när du arbetar med Azure Stack-resurser kan resultera i oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
   * ADLS Gen2
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Att köra Storage Explorer på Linux kräver att vissa beroenden installeras först. Mer information finns i [fel söknings guiden](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) för Storage Explorer.

## <a name="version-181"></a>Version 1.8.1
5/13/2019

### <a name="hotfixes"></a>Snabbkorrigeringar
* I vissa fall kan du klicka på Läs in mer på resurs nivå för att inte returnera nästa sida med resurser. Problemet har åtgärdats. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* I Windows skulle AzCopy-nedladdningar bli misslyckade om en enstaka fil eller mapp laddades ned och namnet på filen eller mappen hade ett ogiltigt format för en Windows-sökväg. Problemet har åtgärdats. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* I sällsynta fall, samtidigt som du utför ett namnbyte på en fil resurs eller byter namn på en fil resurs, om kopiorna för namnbytet misslyckades, eller om lagrings tjänsten inte kunde bekräfta att kopiorna med Azure lyckades, var det möjligt att Storage Explorer ta bort originalfilerna innan kopieringen hade slutförts. Problemet har åtgärdats.

### <a name="new"></a>Ny

* Den integrerade AzCopy-versionen har uppdaterats till version 10.1.0.
* Du kan nu använda Ctrl/Cmd + R för att uppdatera den för närvarande fokuserade redigeraren. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Azure Stack Storage API-versionen har ändrats till 2017-04-17.
* I dialog rutan Hantera åtkomst för ADLS Gen2 kommer nu att synkronisera masken på ett liknande sätt som andra POSIX-behörighets verktyg. Användar gränssnittet varnar dig även om en ändring görs som gör att behörigheterna för en användare eller grupp överskrider maskens gränser. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* För AzCopy-uppladdningar är flaggan för att beräkna och ange MD5-hash nu aktive rad. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Förhandsgranskningsfunktioner

* Logga in för enhets kod flöde är nu tillgängligt för förhands granskning. Om du vill aktivera den går du till "Förhandsgranska" → "Använd enhets kod flödes inloggning". Vi uppmuntrar alla användare som har haft problem med tomma inloggnings fönster att testa den här funktionen, eftersom det kan visa sig vara en mer tillförlitlig form av inloggning.
* Storage Explorer integrerad med AzCopy är för närvarande tillgänglig för för hands version. Om du vill aktivera den går du till "Förhandsgranska" → "Använd AzCopy för att få förbättrad BLOB-överföring och nedladdning". BLOB-överföringar som slutförts med AzCopy bör vara snabbare och mer prestera.

### <a name="fixes"></a>Korrigeringar

* Dialog rutan åtkomst principer kommer inte längre att ange ett förfallo datum för lagrings åtkomst principer som inte har något förfallo datum. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Vissa ändringar har gjorts i dialog rutan skapa SAS för att se till att lagrade åtkomst principer används korrekt när du genererar en SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* När du försöker överföra en icke-512-komprimerad fil till en sid-BLOB kommer Storage Explorer nu att exponera ett mer relevant fel. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Det gick inte att kopiera en BLOB-behållare som använder ett visnings namn. Nu används det faktiska namnet på BLOB-behållaren. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Försök att utföra vissa åtgärder på en ADLS Gen2-mapp som hade Unicode-tecken i namnet skulle Miss lyckas. Alla åtgärder bör nu fungera. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Kända problem

* När du utför en icke-AzCopy BLOB-hämtning, verifieras inte MD5 för stora filer. Detta beror på ett fel i Storage SDK: n. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* När du använder RBAC kräver Storage Explorer vissa behörigheter för hanterings lager för att få åtkomst till dina lagrings resurser. Mer information finns i [fel söknings guiden](./storage/common/storage-explorer-troubleshooting.md) .
* Försök att komma åt ADLS Gen2 blobbar efter att en proxy kan Miss lyckas.
* Om du kopplar från en resurs som är ansluten via SAS URI, till exempel en BLOB-behållare, kan det orsaka ett fel som förhindrar att andra bilagor visas korrekt. Undvik det här problemet genom att bara uppdatera gruppnoden. Se #537 för mer information.
* Om du använder VS för Mac och har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Undvik problemet genom att ta bort innehållet från ~/. IdentityService/AadConfigurations. Om du gör det kan du kommentera det här problemet.
* Azurite har ännu inte implementerat alla API: er för lagring. Därför kan det uppstå oväntade fel eller beteenden när du använder Azurite för utvecklings lagring.
* I sällsynta fall kan träd fokus bli fastnat i snabb åtkomst. Om du vill avaktivera fokus kan du uppdatera alla.
* Det går inte att ladda upp från din OneDrive-mapp på grund av ett fel i NodeJS. Felet har åtgärd ATS men har ännu inte integrerats i Electron. För att undvika det här problemet när du laddar upp eller laddar ned från en BLOB-behållare kan du använda funktionen experimentell AzCopy.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs här.
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Försök att använda dessa funktioner när du arbetar med Azure Stack-resurser kan resultera i oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
   * ADLS Gen2
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Att köra Storage Explorer på Linux kräver att vissa beroenden installeras först. Mer information finns i [fel söknings guiden](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) för Storage Explorer.

## <a name="version-180"></a>Version 1.8.0
2019-05-01

### <a name="new"></a>Ny

* Den integrerade AzCopy-versionen har uppdaterats till version 10.1.0.
* Du kan nu använda Ctrl/Cmd + R för att uppdatera den för närvarande fokuserade redigeraren. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Azure Stack Storage API-versionen har ändrats till 2017-04-17.
* I dialog rutan Hantera åtkomst för ADLS Gen2 kommer nu att synkronisera masken på ett liknande sätt som andra POSIX-behörighets verktyg. Användar gränssnittet varnar dig även om en ändring görs som gör att behörigheterna för en användare eller grupp överskrider maskens gränser. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* För AzCopy-uppladdningar är flaggan för att beräkna och ange MD5-hash nu aktive rad. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Förhandsgranskningsfunktioner

* Logga in för enhets kod flöde är nu tillgängligt för förhands granskning. Om du vill aktivera den går du till "Förhandsgranska" → "Använd enhets kod flödes inloggning". Vi uppmuntrar alla användare som har haft problem med tomma inloggnings fönster att testa den här funktionen, eftersom det kan visa sig vara en mer tillförlitlig form av inloggning.
* Storage Explorer integrerad med AzCopy är för närvarande tillgänglig för för hands version. Om du vill aktivera den går du till "Förhandsgranska" → "Använd AzCopy för att få förbättrad BLOB-överföring och nedladdning". BLOB-överföringar som slutförts med AzCopy bör vara snabbare och mer prestera.

### <a name="fixes"></a>Korrigeringar

* Dialog rutan åtkomst principer kommer inte längre att ange ett förfallo datum för lagrings åtkomst principer som inte har något förfallo datum. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Vissa ändringar har gjorts i dialog rutan skapa SAS för att se till att lagrade åtkomst principer används korrekt när du genererar en SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* När du försöker överföra en icke-512-komprimerad fil till en sid-BLOB kommer Storage Explorer nu att exponera ett mer relevant fel. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Det gick inte att kopiera en BLOB-behållare som använder ett visnings namn. Nu används det faktiska namnet på BLOB-behållaren. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Försök att utföra vissa åtgärder på en ADLS Gen2-mapp som hade Unicode-tecken i namnet skulle Miss lyckas. Alla åtgärder bör nu fungera. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Kända problem

* När du utför en icke-AzCopy BLOB-hämtning, verifieras inte MD5 för stora filer. Detta beror på ett fel i Storage SDK: n. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* När du använder RBAC kräver Storage Explorer vissa behörigheter för hanterings lager för att få åtkomst till dina lagrings resurser. Mer information finns i [fel söknings guiden](./storage/common/storage-explorer-troubleshooting.md) .
* Försök att komma åt ADLS Gen2 blobbar efter att en proxy kan Miss lyckas.
* Om du kopplar från en resurs som är ansluten via SAS URI, till exempel en BLOB-behållare, kan det orsaka ett fel som förhindrar att andra bilagor visas korrekt. Undvik det här problemet genom att bara uppdatera gruppnoden. Se #537 för mer information.
* Om du använder VS för Mac och har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Undvik problemet genom att ta bort innehållet från ~/. IdentityService/AadConfigurations. Om du gör det kan du kommentera det här problemet.
* Azurite har ännu inte implementerat alla API: er för lagring. Därför kan det uppstå oväntade fel eller beteenden när du använder Azurite för utvecklings lagring.
* I sällsynta fall kan träd fokus bli fastnat i snabb åtkomst. Om du vill avaktivera fokus kan du uppdatera alla.
* Det går inte att ladda upp från din OneDrive-mapp på grund av ett fel i NodeJS. Felet har åtgärd ATS men har ännu inte integrerats i Electron. För att undvika det här problemet när du laddar upp eller laddar ned från en BLOB-behållare kan du använda funktionen experimentell AzCopy.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs här.
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Försök att använda dessa funktioner när du arbetar med Azure Stack-resurser kan resultera i oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
   * ADLS Gen2
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Att köra Storage Explorer på Linux kräver att vissa beroenden installeras först. Mer information finns i [fel söknings guiden](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) för Storage Explorer.

## <a name="version-170"></a>Version 1.7.0
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>Ladda ned Azure Storage Explorer 1.7.0
- [Azure Storage Explorer 1.7.0 för Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer 1.7.0 för Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.7.0 för Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Ny

* Du kan nu ändra ägare och ägande grupp när du hanterar åtkomst för en ADLS Gen2 behållare, fil eller mapp.
* I Windows är uppdateringen av Storage Explorer i produkten nu en stegvis installation. Detta bör resultera i en snabbare uppdaterings upplevelse. Om du föredrar en ren installation kan du ladda ned [installations programmet](https://azure.microsoft.com/features/storage-explorer/) själv och sedan installera manuellt. #1089

### <a name="preview-features"></a>Förhandsgranskningsfunktioner

* Logga in för enhets kod flöde är nu tillgängligt för förhands granskning. Om du vill aktivera den går du till "Förhandsgranska" → "Använd enhets kod flödes inloggning". Vi uppmuntrar alla användare som har haft problem med tomma inloggnings fönster att testa den här funktionen, eftersom det kan visa sig vara en mer tillförlitlig form av inloggning. #938
* Storage Explorer integrerad med AzCopy är för närvarande tillgänglig för för hands version. Om du vill aktivera den går du till "Förhandsgranska" → "Använd AzCopy för att få förbättrad BLOB-överföring och nedladdning". BLOB-överföringar som slutförts med AzCopy bör vara snabbare och mer prestera.

### <a name="fixes"></a>Korrigeringar

* Nu kan du välja den Blob-typ som du vill överföra som när AzCopy är aktive rad. #1111
* Om du tidigare har aktiverat statiska webbplatser för ett ADLS Gen2 lagrings konto och kopplat det med namn och nyckel, skulle Storage Explorer inte ha identifierat att hierarkiskt namn område har Aktiver ATS. Problemet har åtgärdats. #1081
* I BLOB-redigeraren, sorteras efter antingen kvarhållning dagar kvar eller status var bruten. Problemet har åtgärdats. #1106
* Efter 1.5.0 väntar Storage Explorer inte längre på att Server sidans kopior ska slutföras innan rapporteringen lyckades under ett namnbyte eller en kopia & klistra in. Problemet har åtgärdats. #976
* När du använder funktionen experimentell AzCopy har kommandot som kopierats efter att du klickat på "Kopiera kommando till Urklipp" inte alltid körbara. Nu kommer alla kommandon som behövs för att köra överföringen att kopieras manuellt. #1079
* Tidigare var ADLS Gen2 blobbar inte tillgängliga om du var bakom en proxy. Detta beror på ett fel i ett nytt nätverks bibliotek som används av Storage SDK. I 1.7.0 har ett försök att minska det här problemet, men vissa personer kan fortsätta att se problem. En fullständig korrigering publiceras i en framtida uppdatering. #1090
* I 1.7.0, kommer dialog rutan Spara fil att korrekt ändra den senaste platsen som du sparade en fil till. #16
* På panelen Egenskaper visas SKU-nivån för ett lagrings konto som kontots typ. Problemet har åtgärdats. #654
* Ibland var det omöjligt att avbryta lånet av en BLOB, även om du har angett namnet på blobben korrekt. Problemet har åtgärdats. #1070

### <a name="known-issues"></a>Kända problem

* När du använder RBAC kräver Storage Explorer vissa behörigheter för hanterings lager för att få åtkomst till dina lagrings resurser. Mer information finns i [fel söknings guiden](./storage/common/storage-explorer-troubleshooting.md) .
* Försök att komma åt ADLS Gen2 blobbar efter att en proxy kan Miss lyckas.
* Om du kopplar från en resurs som är ansluten via SAS URI, till exempel en BLOB-behållare, kan det orsaka ett fel som förhindrar att andra bilagor visas korrekt. Undvik det här problemet genom att bara uppdatera gruppnoden. Se #537 för mer information.
* Om du kopplar från en resurs som är ansluten via SAS URI, till exempel en BLOB-behållare, kan det orsaka ett fel som förhindrar att andra bilagor visas korrekt. Undvik det här problemet genom att bara uppdatera gruppnoden. Mer information finns i #537.
* Om du använder VS för Mac och har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Undvik problemet genom att ta bort innehållet från ~/. IdentityService/AadConfigurations. Om du gör det kan du kommentera det här problemet.
* Azurite har ännu inte implementerat alla API: er för lagring. Därför kan det uppstå oväntade fel eller beteenden när du använder Azurite för utvecklings lagring.
* I sällsynta fall kan träd fokus bli fastnat i snabb åtkomst. Om du vill avaktivera fokus kan du uppdatera alla.
* Det går inte att ladda upp från din OneDrive-mapp på grund av ett fel i NodeJS. Felet har åtgärd ATS men har ännu inte integrerats i Electron. För att undvika det här problemet när du laddar upp eller laddar ned från en BLOB-behållare kan du använda funktionen experimentell AzCopy.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs här.
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Försök att använda dessa funktioner när du arbetar med Azure Stack-resurser kan resultera i oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14,04 måste du se till att GCC är uppdaterat. Detta kan du göra genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17,04 behöver du installera GConf – du kan göra detta genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>Version 1.6.2
1/9/2019

### <a name="hotfixes"></a>Snabbkorrigeringar
* I 1.6.1 har entiteter som lagts till ADLS Gen2 ACL: er av ObjectId, som inte användare har lagts till som grupper. Nu läggs endast grupper till som grupper och entiteter som företags program andService-huvudobjekt läggs till som användare. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Om ett ADLS Gen2 lagrings konto inte har några behållare och kopplats till namnet och nyckeln, identifierar Storage Explorer inte att lagrings kontot var ADLS Gen2. Problemet har åtgärdats. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* I 1.6.0 uppmanas inte konflikter under kopiering och inklistring för en lösning. I stället fungerar inte den konfliktskapande kopian. I den första konflikten får du nu en fråga om hur du vill att den ska lösas. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* På grund av API-begränsningar har all validering av ObjectIds i dialog rutan Hantera åtkomst inaktiverats. Verifiering sker nu bara för användar-UPN. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* I dialog rutan ADLS Gen2 hantera åtkomst går det inte att ändra behörigheterna för en grupp. Problemet har åtgärdats. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Har lagt till dra och släpp överförings stöd till ADLS Gen2 redigeraren. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* URL-egenskapen i dialog rutan Egenskaper för ADLS Gen2 filer och mappar saknar ibland '/'. Problemet har åtgärdats. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Om det inte går att hämta de aktuella behörigheterna för en ADLS Gen2 behållare, fil eller mapp, visas felet nu i aktivitets loggen. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Den tillfälliga sökvägen som skapades för att öppna filer har kortats ned för att minska risken för att skapa en sökväg som är längre än MAX_PATH i Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Dialog rutan Anslut visas nu på rätt sätt när det inte finns några inloggade användare och inga resurser har bifogats. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* När du sparar egenskaper för icke-HNS-blobbar och filer i 1.6.0 kodas värdet för varje egenskap. Detta ledde till onödig kodning av värden som endast innehåller ASCII-tecken. Nu kommer värdena endast att kodas om de innehåller icke-ASCII-tecken. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Att ladda upp en mapp till en icke-HNS BLOB-behållare kan inte köras om en SAS användes och SAS inte hade Läs behörighet. Problemet har åtgärdats. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Det gick inte att avbryta en AzCopy-överföring. Problemet har åtgärdats. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy skulle inte fungera när en mapp skulle hämtas från en ADLS Gen2 BLOB-behållare om mappen hade blank steg i sitt namn. Problemet har åtgärdats. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* CosmosDB-redigeraren har brutits i 1.6.0. Den har nu åtgärd ATS. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Ny

* Du kan nu använda Storage Explorer för att få åtkomst till dina BLOB-data via [RBAC](./storage/common/storage-auth-aad-rbac-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Om du är inloggad och Storage Explorer inte kan hämta nycklarna för ditt lagrings konto, används en OAuth-token för att autentisera när du interagerar med dina data.
* Storage Explorer stöder nu ADLS Gen2 lagrings konton. När Storage Explorer upptäcker att hierarkiskt namn område har Aktiver ATS för ett lagrings konto visas "(ADLS Gen2 för hands version) bredvid namnet på ditt lagrings konto. Storage Explorer kan identifiera om hierarkiskt namn område är aktiverat när du är inloggad eller om du har kopplat ditt lagrings konto med namn och nyckel. För ADLS Gen2 lagrings konton kan du använda Storage Explorer för att:
  * Skapa och ta bort behållare
  * Hantera behållar egenskaper och behörigheter (vänster sida)
  * Visa och navigera i data i behållare
  * Skapa nya mappar
  * Ladda upp, ladda ned, byta namn på och ta bort filer och mappar
  * Hantera egenskaper och behörigheter för filer och mappar (höger sida).

    Andra typiska BLOB-funktioner, till exempel mjuk borttagning och ögonblicks bilder, är inte tillgängliga för närvarande. Hanterings behörigheter är också bara tillgängligt när du är inloggad. När du arbetar i ett ADLS Gen2 lagrings konto använder Storage Explorer dessutom AzCopy för alla uppladdningar och nedladdningar och standard för att använda namn-och autentiseringsuppgifter för alla åtgärder om det är tillgängligt.
* När du har fått en stark användare kan du använda Bryt lån igen för att avbryta lån på flera blobbar samtidigt.

### <a name="known-issues"></a>Kända problem

* Om en av de filer som överförs redan finns vid hämtning från ett ADLS Gen2 lagrings konto kommer AzCopy ibland att krascha. Detta kommer att åtgärdas i en kommande snabb korrigering.
* Om du kopplar från en resurs som är ansluten via SAS URI, till exempel en BLOB-behållare, kan det orsaka ett fel som förhindrar att andra bilagor visas korrekt. Undvik det här problemet genom att bara uppdatera gruppnoden. Mer information finns i #537.
* Om du använder VS för Mac och har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Undvik problemet genom att ta bort innehållet från ~/. IdentityService/AadConfigurations. Om du gör det kan du kommentera det här problemet.
* Azurite har ännu inte implementerat alla API: er för lagring. Därför kan det uppstå oväntade fel eller beteenden när du använder Azurite för utvecklings lagring.
* I sällsynta fall kan träd fokus bli fastnat i snabb åtkomst. Om du vill avaktivera fokus kan du uppdatera alla.
* Det går inte att ladda upp från din OneDrive-mapp på grund av ett fel i NodeJS. Felet har åtgärd ATS men har ännu inte integrerats i Electron. För att undvika det här problemet när du laddar upp eller laddar ned från en BLOB-behållare kan du använda funktionen experimentell AzCopy.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs här.
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Försök att använda dessa funktioner när du arbetar med Azure Stack-resurser kan resultera i oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14,04 måste du se till att GCC är uppdaterat. Detta kan du göra genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17,04 behöver du installera GConf – du kan göra detta genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>Version 1.6.1
12/18/2018

### <a name="hotfixes"></a>Snabbkorrigeringar
* På grund av API-begränsningar har all validering av ObjectIds i dialog rutan Hantera åtkomst inaktiverats. Verifiering sker nu bara för användar-UPN. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* I dialog rutan ADLS Gen2 hantera åtkomst går det inte att ändra behörigheterna för en grupp. Problemet har åtgärdats. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Har lagt till dra och släpp överförings stöd till ADLS Gen2 redigeraren. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* URL-egenskapen i dialog rutan Egenskaper för ADLS Gen2 filer och mappar saknar ibland '/'. Problemet har åtgärdats. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Om det inte går att hämta de aktuella behörigheterna för en ADLS Gen2 behållare, fil eller mapp, visas felet nu i aktivitets loggen. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Den tillfälliga sökvägen som skapades för att öppna filer har kortats ned för att minska risken för att skapa en sökväg som är längre än MAX_PATH i Windows. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Dialog rutan Anslut visas nu på rätt sätt när det inte finns några inloggade användare och inga resurser har bifogats. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* När du sparar egenskaper för icke-HNS-blobbar och filer i 1.6.0 kodas värdet för varje egenskap. Detta ledde till onödig kodning av värden som endast innehåller ASCII-tecken. Nu kommer värdena endast att kodas om de innehåller icke-ASCII-tecken. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Att ladda upp en mapp till en icke-HNS BLOB-behållare kan inte köras om en SAS användes och SAS inte hade Läs behörighet. Problemet har åtgärdats. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Det gick inte att avbryta en AzCopy-överföring. Problemet har åtgärdats. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy skulle inte fungera när en mapp skulle hämtas från en ADLS Gen2 BLOB-behållare om mappen hade blank steg i sitt namn. Problemet har åtgärdats. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* CosmosDB-redigeraren har brutits i 1.6.0. Den har nu åtgärd ATS. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Ny

* Du kan nu använda Storage Explorer för att få åtkomst till dina BLOB-data via [RBAC](./storage/common/storage-auth-aad-rbac-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Om du är inloggad och Storage Explorer inte kan hämta nycklarna för ditt lagrings konto, används en OAuth-token för att autentisera när du interagerar med dina data.
* Storage Explorer stöder nu ADLS Gen2 lagrings konton. När Storage Explorer upptäcker att hierarkiskt namn område har Aktiver ATS för ett lagrings konto visas "(ADLS Gen2 för hands version) bredvid namnet på ditt lagrings konto. Storage Explorer kan identifiera om hierarkiskt namn område är aktiverat när du är inloggad eller om du har kopplat ditt lagrings konto med namn och nyckel. För ADLS Gen2 lagrings konton kan du använda Storage Explorer för att:
  * Skapa och ta bort behållare
  * Hantera behållar egenskaper och behörigheter (vänster sida)
  * Visa och navigera i data i behållare
  * Skapa nya mappar
  * Ladda upp, ladda ned, byta namn på och ta bort filer och mappar
  * Hantera egenskaper och behörigheter för filer och mappar (höger sida).
    
    Andra typiska BLOB-funktioner, till exempel mjuk borttagning och ögonblicks bilder, är inte tillgängliga för närvarande. Hanterings behörigheter är också bara tillgängligt när du är inloggad. När du arbetar i ett ADLS Gen2 lagrings konto använder Storage Explorer dessutom AzCopy för alla uppladdningar och nedladdningar och standard för att använda namn-och autentiseringsuppgifter för alla åtgärder om det är tillgängligt.
* När du har fått en stark användare kan du använda Bryt lån igen för att avbryta lån på flera blobbar samtidigt.

### <a name="known-issues"></a>Kända problem

* Om en av de filer som överförs redan finns vid hämtning från ett ADLS Gen2 lagrings konto kommer AzCopy ibland att krascha. Detta kommer att åtgärdas i en kommande snabb korrigering.
* Om du kopplar från en resurs som är ansluten via SAS URI, till exempel en BLOB-behållare, kan det orsaka ett fel som förhindrar att andra bilagor visas korrekt. Undvik det här problemet genom att bara uppdatera gruppnoden. Mer information finns i #537.
* Om du använder VS för Mac och har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Undvik problemet genom att ta bort innehållet från ~/. IdentityService/AadConfigurations. Om du gör det kan du kommentera det här problemet.
* Azurite har ännu inte implementerat alla API: er för lagring. Därför kan det uppstå oväntade fel eller beteenden när du använder Azurite för utvecklings lagring.
* I sällsynta fall kan träd fokus bli fastnat i snabb åtkomst. Om du vill avaktivera fokus kan du uppdatera alla.
* Det går inte att ladda upp från din OneDrive-mapp på grund av ett fel i NodeJS. Felet har åtgärd ATS men har ännu inte integrerats i Electron. För att undvika det här problemet när du laddar upp eller laddar ned från en BLOB-behållare kan du använda funktionen experimentell AzCopy.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs här.
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Försök att använda dessa funktioner när du arbetar med Azure Stack-resurser kan resultera i oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14,04 måste du se till att GCC är uppdaterat. Detta kan du göra genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17,04 behöver du installera GConf – du kan göra detta genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>Version 1.6.0
2018-12-05

### <a name="new"></a>Ny

* Du kan nu använda Storage Explorer för att få åtkomst till dina BLOB-data via [RBAC](./storage/common/storage-auth-aad-rbac-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Om du är inloggad och Storage Explorer inte kan hämta nycklarna för ditt lagrings konto, används en OAuth-token för att autentisera när du interagerar med dina data.
* Storage Explorer stöder nu ADLS Gen2 lagrings konton. När Storage Explorer upptäcker att hierarkiskt namn område har Aktiver ATS för ett lagrings konto visas "(ADLS Gen2 för hands version) bredvid namnet på ditt lagrings konto. Storage Explorer kan identifiera om hierarkiskt namn område är aktiverat när du är inloggad eller om du har kopplat ditt lagrings konto med namn och nyckel. För ADLS Gen2 lagrings konton kan du använda Storage Explorer för att:
  * Skapa och ta bort behållare
  * Hantera behållar egenskaper och behörigheter (vänster sida)
  * Visa och navigera i data i behållare
  * Skapa nya mappar
  * Ladda upp, ladda ned, byta namn på och ta bort filer och mappar
  * Hantera egenskaper och behörigheter för filer och mappar (höger sida).
    
    Andra typiska BLOB-funktioner, till exempel mjuk borttagning och ögonblicks bilder, är inte tillgängliga för närvarande. Hanterings behörigheter är också bara tillgängligt när du är inloggad. När du arbetar i ett ADLS Gen2 lagrings konto använder Storage Explorer dessutom AzCopy för alla uppladdningar och nedladdningar och standard för att använda namn-och autentiseringsuppgifter för alla åtgärder om det är tillgängligt.
* När du har fått en stark användare kan du använda Bryt lån igen för att avbryta lån på flera blobbar samtidigt.

### <a name="known-issues"></a>Kända problem

* Om en av de filer som överförs redan finns vid hämtning från ett ADLS Gen2 lagrings konto kommer AzCopy ibland att krascha. Detta kommer att åtgärdas i en kommande snabb korrigering.
* Om du kopplar från en resurs som är ansluten via SAS URI, till exempel en BLOB-behållare, kan det orsaka ett fel som förhindrar att andra bilagor visas korrekt. Undvik det här problemet genom att bara uppdatera gruppnoden. Mer information finns i #537.
* Om du använder VS för Mac och har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Undvik problemet genom att ta bort innehållet från ~/. IdentityService/AadConfigurations. Om du gör det kan du kommentera det här problemet.
* Azurite har ännu inte implementerat alla API: er för lagring. Därför kan det uppstå oväntade fel eller beteenden när du använder Azurite för utvecklings lagring.
* I sällsynta fall kan träd fokus bli fastnat i snabb åtkomst. Om du vill avaktivera fokus kan du uppdatera alla.
* Det går inte att ladda upp från din OneDrive-mapp på grund av ett fel i NodeJS. Felet har åtgärd ATS men har ännu inte integrerats i Electron. För att undvika det här problemet när du laddar upp eller laddar ned från en BLOB-behållare kan du använda funktionen experimentell AzCopy.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs här.
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Försök att använda dessa funktioner när du arbetar med Azure Stack-resurser kan resultera i oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14,04 måste du se till att GCC är uppdaterat. Detta kan du göra genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17,04 behöver du installera GConf – du kan göra detta genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>Version 1.5.0
10/29/2018

### <a name="new"></a>Ny

* Nu kan du använda [AzCopy v10 (för hands version)](https://github.com/Azure/azure-storage-azcopy) för att ladda upp och ladda ned blobbar. Aktivera den här funktionen genom att gå till menyn "experimentell" och klicka på "Använd AzCopy för förbättrad BLOB-överföring och hämtning". När det är aktiverat används AzCopy i följande scenarier:
   * Ladda upp mappar och filer till BLOB-behållare, antingen via verktygsfältet eller dra och släpp.
   * Hämtning av mappar och filer, antingen via verktygsfältet eller snabb menyn.

* När du använder AzCopy kan du dessutom:
   * Du kan kopiera AzCopy-kommandot som används för att köra överföringen till Urklipp. Klicka bara på Kopiera AzCopy-kommando till Urklipp i aktivitets loggen.
   * Du måste uppdatera BLOB-redigeraren manuellt när du har laddat upp.
   * Det finns inte stöd för att ladda upp filer till tillägg till blobar, och VHD-filer laddas upp som Page blobbar, och alla andra filer laddas upp som block blobbar.
   * Fel och konflikter som inträffar under uppladdningen eller hämtningen visas inte förrän överföringen eller hämtningen är färdig.

Slutligen kommer stöd för att använda AzCopy med fil resurser att komma i framtiden.
* Storage Explorer använder nu Electron-versionen 2.0.11.
* Avbrytande lån kan nu bara utföras på en BLOB i taget. Dessutom måste du ange namnet på den BLOB vars lån du bryter upp. Den här ändringen gjordes för att minska sannolikheten för att oavsiktligt bryta ett lån, särskilt för virtuella datorer. #394
* Om du stöter på inloggnings problem kan du nu försöka att återställa autentiseringen. Gå till menyn Hjälp och klicka på Återställ för att få åtkomst till den här funktionen. #419

### <a name="fix"></a>Åtgärda

* När starka användare har återkallats har noden standard-emulator återaktiverats. Du kan fortfarande lägga till ytterligare emulator-anslutningar via dialog rutan Anslut, men om din emulator har kon figurer ATS för att använda standard portarna kan du också använda noden "emulator * standard portar" under "lokala & anslutna/lagrings konton". #669
* Storage Explorer kan inte längre ange BLOB-attributvärden som har inledande eller avslutande blank steg. #760
* Knappen "logga in" var alltid aktive rad på samma sidor i dialog rutan Anslut. Den är nu inaktive rad när det är lämpligt. #761
* Snabb åtkomst kommer inte längre att generera ett fel i konsolen när inga snabb åtkomst objekt har lagts till.

### <a name="known-issues"></a>Kända problem

* Om du kopplar från en resurs som är ansluten via SAS URI, till exempel en BLOB-behållare, kan det orsaka ett fel som förhindrar att andra bilagor visas korrekt. Undvik det här problemet genom att bara uppdatera gruppnoden. Mer information finns i #537.
* Om du använder VS för Mac och har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Undvik problemet genom att ta bort innehållet från ~/. IdentityService/AadConfigurations. Om du gör det kan du kommentera det här problemet.
* Azurite har ännu inte implementerat alla API: er för lagring. Därför kan det uppstå oväntade fel eller beteenden när du använder Azurite för utvecklings lagring.
* I sällsynta fall kan träd fokus bli fastnat i snabb åtkomst. Om du vill avaktivera fokus kan du uppdatera alla.
* Det går inte att ladda upp från din OneDrive-mapp på grund av ett fel i NodeJS. Felet har åtgärd ATS men har ännu inte integrerats i Electron. För att undvika det här problemet när du laddar upp eller laddar ned från en BLOB-behållare kan du använda funktionen experimentell AzCopy.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs här.
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner. Försök att använda dessa funktioner när du arbetar med Azure Stack-resurser kan resultera i oväntade fel.
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14,04 måste du se till att GCC är uppdaterat. Detta kan du göra genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17,04 behöver du installera GConf – du kan göra detta genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>Version 1.4.4
10/15/2018

### <a name="hotfixes"></a>Snabbkorrigeringar
* Azure Resource Management API-versionen har återställts för att avblockera Azure-användare från amerikanska myndigheter. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Genom att läsa in rotations rutan använder du nu CSS-animeringar för att minska mängden GPU som används av Storage Explorer. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Ny
* Externa resurs bilagor, till exempel för SAS-anslutningar och emulatorer, har förbättrats avsevärt. Nu kan du:
   * Anpassa visnings namnet för den resurs som du ansluter till. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Anslut till flera lokala emulatorer med olika portar. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Lägg till anslutna resurser i snabb åtkomst. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer stöder nu mjuk borttagning. Du kan:
   * Konfigurera en princip för mjuk borttagning genom att högerklicka på noden BLOB-behållare för ditt lagrings konto.
   * Visa mjuka borttagna blobbar i BLOB-redigeraren genom att markera "aktiva och borttagna blobbar" i list rutan bredvid navigerings fältet.
   * Ta bort mjuka borttagna blobbar.

### <a name="fixes"></a>Korrigeringar
* Åtgärden konfigurera CORS-inställningar är inte längre tillgänglig på Premium Storage konton eftersom Premium Storage-konton inte stöder CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Det finns nu en signatur för delad åtkomst för SAS-anslutna tjänster. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Åtgärden "Ange standard åtkomst nivå" är nu tillgänglig för blob-och GPV2 lagrings konton som har fästs på snabb åtkomst. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Ibland skulle Storage Explorer inte kunna visa klassiska lagrings konton. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Kända problem
* När du använder emulatorer, till exempel Azure Storage emulator eller Azurite, måste du lyssna efter anslutningar på sina standard portar. Annars kommer Storage Explorer inte att kunna ansluta till dem.
* Om du använder VS för Mac och har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Undvik problemet genom att ta bort innehållet från ~/. IdentityService/AadConfigurations. Om du gör det kan du kommentera [det här problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite har ännu inte implementerat alla API: er för lagring. Därför kan det uppstå oväntade fel eller beteenden när du använder Azurite för utvecklings lagring.
* I sällsynta fall kan träd fokus bli fastnat i snabb åtkomst. Om du vill avaktivera fokus kan du uppdatera alla.
* Det går inte att ladda upp från din OneDrive-mapp på grund av ett fel i NodeJS. Felet har åtgärd ATS men har ännu inte integrerats i Electron.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack inte har stöd för fil resurser, visas en noden fil resurser fortfarande under ett kopplat Azure Stack lagrings konto.
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14,04 måste du se till att GCC är uppdaterat. Detta kan du göra genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17,04 behöver du installera GConf – du kan göra detta genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>Version 1.4.3
10/11/2018

### <a name="hotfixes"></a>Snabbkorrigeringar
* Azure Resource Management API-versionen har återställts för att avblockera Azure-användare från amerikanska myndigheter. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Genom att läsa in rotations rutan använder du nu CSS-animeringar för att minska mängden GPU som används av Storage Explorer. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Ny
* Externa resurs bilagor, till exempel för SAS-anslutningar och emulatorer, har förbättrats avsevärt. Nu kan du:
   * Anpassa visnings namnet för den resurs som du ansluter till. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Anslut till flera lokala emulatorer med olika portar. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Lägg till anslutna resurser i snabb åtkomst. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer stöder nu mjuk borttagning. Du kan:
   * Konfigurera en princip för mjuk borttagning genom att högerklicka på noden BLOB-behållare för ditt lagrings konto.
   * Visa mjuka borttagna blobbar i BLOB-redigeraren genom att markera "aktiva och borttagna blobbar" i list rutan bredvid navigerings fältet.
   * Ta bort mjuka borttagna blobbar.

### <a name="fixes"></a>Korrigeringar
* Åtgärden konfigurera CORS-inställningar är inte längre tillgänglig på Premium Storage konton eftersom Premium Storage-konton inte stöder CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Det finns nu en signatur för delad åtkomst för SAS-anslutna tjänster. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Åtgärden "Ange standard åtkomst nivå" är nu tillgänglig för blob-och GPV2 lagrings konton som har fästs på snabb åtkomst. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Ibland skulle Storage Explorer inte kunna visa klassiska lagrings konton. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Kända problem
* När du använder emulatorer, till exempel Azure Storage emulator eller Azurite, måste du lyssna efter anslutningar på sina standard portar. Annars kommer Storage Explorer inte att kunna ansluta till dem.
* Om du använder VS för Mac och har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Undvik problemet genom att ta bort innehållet från ~/. IdentityService/AadConfigurations. Om du gör det kan du kommentera [det här problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite har ännu inte implementerat alla API: er för lagring. Därför kan det uppstå oväntade fel eller beteenden när du använder Azurite för utvecklings lagring.
* I sällsynta fall kan träd fokus bli fastnat i snabb åtkomst. Om du vill avaktivera fokus kan du uppdatera alla.
* Det går inte att ladda upp från din OneDrive-mapp på grund av ett fel i NodeJS. Felet har åtgärd ATS men har ännu inte integrerats i Electron.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack inte har stöd för fil resurser, visas en noden fil resurser fortfarande under ett kopplat Azure Stack lagrings konto.
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14,04 måste du se till att GCC är uppdaterat. Detta kan du göra genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17,04 behöver du installera GConf – du kan göra detta genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>Version 1.4.2
09/24/2018

### <a name="hotfixes"></a>Snabbkorrigeringar
* Uppdatera Azure Resource Management API-versionen till 2018-07-01 för att lägga till stöd för nya Azure Storage konto typer. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Ny
* Externa resurs bilagor, till exempel för SAS-anslutningar och emulatorer, har förbättrats avsevärt. Nu kan du:
   * Anpassa visnings namnet för den resurs som du ansluter till. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Anslut till flera lokala emulatorer med olika portar. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Lägg till anslutna resurser i snabb åtkomst. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer stöder nu mjuk borttagning. Du kan:
   * Konfigurera en princip för mjuk borttagning genom att högerklicka på noden BLOB-behållare för ditt lagrings konto.
   * Visa mjuka borttagna blobbar i BLOB-redigeraren genom att markera "aktiva och borttagna blobbar" i list rutan bredvid navigerings fältet.
   * Ta bort mjuka borttagna blobbar.

### <a name="fixes"></a>Korrigeringar
* Åtgärden konfigurera CORS-inställningar är inte längre tillgänglig på Premium Storage konton eftersom Premium Storage-konton inte stöder CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Det finns nu en signatur för delad åtkomst för SAS-anslutna tjänster. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Åtgärden "Ange standard åtkomst nivå" är nu tillgänglig för blob-och GPV2 lagrings konton som har fästs på snabb åtkomst. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Ibland skulle Storage Explorer inte kunna visa klassiska lagrings konton. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Kända problem
* När du använder emulatorer, till exempel Azure Storage emulator eller Azurite, måste du lyssna efter anslutningar på sina standard portar. Annars kommer Storage Explorer inte att kunna ansluta till dem.
* Om du använder VS för Mac och har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Undvik problemet genom att ta bort innehållet från ~/. IdentityService/AadConfigurations. Om du gör det kan du kommentera [det här problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite har ännu inte implementerat alla API: er för lagring. Därför kan det uppstå oväntade fel eller beteenden när du använder Azurite för utvecklings lagring.
* I sällsynta fall kan träd fokus bli fastnat i snabb åtkomst. Om du vill avaktivera fokus kan du uppdatera alla.
* Det går inte att ladda upp från din OneDrive-mapp på grund av ett fel i NodeJS. Felet har åtgärd ATS men har ännu inte integrerats i Electron.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack inte har stöd för fil resurser, visas en noden fil resurser fortfarande under ett kopplat Azure Stack lagrings konto.
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14,04 måste du se till att GCC är uppdaterat. Detta kan du göra genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17,04 behöver du installera GConf – du kan göra detta genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>Version 1.4.1
08/28/2018

### <a name="hotfixes"></a>Snabbkorrigeringar
* Vid första starten kunde Storage Explorer inte generera nyckeln som används för att kryptera känsliga data. Detta skulle orsaka problem när du använder snabb åtkomst och bifogade resurser. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Om ditt konto inte Kräv MFA för dess hem klient, men gjorde för vissa andra klienter, skulle Storage Explorer inte kunna lista prenumerationer. När du nu har loggat in med ett sådant konto uppmanas du Storage Explorer att ange dina autentiseringsuppgifter igen och utföra MFA. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* Storage Explorer kunde inte ansluta resurser från Azure Tyskland och Azure amerikanska myndigheter. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Om du har loggat in på två konton som hade samma e-postadress kan Storage Explorer ibland inte Visa dina resurser i trädvyn. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* På långsamma Windows-datorer skulle det ibland ta lång tid att Visa välkomst skärmen. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* Dialog rutan Anslut visas även om det fanns anslutna konton eller tjänster. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Ny
* Externa resurs bilagor, till exempel för SAS-anslutningar och emulatorer, har förbättrats avsevärt. Nu kan du:
   * Anpassa visnings namnet för den resurs som du ansluter till. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Anslut till flera lokala emulatorer med olika portar. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Lägg till anslutna resurser i snabb åtkomst. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer stöder nu mjuk borttagning. Du kan:
   * Konfigurera en princip för mjuk borttagning genom att högerklicka på noden BLOB-behållare för ditt lagrings konto.
   * Visa mjuka borttagna blobbar i BLOB-redigeraren genom att markera "aktiva och borttagna blobbar" i list rutan bredvid navigerings fältet.
   * Ta bort mjuka borttagna blobbar.

### <a name="fixes"></a>Korrigeringar
* Åtgärden konfigurera CORS-inställningar är inte längre tillgänglig på Premium Storage konton eftersom Premium Storage-konton inte stöder CORS. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Det finns nu en signatur för delad åtkomst för SAS-anslutna tjänster. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Åtgärden "Ange standard åtkomst nivå" är nu tillgänglig för blob-och GPV2 lagrings konton som har fästs på snabb åtkomst. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Ibland skulle Storage Explorer inte kunna visa klassiska lagrings konton. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Kända problem
* När du använder emulatorer, till exempel Azure Storage emulator eller Azurite, måste du lyssna efter anslutningar på sina standard portar. Annars kommer Storage Explorer inte att kunna ansluta till dem.
* Om du använder VS för Mac och har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Undvik problemet genom att ta bort innehållet från ~/. IdentityService/AadConfigurations. Om du gör det kan du kommentera [det här problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite har ännu inte implementerat alla API: er för lagring. Därför kan det uppstå oväntade fel eller beteenden när du använder Azurite för utvecklings lagring.
* I sällsynta fall kan träd fokus bli fastnat i snabb åtkomst. Om du vill avaktivera fokus kan du uppdatera alla.
* Det går inte att ladda upp från din OneDrive-mapp på grund av ett fel i NodeJS. Felet har åtgärd ATS men har ännu inte integrerats i Electron.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack inte har stöd för fil resurser, visas en noden fil resurser fortfarande under ett kopplat Azure Stack lagrings konto.
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14,04 måste du se till att GCC är uppdaterat. Detta kan du göra genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17,04 behöver du installera GConf – du kan göra detta genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>Version 1.3.0
07/09/2018

### <a name="new"></a>Ny
* Att komma åt $web behållare som används av statiska webbplatser stöds nu. På så sätt kan du enkelt ladda upp och hantera filer och mappar som används av din webbplats. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* App-fältet på macOS har omorganiserats. Ändringarna inkluderar en filmeny, vissa kortkommandon och flera nya kommandon under menyn app. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* Auktoritets slut punkten för att logga in på Azure amerikanska myndigheter har ändrats till https://login.microsoftonline.us/
* Hjälpmedel: när en skärm läsare är aktiv fungerar tangent bords navigering nu med de tabeller som används för att visa objekt på den högra sidan. Du kan använda piltangenterna för att navigera bland rader och kolumner, ange för att anropa standard åtgärder, snabb meny tangenten för att öppna snabb menyn för ett objekt och flytta eller styra till MultiSelect. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Korrigeringar
*  På vissa datorer tog underordnade processer lång tid att starta. När detta skulle inträffa skulle en "underordnad process som inte kunde starta i rätt tid" visas. Den tid som tilldelats en underordnad process för start har nu ökats från 20 till 90 sekunder. Om du fortfarande påverkas av det här problemet kan du kommentera det länkade GitHub-problemet. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* När du använder en SAS som inte har Läs behörighet var det inte möjligt att ladda upp en stor blob. Logiken för uppladdning har ändrats för att fungera i det här scenariot. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* Om du anger en offentlig åtkomst nivå för en behållare tas alla åtkomst principer bort och vice versa. Nu bevaras offentlig åtkomst nivå och åtkomst principer när du anger någon av de två. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* "AccessTierChangeTime" trunkerades i dialog rutan Egenskaper. Problemet har åtgärdats. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* Prefixet "Microsoft Azure Storage Explorer-" saknades i dialog rutan skapa ny katalog. Problemet har åtgärdats. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Hjälpmedel: dialog rutan Lägg till entitet var svår att navigera vid användning av VoiceOver. Förbättringar har gjorts. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Hjälpmedel: bakgrunds färgen för knappen Dölj/expandera i fönstret åtgärder och egenskaper inkonsekvent med liknande GRÄNSSNITTs kontroller i Högkontrast svart tema. Färgen har ändrats. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Hjälpmedel: i Högkontrast svart tema syns inte formatet för fokus för X-knappen i dialog rutan Egenskaper. Problemet har åtgärdats. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Hjälpmedel: flikarna åtgärder och egenskaper saknade flera Aria-värden som resulterade i en subpar skärm läsare. De Aria-värden som saknas har nu lagts till. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Hjälpmedel: minimerade trädnoden till vänster har inte fått ett Aria-expanderat värde falskt. Problemet har åtgärdats. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Kända problem
* Om du kopplar från en resurs som är ansluten via SAS URI, till exempel en BLOB-behållare, kan det orsaka ett fel som förhindrar att andra bilagor visas korrekt. Undvik det här problemet genom att bara uppdatera gruppnoden. Se [det här problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/537) för mer information.
* Om du använder VS för Mac och har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Undvik problemet genom att ta bort innehållet från ~/. IdentityService/AadConfigurations. Om du gör det kan du kommentera [det här problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite har ännu inte implementerat alla API: er för lagring. Därför kan det uppstå oväntade fel eller beteenden när du använder Azurite för utvecklings lagring.
* I sällsynta fall kan träd fokus bli fastnat i snabb åtkomst. Om du vill avaktivera fokus kan du uppdatera alla.
* Det går inte att ladda upp från din OneDrive-mapp på grund av ett fel i NodeJS. Felet har åtgärd ATS men har ännu inte integrerats i Electron.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Azure Stack stöder inte följande funktioner, och om du försöker använda dem när du arbetar med Azure Stack kan det leda till oväntade fel:
   * Filresurser
   * Åtkomstnivåer
   * Mjuk borttagning
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14,04 måste du se till att GCC är uppdaterat. Detta kan du göra genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17,04 behöver du installera GConf – du kan göra detta genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>Version 1.2.0
06/12/2018

### <a name="new"></a>Ny
* Om Storage Explorer inte kan läsa in prenumerationer enbart från en delmängd av klienterna visas alla inlästa prenumerationer tillsammans med ett fel meddelande som är specifikt för de klienter som misslyckades. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* När en uppdatering är tillgänglig i Windows kan du nu välja att uppdatera vid stängning. När det här alternativet har valts kommer installations programmet för uppdateringen att köras när du har stängt Storage Explorer. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* Restore Snapshot har lagts till på snabb menyn i fil resurs redigeraren när du visar en ögonblicks bild av en fil resurs. [#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* Knappen Rensa kö är nu alltid aktive rad. [#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* Stöd för att logga in på ADFS Azure Stack har Aktiver ATS igen. Azure Stack version 1804 eller senare krävs. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Korrigeringar
* Om du har visat ögonblicks bilder för en fil resurs vars namn är ett prefix till en annan fil resurs i samma lagrings konto, visas ögonblicks bilderna för den andra fil resursen också. Det här problemet har åtgärd ATS. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* När du är ansluten via SAS skulle återställning av en fil från en ögonblicks bild av en fil resurs resultera i ett fel. Det här problemet har åtgärd ATS. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* När du visar ögonblicks bilder för en BLOB aktiverades åtgärden befordra ögonblicks bild när bas-bloben och en enskild ögonblicks bild valdes. Åtgärden är nu bara aktive rad om en enda ögonblicks bild har valts. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Om ett enskilt jobb (till exempel laddar ned en BLOB) startades och senare Miss lyckas, kommer det inte automatiskt att försöka igen förrän du startade ett annat jobb av samma typ. Nu ska alla jobb automatiskt försöka igen, oavsett hur många jobb du har placerat i kö.
* Redigerare öppnade för nyskapade BLOB-behållare i GPV2 och Blob Storage konton har inte någon åtkomst nivå kolumn. Det här problemet har åtgärd ATS. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* En kolumn i åtkomst nivån visas ibland inte när ett lagrings konto eller en BLOB-behållare anslöts via SAS. Kolumnen visas nu alltid, men med ett tomt värde om det inte finns någon åtkomst nivå uppsättning. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* Att ange åtkomst nivå för en nyligen överförd Block-Blob har inaktiverats. Det här problemet har åtgärd ATS. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Om knappen "Behåll fliken" är öppen "anropades med tangent bordet skulle tangent bords fokus gå förlorat. Nu kommer fokus att flyttas till fliken som har hållits öppen. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* För en fråga i Frågebyggaren gav inte VoiceOver en användbar Beskrivning av den aktuella operatorn. Nu är det mer beskrivande. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* Sid brytnings länkarna för de olika redigerarena var inte beskrivande. De har ändrats för att vara mer beskrivande. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* I dialog rutan Lägg till entitet presenterade inte VoiceOver vilken kolumn ett inmatat element var en del av. Namnet på den aktuella kolumnen ingår nu i beskrivningen av elementet. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Alternativ knappar och kryss rutor har ingen synlig kant linje när de fokuserades. Det här problemet har åtgärd ATS. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Kända problem
* När du använder emulatorer, till exempel Azure Storage emulator eller Azurite, måste du lyssna efter anslutningar på sina standard portar. Annars kommer Storage Explorer inte att kunna ansluta till dem.
* Om du använder VS för Mac och har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Undvik problemet genom att ta bort innehållet från ~/. IdentityService/AadConfigurations. Om du gör det kan du kommentera [det här problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite har ännu inte implementerat alla API: er för lagring. Därför kan det uppstå oväntade fel eller beteenden när du använder Azurite för utvecklings lagring.
* I sällsynta fall kan träd fokus bli fastnat i snabb åtkomst. Om du vill avaktivera fokus kan du uppdatera alla.
* Det går inte att ladda upp från din OneDrive-mapp på grund av ett fel i NodeJS. Felet har åtgärd ATS men har ännu inte integrerats i Electron.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack inte har stöd för fil resurser, visas en noden fil resurser fortfarande under ett kopplat Azure Stack lagrings konto.
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14,04 måste du se till att GCC är uppdaterat. Detta kan du göra genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17,04 behöver du installera GConf – du kan göra detta genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>Version 1.1.0
05/09/2018

### <a name="new"></a>Ny
* Storage Explorer stöder nu användning av Azurite. Obs: anslutningen till Azurite är hårdkodad till standard utvecklings slut punkterna.
* Storage Explorer stöder nu åtkomst nivåer enbart för blob och GPV2 lagrings konton. Läs mer om åtkomst nivåer [här](./storage/blobs/storage-blob-storage-tiers.md).
* En start tid krävs inte längre när du genererar en SAS.

### <a name="fixes"></a>Korrigeringar
* Hämtning av prenumerationer för amerikanska myndighets konton har brutits. Det här problemet har åtgärd ATS. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* Förfallo tiden för åtkomst principer sparades inte korrekt. Det här problemet har åtgärd ATS. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* När du genererar en SAS-URL för ett objekt i en behållare, lades inte namnet på objektet till i URL: en. Det här problemet har åtgärd ATS. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* När du skapar en SAS är förfallo tiderna i det förflutna ibland standardvärdet. Detta beror på Storage Explorer att använda den senast använda start-och förfallo tiden som standardvärden. Nu skapas en ny uppsättning standardvärden varje gång du öppnar SAS-dialogrutan. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* När du kopierar mellan lagrings konton genereras en säkerhets kopia på 24 timmar. Om kopieringen senast 24 timmarna skulle kopieringen misslyckats. Vi har ökat SAS: s till den senaste 1 veckan för att minska risken för en kopiering som inte kan utföras på grund av en utgånget SAS. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* För vissa aktiviteter som du klickar på Avbryt fungerar det inte alltid. Det här problemet har åtgärd ATS. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* För en del aktiviteter var överföringshastigheten fel. Det här problemet har åtgärd ATS. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* Stavningen av "föregående" på menyn Visa var fel. Den har nu stavats korrekt. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* Den sista sidan i Windows Installer hade en "Nästa"-knapp. Den har ändrats till en "Slutför"-knapp. [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* Fokusera på fliken är inte synligt för knappar i dialog rutor när du använder det svarta temat för HC. Den är nu synlig. [#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* Skift läget för "Lös automatiskt" för åtgärder i aktivitets loggen var fel. Nu är det rätt. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* När du tar bort en entitet från en tabell där du uppmanas att bekräfta visas en felikon i dialog rutan. Dialog rutan använder nu en varnings ikon. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Kända problem
* Om du använder VS för Mac och har skapat en anpassad AAD-konfiguration kanske du inte kan logga in. Undvik problemet genom att ta bort innehållet från ~/. IdentityService/AadConfigurations. Om du gör det kan du kommentera [det här problemet](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite har ännu inte implementerat alla API: er för lagring. Därför kan det uppstå oväntade fel eller beteenden när du använder Azurite för utvecklings lagring.
* I sällsynta fall kan träd fokus bli fastnat i snabb åtkomst. Om du vill avaktivera fokus kan du uppdatera alla.
* Det går inte att ladda upp från din OneDrive-mapp på grund av ett fel i NodeJS. Felet har åtgärd ATS men har ännu inte integrerats i Electron.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack inte har stöd för fil resurser, visas en noden fil resurser fortfarande under ett kopplat Azure Stack lagrings konto.
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14,04 måste du se till att GCC är uppdaterat. Detta kan du göra genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17,04 behöver du installera GConf – du kan göra detta genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>Version 1.0.0
04/16/2018

### <a name="new"></a>Ny
* Utökad autentisering som låter Storage Explorer använda samma konto arkiv som Visual Studio 2017. Om du vill använda den här funktionen måste du logga in igen på dina konton och ange dina filtrerade prenumerationer igen.
* För Azure Stack konton som skyddas av AAD kommer Storage Explorer nu att hämta Azure Stack prenumerationer när mål Azure Stack är aktive rad. Du behöver inte längre skapa en anpassad inloggnings miljö.
* Flera genvägar har lagts till för att möjliggöra snabbare navigering. Detta omfattar att växla mellan olika paneler och att flytta mellan redigerare. Se menyn Visa för mer information.
* Storage Explorer feedback finns nu på GitHub. Du kan komma till vår ärende sida genom att klicka på knappen feedback längst ned till vänster eller genom att gå till [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues) . Du kan göra förslag, rapportera problem, ställa frågor eller lämna andra former av feedback.
* Om du kör problem med TLS/SSL-certifikat och inte kan hitta det felande certifikatet kan du nu starta Storage Explorer från kommando raden med `--ignore-certificate-errors` flaggan. När den startas med den här flaggan kommer Storage Explorer att ignorera TLS/SSL-certifikat fel.
* Det finns nu ett nedladdnings alternativ i snabb menyn för blob-och fil objekt.
* Förbättrat stöd för hjälpmedel och skärm läsare. Om du använder hjälpmedels funktioner, se vår [hjälpmedels dokumentation](./vs-azure-tools-storage-explorer-accessibility.md) för mer information.
* Storage Explorer använder nu Electron 1.8.3

### <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar
* Storage Explorer har bytt till ett nytt bibliotek för autentisering. Som en del av växlingen till biblioteket måste du logga in igen på dina konton och ange dina filtrerade prenumerationer igen
* Den metod som används för att kryptera känsliga data har ändrats. Detta kan leda till att vissa av dina snabb åtkomst objekt måste läggas till på nytt och/eller att vissa av dina anslutna resurser måste återanslutas.

### <a name="fixes"></a>Korrigeringar
* Vissa användare bakom proxyservrar skulle ha grupp-BLOB-uppladdningar eller hämtningar avbrutna av fel meddelandet "Det gick inte att lösa". Det här problemet har åtgärd ATS.
* Om inloggningen behövdes när du använde en direkt länk skulle du Visa en tom dialog ruta genom att klicka på "logga in". Det här problemet har åtgärd ATS.
* Om Storage Explorer inte kan starta på grund av en krasch i en GPU-process på Linux, kommer du nu att bli informerad om kraschen och uppmanas att använda växeln "--Disable-GPU", och Storage Explorer startas sedan om automatiskt med växeln aktive rad.
* Ogiltiga åtkomst principer var svåra att identifiera i dialog rutan åtkomst principer. Ogiltiga åtkomst princip-ID: n beskrivs nu i rött för mer information.
* Aktivitets loggen skulle ibland ha stora områden med tomt utrymme mellan olika delar av en aktivitet. Det här problemet har åtgärd ATS.
* Om du i tabellens Frågeredigeraren har lämnat en timestamp-sats i ett ogiltigt tillstånd och sedan försökte ändra en annan sats, skulle redigeraren låsa. Redigerings programmet kommer nu att återställa timestamp-satsen till sitt senaste giltiga tillstånd när en ändring i en annan-sats upptäcks.
* Om du pausade när du skriver i din Sök fråga i trädvyn börjar sökningen bli stulen från text rutan. Nu måste du starta sökningen explicit genom att trycka på RETUR-tangenten eller genom att klicka på knappen Starta sökning.
* Kommandot Hämta signatur för delad åtkomst kommer ibland att inaktive ras när du högerklickar på en fil i en fil resurs. Det här problemet har åtgärd ATS.
* Om noden för resurs trädet med fokus filtrerades under sökningen, kunde du inte Tabba till resurs trädet och använda piltangenterna för att navigera i resurs trädet. Om noden fokuserad resurs träd är dold, kommer den första noden i resurs trädet att prioriteras automatiskt.
* En extra avgränsare skulle ibland synas i redigerings verktygsfältet. Det här problemet har åtgärd ATS.
* Text rutan för dynamiska länkar skulle ibland orsaka spill. Det här problemet har åtgärd ATS.
* Blob-och fil resurs redigerare skulle ibland uppdatera när många filer laddas upp samtidigt. Det här problemet har åtgärd ATS.
* Funktionen "folder Statistics" hade inget syfte i vyn hantering av fil resurs ögonblicks bilder. Den har nu inaktiverats.
* På Linux visas inte menyn Arkiv. Det här problemet har åtgärd ATS.
* När du överför en mapp till en fil resurs, som standard, laddas bara innehållet i mappen upp. Nu är standard beteendet att överföra innehållet i mappen till en matchande mapp i fil resursen.
* Ordningen på knappar i flera dialog rutor har återställts. Det här problemet har åtgärd ATS.
* Olika säkerhetsrelaterade säkerhets korrigeringar.

### <a name="known-issues"></a>Kända problem
* I sällsynta fall kan träd fokus bli fastnat i snabb åtkomst. Om du vill avaktivera fokus kan du uppdatera alla.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs här.
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack inte har stöd för fil resurser, visas en noden fil resurser fortfarande under ett kopplat Azure Stack lagrings konto.
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För Linux-användare måste du installera [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0).
* För användare på Ubuntu 14,04 måste du se till att GCC är uppdaterat. Detta kan du göra genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17,04 behöver du installera GConf – du kan göra detta genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>Version 0.9.6
02/28/2018

### <a name="fixes"></a>Korrigeringar
* Ett problem förhindrade att blobbar/filer visas i redigeraren. Det här problemet har åtgärd ATS.
* Ett problem orsakade växlingen mellan Snapshot-vyer för att visa objekt på fel sätt. Det här problemet har åtgärd ATS.

### <a name="known-issues"></a>Kända problem
* Storage Explorer stöder inte ADFS-konton.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs [här](https://github.com/Azure/azure-storage-node/issues/317).
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Panelen konto inställningar kan visa att du måste ange autentiseringsuppgifterna på samma sätt för att filtrera prenumerationer.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack inte har stöd för fil resurser, visas en noden fil resurser fortfarande under ett kopplat Azure Stack lagrings konto.
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För användare på Ubuntu 14,04 måste du se till att GCC är uppdaterat. Detta kan du göra genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17,04 behöver du installera GConf – du kan göra detta genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>Version 0.9.5
02/06/2018

### <a name="new"></a>Ny

* Stöd för ögonblicks bilder av fil resurser:
    * Skapa och hantera ögonblicks bilder för dina fil resurser.
    * Växla enkelt vyer mellan ögonblicks bilder av dina fil resurser när du utforskar.
    * Återställ tidigare versioner av dina filer.
* För hands versions stöd för Azure Data Lake Store:
    * Anslut till dina ADLS-resurser över flera konton.
    * Ansluta till och dela ADLS-resurser med hjälp av ADL-URI: er.
    * Utföra grundläggande åtgärder för filer och mappar rekursivt.
    * Fäst enskilda mappar i snabb åtkomst.
    * Visa statistik för mappar.

### <a name="fixes"></a>Korrigeringar
* Förbättringar av start prestanda.
* Olika felkorrigeringar.

### <a name="known-issues"></a>Kända problem
* Storage Explorer stöder inte ADFS-konton.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs här.
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Panelen konto inställningar kan visa att du måste ange autentiseringsuppgifterna på samma sätt för att filtrera prenumerationer.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack inte har stöd för fil resurser, visas en noden fil resurser fortfarande under ett kopplat Azure Stack lagrings konto.
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* För användare på Ubuntu 14,04 måste du se till att GCC är uppdaterat. Detta kan du göra genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17,04 behöver du installera GConf – du kan göra detta genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Version 0.9.4 och 0.9.3
01/21/2018

### <a name="new"></a>Ny
* Ditt befintliga Storage Explorer-fönster kommer att återanvändas när:
    * Öppnar direkt länkar som genererats i Storage Explorer.
    * Öppnar Storage Explorer från portalen.
    * Öppnar Storage Explorer från Azure Storage VS Code-tillägg (kommer snart).
* Möjlighet att öppna ett nytt Storage Explorers fönster från Storage Explorer har lagts till.
    * För Windows finns ett "nytt fönster"-alternativ under menyn Arkiv och i aktivitets fältets snabb meny.
    * För Mac finns ett "nytt fönster"-alternativ under app-menyn.

### <a name="fixes"></a>Korrigeringar
* Åtgärdat ett säkerhets problem. Uppgradera till 0.9.4 i din första bekvämlighet.
* Gamla aktiviteter rensades inte korrekt. Detta påverkar prestanda för långvariga jobb. De rensas nu på rätt sätt.
* Åtgärder som rör ett stort antal filer och kataloger skulle ibland orsaka Storage Explorer att frysa. Begär anden till Azure för fil resurser begränsas nu för att begränsa användningen av system resurser.

### <a name="known-issues"></a>Kända problem
* Storage Explorer stöder inte ADFS-konton.
* Kortkommandon för "Visa Utforskaren" och "Visa konto hantering" måste vara Ctrl/Cmd + Shift + E och Ctrl/Cmd + SHIFT + A.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs här.
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Panelen konto inställningar kan visa att du måste ange autentiseringsuppgifterna på samma sätt för att filtrera prenumerationer.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack inte har stöd för fil resurser, visas en noden fil resurser fortfarande under ett kopplat Azure Stack lagrings konto.
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer --disable-gpu
    ```

* För användare på Ubuntu 14,04 måste du se till att GCC är uppdaterat. Detta kan du göra genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17,04 behöver du installera GConf – du kan göra detta genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>Version 0.9.2
11/01/2017

### <a name="hotfixes"></a>Snabbkorrigeringar
* Oväntade data ändringar var möjliga vid redigering av EDM. DateTime-värden för Table-entiteter beroende på den lokala tids zonen. Redigeraren använder nu en vanlig text ruta som ger exakt, konsekvent kontroll över EDM. DateTime-värden.
* Det går inte att starta eller ladda ned en grupp blobbar när de är kopplade till namnet och nyckeln. Det här problemet har åtgärd ATS.
* Tidigare Storage Explorer bara fråga dig om du vill autentisera om ett inaktuellt konto om ett eller flera av kontots prenumerationer har valts. Nu visas Storage Explorer även om kontot är fullständigt filtrerat.
* Slut punkts domänen för Azure amerikanska myndigheter var fel. Den har åtgärd ATS.
* Knappen Använd på panelen Hantera konton är ibland svår att klicka. Detta bör inte längre ske.

### <a name="new"></a>Ny
* För hands versions stöd för Azure Cosmos DB:
    * [Onlinedokumentation](./cosmos-db/storage-explorer.md)
    * Skapa databaser och samlingar
    * Manipulera data
    * Fråga, skapa eller ta bort dokument
    * Uppdatera lagrade procedurer, användardefinierade funktioner eller utlösare
    * Använd anslutnings strängar för att ansluta till och hantera dina databaser
* Bättre prestanda vid överföring/nedladdning av många små blobbar.
* Lade till åtgärden "försök alla" om det finns fel i en grupp för BLOB-uppladdning eller BLOB-nedladdning.
* Storage Explorer pausar nu iteration under BLOB-uppladdning/nedladdning om den upptäcker att nätverks anslutningen har brutits. Du kan sedan återuppta iterationen när nätverks anslutningen har återupprättats.
* Har lagt till möjligheten att "stänga alla", "Stäng andra" och "Stäng" flikar via snabb menyn.
* Storage Explorer använder nu inbyggda dialog rutor och inbyggda snabb menyer.
* Storage Explorer är nu mer tillgängligt. Här är några av förbättringarna:
    * Förbättrat stöd för skärm läsare, för NVDA i Windows och för VoiceOver på Mac
    * Förbättrad hög kontrast
    * Tangent bords tabbar och tangent bords fokus korrigeras

### <a name="fixes"></a>Korrigeringar
* Om du försökte öppna eller hämta en blob med ett ogiltigt Windows-filnamn skulle åtgärden inte utföras. Storage Explorer kommer nu att identifiera om ett BLOB-namn är ogiltigt och fråga om du vill antingen koda det eller hoppa över blobben. Storage Explorer kommer också att identifiera om ett fil namn verkar vara kodat och fråga om du vill avkoda det innan det laddas upp.
* Under BLOB-uppladdning skulle redigeraren för mål-BLOB-behållaren ibland inte uppdateras korrekt. Det här problemet har åtgärd ATS.
* Stöd för flera typer av anslutnings strängar och SAS-URI försämrat. Vi har åtgärdat alla kända problem, men du kan skicka feedback om du stöter på ytterligare problem.
* Uppdaterings meddelandet har avbrutits för vissa användare i 0.9.0. Det här problemet har åtgärd ATS och för de som påverkas av felet kan du hämta den senaste versionen av Storage Explorer manuellt [här](https://azure.microsoft.com/features/storage-explorer/).

### <a name="known-issues"></a>Kända problem
* Storage Explorer stöder inte ADFS-konton.
* Kortkommandon för "Visa Utforskaren" och "Visa konto hantering" måste vara Ctrl/Cmd + Shift + E och Ctrl/Cmd + SHIFT + A.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs här.
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Panelen konto inställningar kan visa att du måste ange autentiseringsuppgifterna på samma sätt för att filtrera prenumerationer.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack inte har stöd för fil resurser, visas en noden fil resurser fortfarande under ett kopplat Azure Stack lagrings konto.
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer --disable-gpu
    ```

* För användare på Ubuntu 14,04 måste du se till att GCC är uppdaterat. Detta kan du göra genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17,04 behöver du installera GConf – du kan göra detta genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Version 0.9.1 till och och 0.9.0
10/20/2017
### <a name="new"></a>Ny
* För hands versions stöd för Azure Cosmos DB:
    * [Onlinedokumentation](./cosmos-db/storage-explorer.md)
    * Skapa databaser och samlingar
    * Manipulera data
    * Fråga, skapa eller ta bort dokument
    * Uppdatera lagrade procedurer, användardefinierade funktioner eller utlösare
    * Använd anslutnings strängar för att ansluta till och hantera dina databaser
* Bättre prestanda vid överföring/nedladdning av många små blobbar.
* Lade till åtgärden "försök alla" om det finns fel i en grupp för BLOB-uppladdning eller BLOB-nedladdning.
* Storage Explorer pausar nu iteration under BLOB-uppladdning/nedladdning om den upptäcker att nätverks anslutningen har brutits. Du kan sedan återuppta iterationen när nätverks anslutningen har återupprättats.
* Har lagt till möjligheten att "stänga alla", "Stäng andra" och "Stäng" flikar via snabb menyn.
* Storage Explorer använder nu inbyggda dialog rutor och inbyggda snabb menyer.
* Storage Explorer är nu mer tillgängligt. Här är några av förbättringarna:
    * Förbättrat stöd för skärm läsare, för NVDA i Windows och för VoiceOver på Mac
    * Förbättrad hög kontrast
    * Tangent bords tabbar och tangent bords fokus korrigeras

### <a name="fixes"></a>Korrigeringar
* Om du försökte öppna eller hämta en blob med ett ogiltigt Windows-filnamn skulle åtgärden inte utföras. Storage Explorer kommer nu att identifiera om ett BLOB-namn är ogiltigt och fråga om du vill antingen koda det eller hoppa över blobben. Storage Explorer kommer också att identifiera om ett fil namn verkar vara kodat och fråga om du vill avkoda det innan det laddas upp.
* Under BLOB-uppladdning skulle redigeraren för mål-BLOB-behållaren ibland inte uppdateras korrekt. Det här problemet har åtgärd ATS.
* Stöd för flera typer av anslutnings strängar och SAS-URI försämrat. Vi har åtgärdat alla kända problem, men du kan skicka feedback om du stöter på ytterligare problem.
* Uppdaterings meddelandet har avbrutits för vissa användare i 0.9.0. Det här problemet har åtgärd ATS och för de som påverkas av felet kan du hämta den senaste versionen av Storage Explorer manuellt [här](https://azure.microsoft.com/features/storage-explorer/)

### <a name="known-issues"></a>Kända problem
* Storage Explorer stöder inte ADFS-konton.
* Kortkommandon för "Visa Utforskaren" och "Visa konto hantering" måste vara Ctrl/Cmd + Shift + E och Ctrl/Cmd + SHIFT + A.
* När mål Azure Stack laddas upp vissa filer som bifogade blobbar kan Miss lyckas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Det beror på att vi använder det Cancel filter-arbete som beskrivs här.
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Panelen konto inställningar kan visa att du måste ange autentiseringsuppgifterna på samma sätt för att filtrera prenumerationer.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack inte har stöd för fil resurser, visas en noden fil resurser fortfarande under ett kopplat Azure Stack lagrings konto.
* Electron-gränssnittet som används av Storage Explorer har problem med en maskin varu acceleration för GPU (Graphics Processing Unit). Om Storage Explorer visar ett tomt (tomt) huvud fönster kan du försöka att starta Storage Explorer från kommando raden och inaktivera GPU-acceleration genom att lägga till `--disable-gpu` växeln:

    ```
    ./StorageExplorer --disable-gpu
    ```

* För användare på Ubuntu 14,04 måste du se till att GCC är uppdaterat. Detta kan du göra genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17,04 behöver du installera GConf – du kan göra detta genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Version 0.8.16
8/21/2017

### <a name="new"></a>Ny
* När du öppnar en BLOB uppmanas du Storage Explorer att ladda upp den nedladdade filen om en ändring upptäcks
* Förbättrad Azure Stack inloggnings upplevelse
* Bättre prestanda vid överföring/nedladdning av många små filer samtidigt


### <a name="fixes"></a>Korrigeringar
* För vissa BLOB-typer skulle det ibland leda till att överföringen startas om när du väljer "Ersätt" under en överförings konflikt.
* I version 0.8.15 skulle överföringar ibland gå vid 99%.
* När du laddar upp filer till en fil resurs, om du väljer att ladda upp till en katalog som inte ännu fanns, skulle överföringen inte utföras.
* Storage Explorer genererade tidsstämplar felaktigt för signaturer för delad åtkomst och tabell frågor.


### <a name="known-issues"></a>Kända problem
* Att använda ett namn och en nyckel anslutnings sträng fungerar inte för närvarande. Den kommer att korrigeras i nästa version. Tills du kan använda bifoga med namn och nyckel.
* Om du försöker öppna en fil med ett ogiltigt Windows-filnamn kommer nedladdningen att resultera i att filen inte hittas.
* När du klickar på Avbryt i en aktivitet kan det ta en stund innan aktiviteten avbryts. Detta är en begränsning i Azure Storage Node-biblioteket.
* När du har slutfört en BLOB-uppladdning uppdateras fliken som initierade uppladdningen. Detta är en förändring jämfört med föregående beteende och kommer även att leda till att du kommer tillbaka till roten för den behållare som du befinner dig i.
* Om du väljer fel PIN/smartkort måste du starta om för att Storage Explorer glömma det beslutet.
* Panelen konto inställningar kan visa att du måste ange autentiseringsuppgifterna på samma sätt för att filtrera prenumerationer.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack inte har stöd för fil resurser, visas en noden fil resurser fortfarande under ett kopplat Azure Stack lagrings konto.
* För användare på Ubuntu 14,04 måste du se till att GCC är uppdaterat. Detta kan du göra genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* För användare på Ubuntu 17,04 behöver du installera GConf – du kan göra detta genom att köra följande kommandon och sedan starta om datorn:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Version 0.8.14
06/22/2017

### <a name="new"></a>Ny

* Uppdaterade Electron-versionen till 1.7.2 för att kunna utnyttja flera viktiga säkerhets uppdateringar
* Nu kan du snabbt komma åt fel söknings guiden online från Hjälp menyn
* Storage Explorer fel söknings [Guide][2]
* [Anvisningar][3] om hur du ansluter till en Azure Stack-prenumeration

### <a name="known-issues"></a>Kända problem

* Knapparna i bekräftelse dialog rutan ta bort mapp registreras inte med mus klickningar i Linux. Undvik att använda retur nyckeln
* Om du väljer fel PIN-eller smartkorts-certifikat måste du starta om för att Storage Explorer glömma beslutet
* Om mer än tre grupper av blobbar eller filer överförs samtidigt kan det orsaka fel
* Panelen konto inställningar kan visa att du måste ange autentiseringsuppgifter för att kunna filtrera prenumerationer
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack inte har stöd för fil resurser, visas en noden fil resurser fortfarande under ett kopplat Azure Stack lagrings konto.
* Ubuntu 14,04-installationen behöver gcc version uppdaterad eller uppgraderad – steg för att uppgradera nedan:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Version 0.8.13
05/12/2017

#### <a name="new"></a>Ny

* Storage Explorer fel söknings [Guide][2]
* [Anvisningar][3] om hur du ansluter till en Azure Stack-prenumeration

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: fil uppladdning hade stor chans att orsaka minnes fel
* Åtgärdat: nu kan du logga in med PIN/smartkort
* Åtgärdat: öppna i portalen fungerar nu med Azure Kina 21Vianet, Azure Germany, Azure amerikanska myndigheter och Azure Stack
* Fast: vid överföring av en mapp till en BLOB-behållare skulle felet "otillåten åtgärd" uppstå ibland
* Åtgärdat: Markera alla inaktiverades när du hanterade ögonblicks bilder
* Åtgärdat: metadata för bas-bloben kan komma att skrivas över när du har granskat egenskaperna för dess ögonblicks bilder

#### <a name="known-issues"></a>Kända problem

* Om du väljer fel PIN-eller smartkorts-certifikat måste du starta om för att Storage Explorer glömma beslutet
* Vid zoomning in eller ut kan zoomnings nivån tillfälligt återställas till standard nivån
* Om mer än tre grupper av blobbar eller filer överförs samtidigt kan det orsaka fel
* Panelen konto inställningar kan visa att du måste ange autentiseringsuppgifter för att kunna filtrera prenumerationer
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack inte har stöd för fil resurser, visas en noden fil resurser fortfarande under ett kopplat Azure Stack lagrings konto.
* Ubuntu 14,04-installationen behöver gcc version uppdaterad eller uppgraderad – steg för att uppgradera nedan:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Version 0.8.12 och 0.8.11 och 0.8.10
04/07/2017

#### <a name="new"></a>Ny

* Storage Explorer stängs nu automatiskt när du installerar en uppdatering från uppdaterings meddelandet
* Snabb åtkomst på plats ger en bättre upplevelse för att arbeta med resurser som används ofta
* I BLOB container Editor kan du nu se vilken virtuell dator som en lånad BLOB tillhör
* Nu kan du komprimera den vänstra sid panelen
* Identifieringen körs nu på samma gång som hämtningen
* Använd statistik i BLOB-behållaren, fil resursen och tabell redigeraren för att se storleken på din resurs eller ditt val
* Du kan nu logga in till Azure Active Directory (AAD) baserade Azure Stack-konton.
* Du kan nu ladda upp arkivfiler över 32 MB till Premium Storage-konton
* Förbättrat stöd för hjälpmedel
* Nu kan du lägga till betrodda Base-64-kodade X. 509 TLS/SSL-certifikat genom att gå till redigera- &gt; SSL-certifikat – &gt; Importera certifikat

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: när ett kontos autentiseringsuppgifter har uppdaterats kommer trädvyn ibland inte att uppdateras automatiskt
* Fast: generering av en SAS för emulator-köer och tabeller skulle resultera i en ogiltig URL
* Åtgärdat: Premium Storage-konton kan nu expanderas när en proxy är aktive rad
* Åtgärdat: knappen Använd på sidan konto hantering fungerar inte om du har valt 1 eller 0 konton
* Åtgärdat: det går inte att ladda upp blobar som kräver konflikt lösningar, fast i 0.8.11
* Åtgärdat: sändning av feedback har avbrutits i 0.8.11-Fixed i 0.8.12

#### <a name="known-issues"></a>Kända problem

* När du har uppgraderat till 0.8.10 måste du uppdatera alla dina autentiseringsuppgifter.
* Vid zoomning in eller ut kan zoomnings nivån tillfälligt återställas till standard nivån.
* Om mer än tre grupper av blobbar eller filer överförs samtidigt kan det orsaka fel.
* Panelen konto inställningar kan visa att du måste ange autentiseringsuppgifter igen för att kunna filtrera prenumerationer.
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte.
* Även om Azure Stack inte har stöd för fil resurser, visas en noden fil resurser fortfarande under ett kopplat Azure Stack lagrings konto.
* Ubuntu 14,04-installationen behöver gcc version uppdaterad eller uppgraderad – steg för att uppgradera nedan:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>Version 0.8.9 och 0.8.8
02/23/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>Ny

* Storage Explorer 0.8.9 kommer automatiskt att hämta den senaste versionen för uppdateringar.
* Snabb korrigering: om du använder en portal som skapats SAS-URI för att ansluta ett lagrings konto uppstår ett fel.
* Nu kan du skapa, hantera och uppgradera BLOB-ögonblicksbilder.
* Nu kan du logga in till Azure Kina 21Vianet, Azure Germany och Azure amerikanska myndigheters konton.
* Nu kan du ändra zoomnings nivån. Använd alternativen på menyn Visa för att zooma in, zooma ut och Återställ zoom.
* Unicode-tecken stöds nu i användar-metadata för blobbar och filer.
* Hjälpmedels förbättringar.
* Nästa versions versions information kan visas från uppdaterings meddelandet. Du kan också visa den aktuella versions informationen på Hjälp-menyn.

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: versions numret visas nu korrekt i kontroll panelen i Windows
* Åtgärdat: sökningen är inte längre begränsad till 50 000 noder
* Åtgärdat: Ladda upp till en fil resurs Spuninst för alltid om mål katalogen inte redan finns
* Åtgärdat: förbättrad stabilitet för långa uppladdningar och nedladdningar

#### <a name="known-issues"></a>Kända problem

* Vid zoomning in eller ut kan zoomnings nivån tillfälligt återställas till standard nivån.
* Snabb åtkomst fungerar bara med prenumerations objekt. Lokala resurser eller resurser som är anslutna via nyckel eller SAS-token stöds inte i den här versionen.
* Det kan ta snabb åtkomst några sekunder att navigera till mål resursen, beroende på hur många resurser du har.
* Om mer än tre grupper av blobbar eller filer överförs samtidigt kan det orsaka fel.

12/16/2016
### <a name="version-087"></a>Version 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Ny

* Du kan välja hur du vill lösa konflikter i början av en uppdatering, ladda ned eller kopiera sessionen i fönstret aktiviteter
* Hovra över en flik för att se den fullständiga sökvägen till lagrings resursen
* När du klickar på en flik synkroniseras den med dess plats i navigerings fönstret på vänster sida

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: Storage Explorer är nu en betrodd app på Mac
* Åtgärdat: Ubuntu 14,04 stöds igen
* Fast: ibland blinkar Lägg till konto användar gränssnitt vid inläsning av prenumerationer
* Fast: ibland visas inte alla lagrings resurser i navigerings fönstret på vänster sida
* Åtgärdat: åtgärds fönstret visade ibland tomma åtgärder
* Åtgärdat: fönster storleken från den senast stängda sessionen behålls nu
* Åtgärdat: du kan öppna flera flikar för samma resurs med hjälp av snabb menyn

#### <a name="known-issues"></a>Kända problem

* Snabb åtkomst fungerar bara med prenumerations objekt. Lokala resurser eller resurser som är anslutna via nyckel eller SAS-token stöds inte i den här versionen
* Det kan ta snabb åtkomst några sekunder att navigera till mål resursen, beroende på hur många resurser du har
* Om mer än tre grupper av blobbar eller filer överförs samtidigt kan det orsaka fel
* Sök hanterar sökning på ungefär 50 000 noder – efter detta kan prestanda påverkas eller orsaka ohanterat undantag
* För första gången du använder Storage Explorer på macOS kan du se flera frågor som ber om användarens behörighet att komma åt nyckel ringen. Vi rekommenderar att du väljer Tillåt alltid så att meddelandet inte visas igen

11/18/2016
### <a name="version-086"></a>Version 0.8.6

#### <a name="new"></a>Ny

* Nu kan du fästa de vanligaste tjänsterna i snabb åtkomst för enkel navigering
* Nu kan du öppna flera redigerare på olika flikar. Enkel klickning för att öppna en tillfällig flik. Dubbelklicka för att öppna en permanent flik. Du kan också klicka på fliken tillfälligt för att göra den till en permanent flik
* Vi har gjort märkbara prestanda-och stabilitets förbättringar för uppladdning och nedladdning, särskilt för stora filer på snabba datorer
* Tomma "virtuella" mappar kan nu skapas i BLOB-behållare
* Vi har lagt till en omfångs sökning med vår nya förbättrade under Strängs sökning, så du har nu två alternativ för att söka:
    * Global sökning – ange en sökterm i text rutan Sök efter
    * Begränsad sökning – Klicka på förstorings glas ikonen bredvid en nod och Lägg sedan till en sökterm i slutet av sökvägen eller högerklicka och välj "Sök härifrån"
* Vi har lagt till olika teman: Light (standard), mörk, Högkontrast svart och Högkontrast vitt. Gå till redigera- &gt; teman för att ändra dina inställningar
* Du kan ändra blobb-och fil egenskaper
* Vi stöder nu kodade (base64) och uncoded Queue-meddelanden
* I Linux krävs ett 64-bitars operativ system nu. I den här versionen stöder vi bara 64-bitars Ubuntu 16.04.1 LTS
* Vi har uppdaterat vår logo typ!

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: problem med skärm låsning
* Åtgärdat: förbättrad säkerhet
* Fast: ibland kan dubblerade anslutna konton visas
* Åtgärdat: en blob med en odefinierad innehålls typ kan generera ett undantag
* Åtgärdat: det gick inte att öppna Frågity-panelen på en tom tabell
* Åtgärdat: varierar fel i sökning
* Åtgärdat: ökat antal resurser som lästs in från 50 till 100 vid klickning på Läs in mer
* Fast: vid första körningen, om ett konto är inloggat i, väljer vi nu alla prenumerationer för det kontot som standard

#### <a name="known-issues"></a>Kända problem

* Den här versionen av Storage Explorer körs inte på Ubuntu 14,04
* Om du vill öppna flera flikar för samma resurs, ska du inte kontinuerligt Klicka på samma resurs. Klicka på en annan resurs och gå tillbaka och klicka sedan på den ursprungliga resursen för att öppna den igen på en annan flik
* Snabb åtkomst fungerar bara med prenumerations objekt. Lokala resurser eller resurser som är anslutna via nyckel eller SAS-token stöds inte i den här versionen
* Det kan ta snabb åtkomst några sekunder att navigera till mål resursen, beroende på hur många resurser du har
* Om mer än tre grupper av blobbar eller filer överförs samtidigt kan det orsaka fel
* Sök hanterar sökning på ungefär 50 000 noder – efter detta kan prestanda påverkas eller orsaka ohanterat undantag

10/03/2016
### <a name="version-085"></a>Version 0.8.5

#### <a name="new"></a>Ny

* Nu kan du använda Portal-genererade SAS-nycklar för att ansluta till lagrings konton och resurser

#### <a name="fixes"></a>Korrigeringar

* Fast: tävlings villkor under sökningen gjorde ibland att noder kan bli icke-skalbara
* Åtgärdat: "Använd HTTP" fungerar inte vid anslutning till lagrings konton med konto namn och nyckel
* Fast: SAS-nycklar (särskilt Portal-genererade) returnerar fel meddelandet "avslutande snedstreck"
* Åtgärdat: problem med tabell import
    * Ibland har partitionsnyckel och rad nyckel återförts
    * Det gick inte att läsa "null"-partitionsuppsättningar

#### <a name="known-issues"></a>Kända problem

* Sök hanterar sökning på ungefär 50 000 noder – efter detta kan prestanda påverkas
* Azure Stack stöder för närvarande inte filer, så försök att expandera filer visar ett fel

09/12/2016
### <a name="version-084"></a>Version 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Ny

* Generera direkta länkar till lagrings konton, behållare, köer, tabeller eller fil resurser för delning och enkel åtkomst till dina resurser – Windows och Mac OS support
* Sök efter dina BLOB-behållare, tabeller, köer, fil resurser eller lagrings konton från sökrutan
* Nu kan du gruppera satser i Table Query Builder
* Byt namn på och kopiera/klistra in BLOB-behållare, fil resurser, tabeller, blobbar, BLOB-mappar, filer och kataloger i SAS-anslutna konton och behållare
* Att byta namn på och kopiera BLOB-behållare och fil resurser bevarar nu egenskaper och metadata

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: det går inte att redigera tabell entiteter om de innehåller booleska eller binära egenskaper

#### <a name="known-issues"></a>Kända problem

* Sök hanterar sökning på ungefär 50 000 noder – efter detta kan prestanda påverkas

08/03/2016
### <a name="version-083"></a>Version 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Ny

* Byt namn på behållare, tabeller, fil resurser
* Förbättrad upplevelse för Frågeverktyget
* Möjlighet att spara och läsa in frågor
* Direkta länkar till lagrings konton eller behållare, köer, tabeller eller fil resurser för att dela och enkelt komma åt dina resurser (Windows-endast macOS-support kommer snart!)
* Möjlighet att hantera och konfigurera CORS-regler

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: Microsoft-konton kräver ny autentisering var 8-12: e timme

#### <a name="known-issues"></a>Kända problem

* Ibland kan användar gränssnittet verka låsta – maximera fönstret för att lösa problemet
* macOS-installationen kan kräva förhöjd behörighet
* Panelen konto inställningar kan visa att du måste ange autentiseringsuppgifter för att kunna filtrera prenumerationer
* Att byta namn på fil resurser, BLOB-behållare och tabeller bevarar inte metadata eller andra egenskaper för behållaren, till exempel fil resurs kvot, offentlig åtkomst nivå eller åtkomst principer
* Att byta namn på blobbar (individuellt eller inuti en omdöpt BLOB-behållare) bevarar inte ögonblicks bilder. Alla andra egenskaper och metadata för blobbar, filer och entiteter bevaras under ett namnbyte
* Att kopiera eller byta namn på resurser fungerar inte i SAS-anslutna konton

07/07/2016
### <a name="version-082"></a>Version 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Ny

* Lagrings konton grupperas efter prenumerationer. utvecklings lagring och resurser som är anslutna via nyckel eller SAS visas under noden (lokal och ansluten)
* Logga ut från konton i panelen "Azure-konto inställningar"
* Konfigurera proxyinställningar för att aktivera och hantera inloggning
* Skapa och Bryt BLOB-lån
* Öppna BLOB-behållare, köer, tabeller och filer med enkel klickning

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat: köa meddelanden som infogats med .NET eller Java-bibliotek avkodas inte korrekt från base64
* Åtgärdat: $metrics tabeller visas inte för Blob Storage konton
* Fixed: tables-noden fungerar inte för lokal lagring (utveckling)

#### <a name="known-issues"></a>Kända problem

* macOS-installationen kan kräva förhöjd behörighet

06/15/2016
### <a name="version-080"></a>Version 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Ny

* Stöd för fil resurser: Visa, ladda upp, ladda ned, kopiera filer och kataloger, SAS URI: er (skapa och Anslut)
* Förbättrad användar upplevelse för att ansluta till lagring med SAS-URI: er eller konto nycklar
* Exportera tabell frågeresultaten
* Sortering och anpassning av tabell kolumn
* Visa $logs BLOB-behållare och $metrics tabeller för lagrings konton med aktiverade mått
* Förbättrade funktioner för export och import, innehåller nu egenskaps värde typ

#### <a name="fixes"></a>Korrigeringar

* Fast: överföring eller nedladdning av stora blobbar kan resultera i ofullständiga uppladdningar/nedladdningar
* Åtgärdat: redigering, tillägg eller import av en entitet med ett numeriskt sträng värde ("1") kommer att konverteras till dubbelt
* Åtgärdat: det går inte att expandera noden tabell i den lokala utvecklings miljön

#### <a name="known-issues"></a>Kända problem

* $metrics tabeller visas inte för Blob Storage-konton
* Queue meddelanden som läggs till program mässigt kanske inte visas korrekt om meddelandena är kodade med base64-kodning

05/17/2016
### <a name="version-07201605090"></a>Version 0.7.20160509.0

#### <a name="new"></a>Ny

* Bättre fel hantering för program krascher

#### <a name="fixes"></a>Korrigeringar

* Åtgärdat fel där informations fälts meddelanden ibland inte visas när inloggnings uppgifter krävs

#### <a name="known-issues"></a>Kända problem

* Tabeller: lägga till, redigera eller importera en entitet som har en egenskap med ett tvetydigt numeriskt värde, t. ex. "1" eller "1,0", och användaren försöker skicka den som en `Edm.String` , kommer värdet att komma tillbaka via klient-API: et som en EDM. dubbel

03/31/2016

### <a name="version-07201603250"></a>Version 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Ny

* Tabell stöd: Visa, fråga, exportera, importera och CRUD åtgärder för entiteter
* Queue support: Visa, lägga till, ta bort meddelanden i kö
* Genererar SAS-URI: er för lagrings konton
* Ansluta till lagrings konton med SAS-URI: er
* Uppdatera meddelanden för framtida uppdateringar till Storage Explorer
* Uppdaterat utseende och känsla

#### <a name="fixes"></a>Korrigeringar

* Förbättringar av prestanda och tillförlitlighet

### <a name="known-issues-amp-mitigations"></a>Begränsningar vid kända problem &amp;

* Hämtning av stora BLOB-filer fungerar inte korrekt – vi rekommenderar att du använder AzCopy medan vi löser problemet
* Kontoautentiseringsuppgifter kommer inte att hämtas eller cachelagras om det inte går att hitta eller skriva till arbetsmappen
* Om vi lägger till, redigerar eller importerar en entitet som har en egenskap med ett tvetydigt numeriskt värde, t. ex. "1" eller "1,0", och användaren försöker skicka den som en `Edm.String` , kommer värdet att komma tillbaka via klient-API: et som en EDM. dubbel
* När du importerar CSV-filer med Multiline-poster kan data bli chopped eller kodade

02/03/2016

### <a name="version-07201601291"></a>Version 0.7.20160129.1

#### <a name="fixes"></a>Korrigeringar

* Förbättrade övergripande prestanda vid överföring, hämtning och kopiering av blobbar

01/14/2016

### <a name="version-07201601050"></a>Version 0.7.20160105.0

#### <a name="new"></a>Ny

* Linux-stöd (paritets funktioner till OSX)
* Lägg till BLOB-behållare med nyckeln signatur för delad åtkomst (SAS)
* Lägg till lagrings konton för Azure Kina 21Vianet
* Lägg till lagrings konton med anpassade slut punkter
* Öppna och Visa innehållets text-och bild blobbar
* Visa och redigera BLOB-egenskaper och metadata

#### <a name="fixes"></a>Korrigeringar

* Fast: överföring eller hämtning av ett stort antal blobbar (500 +) kan ibland orsaka att appen har en vit skärm
* Fast: när du ställer in offentlig åtkomst nivå för BLOB container, uppdateras inte det nya värdet förrän du ställer in fokus på behållaren på nytt. Dessutom är dialog rutan alltid standard till "ingen offentlig åtkomst" och inte det faktiska aktuella värdet.
* Bättre övergripande stöd för tangent bord/tillgänglighet och gränssnitt
* Text för spår historik radbryts när den är lång med blank steg
* SAS-dialogrutan har stöd för verifiering av Indatatyp
* Lokal lagring fortsätter att vara tillgänglig även om användarautentiseringsuppgifter har upphört att gälla
* När en öppen BLOB-behållare tas bort stängs BLOB Explorer på den högra sidan

#### <a name="known-issues"></a>Kända problem

* Linux-installationen behöver gcc version uppdaterad eller uppgraderad – steg för att uppgradera nedan:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>Version 0.7.20151116.0

#### <a name="new"></a>Ny

* macOS och Windows-versioner
* Logga in för att visa dina lagrings konton – Använd ditt organisations konto, Microsoft-konto, 2FA, osv.
* Lokal utvecklings lagring (Använd Storage-emulator, endast Windows)
* Stöd för Azure Resource Manager och klassisk resurs
* Skapa och ta bort blobbar, köer eller tabeller
* Sök efter vissa blobbar, köer eller tabeller
* Utforska innehållet i BLOB-behållare
* Visa och navigera i kataloger
* Ladda upp, ladda ned och ta bort blobbar och mappar
* Visa och redigera BLOB-egenskaper och metadata
* Generera SAS-nycklar
* Hantera och skapa lagrade åtkomst principer (SAP)
* Sök efter blobbar efter prefix
* Dra n släppa filer för att ladda upp eller ladda ned

#### <a name="known-issues"></a>Kända problem

* När du ställer in offentlig åtkomst nivå för BLOB container, uppdateras inte det nya värdet förrän du ställer in fokus på behållaren igen
* När du öppnar dialog rutan för att ange offentlig åtkomst nivå visas alltid "ingen offentlig åtkomst" som standard och inte det faktiska aktuella värdet
* Det går inte att byta namn på hämtade blobbar
* Aktivitets logg poster kommer ibland att få "fastnat" i status status när ett fel uppstår och felet visas inte
* Ibland kraschar eller blir det helt vitt när du försöker överföra eller ladda ned ett stort antal blobbar
* Ibland fungerar det inte att avbryta en kopierings åtgärd
* När du skapar en behållare (BLOB/kö/tabell), om du har angett ett ogiltigt namn och fortsätter att skapa en annan under en annan behållar typ, kan du inte ange fokus för den nya typen
* Det går inte att skapa en ny mapp eller Byt namn på mapp




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md