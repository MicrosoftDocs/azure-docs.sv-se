---
title: Använd Azure Private Link för att ansluta nätverk till Azure Monitor på ett säkert sätt
description: Använd Azure Private Link för att ansluta nätverk till Azure Monitor på ett säkert sätt
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.subservice: ''
ms.openlocfilehash: 5008da99b63cabba41dade9a745fbd5853345737
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734974"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Använd Azure Private Link för att ansluta nätverk till Azure Monitor på ett säkert sätt

Med [Azures privata länk](../../private-link/private-link-overview.md) kan du på ett säkert sätt länka Azure PaaS-tjänster till ditt virtuella nätverk med hjälp av privata slut punkter. För många tjänster skapar du bara en slut punkt per resurs. Azure Monitor är dock en Constellation av olika sammankopplade tjänster som arbetar tillsammans för att övervaka dina arbets belastningar. Därför har vi skapat en resurs som kallas en Azure Monitor Private Link-omfånget (AMPLS) som gör att du kan definiera gränserna för ditt övervaknings nätverk och ansluta till ditt virtuella nätverk. Den här artikeln beskriver när du ska använda och hur du konfigurerar ett Azure Monitor privat länk omfång.

## <a name="advantages"></a>Fördelar

Med privat länk kan du:

- Anslut privat till Azure Monitor utan att öppna någon offentlig nätverks åtkomst
- Se till att dina övervaknings data endast nås via auktoriserade privata nätverk
- Förhindra data exfiltrering från dina privata nätverk genom att definiera vissa Azure Monitor resurser som ansluter via din privata slut punkt
- Anslut ett privat lokalt nätverk till Azure Monitor med ExpressRoute och privat länk
- Behåll all trafik i Microsoft Azure stamnät nätverket

Mer information finns i  [viktiga fördelar med privat länk](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Så här fungerar det

Azure Monitor privat länk omfång är en grupperings resurs för att ansluta en eller flera privata slut punkter (och därmed de virtuella nätverken de finns i) till en eller flera Azure Monitor resurser. Resurserna omfattar Log Analytics arbets ytor och Application Insights komponenter.

![Diagram över resurs sto pol Ogin](./media/private-link-security/private-link-topology-1.png)

> [!NOTE]
> En enskild Azure Monitor resurs kan tillhöra flera AMPLSs, men du kan inte ansluta ett enskilt VNet till fler än en AMPLS. 

## <a name="planning-based-on-your-network"></a>Planera baserat på ditt nätverk

Innan du konfigurerar dina AMPLS-resurser bör du ta hänsyn till kraven på nätverks isolering. Utvärdera dina virtuella nätverks åtkomst till offentligt Internet och åtkomst begränsningarna för var och en av dina Azure Monitor resurser (det vill säga Application Insights komponenter och Log Analytics arbets ytor).

> [!NOTE]
> Nav-eker-nätverk, eller någon annan topologi av peer-baserade nätverk, kan konfigurera en privat länk mellan hubben (huvud-VNet) och relevanta Azure Monitor resurser, i stället för att skapa en privat länk på varje VNet. Detta är särskilt användbart om Azure Monitor resurser som används av dessa nätverk delas. Men om du vill tillåta varje VNet att få åtkomst till en separat uppsättning övervaknings resurser skapar du en privat länk till en dedikerad AMPLS för varje nätverk.

### <a name="evaluate-which-virtual-networks-should-connect-to-a-private-link"></a>Utvärdera vilka virtuella nätverk som ska anslutas till en privat länk

Börja med att utvärdera vilka av dina virtuella nätverk (virtuella nätverk) som har begränsad åtkomst till Internet. Virtuella nätverk som har gratis internet kanske inte behöver en privat länk för att få åtkomst till dina Azure Monitor-resurser. De övervaknings resurser som din virtuella nätverk ansluter till kan begränsa inkommande trafik och kräva en anslutning för privat anslutning (antingen för logg inmatning eller fråga). I sådana fall måste även ett VNet som har åtkomst till det offentliga Internet måste ansluta till dessa resurser via en privat länk och via en AMPLS.

### <a name="evaluate-which-azure-monitor-resources-should-have-a-private-link"></a>Utvärdera vilka Azure Monitor-resurser som har en privat länk

Granska var och en av dina Azure Monitor resurser:

- Bör resursen bara tillåta inmatning av loggar från resurser som finns på vissa virtuella nätverk?
- Ska resursen endast frågas av klienter som finns på vissa virtuella nätverk?

Om svaret på någon av dessa frågor är Ja anger du de begränsningar som beskrivs i [konfigurera Log Analytics](#configure-log-analytics) arbets ytor och [konfigurerar Application Insights komponenter](#configure-application-insights) och kopplar dessa resurser till en eller flera AMPLS. Virtuella nätverk som ska ha åtkomst till dessa övervaknings resurser måste ha en privat slut punkt som ansluter till relevanta AMPLS.
Kom ihåg – du kan ansluta samma arbets ytor eller program till flera AMPLS för att tillåta att de nås av olika nätverk.

### <a name="group-together-monitoring-resources-by-network-accessibility"></a>Gruppera samman övervakning av resurser efter nätverks tillgänglighet

Eftersom varje VNet bara kan ansluta till en AMPLS-resurs, måste du gruppera de övervaknings resurser som ska vara tillgängliga i samma nätverk. Det enklaste sättet att hantera den här grupperingen är att skapa en AMPLS per VNet och välja de resurser som ska anslutas till nätverket. Men för att minska resurserna och förbättra hanterbarheten kanske du vill återanvända en AMPLS över nätverk.

Om dina interna virtuella nätverk till exempel VNet1 och VNet2 ska ansluta till arbets ytorna Workspace1 och Workspace2 och Application Insights komponent Application Insights 3, associerar du alla tre resurserna till samma AMPLS. Om VNet3 endast ska ha åtkomst till Workspace1 skapar du en annan AMPLS-resurs, associerar Workspace1 till den och ansluter VNet3 som visas i följande diagram:

![Diagram över AMPLS en topologi](./media/private-link-security/ampls-topology-a-1.png)

![Diagram över AMPLS B-topologi](./media/private-link-security/ampls-topology-b-1.png)

### <a name="consider-limits"></a>Överväg begränsningar

Det finns ett antal begränsningar som du bör tänka på när du planerar konfigurationen för den privata länken:

* Ett VNet kan bara ansluta till ett AMPLS-objekt. Det innebär att AMPLS-objektet måste ge åtkomst till alla Azure Monitor-resurser som det virtuella nätverket ska ha åtkomst till.
* En Azure Monitor resurs (arbets yta eller Application Insights komponent) kan ansluta till högst 5 AMPLSs.
* Ett AMPLS-objekt kan bara ansluta till 50 Azure Monitor resurser.
* Ett AMPLS-objekt kan endast ansluta till 10 privata slut punkter.

I nedanstående topologi:
* Varje VNet ansluter till endast **ett** AMPLS-objekt.
* AMPLS B är anslutet till privata slut punkter för två virtuella nätverk (VNet2 och VNet3) med 2/10 (20%) av dess möjliga privata slut punkts anslutningar.
* AMPLS A ansluter till två arbets ytor och en komponent för program insikter med 3/50 (6%) de möjliga Azure Monitor resurs anslutningarna.
* Workspace2 ansluter till AMPLS A och AMPLS B med 2/5 (40%) möjliga AMPLS-anslutningar.

![Diagram över AMPLS-gränser](./media/private-link-security/ampls-limits.png)

> [!NOTE]
> I vissa nätverkstopologier (huvudsakligen hubb-ekrar) kan du snabbt komma åt 10 virtuella nätverk-gränsen för en enda AMPLS. I sådana fall rekommenderar vi att du använder en delad privat länk-anslutning i stället för att separera dem. Skapa en enda privat slut punkt i hubb nätverket, länka den till din AMPLS och peer-koppla de relevanta nätverken till hubb nätverket.

![Hubb-och-eker-Single-PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

## <a name="example-connection"></a>Exempel anslutning

Börja med att skapa en Azure Monitor privat länk omfångs resurs.

1. Gå till **skapa en resurs** i Azure Portal och sök efter **Azure Monitor privat länk omfång**.

   ![Sök omfång för Azure Monitor privat länk](./media/private-link-security/ampls-find-1c.png)

2. Klicka på **skapa**.
3. Välj en prenumeration och en resurs grupp.
4. Ge AMPLS ett namn. Det är bäst att använda ett namn som är tydligt vilket syfte och vilken säkerhets gräns som området ska användas för, så att ingen av misstag kan bryta nätverks säkerhets gränser. Till exempel "AppServerProdTelem".
5. Klicka på **Granska + Skapa**. 

   ![Skapa Azure Monitor privat länk omfång](./media/private-link-security/ampls-create-1d.png)

6. Låt validerings passet ta en titt och klicka sedan på **skapa**.

### <a name="connect-azure-monitor-resources"></a>Anslut Azure Monitor resurser

Anslut Azure Monitor resurser (Log Analytics arbets ytor och Application Insights-komponenter) till din AMPLS.

1. I sökområdet Azure Monitor privat länk klickar du på **Azure Monitor resurser** i den vänstra menyn. Klicka på knappen **Lägg till** .
2. Lägg till arbets ytan eller komponenten. Om du klickar på knappen **Lägg till** visas en dialog ruta där du kan välja Azure Monitor resurser. Du kan bläddra igenom dina prenumerationer och resurs grupper, eller så kan du skriva in namnet för att filtrera ned dem. Välj arbets ytan eller komponenten och klicka på **tillämpa** för att lägga till dem i ditt omfång.

    ![Skärm bild av Välj ett omfångs-UX](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Om du tar bort Azure Monitor resurser måste du först koppla bort dem från alla AMPLS-objekt som de är anslutna till. Det går inte att ta bort resurser som är anslutna till en AMPLS.

### <a name="connect-to-a-private-endpoint"></a>Anslut till en privat slut punkt

Nu när du har resurser som är anslutna till din AMPLS skapar du en privat slut punkt för att ansluta vårt nätverk. Du kan göra det här för att utföra den här uppgiften i det [Azure Portal privata länk centret](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)eller inuti Azure Monitor privata länk omfång, som du gjort i det här exemplet.

1. I din omfattnings resurs klickar du på **anslutningar för privata slut punkter** i den vänstra resurs menyn. Klicka på **privat slut punkt** för att starta processen för att skapa slut punkten. Du kan också godkänna anslutningar som startades i det privata länk centret här genom att markera dem och klicka på **Godkänn**.

    ![Skärm bild av UX för privata slut punkts anslutningar](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Välj prenumeration, resurs grupp och namn för slut punkten och regionen som den ska leva i. Regionen måste vara samma region som det virtuella nätverk som du ska ansluta det till.

3. Klicka på **Nästa: resurs**. 

4. På resurs skärmen

   a. Välj den **prenumeration** som innehåller din Azure Monitor privata scope-resurs. 

   b. För **resurs typ** väljer du **Microsoft. Insights/privateLinkScopes**. 

   c. I list rutan **resurs** väljer du det definitions område för privata länkar som du skapade tidigare. 

   d. Klicka på **Nästa: konfiguration >**.
      ![Skärm bild av Välj Skapa privat slut punkt](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. I konfigurations fönstret,

   a.    Välj det **virtuella nätverk** och **undernät** som du vill ansluta till dina Azure Monitor-resurser. 
 
   b.    Välj **Ja** för **integrering med privat DNS-zon** och låt den automatiskt skapa en ny privat DNS zon. De faktiska DNS-zonerna kan skilja sig från vad som visas på skärm bilden nedan. 
   > [!NOTE]
   > Om du väljer **Nej** och hellre vill hantera DNS-poster manuellt, slutför först konfigurationen av din privata länk, inklusive den här privata slut punkten och AMPLS-konfigurationen. Konfigurera sedan DNS enligt anvisningarna i [Konfigurera DNS för den privata Azure-slutpunkten](../../private-link/private-endpoint-dns.md). Se till att du inte skapar tomma poster när du förbereder konfigurationen av den privata länken. De DNS-poster som du skapar kan åsidosätta befintliga inställningar och påverka din anslutning till Azure Monitor.
 
   c.    Klicka på **Granska + skapa**.
 
   d.    Tillåt validerings pass. 
 
   e.    Klicka på **Skapa**. 

    ![Skärm bild av Välj Skapa privat Endpoint2](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Nu har du skapat en ny privat slut punkt som är ansluten till den här Azure Monitor privata länk omfånget.

## <a name="configure-log-analytics"></a>Konfigurera Log Analytics

Gå till Azure-portalen. I din Log Analytics arbets ytans resurs finns ett meny alternativ för **nätverks isolering** på den vänstra sidan. Du kan styra två olika tillstånd från den här menyn.

![LA nätverks isolering](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Anslutna Azure Monitor privata länk omfång
Alla omfattningar som är anslutna till den här arbets ytan visas på den här skärmen. Genom att ansluta till scope (AMPLSs) tillåts nätverks trafik från det virtuella nätverket som är anslutet till varje AMPLS för att uppnå den här arbets ytan. Att skapa en anslutning med här har samma resultat som när du ställer in det i omfånget, som vi gjorde vid [anslutning Azure Monitor resurser](#connect-azure-monitor-resources). Om du vill lägga till en ny anslutning klickar du på **Lägg till** och väljer omfånget Azure Monitor privat länk. Klicka på **Använd** för att ansluta. Observera att en arbets yta kan ansluta till 5 AMPLS-objekt, vilket beskrivs i [ta hänsyn till begränsningar](#consider-limits). 

### <a name="access-from-outside-of-private-links-scopes"></a>Åtkomst från utanför privata länk omfattningar
Inställningarna på den nedre delen av den här sidan styr åtkomsten från offentliga nätverk, vilket innebär att nätverk som inte är anslutna via de omfång som anges ovan. Om du ställer in **Tillåt offentligt nätverks åtkomst för** inmatning till **Nej** kan inte datorer utanför de anslutna omfattningarna Ladda upp data till den här arbets ytan. Om du ställer in **Tillåt offentlig nätverks åtkomst för frågor** till **Nej**, kommer datorer utanför omfattningarna inte att kunna komma åt data i den här arbets ytan, vilket innebär att det inte går att fråga efter data i arbets ytan. Innehåller frågor i arbets böcker, instrument paneler, API-baserade klient upplevelser, insikter i Azure Portal och mycket annat. Upplevelser som körs utanför Azure Portal och som frågar Log Analytics data måste också köras i det privata, länkade VNET.

### <a name="exceptions"></a>Undantag
Att begränsa åtkomsten enligt beskrivningen ovan gäller inte för Azure Resource Manager och har därför följande begränsningar:
* Åtkomst till data – samtidigt som du blockerar/tillåter frågor från offentliga nätverk gäller de flesta Log Analyticss upplevelser, men vissa upplevelser frågar data via Azure Resource Manager och kommer därför inte att kunna fråga efter data, om inte inställningarna för privata länkar tillämpas på Resource Manager också (funktionen kommer snart snart). Detta omfattar exempelvis Azure Monitor lösningar, arbets böcker och insikter och LogicApp-anslutningen.
* Arbets ytans hantering – inställning av arbets yta och konfigurations ändringar (inklusive aktivering av de här åtkomst inställningarna på eller av) hanteras av Azure Resource Manager. Begränsa åtkomsten till hantering av arbets ytor med lämpliga roller, behörigheter, nätverks kontroller och granskning. Mer information finns i [Azure Monitor roller, behörigheter och säkerhet](roles-permissions-security.md).

> [!NOTE]
> Loggar och mått som överförs till en arbets yta via [diagnostikinställningar](diagnostic-settings.md) går via en säker privat Microsoft-kanal och styrs inte av de här inställningarna.

### <a name="log-analytics-solution-packs-download"></a>Hämta Log Analytics lösnings paket

Om du vill tillåta att Log Analytics agent laddar ned lösnings paket lägger du till rätt fullständigt kvalificerade domän namn i listan över tillåtna domän namn för brand väggen. 


| Moln miljö | Agentresurs | Portar | Riktning |
|:--|:--|:--|:--|
|Azure, offentlig     | scadvisorcontent.blob.core.windows.net         | 443 | Utgående
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Utgående
|Azure Kina 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Utgående

## <a name="configure-application-insights"></a>Konfigurera Application Insights

Gå till Azure-portalen. I din Azure Monitor Application Insights komponent resurs ett meny objekts **nätverks isolering** på den vänstra sidan. Du kan styra två olika tillstånd från den här menyn.

![AI-nätverks isolering](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Först kan du ansluta den här Application Insights resursen till Azure Monitor de privata länk omfattningar som du har åtkomst till. Klicka på **Lägg till** och välj **omfånget Azure Monitor privat länk**. Klicka på Använd för att ansluta. Alla anslutna omfattningar visas på den här skärmen. Genom att göra den här anslutningen kan nätverks trafiken i de anslutna virtuella nätverken komma åt den här komponenten. Att göra anslutningen har samma resultat som att ansluta den från omfånget som vi gjorde när vi [anslöt Azure Monitor-resurser](#connect-azure-monitor-resources). 

För det andra kan du styra hur den här resursen kan nås utanför de privata länk omfattningar som anges ovan. Om du ställer in **Tillåt offentligt nätverks åtkomst för** inmatning till **Nej** kan inte datorer eller SDK: er utanför de anslutna omfattningarna Ladda upp data till den här komponenten. Om du ställer in **Tillåt offentlig nätverks åtkomst för frågor** till **Nej**, kommer datorer utanför omfattningarna inte att kunna komma åt data i den här Application Insights resursen. Dessa data omfattar till gång till APM-loggar, Mät värden och Live Metrics-dataströmmen, samt erfarenheter som bygger på de flesta arbets böcker, instrument paneler, API-baserade klient upplevelser, insikter i Azure Portal och mycket mer. 

Observera att användnings upplevelser som inte är Portal måste köras i det privata, länkade VNET som innehåller de övervakade arbets belastningarna. 

Du måste lägga till resurser som är värdar för de övervakade arbets belastningarna till den privata länken. Här är en [dokumentation](../../app-service/networking/private-endpoint.md) om hur du gör detta för app Services.

Att begränsa åtkomsten på det här sättet gäller endast för data i Application Insights-resursen. Konfigurations ändringar, inklusive aktivering av de här åtkomst inställningarna på eller av, hanteras av Azure Resource Manager. Begränsa i stället åtkomst till Resource Manager med lämpliga roller, behörigheter, nätverks kontroller och granskning. Mer information finns i [Azure Monitor roller, behörigheter och säkerhet](roles-permissions-security.md).

> [!NOTE]
> För att helt skydda arbets ytans baserade Application Insights måste du låsa både åtkomst till Application Insights resurs och den underliggande Log Analytics arbets ytan.
>
> Kod på kod nivå (profilerings-/fel sökning) behöver du ange ditt eget lagrings konto för att stödja privat länk. Här är en [dokumentation](../app/profiler-bring-your-own-storage.md) om hur du gör detta.

## <a name="use-apis-and-command-line"></a>Använda API: er och kommando rad

Du kan automatisera processen som beskrivs tidigare med Azure Resource Manager mallar, REST-och kommando rads gränssnitt.

Om du vill skapa och hantera privata länk omfattningar använder du [REST API](/rest/api/monitor/private%20link%20scopes%20(preview)) eller [Azure CLI (AZ Monitor Private-Link-scope)](/cli/azure/monitor/private-link-scope?view=azure-cli-latest).

Om du vill hantera nätverks åtkomst använder du flaggorna `[--ingestion-access {Disabled, Enabled}]` och `[--query-access {Disabled, Enabled}]` på [Log Analytics arbets ytor](/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest) eller [Application Insights komponenter](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest).

## <a name="collect-custom-logs-over-private-link"></a>Samla in anpassade loggar över privat länk

Lagrings konton används i inmatnings processen för anpassade loggar. Som standard används tjänst hanterade lagrings konton. Men för att mata in anpassade loggar på privata länkar måste du använda dina egna lagrings konton och koppla dem till Log Analytics arbets ytor. Se mer information om hur du konfigurerar sådana konton med hjälp av [kommando raden](/cli/azure/monitor/log-analytics/workspace/linked-storage?view=azure-cli-latest).

Mer information om hur du ansluter ditt eget lagrings konto finns i [kundägda lagrings konton för logg](private-storage.md) inmatning

## <a name="restrictions-and-limitations"></a>Villkor och begränsningar

### <a name="agents"></a>Agenter

De senaste versionerna av Windows-och Linux-agenterna måste användas i privata nätverk för att möjliggöra säker inmatning till Log Analytics arbets ytor. Äldre versioner kan inte överföra övervaknings data i ett privat nätverk.

**Windows-agenten för Log Analytics**

Använd Log Analytics agent version 10.20.18038.0 eller senare.

**Log Analytics-agent för Linux**

Använd agent version 1.12.25 eller senare. Om du inte kan köra kör du följande kommandon på den virtuella datorn.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure Portal

Om du vill använda Azure Monitor Portal upplevelser som Application Insights och Log Analytics måste du tillåta att Azure Portal-och Azure Monitor-tillägg kan nås i privata nätverk. Lägg till **AzureActiveDirectory**, **AzureResourceManager**, **AzureFrontDoor. FirstParty** och **AzureFrontDoor. frontend** [service-Taggar](../../firewall/service-tags.md) i nätverks säkerhets gruppen.

### <a name="programmatic-access"></a>Programmässig åtkomst

Om du vill använda REST API, [CLI](/cli/azure/monitor?view=azure-cli-latest) eller PowerShell med Azure Monitor i privata nätverk lägger du till [service tag-](../../virtual-network/service-tags-overview.md)  **AzureActiveDirectory** och **AzureResourceManager** i brand väggen.

Genom att lägga till dessa taggar kan du utföra åtgärder som att fråga logg data, skapa och hantera Log Analytics arbets ytor och AI-komponenter.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Application Insights SDK-nedladdningar från ett Content Delivery Network

Paketera JavaScript-koden i skriptet så att webbläsaren inte försöker hämta kod från ett CDN. Ett exempel finns i [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>Webbläsarens DNS-inställningar

Om du ansluter till Azure Monitor resurser via en privat länk måste trafiken till dessa resurser gå igenom den privata slut punkt som är konfigurerad i nätverket. Om du vill aktivera den privata slut punkten uppdaterar du dina DNS-inställningar enligt beskrivningen i [Anslut till en privat slut punkt](#connect-to-a-private-endpoint). Vissa webbläsare använder sina egna DNS-inställningar i stället för de som du anger. Webbläsaren kan försöka ansluta till Azure Monitor offentliga slut punkter och kringgå den privata länken helt och hållet. Kontrol lera att inställningarna för webbläsaren inte åsidosätter eller cachelagrar gamla DNS-inställningar. 

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [privat lagring](private-storage.md)