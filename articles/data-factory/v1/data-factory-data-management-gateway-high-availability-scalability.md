---
title: Hög tillgänglighet med data Management Gateway i Azure Data Factory
description: Den här artikeln förklarar hur du kan skala ut en gateway för data hantering genom att lägga till fler noder och skala upp genom att öka antalet samtidiga jobb som kan köras på en nod.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: ad34ed14682d729157f45e67eb3e0d3bb3eb39b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100391736"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Data Management Gateway – hög tillgänglighet och skalbarhet (för hands version)
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory går du till [integration runtime med egen värd i](../create-self-hosted-integration-runtime.md). 


Den här artikeln hjälper dig att konfigurera hög tillgänglighets-och skalbarhets lösning med Data Management Gateway/integrering.    

> [!NOTE]
> Den här artikeln förutsätter att du redan är bekant med grunderna i Integration Runtime (tidigare Data Management Gateway). Om du inte gör det, se [Data Management Gateway](data-factory-data-management-gateway.md).
> 
> **Den här förhands gransknings funktionen stöds officiellt på Data Management Gateway version 2.12. xxxx. x och senare**. Kontrol lera att du använder version 2.12. xxxx. x eller senare. Hämta den senaste versionen av Data Management Gateway [här](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Översikt
Du kan associera data management gatewayer som är installerade på flera lokala datorer med en enda logisk Gateway från portalen. De här datorerna kallas **noder**. Du kan ha upp till **fyra noder** kopplade till en logisk Gateway. Fördelarna med att ha flera noder (lokala datorer med Gateway installerad) för en logisk Gateway är:  

- Förbättra prestanda för data förflyttning mellan lokala och molnbaserade data lager.  
- Om en av noderna kraschar av någon anledning är det fortfarande andra noder som är tillgängliga för att flytta data. 
- Om en av noderna måste kopplas från för underhåll är andra noder fortfarande tillgängliga för att flytta data.

Du kan också konfigurera antalet **samtidiga data flyttnings jobb** som kan köras på en nod för att skala upp möjligheten att flytta data mellan lokala och molnbaserade data lager. 

Med hjälp av Azure Portal kan du övervaka status för dessa noder, vilket hjälper dig att bestämma om du vill lägga till eller ta bort en nod från den logiska gatewayen. 

## <a name="architecture"></a>Arkitektur 
Följande diagram innehåller en översikt över funktionerna för skalbarhet och tillgänglighet i Data Management Gateway: 

![Data Management Gateway – hög tillgänglighet och skalbarhet](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

En **logisk Gateway** är den gateway som du lägger till i en data fabrik i Azure Portal. Tidigare kunde du bara associera en lokal Windows-dator med Data Management Gateway installerat med en logisk Gateway. Den här lokala gateway-datorn kallas för en nod. Nu kan du koppla upp till **fyra fysiska noder** med en logisk Gateway. En logisk Gateway med flera noder kallas för en **Gateway för flera noder**.  

Alla de här noderna är **aktiva**. De kan bearbeta data förflyttnings jobb för att flytta data mellan lokala och molnbaserade data lager. En av noderna fungerar som både dispatcher och Worker. Andra noder i grupperna är arbetsnoder. En **dispatcher** -nod hämtar uppgifter för data förflyttning/jobb från moln tjänsten och skickar dem till arbetsnoder (inklusive sig själv). En  arbetsnoden kör data förflyttnings jobb för att flytta data mellan lokala och molnbaserade data lager. Alla noder är anställda. Endast en nod kan vara både sändning och arbetare.    

Du kan vanligt vis börja med en nod och **skala ut** för att lägga till fler noder eftersom de befintliga noderna är överbelastade med data flyttnings belastningen. Du kan också **skala upp** kapaciteten för data förflyttning i en gateway-nod genom att öka antalet samtidiga jobb som tillåts att köras på noden. Den här funktionen är även tillgänglig med en gateway med en nod (även om funktionen skalbarhets-och tillgänglighet inte är aktive rad). 

En gateway med flera noder sparar autentiseringsuppgifterna för data lagringen på alla noder. Om det finns ett anslutnings problem för nod-till-nod kan autentiseringsuppgifterna vara osynkroniserade. När du anger autentiseringsuppgifter för ett lokalt data lager som använder en gateway, sparas autentiseringsuppgifter på noden dispatcher/Worker. Dispatcher-noden synkroniseras med andra arbetsnoder. Den här processen kallas **synkronisering av autentiseringsuppgifter**. Kommunikations kanalen mellan noder kan **krypteras** med ett offentligt SSL/TLS-certifikat. 

## <a name="set-up-a-multi-node-gateway"></a>Konfigurera en gateway med flera noder
Det här avsnittet förutsätter att du har gått igenom följande två artiklar eller bekant med koncept i de här artiklarna: 

- [Data Management Gateway](data-factory-data-management-gateway.md) – innehåller en detaljerad översikt över gatewayen.
- [Flytta data mellan lokala och molnbaserade data lager](data-factory-move-data-between-onprem-and-cloud.md) – innehåller en genom gång med stegvisa instruktioner för att använda en gateway med en enda nod.  

> [!NOTE]
> Innan du installerar en gateway för data hantering på en lokal Windows-dator, se krav som anges i [huvud artikeln](data-factory-data-management-gateway.md#prerequisites).

1. I [genom gången](data-factory-move-data-between-onprem-and-cloud.md#create-gateway), när du skapar en logisk Gateway, aktiverar du funktionen för **skalbarhet & hög tillgänglighet** . 

    ![Data Management Gateway – aktivera hög tillgänglighet och skalbarhet](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. På sidan **Konfigurera** använder du länken **Express installation** eller **manuell installation** för att installera en gateway på den första noden (en lokal Windows-dator).

    ![Data Management Gateway-Express eller manuell installation](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Om du använder alternativet för snabb installation görs nod-till-nod-kommunikationen utan kryptering. Nodnamnet är samma som namnet på datorn. Använd manuell installation om nod-nodens kommunikation måste vara krypterad eller om du vill ange ett nodnamn som du själv väljer. Det går inte att redigera nodnamn senare.
3. Om du väljer **Express installation**
    1. Följande meddelande visas när gatewayen har installerats:

        ![Data Management Gateway-Express installationen lyckades](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Starta Datahantering Configuration Manager för gatewayen genom att följa [dessa anvisningar](data-factory-data-management-gateway.md#configuration-manager). Du ser Gateway-namn, nodnamn, status osv.

        ![Skärm bild som visar var du kan se gatewayens namn, nodnamn och status.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Om du väljer **manuell installation**:
    1. Hämta installations paketet från Microsoft Download Center, kör det för att installera gateway på din dator.
    2. Använd **nyckeln Authentication** på sidan **Konfigurera** för att registrera gatewayen.
    
        ![Skärm bild som visar var du använder autentiseringsnyckel.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. På sidan **ny Gateway-nod** kan du ange ett eget **namn** på Gateway-noden. Som standard är nodnamn detsamma som dator namnet.    

        ![Data Management Gateway-ange namn](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. På nästa sida kan du välja om du vill **Aktivera kryptering för nod-till-nod-kommunikation**. Klicka på **hoppa över** för att inaktivera kryptering (standard).

        ![Data Management Gateway-aktivera kryptering](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Ändring av krypterings läge stöds bara när du har en enskild Gateway-nod i den logiska gatewayen. Gör så här om du vill ändra krypterings läget när en gateway har flera noder: ta bort alla noder utom en nod, ändra krypterings läget och Lägg sedan till noderna igen.
        > 
        > I avsnittet [TLS/SSL-certifikat krävs](#tlsssl-certificate-requirements) en lista över kraven för att använda ett TLS/SSL-certifikat. 
    5. När gatewayen har installerats klickar du på Starta Configuration Manager:
    
        ![Manuell installation – starta Configuration Manager](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)     
    6. du ser Data Management Gateway Configuration Manager på noden (lokal Windows-dator) som visar anslutnings status, **Gateway-namn** och **nodnamn**.  

        ![Data Management Gateway-installationen lyckades](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Om du konfigurerar gatewayen på en virtuell Azure-dator kan du använda [den här Azure Resource Manager mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Det här skriptet skapar en logisk Gateway, konfigurerar virtuella datorer med Data Management Gateway program installerat och registrerar dem med den logiska gatewayen. 
6. Starta **Gateway** -sidan i Azure Portal: 
    1. På data fabrikens start sida i portalen klickar du på **länkade tjänster**.
    
        ![Skärm bild som visar panelen länkade tjänster.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. Välj **gatewayen** för att se sidan **Gateway** :
    
        ![Datafabrikens startsida](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Sidan **Gateway** visas:   

        ![Gateway med vyn med en nod](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Klicka på **Lägg till nod** i verktygsfältet för att lägga till en nod i den logiska gatewayen. Om du planerar att använda Express installation gör du det här steget från den lokala datorn som kommer att läggas till som en nod i gatewayen. 

    ![Menyn Data Management Gateway – Lägg till nod](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Stegen påminner om hur du konfigurerar den första noden. Med Configuration Manager användar gränssnittet kan du ange nodnamn om du väljer alternativet Manuell installation: 

    ![Configuration Manager-installera andra gateway](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. När gatewayen har installerats på noden visas följande skärm i Configuration Manager-verktyget:  

    ![Configuration Manager-installation av den andra gatewayen lyckades](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Om du öppnar **Gateway** -sidan i portalen visas två Gateway-noder nu: 

    ![Gateway med två noder i portalen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Om du vill ta bort en gateway-nod klickar du på **ta bort nod** i verktygsfältet, markerar den nod som du vill ta bort och klickar sedan på **ta bort** i verktygsfältet. Den här åtgärden tar bort den markerade noden från gruppen. Observera att den här åtgärden inte avinstallerar data Management Gateway-programvaran från noden (lokal Windows-dator). Använd **Lägg till eller ta bort program** på kontroll panelen på den lokala platsen för att avinstallera gatewayen. När du avinstallerar gatewayen från noden tas den bort automatiskt i portalen.   

## <a name="upgrade-an-existing-gateway"></a>Uppgradera en befintlig gateway
Du kan uppgradera en befintlig gateway om du vill använda funktionen hög tillgänglighet och skalbarhet. Den här funktionen fungerar bara med noder som har data Management Gateway för version >= 2.12. xxxx. Du kan se vilken version av data Management Gateway som är installerad på en dator på fliken **Hjälp** i Data Management Gateway Configuration Manager. 

1. Uppdatera gatewayen på den lokala datorn till den senaste versionen genom att följa genom att hämta och köra ett MSI-installationspaket från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Mer information finns i [installations](data-factory-data-management-gateway.md#installation) avsnittet.  
2. Gå till Azure-portalen. Starta **Data Factory sidan** för din data fabrik. Klicka på panelen länkade tjänster för att starta **sidan länkade tjänster**. Välj den gateway som ska starta **Gateway-sidan**. Klicka och aktivera **förhands gransknings funktionen** som visas i följande bild: 

    ![Data Management Gateway – aktivera förhands gransknings funktion](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. När du har aktiverat funktionen för förhands granskning i portalen stänger du alla sidor. Öppna Gateway- **sidan** igen för att se det nya användar gränssnittet för förhands granskning.
 
    ![Data Management Gateway-aktivera för hands versions funktionen lyckades](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Data Management Gateway – för hands versions gränssnitt](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Under uppgraderingen är namnet på den första noden namnet på datorn. 
3. Lägg nu till en nod. På sidan **Gateway** klickar du på **Lägg till nod**.  

    ![Menyn Data Management Gateway – Lägg till nod](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Följ anvisningarna i föregående avsnitt för att konfigurera noden. 

### <a name="installation-best-practices"></a>Metodtips för installation

- Konfigurera energi schema på värddatorn för gatewayen så att datorn inte försätts i vilo läge. Om värddatorn försätts i vilo läge svarar inte gatewayen på data begär Anden.
- Säkerhetskopiera certifikatet som är associerat med gatewayen.
- Se till att alla noder är av liknande konfiguration (rekommenderas) för bästa prestanda. 
- Lägg till minst två noder för att säkerställa hög tillgänglighet.  

### <a name="tlsssl-certificate-requirements"></a>Krav för TLS/SSL-certifikat
Här följer kraven för TLS/SSL-certifikatet som används för att skydda kommunikationen mellan noderna för integration Runtime:

- Certifikatet måste vara ett offentligt betrott X509 v3-certifikat. Vi rekommenderar att du använder certifikat som utfärdats av en offentlig (tredjeparts) certifikat utfärdare (CA).
- Varje nod för integration runtime måste ha förtroende för det här certifikatet, samt klient datorn som kör Credential Manager-programmet. 
  > [!NOTE]
  > Autentiseringshanteraren används när du anger autentiseringsuppgifter på ett säkert sätt från guiden Kopiera i guiden/Azure Portal. Detta kan startas från valfri dator i samma nätverk som det lokala/privata data lagret.
- Jokertecken stöds. Om ditt FQDN-namn är **node1.domain.contoso.com** kan du använda **_. domain.contoso.com_* som certifikatets ämnes namn.
- SAN-certifikat rekommenderas inte eftersom endast det sista objektet i alternativa namn för certifikat mottagare används och alla andra kommer att ignoreras på grund av den aktuella begränsningen. T.ex. du har ett SAN-certifikat vars SAN är **node1.domain.contoso.com** och **node2.domain.contoso.com**. du kan bara använda det här certifikatet på den dator vars FQDN är **node2.domain.contoso.com**.
- Stöder alla nyckel storlekar som stöds av Windows Server 2012 R2 för TLS/SSL-certifikat.
- Certifikat som använder CNG-nycklar stöds inte.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>Vanliga frågor och svar: när skulle jag inte aktivera den här krypteringen?
Om du aktiverar kryptering kan du lägga till viss kostnad i din infrastruktur (ägandet offentligt certifikat), så du kan hoppa över aktiveringen av kryptering i följande fall:
- När integration runtime körs i ett betrott nätverk, eller ett nätverk med transparent kryptering som IP/s. Eftersom kanal kommunikationen bara är begränsad i det betrodda nätverket kanske du inte behöver ytterligare kryptering.
- När integration runtime inte körs i en produktions miljö. Detta kan hjälpa till att minska kostnaderna för TLS/SSL-certifikatet.


## <a name="monitor-a-multi-node-gateway"></a>Övervaka en gateway med flera noder
### <a name="multi-node-gateway-monitoring"></a>Övervakning av Gateway med flera noder
I Azure Portal kan du Visa en ögonblicks bild av resursutnyttjande i real tid (CPU, minne, nätverk (in/ut) osv.) på varje nod tillsammans med status för gateway-noder. 

![Data Management Gateway-övervakning av flera noder](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Du kan aktivera **Avancerade inställningar** på sidan **Gateway** om du vill se avancerade mått som **nätverk**(in/ut), **roll & status för autentiseringsuppgifter**, vilket är till hjälp vid fel sökning av Gateway-problem och **samtidiga jobb** (som körs/begränsas) som kan ändras/ändras enligt detta under prestanda justering. Följande tabell innehåller beskrivningar av kolumner i listan **Gateway-noder** :  

Övervaknings egenskap | Beskrivning
:------------------ | :---------- 
Name | Namnet på den logiska gatewayen och noder som är associerade med gatewayen.  
Status | Status för den logiska gatewayen och gateway-noderna. Exempel: online/offline/begränsat/osv. Information om dessa statusar finns i avsnittet om [Gateway-status](#gateway-status) . 
Version | Visar versionen för den logiska gatewayen och varje gateway-nod. Versionen av den logiska gatewayen fastställs baserat på den version av majoriteten av noderna i gruppen. Om det finns noder med olika versioner i installations programmet för den logiska gatewayen fungerar bara noderna med samma versions nummer som den logiska gatewayen. Andra är i begränsat läge och måste uppdateras manuellt (endast om automatisk uppdatering Miss lyckas). 
Tillgängligt minne | Tillgängligt minne på en gateway-nod. Det här värdet är en nära real tids ögonblicks bild. 
CPU-användning | CPU-användning för en gateway-nod. Det här värdet är en nära real tids ögonblicks bild. 
Nätverk (in/ut) | Nätverks användning för en gateway-nod. Det här värdet är en nära real tids ögonblicks bild. 
Samtidiga jobb (som körs/begränsas) | Antal jobb eller aktiviteter som körs på varje nod. Det här värdet är en nära real tids ögonblicks bild. Gränsen visar maximalt antal samtidiga jobb för varje nod. Det här värdet definieras baserat på datorns storlek. Du kan öka gränsen för att skala upp samtidiga jobb körningar i avancerade scenarier, där processor/minne/nätverk används, men att aktiviteterna är tids gräns. Den här funktionen är även tillgänglig med en gateway med en nod (även om funktionen skalbarhets-och tillgänglighet inte är aktive rad). Mer information finns i avsnittet om [skalnings överväganden](#scale-considerations) . 
Roll | Det finns två typer av roller – dispatcher och Worker. Alla noder är arbetare, vilket innebär att de kan användas för att köra jobb. Det finns bara en dispatcher-nod som används för att hämta aktiviteter/jobb från moln tjänster och skicka dem till olika arbetsnoder (inklusive sig själv). 

![Data Management Gateway-Avancerad övervakning av flera noder](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Gateway-status

Följande tabell innehåller möjliga status värden för en **Gateway-nod**: 

Status    | Kommentarer/scenarier
:------- | :------------------
Online | Noden är ansluten till Data Factory tjänsten.
Offline | Noden är offline.
Uppgradera | Noden uppdateras automatiskt.
Begränsad | På grund av anslutnings problem. Kan bero på problem med HTTP-port 8050, problem med Service Bus-anslutning eller utfärdande av autentiseringsuppgifter. 
Inaktiv | Noden har en annan konfiguration än konfigurationen av andra majoritets noder.<br/><br/> En nod kan vara inaktiv när den inte kan ansluta till andra noder. 


Följande tabell innehåller möjliga status värden för en **logisk Gateway**. Gateway-statusen beror på status för gateway-noderna. 

Status | Kommentarer
:----- | :-------
Behöver registrering | Ingen nod har ännu registrerats till denna logiska Gateway
Online | Gateway-noderna är online
Offline | Ingen nod i online-status.
Begränsad | Alla noder i denna gateway är inte i felfritt tillstånd. Den här statusen är en varning om att en nod kan vara nere! <br/><br/>Det kan bero på ett problem med synkronisering av autentiseringsuppgifter på dispatcher/Worker-noden. 

### <a name="pipeline-activities-monitoring"></a>Övervakning av pipelines/aktiviteter
Azure Portal ger en pipeline för övervakning med detaljerad information om noder på radnivå. Till exempel visas vilka aktiviteter som kördes på vilken nod. Den här informationen kan vara till hjälp när du ska förstå prestanda problem på en viss nod, t. ex. på grund av nätverks begränsning. 

![Data Management Gateway-övervakning av flera noder för pipelines](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Data Management Gateway-pipeline-information](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Skalnings överväganden

### <a name="scale-out"></a>Skala ut
När det **tillgängliga minnet är lågt** och **CPU-användningen är hög** hjälper det att öka belastningen mellan datorer genom att lägga till en ny nod. Om aktiviteterna inte kan utföras på grund av timeout-eller gateway-noden som är offline, hjälper det om du lägger till en nod i gatewayen.
 
### <a name="scale-up"></a>Skala upp
När det tillgängliga minnet och CPU: n inte används, men den inaktiva kapaciteten är 0, bör du skala upp genom att öka antalet samtidiga jobb som kan köras på en nod. Du kanske också vill skala upp när aktiviteterna är tids gräns eftersom gatewayen är överbelastad. Som du ser i följande bild kan du öka den maximala kapaciteten för en nod. Vi rekommenderar att du dubblerar den för att börja med.  

![Överväganden för Data Management Gateway skala](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Kända problem/bryta ändringar

- För närvarande kan du ha upp till fyra fysiska Gateway-noder för en enda logisk Gateway. Om du behöver fler än fyra noder av prestanda skäl kan du skicka ett e-postmeddelande till [DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com) .
- Du kan inte registrera en gateway-nod igen med en autentiseringsnyckel från en annan logisk Gateway för att växla från den aktuella logiska gatewayen. Om du vill registrera om, avinstallera gatewayen från noden, installera om gatewayen och registrera den med autentiseringsnyckel för den andra logiska gatewayen. 
- Om HTTP-proxyn krävs för alla gateway-noder ställer du in proxyn i diahost.exe.config och diawp.exe.config och använder Server hanteraren för att se till att alla noder har samma diahost.exe.config och diawip.exe.config. Mer information finns i avsnittet [Configure Proxy Settings (Konfigurera proxyinställningar](data-factory-data-management-gateway.md#configure-proxy-server-settings) ). 
- Om du vill ändra krypterings läge för nod-till-nod-kommunikation i Gateway-Configuration Manager tar du bort alla noder i portalen, förutom en. Lägg sedan till noderna igen när du har ändrat krypterings läget.
- Använd ett officiellt TLS-certifikat om du väljer att kryptera kommunikations kanalen för nod-till-nod. Självsignerade certifikat kan orsaka anslutnings problem eftersom samma certifikat inte är betrott i certifikat utfärdarens lista på andra datorer. 
- Du kan inte registrera en gateway-nod på en logisk Gateway om Node-versionen är lägre än den logiska Gateway-versionen. Ta bort alla noder för den logiska gatewayen från portalen så att du kan registrera en nod med lägre version (nedgradera). Om du tar bort alla noder i en logisk Gateway måste du manuellt installera och registrera nya noder i den logiska gatewayen. Express installation stöds inte i det här fallet.
- Du kan inte använda Express installation för att installera noder i en befintlig logisk Gateway, som fortfarande använder autentiseringsuppgifter för molnet. Du kan kontrol lera var autentiseringsuppgifterna lagras från Gateway-Configuration Manager på fliken Inställningar.
- Du kan inte använda Express installation för att installera noder i en befintlig logisk Gateway, som har aktiverat nod-till-nod-kryptering. När du anger att krypterings läget omfattar att lägga till certifikat manuellt, är Express installation inte något mer alternativ. 
- För en fil kopiering från en lokal miljö bör du inte använda \\ localhost eller C:\Files längre eftersom localhost eller den lokala enheten kanske inte är tillgänglig via alla noder. Använd i stället \\ ServerName\files för att ange platsen för filer.


## <a name="rolling-back-from-the-preview"></a>Återställa från förhands granskningen 
Ta bort alla noder, men en nod, för att återställa från förhands granskningen. Det spelar ingen roll vilka noder du tar bort, men se till att du har minst en nod i den logiska gatewayen. Du kan ta bort en nod antingen genom att avinstallera gateway på datorn eller med hjälp av Azure Portal. I Azure Portal på sidan **Data Factory** klickar du på länkade tjänster för att starta sidan **länkade tjänster** . Välj den gateway som ska starta **Gateway** -sidan. På sidan Gateway kan du se de noder som är associerade med gatewayen. På sidan kan du ta bort en nod från gatewayen.
 
När du har tagit bort klickar du på **Förhandsgranska funktioner** på samma Azure Portal sida och inaktiverar för hands versions funktionen. Du har återställt din gateway till en nod GA (allmän tillgänglighet) Gateway.


## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:
- [Data Management Gateway](data-factory-data-management-gateway.md) – innehåller en detaljerad översikt över gatewayen.
- [Flytta data mellan lokala och molnbaserade data lager](data-factory-move-data-between-onprem-and-cloud.md) – innehåller en genom gång med stegvisa instruktioner för att använda en gateway med en enda nod. 
