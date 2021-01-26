---
title: Ansluta till SAP-system
description: Få åtkomst till och hantera SAP-resurser genom att automatisera arbets flöden med Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 01/25/2021
tags: connectors
ms.openlocfilehash: 93e705eea39443ffc15fbdd079e1376ec46cb51c
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786698"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Ansluta till SAP-system från Azure Logic Apps

I den här artikeln förklaras hur du kan komma åt dina SAP-resurser från Logic Apps med hjälp av [SAP-anslutningen](https://docs.microsoft.com/connectors/sap/).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration ännu kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

* En Logic-app från vilken du vill få åtkomst till dina SAP-resurser. Om du inte har använt Logic Apps kan du läsa [översikten över Logic Appss tjänsten](../logic-apps/logic-apps-overview.md) och [snabb starten för att skapa din första Logic-app i Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

    * Om du har använt en tidigare version av SAP-kopplingen som är föråldrad måste du [migrera till den aktuella anslutningen](#migrate-to-current-connector) innan du kan ansluta till SAP-servern.

    * Om du kör din Logi Kap par i Azure med flera klienter kan du läsa mer i [kraven för flera klient organisationer](#multi-tenant-azure-prerequisites).

    * Om du kör din Logic-app i en Premium-[ miljö (Premium service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)på Premium-nivå, se [kraven för ISE](#ise-prerequisites).

* En [SAP-Programserver](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) eller en [SAP-MSMQ-server](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm) som du vill komma åt från Logic Apps. Information om vilka SAP-servrar och SAP-åtgärder som du kan använda med anslutnings tjänsten finns i [SAP-kompatibilitet](#sap-compatibility).

* Meddelande innehåll som ska skickas till din SAP-server, till exempel en IDoc-fil. Innehållet måste vara i XML-format och innehålla namn området för den SAP-åtgärd som du vill använda. Du kan [Skicka idocs med ett Flat File-schema genom att omsluta dem i ett XML-kuvert](#send-flat-file-idocs).

* Om du vill använda **när ett meddelande tas emot från SAP** -utlösaren måste du också göra följande:

    * Konfigurera dina säkerhets behörigheter för SAP Gateway med den här inställningen: `"TP=Microsoft.PowerBI.EnterpriseGateway HOST=<gateway-server-IP-address> ACCESS=*"`

    * Konfigurera din säkerhets loggning för SAP Gateway för att hitta Access Control List (ACL). Mer information finns i [Hjälp avsnittet för SAP för att konfigurera gateway-loggning](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm). Annars visas följande fel meddelande: `"Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed"`

    > [!NOTE]
    > Den här utlösaren använder samma URI-plats för att både förnya och avbryta prenumerationen på en webhook-prenumeration. Vid förnyelse-åtgärden används HTTP- `PATCH` metoden, medan den avbrutna prenumerationen använder HTTP- `DELETE` metoden. Detta kan göra att en förnyelse åtgärd visas som en avställnings åtgärd i utlösarens historik, men åtgärden är fortfarande en förnyelse eftersom utlösaren använder `PATCH` som HTTP-metod `DELETE` .

### <a name="sap-compatibility"></a>SAP-kompatibilitet

SAP-kopplingen är kompatibel med följande typer av SAP-system:

* Lokala och molnbaserade HANA-baserade SAP-system, till exempel S/4 HANA.

* Klassiska lokala SAP-system, till exempel R/3 och ECC.

SAP Connector stöder följande typer av meddelande-och data integrering från SAP NetWeaver-baserade system:

* Mellanliggande dokument (IDoc)

* Gränssnittet för affärs program programmering (BAPI)

* Fjärr funktions anrop (RFC) och transaktionella RFC (tRFC)

SAP-anslutaren använder [NCo-biblioteket (SAP .net Connector)](https://support.sap.com/en/product/connectors/msnet.html). Du kan använda följande SAP-åtgärder och utlösare med anslutningen:

* **Skicka meddelande till SAP** för att [Skicka idocs över tRFC](#send-idoc-action) -åtgärden, som du kan använda för att:

    * [Anropar BAPI-funktioner över RFC](#call-bapi-action)

    * Anropa RFC/tRFC i SAP-system

    * Skapa eller stänga tillstånds känsliga sessioner

    * Genomföra eller återställa BAPI-transaktioner

    * Bekräfta ett transaktions-ID

    * Skicka IDocs, hämta en IDoc status från dess nummer och hämta en lista över IDocs för en transaktion

    * Läsa en SAP-tabell

* **När ett meddelande tas emot från SAP** -utlösaren, som du kan använda för att:

    * Ta emot IDocs över tRFC

    * Anropar BAPI-funktioner över tRFC

    * Anropa RFC/tRFC i SAP-system

* **Skapa schema** åtgärd, som du kan använda för att skapa scheman för SAP-artefakter för iDOC, BAPI eller RFC.

Om du vill använda dessa SAP-åtgärder måste du först autentisera anslutningen med ett användar namn och lösen ord. SAP Connector stöder även [Säker nätverkskommunikation (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). Du kan använda SNC för SAP NetWeaver enkel inloggning (SSO) eller för ytterligare säkerhetsfunktioner från externa produkter. Om du använder SNC, se [kraven för SNC](#snc-prerequisites).

### <a name="migrate-to-current-connector"></a>Migrera till aktuell anslutning

De tidigare SAP Application Server-och SAP Message Server-kopplingarna var föråldrade den 29 februari 2020. Följ dessa steg om du vill migrera till den aktuella SAP-anslutningen:

1. Uppdatera din [lokala datagateway](https://www.microsoft.com/download/details.aspx?id=53127) till den aktuella versionen. Mer information finns i [installera en lokal datagateway för Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

1. Ta bort åtgärden **Skicka till SAP** i din Logic app som använder den föråldrade SAP-anslutningen.

1. Lägg till åtgärden **Skicka meddelande till SAP** från den aktuella SAP-anslutningen.

1. Återanslut till ditt SAP-system i den nya åtgärden.

1. Spara logikappen.

### <a name="multi-tenant-azure-prerequisites"></a>Krav för flera klientorganisationer i Azure

Dessa krav gäller om din Logic App körs i Azure med flera innehavare. Den hanterade SAP-kopplingen körs inte internt i en [ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

> [!TIP]
> Om du använder en Premium-nivå ISE kan du använda SAP ISE-anslutningen i stället för den hanterade SAP-anslutningen. Mer information finns i krav för [ISE](#ise-prerequisites).

Den hanterade SAP-anslutningen integreras med SAP-system via din [lokala datagateway](../logic-apps/logic-apps-gateway-connection.md). När ett meddelande till exempel skickas från en Logic app till ett SAP-system fungerar datagatewayen som en RFC-klient och vidarebefordrar de begär Anden som tas emot från Logic app till SAP i scenarier för att skicka meddelanden. På samma sätt fungerar datagatewayen som en RFC-server som tar emot begär Anden från SAP och vidarebefordrar dem till Logic app i scenarier med mottagna meddelanden.

* [Hämta och installera den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md) på en värddator eller virtuell dator som finns i samma virtuella nätverk som det SAP-system som du ansluter till.

* [Skapa en Azure gateway-resurs](../logic-apps/logic-apps-gateway-connection.md#create-azure-gateway-resource) för din lokala datagateway i Azure Portal. Den här gatewayen hjälper dig att komma åt lokala data och resurser på ett säkert sätt. Se till att använda en version av gatewayen som stöds.

    * Om det uppstår ett problem med din gateway kan du försöka att [Uppgradera till den senaste versionen](https://aka.ms/on-premises-data-gateway-installer), som kan innehålla uppdateringar för att lösa problemet.

* [Hämta och installera det senaste SAP-klient biblioteket](#sap-client-library-prerequisites) på samma lokala dator som din lokala datagateway.

### <a name="ise-prerequisites"></a>ISE-krav

Dessa krav gäller om du kör din Logi Kap par på en Premium-nivå ISE. De gäller dock inte för logi Kap par som körs i en utvecklings nivå ISE. En ISE ger åtkomst till resurser som skyddas av ett virtuellt Azure-nätverk och erbjuder andra ISE-ursprungliga anslutningar som låter Logic Apps direkt komma åt lokala resurser utan att använda lokal datagateway.

> [!NOTE]
> Även om SAP ISE-anslutaren är synlig i en utvecklings nivå ISE kommer försök att installera anslutnings programmet att Miss lyckas.

1. Om du inte redan har ett Azure Storage konto med en BLOB-behållare skapar du en behållare med hjälp av antingen [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) eller [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

1. [Hämta och installera det senaste SAP-klient biblioteket](#sap-client-library-prerequisites) på den lokala datorn. Du bör ha följande Assembly-filer:

   * libicudecnumber.dll

   * rscp4n.dll

   * sapnco.dll

   * sapnco_utils.dll

1. Skapa en zip-fil som innehåller dessa Assembly-filer. Ladda upp paketet till BLOB-behållaren i Azure Storage.

1. I antingen Azure Portal eller Azure Storage Explorer bläddrar du till den behållare plats där du laddade upp zip-filen.

1. Kopiera URL: en för behållar platsen. Se till att inkludera signaturen för signatur för delad åtkomst (SAS), så att SAS-token är auktoriserad. Annars Miss lyckas distributionen av SAP ISE-anslutaren.

1. Installera och distribuera SAP-anslutaren i din ISE. Mer information finns i [Lägg till ISE-anslutningar](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment).

   1. Leta upp och öppna din ISE i [Azure Portal](https://portal.azure.com).

   1. På menyn ISE väljer du **hanterade anslutningar** &gt; **Lägg till**. I listan kopplingar letar du reda på och väljer **SAP**.

   1. I rutan för att **lägga till en ny hanterad koppling** i rutan **SAP-paket** klistrar du in URL: en för zip-filen som innehåller SAP-sammansättningarna. Se till att ta med SAS-token igen.
 
  1. Välj **skapa** för att slutföra skapandet av din ISE-anslutning.

1. Om SAP-instansen och ISE finns i olika virtuella nätverk måste du också [peer-koppla dessa nätverk](../virtual-network/tutorial-connect-virtual-networks-portal.md) så att de är anslutna.

### <a name="sap-client-library-prerequisites"></a>Krav för SAP-klient bibliotek

Dessa krav gäller för det SAP-klient bibliotek som du använder med anslutnings tjänsten.

* Se till att du installerar den senaste versionen, [SAP Connector (NCo 3,0) för Microsoft .net 3.0.22.0 som kompilerats med .NET Framework 4,0-Windows 64-bit (x64)](https://support.sap.com/en/product/connectors/msnet.html). Tidigare versioner av SAP NCo kan drabbas av problem när fler än ett IDoc-meddelande skickas på samma gång. Det här villkoret blockerar alla senare meddelanden som skickas till SAP-målet, vilket gör att meddelandena blir timeouta.

* Du måste ha 64-bitars versionen av SAP-klient biblioteket installerad, eftersom datagatewayen bara körs på 64-bitars system. Installation av 32-bitars versionen som inte stöds resulterar i fel meddelandet "felaktig avbildning".

* Kopiera Assembly-filerna från standardmappen för installation till en annan plats, baserat på ditt scenario enligt följande.

    * För Logic Apps som körs i en ISE följer du [kraven för ISE](#ise-prerequisites) i stället.

    * För logi Kap par som körs i Azure med flera innehavare och använder din lokala datagateway, kopierar du Assembly-filerna till installationsmappen för datagateway. 

        
        * Om din SAP-anslutning Miss lyckas med fel meddelandet **kontrollerar du kontots information och/eller behörigheter och försöker igen**, se till att du har kopierat Assembly-filerna till datagateway-installationsmappen.
        
        * Felsök ytterligare problem med [logg visaren för .net-sammansättnings bindning](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer). Med det här verktyget kan du kontrol lera att dina Assembly-filer finns på rätt plats. 
        
        * Du kan också välja alternativet **Global Assembly Cache Registration** när du installerar SAP-klient biblioteket.

Observera följande relationer mellan SAP-klient biblioteket, .NET Framework, .NET-körningen och gatewayen:

* Både Microsoft SAP-styrenheten och värd tjänsten för Gateway använder .NET Framework 4.7.2.

* SAP-NCo för .NET Framework 4,0 fungerar med processer som använder .NET Runtime 4,0 till 4,8.

* SAP-NCo för .NET Framework 2,0 fungerar med processer som använder .NET Runtime 2,0 till 3,5, men inte längre fungerar med den senaste gatewayen.

### <a name="snc-prerequisites"></a>SNC-krav

Om du använder en lokal datagateway med valfria SNC, som endast stöds i Azure med flera innehavare, måste du konfigurera dessa ytterligare inställningar.

Om du använder SNC med SSO kontrollerar du att data Gateway-tjänsten körs som en användare som är mappad mot SAP-användaren. Om du vill ändra standard kontot väljer du **Ändra konto** och anger autentiseringsuppgifterna för användaren.

![Skärm bild av inställningarna för lokal datagateway i Azure Portal och sidan tjänst inställningar visas med knappen för att ändra Gateway-tjänstkontot som valts.](./media/logic-apps-using-sap-connector/gateway-account.png)

Om du aktiverar SNC via en extern säkerhets produkt kopierar du SNC-biblioteket eller-filerna på samma dator där din datagateway är installerad. Några exempel på SNC-produkter är [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), KERBEROS och NTLM. Mer information om hur du aktiverar SNC för data Gateway finns i [Aktivera säker nätverkskommunikation](#enable-secure-network-communications).

## <a name="send-idoc-messages-to-sap-server"></a>Skicka IDoc-meddelanden till SAP-server

Följ dessa exempel för att skapa en Logic app som skickar ett IDoc-meddelande till en SAP-server och returnerar ett svar:

1. [Skapa en logisk app som utlöses av en HTTP-begäran.](#create-http-request-trigger)

1. [Skapa en åtgärd i arbets flödet för att skicka ett meddelande till SAP.](#create-sap-action-to-send-message)

1. [Skapa en HTTP-svars åtgärd i ditt arbets flöde.](#create-http-response-action)

1. [Skapa ett fråge svars mönster för fjärran sluten funktions anrop (RFC) om du använder en RFC för att ta emot svar från SAP ABAP.](#create-rfc-request-response)

1. [Testa din Logic app.](#test-logic-app)

### <a name="create-http-request-trigger"></a>Skapa utlösare för HTTP-begäran

> [!NOTE]
> När en Logic-app tar emot IDocs från SAP, stöder [utlösaren](../connectors/connectors-native-reqres.md) SAP Plain XML-format. Använd utlösaren **när ett meddelande tas emot från SAP** för att ta emot idocs som vanlig XML. Ange parametern **iDOC-format** till **SapPlainXml**.

Börja med att skapa en Logic-app med en slut punkt i Azure för att skicka *http post-* förfrågningar till din Logic app. När din Logic app tar emot dessa HTTP-förfrågningar utlöses [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts) och kör nästa steg i arbets flödet.

1. I [Azure Portal](https://portal.azure.com)skapar du en tom Logic-app som öppnar **Logic Apps designer**.

1. I rutan Sök anger `http request` du som filter. Välj **när en HTTP-begäran tas emot** från listan **utlösare** .

   ![Skärm bild av Logic Apps designer, som visar en ny HTTP-begäran-utlösare som läggs till i Logic app.](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Spara din Logic app så att du kan skapa en slut punkts-URL för din Logic app. I verktygsfältet designer väljer du **Spara**. Slut punkts-URL: en visas nu i utlösaren. 

   ![Skärm bild av Logic Apps designer, visar utlösare för HTTP-begäran med genererad POST-URL som kopieras.](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="create-sap-action-to-send-message"></a>Skapa SAP-åtgärd för att skicka meddelande

Skapa sedan en åtgärd för att skicka ditt IDoc-meddelande till SAP när din [http-begäran](#create-http-request-trigger) utlöses.

1. I Logic Apps designer väljer du **nytt steg** under utlösaren.

   ![Skärm bild av Logic Apps designer, som visar att Logic app redige ras för att lägga till ett nytt steg.](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. I rutan Sök anger `sap` du som filter. I listan **åtgärder** väljer **du skicka meddelande till SAP**.
  
   ![Skärm bild av Logic Apps designer, med val av "Skicka meddelande till SAP"-åtgärd.](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Alternativt kan du välja fliken **Enterprise** och välja SAP-åtgärd.

   ![Skärm bild av Logic Apps designer, som visar val av åtgärden "Skicka meddelande till SAP" på fliken Enterprise.](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Om anslutningen redan finns fortsätter du till nästa steg. Om du uppmanas att skapa en ny anslutning anger du följande information för att ansluta till din lokala SAP-server.

   1. Ange ett namn för anslutningen.

   1. Följ dessa steg om du använder data gatewayen:
   
      1. I avsnittet **datagateway** , under **prenumeration**, väljer du först Azure-prenumerationen för den data gateway-resurs som du skapade i Azure Portal för din datagateway-installation.
   
      1. Under **anslutnings-Gateway** väljer du din data gateway-resurs i Azure.

   1. Fortsätt att tillhandahålla anslutnings information. För egenskapen **inloggnings typ** följer du stegen baserat på om egenskapen har angetts till **program Server** eller **grupp**:
   
      * För **program Server** krävs de här egenskaperna, som oftast visas som valfria,:

        ![Skapa SAP Application Server-anslutning](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * För **gruppen** är dessa egenskaper, som vanligt vis visas som valfria, obligatoriska:

        ![Skapa anslutning till SAP Message Server](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Som standard används stark inmatning för att kontrol lera ogiltiga värden genom att utföra XML-verifiering mot schemat. Det här beteendet kan hjälpa dig att identifiera problem tidigare. Alternativet för **säker inmatning** är tillgängligt för bakåtkompatibilitet och kontrollerar sträng längden. Läs mer om [alternativet för säker inmatning](#safe-typing).

   1. När du är klar väljer du **skapa**.

      Logic Apps konfigurerar och testar anslutningen för att kontrol lera att anslutningen fungerar korrekt.

    > [!NOTE]

    > Om du får följande fel uppstår ett problem med installationen av klient biblioteket SAP NCo: 
    >
    > **Test anslutningen misslyckades. Fel: det gick inte att bearbeta begäran. Fel information: det gick inte att läsa in filen eller sammansättningen sapnco, version = 3.0.0.42, Culture = neutral, PublicKeyToken 50436dca5c7f7d23 eller något av dess beroenden. Systemet kan inte hitta den angivna filen.**
    >
    > Se till att [installera den version av SAP NCo-klient biblioteket som krävs och uppfyller alla andra krav](#sap-client-library-prerequisites).

1. Nu hittar och väljer du en åtgärd från SAP-servern.

   1. I rutan **SAP-åtgärd** väljer du mappikonen. I listan fil söker du efter och väljer det SAP-meddelande som du vill använda. Använd pilarna för att navigera i listan.

      Det här exemplet väljer en IDoc med typen **order** .

      ![Sök och Välj åtgärden IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Om du inte hittar den åtgärd du vill ha kan du ange en sökväg manuellt, till exempel:

      ![Ange sökväg till IDoc-åtgärden manuellt](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Ange värdet för **SAP-åtgärd** via uttrycks redigeraren. På så sätt kan du använda samma åtgärd för olika meddelande typer.

      Mer information om IDoc-åtgärder finns i [meddelande scheman för iDOC-åtgärder](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Klicka i rutan **inmatat meddelande** så att listan med dynamiskt innehåll visas. Välj fältet **brödtext** under **när en http-begäran tas emot** från listan.

      Det här steget inkluderar bröd innehållet från din HTTP Request-utlösare och skickar utdata till SAP-servern.

      ![Välj egenskapen "brödtext" från utlösaren](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      När du är klar ser din SAP-åtgärd ut som i det här exemplet:

      ![Slutför SAP-åtgärden](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

#### <a name="send-flat-file-idocs"></a>Skicka Flat File-IDocs

Du kan använda IDocs med ett Flat File-schema om du omsluter dem i ett XML-kuvert. Om du vill skicka en platt fil IDoc använder du de allmänna anvisningarna för att [skapa en SAP-åtgärd för att skicka ditt iDOC-meddelande](#create-sap-action-to-send-message) med följande ändringar.

1. Använd SAP åtgärds-URI för att **Skicka meddelande till SAP** -åtgärd `http://microsoft.lobservices.sap/2007/03/Idoc/SendIdoc` .

1. Formatera indata-meddelandet med ett XML-kuvert. Ett exempel finns i följande exempel meddelande:

```xml
<?xml version="1.0" encoding="utf-8"?>
<SendIdoc xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/">
  <idocData>EDI_DC    300                      ORDERS052SAPMSS    LIMSFTABCSWI                                                                                           ED  93AORDERSOLP     VLTRFC    KUMSFTABCSWI                                                                                           13561                       231054476                                                                           20190523085430ORDERSORDERS05          US
E2EDK01005300                1     E2EDK010050     1       USD                                                                        Z4O14506907554
E2EDK03   300                2     E2EDK03   0     2   02220190523
E2EDKA1   300                3     E2EDKA1   0     2   RE                  MSFTASWI
E2EDKA1   300                4     E2EDKA1   0     2   US                  MSFTASWI
E2EDKA1   300                5     E2EDKA1   0     2   WE                  MSFTASWILIC
E2EDKA1   300                6     E2EDKA1   0     2   Z1 KKKKKKK                           ABC YYYYYYYYYYY ZZ                                                                                                                          BBBBBBBBBBBBBBBB 11                                                                                      ttttttttttt                                 6666              US                                                                                                999 999 99 99                                                                                                                SSSSSSS SSS SSSSSS                                                                                                                                SSSSSSS SSS SSSSSS
E2EDKA1   300                7     E2EDKA1   0     2   Z2 KKKKKKK                           BBBBBBBBBBBBBBBB DDDDDDDD ZZ                                                                                                                EEEEEEEEEEE 86                                                                                           rrrrrrrr                                    8888              US                                                                                                999 999 99 99                                                                                                                NNNNNN NNNNNN                                                                                                                                     NNNNNN NNNNNN
E2EDK02   300                8     E2EDK02   0     2   901Z
E2EDK02   300                9     E2EDK02   0     2   90399680096ZZS2002
E2EDK02   300                10    E2EDK02   0     2   902S
E2EDKT1   300                11    E2EDKT1   0     2   Z1EME
E2EDKT2   300                12    E2EDKT2   0     3   xxx@xxx-xx.xx
E2EDKT1   300                13    E2EDKT1   0     2   Z2EME
E2EDKT2   300                14    E2EDKT2   0     3   x.xxxxxx@xxxxxxxx-xxxxxxxxxx.xx
E2EDP01001300                15    E2EDP010010     2   10         1              EA                          999.9
E2EDP19   300                16    E2EDP19   0     3   00AAAA-11111</idocData>
</SendIdoc>
```



### <a name="create-http-response-action"></a>Skapa svars åtgärd för HTTP

Lägg nu till en svars åtgärd i din Logic Apps-arbetsflöde och inkludera utdata från SAP-åtgärden. På så sätt returnerar din Logic-app resultatet från din SAP-server till den ursprungliga begär Ande.

1. Välj **nytt steg** under SAP-åtgärden i Logic Apps designer.

1. I rutan Sök anger `response` du som filter. I listan **åtgärder** väljer du **svar**.

1. Klicka i rutan **brödtext** så att listan med dynamiskt innehåll visas. I listan, under **Skicka meddelande till SAP**, väljer du fältet **brödtext** .

   ![Slutför SAP-åtgärd](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Spara logikappen.

#### <a name="create-rfc-request-response"></a>Skapa RFC-begäran-svar

> [!NOTE]
> SAP-utlösaren tar emot IDocs över tRFC, som inte har en svars parameter genom design. 

Du måste skapa ett mönster för begäran och svar om du behöver ta emot svar genom att använda ett RPC (Remote Function Call) för att Logic Apps från SAP ABAP. Om du vill ta emot IDocs i din Logic app bör du först utföra en [http-begäran](../connectors/connectors-native-reqres.md#add-a-response-action) med status kod `200 OK` och inget innehåll. Det här rekommenderade steget Slutför SAP LUW Asynchronous Transfer över tRFC omedelbart, vilket gör att SAP CPIC-konversationen är tillgänglig igen. Du kan sedan lägga till ytterligare åtgärder i din Logic app för att bearbeta de mottagna IDoc utan att blockera ytterligare överföringar.

Om du vill implementera ett mönster för begäran och svar måste du först identifiera RFC-schemat med hjälp av [ `generate schema` kommandot](#generate-schemas-for-artifacts-in-sap). Det genererade schemat har två möjliga rotnoder: 

1. Noden begäran, som är det anrop som du får från SAP.

1. Noden svar, som är ditt svar tillbaka till SAP.

I följande exempel genereras ett mönster för begäran och svar från `STFC_CONNECTION` RFC-modulen. XML-begäran parsas för att extrahera ett Node-värde där SAP-begäranden `<ECHOTEXT>` . Svaret infogar den aktuella tidstämpeln som ett dynamiskt värde. Du får ett liknande svar när du skickar en `STFC_CONNECTION` RFC från en Logic app till SAP.

```http

<STFC_CONNECTIONResponse xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <ECHOTEXT>@{first(xpath(xml(triggerBody()?['Content']), '/*[local-name()="STFC_CONNECTION"]/*[local-name()="REQUTEXT"]/text()'))}</ECHOTEXT>
  <RESPTEXT>Azure Logic Apps @{utcNow()}</RESPTEXT>


```

### <a name="test-logic-app"></a>Testa logikapp

1. Om din Logic app inte redan är aktive rad väljer du **Översikt** på din Logic app-meny. Välj **Aktivera** i verktygsfältet.

1. I verktygsfältet designer väljer du **Kör**. Det här steget startar din Logic app manuellt.

1. Utlös din Logic app genom att skicka en HTTP POST-begäran till URL: en i din HTTP Request-utlösare.
Inkludera ditt meddelande innehåll med din begäran. Du kan använda ett verktyg som [Postman](https://www.getpostman.com/apps)för att skicka begäran.

   I den här artikeln skickar begäran en IDoc-fil som måste vara i XML-format och innehålla namn området för den SAP-åtgärd som du använder, till exempel:

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. När du har skickat din HTTP-begäran väntar du på svaret från din Logic app.

   > [!NOTE]
   > Din Logi Kap par kan ta lång tid om alla steg som krävs för svaret inte slutförs inom [tids gränsen för begäran](./logic-apps-limits-and-config.md). Om det här tillståndet inträffar kan begär Anden blockeras. För att hjälpa dig att diagnostisera problem kan du läsa mer om hur du kan [kontrol lera och övervaka dina Logic Apps](../logic-apps/monitor-logic-apps.md).

Nu har du skapat en logisk app som kan kommunicera med din SAP-server. Nu när du har konfigurerat en SAP-anslutning för din Logic app kan du utforska andra tillgängliga SAP-åtgärder, till exempel BAPI och RFC.

## <a name="receive-message-from-sap"></a>Ta emot meddelande från SAP

I det här exemplet används en Logic-app som utlöses när appen tar emot ett meddelande från ett SAP-system.

### <a name="add-an-sap-trigger"></a>Lägg till en SAP-utlösare

1. I Azure Portal skapar du en tom Logic-app som öppnar Logic Apps designer.

1. I rutan Sök anger `sap` du som filter. Välj **när ett meddelande tas emot från SAP** i listan **utlösare** .

   ![Lägg till SAP-utlösare](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   Alternativt kan du välja fliken **Enterprise** och sedan välja utlösaren:

   ![Lägg till SAP-utlösare från fliken Enterprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Om anslutningen redan finns fortsätter du med nästa steg så att du kan konfigurera din SAP-åtgärd. Men om du uppmanas att ange anslutnings information anger du informationen så att du kan skapa en anslutning till din lokala SAP-server nu.

   1. Ange ett namn för anslutningen.

   1. Följ dessa steg om du använder data gatewayen:

      1. I avsnittet **datagateway** , under **prenumeration**, väljer du först Azure-prenumerationen för den data gateway-resurs som du skapade i Azure Portal för din datagateway-installation.

      1. Under **anslutnings-Gateway** väljer du din data gateway-resurs i Azure.

   1. Fortsätt att ange information om anslutningen. För egenskapen **inloggnings typ** följer du stegen baserat på om egenskapen har angetts till **program Server** eller **grupp**:

      * För **program Server** krävs de här egenskaperna, som oftast visas som valfria,:

        ![Skapa SAP Application Server-anslutning](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * För **gruppen** är dessa egenskaper, som vanligt vis visas som valfria, obligatoriska:

        ![Skapa anslutning till SAP Message Server](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Som standard används stark inmatning för att kontrol lera ogiltiga värden genom att utföra XML-verifiering mot schemat. Det här beteendet kan hjälpa dig att identifiera problem tidigare. Alternativet för **säker inmatning** är tillgängligt för bakåtkompatibilitet och kontrollerar sträng längden. Läs mer om [alternativet för säker inmatning](#safe-typing).

   1. När du är klar väljer du **skapa**.

      Logic Apps konfigurerar och testar anslutningen för att kontrol lera att anslutningen fungerar korrekt.

1. Ange de [parametrar som krävs](#parameters) baserat på din konfiguration av SAP-systemet. 

   Du kan [filtrera meddelandena som du tar emot från SAP-servern genom att ange en lista över SAP-åtgärder](#filter-with-sap-actions).

   Du kan välja en SAP-åtgärd från fil väljaren:

   ![Lägg till SAP-åtgärd i Logic app](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Du kan också ange en åtgärd manuellt:

   ![Ange den SAP-åtgärd som du vill använda manuellt](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Här är ett exempel som visar hur åtgärden visas när du konfigurerar utlösaren för att ta emot mer än ett meddelande.

   ![Utlösnings exempel som tar emot flera meddelanden](media/logic-apps-using-sap-connector/example-trigger.png)

   Mer information om SAP-åtgärden finns i [meddelande scheman för iDOC-åtgärder](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Spara din Logic app så att du kan börja ta emot meddelanden från SAP-systemet. I verktygsfältet designer väljer du **Spara**.

Din Logi Kap par är nu redo att ta emot meddelanden från SAP-systemet.

> [!NOTE]
> SAP-utlösaren är ingen avsöknings utlösare utan är en webhook-baserad utlösare i stället. Om du använder datagatewayen anropas utlösaren från datagatewayen endast när det finns ett meddelande, så ingen avsökning krävs.

#### <a name="parameters"></a>Parametrar

Tillsammans med enkla sträng-och siffer inmatningar accepterar SAP-kopplingen följande tabell parametrar ( `Type=ITAB` indata):

* Tabell riktnings parametrar, både indata och utdata, för äldre SAP-versioner.

* Ändra parametrar, som ersätter tabell riktnings parametrarna för nyare SAP-versioner.

* Parametrar för hierarkisk tabell

#### <a name="filter-with-sap-actions"></a>Filtrera med SAP-åtgärder

Du kan välja att filtrera meddelanden som din Logic app tar emot från din SAP-server genom att tillhandahålla en lista, eller matris, med en eller flera SAP-åtgärder. Som standard är den här matrisen tom, vilket innebär att din Logic app tar emot alla meddelanden från din SAP-server utan att filtrera. 

När du ställer in mat ris filtret tar utlösaren bara emot meddelanden från de angivna SAP-åtgärds typerna och avvisar alla andra meddelanden från SAP-servern. Filtret påverkar dock inte om inmatningen av den mottagna nytto lasten är svag eller stark.

Alla SAP-åtgärds filtrering sker på SAP-kortets nivå för din lokala datagateway. Mer information finns i [Skicka test-idocs till Logic Apps från SAP](#test-sending-idocs-from-sap).

Om du inte kan skicka IDoc-paket från SAP till din Logic Apps utlösare, se meddelandet transaktionell RFC (tRFC)-anrop för avvisning i dialog rutan SAP tRFC (T-Code SM58). I SAP-gränssnittet kan du få följande fel meddelanden, som klipps ut på grund av del Strängs gränserna i **text fältet status** .

* `The RequestContext on the IReplyChannel was closed without a reply being`: Oväntade fel inträffar när en catch-all-hanterare för kanalen avslutar kanalen på grund av ett fel och återkonstruerar kanalen för att bearbeta andra meddelanden.

  * [Lägg till en svars åtgärd](../connectors/connectors-native-reqres.md#add-a-response-action) som returnerar en status kod för att bekräfta att din Logic app tog emot iDOC `200 OK` . IDoc transporteras via tRFC, vilket inte tillåter en nytto last för svar.

  * Om du behöver avvisa IDoc i stället svarar du med HTTP-statuskod förutom `200 OK` så att SAP-kortet returnerar ett undantag tillbaka till SAP för din räkning. 

* `The segment or group definition E2EDK36001 was not found in the IDoc meta`: Förväntade fel inträffar med andra fel, till exempel att det inte gick att generera en IDoc XML-nyttolast eftersom dess segment inte släpps av SAP, så segment typen metadata som krävs för konvertering saknas. 

  * Kontakta ABAP-teknikern för ditt SAP-system om du vill att dessa segment ska lanseras av SAP.
### <a name="asynchronous-request-reply-for-triggers"></a>Asynkron begäran-svara på utlösare

SAP-anslutaren stöder Azures [asynkrona svars svars mönster](/azure/architecture/patterns/async-request-reply.md) för Logic Apps utlösare. Du kan använda det här mönstret för att skapa lyckade förfrågningar som annars skulle Miss lyckas med Standardsvars mönstret för synkrona begär Anden. 

> [!TIP]
> I Logi Kap par med flera svars åtgärder måste alla svars åtgärder använda samma begär ande svars mönster. Om din Logic app exempelvis använder en växel kontroll med flera möjliga svars åtgärder måste du konfigurera alla svars åtgärder så att de använder samma svars svars mönster, antingen synkront eller asynkront. 

Genom att aktivera asynkron respons för din svars åtgärd kan din Logi Kap par svara med ett `202 Accepted` svar när en begäran har godkänts för bearbetning. Svaret innehåller ett plats huvud som du kan använda för att hämta det slutliga status för din begäran.

Så här konfigurerar du en asynkron begäran-svars mönster för din Logic app med hjälp av SAP-anslutnings tjänsten:

1. Öppna din Logic-app i **Logic Apps designer**.

1. Bekräfta att SAP-kopplingen är utlösaren för din Logic app.

1. Öppna din Logic Apps **svars** åtgärd. I åtgärdens namn List väljer du menyn (**...**) &gt; **Inställningar**.

1. I **inställningarna** för din svars åtgärd aktiverar du växla under **asynkron respons**. Välj färdig.

1. Spara ändringarna i din Logic app.

## <a name="find-extended-error-logs"></a>Hitta utökade felloggar

För fullständiga fel meddelanden kontrollerar du SAP-kortets utökade loggar. Du kan också [Aktivera en utökad logg fil för SAP-anslutningen](#extended-sap-logging-in-on-premises-data-gateway).

För lokala data Gateway-versioner från juni 2020 och senare kan du [Aktivera Gateway-loggar i appinställningar](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app). 

För lokala data Gateway-versioner från april 2020 och tidigare inaktive ras loggar som standard.

### <a name="extended-sap-logging-in-on-premises-data-gateway"></a>Utökad SAP-loggning i lokal datagateway

Om du använder en [lokal datagateway för Logic Apps](../logic-apps/logic-apps-gateway-install.md)kan du konfigurera en utökad logg fil för SAP-kopplingen. Du kan använda din lokala datagateway för att omdirigera ETW (Event Tracing for Windows)-händelser (ETW) till roterande loggfiler som ingår i din gateways log. zip-filer. 

Du kan [Exportera alla gatewayens konfigurations-och tjänst loggar](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app) till en. zip-fil i från Gateway-appens inställningar.

> [!NOTE]
> Utökad loggning kan påverka dina Logi Kap par prestanda när de alltid är aktiverade. Vi rekommenderar att du inaktiverar utökade loggfiler när du är klar med att analysera och felsöka ett problem.

#### <a name="capture-etw-events"></a>Fånga ETW-händelser

Alternativt kan avancerade användare fånga ETW-händelser direkt. Du kan sedan [använda dina data i Azure-diagnostik i Event Hubs](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs) eller [samla in data till Azure Monitor loggar](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-logs). Mer information finns i [metod tips för att samla in och lagra data](https://docs.microsoft.com/azure/architecture/best-practices/monitoring#collecting-and-storing-data). Du kan använda [PerfView](https://github.com/Microsoft/perfview/blob/master/README.md) för att arbeta med de resulterande ETL-filerna, eller så kan du skriva ditt eget program. I den här genom gången används PerfView:

1. På PerfView-menyn väljer du **samla** &gt; **in insamling** för att avbilda händelserna.

1. I fältet **ytterligare leverantör** anger `*Microsoft-LobAdapter` du för att ange SAP-providern för att avbilda SAP-kort händelser. Om du inte anger den här informationen innehåller spårningen bara allmänna ETW-händelser.

1. Behåll de andra standardinställningarna. Om du vill kan du ändra fil namnet eller platsen i fältet **data fil** .

1. Starta spårningen genom att välja **Starta samling** .

1. När du har återskapat problemet eller samlat in tillräckligt med analys data väljer du **stoppa insamling**.

Om du vill dela dina data med en annan part, till exempel Azure support Engineers, komprimera ETL-filen.

Så här visar du innehållet i spårningen:

1. I PerfView väljer du  &gt; **öppen** fil och väljer den ETL-fil som du nyss skapade.

1. I PerfView-sidofält, avsnittet **händelser** under din ETL-fil.

1. Filtrera efter `Microsoft-LobAdapter` för att endast se relevanta händelser och gateway-processer under filtrera.

### <a name="test-your-logic-app"></a>Testa din Logic app

1. Skicka ett meddelande från SAP-systemet för att utlösa din Logic app.

1. På menyn Logic app väljer du **Översikt**. Granska **körnings historiken** för alla nya körningar för din Logic app.

1. Öppna den senaste körningen, som visar meddelandet som skickas från ditt SAP-system i avsnittet Utlös utmatningar.

### <a name="test-sending-idocs-from-sap"></a>Testa att skicka IDocs från SAP

Om du vill skicka IDocs från SAP till din Logic app behöver du följande minsta konfiguration:

> [!IMPORTANT]
> Använd bara de här stegen när du testar din SAP-konfiguration med din Logic app. Produktions miljöer kräver ytterligare konfiguration.

1. [Konfigurera ett RFC-mål i SAP](#create-rfc-destination)

1. [Skapa en ABAP-anslutning till ditt RFC-mål](#create-abap-connection)

1. [Skapa en mottagar port](#create-receiver-port)

1. [Skapa en avsändar port](#create-sender-port)

1. [Skapa en logisk system partner](#create-logical-system-partner)

1. [Skapa en partner profil](#create-partner-profiles)

1. [Testa att skicka meddelanden](#test-sending-messages)

#### <a name="create-rfc-destination"></a>Skapa en RFC-destination

1. Om du vill öppna **konfigurationen av inställningarna för RFC-anslutningar** går du till SAP-gränssnittet och använder **sm59** -transaktions kod (T-kod) med prefixet **/n** .

1. Välj **TCP/IP-anslutningar**  >  **skapa**.

1. Skapa ett nytt RFC-mål med följande inställningar:
    
    * Ange ett namn för ditt **RFC-mål**.
    
    * På fliken **tekniska inställningar** väljer du **registrerade Server program** för **aktiverings typ**. Ange ett värde för ditt **program-ID**. I SAP registreras din Logic Apps-utlösare genom att använda den här identifieraren.
    
    * På fliken **Unicode** , för **kommunikations typ med mål system**, väljer du **Unicode**.

1. Spara ändringarna.

1. Registrera ditt nya **program-ID** med Azure Logic Apps.

1. Om du vill testa anslutningen går du till SAP-gränssnittet och väljer **anslutnings test** under ditt nya **RFC-mål**.

#### <a name="create-abap-connection"></a>Skapa ABAP-anslutning

1. Om du vill öppna **konfigurationen av inställningarna för RFC-anslutningar** går du till SAP-gränssnittet och använder **sm59** _ transaktions kod (T-Code) med prefixet _ */n**.

1. Välj **ABAP-anslutningar**  >  **skapa**.

1. För **RFC-mål** anger du ID: t för [ditt test-SAP-system](#create-rfc-destination).

1. Spara ändringarna.

1. Om du vill testa anslutningen väljer du **anslutnings test**.

#### <a name="create-receiver-port"></a>Skapa mottagar port

1. Om du vill öppna **portarna i iDOC bearbetnings** inställningar, i SAP-gränssnittet, använder du **we21** -transaktions kod (T-kod) med prefixet **/n** .

1. Välj de **portar** som  >  **transaktions-RFC**  >  **skapa**.

1. I rutan inställningar som öppnas väljer du **eget port namn**. Ange ett **namn** för test porten. Spara ändringarna.

1. I inställningarna för den nya mottagar porten, för **RFC-mål**, anger du identifieraren för [ditt test-RFC-mål](#create-rfc-destination).

1. Spara ändringarna.

#### <a name="create-sender-port"></a>Skapa avsändar port

1.  Om du vill öppna **portarna i iDOC bearbetnings** inställningar, i SAP-gränssnittet, använder du **we21** -transaktions kod (T-kod) med prefixet **/n** .

1. Välj de **portar** som  >  **transaktions-RFC**  >  **skapa**.

1. I rutan inställningar som öppnas väljer du **eget port namn**. För test porten anger du ett **namn** som börjar med **SAP**. Alla avsändar port namn måste börja med bokstäverna **SAP**, till exempel **SAPTEST**. Spara ändringarna.

1. I inställningarna för den nya avsändar porten för RFC- **mål** anger du identifieraren för [din ABAP-anslutning](#create-abap-connection).

1. Spara ändringarna.

#### <a name="create-logical-system-partner"></a>Skapa logisk system partner

1. Öppna **vyn ändra visning av logiska system: översikts** inställningar i SAP-gränssnittet med **bd54** -transaktions kod (T-Code).

1. Godkänn varnings meddelandet som visas: varning **: tabellen är mellan klient**

1. Välj **nya poster** ovanför listan som visar dina befintliga logiska system.

1. För det nya logiska systemet anger du en **Log.System** -identifierare och en beskrivning av kort **namn** . Spara ändringarna.

1. När **meddelandet om Workbench** visas skapar du en ny begäran genom att ange en beskrivning eller om du redan har skapat en begäran, hoppa över det här steget.

1. När du har skapat Workbench-begäran länkar du den till begäran till tabellen uppdaterings förfrågan. Bekräfta att tabellen har uppdaterats genom att spara ändringarna.

#### <a name="create-partner-profiles"></a>Skapa partner profiler

För produktions miljöer måste du skapa två partner profiler. Den första profilen är för avsändaren, som är din organisation och SAP-system. Den andra profilen gäller för mottagaren, som är din Logic-app.

1. Om du vill öppna inställningarna för **partner profiler** , i SAP-gränssnittet, använder du **we20** -transaktions koden (T-Code) med kommandot **/n** .

1. Under **partner profiler** väljer du **partner typ LS**  >  **create**.

1. Skapa en ny partner profil med följande inställningar:

    * För **partner nr.** ange [ID för din logiska system partner](#create-logical-system-partner).

    * För **en del. Skriv**, ange **ls**.

    * För **agent** anger du identifieraren för det SAP-användarkonto som ska användas när du registrerar program identifierare för Azure Logic Apps eller andra icke-SAP-system.

1. Spara ändringarna. Om du inte har [skapat den logiska system partnern](#create-logical-system-partner)får du felet genom att **Ange ett giltigt partner nummer**.

1. I partner profilens inställningar, under **utgående parmtrs**, väljer du **Skapa utgående parameter**.

1. Skapa en ny utgående parameter med följande inställningar:

    * Ange din **meddelande typ**, till exempel **CREMAS**.

    * Ange [mottagarens Port-ID](#create-receiver-port).

    * Ange en IDoc storlek för **paketet. Storlek**. Eller om du vill [Skicka idocs en i taget från SAP](#receive-idoc-packets-from-sap)väljer du **passe iDOC omedelbart**.

1. Spara ändringarna.

#### <a name="test-sending-messages"></a>Testa att skicka meddelanden

1. Öppna **test verktyget för iDOC bearbetnings** inställningar i SAP-gränssnittet med **we19** -transaktions kod (T-kod) med prefixet **/n** .

1. Under **mall för test** väljer du **via meddelande typ** och anger din meddelande typ, till exempel **CREMAS**. Välj **Skapa**.

1. Bekräfta **vilken iDOC-typ?** meddelande genom att välja **Fortsätt**.

1. Välj noden **EDIDC** . Ange lämpliga värden för mottagaren och avsändar portarna. Välj **Fortsätt**.

1. Välj **standard utgående bearbetning**.

1. Om du vill starta utgående IDoc-bearbetning väljer du **Fortsätt**. När bearbetningen är klar visas **iDOC som skickas till SAP-systemet eller det externa program** meddelandet.

1.  Om du vill söka efter bearbetnings fel använder du **SM58** -transaktions kod (T-kod) med kommandot **/n** .

## <a name="receive-idoc-packets-from-sap"></a>Ta emot IDoc-paket från SAP

Du kan konfigurera SAP för att [Skicka idocs i paket](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html), som är batchar eller grupper med IDocs. För att ta emot IDoc-paket behöver SAP-anslutaren och särskilt utlösaren ingen extra konfiguration. Men för att bearbeta varje objekt i ett IDoc-paket när utlösaren tar emot paketet, krävs ytterligare steg för att dela paketet i enskilda IDocs.

Här är ett exempel som visar hur du extraherar enskilda IDocs från ett paket med hjälp av [ `xpath()` funktionen](./workflow-definition-language-functions-reference.md#xpath):

1. Innan du börjar måste du ha en Logic-app med en SAP-utlösare. Om du inte redan har den här Logic-appen följer du de föregående stegen i det här avsnittet för att [Konfigurera en Logic app med en SAP-utlösare](#receive-message-from-sap).

   Ett exempel:

   ![Lägg till SAP-utlösare i Logic app](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Hämta rot namn området från XML-IDoc som din Logic app tar emot från SAP. Om du vill extrahera det här namn området från XML-dokumentet lägger du till ett steg som skapar en lokal sträng variabel och lagrar namn området med hjälp av ett `xpath()` uttryck:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Hämta rot namn område från IDoc](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Om du vill extrahera en enskild IDoc lägger du till ett steg som skapar en mat ris variabel och lagrar IDoc-samlingen med hjälp av ett annat `xpath()` uttryck:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Hämta objekt mat ris](./media/logic-apps-using-sap-connector/get-array.png)

   Array-variabeln gör varje IDoc tillgänglig för din Logic app för att bearbeta en individuellt genom att räkna upp över samlingen. I det här exemplet överför Logic-appen varje IDoc till en SFTP-server med hjälp av en slinga:

   ![Skicka IDoc till SFTP-server](./media/logic-apps-using-sap-connector/loop-batch.png)

   Varje IDoc måste innehålla rot namn området, vilket är orsaken till att fil innehållet är omslutet i ett- `<Receive></Receive` element tillsammans med rot namn området innan du skickar iDOC till den underordnade appen eller SFTP-servern i det här fallet.

Du kan använda snabb starts mal len för det här mönstret genom att välja den här mallen i Logic Apps designer när du skapar en ny Logic app.

![Välj app-mall för batch-logik](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Generera scheman för artefakter i SAP

I det här exemplet används en Logic-app som du kan utlösa med en HTTP-begäran. För att generera scheman för den angivna IDoc och BAPI skickar SAP-åtgärden **generate schema** en begäran till ett SAP-system.

Denna SAP-åtgärd returnerar ett [XML-schema](#sample-xml-schemas), inte innehållet eller data i själva XML-dokumentet. Scheman som returneras i svaret överförs till ett integrations konto med hjälp av Azure Resource Manager anslutningen. Scheman innehåller följande delar:

* Begär ande meddelandets struktur. Använd den här informationen för att skapa en BAPI- `get` lista.

* Svars meddelandets struktur. Använd den här informationen för att parsa svaret. 

Om du vill skicka begär ande meddelandet använder du den allmänna SAP-åtgärden **Skicka meddelande till SAP**, eller målet för målets **BAPI** -åtgärd.

### <a name="sample-xml-schemas"></a>Exempel på XML-scheman

Om du lär dig hur du skapar ett XML-schema som ska användas för att skapa ett exempel dokument, se följande exempel. I de här exemplen visas hur du kan arbeta med många typer av nytto laster, inklusive:

* [RFC-begäranden](#xml-samples-for-rfc-requests)

* [BAPI-begäranden](#xml-samples-for-bapi-requests)

* [IDoc-begäranden](#xml-samples-for-idoc-requests)

* Data typer för enkla eller komplexa XML-scheman

* Tabell parametrar

* Valfria XML-beteenden

Du kan börja XML-schemat med en valfri XML-prolog. SAP Connector fungerar med eller utan XML-prolog.

```xml

<?xml version="1.0" encoding="utf-8">

```

#### <a name="xml-samples-for-rfc-requests"></a>XML-exempel för RFC-förfrågningar

Följande exempel är ett grundläggande RFC-anrop. RFC-namnet är `STFC_CONNECTION` . Den här begäran använder standard namn området `xmlns=` , men du kan tilldela och använda namn rymds Ali Aset, till exempel `xmmlns:exampleAlias=` . Namn områdets värde är namn området för alla RFC: er i SAP för Microsoft-tjänster. Det finns en enkel indataparameter i begäran `<REQUTEXT>` .

```xml

<STFC_CONNECTION xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <REQUTEXT>exampleInput</REQUTEXT>
</STFC_CONNECTION>

```

Följande exempel är ett RFC-anrop med en tabell parameter. Det här exempel anropet och dess grupp med test-RFC: er är tillgängliga som en del av alla SAP-system. Tabell parameterns namn är `TCPICDAT` . Tabell radens typ är `ABAPTEXT` och det här elementet upprepas för varje rad i tabellen. Det här exemplet innehåller en enskild rad som kallas `LINE` . Begär Anden med en tabell parameter kan innehålla valfritt antal fält, där talet är ett positivt heltal (*n*). 

```xml

<STFC_WRITE_TO_TCPIC xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <RESTART_QNAME>exampleQName</RESTART_QNAME>
    <TCPICDAT>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput1</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput2</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput3</LINE>
      </ABAPTEXT>
    </TCPICDAT>
</STFC_WRITE_TO_TCPIC>

```

Följande exempel är ett RFC-anrop med en tabell parameter som har ett anonymt fält. Ett anonymt fält är när fältet inte har tilldelats något namn. Komplexa typer deklareras under en separat namnrymd där deklarationen anger en ny standard för den aktuella noden och alla dess underordnade element. Exemplet använder hex `x002F` -koden som ett escape-tecken för symbolen */* , eftersom den här symbolen är reserverad i SAP-fält namnet.

```xml

<RFC_XML_TEST_1 xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <IM_XML_TABLE>
    <RFC_XMLCNT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
      <_x002F_AnonymousField>exampleFieldInput</_x002F_AnonymousField>
    </RFC_XMLCNT>
  </IM_XML_TABLE>
</RFC_XML_TEST_1>

```

Följande exempel innehåller prefix för namn områden. Du kan deklarera alla prefix samtidigt, eller så kan du deklarera valfritt antal prefix som attribut för en nod. Namn områdes Ali Aset `ns0` för RFC används som rot och parametrar för Basic-typen.

> [!NOTE]
> komplexa typer deklareras under en annan namnrymd för RFC-typer med alias `ns3` i stället för det vanliga RFC-namnområdet med aliaset `ns0` .

```xml

<ns0:BBP_RFC_READ_TABLE xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Rfc/" xmlns:ns3="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc/">
  <ns0:DELIMITER>0</ns0:DELIMITER>
  <ns0:QUERY_TABLE>KNA1</ns0:QUERY_TABLE>
  <ns0:ROWCOUNT>250</ns0:ROWCOUNT>
  <ns0:ROWSKIPS>0</ns0:ROWSKIPS>
  <ns0:FIELDS>
    <ns3:RFC_DB_FLD>
      <ns3:FIELDNAME>KUNNR</ns3:FIELDNAME>
    </ns3:RFC_DB_FLD>
  </ns0:FIELDS>
</ns0:BBP_RFC_READ_TABLE>

```

#### <a name="xml-samples-for-bapi-requests"></a>XML-exempel för BAPI-förfrågningar

Följande XML-exempel är exempel begär Anden om att [anropa BAPI-metoden](#call-bapi-action).

> [!NOTE]
> SAP gör affärs objekt tillgängliga för externa system genom att beskriva dem som svar på RFC `RPY_BOR_TREE_INIT` , som Logic Apps problem utan indatamängds filter. Logic Apps kontrollerar tabellen output `BOR_TREE` . `SHORT_TEXT`Fältet används för namn på affärs objekt. Affärs objekt som inte returneras av SAP i utdatatabellen är inte tillgängliga för Logic Apps.
> 
> Om du använder anpassade affärs objekt måste du se till att publicera och släppa dessa affärs objekt i SAP. Annars listar inte SAP dina anpassade affärs objekt i utdatatabellen `BOR_TREE` . Du kan inte komma åt dina anpassade affärs objekt i Logic Apps förrän du exponerar affärs objekt från SAP. 

I följande exempel hämtas en lista över banker med hjälp av BAPI-metoden `GETLIST` . Det här exemplet innehåller affärs objekt för en bank `BUS1011` . 

```xml

<GETLIST xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_CTRY>US</BANK_CTRY>
  <MAX_ROWS>10</MAX_ROWS>
</GETLIST>

```

I följande exempel skapas ett bank objekt med hjälp av- `CREATE` metoden. I det här exemplet används samma affärs objekt som i föregående exempel `BUS1011` . När du använder `CREATE` metoden för att skapa en bank måste du se till att spara ändringarna eftersom den här metoden inte är tilldelad som standard.

> [!TIP]
> Se till att XML-dokumentet följer alla verifierings regler som kon figurer ATS i SAP-systemet. I det här exempel dokumentet måste exempelvis bank nyckeln ( `<BANK_KEY>` ) vara ett bank organisations nummer, även kallat ABA-nummer, i USA.

```xml

<CREATE xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_ADDRESS>
    <BANK_NAME xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleBankName</BANK_NAME>
    <REGION xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleRegionName</REGION>
    <STREET xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleStreetAddress</STREET>
    <CITY xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">Redmond</CITY>
  </BANK_ADDRESS>
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</CREATE>

```

Följande exempel hämtar information om en bank som använder bankens Routing Number, värdet för `<BANK_KEY>` . 

```xml

<GETDETAIL xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</GETDETAIL>

```

#### <a name="xml-samples-for-idoc-requests"></a>XML-exempel för IDoc-förfrågningar

Om du vill generera ett oformaterat SAP IDoc XML-schema använder du **SAP-inloggnings** programmet och T-koden `WE-60` . Få åtkomst till SAP-dokumentationen via det grafiska användar gränssnittet och generera XML-scheman i XSD-format för dina IDoc-typer och-tillägg. En förklaring av allmänna SAP-format och nytto laster och deras inbyggda dialog rutor finns i [SAP-dokumentationen](https://help.sap.com/viewer/index).

I det här exemplet deklaras rotnoden och namn områdena. URI: n i exempel koden, `http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send` deklarerar följande konfiguration:

* `/IDoc` är rot noteringen för alla IDocs
* `/3` är post typen version för gemensamma segment definitioner
* `/ORDERS05` är typen av IDoc
* `//` är ett tomt segment eftersom det inte finns något IDoc-tillägg
* `/700` är SAP-versionen
* `/Send` är åtgärden för att skicka informationen till SAP

```xml

<ns0:Send xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send" xmlns:ns3="http://schemas.microsoft.com/2003/10/Serialization" xmlns:ns1="http://Microsoft.LobServices.Sap/2007/03/Types/Idoc/Common/" xmlns:ns2="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700">
  <ns0:idocData>

```

Du kan upprepa `idocData` noden för att skicka en batch med idocs i ett enda anrop. I exemplet nedan finns det en kontroll post, `EDI_DC40` och flera data poster.

```xml

<...>
  <ns0:idocData>
    <ns2:EDI_DC40>
      <ns1:TABNAM>EDI_DC40</ns1:TABNAM>
<...>
      <ns1:ARCKEY>Cor1908207-5</ns1:ARCKEY>
    </ns2:EDI_DC40>
    <ns2:E2EDK01005>
      <ns2:DATAHEADERCOLUMN_SEGNAM>E23DK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:CURCY>USD</ns2:CURCY>
    </ns2:E2EDK01005>
    <ns2:E2EDK03>
<...>
  </ns0:idocData>

```

Följande exempel är ett exempel på en IDoc kontroll post, som använder prefixet `EDI_DC` . Du måste uppdatera värdena för att matcha din SAP-installations-och IDoc-typ. IDoc-klientens kod kan till exempel inte vara `800` . Kontakta ditt SAP-team för att se till att du använder rätt värden för din SAP-installation.

```xml

<ns2:EDI_DC40>
  <ns:TABNAM>EDI_DC40</ns1:TABNAM>
  <ns:MANDT>800</ns1:MANDT>
  <ns:DIRECT>2</ns1:DIRECT>
  <ns:IDOCTYP>ORDERS05</ns1:IDOCTYP>
  <ns:CIMTYP></ns1:CIMTYP>
  <ns:MESTYP>ORDERS</ns1:MESTYP>
  <ns:STD>X</ns1:STD>
  <ns:STDVRS>004010</ns1:STDVRS>
  <ns:STDMES></ns1:STDMES>
  <ns:SNDPOR>SAPENI</ns1:SNDPOR>
  <ns:SNDPRT>LS</ns1:SNDPRT>
  <ns:SNDPFC>AG</ns1:SNDPFC>
  <ns:SNDPRN>ABAP1PXP1</ns1:SNDPRN>
  <ns:SNDLAD></ns1:SNDLAD>
  <ns:RCVPOR>BTSFILE</ns1:RCVPOR>
  <ns:RCVPRT>LI</ns1:RCVPRT>

```

Följande exempel är en exempel data post med enkla segment. I det här exemplet används formatet SAP-datum. Starkt skrivna dokument kan använda interna XML-datum format, till exempel `2020-12-31 23:59:59` .

```xml

<ns2:E2EDK01005>
  <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
    <ns2:CURCY>USD</ns2:CURCY>
    <ns2:BSART>OR</ns2:BSART>
    <ns2:BELNR>1908207-5</ns2:BELNR>
    <ns2:ABLAD>CC</ns2:ABLAD>
  </ns2>
  <ns2:E2EDK03>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK03</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:IDDAT>002</ns2:IDDAT>
      <ns2:DATUM>20160611</ns2:DATUM>
  </ns2:E2EDK03>

```

Följande exempel är en data post med grupperade segment. Posten innehåller en överordnad nod, `E2EDKT1002GRP` och flera underordnade noder, inklusive `E2EDKT1002` och `E2EDKT2001` . 

```xml

<ns2:E2EDKT1002GRP>
  <ns2:E2EDKT1002>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT1002</ns2:DATAHEADERCOLUMN_SEGNAM>
      <NS2:TDID>ZONE</ns2:TDID>
  </ns2:E2EDKT1002>
  <ns2:E2EDKT2001>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT2001</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDLINE>CRSD</ns2:TDLINE>
  </ns2:E2EDKT2001>
</ns2:E2EDKT1002GRP>

```

Den rekommenderade metoden är att skapa en IDoc-identifierare för användning med tRFC. Du kan ställa in transaktions-ID: `tid` t med hjälp av [åtgärden skicka iDOC](/connectors/sap/#send-idoc) i SAP Connector-API: et.

Följande exempel är en alternativ metod för att ange transaktions-ID: t eller `tid` . I det här exemplet stängs den sista noden för data post segmentet och IDoc-datanoden. Sedan används GUID, `guid` som tRFC-ID för att identifiera dubbletter. 

```xml

    </E2STZUM002GRP>
  </idocData>
  <guid>8820ea40-5825-4b2f-ac3c-b83adc34321c</guid>
</Send>

```

### <a name="add-an-http-request-trigger"></a>Lägg till en HTTP-begäran-utlösare

1. I Azure Portal skapar du en tom Logic-app som öppnar Logic Apps designer.

1. I rutan Sök anger `http request` du som filter. Välj **när en HTTP-begäran tas emot** från listan **utlösare** .

   ![Lägg till utlösare för HTTP-begäran](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Spara din Logic app så att du kan skapa en slut punkts-URL för din Logic app.
I verktygsfältet designer väljer du **Spara**.

   Slut punkts-URL: en visas nu i utlösaren, till exempel:

   ![Generera URL för slut punkt](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Lägga till en SAP-åtgärd för att generera scheman

1. I Logic Apps designer väljer du **nytt steg** under utlösaren.

   ![Lägg till nytt steg i Logic app](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. I rutan Sök anger `sap` du som filter. I listan **åtgärder** väljer du **Skapa scheman**.
  
   ![Lägg till åtgärden "Skapa scheman" i Logic app](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Alternativt kan du välja fliken **Enterprise** och välja SAP-åtgärd.

   ![Välj SAP skicka åtgärd från fliken Enterprise](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Om anslutningen redan finns fortsätter du med nästa steg så att du kan konfigurera din SAP-åtgärd. Men om du uppmanas att ange anslutnings information anger du informationen så att du kan skapa en anslutning till din lokala SAP-server nu.

   1. Ange ett namn för anslutningen.

   1. I avsnittet **datagateway** , under **prenumeration**, väljer du först Azure-prenumerationen för den data gateway-resurs som du skapade i Azure Portal för din datagateway-installation. 
   
   1. Under **anslutnings-Gateway** väljer du din data gateway-resurs i Azure.

   1. Fortsätt att ange information om anslutningen. För egenskapen **inloggnings typ** följer du stegen baserat på om egenskapen har angetts till **program Server** eller **grupp**:
   
      * För **program Server** krävs de här egenskaperna, som oftast visas som valfria,:

        ![Skapa SAP Application Server-anslutning](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * För **gruppen** är dessa egenskaper, som vanligt vis visas som valfria, obligatoriska:

        ![Skapa anslutning till SAP Message Server](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Som standard används stark inmatning för att kontrol lera ogiltiga värden genom att utföra XML-verifiering mot schemat. Det här beteendet kan hjälpa dig att identifiera problem tidigare. Alternativet för **säker inmatning** är tillgängligt för bakåtkompatibilitet och kontrollerar sträng längden. Läs mer om [alternativet för säker inmatning](#safe-typing).

   1. När du är klar väljer du **skapa**.

      Logic Apps konfigurerar och testar anslutningen för att kontrol lera att anslutningen fungerar korrekt.

1. Ange sökvägen till den artefakt som du vill skapa schemat för.

   Du kan välja SAP-åtgärden från fil väljaren:

   ![Välj SAP-åtgärd](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Du kan också ange åtgärden manuellt:

   ![Ange SAP-åtgärd manuellt](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Om du vill generera scheman för fler än en artefakt anger du SAP-åtgärds informationen för varje artefakt, till exempel:

   ![Välj Lägg till nytt objekt](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Visa två objekt](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Mer information om SAP-åtgärden finns i [meddelande scheman för iDOC-åtgärder](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

### <a name="test-your-logic-app"></a>Testa din Logic app

1. I verktygsfältet designer väljer du **Kör** för att utlösa en körning för din Logic app.

1. Öppna körningen och kontrol lera utdata för åtgärden **generera scheman** .

   Utdata visar de genererade scheman för den angivna listan med meddelanden.

### <a name="upload-schemas-to-an-integration-account"></a>Ladda upp scheman till ett integrations konto

Du kan också hämta eller lagra de genererade schemana i-databaser, till exempel ett BLOB-, lagrings-eller integrations konto. Integrations konton ger en förstklassig upplevelse med andra XML-åtgärder, så det här exemplet visar hur du överför scheman till ett integrations konto för samma Logic-app med hjälp av Azure Resource Manager-anslutningen.

1. I Logic Apps designer väljer du **nytt steg** under utlösaren.

1. I rutan Sök anger `Resource Manager` du som filter. Välj **skapa eller uppdatera en resurs**.

   ![Välj Azure Resource Manager åtgärd](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Ange information om åtgärden, inklusive Azure-prenumeration, Azure-resurs grupp och integrations konto. Om du vill lägga till SAP-tokens i fälten klickar du i rutorna för dessa fält och väljer från listan med dynamiskt innehåll som visas.

   1. Öppna listan **Lägg till ny parameter** och välj fälten **plats** och **Egenskaper** .

   1. Ange information om de nya fälten som visas i det här exemplet.

      ![Ange information om Azure Resource Manager åtgärd](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   Åtgärden **skapa schema** i SAP genererar scheman som en samling, så att design verktyget automatiskt lägger till en **för varje** loop till åtgärden. Här är ett exempel som visar hur den här åtgärden visas:

   ![Azure Resource Manager åtgärd med "för varje"-loop](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > Schemana använder Base64-kodat format. Om du vill överföra scheman till ett integrations konto måste de avkodas med hjälp av `base64ToString()` funktionen. Här är ett exempel som visar koden för- `"properties"` elementet:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

### <a name="test-your-logic-app"></a>Testa din Logic app

1. I verktygsfältet designer väljer du **Kör** för att utlösa din Logic app manuellt.

1. Efter en lyckad körning går du till integrations kontot och kontrollerar att de genererade schemana finns.

## <a name="enable-secure-network-communications"></a>Aktivera säker nätverkskommunikation

Innan du börjar ska du kontrol lera att du har uppfyllt de tidigare angivna [kraven](#prerequisites), som endast tillämpas när du använder datagatewayen och dina Logi Kap par körs i Azure med flera innehavare:

* Kontrol lera att den lokala datagatewayen är installerad på en dator som är i samma nätverk som ditt SAP-system.

* För SSO körs datagatewayen som en användare som är mappad till en SAP-användare.

* SNC-biblioteket som tillhandahåller ytterligare säkerhetsfunktioner installeras på samma dator som data gatewayen. Några exempel är [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), KERBEROS och NTLM.

   Om du vill aktivera SNC för dina begär anden till eller från SAP-systemet markerar du kryss rutan **Använd SNC** i SAP-anslutningen och anger följande egenskaper:

   ![Konfigurera SAP-SNC i anslutning](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Egenskap | Beskrivning |
   |----------| ------------|
   | **Sökväg till SNC-bibliotek** | SNC-bibliotekets namn eller sökväg i förhållande till NCo-installations plats eller absolut sökväg. Exempel är `sapsnc.dll` eller `.\security\sapsnc.dll` eller `c:\security\sapsnc.dll` . |
   | **SNC SSO** | När du ansluter via SNC används SNC-identiteten vanligt vis för att autentisera anroparen. Ett annat alternativ är att åsidosätta så att information om användare och lösen ord kan användas för att autentisera anroparen, men linjen är fortfarande krypterad. |
   | **SNC mitt namn** | I de flesta fall kan den här egenskapen utelämnas. Den installerade SNC-lösningen känner vanligt vis till sitt eget SNC-namn. För lösningar som har stöd för flera identiteter kan du behöva ange vilken identitet som ska användas för det aktuella målet eller servern. |
   | **SNC partner namn** | Namnet på backend-SNC. |
   | **SNC skydds kvalitet** | Quality of service som ska användas för SNC-kommunikation för det specifika målet eller den här servern. Standardvärdet definieras av Server dels systemet. Det maximala värdet definieras av den säkerhets produkt som används för SNC. |
   |||

   > [!NOTE]
   > Ange inte miljövariabler SNC_LIB och SNC_LIB_64 på den dator där du har data gatewayen och SNC-biblioteket. Om det är inställt, prioriteras de av SNC-bibliotekets värde som skickas via anslutningen.

## <a name="safe-typing"></a>Säker inmatning

När du skapar din SAP-anslutning används som standard stark inmatning för att kontrol lera ogiltiga värden genom att utföra XML-verifiering mot schemat. Det här beteendet kan hjälpa dig att identifiera problem tidigare. Alternativet för **säker inmatning** är tillgängligt för bakåtkompatibilitet och kontrollerar sträng längden. Om du väljer **säker inmatning** behandlas dats-typen och Tims-typen i SAP som strängar i stället för motsvarande XML-motsvarigheter, `xs:date` och `xs:time` där `xmlns:xs="http://www.w3.org/2001/XMLSchema"` . Säker inmatning påverkar beteendet för all schema generering, att skicka meddelandet för både "har skickats"-nytto lasten och "mottaget"-svaret och utlösaren. 

När stark inmatning används (**säker inmatning** är inte aktiverat) mappar schemat dats-och Tims-typerna till fler enkla XML-typer:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

När du skickar meddelanden med stark inmatning uppfyller DATS-och TIMS-svaret det matchande XML-typ formatet:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

När **säker inmatning** är aktiverat mappar schemat dats-och Tims-typerna till XML-sträng fält med längd begränsningar, till exempel:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

När meddelanden skickas med **säker inmatning** aktive rad ser dats och Tims-svaret ut som i det här exemplet:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Avancerade scenarier

### <a name="change-language-headers"></a>Ändra språk rubriker

När du ansluter till SAP från Logic Apps är standard språket för anslutningen engelska. Du kan ställa in språket för din anslutning med hjälp av [standard-HTTP `Accept-Language` -huvud](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4) med dina inkommande begär Anden.

> [!TIP]
> De flesta webbläsare lägger till en `Accept-Language` rubrik baserat på användarens inställningar. Webbläsaren använder den här rubriken när du skapar en ny SAP-anslutning i Logic Apps designer. Om du inte vill skapa SAP-anslutningar i webbläsarens språk, uppdaterar du webbläsarens inställningar så att de använder det språk du föredrar, eller så skapar du en SAP-anslutning med Azure Resource Manager i stället för Logic Apps designer. 

Du kan till exempel skicka en begäran med `Accept-Language` sidhuvudet till din Logic-app genom att använda **http-begäran** utlösare. Alla åtgärder i din Logic app får rubriken. Sedan använder SAP de angivna språken i system meddelanden, t. ex. BAPI-felmeddelanden.

SAP-anslutnings parametrarna för en Logic app har ingen språk egenskap. Så om du använder `Accept-Language` rubriken kan du få följande fel meddelande: **kontrol lera konto information och/eller behörigheter och försök igen.** I det här fallet kontrollerar du SAP-komponentens fel loggar i stället. Felet inträffar faktiskt i SAP-komponenten som använder rubriken, så du kan få ett av följande fel meddelanden:

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`
* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### <a name="confirm-transaction-explicitly"></a>Bekräfta transaktionen explicit

När du skickar transaktioner till SAP från Logic Apps sker detta Exchange i två steg enligt beskrivningen i SAP-dokumentet, [TRANSAKTIONELLA RFC Server-program](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). Som standard hanterar åtgärden **Skicka till SAP** både stegen för funktions överföringen och för transaktions bekräftelsen i ett enda anrop. Med SAP Connector får du möjlighet att koppla från de här stegen. Du kan skicka en IDoc och i stället för att automatiskt bekräfta transaktionen, kan du använda åtgärden explicit **Bekräfta transaktions-ID** .

Den här funktionen för att ta del av transaktions-ID-bekräftelsen är användbar när du inte vill duplicera transaktioner i SAP, till exempel i scenarier där fel kan uppstå på grund av orsaker till nätverks problem. Genom att bekräfta transaktions-ID: t separat slutförs transaktionen bara en tid i SAP-systemet.

Här är ett exempel som visar det här mönstret:

1. Skapa en tom Logic-app och Lägg till en HTTP-utlösare.

1. Lägg till åtgärden **Skicka iDOC** från SAP-anslutaren. Ange information om IDoc som du skickar till ditt SAP-system.

1. För att explicit bekräfta transaktions-ID: t i ett separat steg, i fältet **Bekräfta tid** , väljer du **Nej**. För det valfria fältet **transaktions-ID GUID** kan du antingen manuellt ange värdet eller låta kopplingen automatiskt generera och returnera denna GUID i svaret från åtgärden skicka iDOC.

   ![Skicka åtgärds egenskaper för IDOC](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. För att explicit bekräfta transaktions-ID: t, Lägg till åtgärden **Bekräfta transaktions-ID** och se till att [undvika att skicka dubbletter av idocs till SAP](#avoid-sending-duplicate-idocs). Klicka i rutan **transaktions-ID** så att listan med dynamiskt innehåll visas. Välj det **transaktions-ID-** värde som returneras från åtgärden **Skicka iDOC** i listan.

   ![Bekräfta transaktions-ID-åtgärd](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   När det här steget körs markeras den aktuella transaktionen som slutförd i båda ändar, på sidan SAP-koppling och på SAP system-sidan.

#### <a name="avoid-sending-duplicate-idocs"></a>Undvik att skicka dubbletter av IDocs

Om det uppstår ett problem med att duplicera IDocs skickas till SAP från din Logic app, följer du de här stegen för att skapa en sträng variabel som fungerar som IDoc-transaktions-ID. Genom att skapa denna transaktions identifierare kan du förhindra dubbla nätverks sändningar när det finns problem, till exempel tillfälliga avbrott, nätverks problem eller förlorade bekräftelser.

> [!NOTE]
> SAP-system glömmer ett transaktions-ID efter en angiven tid eller 24 timmar som standard. Därför Miss lyckas SAP aldrig att bekräfta en transaktions-ID om ID eller GUID är okänt.
> Om det inte går att bekräfta ett transaktions-ID indikerar det här felet att kommunikation med SAP-systemet misslyckades innan SAP kunde bekräfta bekräftelsen.

1. I Logic Apps designer lägger du till den åtgärd som **initierar variabeln** i din Logic app. 

1. Konfigurera följande inställningar i redigerings programmet för **variabeln åtgärd initiera**. Spara sedan ändringarna.

    1. I **namn** anger du ett namn för din variabel. Ett exempel är `IDOCtransferID`.

    1. I **typ** väljer du **sträng** som variabel typ.

    1. För **värde** väljer du text rutan **Ange start värde** för att öppna menyn för dynamiskt innehåll. Välj fliken **uttryck** . I listan med funktioner anger du funktionen `guid()` . Välj **OK** för att spara ändringarna. Fältet **värde** är nu inställt på `guid()` funktionen, vilket genererar ett GUID.

1. Efter åtgärden **initiera variabel** lägger du till åtgärden **Skicka iDOC**.

1. Konfigurera följande inställningar i redigerings programmet för åtgärden **Skicka iDOC**. Spara sedan ändringarna.

    1. För **iDOC-typ** väljer du meddelande typ och anger ditt meddelande för **iDOC meddelande**.

    1. För **SAP-version** väljer du din SAP-konfigurations värden.

    1. För **post typer version** väljer du din SAP-konfigurations värden.

    1. För **Bekräfta tid** väljer du **Nej**.

    1. Välj **Lägg till ny parameter lista**  >  **transaktions-ID GUID**. Välj text rutan för att öppna menyn med dynamiskt innehåll. Under fliken **variabler** väljer du namnet på variabeln som du skapade. Ett exempel är `IDOCtransferID`.

1. I namn listen för åtgärden **Skicka iDOC** väljer du **...**  >  **Inställningar**. För **principer för återförsök** rekommenderar vi att du väljer **standard** &gt; **färdig**. Du kan dock istället konfigurera en anpassad princip för dina behov. För anpassade principer rekommenderar vi att du konfigurerar minst ett försök att lösa tillfälliga avbrott i nätverket.

1. När åtgärden har **skickat iDOC** lägger du till åtgärden **Bekräfta transaktions-ID**.

1. Konfigurera följande inställningar i redigerings programmet för åtgärd **Bekräfta transaktions-ID**. Spara sedan ändringarna.

    1. För **transaktions-ID** anger du namnet på variabeln igen. Ett exempel är `IDOCtransferID`.

1. Du kan också verifiera dedupliceringen i din test miljö. Upprepa åtgärden **Skicka iDOC** med samma **transaktions-ID-** GUID som du använde i föregående steg. När du skickar samma IDoc två gånger kan du verifiera att SAP kan identifiera dupliceringen av tRFC-anropet och lösa de två anropen till ett enda inkommande IDoc-meddelande.

## <a name="known-issues-and-limitations"></a>Kända problem och begränsningar

Här följer de kända problemen och begränsningarna för den hanterade SAP-anslutningen (ej-ISE):

* SAP-utlösaren stöder inte data Gateway-kluster. I vissa fall kan datagateway-noden som kommunicerar med SAP-systemet skilja sig från den aktiva noden, vilket resulterar i ett oväntat beteende. För sändnings scenarier stöds data Gateway-kluster.

* SAP-anslutaren stöder för närvarande inte SAP-router-strängar. Den lokala datagatewayen måste finnas i samma lokala nätverk som det SAP-system som du vill ansluta till.

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här anslutningen, till exempel utlösare, åtgärder och begränsningar som beskrivs av kopplingens Swagger-fil finns på [kopplingens referens sida](/connectors/sap/). Ytterligare dokumentation för Logic Apps tillhandahålls för följande åtgärder:

* [Ring BAPI](#call-bapi-action)

* [Skicka IDOC](#send-idoc-action)

> [!NOTE]
> För logi Kap par i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandets gränser](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

### <a name="call-bapi-action"></a>Åtgärd för att anropa BAPI

Åtgärden [anropa BAPI ( `CallBapi` )](
https://docs.microsoft.com/connectors/sap/#call-bapi-(preview)) anropar BAPI-metoden på din SAP-server. 

Du måste använda följande parametrar för ditt anrop: 

* **Företags objekt** ( `businessObject` ), som är en sökbar listruta.

* **Metod** ( `method` ) som fyller på tillgängliga metoder när du har valt ett **affärs objekt**. De tillgängliga metoderna varierar beroende på vilket **affärs objekt** som valts.

* **Indataparametrar för indata** ( `body` ), där du anropar XML-dokumentet som innehåller BAPI-metodens indataparametrar för anropet, eller URI: n för den lagrings-blob som innehåller dina BAPI-parametrar.

Detaljerade exempel på hur du använder åtgärden anropa BAPI finns i [XML-exempel för BAPI-begäranden](#xml-samples-for-bapi-requests).

> [!TIP]
> Om du använder Logic Apps designer för att redigera din BAPI-begäran kan du använda följande Sök funktioner: 
> 
> * Välj ett objekt i designern för att se en nedrullningsbar meny med tillgängliga metoder.
> * Filtrera affärs objekt typer efter nyckelord med hjälp av den sökbara lista som tillhandahålls av BAPI API-anropet.

### <a name="send-idoc-action"></a>Skicka IDoc-åtgärd

Åtgärden [Skicka iDOC ( `SendIDoc` )](https://docs.microsoft.com/connectors/sap/#send-idoc-(preview)) skickar iDOC-meddelandet till din SAP-server.

Du måste använda följande parametrar för ditt anrop: 

* **IDOC-typ med valfritt tillägg** ( `idocType` ), som är en sökbar listruta.

    * Den valfria parametern **SAP release version** ( `releaseVersion` ) fyller i värden när du har valt typen iDOC och är beroende av den valda iDOC-typen.

* **IDOC Message** ( `body` ), där du anropar XML-dokumentet som innehåller iDOC-nyttolasten eller URI: n för den lagrings-blob som innehåller ditt iDOC XML-dokument. Det här dokumentet måste vara kompatibelt med antingen SAP IDOC XML-schemat enligt WE60 IDoc-dokumentationen eller det genererade schemat för matchande SAP IDoc-åtgärds-URI.

Detaljerade exempel på hur du använder åtgärden skicka IDoc finns i [genom gången för att skicka iDOC-meddelanden till din SAP-server](#send-idoc-messages-to-sap-server).

Information om hur du använder valfri parameter **Bekräfta tid** ( `confirmTid` ) finns i [genom gången för att bekräfta transaktionen explicit](#confirm-transaction-explicitly).

## <a name="next-steps"></a>Nästa steg

* [Anslut till lokala system](../logic-apps/logic-apps-gateway-connection.md) från Azure Logic Apps.

* Lär dig hur du verifierar, transformerar och använder andra meddelande åtgärder med [Enterprise-integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md).

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md).
