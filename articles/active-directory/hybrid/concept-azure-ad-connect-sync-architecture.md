---
title: 'Azure AD Connect synkronisering: förstå arkitekturen – Azure'
description: I det här avsnittet beskrivs arkitekturen i Azure AD Connect Sync och förklarar de termer som används.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 465bcbe9-3bdd-4769-a8ca-f8905abf426d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27055ce84bbb073045b69b942fd13f4fde4e3b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90563870"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Azure AD Connect synkronisering: förstå arkitekturen
I det här avsnittet beskrivs den grundläggande arkitekturen för Azure AD Connect Sync. I många aspekter liknar de föregående aktiviteter MIIS 2003, ILM 2007 och FIM 2010. Azure AD Connect Sync är utvecklingen av dessa tekniker. Om du är bekant med någon av dessa tidigare tekniker är innehållet i det här avsnittet bekant för dig också. Om du inte har använt synkronisering igen är det här avsnittet för dig. Det är dock inte nödvändigt att veta mer om det här ämnet för att göra anpassningar till Azure AD Connect Sync (kallas Synkroniseringsmotorn i det här avsnittet).

## <a name="architecture"></a>Arkitektur
Synkroniseringsmotorn skapar en integrerad vy av objekt som lagras i flera anslutna data källor och hanterar identitets information i dessa data källor. Den här integrerade vyn bestäms av identitets informationen som hämtats från anslutna data källor och en uppsättning regler som avgör hur informationen ska bearbetas.

### <a name="connected-data-sources-and-connectors"></a>Anslutna data källor och anslutningar
Synkroniseringsmotorn bearbetar identitets information från olika data centraler, till exempel Active Directory eller en SQL Server databas. Alla data centraler som organiserar sina data i ett databas format och som tillhandahåller standard metoder för data åtkomst är en potentiell data käll kandidat för Synkroniseringsmotorn. Data centralerna som synkroniseras med Sync-motorn kallas **anslutna data källor** eller **anslutna kataloger** (CD).

Synkroniseringsmotorn kapslar in interaktionen med en ansluten data källa i en modul som kallas för en **koppling**. Varje typ av ansluten data källa har en speciell koppling. Anslutningen översätter en nödvändig åtgärd till det format som den anslutna data källan förstår.

Anslutningar gör API-anrop till att utbyta identitets information (både läsning och skrivning) med en ansluten data källa. Det är också möjligt att lägga till en anpassad anslutning med hjälp av Extensible Connectivity Framework. Följande bild visar hur en koppling ansluter en ansluten data källa till Synkroniseringsmotorn.

![Diagrammet visar en ansluten data källa och en Sync-motor som associeras med en rad som kallas koppling.](./media/concept-azure-ad-connect-sync-architecture/arch1.png)

Data kan flöda i båda riktningarna, men det går inte att flöda i båda riktningarna samtidigt. Med andra ord kan en anslutning konfigureras för att tillåta att data flödar från den anslutna data källan till Synkroniseringsmotorn eller från Synkroniseringsmotorn till den anslutna data källan, men endast en av dessa åtgärder kan utföras vid en och samma tidpunkt för ett objekt och attribut. Riktningen kan vara olika för olika objekt och för olika attribut.

Om du vill konfigurera en koppling anger du de objekt typer som du vill synkronisera. Om du anger objekt typer definieras omfattningen av de objekt som ingår i synkroniseringsprocessen. Nästa steg är att välja de attribut som ska synkroniseras, vilket kallas för en lista för att inkludera attribut. De här inställningarna kan ändras när som helst som svar på ändringar i affärs reglerna. När du använder installations guiden för Azure AD Connect konfigureras de här inställningarna åt dig.

Om du vill exportera objekt till en ansluten data källa måste attributlistan innehålla minst de minsta attribut som krävs för att skapa en angiven objekt typ i en ansluten data källa. Attributet **sAMAccountName** måste till exempel inkluderas i listan över inkluderade attribut för att exportera ett användar objekt till Active Directory eftersom alla användar objekt i Active Directory måste ha ett definierat **sAMAccountName** -attribut. Installations guiden kommer att konfigureras för dig.

Om den anslutna data källan använder strukturella komponenter, till exempel partitioner eller behållare för att ordna objekt, kan du begränsa områdena i den anslutna data källan som används för en specifik lösning.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Intern struktur för Sync-motorns namnrymd
Hela namn området för Sync-motorn består av två namn rymder som lagrar identitets informationen. De två namn områdena är:

* Kopplings utrymmet (CS)
* Metaversum (MV)

**Kopplings** området är ett mellanlagringsområde som innehåller representationer av de angivna objekten från en ansluten data källa och de attribut som anges i listan över inkluderade attribut. Synkroniseringsmotorn använder anslutnings utrymmet för att avgöra vad som har ändrats i den anslutna data källan och för att mellanlagra inkommande ändringar. Synkroniseringsmotorn använder också anslutnings utrymmet för att mellanlagra utgående ändringar för export till den anslutna data källan. Synkroniseringsmotorn underhåller ett distinkt anslutnings utrymme som ett mellanlagringsområde för varje koppling.

Med hjälp av ett mellanlagringsområde förblir Synkroniseringsmotorn oberoende av anslutna data källor och påverkas inte av deras tillgänglighet och tillgänglighet. Därför kan du bearbeta identitets information när som helst genom att använda data i mellanlagringsområdet. Synkroniseringsmotorn kan bara begära ändringar som gjorts i den anslutna data källan sedan den senaste sessionen avslutades eller push-överföra endast ändringar i identitets information som den anslutna data källan inte har tagits emot, vilket minskar nätverks trafiken mellan Synkroniseringsmotorn och den anslutna data källan.

Dessutom lagrar Sync-motorn statusinformation om alla objekt som det är i steg i anslutnings utrymmet. När nya data tas emot, utvärderar Sync-motorn alltid om data redan har synkroniserats.

**Metaversum** är ett lagrings utrymme som innehåller den sammanställda identitets informationen från flera anslutna data källor, vilket ger en enda global, integrerad vy över alla kombinerade objekt. Metaversum-objekt skapas utifrån identitets information som hämtas från anslutna data källor och en uppsättning regler som gör att du kan anpassa synkroniseringsprocessen.

Följande bild visar namn området för kopplings utrymmet och metaversum-namnområdet i Synkroniseringsmotorn.

![Diagrammet visar en ansluten data källa och en Sync-motor, som är separerad till kopplings utrymme och metaversum-namnområden, som associeras med en linje som kallas koppling.](./media/concept-azure-ad-connect-sync-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Synkronisera motor identitets objekt
Objekten i Synkroniseringsmotorn är representationer av antingen objekt i den anslutna data källan eller den integrerade vy som Synkroniseringsmotorn har av dessa objekt. Varje synkroniseringsobjekt måste ha en globalt unik identifierare (GUID). GUID ger data integritet och Express-relationer mellan objekt.

### <a name="connector-space-objects"></a>Kopplings rymds objekt
När Synkroniseringsmotorn kommunicerar med en ansluten data källa läser den identitets informationen i den anslutna data källan och använder den informationen för att skapa en representation av Identity-objektet i anslutnings utrymmet. Du kan inte skapa eller ta bort dessa objekt individuellt. Du kan dock manuellt ta bort alla objekt i ett kopplings utrymme.

Alla objekt i kopplings utrymmet har två attribut:

* En globalt unik identifierare (GUID)
* Ett unikt namn (kallas även DN)

Om den anslutna data källan tilldelar ett unikt attribut till objektet, kan objekt i anslutnings utrymmet också ha ett Anchor-attribut. Attributet Anchor identifierar unikt ett objekt i den anslutna data källan. Synkroniseringsmotorn använder fäst punkten för att hitta motsvarande representation av objektet i den anslutna data källan. Synkroniseringsmotorn förutsätter att fäst punkten för ett objekt aldrig ändras under objektets livs längd.

Många av kopplingarna använder en känd unik identifierare för att generera ett ankare automatiskt för varje objekt när det importeras. Till exempel använder Active Directory-anslutaren attributet **objectGUID** för ett ankare. För anslutna data källor som inte tillhandahåller en tydligt definierad unik identifierare kan du ange Anchor-generering som en del av anslutnings konfigurationen.

I så fall skapas ankaret från en eller flera unika attribut för en objekt typ, inte av vilka ändringar och som unikt identifierar objektet i kopplings området (till exempel ett anställnings nummer eller användar-ID).

Ett anslutnings utrymmes objekt kan vara något av följande:

* Ett mellanlagringsplats
* En plats hållare

### <a name="staging-objects"></a>Mellanlagrings objekt
Ett mellanlagringsplats objekt representerar en instans av de angivna objekt typerna från den anslutna data källan. Förutom GUID och det unika namnet har ett mellanlagringsplats alltid ett värde som anger objekt typen.

Tillfälliga objekt som har importer ATS har alltid ett värde för attributet Anchor. Mellanlagrings objekt som nyligen har etablerats av Synkroniseringsmotorn och som håller på att skapas i den anslutna data källan har inget värde för attributet Anchor.

Mellanlagrings objekt har även aktuella värden för affärsattribut och drift information som krävs av Synkroniseringsmotorn för att utföra synkroniseringsprocessen. Drift information innehåller flaggor som anger vilken typ av uppdateringar som mellanlagras på mellanlagringsplatsen. Om ett mellanlagringsplats objekt har fått ny identitets information från den anslutna data källan som ännu inte har bearbetats flaggas objektet som **väntande import**. Om ett mellanlagringsplats har ny identitets information som ännu inte har exporter ATS till den anslutna data källan, flaggas det som **väntande export**.

Ett mellanlagringsplats objekt kan vara ett import objekt eller ett export objekt. Synkroniseringsmotorn skapar ett import objekt genom att använda objekt information som tagits emot från den anslutna data källan. När Synkroniseringsmotorn tar emot information om förekomsten av ett nytt objekt som matchar en av de objekt typer som valts i kopplingen, skapas ett import objekt i anslutnings utrymmet som en representation av objektet i den anslutna data källan.

Följande bild visar ett import objekt som representerar ett objekt i den anslutna data källan.

![Diagram visar ett import objekt från den anslutna data källan till kopplings områdets namnrymd i Synkroniseringsmotorn.](./media/concept-azure-ad-connect-sync-architecture/arch3.png)

Synkroniseringsmotorn skapar ett export objekt med hjälp av objekt information i metaversum. Exportera objekt exporteras till den anslutna data källan under nästa kommunikations-session. Från Sync-motorns perspektiv finns inte export objekt i den anslutna data källan ännu. Därför är attributet Anchor för ett export objekt inte tillgängligt. När du har tagit emot objektet från Synkroniseringsmotorn skapar den anslutna data källan ett unikt värde för objektets Anchor-attribut.

Följande bild visar hur ett export objekt skapas med hjälp av identitets information i metaversum.

![Diagrammet visar ett export objekt från metaversum till kopplings områdets namnrymd, sedan till den anslutna data källan.](./media/concept-azure-ad-connect-sync-architecture/arch4.png)

Synkroniseringsmotorn bekräftar exporten av objektet genom att importera objektet från den anslutna data källan. Exportera objekt blir import objekt när Synkroniseringsmotorn tar emot dem under nästa import från den anslutna data källan.

### <a name="placeholders"></a>Platshållare
Synkroniseringsmotorn använder ett plant namn område för att lagra objekt. Men vissa anslutna data källor, till exempel Active Directory använder ett hierarkiskt namn område. För att transformera information från ett hierarkiskt namn område till ett fast namn område använder Synkroniseringsmotorn plats hållare för att bevara hierarkin.

Varje plats hållare representerar en komponent (till exempel en organisationsenhet) av ett objekts hierarkiskt namn som inte har importer ATS till Synkroniseringsmotorn men som krävs för att skapa det hierarkiska namnet. De fyller luckor som skapats av referenser i den anslutna data källan till objekt som inte är mellanlagrings objekt i anslutnings utrymmet.

Synkroniseringsmotorn använder också plats hållare för att lagra refererade objekt som ännu inte har importer ATS. Om synkronisering till exempel är konfigurerat för att inkludera attributet Manager för *Abbie Spencer* -objektet och det mottagna värdet är ett objekt som inte har importer ATS än, till exempel *CN = Pettersson Sperry, CN = användare, DC = Fabrikam, DC = com*, lagras Manager-informationen som plats hållare i anslutnings utrymmet. Om objektet Manager senare importeras skrivs placeholder-objektet över av det mellanlagringsplats som representerar chefen.

### <a name="metaverse-objects"></a>Metaversum-objekt
Ett metaversum-objekt innehåller den sammanställda vyn som Sync-motorn har för mellanlagrings objekt i anslutnings utrymmet. Synkroniseringsmotorn skapar metaversum-objekt med hjälp av informationen i Importera objekt. Flera kopplings utrymmes objekt kan länkas till ett enda metaversum-objekt, men ett kopplings rymd objekt kan inte länkas till fler än ett metaversum-objekt.

Metaversum-objekt kan inte skapas eller tas bort manuellt. Synkroniseringsmotorn tar automatiskt bort metaversum-objekt som inte har någon länk till något kopplings utrymmes objekt i kopplings utrymmet.

För att mappa objekt inom en ansluten data källa till en motsvarande objekt typ inom metaversum, tillhandahåller Synkroniseringsmotorn ett utöknings Bart schema med en fördefinierad uppsättning objekt typer och associerade attribut. Du kan skapa nya objekt typer och attribut för metaversum-objekt. Attribut kan vara enkelvärdesattribut eller flervärdesattribut, och attributtyper kan vara strängar, referenser, siffror och booleska värden.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relationer mellan mellanlagrings objekt och metaversum-objekt
I namn området för Synkroniseringsmotorn aktive ras data flödet av länk relationen mellan mellanlagrings objekt och metaversum-objekt. Ett mellanlagringsplats objekt som är länkat till ett metaversum-objekt kallas för ett **anslutet objekt** (eller **kopplings objekt**). Ett mellanlagringsplats objekt som inte är länkat till ett metaversum-objekt kallas för ett frånkopplat **objekt** (eller ett **från kopplings objekt**). Villkoren som anslöts och kopplas från är att föredra att inte förväxla med de anslutningar som ansvarar för att importera och exportera data från en ansluten katalog.

Plats hållare länkas aldrig till ett metaversum-objekt

Ett kopplat objekt omfattar ett mellanlagringsplats och dess länkade relation till ett enda metaversum-objekt. Anslutna objekt används för att synkronisera attributvärden mellan ett kopplings utrymmes objekt och ett metaversum-objekt.

När ett mellanlagringsplats blir ett anslutet objekt under synkroniseringen kan attributen flöda mellan mellanlagringsplatsen och metaversum-objektet. Attribute Flow är dubbelriktat och har kon figurer ATS med hjälp av regler för att importera attribut och exportera regler för attribut.

Ett enda objekt för kopplings utrymme kan bara länkas till ett metaversum-objekt. Varje metaversum-objekt kan dock länkas till flera kopplings utrymmes objekt i samma eller i olika kopplings utrymmen, som du ser i följande bild.

![Diagrammet visar två anslutna data objekt som är associerade med kopplingar till en Sync-motor, som har anslutna objekt och ett frånkopplat objekt.](./media/concept-azure-ad-connect-sync-architecture/arch5.png)

Den länkade relationen mellan mellanlagringsplatsen och ett metaversum-objekt är beständigt och kan bara tas bort av regler som du anger.

Ett frånkopplat objekt är ett mellanlagringsplats som inte är länkat till något metaversum-objekt. Attributvärdena för ett frånkopplat objekt bearbetas inte ytterligare i metaversum. Attributvärdena för motsvarande objekt i den anslutna data källan uppdateras inte av Synkroniseringsmotorn.

Genom att använda frånkopplade objekt kan du lagra identitets information i Synkroniseringsmotorn och bearbeta den senare. Att hålla ett mellanlagringsplats som ett frånkopplat objekt i anslutnings utrymmet har många fördelar. Eftersom systemet redan har mellanlagrat nödvändig information om det här objektet, behöver du inte skapa en representation av det här objektet igen under nästa import från den anslutna data källan. På så sätt har Sync-motorn alltid en fullständig ögonblicks bild av den anslutna data källan, även om det inte finns någon aktuell anslutning till den anslutna data källan. Frånkopplade objekt kan konverteras till anslutna objekt och vice versa, beroende på vilka regler som du anger.

Ett import objekt skapas som ett frånkopplat objekt. Ett export objekt måste vara ett anslutet objekt. System logiken tillämpar den här regeln och tar bort alla export objekt som inte är kopplade till objekt.

## <a name="sync-engine-identity-management-process"></a>Hanterings process för synkronisering av motor identitet
Processen för identitets hantering styr hur identitets information uppdateras mellan olika anslutna data källor. Identitets hantering sker i tre processer:

* Importera
* Synkronisering
* Exportera

Under importen utvärderar Synkroniseringsmotorn den inkommande identitets informationen från en ansluten data källa. När ändringar identifieras, skapar den antingen nya mellanlagringsplatser eller uppdaterar befintliga mellanlagringsplatser i anslutnings utrymmet för synkronisering.

Under synkroniseringsprocessen uppdaterar Synkroniseringsmotorn metaversum för att avspegla ändringar som har inträffat i anslutnings utrymmet och uppdaterar anslutnings utrymmet för att återspegla ändringar som har inträffat i metaversum.

Under exporten skickar Synkroniseringsmotorn ut ändringar som mellanlagras för mellanlagrings objekt och som är flaggade som väntande export.

Följande bild visar var varje process sker när identitets information flödar från en ansluten data källa till en annan.

![Diagrammet visar flödet av identitets information från anslutna data till kopplings utrymme (import) till metaversum till kopplings utrymme (synchonization) till anslutna data (export).](./media/concept-azure-ad-connect-sync-architecture/arch6.png)

### <a name="import-process"></a>Importera process
Under importen utvärderar Synkroniseringsmotorn uppdateringar av identitets information. Synkroniseringsmotorn jämför identitets informationen som togs emot från den anslutna data källan med identitets informationen om ett mellanlagringsplatss objekt och avgör om mellanlagringsmappen kräver uppdateringar. Om det är nödvändigt att uppdatera mellanlagringsplatsen med nya data flaggas mellanlagringsmappen som väntande import.

Genom att mellanlagra objekt i anslutnings utrymmet före synkroniseringen, kan Synkroniseringsmotorn endast bearbeta den identitets information som har ändrats. Den här processen ger följande fördelar:

* **Effektiv synkronisering**. Mängden data som bearbetas under synkroniseringen minimeras.
* **Effektiv omsynkronisering**. Du kan ändra hur Synkroniseringsmotorn bearbetar identitets information utan att behöva återansluta Synkroniseringsmotorn till data källan.
* **Möjlighet att förhandsgranska synkroniseringen**. Du kan förhandsgranska synkroniseringen för att kontrol lera att dina antaganden om identitets hanterings processen är korrekt.

För varje objekt som anges i anslutningen försöker Synkroniseringsmotorn först hitta en representation av objektet i kopplingens anslutnings område. Synkroniseringsmotorn undersöker alla mellanlagrings objekt i anslutnings utrymmet och försöker hitta ett motsvarande mellanlagringsplats objekt som har ett matchande Anchor-attribut. Om inget befintligt mellanlagringsdatabas har ett matchande Anchor-attribut försöker Synkroniseringsmotorn hitta ett motsvarande mellanlagringsplats med samma unika namn.

När Synkroniseringsmotorn hittar ett mellanlagringsplats som matchar med unikt namn men inte av ankare, inträffar följande särskilda beteenden:

* Om objektet som finns på kopplings området inte har något ankare, tar Synkroniseringsmotorn bort objektet från anslutnings utrymmet och markerar metaversum-objektet som det är länkat till som **försök att etablering vid nästa synkronisering**. Sedan skapas det nya import-objektet.
* Om objektet som finns på kopplings utrymmet har ett ankare, förutsätter Synkroniseringsmotorn att objektet antingen har bytt namn eller tagits bort i den anslutna katalogen. Det tilldelar ett tillfälligt, nytt unikt namn för objektet Connector-utrymme så att det kan mellanlagra det inkommande objektet. Det gamla objektet blir **tillfälligt övergående** och väntar på att anslutningen ska importera namnbytet eller borttagningen för att lösa problemet.

Om Synkroniseringsmotorn hittar ett mellanlagringsplats objekt som motsvarar det objekt som anges i anslutningen, avgör det vilken typ av ändringar som ska tillämpas. Till exempel kan Synkroniseringsmotorn byta namn på eller ta bort objektet i den anslutna data källan, eller så kan det bara uppdatera objektets attributvärden.

Mellanlagring av objekt med uppdaterade data markeras som väntande import. Olika typer av väntande importer är tillgängliga. Beroende på resultatet av import processen har ett mellanlagringsplats utrymme i anslutnings utrymmet någon av följande väntande import typer:

* **Ingen**. Inga ändringar av attributen för mellanlagringsplatsen är tillgängliga. Synkroniseringsmotorn flaggar inte den här typen som väntande import.
* **Lägg till**. Mellanlagringsplatsen är ett nytt import objekt i anslutnings utrymmet. Synkroniseringsmotorn flaggar den här typen som väntande import för ytterligare bearbetning i metaversum.
* **Uppdatera**. Synkroniseringsmotorn hittar ett motsvarande mellanlagrings objekt i anslutnings utrymmet och flaggar den här typen som väntande import så att uppdateringar av attributen kan bearbetas i metaversum. Uppdateringarna innehåller namnbyte för objekt.
* **Delete** (Ta bort). Synkroniseringsmotorn hittar ett motsvarande mellanlagrings objekt i anslutnings utrymmet och flaggar den här typen som väntande import så att det anslutna objektet kan tas bort.
* **Ta bort/Lägg till**. Synkroniseringsmotorn hittar ett motsvarande mellanlagrings objekt i anslutnings utrymmet, men objekt typerna stämmer inte överens. I det här fallet mellanlagras en ändring av borttagnings tillägg. En borttagning – Lägg till-ändring anger till Synkroniseringsmotorn att en fullständig omsynkronisering av det här objektet måste inträffa på grund av att olika uppsättningar regler gäller för objektet när objekt typen ändras.

Genom att ange status för väntande import för ett mellanlagringsplats är det möjligt att minska mängden data som bearbetas under synkroniseringen, eftersom det gör att systemet endast bearbetar de objekt som har uppdaterade data.

### <a name="synchronization-process"></a>Synkroniseringsprocess
Synkroniseringen består av två relaterade processer:

* Inkommande synkronisering, när innehållet i metaversum uppdateras med hjälp av data i anslutnings utrymmet.
* Utgående synkronisering, när innehållet i anslutnings utrymmet uppdateras med hjälp av data i metaversum.

Genom att använda den information som mellanlagras i anslutnings utrymmet skapar processen för inkommande synkronisering i metaversum den integrerade vyn av de data som lagras i de anslutna data källorna. Antingen alla mellanlagrings objekt eller endast de som har en väntande import information sammanställs, beroende på hur reglerna har kon figurer ATS.

Processen för utgående synkronisering uppdaterar export objekt när metaversum objekt ändras.

Inkommande synkronisering skapar den integrerade vyn i metaversum för den identitets information som tas emot från anslutna data källor. Synkroniseringsmotorn kan bearbeta identitets information när som helst genom att använda den senaste identitets informationen som den har från den anslutna data källan.

**Inkommande synkronisering**

Inkommande synkronisering omfattar följande processer:

* **Etablera** (kallas även **projektion** om det är viktigt att skilja den här processen från utgående synkronisering). Synkroniseringsmotorn skapar ett nytt metaversum-objekt baserat på ett mellanlagringsplats objekt och länkar dem. Etablera är en åtgärd på objekt nivå.
* **Anslut**. Synkroniseringsmotorn länkar ett mellanlagringsplats till ett befintligt metaversum-objekt. En koppling är en åtgärd på objekt nivå.
* **Importera attributets flöde**. Synkroniseringsmotorn uppdaterar attributvärdena, anropade Attribute Flow, för objektet i metaversum. Importera attributets flöde är en åtgärd på attributnivå som kräver en länk mellan ett mellanlagringsplatss objekt och ett metaversum-objekt.

Etablera är den enda processen som skapar objekt i metaversum. Etableringen påverkar endast import objekt som är frånkopplade objekt. Under etableringen skapar Synkroniseringsmotorn ett metaversum-objekt som motsvarar objekt typen för objektet importera och upprättar en länk mellan båda objekten, vilket skapar ett anslutet objekt.

Kopplings processen upprättar också en länk mellan import objekt och ett metaversum-objekt. Skillnaden mellan koppling och etablering är att kopplings processen kräver att import-objektet är länkat till ett befintligt metaversum-objekt, där etablerings processen skapar ett nytt metaversum-objekt.

Synkroniseringsmotorn försöker ansluta ett import objekt till ett metaversum-objekt med hjälp av villkor som anges i konfigurationen för synkroniseringsregeln.

Under etableringen och Join-processen länkar Synkroniseringsmotorn ett frånkopplat objekt till ett metaversum-objekt, vilket gör dem anslutna. När dessa objekt nivå åtgärder har slutförts kan Synkroniseringsmotorn uppdatera attributvärdena för det associerade metaversum-objektet. Den här processen kallas för att importera attribut.

Importera attributets flöde sker på alla import objekt som har nya data och som är länkade till ett metaversum-objekt.

**Utgående synkronisering**

Utgående uppdateringar för synkronisering Exportera objekt när ett metaversum-objekt ändras, men inte tas bort. Målet med utgående synkronisering är att utvärdera om ändringar av metaversum-objekt kräver uppdateringar av mellanlagrings objekt i kopplings utrymmen. I vissa fall kan ändringarna kräva att mellanlagrings objekt i alla kopplings utrymmen uppdateras. Tillfälliga objekt som ändras flaggas som väntande export, vilket gör dem till att exportera objekt. Dessa export objekt publiceras senare till den anslutna data källan under export processen.

Utgående synkronisering har tre processer:

* **Etablering**
* **Avetablering**
* **Exportera attributets flöde**

Etablering och avetablering är både åtgärder på objekt nivå. Avetableringen beror på etableringen eftersom endast etableringen kan initieras. Avetablering utlöses när etableringen tar bort länken mellan ett metaversum-objekt och ett export objekt.

Etableringen utlöses alltid när ändringar tillämpas på objekt i metaversum. När ändringar görs i metaversum-objekt kan Synkroniseringsmotorn utföra någon av följande uppgifter som en del av etablerings processen:

* Skapa kopplade objekt, där ett metaversum-objekt är länkat till ett nyligen skapat export objekt.
* Byt namn på ett anslutet objekt.
* Koppla ihop länkar mellan ett metaversum-objekt och mellanlagrings objekt, vilket skapar ett frånkopplat objekt.

Om etableringen kräver att Synkroniseringsmotorn används för att skapa ett nytt kopplings objekt är det mellanlagringsplatsen som metaversum-objektet länkas till alltid ett export objekt, eftersom objektet inte finns i den anslutna data källan ännu.

Om etableringen kräver att Synkroniseringsmotorn används för att koppla från ett kopplat objekt, utlöses avetablering. Avetablerings processen tar bort objektet.

Under avetableringen tar borttagning av ett export objekt inte bort objektet fysiskt. Objektet är flaggat som **Borttaget**, vilket innebär att borttagnings åtgärden mellanlagras på objektet.

Export av Attribute-flöde sker även under processen för utgående synkronisering, på samma sätt som när det gäller att importera Attribute-flöde under inkommande synkronisering. Export av Attribute-flöde sker bara mellan metaversum och exporterade objekt som är anslutna.

### <a name="export-process"></a>Exportera process
Under exporten undersöker Synkroniseringsmotorn alla export objekt som är flaggade som väntande export i anslutnings utrymmet och skickar sedan uppdateringar till den anslutna data källan.

Synkroniseringsmotorn kan avgöra om en export lyckades, men det är inte tillräckligt för att avgöra om identitets hanterings processen är klar. Objekt i den anslutna data källan kan alltid ändras av andra processer. Eftersom Synkroniseringsmotorn inte har en permanent anslutning till den anslutna data källan räcker det inte att göra antaganden om egenskaperna för ett objekt i den anslutna data källan enbart baserat på ett lyckat export meddelande.

En process i den anslutna data källan kan till exempel ändra objektets attribut tillbaka till sina ursprungliga värden (det vill säga att den anslutna data källan skriver över värdena direkt efter att data har skickats ut av Synkroniseringsmotorn och tillämpats i den anslutna data källan).

Synkroniseringsmotorn lagrar information om export-och import status för varje mellanlagringsdatabas. Om värdena för attributen som anges i listan över inkludering av attribut har ändrats sedan den senaste exporten, gör lagringen av import-och export status att Synkroniseringsmotorn kan reagera på lämpligt sätt. Synkroniseringsmotorn använder import processen för att bekräfta attributvärden som har exporter ATS till den anslutna data källan. En jämförelse mellan den importerade och exporterade informationen, som du ser i följande bild, aktiverar Synkroniseringsmotorn för att avgöra om exporten lyckades eller om den måste upprepas.

![Diagrammet visar synkroniseringen av ett objekt mellan kopplings utrymme och anslutna data via anslutningen.](./media/concept-azure-ad-connect-sync-architecture/arch7.png)

Om till exempel Synkroniseringsmotorn exporterar attribut C, som har värdet 5, till en ansluten data källa, lagrar den C = 5 i exportens status minne. Varje ytterligare export av det här objektet resulterar i ett försök att exportera C = 5 till den anslutna data källan igen eftersom Synkroniseringsmotorn förutsätter att det här värdet inte har tillämpats permanent på objektet (det vill säga om ett annat värde har importer ATS nyligen från den anslutna data källan). Export minnet rensas när C = 5 tas emot under en import åtgärd på objektet.

## <a name="next-steps"></a>Nästa steg
Läs mer om [Azure AD Connect Sync](how-to-connect-sync-whatis.md) -konfigurationen.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).

