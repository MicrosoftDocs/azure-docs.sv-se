---
title: Skapa ett kommersiellt Azure Remote Rendering-program
description: Strategier och överväganden för att skapa ett kommersiellt klart program med Azure Remote rendering
author: FlorianBorn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.openlocfilehash: 0dad78ad76a870ea9f1db28a3cb5ccace5cd804f
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510937"
---
# <a name="tutorial-creating-a-commercial-ready-azure-remote-rendering-application"></a>Självstudie: skapa ett affärs klart program för Azure Remote rendering

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Sessionshantering för kommersiella program
> * Spåra sessioner för fakturering
> * Optimera användar upplevelsen kring tiden för inläsning av session
> * Att tänka på kring nätverks fördröjning

## <a name="prerequisites"></a>Krav

* Den här självstudien bygger på [Självstudier: skydda Azure-Fjärråter givning och modell lagring](../security/security.md).

## <a name="introduction-to-commercial-readiness"></a>Introduktion till kommersiell beredskap

Azure Remote rendering utökar vad som är möjligt med Mixad verklighet. När grunderna är integrerade i din lösning finns det ett antal ytterligare saker att se till att din lösning är säker, skalbar och redo att leverera värde.

I den här modulen får du en introduktion till vissa ytterligare funktioner som du kan behöva tänka på för ditt kommersiella program.

En bred översikt över metod tips för System-Wide-arkitektur finns på:

* [Azure Architecture Center](https://docs.microsoft.com/azure/architecture/)
* [Kom igång-guide för Azure-utvecklare](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide)

## <a name="analytics"></a>Analytics

Integrering av analys verktyg kan hjälpa dig att hantera, spåra och förbättra din lösning.

En omfattande lista över de analys resurser som är tillgängliga för dig finns på:

* [Azure Analytics-tjänster](https://azure.microsoft.com/product-categories/analytics/)

### <a name="tracking-usage-for-billing"></a>Spåra användning för fakturering

Spårning av användningen av Azure-fjärrrendering av flera interna team eller externa klienter blir en viktig faktor, särskilt i situationer med flera innehavare.

För att uppnå detta erbjuder Azure en tjänst som kallas resurs märkning, som associerar användningen av Azure Remote rendering-tjänsten med varje klient.

För mer information om resurs namn och taggning är en bra plats att starta:

* [Guide för resurs namn givning och Taggnings beslut](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

### <a name="diagnostics"></a>Diagnostik

Kraftfulla verktyg som ETW (Event Tracing for Windows) (ETW) och händelse spårnings loggning (ETL) gör det enkelt att generera spårnings händelser i ditt program och kan hjälpa till att diagnostisera nätverk, innehålls inmatning, sessioner, program och andra problem som kan uppstå i en kommersiell lösnings distribution.

Mer information finns på:

* [Skapa prestanda spårning på klient Sidan](https://docs.microsoft.com/azure/remote-rendering/how-tos/performance-tracing)
* [Samla in ETW (Event Tracing for Windows)-data (ETW)](https://docs.microsoft.com/visualstudio/profiling/how-to-collect-event-tracing-for-windows-etw-data)
* [Använda Windows-enhets portalen: Logga](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal)

### <a name="usage-analysis"></a>Användnings analys

Azure Application Insights hjälper dig att förstå hur personer använder ditt Azure-program för fjärråter givning. Varje gång du uppdaterar din app kan du utvärdera hur väl den fungerar för användare och förbättra din lösning. Med den här kunskapen kan du fatta data drivna beslut om dina nästa utvecklings cykler.

Mer information finns på:

* [Användnings analys med Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)

## <a name="fast-startup-time-strategies"></a>Snabba start tids strategier

Ditt användnings fall kan kräva snabb start från program start till visning av 3D-modell. Till exempel, under ett viktigt möte där det är viktigt att allt körs i förväg. Ett annat exempel är under en granskning av en CAD 3D-modell där snabba design iterationer mellan ett CAD-program och Mixad verklighet är nyckeln till effektiv.

Azure Remote rendering kräver förbearbetade 3D-modeller och Azure tar för närvarande flera minuter att skapa en session och läsa in en modell för åter givning. Att göra den här processen så sömlös och snabb som möjligt kräver förberedelse av 3D-modellens data och ARR-sessionen i förväg.

De förslag som delas här ingår för närvarande inte i Azures standard åter givning, men du kan implementera dem på egen hand för snabbare start tider.

### <a name="initiate-early"></a>Initiera tidig

För att minska start tiden är den enklaste lösningen att flytta skapande och initiering av sessionen så tidigt som möjligt i användar arbets flödet. En strategi är att initiera sessionen så snart det är känt att en ARR-session krävs. Detta är ofta när användaren börjar ladda upp en 3D-modell till Azure Blob Storage använda med Azure-fjärrrendering. I det här fallet kan skapande av sessioner och initiering initieras samtidigt som den nedladdade 3D-modellen så att båda arbets strömmar körs parallellt.

Den här processen kan effektiviseras ytterligare genom att se till att de valda Azure Blob Storage-och utmatnings behållarna finns i samma regionala Data Center som Azure-fjärrrendering-sessionen.

### <a name="scheduling"></a>Schemaläggning

Om du vet att du har ett framtida behov av Azure-fjärrrendering kan du schemalägga ett visst datum och en tid för att starta Azure-fjärrrendering-sessionen.

Det här alternativet kan erbjudas via en webb portal där människor både kan ladda upp en 3D-modell och schemalägga en tid för att visa den i framtiden. Detta är också en bra plats för att be om andra inställningar som [*standard*](../../../reference/vm-sizes.md) eller [*Premium*](../../../reference/vm-sizes.md) åter givning. *Premium* -åter givning kan vara lämpligt om det finns en önskan att visa en blandning av till gångar där den idealiska storleken är svårare att fastställa eller ett behov av att se till att Azure-regionen har tillgängliga virtuella datorer vid den angivna tidpunkten.

### <a name="session-pooling"></a>Anslutningspoolning

I de mest krävande situationer är ett annat alternativ att anslutningspoolen, där en eller flera sessioner skapas och initieras hela tiden. Detta skapar en sessionsbiljett för omedelbar användning av en begär ande användare. Nack delen med den här metoden är att när den virtuella datorn har initierats startar faktureringen för tjänsten. Det kanske inte är kostnads effektivt att ha en sessionsbiljett som körs hela tiden, men baserat på analys, kan det vara möjligt att förutsäga högsta belastning eller kombineras med planerings strategin ovan för att förutsäga när sessioner kommer att behövas och öka och ned i sessionen.

Den här strategin hjälper också till att optimera valet mellan *standard* -och *Premium* -sessioner på ett mer dynamiskt sätt eftersom det skulle bli mycket snabbare att växla mellan de två typerna i en enda användarsession, till exempel när en *förstklassig* modell visas först, följt av en som kan fungera inom *standard*. Om dessa användarsessioner är ganska långa kan det vara betydande besparingar.

Om du vill ha mer information om Azure Remote rendering-sessioner kan du kolla in:

* [Remote Rendering-sessioner](https://docs.microsoft.com/azure/remote-rendering/concepts/sessions)

## <a name="standard-vs-premium-server-size-routing-strategies"></a>Strategier för routning av standard vs. Premium-Server storlek

Genom att välja om du vill skapa en *standard* -eller *Premium* Server-storlek presenteras en utmaning vid utformningen av användar upplevelsen och slut punkt till slut punkt. Även om endast *Premium* -sessioner används är ett alternativ, använder *standard* -sessioner mycket mindre Azure Compute-resurser och är billigare än *Premium*. Detta ger ett starkt motivation att använda *standardsessioner* närhelst det är möjligt och endast använda *Premium* vid behov.

Här delar vi flera alternativ, från minst till mest omfattande, för att kunna hantera valen av sessioner.

### <a name="use-only-standard-or-premium"></a>Använd endast standard eller Premium

Om du är säker på att dina behov *alltid* hamnar under tröskelvärdet mellan *standard* och *Premium*, så fören klar ditt beslut avsevärt. Använd bara *standard*. Tänk på att påverkan på användar upplevelsen är viktig om den totala komplexiteten för de inlästa resurserna avvisas som för komplex *för en* standardsession.

Om du förväntar dig att en stor del av användningarna överskrider tröskelvärdet mellan *standard* och *Premium*, eller om kostnaden inte är en viktig faktor i ditt användnings fall, är det också ett alternativ för att hålla det enkelt att välja *Premium* .

### <a name="ask-the-user"></a>Be användaren

Om du vill ha stöd för både *standard* och *Premium*, är det enklaste sättet att avgöra vilken typ av session som ska instansieras att fråga användaren när de väljer 3D-tillgångar att visa. Utmaningen med den här metoden är att användaren måste förstå 3D-till gångens komplexitet eller till och med flera till gångar som ska visas. Detta rekommenderas vanligt vis inte av den anledningen. Om användaren väljer fel och väljer *standard*kan den resulterande användar upplevelsen komprometteras vid en inopportune tidpunkt.

### <a name="analyze-the-3d-model"></a>Analysera 3D-modellen

En annan relativt enkel metod är att analysera komplexiteten för de valda 3D-resurserna. Om modell komplexiteten är under tröskelvärdet för *standard*startar du en *standardsession,* annars initierar en *Premium* -session. Här är utmaningen att en enskild session slutligen kan användas för att visa flera modeller av vilka vissa kan överstiga komplexitets tröskeln för en standardsession, vilket ger möjlighet att sömlöst använda samma session *för en sekvens* med olika 3D-tillgångar.

### <a name="automatic-switching"></a>Automatisk växling

Automatisk växling mellan *standard* -och *Premium* -sessioner kan göra mycket bra i en system design som även omfattar anslutningspoolen. Den här strategin möjliggör ytterligare optimering av resursutnyttjande. När användaren läser in modeller för visning, bestäms komplexiteten och rätt sessions storlek begärs från tjänsten session pooling.

## <a name="working-with-networks"></a>Arbeta med nätverk

### <a name="diagnostics"></a>Diagnostik

Azure Remote rendering kräver en snabb Internet anslutning med låg latens. Kvaliteten på användarens nätverk kan ha stor inverkan på upplevelsens kvalitet. Med tanke på att dina klienter troligen har olika nätverkskonfigurationer och bara ibland dåliga nätverks fördröjning, är diagnostikverktygen viktiga.  

För att säkerställa att du kan leverera en konsekvent hög kvalitet, rekommenderar vi att du integrerar analys verktyg på Server sidan och klient sidan i Azure-programmet för fjärrrendering. Om du gör det kan du använda den information som du behöver för att diagnostisera och minimera eventuella nätverks problem som dina klienter kan ha.

### <a name="client-network-configurations"></a>Klient nätverks konfiguration

En av de största utmaningarna med att utveckla robusta samarbets lösningar som distribueras till en rad olika företags miljöer förbereds för den olika nätverks sto pol Ogin och de konfigurationer för Enterprise-brandvägg som dina klienter använder.

Många företag blockerar all peer-to-peer-trafik i ett lokalt nätverk. Detta gör det svårt att dra nytta av den enkla och effektiviserade UX-identifieraren för automatisk LAN-identifiering för att upprätta en lokal delad session bland alla identifierade instanser av ditt blandade Reality-program.

Andra möjliga felpunkter är routrar som kon figurer ATS för avsiktlig begränsning av bandbredd och brand väggar som blockerar de flesta TCP/IP-portarna.

När du planerar att använda Azure Remote rendering på ett välkänt nätverk rekommenderar vi följande:

* Ange en check lista för för hands möten för att utvärdera nätverks beredskap.
* Se till att det aktuella regionala data centret kan betjäna begäran.
* Tillåt gott om tid för att diagnostisera eventuella problem.
* Ta ett mobilt hotspot-objekt med en hög bandbredds plan som säkerhets kopiering.

### <a name="end-to-end-bandwidth"></a>Bandbredd från slut punkt till slut punkt

Det är viktigt att utvärdera bandbredds funktionerna för varje del av nätverket som kan finnas mellan den virtuella Azure-fjärråtergivningen och slut klienten. Tänk på att nätverks segmentet från Azure Data Center till klientens Internet leverantör kan vara mer av en begränsnings faktor än från Internet leverantören till klienten. Hastighets testet för BLOB-hämtning kan användas för att diagnostisera problem.

### <a name="bandwidth-competition"></a>Bandbredds tävling

När du designar ditt blandade Reality-program bör du tänka på att olika funktioner i appen kan konkurrera med Azures fjärrrendering för bandbredd. Det mest sannolika exemplet är när många deltagare i ett enda rum förväntar sig att använda ARR samtidigt för att visa en 3D-till gång. Varje del av nätverks data flödet måste ha kapacitet att transportera summan av alla ARR-dataströmmar tillsammans.

Andra exempel är direktuppspelad video, samtidiga bakgrunds överföringar av annat relaterat innehåll och röst samtal, särskilt där det finns många deltagare och systemet använder en distribuerad peer-to-peer-metod i stället för en ljud mixnings server i den mellersta metoden.

För mer information om Network Analytics, se:

* [Hastighets test för Azure Storage Blob hämtning](https://www.azurespeed.com/Azure/Download)
* [Svars tids statistik för Azure Network tur och retur](https://docs.microsoft.com/azure/networking/azure-network-latency)
* [Prestanda spårning på Server Sidan](https://docs.microsoft.com/azure/remote-rendering/overview/features/performance-queries)
* [Prestanda spårning på klient Sidan](https://docs.microsoft.com/azure/remote-rendering/how-tos/performance-tracing)

## <a name="collaboration-considerations"></a>Samarbets överväganden

Några av de mest värdefulla användningarna av Azure-fjärråter givning innebär samarbete mellan flera deltagare som visar samma 3D-upplevelse på samma tid. I dessa delade sessioner är det viktigt att känna till att varje deltagare behöver en unik Azure-fjärrrendering-session, oavsett om de finns på samma plats i samma nätverk eller inte.

Detta är sant eftersom varje deltagare faktiskt ser samma erfarenhet från olika Vantage punkter, vilket kräver att samma 3D-tillgångar återges från var och en av dessa perspektiv samtidigt.

### <a name="multiple-azure-remote-rendering-sessions"></a>Flera Azure Remote rendering-sessioner

Om du planerar att stödja delade upplevelser med Azure-fjärrrendering måste de system som du lägger på plats för att skapa och hantera ARR-sessioner förberedas för att initiera flera sessioner. Dessa sessioner kan behöva initieras i olika Azure-datacenter om deltagarna är geografiskt spridda.

Ditt system måste också hantera möjligheten att en eller flera av deltagarna kan finnas i en geografisk region som för närvarande inte stöds av Azures fjärrrendering eller som för närvarande inte har några tillgängliga VM-instanser för Azure Remote rendering.

Den här hanteringen av flera samtidiga sessioner kan effektiviseras ytterligare när den kombineras med anslutningspoolning och andra strategier som beskrivs i det här dokumentet.

### <a name="azure-blob-storage-considerations"></a>Överväganden för Azure Blob Storage

Alla samtidiga ARR-sessioner kan referera till samma SAS-URI för den konverterade modell som ska visas. Detta gör det möjligt att ladda upp och konvertera önskade 3D-tillgångar en gång och sedan dela den i alla sessioner. Detta gäller särskilt när deltagarna är samplacerade och använder samma data Center där det inte finns några prestanda problem som rör Azure-Blob Storage som finns i ett annat data Center än Azure-fjärråter givnings servern och användaren.

Om 3D-tillgångar ofta laddas upp för en enda visnings session och sedan ignoreras, t. ex. i en design gransknings session, är det geografiska området för Azure-Blob Storage i relation till Azure-fjärrrendering-servern också mindre kritiskt.

Men för 3D-tillgångar som ska användas flera gånger, till exempel i ett scenario med inlärnings användning, rekommenderar vi att du behåller färdiga 3D-tillgångar i Blob Storage i varje regionalt Data Center där du planerar att använda Azure fjärrrendering. Detta kan automatiseras med Azure Storage redundans. CDN används ofta även för detta ändamål, men det är ännu inget alternativ för Azure-fjärrrendering.

Mer information:

* [Delade upplevelser i Mixad verklighet](https://docs.microsoft.com/windows/mixed-reality/shared-experiences-in-mixed-reality)
* [Azure Storage redundans](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

## <a name="managing-model-access"></a>Hantera modell åtkomst

Genom att använda Azures fjärrrendering måste du noggrant tänka på den heltäckande infrastrukturen för att hantera 3D-modeller.

En fördel med att använda Azure fjärrrendering är att stora 3D-tillgångar aldrig behöver överföras direkt till den mixade verkligheten innan de visas.  När en 3D-till gång har laddats upp och konverterats för användning med Azure Remote rendering kan valfritt antal användare dela den enskilda instansen av 3D-modellen.

### <a name="considerations-for-3d-model-access"></a>Överväganden för åtkomst till 3D-modell

Här är några viktiga överväganden när du bestämmer dig för din modell åtkomst strategi.

Utifrån det förväntade användnings fallet bestämmer du den bästa platsen eller kombinationen av platser så att en användare kan välja 3D-tillgångar för visning. Några vanliga alternativ är:

* Direkt inom den blandade verklighets upplevelsen
* Via en medföljande webb Portal
* I ett tillbehörs skriv bord eller mobil program

Om ditt användnings mönster har användnings mönster där samma 3D-till gång kan överföras flera gånger, kommer backend att spåra vilka modeller som redan har konverterats för användning med ARR, så att en modell bara bearbetas i förväg en gång för flera framtida val. Ett exempel på design granskning är där ett team har åtkomst till en gemensam ursprunglig 3D-till gång. Varje grupp medlem förväntas granska modellen med hjälp av ARR vid en viss tidpunkt i arbets flödet. Endast den första vyn utlöser sedan för bearbetnings steget. De efterföljande vyerna skulle slå upp den associerade efter bearbetningen av den associerade filen med SAS-utdata.

Beroende på användnings fallet vill du förmodligen bestämma och eventuellt spara rätt storlek, *standard* eller *Premium*för Azure Remote rendering-servern för varje 3D-till gång eller grupp av till gångar som ska visas tillsammans i samma session.  

### <a name="on-device-model-selection-list"></a>Val lista för enhets modell

I många användnings fall, till exempel utbildning, uppgifts vägledning eller marknadsförings program, kan det vara relativt statiskt att använda de 3D-tillgångar som ska visas i Azures fjärrrendering. I dessa fall kan en granskad uppsättning 3D-till gångar förkonverteras och göras tillgänglig via en databas som innehåller nödvändig information för att fylla i en urvals lista med granskade till gångar. Dessa data kan sedan hämtas från programmet för Mixad verklighet för att fylla i en val meny.

Detta kan ske ytterligare genom att även erbjuda ett sätt att ladda upp privata 3D-tillgångar, unikt för varje enskild person eller grupp. Listan med privata till gångar kan sedan kombineras med listan över vanliga, granskade till gångar i användar upplevelsen för att välja 3D-tillgångar att visa.

### <a name="on-device-onedrive-access"></a>OneDrive-åtkomst på enhet

Med tanke på att en OneDrive-filväljning är inbyggd i Microsofts blandade Reality-enheter, kan du välja 3D-till gångar på enhet från OneDrive, särskilt för användnings fall där det är vanligt att läsa in olika eller modifierade 3D-modeller. I det här scenariot väljer användaren en eller flera 3D-tillgångar via OneDrive-filväljaren i ditt blandade Reality-program. 3D-till gångarna migreras sedan till en SAS-behållare, konverteras till en SAS-behållare och kopplas till ARR-sessionen. Vi rekommenderar att programmet Mixed Reality anropar en molnbaserad process för att utföra dessa steg i stället för att flytta alla bitar från OneDrive till enheten och tillbaka till Azure Blob Storage.

Den här metoden kan fattas ett steg ytterligare genom att bevara en koppling mellan 3D-tillgångar som tidigare har visats, vilket innebär att när samma modell väljs igen från OneDrive, kan programmet kringgå konverterings processen och direkt läsa in den associerade 3D-till gången via dess SAS-URI.

Mer information:

* [Microsoft Power automatiserings mall för OneDrive till Azure Storage-replikering](https://flow.microsoft.com/galleries/public/templates/2f90b5d3-029b-4e2e-ad37-1c0fe6d187fe/when-a-file-is-uploaded-to-onedrive-copy-it-to-azure-storage-container/)
* [Översikt över OneDrive File Storage API](https://docs.microsoft.com/graph/onedrive-concept-overview)

### <a name="direct-cad-access"></a>Direkt CAD-åtkomst

Ett övertygande användnings fall för Mixad verklighet är design granskningar av CAD-arbete som pågår. I det här scenariot är den snabbast inläsnings tiden från Skriv bordet till Mixad verklighet nyckel. En idealisk lösning kan innebära att utveckla plugin-program för vissa CAD-program. Dessa plugin-program hanterar direkt varje aspekt av belastnings-, konverterings-och visnings processen:

* Ange ett UX för att:
  * Länka CAD-programmet till en specifik Mixad verklighet-enhet (en gång).
  * Begär att den valda geometrin ska visas på den blandade Reality-enheten.
* Om den inte redan körs, kan du sätta igång Azure-fjärrrenderingen så att den kan bearbeta parallellt samtidigt som du laddar upp och konverterar CAD-filen
* Normalisera CAD-geometriska data till något av de format som stöds av Azure Remote rendering
* Överför normaliserade data direkt till Azure Blob Storage inmatnings behållare
* Initiera modell konverterings processen
* Länka modellens SAS-URI för utdata-behållare till Azures session för fjärrrendering
* Meddela det kopplade mixade programmets Reality-program att modellen är tillgänglig och klar för att visa och tillhandahålla SAS-URI: n för utdata, så att programmet kan ansluta den till sessionen.

Ett mycket enklare men något mindre effektiviserat tillvägagångs sätt kan automatisera processen med att spara 3D-modellen på en lokal hård disk och sedan starta en process för att överföra den sparade filen till SAS-datacontainern.

### <a name="azure-marketplace"></a>Azure Marketplace

Många företags klienter bestämmer att din Azure Stack kan distribueras under sina egna Azure-konton och autentiseringsuppgifter av säkerhets skäl. För att göra detta möjligt vill du överväga att paketera ditt Azure-hanterade program så att det kan publiceras på Azure Marketplace som ett Azure Application erbjudande.

Mer information:

* [Azure Marketplace](https://azure.microsoft.com/marketplace/)
* [Självstudie: publicera Azure-hanterade program på Marketplace](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-marketplace-app)

### <a name="security"></a>Säkerhet

Det är viktigt att skapa din heltäckande lösning för Azure-fjärråter givning för säkerhet från grunden. Det finns många aspekter av säkerhet att tänka på vid utformningen av din kompletta lösning, inklusive:

* Autentiseringsstrategier
* Åtkomst hantering – grupper, principer och behörigheter
* Flera innehavare
* Kryptering av data lagring och överföring
* Tillfälliga use-token
* DDoS-attacker (distributed denial of Service)
* Hotidentifiering
* VPN och säkra nätverk
* Brandväggar
* Hantering av certifikat och nyckel för hemliga nycklar
* Sårbarhet och sårbarheter i program

För autentisering är det klokt att flytta så mycket av ARR-autentisering och-sessionshantering till en Azure-webbtjänst som möjligt. Detta leder till en bättre hanterad och säkrare lösning.

Mer information:

* [Azure AD-tjänsteautentisering](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-service-authentication)
* [Förbättra din säkerhets position med Azure](https://azure.microsoft.com/overview/security/)
* [Cloud Security](https://azure.microsoft.com/product-categories/security/)
