---
title: Åtkomst till data källor lokalt
description: Anslut till lokala data källor från Azure Logic Apps genom att skapa en data gateway-resurs i Azure
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: 356e63bb0a749ad0f41d886e75971e9b05c7f9dc
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219002"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Ansluta till lokala datakällor från Azure Logic Apps

När du har [installerat den lokala *datagatewayen* på en lokal dator](../logic-apps/logic-apps-gateway-install.md) och innan du kan komma åt data källor lokalt från dina Logi Kap par måste du skapa en gateway-resurs i Azure för din gateway-installation. Du kan sedan välja denna gateway-resurs i de utlösare och åtgärder som du vill använda för [lokala anslutningar](../connectors/apis-list.md#on-premises-connectors) som är tillgängliga i Azure Logic Apps. Azure Logic Apps stöder Läs-och skriv åtgärder via datagatewayen. Dessa åtgärder har dock [gränser för deras nytto Last storlek](/data-integration/gateway/service-gateway-onprem#considerations).

Den här artikeln visar hur du skapar en Azure gateway-resurs för en tidigare [installerad gateway på den lokala datorn](../logic-apps/logic-apps-gateway-install.md). Mer information om gatewayen finns i [så här fungerar gatewayen](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Om du vill få direkt åtkomst till lokala resurser i virtuella Azure-nätverk utan att behöva använda gatewayen kan du skapa en [*integrerings tjänst miljö*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) i stället. 

Information om hur du använder gatewayen med andra tjänster finns i följande artiklar:

* [Microsoft Power automatisering av lokal datagateway](/power-automate/gateway-reference)
* [Microsoft Power BI lokal datagateway](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps lokal datagateway](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services lokal datagateway](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Datakällor som stöds

I Azure Logic Apps stöder den lokala datagatewayen de [lokala](../connectors/apis-list.md#on-premises-connectors) anslutningarna för dessa data Källor:

* BizTalk Server 2016
* Filsystem
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle-databas
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Du kan också skapa [anpassade anslutningar](../logic-apps/custom-connector-overview.md) som ansluter till data källor via http eller https med hjälp av rest eller SOAP. Även om själva gatewayen inte ådrar sig extra kostnader gäller [Logic Apps prissättnings modell](../logic-apps/logic-apps-pricing.md) för dessa anslutningar och andra åtgärder i Azure Logic Apps.

## <a name="prerequisites"></a>Förutsättningar

* Du har redan [installerat den lokala datagatewayen på en lokal dator](../logic-apps/logic-apps-gateway-install.md). Det måste finnas en gateway-installation innan du kan skapa en gateway-resurs som länkar till den här installationen.

* Du har [samma Azure-konto och prenumeration](../logic-apps/logic-apps-gateway-install.md#requirements) som du använde för din gateway-installation. Det här Azure-kontot måste tillhöra en enda [Azure Active Directory (Azure AD)-klient eller-katalog](../active-directory/fundamentals/active-directory-whatis.md#terminology). Du måste använda samma Azure-konto och-prenumeration för att skapa en gateway-resurs i Azure eftersom bara Gateway-administratören kan skapa Gateway-resursen i Azure. Tjänst huvud namn stöds inte för närvarande.

  * När du skapar en gateway-resurs i Azure väljer du en gateway-installation som länkar till din gateway-resurs och bara den gateway-resursen. Varje gateway-resurs kan bara länka till en gateway-installation. Du kan inte välja en gateway-installation som redan är kopplad till en annan gateway-resurs.

  * Din Logic app-och gateway-resurs behöver inte finnas i samma Azure-prenumeration. I utlösare och åtgärder där du kan använda Gateway-resursen kan du välja en annan Azure-prenumeration som har en gateway-resurs, men bara om den prenumerationen finns i samma Azure AD-klient eller katalog som din Logic app. Du måste också ha administratörs behörighet på gatewayen, vilken en annan administratör kan konfigurera åt dig. Mer information finns i [data Gateway: Automation med PowerShell-del 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) och [PowerShell: data Gateway-Add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser).

    > [!NOTE]
    > För närvarande kan du inte dela en gateway-resurs eller-installation över flera prenumerationer. Om du vill skicka feedback om produkten, se [Microsoft Azure feedback forum](https://feedback.azure.com/forums/34192--general-feedback).

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Skapa Azure gateway-resurs

När du har installerat gatewayen på en lokal dator skapar du Azure-resursen för din gateway.

1. Logga in på [Azure Portal](https://portal.azure.com) med samma Azure-konto som användes för att installera gatewayen.

1. I sökrutan Azure Portal anger du "lokal datagateway" och väljer **lokala Datagatewayer**.

   ![Hitta en lokal datagateway](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. Under **lokala Datagatewayer** väljer du **Lägg till**.

   ![Lägg till ny Azure-resurs för datagateway](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. Under **skapa Gateway för anslutning** anger du den här informationen för din gateway-resurs. När du är färdig väljer du **Skapa**.

   | Egenskap | Beskrivning |
   |----------|-------------|
   | **Resursnamn** | Ange ett namn för din gateway-resurs som bara innehåller bokstäver, siffror, bindestreck ( `-` ), under streck ( `_` ), parenteser ( `(` , `)` ) eller punkter ( `.` ). |
   | **Prenumeration** | Välj Azure-prenumerationen för det Azure-konto som användes för gateway-installationen. Standard prenumerationen baseras på det Azure-konto som du använde för att logga in. |
   | **Resursgrupp** | Den [Azure-resurs grupp](../azure-resource-manager/management/overview.md) som du vill använda |
   | **Plats** | Samma region eller plats som valdes för gateway-moln tjänsten under [Gateway-installationen](../logic-apps/logic-apps-gateway-install.md). Annars visas inte Gateway-installationen i listan **installations namn** . Din Logic app-plats kan skilja sig från din plats för gateway-resursen. |
   | **Installations namn** | Välj en gateway-installation som bara visas i listan när följande villkor uppfylls: <p><p>– Gateway-installationen använder samma region som den gateway-resurs som du vill skapa. <br>-Gateway-installationen är inte länkad till en annan Azure gateway-resurs. <br>– Gateway-installationen är länkad till samma Azure-konto som du använder för att skapa Gateway-resursen. <br>– Ditt Azure-konto tillhör en enda [Azure Active Directory (Azure AD)-klient eller-katalog](../active-directory/fundamentals/active-directory-whatis.md#terminology) och är samma konto som du använde för gateway-installationen. <p><p>Mer information finns i avsnittet [vanliga frågor och svar](#faq) . |
   |||

   Här är ett exempel som visar en gateway-installation som är i samma region som din gateway-resurs och som är länkad till samma Azure-konto:

   ![Ange information för att skapa en data gateway-resurs](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Ansluta till lokala data

När du har skapat din gateway-resurs och associerat din Azure-prenumeration med den här resursen kan du nu skapa en anslutning mellan din Logic app och din lokala data källa med hjälp av gatewayen.

1. I Azure Portal skapar eller öppnar du din Logic app i Logic App Designer.

1. Lägg till en anslutning som stöder lokala anslutningar, till exempel **SQL Server**.

1. Välj **Anslut via lokal datagateway**.

1. Under **Gateway** väljer du din Azure-prenumeration som har den gateway-resurs som du vill använda från listan **prenumeration** .

   Din Logic app-och gateway-resurs behöver inte finnas i samma Azure-prenumeration. Du kan välja från andra Azure-prenumerationer som var och en har en gateway-resurs, men bara om de här prenumerationerna finns i samma Azure AD-klient eller katalog som din Logic app, och du har administratörs behörighet på gatewayen, som en annan administratör kan konfigurera åt dig. Mer information finns i [data Gateway: Automation med PowerShell-del 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) och [PowerShell: data Gateway-Add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser).
  
1. Välj den gateway-resurs som du vill använda från listan **anslutnings Gateway** , som visar tillgängliga gateway-resurser i den valda prenumerationen. Varje gateway-resurs är länkad till en enda Gateway-installation.

   > [!NOTE]
   > Gateway-listan innehåller gateway-resurser i andra regioner eftersom din Logi Kap par plats kan skilja sig från Gateway-resursens plats. 

1. Ange ett unikt anslutnings namn och annan information som krävs, beroende på vilken anslutning du vill skapa.

   Ett unikt anslutnings namn hjälper dig att enkelt hitta anslutningen senare, särskilt om du skapar flera anslutningar. Om tillämpligt, inkludera även den kvalificerade domänen för ditt användar namn.

   Här är ett exempel:

   ![Skapa anslutning mellan Logic app och datagateway](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. När du är färdig väljer du **Skapa**.

Din gateway-anslutning är nu klar för din Logic app att använda.

## <a name="edit-connection"></a>Redigera anslutning

Om du vill uppdatera inställningarna för en gateway-anslutning kan du redigera anslutningen.

1. Om du vill hitta alla API-anslutningar för just din Logic app går du till din Logic Apps-meny, under **utvecklingsverktyg**, och väljer **API-anslutningar**.

   ![På din Logic app-meny väljer du "API-anslutningar"](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Välj den gateway-anslutning som du vill använda och välj sedan **Redigera API-anslutning**.

   > [!TIP]
   > Om dina uppdateringar inte börjar gälla kan du prova [med att stoppa och starta om gatewayens Windows-](../logic-apps/logic-apps-gateway-install.md#restart-gateway) tjänstkonto för gateway-installationen.

Så här hittar du alla API-anslutningar som är associerade med din Azure-prenumeration:

* Från Azure Portal-menyn väljer du **alla tjänster**  >  **Web**  >  **API-anslutningar**.
* Eller Välj **alla resurser** från Azure Portal-menyn. Ange **typ** filter till **API-anslutning**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Ta bort gateway-resurs

Om du vill skapa en annan gateway-resurs länkar du Gateway-installationen till en annan gateway-resurs eller tar bort Gateway-resursen, kan du ta bort Gateway-resursen utan att det påverkar Gateway-installationen.

1. Från Azure Portal-menyn väljer du **alla resurser** eller söker efter och väljer **alla resurser** från vilken sida som helst. Hitta och välj din gateway-resurs.

1. Om du inte redan har valt väljer du **lokal datagateway** på menyn gateway-resurs. I verktygsfältet gateway-resurs väljer du **ta bort**.

   Exempel:

   ![Ta bort gateway-resurs i Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F**: Varför visas inte min Gateway-installation när jag skapar min gateway-resurs i Azure? <br/>
**A**: det här problemet kan inträffa av följande orsaker:

* Ditt Azure-konto är inte samma konto som du använde för gateway-installationen på den lokala datorn. Kontrol lera att du har loggat in på Azure Portal med samma identitet som du använde för gateway-installationen. Endast Gateway-administratören kan skapa Gateway-resursen i Azure. Tjänst huvud namn stöds inte för närvarande.

* Ditt Azure-konto tillhör inte bara en enda [Azure AD-klient eller-katalog](../active-directory/fundamentals/active-directory-whatis.md#terminology). Kontrol lera att du använder samma Azure AD-klient eller katalog som du använde vid installationen av gatewayen.

* Gateway-resursen och gateway-installationen finns inte i samma region. Den logiska appens plats kan dock skilja sig från platsens resurs plats.

* Gateway-installationen är redan kopplad till en annan gateway-resurs. Varje gateway-resurs kan bara länka till en gateway-installation, som bara kan länka till ett Azure-konto och-prenumeration. Därför kan du inte välja en gateway-installation som redan är kopplad till en annan gateway-resurs. De här installationerna visas inte i listan **installations namn** .

  Om du vill granska dina gateway-registreringar i Azure Portal hittar du alla dina Azure-resurser som har resurs typen **lokal Datagatewayer** för *alla* dina Azure-prenumerationer. Information om hur du avlänkar Gateway-installationen från den andra gateway-resursen finns i [ta bort gateway-resurs](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Nästa steg

* [Skydda dina logikappar](./logic-apps-securing-a-logic-app.md)
* [Vanliga exempel och scenarier för Logic Apps](./logic-apps-examples-and-scenarios.md)
