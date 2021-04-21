---
title: Affärskontinuitet och haveriberedskap
description: Utforma din strategi för att skydda data, återställa snabbt från störande händelser, återställa resurser som krävs av kritiska affärsfunktioner och upprätthålla affärskontinuui för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: f974a99c59b19b5df7bf6ffcc66c2dc133743f0a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790547"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Affärskontinui och haveriberedskap för Azure Logic Apps

Se till att du har en haveriberedskapslösning [  (DR)](https://en.wikipedia.org/wiki/Disaster_recovery) på plats för att skydda data, snabbt återställa de resurser som stöder kritiska affärsfunktioner och hålla driften igång för att upprätthålla affärskontinu kontinuitet [  (BC)](https://en.wikipedia.org/wiki/Business_continuity_planning)för att minska påverkan och effekter som oförutsägbara händelser har på ditt företag och dina kunder. Till exempel kan avbrott omfatta avbrott, förluster i underliggande infrastruktur eller komponenter som lagring, nätverk eller beräkningsresurser, oåterkalleliga programfel eller till och med en fullständig datacenterförlust. Genom att ha en lösning för affärskontinuuisering och haveriberedskap (BCDR) redo kan ditt företag eller din organisation svara snabbare på avbrott, planerade eller oplanerade och minska stilleståndstiden för dina kunder.

Den här artikeln innehåller BCDR-vägledning och strategier som du kan använda när du skapar automatiserade arbetsflöden med hjälp av [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Med logikapparbetsflöden kan du enkelt integrera och samordna data mellan appar, molntjänster och lokala system genom att minska hur mycket kod du behöver skriva. När du planerar för BCDR bör du inte bara tänka på dina logikappar, utan även de Azure-resurser som du använder med dina logikappar:

* [Anslutningar](../connectors/apis-list.md) som du skapar från logikappar till andra appar, tjänster och system. Mer information finns i Anslutningar [till resurser senare](#resource-connections) i det här avsnittet.

* [Lokala datagatewayer som är Azure-resurser](../logic-apps/logic-apps-gateway-connection.md) som du skapar och använder i dina logikappar för att komma åt data i lokala system. Varje gateway-resurs representerar en separat [datagatewayinstallation](../logic-apps/logic-apps-gateway-install.md) på en lokal dator. Mer information finns i [Lokala datagatewayer senare i](#on-premises-data-gateways) det här avsnittet.

* [Integrationskonton](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) där du definierar och lagrar artefakter som logikappar använder för företagsintegreringsscenarier för företag till företag [(B2B).](../logic-apps/logic-apps-enterprise-integration-overview.md) Du kan till exempel konfigurera [haveriberedskap mellan regioner för integrationskonton.](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)

* [Integreringstjänstmiljöer (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) där du skapar logikappar som körs i en isolerad Logic Apps-körningsinstans i ett virtuellt Azure-nätverk. Dessa logikappar kan sedan komma åt resurser som skyddas bakom en brandvägg i det virtuella nätverket.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Primära och sekundära platser

Varje logikapp måste ange den plats som du vill använda för distribution. Den här platsen är antingen en offentlig region i globala Azure för flera innehavare, till exempel "USA, västra", eller en integrationstjänstmiljö (ISE) som du tidigare har skapat och distribuerat till ett virtuellt Azure-nätverk. Att köra logikappar i en ISE liknar att köra logikappar i en global Azure-region, vilket innebär att din strategi för haveriberedskap kan tillämpas på båda scenarierna. Ise-företag har dock andra överväganden, till exempel att konfigurera åtkomst till resurser som endast är tillgängliga för ISE:er.

> [!NOTE]
> Om logikappen också fungerar med B2B-artefakter, till exempel handelspartner, avtal, scheman, kartor och certifikat, som lagras i ett integrationskonto, måste både ditt integrationskonto och dina logikappar ange samma plats.

Den här strategin för haveriberedskap [](https://en.wikipedia.org/wiki/Failover) fokuserar på att konfigurera din primära logikapp för redundans till en logikapp för vänteläge eller säkerhetskopiering på en annan plats där Azure Logic Apps också är tillgänglig. På så sätt kan den sekundära sekundärt ta på sig arbetet om den primära drabbas av förluster, avbrott eller fel. Den här strategin kräver att din sekundära logikapp och beroende resurser redan har distribuerats och är redo på den alternativa platsen.

Om du följer bra DevOps-metoder använder du redan [Azure Resource Manager för](../azure-resource-manager/management/overview.md) att definiera och distribuera dina logikappar och deras beroende resurser. Resource Manager ger dig möjlighet att använda en enda distributionsdefinition och sedan använda parameterfiler för att ange de konfigurationsvärden som ska användas för varje distributionsmål. Den här funktionen innebär att du kan distribuera samma logikapp till olika miljöer, till exempel utveckling, testning och produktion. Du kan också distribuera samma logikapp till olika Azure-regioner eller ISE:er, som stöder haveriberedskapsstrategier [som använder parkopplade regioner.](../best-practices-availability-paired-regions.md)

För redundansstrategin måste dina logikappar och platser uppfylla följande krav:

* Den sekundära logikappinstansen har åtkomst till samma appar, tjänster och system som den primära logikappinstansen.

* Båda logikappinstanserna har samma värdtyp. Antingen distribueras båda instanserna till regioner i globala Azure för flera innehavare, eller så distribueras båda instanserna till ISE:er, vilket gör att dina logikappar kan komma åt resurser direkt i ett virtuellt Azure-nätverk. Metodtips och mer information om parkopplade regioner för BCDR finns i Affärskontinui och haveriberedskap [(BCDR): Azure-parkopplade regioner](../best-practices-availability-paired-regions.md).

  Till exempel måste både den primära och den sekundära platsen vara ISE:er när den primära logikappen körs i en ISE och använder [ISE-versionsanslutningsappar, HTTP-åtgärder](../connectors/managed.md#ise-connectors)för att anropa resurser i det virtuella Azure-nätverket eller både och. I det här scenariot måste din sekundära logikapp också ha en liknande konfiguration på den sekundära platsen som den primära logikappen.

  > [!NOTE]
  > För mer avancerade scenarier kan du blanda både Azure för flera innehavare och en ISE som platser. Se dock till att du överväger och förstår skillnaderna mellan [hur logikappar körs i en ISE jämfört](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)med Azure för flera innehavare.

* Om du använder [ISE:er kontrollerar du att de skalas ut eller har tillräckligt med kapacitet](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) för att hantera belastningen.

#### <a name="example-multi-tenant-azure"></a>Exempel: Azure för flera innehavare

I det här exemplet visas primära och sekundära logikappinstanser som distribueras till separata regioner i den globala Azure för flera innehavare för det här scenariot. En enda [Resource Manager definierar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) både logikappinstanser och de beroende resurser som krävs av dessa logikappar. Separata parameterfiler anger de konfigurationsvärden som ska användas för varje distributionsplats:

![Primära och sekundära logikappinstanser på olika platser](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Exempel: Integreringstjänstmiljö

Det här exemplet visar de tidigare primära och sekundära logikappinstanserna men distribueras till separata ISE:er. En enda Resource Manager-mall definierar både logikappinstanser, de beroende resurser som krävs av dessa logikappar och ISE:erna som distributionsplatser. Separata parameterfiler definierar de konfigurationsvärden som ska användas för distribution på varje plats:

![Primära och sekundära logikappar på olika platser](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Anslutningar till resurser

Azure Logic Apps innehåller inbyggda utlösare och åtgärder plus hundratals hanterade anslutningsappar som din logikapp kan använda för att arbeta med andra appar, tjänster, system och andra resurser, till exempel Azure Storage-konton, [SQL Server-databaser, e-postkonton](../connectors/apis-list.md) för arbete eller skola och så vidare. Om logikappen behöver åtkomst till dessa resurser skapar du anslutningar som autentiserar åtkomsten till dessa resurser. Varje anslutning är en separat Azure-resurs som finns på en specifik plats och som inte kan användas av resurser på andra platser.

För din strategi för haveriberedskap bör du överväga de platser där beroende resurser finns i förhållande till dina logikappinstanser:

* Din primära instans och beroende resurser finns på olika platser. I det här fallet kan din sekundära instans ansluta till samma beroende resurser eller slutpunkter. Du bör dock skapa anslutningar specifikt för den sekundära instansen. På så sätt påverkas inte den sekundära platsens anslutningar om den primära platsen blir otillgänglig.

  Anta till exempel att din primära logikapp ansluter till en extern tjänst, till exempel Salesforce. Vanligtvis är den externa tjänstens tillgänglighet och plats oberoende av logikappens tillgänglighet. I det här fallet kan din sekundära instans ansluta till samma tjänst men ha en egen anslutning.

* Både din primära instans och beroende resurser finns på samma plats. I det här fallet bör beroende resurser ha säkerhetskopior eller replikerade versioner på en annan plats så att den sekundära instansen fortfarande kan komma åt dessa resurser.

  Anta till exempel att din primära logikapp ansluter till en tjänst som finns på samma plats eller region, till exempel Azure SQL Database. Om hela den här regionen blir otillgänglig Azure SQL Database tjänsten i den regionen är förmodligen inte tillgänglig. I det här fallet vill du att den sekundära instansen ska använda en replikerad eller säkerhetskopierad databas tillsammans med en separat anslutning till databasen.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>Lokala datagatewayer

Om logikappen körs i Azure för flera innehavare och behöver åtkomst till lokala resurser, till exempel SQL Server-databaser, måste du installera den lokala [datagatewayen](../logic-apps/logic-apps-gateway-install.md) på en lokal dator. Du kan sedan skapa en datagatewayresurs i Azure Portal så att logikappen kan använda gatewayen när du skapar en anslutning till resursen.

Datagatewayresursen är associerad med en plats eller Azure-region, precis som din logikappresurs. I din strategi för haveriberedskap kontrollerar du att datagatewayen fortfarande är tillgänglig för logikappen. Du kan [aktivera hög tillgänglighet för din gateway när](../logic-apps/logic-apps-gateway-install.md#high-availability) du har flera gatewayinstallationer.

> [!NOTE]
> Om logikappen körs i en integrationstjänstmiljö (ISE) och endast använder ISE-versionsbaserade anslutningsappar för lokala datakällor behöver du inte datagatewayen eftersom ISE-anslutningarna ger direktåtkomst till den lokala resursen.
>
> Om det inte finns någon ISE-version för anslutning för den lokala resursen som du vill ha kan logikappen fortfarande skapa anslutningen med hjälp av en icke-ISE-anslutningsapp som körs i den globala Azure för flera innehavare, inte din ISE. Den här anslutningen kräver dock den lokala datagatewayen.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Roller som är aktiva-aktiva eller aktiva-passiva

Du kan konfigurera dina primära och sekundära platser så att dina logikappinstanser på dessa platser kan ha följande roller:

| Primär-sekundär roll | Description |
|------------------------|-------------|
| *Aktiv-aktiv* | De primära och sekundära logikappinstanserna på båda platserna hanterar aktivt begäranden genom att följa något av dessa mönster: <p><p>- *Belastningsutjämning:* Båda instanserna kan lyssna på en slutpunkt och belastningsutjämna trafik till varje instans efter behov. <p>- *Konkurrerande* konsumenter: Båda instanserna kan fungera som konkurrerande konsumenter så att instanserna konkurrerar om meddelanden från en kö. Om en instans misslyckas tar den andra instansen över arbetsbelastningen. |
| *Aktiv-passiv* | Den primära logikappinstansen hanterar aktivt hela arbetsbelastningen, medan den sekundära instansen är passiv (inaktiverad eller inaktiv). Den sekundära väntar på en signal om att den primära är otillgänglig eller inte fungerar på grund av avbrott eller fel och tar över arbetsbelastningen som den aktiva instansen. |
| Kombination | Vissa logikappar har en aktiv-aktiv-roll, medan andra logikappar har en aktiv-passiv roll. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Exempel på aktiv-aktiv

De här exemplen visar konfigurationen aktiv-aktiv där båda logikappinstanserna aktivt hanterar begäranden eller meddelanden. Vissa andra system eller tjänster distribuerar begäranden eller meddelanden mellan instanser, till exempel något av följande alternativ:

* En "fysisk" lastbalanserare, till exempel en maskinvara som dirigerar trafik

* En "mjuk" lastbalanserare som [Azure Load Balancer](../load-balancer/load-balancer-overview.md) eller [Azure API Management](../api-management/api-management-key-concepts.md). Med API Management kan du ange principer som bestämmer hur inkommande trafik ska belastningsutjämnas. Eller så kan du använda en tjänst som stöder tillståndsspårning, till exempel [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

  Även om det här Azure Load Balancer främst visar Azure Load Balancer kan du använda det alternativ som bäst passar ditt scenarios behov:

  ![Konfiguration av "aktiv-aktiv" som använder en lastbalanserare eller tillståndsfull tjänst](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Varje logikappinstans fungerar som konsument och båda instanserna konkurrerar om meddelanden från en kö:

  !["Aktiv-aktiv"-konfiguration som använder "konkurrerande konsumenter"](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Aktiv-passiv-exempel

Det här exemplet visar den aktiva-passiva konfigurationen där den primära logikappinstansen är aktiv på en plats, medan den sekundära instansen förblir inaktiv på en annan plats. Om de primära upplever avbrott eller fel kan du be en operatör att köra ett skript som aktiverar den sekundära för att ta på arbetsbelastningen.

!["Aktiv-passiv"-konfiguration som använder "konkurrerande konsumenter"](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Kombination med aktiv-aktiv och aktiv-passiv

Det här exemplet visar en kombinerad konfiguration där den primära platsen har båda aktiva logikappinstanser, medan den sekundära platsen har aktiv-passiva logikappinstanser. Om den primära platsen får ett avbrott eller fel kan den aktiva logikappen på den sekundära platsen, som redan hanterar en partiell arbetsbelastning, ta över hela arbetsbelastningen.

* På den primära platsen lyssnar en aktiv logikapp på en Azure Service Bus-kö efter meddelanden, medan en annan aktiv logikapp söker efter e-postmeddelanden med hjälp av en Avsökningsutlösare i Office 365 Outlook.

* På den sekundära platsen fungerar en aktiv logikapp med logikappen på den primära platsen genom att lyssna och konkurrera om meddelanden från samma Service Bus kö. Under tiden väntar en passiv inaktiv logikapp i vänteläge för att  söka efter e-postmeddelanden när den primära platsen blir otillgänglig, men inaktiveras för att undvika att läsa e-postmeddelanden igen.

!["Aktiv-passiv" och "aktiv-passiv"-kombination som använder upprepningsutlösare](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Logikappens tillstånd och historik

När logikappen utlöses och börjar köras lagras appens tillstånd på samma plats där appen startade och kan inte överföras till en annan plats. Om ett fel eller avbrott inträffar överger du pågående arbetsflödesinstanser. När du har en primär och sekundär plats konfigurerad börjar nya arbetsflödesinstanser köras på den sekundära platsen.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Minska övergivna pågående instanser

Om du vill minimera antalet övergivna pågående arbetsflödesinstanser kan du välja bland olika meddelandemönster som du kan implementera, till exempel:

* [Ett fast routningsnedstegsmönster har åtgärdats](/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Det här företagsmeddelandemönstret som delar upp en affärsprocess i mindre steg. För varje fas ställer du in en logikapp som hanterar arbetsbelastningen för den fasen. För att kommunicera med varandra använder dina logikappar ett asynkront meddelandeprotokoll, till exempel Azure Service Bus köer eller ämnen. När du delar upp en process i mindre steg minskar du antalet affärsprocesser som kan fastna på en instans av en logikapp som misslyckats. Mer allmän information om det här mönstret finns i [Enterprise integration patterns - Routing hak](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html).

  Det här exemplet visar ett mönster för routningsnedsteg där varje logikapp representerar en fas och använder en Service Bus-kö för att kommunicera med nästa logikapp i processen.

  ![Dela upp en affärsprocess i faser som representeras av logikappar, som kommunicerar med varandra med hjälp Azure Service Bus köer](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Om både primära och sekundära logikappinstanser följer samma mönster för routningsnedsteg på sina platser kan du implementera mönstret för konkurrerande konsumenter genom att konfigurera [aktiva-aktiva](#roles) roller för dessa instanser. [](/azure/architecture/patterns/competing-consumers)

* [Mönster för processhanterare (a broker)](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Granska lås utan timeout-mönster](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Åtkomst till utlösar- och körningshistorik

Om du vill ha mer information om logikappens tidigare arbetsflödeskörningar kan du granska appens utlösare och körningshistorik. En logikapps historikkörningshistorik lagras på samma plats eller region där logikappen kördes, vilket innebär att du inte kan migrera den här historiken till en annan plats. Om den primära instansen går över till en sekundär instans kan du bara komma åt varje instanss utlösare och körningshistorik på respektive platser där dessa instanser kördes. Du kan dock hämta platsoberoende information om logikappens historik genom att konfigurera logikapparna så att de skickar diagnostikhändelser till en Azure Log Analytics-arbetsyta. Du kan sedan granska hälsotillståndet och historiken för logikappar som körs på flera platser.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Vägledning för utlösartyp

Den utlösartyp som du använder i dina logikappar avgör dina alternativ för hur du kan konfigurera logikappar mellan platser i din strategi för haveriberedskap. Här är de tillgängliga utlösartyper som du kan använda i logikappar:

* [Upprepningsutlösare](#recurrence-trigger)
* [Avsökningsutlösare](#polling-trigger)
* [Begärandeutlösare](#request-trigger)
* [Webhook-utlösare](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Upprepningsutlösare

Upprepningsutlösaren är oberoende av en specifik tjänst eller slutpunkt och utlöses enbart baserat på ett angivet schema och inga andra kriterier, till exempel: 

* En fast frekvens och ett intervall, till exempel var 10:e minut
* Ett mer avancerat schema, till exempel den sista måndagen i varje månad kl. 17:00

När logikappen börjar med en upprepningsutlösare måste du konfigurera dina primära och sekundära logikappinstanser med de [aktiva-passiva rollerna](#roles). För att  minska målet för återställningstid (RTO), som avser målvaraktigheten för återställning av en affärsprocess efter ett avbrott eller en katastrof, kan du konfigurera dina logikappinstanser med en kombination av [aktiva-passiva](#roles) roller och [passiv-aktiva roller.](#roles) I den här konfigurationen delar du upp schemat mellan platser.

Anta till exempel att du har en logikapp som måste köras var 10:e minut. Du kan konfigurera logikappar och platser så att den sekundära platsen kan ta över arbetet om den primära platsen blir otillgänglig:

![Kombinationen "Aktiv-passiv" och "passiv-aktiv" som använder upprepningsutlösare](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* Konfigurera aktiva-passiva roller för [dessa logikappar på den](#roles) primära platsen:

  * För den *aktiva* aktiverade logikappen ställer du in upprepningsutlösaren så att den startar högst upp i timmen och upprepas var 20:e minut, till exempel 09:00, 9:20 och så vidare.

  * För  den passiva inaktiverade logikappen anger du upprepningsutlösaren till samma schema men börjar 10 minuter efter timmen och upprepar var 20:e minut, till exempel 9:10, 9:30 och så vidare.

* Konfigurera passiv-aktiv för dessa [logikappar på den](#roles) sekundära platsen:

  * För  den passiva inaktiverade logikappen anger du upprepningsutlösaren till samma schema som den aktiva logikappen på den primära platsen, som är högst upp i timmen och upprepas var 20:e minut, till exempel 09:00, 9:10 och så vidare.

  * För  den aktiva aktiverade logikappen anger du upprepningsutlösaren till samma schema som den passiva logikappen på den primära platsen, vilket är att starta kl. 10 minuter efter timmen och upprepas var 20:e minut, till exempel 9:10, 9:20 och så vidare.

Om en störande händelse inträffar på den primära platsen aktiverar du nu den passiva logikappen på den alternativa platsen. Om det tar tid att hitta felet begränsar den här konfigurationen antalet missade upprepningar under den fördröjningen.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Avsökningsutlösare

Om du regelbundet vill kontrollera om nya data för bearbetning är tillgängliga  från en viss tjänst eller slutpunkt kan logikappen använda en avsökningsutlösare som upprepade gånger anropar tjänsten eller slutpunkten baserat på ett fast upprepningsschema. De data som tjänsten eller slutpunkten tillhandahåller kan ha någon av följande typer:

* Statiska data, som beskriver data som alltid är tillgängliga för läsning
* Ej beständiga data, som beskriver data som inte längre är tillgängliga efter läsning

För att undvika upprepade läsningar av samma data måste logikappen komma ihåg vilka data som tidigare lästes genom att upprätthålla tillståndet antingen på klientsidan eller på servern, tjänsten eller systemsidan.

* Logikappar som fungerar med klientsidans tillstånd använder utlösare som kan upprätthålla tillståndet.

  En utlösare som läser ett nytt meddelande från en inkorg i ett e-postmeddelande kräver till exempel att utlösaren kommer ihåg det senast lästa meddelandet. På så sätt startar utlösaren logikappen endast när nästa olästa meddelande tas emot.

* Logikappar som fungerar med server-, tjänst- eller systemtillstånd använder egenskapsvärden eller inställningar som finns på server-, tjänst- eller systemsidan.

  Till exempel kräver en frågebaserad utlösare som läser en rad från en databas att raden har en `isRead` kolumn som är inställd på `FALSE` . Varje gång utlösaren läser en rad uppdaterar logikappen den raden genom att ändra `isRead` kolumnen `FALSE` från till `TRUE` .

  Den här metoden på serversidan fungerar på samma sätt för Service Bus köer eller ämnen som har kösemantik där en utlösare kan läsa och låsa ett meddelande medan logikappen bearbetar meddelandet. När logikappen är klar med bearbetningen tar utlösaren bort meddelandet från kön eller ämnet.

När du ställer in logikappens primära och sekundära instanser ur ett haveriberedskapsperspektiv ska du se till att ta hänsyn till dessa beteenden baserat på om logikappen spårar tillstånd på klientsidan eller på serversidan:

* För en logikapp som fungerar med klientsidans tillstånd kontrollerar du att logikappen inte läser samma meddelande mer än en gång. Endast en plats kan ha en aktiv logikappinstans vid en viss tidpunkt. Kontrollera att logikappinstansen på den alternativa platsen är inaktiv eller inaktiverad tills den primära instansen växlar över till den alternativa platsen.

  Till exempel behåller Office 365 Outlook-utlösaren klientsidans tillstånd och spårar tidsstämpeln för den senast lästa e-postmeddelandet för att undvika att läsa en dubblett.

* För en logikapp som fungerar med tillstånd på serversidan kan du konfigurera dina logikappinstanser så att de antingen spelar [aktiva-aktiva](#roles) roller där de fungerar som konkurrerande konsumenter eller [aktiva-passiva](#roles) roller där den alternativa instansen väntar tills den primära instansen växlar över till den alternativa platsen.

  Om du till exempel läser från en meddelandekö, till exempel en Azure Service Bus-kö, används tillståndet på serversidan eftersom kötjänsten har lås på meddelanden för att förhindra att andra klienter läser samma meddelanden.

  > [!NOTE]
  > Om logikappen behöver läsa meddelanden i en viss ordning, till exempel från en Service Bus-kö, kan du använda det konkurrerande konsumentmönstret, men endast i kombination med Service Bus-sessioner, som även kallas sekventiell [ *konvojmönster.*](/azure/architecture/patterns/sequential-convoy) Annars måste du konfigurera dina logikappinstanser med de aktiva-passiva rollerna.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Begärandeutlösare

Begärandeutlösaren gör att logikappen kan anropas från andra appar, tjänster och system och används vanligtvis för att tillhandahålla följande funktioner: 

* En direkt REST API för din logikapp som andra kan anropa

  Använd till exempel utlösaren Request (Begäran) för att starta logikappen så att andra logikappar kan anropa utlösaren med åtgärden Call workflow - Logic Apps (Anropa arbetsflöde **– Logic Apps).**

* En [webhook eller](#webhook-trigger) återanropsmekanism för logikappen

* Ett sätt att manuellt köra användaråtgärder eller rutiner för att anropa logikappen, till exempel genom att använda ett PowerShell-skript som utför en viss uppgift

Ur ett haveriberedskapsperspektiv är begärandeutlösaren en passiv mottagare eftersom logikappen inte gör något arbete och väntar tills någon annan tjänst eller ett annat system uttryckligen anropar utlösaren. Som en passiv slutpunkt kan du konfigurera dina primära och sekundära instanser på följande sätt:

* [Aktiv-aktiv:](#roles)Båda instanserna hanterar aktivt begäranden eller anrop. Anroparen eller routern balanserar eller distribuerar trafik mellan dessa instanser.

* [Aktiv-passiv:](#roles)Endast den primära instansen är aktiv och hanterar allt arbete, medan den sekundära instansen väntar tills den primära instansen upplever avbrott eller fel. Anroparen eller routern avgör när den sekundära instansen ska anropas.

Som en rekommenderad arkitektur kan du använda Azure API Management som proxy för de logikappar som använder begärandeutlösare. API Management ger inbyggd återhämtning mellan olika länder och [möjligheten att dirigera trafik över flera slutpunkter.](../api-management/api-management-howto-deploy-multi-region.md)

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Webhook-utlösare

En *webhook-utlösare* ger logikappen möjlighet att prenumerera på en tjänst genom att skicka en *återanrops-URL* till tjänsten. Logikappen kan sedan lyssna och vänta tills en specifik händelse inträffar på den tjänstslutpunkten. När händelsen inträffar anropar tjänsten webhook-utlösaren med hjälp av motringning-URL:en, som sedan kör logikappen. När den är aktiverad prenumererar webhook-utlösaren på tjänsten. När den är inaktiverad avbryts utlösaren från tjänsten.

Ur ett haveriberedskapsperspektiv kan du konfigurera primära och sekundära instanser som använder webhook-utlösare för att spela aktiv-passiva roller eftersom endast en instans ska ta emot händelser eller meddelanden från den prenumererade slutpunkten.

## <a name="assess-primary-instance-health"></a>Utvärdera den primära instansens hälsa

För att din strategi för haveriberedskap ska fungera behöver din lösning sätt att utföra dessa uppgifter:

* [Kontrollera tillgängligheten för den primära instansen](#check-primary-availability)
* [Övervaka den primära instansens hälsa](#monitor-primary-health)
* [Aktivera den sekundära instansen](#activate-secondary)

I det här avsnittet beskrivs en lösning som du kan använda direkt eller som grund för din egen design. Här är en översikt över det visuella objektet på hög nivå för den här lösningen:

![Skapa watchdog-logikapp som övervakar en logikapp för hälsokontroll på den primära platsen](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Kontrollera tillgänglighet för primär instans

För att avgöra om den primära instansen är tillgänglig, körs och kan fungera kan du skapa en "hälsokontroll"-logikapp som finns på samma plats som den primära instansen. Du kan sedan anropa den här hälsokontrollappen från en alternativ plats. Om hälsokontrollappen svarar är den underliggande infrastrukturen för Azure Logic Apps i den regionen tillgänglig och fungerar. Om hälsokontrollappen inte svarar kan du anta att platsen inte längre är felfri.

För den här uppgiften skapar du en grundläggande logikapp för hälsokontroll som utför följande uppgifter:

1. Tar emot ett anrop från watchdog-appen med hjälp av begärandeutlösaren.

1. Svara med en status som anger om den markerade logikappen fortfarande fungerar med åtgärden Svar.

   > [!IMPORTANT]
   > Logikappen för hälsokontroll måste använda en svarsåtgärd så att appen svarar synkront, inte asynkront.

1. Om du vill fastställa om den primära platsen är felfri kan du även ta med andra tjänster som interagerar med mållogikappen på den här platsen i faktorer. Expandera bara logikappen för hälsokontroll för att utvärdera hälsotillståndet för dessa andra tjänster också.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Skapa en watchdog-logikapp

Om du vill övervaka den primära instansens hälsa och anropa logikappen för hälsokontroll skapar du en "watchdog"-logikapp på en *alternativ plats.* Du kan till exempel konfigurera logikappen watchdog så att watchdog-logiken kan skicka en avisering till driftteamet om det inte går att anropa logiken för hälsokontroll så att de kan undersöka felet och varför den primära instansen inte svarar.

> [!IMPORTANT]
> Kontrollera att logikappen watchdog finns på en *plats som skiljer sig från den primära platsen*. Om det Logic Apps tjänsten på den primära platsen uppstår problem kanske logikappen watchdog inte körs.

För den här uppgiften skapar du på den sekundära platsen en watchdog-logikapp som utför dessa uppgifter:

1. Kör baserat på en fast eller schemalagd upprepning med hjälp av upprepningsutlösaren.

   Du kan ange upprepningen till ett värde som understiger toleransnivån för ditt mål för återställningstid (RTO).

1. Anropa logikappen för hälsokontroll på den primära platsen med hjälp av HTTP-åtgärden, till exempel:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>Aktivera din sekundära instans

Om du vill aktivera den sekundära instansen automatiskt kan du [](/connectors/arm/) skapa en logikapp som anropar hanterings-API:et, till exempel Azure Resource Manager-anslutningsappen, för att aktivera lämpliga logikappar på den sekundära platsen. Du kan expandera din watchdog-app för att anropa den här aktiveringslogiken när ett visst antal fel inträffar.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Samla in diagnostikdata

Du kan konfigurera loggning för dina logikappkörningar och skicka resulterande diagnostikdata till tjänster som Azure Storage, Azure Event Hubs och Azure Log Analytics för ytterligare hantering och bearbetning.

* Om du vill använda dessa data med Azure Log Analytics kan du göra data tillgängliga för både  den primära och sekundära platsen genom att konfigurera logikappens diagnostikinställningar och skicka data till flera Log Analytics-arbetsytor. Mer information finns i Konfigurera [loggar Azure Monitor samla in diagnostikdata för Azure Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md).

* Om du vill skicka data till Azure Storage eller Azure Event Hubs kan du göra data tillgängliga för både den primära och sekundära platsen genom att konfigurera geo-redundans. Mer information finns i de här artiklarna:<p>

  * [Azure Blob Storage haveriberedskap och redundans för konto](../storage/common/storage-disaster-recovery-guidance.md)
  * [Azure Event Hubs geo-haveriberedskap](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Nästa steg

* [Översikt över återhämtning för Azure](/azure/architecture/framework/resiliency/overview)
* [Checklista för återhämtning för specifika Azure-tjänster](/azure/architecture/checklist/resiliency-per-service)
* [Datahantering för återhämtning i Azure](/azure/architecture/framework/resiliency/data-management)
* [Säkerhetskopiering och haveriberedskap för Azure-program](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Återställa från regionomfattande tjänststörningar](/azure/architecture/resiliency/recovery-loss-azure-region)
* [Microsofts serviceavtal (SLA) för Azure-tjänster](https://azure.microsoft.com/support/legal/sla/)
