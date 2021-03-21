---
title: Affärskontinuitet och haveriberedskap
description: Utforma din strategi för att skydda data, återställa snabbt från störande händelser, återställa resurser som krävs av kritiska affärs funktioner och upprätthålla affärs kontinuiteten för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 0a36cb468ebcb77c0614bffd0afc392df3655c20
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89658204"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Verksamhets kontinuitet och haveri beredskap för Azure Logic Apps

För att minska påverkan och effekter som oförutsägbara händelser har på ditt företag och kunder, se till att du har en lösning för [ *haveri beredskap* (Dr)](https://en.wikipedia.org/wiki/Disaster_recovery) så att du kan skydda data, snabbt återställa de resurser som har stöd för kritiska affärs funktioner och fortsätta driften att underhålla verksamhets [ *kontinuiteten* (BC)](https://en.wikipedia.org/wiki/Business_continuity_planning). Till exempel kan avbrott omfatta avbrott, förluster i underliggande infrastruktur eller komponenter, till exempel lagring, nätverk eller beräknings resurser, oåterkalleliga program fel eller till och med en fullständig Data Center förlust. Genom att ha en BCDR-lösning (verksamhets kontinuitet och haveri beredskap) kan företaget eller organisationen svara snabbare på avbrott, planerade eller oplanerade och minska stillestånds tiden för dina kunder.

Den här artikeln innehåller vägledning och strategier för BCDR som du kan använda när du skapar automatiserade arbets flöden med hjälp av [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Med Logic app-arbetsflöden kan du enklare integrera och dirigera data mellan appar, moln tjänster och lokala system genom att minska hur mycket kod du behöver skriva. När du planerar för BCDR bör du se till att du inte bara vill ha dina Logi Kap par, utan även de Azure-resurser som du använder med dina Logic Apps:

* [Anslutningar](../connectors/apis-list.md) som du skapar från Logic Apps till andra appar, tjänster och system. Mer information finns i [anslutningar till resurser](#resource-connections) senare i det här avsnittet.

* [Lokala datagatewayer](../logic-apps/logic-apps-gateway-connection.md) som är Azure-resurser som du skapar och använder i dina Logic Apps för att komma åt data i lokala system. Varje gateway-resurs representerar en separat [datagateway-installation](../logic-apps/logic-apps-gateway-install.md) på en lokal dator. Mer information finns i [lokala datagatewayer](#on-premises-data-gateways) senare i det här avsnittet.

* [Integrations konton](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) där du definierar och lagrar artefakter som Logic Apps använder för [företags integrerings scenarier för företag till företag (B2B)](../logic-apps/logic-apps-enterprise-integration-overview.md) . Du kan till exempel [Konfigurera haveri beredskap mellan regioner för integrations konton](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md).

* [Integrerings tjänst miljöer (ISEs)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) där du skapar Logi Kap par som körs i en isolerad Logic Apps runtime-instans i ett virtuellt Azure-nätverk. Dessa Logic Apps kan sedan komma åt resurser som skyddas bakom en brand vägg i det virtuella nätverket.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Primära och sekundära platser

Varje Logic app måste ange den plats som du vill använda för distribution. Den här platsen är antingen en offentlig region i Global Azure med flera innehavare, t. ex. "västra USA" eller en integrerings tjänst miljö (ISE) som du tidigare har skapat och distribuerat till ett virtuellt Azure-nätverk. Att köra Logi Kap par i en ISE liknar att köra Logi Kap par i en global Azure-region, vilket innebär att din katastrof återställnings strategi kan tillämpas på båda scenarierna. ISEs har dock andra överväganden som att konfigurera åtkomst till resurser som endast är tillgängliga för ISEs.

> [!NOTE]
> Om din Logic app också fungerar med B2B-artefakter, till exempel handels partner, avtal, scheman, kartor och certifikat, som lagras i ett integrations konto, måste både integrations kontot och logi Kap par ange samma plats.

Den här strategin för haveri beredskap fokuserar på att konfigurera din primära Logic-app för [*redundans*](https://en.wikipedia.org/wiki/Failover) till en standby-eller säkerhets kopierings-Logic-app på en annan plats där Azure Logic Apps också är tillgängligt. På så sätt kan den sekundära åtgärden ta på arbetet om den primära förlusten uppstår, avbrott eller haverier. Den här strategin kräver att din sekundära Logic-app och beroende resurser redan har distribuerats och är redo på den alternativa platsen.

Om du följer DevOps-praxis använder du redan [Azure Resource Manager mallar](../azure-resource-manager/management/overview.md) för att definiera och distribuera dina Logi Kap par och deras beroende resurser. Resource Manager-mallar ger dig möjlighet att använda en enda distributions definition och sedan använda parametriserade för att tillhandahålla de konfigurations värden som ska användas för varje distributions mål. Den här funktionen innebär att du kan distribuera samma Logic-app till olika miljöer, till exempel utveckling, testning och produktion. Du kan också distribuera samma Logic-app till olika Azure-regioner eller ISEs, som har stöd för katastrof återställnings strategier som använder [kopplade regioner](../best-practices-availability-paired-regions.md).

För redundans strategin måste dina Logi Kap par och platser uppfylla följande krav:

* Den sekundära Logic App-instansen har åtkomst till samma appar, tjänster och system som den primära Logic App-instansen.

* Båda Logic app-instanserna har samma värd typ. Båda instanserna distribueras till regioner i globala Azure för flera klienter, eller båda instanserna distribueras till ISEs, vilket gör att dina Logi Kap par får direkt åtkomst till resurser i ett virtuellt Azure-nätverk. Bästa praxis och mer information om kopplade regioner för BCDR finns i [verksamhets kontinuitet och haveri beredskap (BCDR): Azure-kopplade regioner](../best-practices-availability-paired-regions.md).

  Till exempel måste både den primära och den sekundära platsen vara ISEs när den primära Logic-appen körs i en ISE och använder [ISE-versions anslutningar](../connectors/apis-list.md#ise-connectors), http-åtgärder för att anropa resurser i det virtuella Azure-nätverket eller båda. I det här scenariot måste den sekundära Logic-appen också ha en liknande installation på den sekundära platsen som den primära Logic-appen.

  > [!NOTE]
  > För mer avancerade scenarier kan du blanda både Azure med flera innehavare och en ISE som platser. Men se till att du betraktar och förstår [skillnaderna mellan hur Logi Kap par körs i en ISE jämfört med flera klienter i Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

* Om du använder ISEs måste du [kontrol lera att de skalas ut eller har tillräckligt med kapacitet](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) för att hantera belastningen.

#### <a name="example-multi-tenant-azure"></a>Exempel: Azure med flera innehavare

I det här exemplet visas de primära och sekundära Logic app-instanserna som distribueras till separata regioner i den globala Azure med flera innehavare för det här scenariot. En enskild [Resource Manager-mall](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) definierar både Logic App-instanser och de beroende resurser som krävs av dessa Logic Apps. Separata parameter-filer anger de konfigurations värden som ska användas för varje distributions plats:

![Primära och sekundära Logic App-instanser på separata platser](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Exempel: integrerings tjänst miljö

I det här exemplet visas de tidigare primära och sekundära Logic app-instanserna, men distribueras till separata ISEs. En enskild Resource Manager-mall definierar både Logic App-instanser, de beroende resurser som krävs av dessa Logic Apps och ISEs som distributions platser. Separata parameter-filer definierar de konfigurations värden som ska användas för distribution på varje plats:

![Primära och sekundära Logic Apps på olika platser](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Anslutningar till resurser

Azure Logic Apps innehåller [inbyggda utlösare och åtgärder plus hundratals hanterade anslutningar](../connectors/apis-list.md) som din Logic app kan använda för att arbeta med andra appar, tjänster, system och andra resurser, till exempel Azure Storage konton, SQL Server databaser, arbets-eller skol-e-postkonton och så vidare. Om din Logi Kap par behöver åtkomst till dessa resurser, skapar du anslutningar som autentiserar åtkomst till dessa resurser. Varje anslutning är en separat Azure-resurs som finns på en särskild plats och som inte kan användas av resurser på andra platser.

För katastrof återställnings strategin bör du tänka på var beroende resurser finns i förhållande till dina Logic App-instanser:

* Din primära instans och beroende resurser finns på olika platser. I det här fallet kan den sekundära instansen ansluta till samma beroende resurser eller slut punkter. Du bör dock skapa anslutningar specifikt för din sekundära instans. På så sätt påverkas inte dina sekundära anslutningar om din primära plats blir otillgänglig.

  Anta till exempel att din primära Logic-App ansluter till en extern tjänst, till exempel Salesforce. Normalt är den externa tjänstens tillgänglighet och plats oberoende av din Logic Apps tillgänglighet. I det här fallet kan den sekundära instansen ansluta till samma tjänst men bör ha en egen anslutning.

* Både din primära instans och beroende resurser finns på samma plats. I det här fallet bör beroende resurser ha säkerhets kopior eller replikerade versioner på en annan plats så att den sekundära instansen fortfarande kan komma åt dessa resurser.

  Anta till exempel att din primära Logic-App ansluter till en tjänst som finns på samma plats eller region, till exempel Azure SQL Database. Om hela regionen blir otillgänglig är Azure SQL Database tjänsten i den regionen också troligt vis otillgänglig. I det här fallet vill du att den sekundära instansen ska använda en replikerad databas eller en säkerhets kopie rad databas tillsammans med en separat anslutning till databasen.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>Lokala datagatewayer

Om din Logic App körs i Azure med flera innehavare och behöver åtkomst till lokala resurser som SQL Server-databaser, måste du installera den lokala [datagatewayen](../logic-apps/logic-apps-gateway-install.md) på en lokal dator. Du kan sedan skapa en data gateway-resurs i Azure Portal så att din Logic-app kan använda gatewayen när du skapar en anslutning till resursen.

Data Gateway-resursen är associerad med en plats eller Azure-region, precis som din Logic app-resurs. I din strategi för haveri beredskap kontrollerar du att datagatewayen fortfarande är tillgänglig för din Logic app. Du kan [Aktivera hög tillgänglighet för din gateway](../logic-apps/logic-apps-gateway-install.md#high-availability) när du har flera gateway-installationer.

> [!NOTE]
> Om din Logic App körs i en integrerings tjänst miljö (ISE) och endast använder ISE-versioner av anslutningar för lokala data källor behöver du inte data gatewayen eftersom ISE-kopplingar ger direkt åtkomst till den lokala resursen.
>
> Om ingen ISE-versions koppling är tillgänglig för den lokala resurs som du vill ha, kan din Logic app fortfarande skapa anslutningen med hjälp av en icke-ISE-koppling, som körs i den globala Azure-klienten, inte av ISE. Den här anslutningen kräver dock den lokala datagatewayen.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Aktiva och aktiva kontra aktiva-passiva roller

Du kan ställa in dina primära och sekundära platser så att dina Logic App-instanser på dessa platser kan spela dessa roller:

| Primär-sekundär roll | Beskrivning |
|------------------------|-------------|
| *Aktiv-aktiv* | De primära och sekundära Logic app-instanserna på båda platserna hanterar begär Anden aktivt genom att följa något av följande mönster: <p><p>- *Belastnings utjämning*: du kan låta båda instanserna Lyssna på en slut punkt och belastningsutjämna trafik till varje instans vid behov. <p>- *Konkurrerande konsumenter*: du kan låta båda instanserna fungera som konkurrerande konsumenter så att instanserna konkurrerar om meddelanden från en kö. Om en instans Miss lyckas tar den andra instansen över arbets belastningen. |
| *Aktiv-passiv* | Den primära Logic App-instansen hanterar aktivt hela arbets belastningen medan den sekundära instansen är passiv (inaktive rad eller inaktiv). Den sekundära väntar på en signal som den primära inte är tillgänglig eller inte fungerar på grund av avbrott eller haveri och tar över arbets belastningen som den aktiva instansen. |
| Tangentkombination | Vissa Logic Apps spelar en aktiv-aktiv roll medan andra Logic Apps spelar en aktiv-passiv roll. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Aktiva – aktiva exempel

I de här exemplen visas den aktiva-aktiva installationen där båda Logic app-instanserna hanterar begär Anden eller meddelanden aktivt. Vissa andra system och tjänster distribuerar begär Anden eller meddelanden mellan instanser, till exempel ett av följande alternativ:

* En "fysisk" belastningsutjämnare, till exempel en maskin varu enhet som dirigerar trafik

* En mjuk belastningsutjämnare som [Azure Load Balancer](../load-balancer/load-balancer-overview.md) eller [Azure API Management](../api-management/api-management-key-concepts.md). Med API Management kan du ange principer för att belastningsutjämna inkommande trafik. Du kan också använda en tjänst som har stöd för tillstånds spårning, till exempel [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

  Även om det här exemplet främst visar Azure Load Balancer, kan du använda det alternativ som bäst passar ditt scenarios behov:

  !["Aktiv-aktiv"-installation som använder en belastningsutjämnare eller tillstånds känslig tjänst](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Varje Logic App-instans fungerar som en konsument och båda instanserna konkurrerar om meddelanden från en kö:

  !["Aktiv-aktiv"-installation som använder "konkurrerande konsumenter"](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Aktiva – passiva exempel

I det här exemplet visas den aktiva passiva installationen där den primära Logic App-instansen är aktiv på en plats, medan den sekundära instansen förblir inaktiv på en annan plats. Om det uppstår ett avbrott eller haveri i den primära upplevelsen kan du köra ett skript som aktiverar den sekundära som ska utföras på arbets belastningen.

!["Aktiv-passiv"-installation som använder "konkurrerande konsumenter"](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Kombination med aktiv-aktiv och aktiv-passiv

I det här exemplet visas en kombinerad installation där den primära platsen har både aktiva Logic App-instanser, medan den sekundära platsen har aktiva-passiva Logic App-instanser. Om den primära platsen upplever ett avbrott eller haveri, kan den aktiva Logic-appen på den sekundära platsen, som redan hanterar en delvis arbets belastning, ta över hela arbets belastningen.

* På den primära platsen lyssnar en aktiv Logic-app till en Azure Service Bus kö för meddelanden, medan en annan aktiv Logic-app söker efter e-postmeddelanden med hjälp av en Office 365 Outlook-avsöknings utlösare.

* På den sekundära platsen fungerar en aktiv Logic-app med Logic-appen på den primära platsen genom att lyssna och konkurrera om meddelanden från samma Service Bus kö. Samtidigt väntar en passiv inaktiv Logic app på vänte läge för att söka efter e-postmeddelanden när den primära platsen blir otillgänglig men är *inaktive rad* för att förhindra att e-postmeddelanden läses om.

![Kombinationen "aktiv-passiv" och "aktiv-passiv" som använder upprepnings utlösare](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Status och historik för Logic app

När din Logi Kap par utlöses och börjar köras, lagras appens tillstånd på samma plats som appen startade och kan inte överföras till en annan plats. Om ett haveri inträffar eller om det uppstår avbrott överges alla pågående arbets flödes instanser. När du har konfigurerat primära och sekundära platser börjar nya arbets flödes instanser att köras på den sekundära platsen.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Minska antalet övergivna instanser

För att minimera antalet övergivna arbets flödes instanser kan du välja mellan olika meddelande mönster som du kan implementera, till exempel:

* [Mönster för fast cirkulations lista](/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Detta företags meddelande mönster som delar upp en affärs process i mindre steg. För varje steg ställer du in en Logi Kap par som hanterar arbets belastningen för det steget. För att kommunicera med varandra använder dina Logic Apps ett asynkront meddelande protokoll som Azure Service Bus köer eller ämnen. När du delar upp en process i mindre steg minskar du antalet affärs processer som kan ha fastnat på en misslyckad Logic App-instans. Mer allmän information om det här mönstret finns i [företags integrations mönster – cirkulations lista](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html).

  I det här exemplet visas ett cirkulations mönster där varje Logic app representerar ett stadium och använder en Service Bus kö för att kommunicera med nästa Logic-app i processen.

  ![Dela upp en affärs process i steg som representeras av Logic Apps, som kommunicerar med varandra med hjälp av Azure Service Bus köer](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Om både den primära och sekundära Logic app-instanserna följer samma cirkulations mönster på deras platser, kan du implementera det [konkurrerande konsument mönstret](/azure/architecture/patterns/competing-consumers) genom att ställa in [aktiva roller](#roles) för dessa instanser.

* [Process hanterarens (Broker) mönster](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Spetsig/lås utan tids gräns mönster](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Åtkomst till utlösare och körnings historik

Om du vill ha mer information om din Logic Apps tidigare arbets flödes körningar kan du granska appens utlösare och köra historik. En Logic Apps historik för körning av historik lagras på samma plats eller region där Logic app kördes, vilket innebär att du inte kan migrera den här historiken till en annan plats. Om den primära instansen växlar över till en sekundär instans kan du bara komma åt varje instanss utlösare och köra historik på respektive platser där dessa instanser kördes. Du kan dock få plats-oberoende information om din Logic Apps historik genom att konfigurera dina Logi Kap par för att skicka diagnostiska händelser till en Azure Log Analytics-arbetsyta. Du kan sedan granska hälso tillståndet och historiken i Logic Apps som körs på flera platser.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Vägledning för utlösnings typ

Den utlösare typ som du använder i Logi Kap par bestämmer dina alternativ för hur du kan ställa in Logi Kap par mellan platser i din strategi för katastrof återställning. Här följer de tillgängliga utlösnings typerna som du kan använda i Logic Apps:

* [Upprepnings utlösare](#recurrence-trigger)
* [Avsöknings utlösare](#polling-trigger)
* [Begär utlösare](#request-trigger)
* [Webhook-utlösare](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Upprepnings utlösare

**Upprepnings** utlösaren är oberoende av en specifik tjänst eller slut punkt och utlöses enbart baserat på ett angivet schema och inga andra kriterier, till exempel:

* En fast frekvens och intervall, till exempel var 10: e minut
* Ett mer avancerat schema, till exempel sista måndagen i varje månad på 5:00 PM

När din Logi Kap par börjar med en återkommande utlösare måste du konfigurera dina primära och sekundära Logic App-instanser med [aktiva passiva roller](#roles). För att minska *återställnings tiden* (RTO), som avser mål varaktigheten för att återställa en affärs process efter ett avbrott eller haveri, kan du konfigurera dina Logic App-instanser med en kombination av [aktiva passiva roller](#roles) och [passiva aktiva roller](#roles). I den här konfigurationen delar du schemat mellan platser.

Anta till exempel att du har en logisk app som måste köras var tionde minut. Du kan ställa in Logi Kap par och platser så att om den primära platsen blir otillgänglig kan den sekundära platsen ta över arbetet:

![Kombinationen "aktiv-passiv" och "passiv-aktiv" som använder upprepnings utlösare](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* På den primära platsen ställer du in [aktiva-passiva roller](#roles) för dessa Logic Apps:

  * För den *aktiva* aktiverade Logic-appen ställer du in upprepnings utlösaren så att den startar längst upp i timmen och upprepas var 20: e minut, till exempel 9:00, 9:20 am, och så vidare.

  * För den *passiva* inaktiverade Logic-appen ställer du in upprepnings utlösaren till samma schema, men startar vid 10 minuter efter timmen och upprepar var 20: e minut, till exempel 9:10, 9:30 am, och så vidare.

* På den sekundära platsen konfigurerar du [passiv-aktiv](#roles) för dessa Logic Apps:

  * För den *passiva* inaktiverade Logic-appen ställer du in upprepnings utlösaren på samma schema som den aktiva Logic-appen på den primära platsen, som är högst upp i timmen och upprepar var 20: e minut, till exempel 9:00, 9:10 och så vidare.

  * För den *aktiva* aktiverade Logic-appen ställer du in upprepnings utlösaren på samma schema som den passiva Logic-appen på den primära platsen, vilket är att starta vid 10 minuter efter timmen och upprepa var 20: e minut, till exempel 9:10, 9:20 am och så vidare.

Om en störnings händelse inträffar på den primära platsen aktiverar du den passiva Logic-appen på den alternativa platsen. På så sätt begränsar den här installationen antalet missade upprepningar under den tiden, om det tar tid att hitta felet.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Avsöknings utlösare

För att regelbundet kontrol lera om nya data för bearbetning är tillgängliga från en viss tjänst eller slut punkt, kan din Logic-app använda en *avsöknings* utlösare som anropar tjänsten eller slut punkten upprepade gånger baserat på ett fast upprepnings schema. De data som tjänsten eller slut punkten erbjuder kan ha någon av följande typer:

* Statiska data, som beskriver data som alltid är tillgängliga för läsning
* Temporära data, som beskriver data som inte längre är tillgängliga efter läsning

För att undvika att flera gånger läser samma data måste din Logic app komma ihåg vilka data som tidigare har lästs genom att underhålla tillstånden antingen på klient sidan eller på servern, tjänsten eller system sidan.

* Logi Kap par som arbetar med tillstånd på klient sidan använder utlösare som kan underhålla tillstånd.

  En utlösare som läser ett nytt meddelande från en e-postinkorg kräver till exempel att utlösaren kan komma ihåg det senast lästa meddelandet. På så sätt startar utlösaren Logic app endast när nästa olästa meddelande anländer.

* Logi Kap par som fungerar med Server-, tjänst-eller system sidan använder egenskaps värden eller inställningar som finns på servern, tjänsten eller system sidan.

  Till exempel kräver en frågetyp-baserad utlösare som läser en rad från en databas att raden har en `isRead` kolumn som är inställd på `FALSE` . Varje gång som utlösaren läser en rad uppdaterar Logic-appen raden genom `isRead` att ändra kolumnen från `FALSE` till `TRUE` .

  Den här metoden fungerar på samma sätt för Service Bus köer eller ämnen som innehåller köer där en utlösare kan läsa och låsa ett meddelande medan Logic app bearbetar meddelandet. När Logic-appen slutför bearbetningen tar utlösaren bort meddelandet från kön eller ämnet.

När du ställer in din Logic Apps primära och sekundära instanser i ett haveri beredskaps perspektiv måste du se till att du har ett konto för dessa beteenden baserat på om din Logic app spårar tillstånd på klient sidan eller på Server sidan:

* För en Logi Kap par som arbetar med tillstånd på klient sidan, se till att din Logic app inte läser samma meddelande mer än en stund. Endast en plats kan ha en aktiv Logic App-instans vid en angiven tidpunkt. Se till att Logic App-instansen på den alternativa platsen är inaktiv eller inaktive rad tills den primära instansen växlar över till den alternativa platsen.

  Office 365 Outlook-utlösaren hanterar till exempel klient sidans tillstånd och spårar tidsstämpeln för den senast lästa e-postmeddelandet för att undvika att läsa en dubblett.

* För en Logic app som fungerar med Server sidans tillstånd kan du konfigurera dina Logic App-instanser för att spela upp [aktiva roller](#roles) där de fungerar som konkurrerande konsumenter eller [aktiva passiva roller](#roles) där den alternativa instansen växlar till den alternativa platsen.

  Om du till exempel läser från en meddelandekö, till exempel en Azure Service Bus kö, använder Server sidans tillstånd eftersom tjänsten Queuing underhåller lås meddelanden för att förhindra att andra klienter läser samma meddelanden.

  > [!NOTE]
  > Om din Logi Kap par behöver läsa meddelanden i en viss ordning, till exempel från en Service Bus kö, kan du använda det konkurrerande konsument mönstret men bara när det kombineras med Service Bus-sessioner, som även kallas för [ *sekventiella konvojmönster* -mönster](/azure/architecture/patterns/sequential-convoy). Annars måste du konfigurera dina Logic App-instanser med aktiva passiva roller.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Begär utlösare

Utlösaren för **förfrågningar** gör att din Logic app kan anropas från andra appar, tjänster och system och används vanligt vis för att tillhandahålla dessa funktioner:

* En direkt REST API för din Logic app som andra kan anropa

  Använd exempelvis utlösaren för begäran för att starta din Logi Kap par så att andra Logi Kap par kan anropa utlösaren med hjälp av åtgärden **anropa arbets flöde – Logic Apps** .

* En [webhook](#webhook-trigger) eller callback-metod för din Logic app

* Ett sätt att köra användar åtgärder eller rutiner manuellt för att anropa din Logic app, till exempel genom att använda ett PowerShell-skript som utför en speciell uppgift

Från ett haveri beredskaps perspektiv är utlösaren för begäran en passiv mottagare eftersom Logic app inte gör något arbete och väntar tills en annan tjänst eller ett system explicit anropar utlösaren. Som passiv slut punkt kan du konfigurera dina primära och sekundära instanser på följande sätt:

* [Aktiv-aktiv](#roles): båda instanserna hanterar begär Anden eller anrop aktivt. Anroparen eller routern balanserar eller distribuerar trafik mellan dessa instanser.

* [Aktiv-passiv](#roles): endast den primära instansen är aktiv och hanterar allt arbete, medan den sekundära instansen väntar tills det uppstår avbrott eller haveri. Anroparen eller routern bestämmer när den sekundära instansen ska anropas.

Som en rekommenderad arkitektur kan du använda Azure API Management som proxy för de Logi Kap par som använder begär ande utlösare. API Management tillhandahåller [inbyggd över-regional återhämtning och möjlighet att dirigera trafik över flera slut punkter](../api-management/api-management-howto-deploy-multi-region.md).

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Webhook-utlösare

En *webhook* -utlösare gör det möjligt för din Logic app att prenumerera på en tjänst genom att skicka en *callback-URL* till den tjänsten. Din Logi Kap par kan sedan lyssna och vänta på att en händelse ska inträffa vid den tjänst slut punkten. När händelsen inträffar anropar tjänsten webhook-utlösaren med hjälp av återanrops-URL: en och kör sedan Logic-appen. När den är aktive rad utlöser webhooken prenumerationen på tjänsten. När den är inaktive rad avbryter utlösaren från tjänsten.

I ett haveri beredskaps perspektiv ställer du in primära och sekundära instanser som använder webhook-utlösare för att spela upp aktiva-passiva roller eftersom endast en instans ska ta emot händelser eller meddelanden från den prenumererade slut punkten.

## <a name="assess-primary-instance-health"></a>Utvärdera primär instans hälsa

För att din strategi för haveri beredskap ska fungera behöver din lösning olika sätt att utföra dessa uppgifter:

* [Kontrol lera tillgänglighet för den primära instansen](#check-primary-availability)
* [Övervaka den primära instansens hälsa](#monitor-primary-health)
* [Aktivera den sekundära instansen](#activate-secondary)

I det här avsnittet beskrivs en lösning som du kan använda direkt till höger eller som en grund för din egen design. Här är en översikt på hög nivå för den här lösningen:

![Skapa en Logic-app i övervaknings övervakaren som övervakar hälso-och sjukvårds program på den primära platsen](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Kontrol lera tillgänglighet för primär instans

För att avgöra om den primära instansen är tillgänglig, körs och kan fungera, kan du skapa en "Health-Check"-logisk app som finns på samma plats som den primära instansen. Du kan sedan anropa den här appen för hälso kontroll från en annan plats. Om hälso kontrollen av appen svarar, är den underliggande infrastrukturen för den Azure Logic Apps tjänsten i regionen tillgänglig och fungerar. Om hälso kontrollen av appen inte svarar kan du anta att platsen inte längre är felfri.

För den här aktiviteten skapar du en grundläggande hälso-och kontroll logik app som utför dessa uppgifter:

1. Tar emot ett samtal från övervaknings-appen med hjälp av begär ande utlösare.

1. Svara med en status som anger om den markerade Logic-appen fortfarande fungerar med hjälp av svars åtgärden.

   > [!IMPORTANT]
   > Health-kontrol lera Logic-appen måste använda en svars åtgärd så att appen svarar synkront, inte asynkront.

1. Om du vill ta reda på om den primära platsen är felfri, kan du bedöma hälso tillståndet för andra tjänster som interagerar med mål logiks appen på den här platsen. Du kan bara expandera hälso appen för hälso kontroll för att utvärdera hälsan för dessa andra tjänster.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Skapa en övervaknings-Logic-app

Om du vill övervaka den primära instansens hälso tillstånd och anropa Health-kontrol lera Logic-appen skapar du en "övervaknings logik app" på en *annan plats*. Du kan till exempel konfigurera appen övervaknings logik så att om du anropar hälso kontrolls logiken Miss lyckas, kan övervaknings enheten skicka en avisering till din drifts grupp så att de kan undersöka felen och varför den primära instansen inte svarar.

> [!IMPORTANT]
> Se till att din övervaknings-Logic-app finns på en *plats som skiljer sig från den primära platsen*. Om Logic Apps tjänsten på den primära platsen upplever problem kanske din övervaknings-Logic-app inte körs.

För den här aktiviteten, på den sekundära platsen, skapar du en övervaknings-Logic-app som utför dessa uppgifter:

1. Körs baserat på en fast eller schemalagd upprepning genom att använda upprepnings utlösaren.

   Du kan ställa in upprepningen på ett värde som ligger under tolerans nivån för ditt återställnings tids mål (RTO).

1. Anropa appen hälso tillstånds kontroll på den primära platsen genom att använda HTTP-åtgärden, till exempel:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>Aktivera din sekundära instans

Om du vill aktivera den sekundära instansen automatiskt kan du skapa en Logic-app som anropar hanterings-API: t, till exempel [Azure Resource Manager-anslutningen](/connectors/arm/) för att aktivera lämpliga Logi Kap par på den sekundära platsen. Du kan expandera din övervaknings-app för att anropa den här appen för aktiverings logik när ett angivet antal problem inträffar.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Samla in diagnostikdata

Du kan ställa in loggning för din Logi Kap par körning och skicka de resulterande diagnostikdata till tjänster som Azure Storage, Azure Event Hubs och Azure Log Analytics för ytterligare hantering och bearbetning.

* Om du vill använda dessa data med Azure Log Analytics kan du göra data tillgängliga för både den primära och den sekundära platsen genom att ställa in din Logic Apps **diagnostikinställningar** och skicka data till flera Log Analytics arbets ytor. Mer information finns i [konfigurera Azure Monitor loggar och samla in diagnostikdata för Azure Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md).

* Om du vill skicka data till Azure Storage eller Azure Event Hubs kan du göra data tillgängliga för både den primära och sekundära platsen genom att ställa in GEO-redundans. Mer information finns i de här artiklarna:<p>

  * [Återställning av Azure Blob Storage haveri beredskap och konto redundans](../storage/common/storage-disaster-recovery-guidance.md)
  * [Azure Event Hubs geo-katastrof återställning](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Nästa steg

* [Återhämtnings översikt för Azure](/azure/architecture/framework/resiliency/overview)
* [Checklista för återhämtning för specifika Azure-tjänster](/azure/architecture/checklist/resiliency-per-service)
* [Data hantering för återhämtning i Azure](/azure/architecture/framework/resiliency/data-management)
* [Säkerhets kopiering och haveri beredskap för Azure-program](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Återställa från regionomfattande tjänststörningar](/azure/architecture/resiliency/recovery-loss-azure-region)
* [Microsofts Service nivå avtal (service avtal) för Azure-tjänster](https://azure.microsoft.com/support/legal/sla/)
