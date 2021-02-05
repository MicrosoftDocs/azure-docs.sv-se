---
title: Ansluta till 3270-appar i IBM-stordatorer
description: Integrera och automatisera 3270-skärm drivna appar med Azure med hjälp av Azure Logic Apps och IBM 3270-anslutning
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 02/03/2021
tags: connectors
ms.openlocfilehash: 7873076618125241d9bdf4f225c194572553d060
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573440"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Integrera 3270-skärm drivna appar i IBM-stordatorer med Azure med hjälp av Azure Logic Apps och IBM 3270-anslutning

Med Azure Logic Apps och IBM 3270-anslutningsprogrammet kan du få åtkomst till och köra IBM-stordatorappar som du normalt kör genom att navigera genom 3270-emulatorns skärmar. På så sätt kan du integrera IBM-stordatorappar med Azure, Microsoft och andra appar, tjänster och system genom att skapa automatiserade arbetsflöden med Azure Logic Apps. Anslutningsprogrammet kommunicerar med IBM-stordatorer med hjälp av TN3270-protokollet och är tillgängligt i alla Azure Logic Apps-regioner förutom för Azure Government och Azure China 21Vianet. Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Den här artikeln beskriver dessa aspekter för att använda 3270-anslutningen: 

* Varför ska du använda IBM 3270-anslutningen i Azure Logic Apps och hur kopplingen kör 3270-drivna appar

* Förutsättningarna och installations programmet för att använda 3270-anslutaren

* Stegen för att lägga till 3270 kopplings åtgärder i din Logic app

## <a name="why-use-this-connector"></a>Varför ska jag använda den här anslutningen?

För att komma åt appar i IBM-stordatorer använder du vanligt vis en 3270-termin Ale mula Tor som ofta kallas "grön skärm". Den här metoden är ett Tidstestat sätt men har begränsningar. Även om värd integrerings servern (hans) hjälper dig att arbeta direkt med dessa appar kan ibland det inte vara möjligt att skilja på skärmen och affärs logiken. Eller så kanske du inte längre har information om hur värd programmen fungerar.

För att utöka de här scenarierna fungerar IBM 3270-anslutaren i Azure Logic Apps med design verktyget 3270, som du använder för att registrera eller "avbilda", värd skärmarna som används för en speciell aktivitet, definierar navigerings flödet för aktiviteten via din stordator-app och definierar metoderna med indata-och utdataparametrar för aktiviteten. Design verktyget konverterar informationen till metadata som 3270-anslutningen använder vid anrop till en åtgärd som representerar uppgiften från din Logic app.

När du har genererat metadatafilen från design verktyget lägger du till filen i ett integrations konto i Azure. På så sätt kan din Logic app komma åt appens metadata när du lägger till en 3270 kopplings åtgärd. Anslutningen läser metadatafilen från ditt integrations konto, hanterar navigering via 3270-skärmarna och dynamiskt visar parametrarna för åtgärden 3270 koppling. Du kan sedan tillhandahålla data till värd programmet och kopplingen returnerar resultatet till din Logic app. På så sätt kan du integrera dina äldre appar med Azure, Microsoft och andra appar, tjänster och system som Azure Logic Apps stöder.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Rekommenderas: en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  Du kan välja den här miljön som plats för att skapa och köra din Logic app. En ISE ger åtkomst från din Logic app till resurser som skyddas i virtuella Azure-nätverk.

* Den Logic-app som ska användas för att automatisera och köra din 3270-skärm drivna app

  IBM 3270-anslutningen har inte utlösare, så Använd en annan utlösare för att starta din Logic app, till exempel **upprepnings** utlösaren. Du kan sedan lägga till 3270 kopplings åtgärder. Kom igång genom att [skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Om du använder en ISE väljer du den ISE som din Logic app-plats.

* [Hämta och installera design verktyget 3270](https://aka.ms/3270-design-tool-download).
Den enda förutsättningen är [Microsoft .NET Framework 4,8](https://aka.ms/net-framework-download).

  Med det här verktyget kan du registrera skärmar, navigerings Sök vägar, metoder och parametrar för de aktiviteter i din app som du lägger till och kör som 3270 kopplings åtgärder. Verktyget genererar en XML-fil (HIDX) för Host Integration designer som innehåller de metadata som krävs för att anslutnings tjänsten ska kunna använda för att köra din stordator-app.
  
  När du har laddat ned och installerat verktyget följer du de här stegen för att ansluta till värden:

  1. Öppna design verktyget för 3270. Från **sessionen** väljer du Host- **sessioner**.
  
  1. Ange information om din TN3270-värd Server.

* Ett [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), som är den plats där du lagrar din HIDX-fil som en karta så att din Logi Kap par kan komma åt metadata-och metod definitionerna i filen. 

  Kontrol lera att integrations kontot är länkat till den Logic app som du använder. Om du använder en ISE kontrollerar du också att integrations kontots plats är samma ISE som din Logic app använder.

* Åtkomst till TN3270-servern som är värd för din stordator-app

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Översikt över Create metadata

I en 3270-skärm driven app är skärmarna och data fälten unika för dina scenarier, så att 3270-anslutningen behöver den här informationen om din app, som du kan tillhandahålla som metadata. Dessa metadata beskriver information som hjälper din Logic app att identifiera och identifiera skärmar, beskriver hur du navigerar mellan skärmar, var du kan mata in data och var resultatet ska förväntas. Om du vill ange och generera dessa metadata använder du design verktyget 3270, som vägleder dig genom dessa speciella *lägen*, eller steg, som beskrivs senare i mer information:

* **Avbildning**: i det här läget registrerar du de skärmar som krävs för att slutföra en speciell uppgift med en stordator, till exempel, hämta ett bank saldo.

* **Navigering**: i det här läget kan du ange en plan eller sökväg för hur du navigerar genom dina stordator skärmar för den aktuella aktiviteten.

* **Metoder**: i det här läget definierar du metoden, till exempel `GetBalance` , som beskriver sökvägen till skärm navigering. Du väljer också fälten på varje skärm som blir metodens indata-och utdataparametrar.

### <a name="unsupported-elements"></a>Element som inte stöds

Design verktyget har inte stöd för dessa element:

* Partiella BMS-mappningar (IBM Basic Maps support): om du importerar en BMS-karta ignorerar design verktyget partiella skärm definitioner.

* Meny bearbetning

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Avbilda skärmar

I det här läget markerar du ett objekt på varje 3270-skärm som unikt identifierar skärmen. Du kan till exempel ange en rad med text eller en mer komplex uppsättning villkor, till exempel en speciell text och ett fält som inte är tomt. Du kan antingen spela in dessa skärmar via en Live-anslutning till värd servern eller importera informationen från en IBM Basic Map-BMS (mappning). Live-anslutningen använder en TN3270-emulator för att ansluta till värden. Varje kopplings åtgärd måste mappas till en enda aktivitet som börjar med att ansluta till din-session och avslutas med att koppla från sessionen.

1. Öppna design verktyget 3270 om du inte redan gjort det. I verktygsfältet väljer du **fånga** så att du anger bild tagnings läge.

1. Från menyn **session** väljer du **Anslut**.

1. Starta inspelningen genom att välja **Starta inspelning** på **inspelnings** menyn. (Tangent bord: Ctrl + E)

1. I fönstret **Capture** , som börjar från den första skärmen i din app, steg för steg igenom din app för den uppgift som du spelar in.

1. När du är klar med uppgiften loggar du ut från din app som vanligt.

1. Från menyn **session** väljer du **Koppla från**.

1. Om du vill stoppa inspelningen väljer du **stoppa inspelning** på **inspelnings** menyn. (Tangent bord: CTRL + SKIFT + E)

   När du har hämtat skärmarna för en aktivitet visar verktyget designer miniatyrer som representerar dessa skärmar. Några anmärkningar om de här miniatyr bilderna:

   * Som ingår i dina infångade skärmar har du en skärm med namnet "Tom".

     När du först ansluter till [CICS](https://www.ibm.com/it-infrastructure/z/cics)måste du skicka "Rensa"-nyckeln innan du kan ange namnet på den transaktion som du vill köra. Skärmen där du skickar "Rensa"-nyckeln har inga *igenkännings attribut*, till exempel en skärm rubrik, som du kan lägga till med hjälp av skärm igenkännings redigeraren. För att representera den här skärmen innehåller miniatyr bilderna en skärm med namnet "Tom". Du kan senare använda den här skärmen för att representera skärmen där du anger transaktions namnet.

   * Som standard använder namnet på en infångad skärm det första ordet på skärmen. Om det namnet redan finns lägger design verktyget till namnet med ett under streck och ett tal, till exempel "WBGB" och "WBGB_1".

1. Följ dessa steg om du vill ge ett mer meningsfullt namn till en insamlad skärm:

   1. I fönstret **värd raster** väljer du den skärm som du vill byta namn på.

   1. I samma ruta, längst ned i samma fönster, hittar du egenskapen **skärm namn** .

   1. Ändra det aktuella skärm namnet till ett mer beskrivande namn.

1. Ange nu fälten för att identifiera varje skärm.

   Med data strömmen 3270 har skärmarna inga standard identifierare, så du måste välja unik text på varje skärm. I komplexa scenarier kan du ange flera villkor, till exempel unik text och ett fält med ett särskilt villkor.

När du har valt att välja igenkännings fält går du vidare till nästa läge.

### <a name="conditions-for-identifying-repeated-screens"></a>Villkor för att identifiera upprepade skärmar

För att kopplingen ska kunna navigera och skilja mellan skärmar, hittar du vanligt vis unik text på en skärm som du kan använda som identifierare bland de fångade skärmarna. För upprepade skärmar kan du behöva fler identifierings metoder. Anta till exempel att du har två skärmar som ser likadana ut utom en skärm returnerar ett giltigt värde, medan den andra skärmen returnerar ett fel meddelande.

I design verktyget kan du lägga till *igenkännings attribut*, till exempel en skärm rubrik, till exempel "Hämta konto balans" med hjälp av skärm igenkännings redigeraren. Om du har en förgrenings väg och båda grenarna returnerar samma skärm men med olika resultat behöver du andra igenkännings attribut. Vid körning använder anslutningen dessa attribut för att fastställa aktuell gren och förgrening. Här följer de villkor som du kan använda:

* Specifikt värde: det här värdet matchar den angivna strängen på den angivna platsen.
* INGET specifikt värde: det här värdet matchar inte den angivna strängen på den angivna platsen.
* Tom: det här fältet är tomt.
* INTE tomt: det här fältet är tomt.

Mer information finns i exemplet på [navigerings planen](#example-plan) senare i det här avsnittet.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Definiera navigerings planer

I det här läget definierar du flödet eller stegen för att navigera genom dina stordator skärmar för din speciella uppgift. Ibland kan du ibland ha fler än en sökväg som din app kan vidta där en sökväg ger rätt resultat, medan den andra sökvägen genererar ett fel. För varje skärm anger du de tangenter som krävs för att flytta till nästa skärm, till exempel `CICSPROD <enter>` .

> [!TIP]
> Om du automatiserar flera uppgifter som använder samma anslutnings-och från kopplings skärmar, tillhandahåller design verktyget särskilda prenumerations typer för att ansluta och koppla från. När du definierar dessa planer kan du lägga till dem i din navigerings Plans start-och slut punkt.

### <a name="guidelines-for-plan-definitions"></a>Rikt linjer för plan definitioner

* Ta med alla skärmar, från och med att koppla från.

* Du kan skapa en fristående plan eller använda planerna för att ansluta och koppla från, vilket gör att du kan återanvända en serie skärmar som är gemensamma för alla dina transaktioner.

  * Den sista skärmen i din Connect-plan måste vara samma skärm som den första skärmen i navigerings planen.

  * Den första skärmen i Disconnect-planen måste vara samma skärm som den sista skärmen i navigerings planen.

* Dina infångade skärmar kan innehålla många upprepade skärmar, så välj och Använd bara en instans av alla upprepande skärmar i planen. Här följer några exempel på upprepade skärmar:

  * Inloggnings skärmen, till exempel skärmen **MSG-10**
  * Välkomst skärmen för CICS
  * "Rensa" eller en **Tom** skärm

<a name="create-plans"></a>

### <a name="create-plans"></a>Skapa planer

1. I verktygsfältet 3270 design verktyg väljer du **navigering** så att du kan ange navigerings läge.

1. Starta planen genom att välja **ny plan** i **navigerings** fönstret.

1. Under **Välj nytt plan namn** anger du ett namn för din plan. I listan **typ** väljer du typ av prenumeration:

   | Plantyp | Description |
   |-----------|-------------|
   | **Process** | För fristående eller kombinerade planer |
   | **Anslut** | För Connect-planer |
   | **Koppla från** | För Disconnect-planer |
   |||

1. Från fönstret **värd raster** drar du de insamlade miniatyr bilderna till navigerings planens yta i **navigerings** fönstret.

   Använd skärmen "Tom" för att representera den tomma skärmen där du anger transaktions namnet.

1. Ordna skärmarna i den ordning som beskriver den uppgift som du definierar.

1. För att definiera flödes vägen mellan skärmar, inklusive förgreningar och kopplingar, i verktygsfältet i design verktyget, väljer du **Flow**.

1. Välj den första skärmen i flödet. Dra och rita en anslutning till nästa skärm i flödet.

1. För varje skärm anger du värdena för egenskapen **stöd nyckel** (Attention Identifier) och för egenskapen **fast text** som flyttar flödet till nästa skärm.

   Du kanske bara har stöd nyckeln eller både stöd nyckeln och den fasta texten.

När du har slutfört din navigerings plan kan du [definiera metoder i nästa läge](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Exempel

I det här exemplet antar vi att du kör en CICS-transaktion med namnet "WBGB" som har följande steg: 

* På den första skärmen anger du ett namn och ett konto.
* På den andra skärmen får du konto balansen.
* Du avslutar på skärmen "Tom".
* Du loggar ut från CICS till skärmen "MSG-10".

Anta också att du upprepar de här stegen, men du anger felaktiga data så att du kan avbilda skärmen som visar felet. Här är de skärmar som du hämtar:

* MSG-10
* Välkommen till CICS
* Tom
* WBGB_1 (Indatatyp)
* WBGB_2 (fel)
* Empty_1
* MSG-10_1

Även om många skärmar här får unika namn, är vissa skärmar samma skärm, till exempel "MSG-10" och "Empty". För en upprepad skärm använder du endast en instans för den skärmen i din plan. Här följer några exempel som visar hur en fristående plan, en kopplings plan, Disconnect-plan och en kombinerad plan kan se ut:

* Fristående plan

  ![Fristående navigerings plan](./media/connectors-create-api-3270/standalone-plan.png)

* Anslut plan

  ![Anslut plan](./media/connectors-create-api-3270/connect-plan.png)

* Koppla bort plan

  ![Koppla bort plan](./media/connectors-create-api-3270/disconnect-plan.png)

* Kombinerad plan

  ![Kombinerad plan](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Exempel: identifiera upprepade skärmar

För att kopplingen ska kunna navigera och urskilja skärmar, hittar du vanligt vis unik text på en skärm som du kan använda som en identifierare över de fångade skärmarna. För upprepade skärmar kan du behöva fler identifierings metoder. Exempel planen har en förgrening där du kan få skärmar som ser likadana ut. En skärm returnerar ett konto balans, medan den andra skärmen returnerar ett fel meddelande.

Med design verktyget kan du lägga till igenkännings attribut, till exempel en skärm rubrik med namnet "Hämta konto balans" med hjälp av skärm igenkännings redigeraren. I fallet med liknande skärmar behöver du andra attribut. Vid körning använder anslutningen dessa attribut för att fastställa grenen och förgreningen.

* I grenen som returnerar giltiga indatatyper, vilket är skärmen med saldot för kontot, kan du lägga till ett fält med ett "not tomt"-villkor.

* I grenen som returnerar med ett fel kan du lägga till ett fält med ett "tomt"-villkor.

<a name="define-method"></a>

## <a name="define-methods"></a>Definiera metoder

I det här läget definierar du en metod som är kopplad till din navigerings plan. För varje metod parameter anger du datatyp, till exempel en sträng, ett heltal, ett datum eller en tid, och så vidare. När du är klar kan du testa metoden på den levande värden och kontrol lera att metoden fungerar som förväntat. Du genererar sedan metadatafilen eller Host Integration designer XML-filen (HIDX) som nu har de metod definitioner som ska användas för att skapa och köra en åtgärd för IBM 3270-anslutningen.

1. I verktygsfältet 3270 design verktyg väljer du **metoder** så att du kan ange metod läge. 

1. I **navigerings** fönstret väljer du den skärm som innehåller de indatafält som du vill använda.

1. Följ dessa steg om du vill lägga till den första Indataparametern för metoden:

   1. I fönstret **Capture** på 3270-emulatorn-skärmen väljer du hela fältet, inte bara text i fältet som du vill ha som första inmatade.

      > [!TIP]
      > Om du vill visa alla fält och se till att du väljer fältet fullständig går du till menyn **Visa** och väljer **alla fält**.

   1. I verktygsfältet i design verktyget väljer du **inmatade fält**. 

   Upprepa föregående steg för varje parameter om du vill lägga till fler indataparametrar.

1. Följ dessa steg om du vill lägga till den första Utdataparametern för metoden:

   1. I fönstret **Capture** på 3270-emulatorn-skärmen väljer du hela fältet, inte bara text i fältet som du vill ha som första utdata.

      > [!TIP]
      > Om du vill visa alla fält och se till att du väljer fältet fullständig går du till menyn **Visa** och väljer **alla fält**.

   1. I verktygsfältet i design verktyget väljer du **utmatnings fält**.

   Om du vill lägga till fler utdataparametrar upprepar du föregående steg för varje parameter.

1. När du har lagt till alla parametrar för din metod definierar du dessa egenskaper för varje parameter:

   | Egenskapsnamn | Möjliga värden | 
   |---------------|-----------------|
   | **Datatyp** | Byte, datum tid, decimal, heltal, lång, kort, sträng |
   | **Fält fyllnings teknik** | Parametrarna har stöd för dessa fyllnings typer och fyller med blank steg om det behövs: <p><p>- **Skriv**: Ange tecken sekventiellt i fältet. <p>- **Fill**: Ersätt fältets innehåll med tecken och fyll med blank steg om det behövs. <p>- **EraseEofType**: Rensa fältet och ange sedan tecknen i turordning i fältet. |
   | **Format sträng** | Vissa parameter data typer använder en format sträng som informerar 3270-anslutningen så här konverterar du text från skärmen till en .NET-datatyp: <p><p>- **Datetime**: datetime-format strängen följer [anpassade .net-datum-och tids format strängar](/dotnet/standard/base-types/custom-date-and-time-format-strings). Till exempel `06/30/2019` används format strängen i datumet `MM/dd/yyyy` . <p>- **Decimal**: decimal format strängen använder [COBOL Picture-satsen](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_73/rzasb/picture.htm). Till exempel `100.35` använder talet formatet sträng `999V99` . |
   |||

## <a name="save-and-view-metadata"></a>Spara och visa metadata

När du har definierat din metod, men innan du testar metoden, sparar du all information som du definierade hittills i en RAP-fil (. Rap).
Du kan spara till den här RAP-filen när som helst under ett läge. Design verktyget innehåller också en exempel-RAP-fil som du kan öppna och granska genom att bläddra till design verktygets installationsmapp på den här platsen och öppna filen "WoodgroveBank. Rap":

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

Men om du försöker spara ändringar i exempel-RAP-filen eller skapa en HIDX-fil från exempel-RAP-filen medan filen finns kvar i design verktygets installationsmapp, kan du få ett fel meddelande om "åtkomst nekad". Som standard installeras design verktyget i mappen program filer utan förhöjd behörighet. Om du får ett fel meddelande kan du prova någon av följande lösningar:

* Kopiera exempel filen till en annan plats.
* Kör design verktyget som administratör.
* Bli ägare till SDK-mappen.

## <a name="test-your-method"></a>Testa din metod

1. Om du vill köra din metod mot Live-värden, medan du fortfarande befinner dig i metod läge, trycker du på F5 eller väljer **testa** i design verktygets verktygsfält.

   > [!TIP]
   > Du kan ändra lägen när som helst. Välj **läge** på **Arkiv** -menyn och välj sedan det läge som du vill använda.

1. Ange dina parameter värden och välj **OK**.

1. Fortsätt till nästa skärm genom att välja **Nästa**.

1. När du är klar väljer du **klar**, som visar värdena för utmatnings parameter.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>Generera och ladda upp HIDX-fil

När du är klar genererar du HIDX-filen så att du kan överföra till ditt integrations konto. Design verktyget 3270 skapar HIDX-filen i en ny undermapp där du sparade din RAP-fil.

1. I design verktyget 3270 väljer du **generera definitioner** på **verktyg** -menyn. (Tangent bord: F6)

1. Gå till den mapp som innehåller din RAP-fil och öppna undermappen som verktyget skapade när du har genererat HIDX-filen. Bekräfta att verktyget skapade HIDX-filen.

1. Logga in på [Azure Portal](https://portal.azure.com)och hitta ditt integrations konto.

1. Lägg till din HIDX-fil som en karta till ditt integrations konto genom [att följa dessa liknande steg för att lägga till kartor](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md), men när du väljer kart typen väljer du **HIDX**.

Senare i det här avsnittet när du lägger till en IBM 3270-åtgärd i din Logic app för första gången uppmanas du att skapa en anslutning mellan din Logic app och värd servern genom att tillhandahålla anslutnings information, till exempel namn för ditt integrations konto och värd servern. När du har skapat anslutningen kan du välja den nyligen tillagda HIDX-filen, vilken metod som ska köras och vilka parametrar som ska användas.

När du har slutfört alla dessa steg kan du använda den åtgärd som du skapar i din Logic app för att ansluta till IBM-stordatoren, enhets skärmarna för din app, ange data, returnera resultat och så vidare. Du kan också fortsätta att lägga till andra åtgärder till din Logic app för integrering med andra appar, tjänster och system.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>Kör IBM 3270-åtgärder

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. Under det sista steget där du vill lägga till en åtgärd väljer du **nytt steg** **>** **Lägg till en åtgärd**. 

1. Under sökrutan väljer du **företag**. I rutan Sök anger `3270` du som filter. I listan åtgärder väljer du den åtgärd som heter **kör ett stordator program över en TN3270-anslutning**

   ![Välj 3270-åtgärd](./media/connectors-create-api-3270/select-3270-action.png)

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. Om det inte finns någon anslutning ännu, anger du nödvändig information för anslutningen och väljer **skapa**.

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Anslutnings namn** | Yes | <*anslutnings namn*> | Namnet på anslutningen |
   | **Integrations konto-ID** | Yes | <*integration – konto namn*> | Integrations kontots namn |
   | **SAS-URL för integrations konto** | Yes | <*integration – konto – SAS-URL*> | Ditt integrations kontos URL för signatur för delad åtkomst (SAS), som du kan generera från integrations kontots inställningar i Azure Portal. <p>1. i menyn integrations konto **väljer du** **återanrops-URL**. <br>2. i den högra rutan kopierar du det **genererade URL** -värdet för motringning. |
   | **Server** | Yes | <*TN3270-Server namn*> | Server namnet för din TN3270-tjänst |
   | **Port** | No | <*TN3270-server-port*> | Porten som används av TN3270-servern. Om det lämnas tomt används anslutningen `23` som standardvärde. |
   | **Enhetstyp** | No | <*IBM-Terminal-Model*> | Modell namnet eller numret för den IBM-Terminal som ska emuleras. Om det lämnas tomt används standardvärden av kopplings funktionen. |
   | **Kodsida** | No | <*kod-sid nummer*> | Värdens tecken tabell nummer. Om det lämnas tomt används anslutningen `37` som standardvärde. |
   | **Namn på logisk enhet** | No | <*logiskt-enhets namn*> | Den angivna logiska enhetens namn som ska begäras från värden |
   | **Vill du aktivera SSL?** | No | På eller av | Aktivera eller inaktivera TLS-kryptering. |
   | **Verifiera SSL-certifikatet på värden?** | No | På eller av | Aktivera eller inaktivera verifiering för Server certifikatet. |
   ||||

   Exempel:

   ![Anslutningsegenskaper](./media/connectors-create-api-3270/connection-properties.png)

1. Ange nödvändig information för åtgärden:

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **HIDX namn** | Yes | <*HIDX – fil namn*> | Välj den 3270 HIDX-fil som du vill använda. |
   | **Metodnamn** | Yes | <*metod namn*> | Välj metoden i den HIDX-fil som du vill använda. När du har valt en metod visas listan **Lägg till ny parameter** så att du kan välja parametrar som ska användas med den metoden. |
   ||||

   Exempel:

   **Välj filen HIDX**

   ![Välj HIDX-fil](./media/connectors-create-api-3270/select-hidx-file.png)

   **Välj metod**

   ![Välj metod](./media/connectors-create-api-3270/select-method.png)

   **Välj parametrarna**

   ![Välj parametrar](./media/connectors-create-api-3270/add-parameters.png)

1. När du är klar sparar du och kör din Logic app.

   När din Logic-app har körts klart visas stegen från körningen. 
   De steg som visas visar kryss markeringar, medan de åtgärder som inte lyckades visar bokstaven "X".

1. Om du vill granska indata och utdata för varje steg expanderar du det steget.

1. Om du vill granska utdata väljer du **se rå data**.

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här anslutningen, till exempel utlösare, åtgärder och begränsningar som beskrivs av kopplingens Swagger-fil finns på [kopplingens referens sida](/connectors/si3270/).

> [!NOTE]
> För logi Kap par i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandets gränser](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)

