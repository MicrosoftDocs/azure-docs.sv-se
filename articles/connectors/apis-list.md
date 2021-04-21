---
title: Anslutningsprogram för Azure Logic Apps
description: Översikt över anslutningsappar för att skapa automatiserade arbetsflöden med Azure Logic Apps. Lär dig hur olika typer av anslutningsappar, utlösare och åtgärder fungerar.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 9e138cb7fb591728e7bc6a02ff61b3167d13da30
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771361"
---
# <a name="connectors-for-azure-logic-apps"></a>Anslutningsprogram för Azure Logic Apps

I Azure Logic Apps *kan du* med anslutningsappar snabbt komma åt händelser, data och åtgärder från andra appar, tjänster, system, protokoll och plattformar. Du kan ofta utföra dessa uppgifter utan ytterligare kod. Du kan också använda anslutningsappar för Logic Apps skapa arbetsflöden som använder den här informationen i molnbaserade, lokala eller hybridmiljöer.

Det finns hundratals tillgängliga anslutningsappar för Logic Apps. Därför fokuserar den här dokumentationen på några av de mest populära och vanliga anslutningsapparna för Logic Apps. Fullständig information om anslutningsappar i Logic Apps, Microsoft Power Automate och Microsoft Power Apps finns i [dokumentationen om anslutningsappar.](/connectors) 

Information om priser finns i Logic Apps [och i](../logic-apps/logic-apps-pricing.md) [Logic Apps prisinformation.](https://azure.microsoft.com/pricing/details/logic-apps/)

Om du vill integrera logikappen med en tjänst eller ett API som inte har en anslutningsapp kan du antingen anropa tjänsten via ett protokoll, till exempel HTTP, eller skapa en [anpassad anslutningsapp](#custom-apis-and-connectors).

## <a name="what-are-connectors"></a>Vad är anslutningsappar?

I Azure Logic Apps tillhandahåller anslutningsappar *utlösare och* *åtgärder som* du använder för att utföra uppgifter i logikappens arbetsflöde. Varje utlösare och åtgärd har egenskaper som du kan konfigurera. Vissa utlösare och åtgärder kräver att du [skapar och konfigurerar anslutningar](#connection-configuration) så att arbetsflödet kan komma åt en specifik tjänst eller ett visst system.

### <a name="triggers"></a>Utlösare

En *utlösare* är alltid det första steget i ett arbetsflöde, som anger den händelse som startar arbetsflödet. Det finns flera typer av utlösare:

- *Avsökningsutlösare* kontrollerar regelbundet en viss tjänst eller ett visst system enligt ett angivet schema för att söka efter nya data eller en specifik händelse. Om nya data är tillgängliga, eller om den specifika händelsen inträffar, skapar och kör dessa utlösare en ny instans av arbetsflödet. Den här nya instansen kan sedan använda de data som skickas som indata.
- *Push-utlösare* lyssnar efter nya data eller om en händelse ska inträffa, utan avsökning. När nya data är tillgängliga, eller när händelsen inträffar, skapar och kör dessa utlösare en ny instans av logikappen. Den nya instansen kan sedan använda de data som skickas som indata.

Du kanske till exempel vill skapa ett arbetsflöde som gör något när en fil laddas upp till FTP-servern. Du kan lägga till FTP-anslutningsutlösaren **med namnet När en fil läggs till eller ändras** som det första steget i arbetsflödet. Du kan sedan ange ett schema för att regelbundet söka efter uppladdningshändelser.

En utlösare skickar även eventuella indata och andra nödvändiga data till arbetsflödet där senare åtgärder kan referera till och använda dessa data i hela arbetsflödet. Du kanske till exempel vill använda Office 365 Outlook-utlösaren med namnet **När ett nytt e-postmeddelande kommer**. Du kan konfigurera den här utlösaren så att den skickar vidare innehållet från varje nytt e-postmeddelande, till exempel avsändare, ämnesrad, brödtext, bifogade filer och så vidare. Sedan kan du bearbeta informationen i logikappen med hjälp av åtgärder.

### <a name="actions"></a>Åtgärder

En *åtgärd* är en åtgärd som följer utlösaren och utför någon typ av uppgift i arbetsflödet. Du kan använda flera åtgärder i logikappen. Du kan till exempel ha en SQL-utlösare som identifierar nya kunddata i en SQL-databas. Arbetsflödet kan innehålla en första SQL-åtgärd som hämtar kunddata, följt av en annan åtgärd som inte nödvändigtvis är en SQL-åtgärd, som bearbetar data.

## <a name="connector-categories"></a>Kategorier för anslutningsappar

I Logic Apps finns det vanligtvis inbyggda eller hanterade anslutningsversioner av utlösare och åtgärder. Ett litet antal utlösare och åtgärder är tillgängliga i båda versionerna. De specifika versionerna beror på om du skapar en logikapp för flera innehavare eller en logikapp för en enskild klientorganisation, som för närvarande endast är tillgänglig [i Logic Apps Förhandsversion.](../logic-apps/logic-apps-overview-preview.md)

[Inbyggda utlösare och åtgärder](built-in.md) körs inbyggt på Logic Apps runtime, kräver inte att anslutningar skapas och utför den här typen av uppgifter:

- [Kör kod i dina arbetsflöden](built-in.md#run-code-from-workflows).
- [Organisera och kontrollera dina data](built-in.md#control-workflow).
- [Hantera eller manipulera data](built-in.md#manage-or-manipulate-data).

[Hanterade anslutningsappar](managed.md) distribueras, hanteras och hanteras av Microsoft. Dessa anslutningsappar tillhandahåller utlösare och åtgärder för molntjänster, lokala system eller både och. Dessa omfattar:

- [Lokala anslutningsappar som hjälper](managed.md#on-premises-connectors) dig att komma åt data och resurser i lokala system.
- [Enterprise-anslutningsappar](managed.md#enterprise-connectors), som är versioner av Logic Apps anslutningsappar som ger åtkomst till företagssystem.
- [Anslutningsappar för integrationskonto](managed.md#integration-account-connectors)som stöder B2B-kommunikationsscenarier (business-to-business).
- [Integration Service Environment (ISE) som är](managed.md#ise-connectors) en liten grupp hanterade anslutningsappar som [endast är tillgängliga för ISE:er.](#ise-and-connectors)

## <a name="connection-configuration"></a>Anslutningskonfiguration

De flesta anslutningsappar kräver att du först skapar *en* anslutning till måltjänsten eller målsystemet innan du kan använda dess utlösare eller åtgärder i logikappen. Om du vill skapa en anslutning måste du autentisera din identitet med kontoautentiseringsuppgifter och ibland annan anslutningsinformation. Innan arbetsflödet till exempel kan komma åt och arbeta med ditt Office 365 Outlook-e-postkonto i en logikapp måste du auktorisera en anslutning till det kontot.

För anslutningsappar som använder Azure Active Directory (Azure AD) OAuth, till exempel Office 365, Salesforce eller GitHub, [](../security/fundamentals/encryption-overview.md) måste du logga in på tjänsten där din åtkomsttoken krypteras och lagras på ett säkert sätt i en Azure-hemlighet. Andra anslutningsappar, till exempel FTP och SQL, kräver en anslutning som har konfigurationsinformation, till exempel serveradress, användarnamn och lösenord. Informationen om anslutningskonfigurationen [krypteras också och lagras på ett säkert sätt i Azure](../security/fundamentals/encryption-overview.md).

Etablerade anslutningar kan komma åt måltjänsten eller målsystemet så länge tjänsten eller systemet tillåter det. För tjänster som använder Azure AD OAuth-anslutningar, till exempel Office 365 och Dynamics, Logic Apps åtkomsttoken på obestämd tid. Andra tjänster kan ha begränsningar för hur länge tjänsten Logic Apps kan använda en token utan uppdatering. I allmänhet ogiltigförklarar vissa åtgärder, till exempel att ändra ditt lösenord, alla åtkomsttoken.

Även om du skapar anslutningar inifrån ett arbetsflöde är anslutningar separata Azure-resurser med egna resursdefinitioner. Om du vill granska definitionerna för [anslutningsresursen laddar du ned logikappen från Azure till Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md). Den här metoden är det enklaste sättet att skapa en giltig, parametriserad logikappmall som främst är redo för distribution.

> [!TIP]
> Om din organisation inte tillåter att du får åtkomst till specifika [](../logic-apps/block-connections-connectors.md) resurser via Logic Apps anslutningsappar kan du blockera möjligheten att skapa sådana anslutningar med [hjälp av Azure Policy](../governance/policy/overview.md).

## <a name="recurrence-behavior"></a>Upprepningsbeteende

Återkommande inbyggda utlösare, [](../connectors/connectors-native-recurrence.md)till exempel upprepningsutlösaren, körs inbyggt i Azure Logic Apps och skiljer sig från återkommande anslutningsbaserade utlösare, till exempel utlösaren för Office 365 Outlook-anslutningsappen, där du måste skapa en anslutning först.

För båda typerna av utlösare gäller att om en upprepning inte anger ett specifikt startdatum och en viss tid, körs den första upprepningen omedelbart när du sparar eller distribuerar logikappen, trots utlösarens upprepningskonfiguration. För att undvika det här beteendet anger du ett startdatum och en tid för när du vill att den första upprepningen ska köras.

### <a name="recurrence-for-built-in-triggers"></a>Upprepning för inbyggda utlösare

Återkommande inbyggda utlösare följer det schema som du anger, inklusive en angiven tidszon. Men om en upprepning inte anger andra avancerade schemaläggningsalternativ, till exempel specifika tider för att köra framtida upprepningar, baseras dessa upprepningar på den senaste utlösarkörningen. Därför kan starttiderna för dessa upprepningar fördröjs på grund av faktorer som svarstid under lagringsanrop. Felsökningshjälp finns i avsnittet [om upprepningsproblem.](#recurrence-issues)

### <a name="recurrence-for-connection-based-triggers"></a>Upprepning för anslutningsbaserade utlösare

I återkommande anslutningsbaserade utlösare, till exempel Office 365 Outlook, är schemat inte den enda drivrutinen som styr körningen. Tidszonen avgör endast den första starttiden. Efterföljande körningar beror på upprepningsschemat, den senaste utlösarkörningen och andra faktorer som kan orsaka körningstider för att avgå eller generera oväntat beteende. Dessa omfattar:

- Om utlösaren har åtkomst till en server som har mer data, vilket utlösaren omedelbart försöker hämta.
- Eventuella fel eller återförsök som utlöses av utlösaren.
- Svarstid under lagringssamtal.
- Underhåller inte det angivna schemat när sommartid (DST) startar och slutar.
- Andra faktorer som kan påverka när nästa körning sker.

Felsökningshjälp finns i [avsnittet Upprepningsproblem.](#recurrence-issues) 

### <a name="recurrence-issues"></a>Upprepningsproblem

Prova följande lösningar för att se till att arbetsflödet körs vid den angivna starttiden och inte missar en upprepning, särskilt när frekvensen är i dagar eller längre.

När DST har verkställts justerar du upprepningen manuellt så att arbetsflödet fortsätter att köras vid förväntad tid. Annars flyttas starttiden en timme framåt när DST startar och en timme bakåt när DST slutar. Mer information och exempel finns i [Upprepning för sommartid och standardtid.](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time)

Om du använder en **upprepningsutlösare** anger du en tidszon, ett startdatum och en tid. Konfigurera dessutom specifika tider för att köra  efterföljande upprepningar i egenskaperna Vid dessa timmar och Vid dessa minuter **,** som endast är tillgängliga för **dag-** och **veckofrekvenserna.** Vissa tidsfönster kan dock fortfarande orsaka problem när tiden skiftar. 

Överväg att använda en [ **utlösare för skjutfönster**](../connectors/connectors-native-sliding-window.md) i stället för en **upprepningsutlösare** för att undvika missade upprepningar.

## <a name="custom-apis-and-connectors"></a>Anpassade API:er och anslutningsappar

Om du vill anropa API:er som kör anpassad kod eller inte är tillgängliga som anslutningsappar kan du utöka Logic Apps-plattformen genom att [skapa anpassade API Apps](../logic-apps/logic-apps-create-api-app.md). 

Du kan också [skapa anpassade anslutningsappar för](../logic-apps/custom-connector-overview.md) rest- eller SOAP-baserade API:er, vilket gör dessa API:er tillgängliga för alla logikappar i din Azure-prenumeration. 

Om du vill göra anpassade API Apps eller anslutningsappar offentliga så att alla kan använda dem i Azure kan du skicka in [anslutningsappar för Microsoft-certifiering.](/connectors/custom-connectors/submit-certification)

## <a name="ise-and-connectors"></a>ISE och anslutningsappar

För arbetsflöden som behöver direkt åtkomst till resurser i ett virtuellt Azure-nätverk kan du skapa en dedikerad [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) där du kan skapa, distribuera och köra dina arbetsflöden på dedikerade resurser. Mer information om hur du skapar ISE:er finns [i Ansluta till virtuella Azure-nätverk från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

Anpassade anslutningsappar som skapats i en ISE fungerar inte med den lokala datagatewayen. Dessa anslutningsappar kan dock direkt komma åt lokala datakällor som är anslutna till ett virtuellt Azure-nätverk som är värd för ISE. Logikappar i en ISE behöver troligen inte datagatewayen vid kommunikation med dessa resurser. Om du har anpassade anslutningsappar som du har skapat utanför en ISE som kräver den lokala datagatewayen kan logikappar i en ISE använda dessa anslutningsappar.

När du i Logic Apps Designer bläddrar bland de anslutningsappar som du vill använda för logikappar i en ISE, visas en **CORE-etikett** på inbyggda utlösare och åtgärder, medan **ISE-etiketten** visas på vissa anslutningsappar.

:::row:::
    :::column:::
        ![Exempel på CORE-anslutning](./media/apis-list/example-core-connector.png)
        \
        \
        **Core**
        \
        \
        Inbyggda utlösare och åtgärder med den här etiketten körs i samma ISE som dina logikappar.
    :::column-end:::
    :::column:::
        ![Exempel på ISE-anslutning](./media/apis-list/example-ise-connector.png)
        \
        \
        **Ise**
        \
        \
        Hanterade anslutningsappar med den här etiketten körs i samma ISE som dina logikappar. Om du har ett lokalt system som är anslutet till ett virtuellt Azure-nätverk kan dina logikappar få direkt åtkomst till systemet utan den lokala [datagatewayen](../logic-apps/logic-apps-gateway-connection.md). I stället kan du antingen använda systemets **ISE-anslutning** om det är tillgängligt, en HTTP-åtgärd eller en [anpassad anslutningsapp.](connectors-overview.md#custom-apis-and-connectors) För lokala system som inte har **ISE-anslutningsappar** använder du en lokal datagateway. Information om tillgängliga ISE-anslutningsappar finns i [ISE-anslutningsappar.](#ise-and-connectors)
    :::column-end:::
    :::column:::
        ![Exempel på anslutning för flera innehavare](./media/apis-list/example-multi-tenant-connector.png)
        \
        \
        Ingen etikett     \
        \
        Alla andra anslutningsappar utan **CORE-** eller **ISE-etiketten,** som du kan fortsätta att använda, körs i den globala tjänsten för Logic Apps klientorganisation.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="known-issues"></a>Kända problem

Följande är kända problem för Logic Apps anslutningsappar.

#### <a name="error-badgateway-client-request-id-guid"></a>Fel: BadGateway. Id för klientbegäran: {GUID}

Det här felet beror på att taggarna uppdateras i en logikapp där en eller flera anslutningar inte stöder Azure Active Directory-autentisering (Azure AD) OAuth, till exempel SFTP ad SQL, vilket bryter anslutningarna. Undvik att uppdatera dessa taggar för att förhindra det här beteendet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa anpassade API:er som du kan anropa från Logic Apps](/logic-apps/logic-apps-create-api-app)
