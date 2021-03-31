---
title: Ansluta datorer med hjälp av Log Analytics Gateway | Microsoft Docs
description: Anslut dina enheter och Operations Manager-övervakade datorer med hjälp av Log Analytics Gateway för att skicka data till Azure Automation och Log Analytics-tjänsten när de inte har till gång till Internet.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: bae48dc78eb6973e5bce4d535091bc330c4c897f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102509038"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Ansluta datorer utan Internet åtkomst med hjälp av Log Analytics gateway i Azure Monitor

>[!NOTE]
>Som Microsoft Operations Management Suite (OMS) över gångar till Microsoft Azure övervakaren ändras terminologin. Den här artikeln avser OMS-gateway som Azure Log Analytics Gateway. 
>

Den här artikeln beskriver hur du konfigurerar kommunikation med Azure Automation och Azure Monitor med hjälp av Log Analytics gateway när datorer som är direktanslutna eller som övervakas av Operations Manager inte har någon Internet åtkomst. 

Log Analytics Gateway är en HTTP-proxy för vidarebefordran som stöder HTTP-tunnlar med hjälp av HTTP CONNECT-kommandot. Den här gatewayen skickar data till Azure Automation och en Log Analytics arbets yta i Azure Monitor åt datorer som inte kan ansluta direkt till Internet. 

Log Analytics Gateway stöder:

* Rapporterar till samma Log Analytics arbets ytor som kon figurer ATS på varje agent och som är konfigurerade med Azure Automation hybrid Runbook Worker.  
* Windows-datorer där Microsoft Monitoring Agent är direkt ansluten till en Log Analytics arbets yta i Azure Monitor.
* Linux-datorer där en Log Analytics-agent för Linux är direkt ansluten till en Log Analytics arbets yta i Azure Monitor.  
* System Center Operations Manager 2012 SP1 med UR7, Operations Manager 2012 R2 med UR3 eller en hanterings grupp i Operations Manager 2016 eller senare som är integrerad med Log Analytics.  

Vissa IT-säkerhetsprinciper tillåter inte Internet anslutning för nätverks datorer. Dessa frånkopplade datorer kan vara kassa enheter eller servrar som har stöd för IT-tjänster, till exempel. Om du vill ansluta dessa enheter till Azure Automation eller en Log Analytics arbets yta så att du kan hantera och övervaka dem, konfigurerar du dem för att kommunicera direkt med Log Analytics Gateway. Log Analytics Gateway kan ta emot konfigurations information och vidarebefordra data för deras räkning. Om datorerna har kon figurer ATS med Log Analytics agent för att ansluta direkt till en Log Analytics arbets yta, kommunicerar datorerna i stället med Log Analytics Gateway.  

Log Analytics gatewayen överför data från agenterna till tjänsten direkt. Det går inte att analysera data under överföringen och gatewayen cachelagrar inte data när den förlorar anslutningen till tjänsten. När gatewayen inte kan kommunicera med tjänsten fortsätter agenten att köras och köar insamlade data från den övervakade datorns disk. När anslutningen har återställts skickar agenten cachelagrade data som samlas in till Azure Monitor.

När en Operations Manager hanterings grupp är integrerad med Log Analytics kan hanterings servrarna konfigureras för att ansluta till Log Analytics Gateway för att ta emot konfigurations information och skicka insamlade data, beroende på vilken lösning du har aktiverat.  Operations Manager agenter skickar data till hanterings servern. Agenter kan till exempel skicka Operations Manager aviseringar, konfigurations bedömnings data, instans utrymmes data och kapacitets data. Andra data med hög volym, till exempel Internet Information Services (IIS)-loggar, prestanda data och säkerhets händelser, skickas direkt till Log Analytics Gateway. 

Om en eller flera Operations Manager Gateway-servrar distribueras för att övervaka icke-betrodda system i ett perimeternätverk eller ett isolerat nätverk, kan servrarna inte kommunicera med en Log Analytics Gateway.  Operations Manager Gateway-servrar kan endast rapportera till en hanterings Server.  När en Operations Manager hanterings grupp konfigureras för att kommunicera med Log Analytics Gateway, distribueras konfigurations informationen för proxy automatiskt till alla datorer som hanteras med agent som kon figurer ATS för att samla in loggdata för Azure Monitor, även om inställningen är tom.

För att tillhandahålla hög tillgänglighet för direktanslutna eller drift hanterings grupper som kommunicerar med en Log Analytics arbets yta via gatewayen använder du Utjämning av nätverks belastning (NLB) för att dirigera om och distribuera trafik över flera gateway-servrar. På så sätt omdirigeras trafiken till en annan tillgänglig nod om en gateway-server slutar fungera.  

Datorn som kör Log Analytics Gateway kräver att Log Analytics Windows-agenten identifierar de tjänst slut punkter som gatewayen behöver för att kommunicera med. Agenten måste också dirigera gatewayen för att kunna rapportera till samma arbets ytor som agenterna eller Operations Manager hanterings gruppen bakom gatewayen har kon figurer ATS med. Den här konfigurationen gör att gatewayen och agenten kan kommunicera med sin tilldelade arbets yta.

En gateway kan vara multihomed till upp till fyra arbets ytor. Detta är det totala antalet arbets ytor som en Windows-Agent stöder.  

Varje agent måste ha en nätverks anslutning till gatewayen så att agenter automatiskt kan överföra data till och från gatewayen. Undvik att installera gatewayen på en domänkontrollant. Linux-datorer som finns bakom en gateway-server kan inte använda installations metoden för [omslutnings skript](../agents/agent-linux.md#install-the-agent-using-wrapper-script) för att installera Log Analytics agent för Linux. Agenten måste hämtas manuellt, kopieras till datorn och installeras manuellt eftersom gatewayen endast stöder kommunikation med de Azure-tjänster som nämns ovan.

Följande diagram visar data som flödar från direkta agenter, via gatewayen, till Azure Automation och Log Analytics. Agentens proxykonfiguration måste matcha porten som Log Analytics Gateway har kon figurer ATS med.  

![Diagram över direkt agent kommunikation med tjänster](./media/gateway/oms-omsgateway-agentdirectconnect.png)

I följande diagram visas data flödet från en Operations Manager hanterings grupp som Log Analytics.   

![Diagram över Operations Manager kommunikation med Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Konfigurera systemet

Datorer som är avsedda att köra Log Analytics gatewayen måste ha följande konfiguration:

* Windows 10, Windows 8,1 eller Windows 7
* Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 eller Windows Server 2008
* Microsoft .NET Framework 4.5
* Minst en processor på 4 kärnor och 8 GB minne 
* En [Log Analytics agent för Windows](../agents/agent-windows.md) som har kon figurer ATS för att rapportera till samma arbets yta som de agenter som kommunicerar via gatewayen

### <a name="language-availability"></a>Språk tillgänglighet

Log Analytics Gateway finns på följande språk:

- Kinesiska (förenklad)
- Kinesiska (traditionell)
- Tjeckiska
- Nederländska
- Engelska
- Franska
- Tyska
- Ungerska
- Italienska
- Japanska
- Koreanska
- Polska
- Portugisiska (Brasilien)
- Portugisiska (Portugal)
- Ryska
- Spanska (internationell)

### <a name="supported-encryption-protocols"></a>Krypterings protokoll som stöds

Log Analytics Gateway stöder bara Transport Layer Security (TLS) 1,0, 1,1 och 1,2.  Den stöder inte Secure Sockets Layer (SSL).  Konfigurera gatewayen så att den använder minst TLS 1,2 för att säkerställa säkerheten för data som överförs till Log Analytics. Äldre versioner av TLS eller SSL är sårbara. Även om de för närvarande tillåter bakåtkompatibilitet, Undvik att använda dem.  

Mer information finns i [skicka data på ett säkert sätt med TLS 1,2](../logs/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Antal agent anslutningar som stöds

Följande tabell visar ungefär hur många agenter som kan kommunicera med en gateway-server. Support baseras på agenter som laddar upp cirka 200 KB data var 6: e sekund. För varje agent testad är data volymen cirka 2,7 GB per dag.

|Gateway |Agenter som stöds (ungefärligt)|  
|--------|----------------------------------|  
|CPU: Intel Xeon-Processor E5-2660 v3 \@ 2,6 GHz 2 kärnor<br> Minne: 4 GB<br> Nätverks bandbredd: 1 Gbit/s| 600|  
|CPU: Intel Xeon-Processor E5-2660 v3 \@ 2,6 GHz 4 kärnor<br> Minne: 8 GB<br> Nätverks bandbredd: 1 Gbit/s| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Ladda ned Log Analytics Gateway

Hämta den senaste versionen av installations filen för Log Analytics Gateway från antingen Microsoft Download Center ([nedladdnings länk](https://go.microsoft.com/fwlink/?linkid=837444)) eller Azure Portal.

Följ dessa steg om du vill hämta Log Analytics gatewayen från Azure Portal:

1. Bläddra i listan över tjänster och välj sedan **Log Analytics**. 
1. Välj en arbetsyta.
1. I bladet arbets yta väljer du **Snabbstart** under **Allmänt**. 
1. Under **Välj en data källa som ska anslutas till arbets ytan** väljer du **datorer**.
1. På bladet **Direct agent** väljer du **Hämta Log Analytics Gateway**.
 
   ![Skärm bild av stegen för att ladda ned Log Analytics Gateway](./media/gateway/download-gateway.png)

eller 

1. I bladet arbets yta väljer du **Avancerade inställningar** under **Inställningar**.
1. Gå till **anslutna källor**  >  **Windows-servrar** och välj **Ladda ned Log Analytics Gateway**.

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>Installera Log Analytics Gateway med hjälp av installations guiden

Följ dessa steg om du vill installera en gateway med installations guiden. 

1. I målmappen dubbelklickar du på **Log Analytics gateway.msi**.
1. På sidan **Välkommen** klickar du på **Nästa**.

   ![Skärm bild av Välkomst sidan i guiden Gateway-installation](./media/gateway/gateway-wizard01.png)

1. På sidan **licens avtal** väljer **du jag accepterar villkoren i licens avtalet** för att godkänna licens villkoren för program vara från Microsoft och väljer sedan **Nästa**.
1. På sidan **port-och proxyadress** :

   a. Ange det TCP-portnummer som ska användas för gatewayen. Installations programmet använder det här port numret för att konfigurera en regel för inkommande trafik i Windows-brandväggen.  Standardvärdet är 8080.
      Det giltiga intervallet för port numret är 1 till 65535. Ett fel meddelande visas om indatamängden inte hamnar i det här intervallet.

   b. Om den server där gatewayen är installerad måste kommunicera via en proxyserver anger du den proxyadress där gatewayen ska anslutas. Ange till exempel `http://myorgname.corp.contoso.com:80`.  Om du lämnar det här fältet tomt kommer gatewayen att försöka ansluta direkt till Internet.  Om proxyservern kräver autentisering anger du ett användar namn och lösen ord.

   c. Välj **Nästa**.

   ![Skärm bild av konfigurationen för Gateway-proxyn](./media/gateway/gateway-wizard02.png)

1. Om du inte har Microsoft Update aktive rad visas sidan Microsoft Update och du kan välja att aktivera den. Gör ett val och välj sedan **Nästa**. Annars fortsätter du till nästa steg.
1. På sidan **målmapp** lämnar du standardmappen C:\Program Files\OMS gateway eller anger den plats där du vill installera gatewayen. Välj sedan **Nästa**.
1. På sidan **klar att installera** väljer du **Installera**. Välj **Ja** om User Account Control begär behörighet att installera.
1. När installationen är klar väljer du **Slutför**. Verifiera att tjänsten körs genom att öppna snapin-modulen Services. msc och kontrol lera att OMS- **gatewayen** visas i listan över tjänster och att dess status är **igång**.

   ![Skärm bild av lokala tjänster som visar att OMS-gatewayen körs](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>Installera Log Analytics Gateway med hjälp av kommando raden

Den hämtade filen för gatewayen är ett Windows Installer-paket som stöder tyst installation från kommando raden eller någon annan automatiserad metod. Om du inte är bekant med standard kommando rads alternativen för Windows Installer, se [kommando rads alternativ](/windows/desktop/msi/command-line-options).
 
I följande tabell beskrivs de parametrar som stöds av installations programmet.

|Parametrar| Kommentarer|
|----------|------| 
|Port | TCP-portnummer för gateway att lyssna på |
|PROGRAMPROXYFILEN | IP-adress för proxyserver |
|INSTALLDIR | Fullständigt kvalificerad sökväg för att ange installations katalog för gateway-programfiler |
|USERNAME | Användar-ID för autentisering med proxyserver |
|ORDS | Lösen ord för användar-ID för autentisering med proxy |
|LicenseAccepted | Ange värdet **1** för att kontrol lera att du accepterar licens avtalet |
|HASAUTH | Ange värdet **1** när PARAMETRARNA för användar namn/lösen ord anges |
|HASPROXY | Ange värdet **1** när du anger en IP-adress för **proxy** -parametern |

Om du vill installera gatewayen i tyst läge och konfigurera den med en angiven proxyadress, port nummer, skriver du följande:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

Om du använder kommando rads alternativet/Qn döljer installations programmet,/QB, visar installations programmet vid tyst installation.  

Om du behöver ange autentiseringsuppgifter för att autentisera med proxyservern skriver du följande:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

Efter installationen kan du bekräfta att inställningarna godkänns (exklusive användar namn och lösen ord) med följande PowerShell-cmdletar:

- **Get-OMSGatewayConfig** – returnerar TCP-porten som gatewayen är konfigurerad för att lyssna på.
- **Get-OMSGatewayRelayProxy** – returnerar IP-adressen för den proxyserver som du konfigurerade den för att kommunicera med.

## <a name="configure-network-load-balancing"></a>Konfigurera utjämning av nätverks belastning

Du kan konfigurera gatewayen för hög tillgänglighet med utjämning av nätverks belastning (NLB) med antingen Microsoft [NLB (utjämning av nätverks belastning)](/windows-server/networking/technologies/network-load-balancing), [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)eller maskinvarubaserad belastnings utjämning. Belastningsutjämnaren hanterar trafik genom att omdirigera de begärda anslutningarna från Log Analytics agenter eller Operations Manager hanterings servrar över noderna. Om en gateway-server kraschar omdirigeras trafiken till andra noder.

### <a name="microsoft-network-load-balancing"></a>Microsoft NLB (utjämning av nätverks belastning)

Information om hur du utformar och distribuerar ett kluster för Utjämning av nätverks belastning i Windows Server 2016 finns i [utjämning av nätverks belastning](/windows-server/networking/technologies/network-load-balancing). Följande steg beskriver hur du konfigurerar ett kluster för Utjämning av nätverks belastning i Microsoft.  

1. Logga in på Windows Server som är medlem i NLB-klustret med ett administratörs konto.  
2. Öppna hanteraren för Utjämning av nätverks belastning i Serverhanteraren, klicka på **verktyg** och klicka sedan på **hanteraren för Utjämning av nätverks belastning**.
3. Om du vill ansluta en Log Analytics Gateway-server med Microsoft Monitoring Agent installerad högerklickar du på klustrets IP-adress och klickar sedan på **Lägg till värd i kluster**. 

    ![Hanteraren för Utjämning av nätverks belastning – Lägg till värd i kluster](./media/gateway/nlb02.png)
 
4. Ange IP-adressen för den gateway-server som du vill ansluta. 

    ![Hanteraren för Utjämning av nätverks belastning – Lägg till värd i kluster: Anslut](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer

Information om hur du utformar och distribuerar en Azure Load Balancer finns i [Vad är Azure Load Balancer?](../../load-balancer/load-balancer-overview.md). Om du vill distribuera en grundläggande belastningsutjämnare följer du stegen som beskrivs i den här [snabb](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) starten, med undantag för de steg som beskrivs i avsnittet **skapa backend-servrar**.   

> [!NOTE]
> Om du konfigurerar Azure Load Balancer med hjälp av **Basic SKU**, krävs det att virtuella Azure-datorer tillhör en tillgänglighets uppsättning. Mer information om tillgänglighets uppsättningar finns i [Hantera tillgängligheten för virtuella Windows-datorer i Azure](../../virtual-machines/availability.md). Om du vill lägga till befintliga virtuella datorer i en tillgänglighets uppsättning läser du [ange Azure Resource Manager tillgänglighets uppsättning för virtuella](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)datorer.
> 

När belastningsutjämnaren har skapats måste en backend-pool skapas, som distribuerar trafik till en eller flera gateway-servrar. Följ stegen som beskrivs i artikeln snabb starts artikel [Skapa resurser för belastningsutjämnaren](../../load-balancer/quickstart-load-balancer-standard-public-portal.md).  

>[!NOTE]
>När du konfigurerar hälso avsökningen bör den konfigureras att använda TCP-porten för Gateway-servern. Hälso avsökningen lägger till eller tar bort gateway-servrarna dynamiskt från belastnings Utjämnings rotationen baserat på deras svar på hälso kontroller. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Konfigurera Log Analytics agent och Operations Manager hanterings grupp

I det här avsnittet får du se hur du konfigurerar direkt anslutna Log Analytics agenter, en Operations Manager hanterings grupp eller Azure Automation hybrid Runbook Worker med Log Analytics Gateway för att kommunicera med Azure Automation eller Log Analytics.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Konfigurera en fristående Log Analytics agent

När du konfigurerar Log Analytics agent ersätter du värdet för proxyservern med IP-adressen för Log Analytics Gateway-servern och dess port nummer. Om du har distribuerat flera gateway-servrar bakom en belastningsutjämnare är konfigurationen av Log Analytics agent-proxy den virtuella IP-adressen för belastningsutjämnaren.  

>[!NOTE]
>Information om hur du installerar Log Analytics-agenten på gateway-och Windows-datorer som ansluter direkt till Log Analytics finns i [ansluta Windows-datorer till Log Analytics-tjänsten i Azure](../agents/agent-windows.md). Information om hur du ansluter Linux-datorer finns i [Anslut Linux-datorer till Azure Monitor](../agents/agent-linux.md). 
>

När du har installerat agenten på Gateway-servern konfigurerar du den så att den rapporterar till arbets ytan eller arbets ytans agenter som kommunicerar med gatewayen. Om Log Analytics Windows-agenten inte är installerad på gatewayen skrivs händelse 300 till händelse loggen för OMS Gateway, vilket anger att agenten måste installeras. Om agenten är installerad men inte konfigurerad för att rapportera till samma arbets yta som de agenter som kommunicerar genom den, skrivs händelse 105 till samma logg, vilket anger att agenten på gatewayen måste konfigureras att rapportera till samma arbets yta som de agenter som kommunicerar med gatewayen.

När du har slutfört konfigurationen startar du om **OMS Gateway** -tjänsten för att tillämpa ändringarna. Annars avvisar gatewayen agenter som försöker kommunicera med Log Analytics och kommer att rapportera händelse 105 i händelse loggen för OMS Gateway. Detta sker även när du lägger till eller tar bort en arbets yta från agent konfigurationen på Gateway-servern.

Information som rör Automation-Hybrid Runbook Worker finns i [automatisera resurser i ditt data Center eller i molnet med hjälp av hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Konfigurera Operations Manager, där alla agenter använder samma proxyserver

Operations Manager proxy-konfigurationen tillämpas automatiskt på alla agenter som rapporterar till Operations Manager, även om inställningen är tom.  

Om du vill använda OMS-Gateway för att stödja Operations Manager måste du ha:

* Microsoft Monitoring Agent (version 8.0.10900.0 eller senare) har installerats på OMS Gateway-servern och kon figurer ATS med samma Log Analytics arbets ytor som hanterings gruppen har kon figurer ATS för att rapportera till.
* Internet anslutning. Alternativt måste OMS-Gateway vara ansluten till en proxyserver som är ansluten till Internet.

> [!NOTE]
> Om du inte anger något värde för gatewayen, skickas tomma värden till alla agenter.
>

Om din Operations Manager hanterings grupp registreras med en Log Analytics arbets yta för första gången visas inte alternativet för att ange proxykonfigurationen för hanterings gruppen i drift konsolen. Det här alternativet är bara tillgängligt om hanterings gruppen har registrerats med tjänsten.  

Om du vill konfigurera en integrering uppdaterar du konfigurationen av systemets proxy med hjälp av netsh på det system där du kör drift konsolen och på alla hanterings servrar i hanterings gruppen. Följ de här stegen:

1. Öppna en upphöjd kommando tolk:

   a. Välj **Start** och ange **cmd**.  

   b. Högerklicka på **kommando tolken** och välj **Kör som administratör**.  

1. Ange följande kommando:

   `netsh winhttp set proxy <proxy>:<port>`

När du har slutfört integrationen med Log Analytics tar du bort ändringen genom att köra `netsh winhttp reset proxy` . I drift konsolen använder du sedan alternativet **Konfigurera proxyserver** för att ange Log Analytics Gateway-server. 

1. Välj **anslutning** under **Operations Management Suite** i Operations Manager-konsolen och välj sedan **Konfigurera proxyserver**.

   ![Skärm bild av Operations Manager, som visar valet konfigurera proxyserver](./media/gateway/scom01.png)

1. Välj **Använd en proxyserver för att få åtkomst till Operations Management Suite** och ange sedan IP-adressen för den Log Analytics Gateway-servern eller den virtuella IP-adressen för belastningsutjämnaren. Var noga med att börja med prefixet `http://` .

   ![Skärm bild av Operations Manager, som visar proxyserverns adress](./media/gateway/scom02.png)

1. Välj **Slutför**. Din Operations Manager hanterings grupp har nu kon figurer ATS för att kommunicera via Gateway-servern till Log Analyticss tjänsten.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Konfigurera Operations Manager, där vissa agenter använder en proxyserver

För stora och komplexa miljöer kanske du bara vill att vissa servrar (eller grupper) ska använda Log Analytics Gateway-servern.  För dessa servrar kan du inte uppdatera Operations Manager agenten direkt, eftersom det här värdet skrivs över av hanterings gruppens globala värde.  Åsidosätt i stället regeln som används för att skicka dessa värden.  

> [!NOTE] 
> Använd den här konfigurations metoden om du vill tillåta flera Log Analytics Gateway-servrar i din miljö.  Du kan till exempel kräva att specifika Log Analytics Gateway-servrar anges på regional basis.
>

Konfigurera vissa servrar eller grupper så att de använder Log Analytics Gateway-servern: 

1. Öppna Operations Manager-konsolen och välj arbets ytan **redigering** .  
1. I arbets ytan redigering väljer du **regler**. 
1. I verktygsfältet Operations Manager väljer du knappen **omfattning** . Om den här knappen inte är tillgänglig kontrollerar du att du har valt ett objekt, inte en mapp, i fönstret **övervakning** . Dialog rutan **omfattnings hanterings paket objekt** visar en lista över vanliga riktade klasser, grupper eller objekt. 
1. I fältet **Sök efter** anger du **Hälsotjänst** och väljer den i listan. Välj **OK**.  
1. Sök efter **inställnings regel för Advisor**. 
1. I verktygsfältet Operations Manager väljer du **åsidosättningar** och pekar sedan på **Åsidosätt det Rule\For ett särskilt objekt av klassen: Hälsotjänst** och väljer ett objekt i listan.  Eller skapa en anpassad grupp som innehåller hälso tjänst objekt för de servrar som du vill tillämpa åsidosättningen på. Tillämpa sedan åsidosättningen på den anpassade gruppen.
1. I dialog rutan **Egenskaper för åsidosättning** lägger du till en bock i kolumnen **Åsidosätt** bredvid parametern **WebProxyAddress** .  I fältet **Åsidosätt värde** anger du URL: en för Log Analytics Gateway-servern. Var noga med att börja med prefixet `http://` .  

    >[!NOTE]
    > Du behöver inte aktivera regeln. Den hanteras redan automatiskt med en åsidosättning i Microsoft System Center Advisor Secure referens override Management Pack som är riktad mot Microsoft System Center Advisor Monitoring Server Group.
    > 

1. Välj ett hanterings paket i listan **Välj mål hanterings paket** eller skapa ett nytt oförseglat hanterings paket genom att välja **nytt**. 
1. Välj **OK** när du är klar. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Konfigurera för Automation hybrid Runbook Worker

Om du har Automation hybrid Runbook Worker i din miljö följer du dessa steg för att konfigurera gatewayen så att den stöder arbets tagarna.

Se avsnittet [Konfigurera ditt nätverk](../../automation/automation-hybrid-runbook-worker.md#network-planning) i Automation-dokumentationen för att hitta URL: en för varje region.

Om datorn är registrerad som en Hybrid Runbook Worker automatiskt, till exempel om Uppdateringshantering lösning är aktive rad för en eller flera virtuella datorer, följer du dessa steg:

1. Lägg till URL: erna för jobb körnings data tjänsten i listan över tillåtna värdar på Log Analytics Gateway. Exempelvis: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Starta om tjänsten Log Analytics Gateway med hjälp av följande PowerShell-cmdlet: `Restart-Service OMSGatewayService`

Följ dessa steg om datorn är ansluten till Azure Automation med hjälp av Hybrid Runbook Worker registrerings-cmdlet:

1. Lägg till Agent tjänstens registrerings-URL i listan över tillåtna värdar på Log Analytics Gateway. Exempelvis: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Lägg till URL: erna för jobb körnings data tjänsten i listan över tillåtna värdar på Log Analytics Gateway. Exempelvis: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Starta om tjänsten Log Analytics Gateway.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Användbara PowerShell-cmdletar

Du kan använda cmdlets för att slutföra aktiviteterna för att uppdatera konfigurations inställningarna för Log Analytics Gateway. Innan du använder-cmdlets måste du se till att:

1. Installera Log Analytics Gateway (Microsoft Windows Installer).
1. Öppna ett fönster i PowerShell-konsolen.
1. Importera modulen genom att skriva följande kommando: `Import-Module OMSGateway`
1. Om inget fel inträffade i föregående steg har modulen importer ATS och cmdletarna kan användas. Ange `Get-Module OMSGateway`
1. När du har använt cmdlets för att göra ändringar startar du om OMS Gateway-tjänsten.

Ett fel i steg 3 innebär att modulen inte har importer ATS. Felet kan inträffa när PowerShell inte hittar modulen. Du hittar modulen i installations Sök vägen för OMS-Gateway: *C:\Program FILES\MICROSOFT OMS Gateway\PowerShell\OmsGateway*.

| **Cmdlet** | **Parametrar** | **Beskrivning** | **Exempel** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Nyckel |Hämtar konfigurationen för tjänsten |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Nyckel (obligatoriskt) <br> Värde |Ändrar konfigurationen för tjänsten |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Hämtar adressen för relä-proxyn (uppströms) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adress<br> Användarnamn<br> Lösen ord (säker sträng) |Anger adressen (och autentiseringsuppgiften) för relä-proxyn (uppströms) |1. Ange en relä-proxy och autentiseringsuppgifter:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Ange en Relay proxy som inte behöver autentisering: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Rensa proxyinställningar för relä:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Hämtar den för tillfället tillåtna värden (endast lokalt konfigurerade tillåtna värdar, inte automatiskt hämtade tillåtna värdar) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Värd (krävs) |Lägger till värden i listan över tillåtna |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Värd (krävs) |Tar bort värden från listan över tillåtna |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Ämne (krävs) |Lägger till klient certifikat ämne i listan över tillåtna |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Ämne (krävs) |Tar bort klient certifikatets ämne från listan över tillåtna |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Hämtar de för närvarande tillåtna klient certifikats ämnena (endast lokalt konfigurerade tillåtna ämnen, inte automatiskt hämtade tillåtna ämnen) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Felsökning

Om du vill samla in händelser som loggats av gatewayen bör du ha Log Analytics-agenten installerad.

![Skärm bild av Loggbokens listan i Log Analytics Gateway-loggen](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Händelse-ID och beskrivningar för Log Analytics Gateway

I följande tabell visas händelse-ID: n och beskrivningar för Log Analytics Gateway logg händelser.

| **ID** | **Beskrivning** |
| --- | --- |
| 400 |Alla program fel som inte har något angivet ID. |
| 401 |Felaktig konfiguration. Till exempel List = "text" i stället för ett heltal. |
| 402 |Undantag vid parsning av TLS-handskakning-meddelanden. |
| 403 |Nätverks fel. Kan till exempel inte ansluta till mål servern. |
| 100 |Allmän information. |
| 101 |Tjänsten har startats. |
| 102 |Tjänsten har stoppats. |
| 103 |Ett HTTP CONNECT-kommando togs emot från klienten. |
| 104 |Inte ett HTTP CONNECT-kommando. |
| 105 |Mål servern är inte i listan över tillåtna eller också är mål porten inte säker (443). <br> <br> Se till att MMA-agenten på OMS-Gateway-servern och de agenter som kommunicerar med OMS-Gateway är anslutna till samma Log Analytics-arbetsyta. |
| 105 |FEL TcpConnection – ogiltigt klient certifikat: CN = Gateway. <br><br> Se till att du använder version 1.0.395.0 eller senare av OMS-Gateway. Se också till att MMA-agenten på din OMS Gateway-server och de agenter som kommunicerar med OMS-Gateway är anslutna till samma Log Analytics-arbetsyta. |
| 106 |TLS/SSL-protokollets version stöds inte.<br><br> Log Analytics Gateway stöder endast TLS 1,0, TLS 1,1 och 1,2. Den stöder inte SSL.|
| 107 |TLS-sessionen har verifierats. |

### <a name="performance-counters-to-collect"></a>Prestanda räknare som ska samlas in

I följande tabell visas de prestanda räknare som är tillgängliga för Log Analytics Gateway. Använd prestanda övervakaren för att lägga till räknarna.

| **Namn** | **Beskrivning** |
| --- | --- |
| Log Analytics Gateway/aktiv klient anslutning |Antal aktiva klient nätverks anslutningar (TCP) |
| Log Analytics Gateway/fel antal |Antal fel |
| Log Analytics Gateway/ansluten klient |Antal anslutna klienter |
| Antal Log Analytics Gateway/avvisande |Antalet avslag på grund av eventuella TLS-verifierings fel |

![Skärm bild av gränssnittet för Log Analytics Gateway, med prestanda räknare](./media/gateway/counters.png)

## <a name="assistance"></a>Hjälp

När du är inloggad på Azure Portal kan du få hjälp med Log Analytics gateway eller någon annan Azure-tjänst eller-funktion.
Om du vill ha hjälp väljer du frågetecknet i det övre högra hörnet i portalen och väljer **ny supportbegäran**. Fyll sedan i det nya formuläret för supportbegäran.

![Skärm bild av en ny supportbegäran](./media/gateway/support.png)

## <a name="next-steps"></a>Nästa steg

[Lägg till data källor](./../agents/agent-data-sources.md) för att samla in data från anslutna källor och lagra data i Log Analytics-arbetsytan.