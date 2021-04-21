---
title: Få åtkomst till datakällor lokalt
description: Ansluta till lokala datakällor från en Azure Logic Apps genom att skapa en datagatewayresurs i Azure
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 49da5d7f045ed06ba16696ebd16ad212b9d140d8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763317"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Ansluta till lokala datakällor från Azure Logic Apps

När du har installerat den lokala datagatewayen på en lokal dator och innan du kan komma åt datakällor lokalt från dina logikappar måste du skapa en [ *gatewayresurs*](../logic-apps/logic-apps-gateway-install.md) i Azure för gatewayinstallationen. Du kan sedan välja den här [gatewayresursen](../connectors/managed.md#on-premises-connectors) i de utlösare och åtgärder som du vill använda för de lokala anslutningsappar som är tillgängliga i Azure Logic Apps. Azure Logic Apps har stöd för läs- och skrivåtgärder via datagatewayen. Dessa åtgärder har dock begränsningar [för nyttolastens storlek.](/data-integration/gateway/service-gateway-onprem#considerations)

Den här artikeln visar hur du skapar din Azure-gatewayresurs för en tidigare [installerad gateway på den lokala datorn](../logic-apps/logic-apps-gateway-install.md). Mer information om gatewayen finns i [Så här fungerar gatewayen.](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)

> [!TIP]
> Om du vill ha direkt åtkomst till lokala resurser i virtuella Azure-nätverk utan att behöva använda gatewayen bör du överväga att skapa en [*integreringstjänstmiljö i*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) stället. 

Information om hur du använder gatewayen med andra tjänster finns i följande artiklar:

* [Microsoft Power Automate lokal datagateway](/power-automate/gateway-reference)
* [Microsoft Power BI lokal datagateway](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps lokal datagateway](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services lokal datagateway](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Datakällor som stöds

I Azure Logic Apps stöder den lokala datagatewayen [de lokala anslutningsapparna för](../connectors/managed.md#on-premises-connectors) dessa datakällor:

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

Du kan också skapa [anpassade anslutningsappar som](../logic-apps/custom-connector-overview.md) ansluter till datakällor via HTTP eller HTTPS med hjälp av REST eller SOAP. Även om själva gatewayen inte medför några extra kostnader Logic Apps prissättningsmodellen [för](../logic-apps/logic-apps-pricing.md) dessa anslutningsappar och andra åtgärder i Azure Logic Apps.

## <a name="prerequisites"></a>Förutsättningar

* Du har [redan installerat den lokala datagatewayen på en lokal dator](../logic-apps/logic-apps-gateway-install.md). Gatewayinstallationen måste finnas innan du kan skapa en gatewayresurs som länkar till den här installationen.

* Du har samma [Azure-konto och -prenumeration som](../logic-apps/logic-apps-gateway-install.md#requirements) du använde för gatewayinstallationen. Det här Azure-kontot får bara tillhöra en [enda Azure Active Directory (Azure AD)-klient eller katalog](../active-directory/fundamentals/active-directory-whatis.md#terminology). Du måste använda samma Azure-konto och -prenumeration för att skapa din gatewayresurs i Azure eftersom endast gatewayadministratören kan skapa gatewayresursen i Azure. Tjänstens huvudnamn stöds inte för närvarande.

  * När du skapar en gatewayresurs i Azure väljer du en gatewayinstallation för att länka till din gatewayresurs och endast den gatewayresursen. Varje gatewayresurs kan endast länka till en gatewayinstallation. Du kan inte välja en gatewayinstallation som redan är associerad med en annan gatewayresurs.

  * Logikappen och gatewayresursen behöver inte finnas i samma Azure-prenumeration. I utlösare och åtgärder där du kan använda gatewayresursen kan du välja en annan Azure-prenumeration som har en gatewayresurs, men bara om prenumerationen finns i samma Azure AD-klientorganisation eller -katalog som logikappen. Du måste också ha administratörsbehörighet för gatewayen, som en annan administratör kan konfigurera åt dig. Mer information finns i [DataGateway: Automation using PowerShell - Part 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) and [PowerShell: Data Gateway - Add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser).

    > [!NOTE]
    > För närvarande kan du inte dela en gatewayresurs eller installation över flera prenumerationer. Om du vill skicka produktfeedback [kan du Microsoft Azure feedbackforumet.](https://feedback.azure.com/forums/34192--general-feedback)

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Skapa en Azure-gatewayresurs

När du har installerat gatewayen på en lokal dator skapar du Azure-resursen för din gateway.

1. Logga in på [Azure Portal](https://portal.azure.com) med samma Azure-konto som användes för att installera gatewayen.

1. I sökrutan Azure Portal du "lokal datagateway" och **väljer Lokala datagatewayer.**

   ![Hitta "Lokal datagateway"](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. Under **Lokala datagatewayer väljer** du Lägg **till**.

   ![Lägga till ny Azure-resurs för datagateway](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. Under **Skapa anslutningsgateway** anger du den här informationen för din gatewayresurs. När du är färdig väljer du **Skapa**.

   | Egenskap | Beskrivning |
   |----------|-------------|
   | **Resursnamn** | Ange ett namn för din gatewayresurs som endast innehåller bokstäver, siffror, bindestreck ( `-` ), understreck ( `_` ), parenteser ( `(` , ), eller punkter ( `)` `.` ). |
   | **Prenumeration** | Välj Azure-prenumerationen för det Azure-konto som användes för gatewayinstallationen. Standardprenumerationen baseras på det Azure-konto som du använde för att logga in. |
   | **Resursgrupp** | Den [Azure-resursgrupp](../azure-resource-manager/management/overview.md) som du vill använda |
   | **Plats** | Samma region eller plats som valdes för gatewaymolntjänsten under [gatewayinstallationen](../logic-apps/logic-apps-gateway-install.md). Annars visas inte gatewayinstallationen i listan **Installationsnamn.** Logikappens plats kan skilja sig från platsen för gatewayresursen. |
   | **Installationsnamn** | Välj en gatewayinstallation som endast visas i listan när dessa villkor är uppfyllda: <p><p>– Gatewayinstallationen använder samma region som den gatewayresurs som du vill skapa. <br>– Gatewayinstallationen är inte länkad till någon annan Azure-gatewayresurs. <br>– Gatewayinstallationen är länkad till samma Azure-konto som du använder för att skapa gatewayresursen. <br>– Ditt Azure-konto tillhör en [enskild Azure Active Directory(Azure AD)-klient](../active-directory/fundamentals/active-directory-whatis.md#terminology) eller -katalog och är samma konto som du använde för gatewayinstallationen. <p><p>Mer information finns i [avsnittet Vanliga](#faq) frågor och svar. |
   |||

   Här är ett exempel som visar en gatewayinstallation som finns i samma region som din gatewayresurs och som är länkad till samma Azure-konto:

   ![Ange information för att skapa en datagatewayresurs](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Ansluta till lokala data

När du har skapat din gatewayresurs och associerat din Azure-prenumeration med den här resursen kan du nu skapa en anslutning mellan logikappen och din lokala datakälla med hjälp av gatewayen.

1. I Azure Portal du eller öppnar logikappen i Logikappdesignern.

1. Lägg till en anslutningsapp som stöder lokala anslutningar, till exempel **SQL Server**.

1. Välj **Anslut via lokal datagateway**.

1. Under **Gateway** går du till **listan** Prenumeration och väljer den Azure-prenumeration som har den gatewayresurs som du vill använda.

   Logikappen och gatewayresursen behöver inte finnas i samma Azure-prenumeration. Du kan välja från andra Azure-prenumerationer som var och en har en gatewayresurs, men endast om dessa prenumerationer finns i samma Azure AD-klient eller -katalog som din logikapp och du har administratörsbehörighet på gatewayen, som en annan administratör kan konfigurera åt dig. Mer information finns i [DataGateway: Automation using PowerShell - Part 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) and [PowerShell: Data Gateway - Add-DataGatewayClusterUser](/powershell/module/datagateway/add-datagatewayclusteruser).
  
1. I listan **Anslutningsgateway,** som visar tillgängliga gatewayresurser i den valda prenumerationen, väljer du den gatewayresurs som du vill använda. Varje gatewayresurs är länkad till en enda gatewayinstallation.

   > [!NOTE]
   > Listan över gatewayer innehåller gatewayresurser i andra regioner eftersom logikappens plats kan skilja sig från gatewayresursens plats. 

1. Ange ett unikt anslutningsnamn och annan nödvändig information som beror på vilken anslutning du vill skapa.

   Ett unikt anslutningsnamn hjälper dig att enkelt hitta den anslutningen senare, särskilt om du skapar flera anslutningar. Om det är tillämpligt inkluderar du även den kvalificerade domänen för ditt användarnamn.

   Här är ett exempel:

   ![Skapa en anslutning mellan logikappen och datagatewayen](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. När du är färdig väljer du **Skapa**.

Gatewayanslutningen är nu klar för logikappen att använda.

## <a name="edit-connection"></a>Redigera anslutning

Om du vill uppdatera inställningarna för en gateway-anslutning kan du redigera anslutningen.

1. Om du vill hitta alla API-anslutningar för bara logikappen går du till logikappens meny under **Utvecklingsverktyg** och väljer **API-anslutningar.**

   ![På logikappmenyn väljer du "API-anslutningar"](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Välj den gatewayanslutning som du vill använda och välj sedan **Redigera API-anslutning.**

   > [!TIP]
   > Om dina uppdateringar inte börjar gälla provar du att [stoppa och starta om gatewayens Windows-tjänstkonto för](../logic-apps/logic-apps-gateway-install.md#restart-gateway) gatewayinstallationen.

Så här hittar du alla API-anslutningar som är associerade med din Azure-prenumeration:

* Från menyn Azure Portal väljer du Alla **tjänster**  >    >  **Webb-API-anslutningar**.
* Du kan också Azure Portal på **menyn Alla resurser**. Ange filtret **Typ** till **API-anslutning**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Ta bort gatewayresurs

Om du vill skapa en annan gatewayresurs, länka gatewayinstallationen till en annan gatewayresurs eller ta bort gatewayresursen kan du ta bort gatewayresursen utan att påverka gatewayinstallationen.

1. På Azure Portal väljer du **Alla resurser** eller söker efter och väljer Alla **resurser på** valfri sida. Leta upp och välj din gatewayresurs.

1. Om du inte redan har valt väljer du Lokal **datagateway på resursmenyn för gatewayen.** I verktygsfältet för gatewayresursen väljer du Ta **bort**.

   Exempel:

   ![Ta bort gatewayresurs i Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F:** Varför visas inte gatewayinstallationen när jag skapar min gatewayresurs i Azure? <br/>
**S:** Det här problemet kan inträffa av följande skäl:

* Ditt Azure-konto är inte samma konto som du använde för gatewayinstallationen på den lokala datorn. Kontrollera att du har loggat in på Azure Portal med samma identitet som du använde för gatewayinstallationen. Endast gatewayadministratören kan skapa gatewayresursen i Azure. Tjänstens huvudnamn stöds inte för närvarande.

* Ditt Azure-konto tillhör inte bara en enda [Azure AD-klient eller katalog.](../active-directory/fundamentals/active-directory-whatis.md#terminology) Kontrollera att du använder samma Azure AD-klient eller -katalog som du använde under gatewayinstallationen.

* Gatewayresursen och gatewayinstallationen finns inte i samma region. Logikappens plats kan dock skilja sig från platsen för gatewayresursen.

* Gatewayinstallationen är redan associerad med en annan gatewayresurs. Varje gatewayresurs kan endast länka till en gatewayinstallation, som bara kan länka till ett Azure-konto och en azure-prenumeration. Därför kan du inte välja en gatewayinstallation som redan är associerad med en annan gatewayresurs. De här installationerna visas inte i **listan Installationsnamn.**

  Om du vill granska gatewayregistreringarna i Azure Portal du alla dina Azure-resurser som har resurstypen Lokala **datagatewayer** i *alla* dina Azure-prenumerationer. Information om hur du tar bort länken till gatewayinstallationen från den andra gatewayresursen finns i [Ta bort gatewayresurs.](#change-delete-gateway-resource)

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Nästa steg

* [Skydda dina logikappar](./logic-apps-securing-a-logic-app.md)
* [Vanliga exempel och scenarier för logikappar](./logic-apps-examples-and-scenarios.md)
