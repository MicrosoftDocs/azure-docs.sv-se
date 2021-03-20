---
title: Ansluta till Azure Blob Storage
description: Skapa och hantera blobbar i Azure Storage-konton med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: cd23ff0f5ad9912440d38903a344011b069aaf16
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92677717"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Skapa och hantera blobbar i Azure Blob Storage med Azure Logic Apps

Den här artikeln visar hur du kan komma åt och hantera filer som lagras som blobbar i ditt Azure Storage-konto inifrån en Logic-app med Azure Blob Storage-anslutningen. På så sätt kan du skapa Logi Kap par som automatiserar uppgifter och arbets flöden för att hantera dina filer. Du kan till exempel skapa logikappar som skapar, hämtar, uppdaterar och tar bort filer i ditt lagringskonto.

Anta att du har ett verktyg som uppdateras på en Azure-webbplats. Detta fungerar som utlösare för din Logic app. När den här händelsen inträffar kan du låta din Logic app uppdatera en fil i din Blob Storage-behållare, som är en åtgärd i din Logic app.

Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabb start: skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Information om anslutningsspecifika teknisk information finns i referens för [Azure Blob Storage Connector](/connectors/azureblobconnector/).

> [!IMPORTANT]
> Logic Apps kan inte komma åt lagrings konton som ligger bakom brand väggar om båda finns i samma region. Som en lösning kan du använda dina Logi Kap par och lagrings konton i olika regioner. Mer information om hur du aktiverar åtkomst från Azure Logic Apps till lagrings konton bakom brand väggar finns i avsnittet [åtkomst till lagrings konton bakom brand väggar](#storage-firewalls) längre fram i det här avsnittet.

<a name="blob-storage-limits"></a>

## <a name="limits"></a>Gränser

* Som standard kan Azure Blob Storage-åtgärder läsa eller skriva filer som är *50 MB eller mindre*. För att hantera filer som är större än 50 MB men upp till 1024 MB, stöder Azure Blob Storage-åtgärder [meddelande segment](../logic-apps/logic-apps-handle-large-messages.md). Åtgärden **Hämta BLOB-innehåll** använder implicit segment.

* Azure Blob Storage-utlösare stöder inte segment. När du begär fil innehåll väljer utlösare endast filer som är 50 MB eller mindre. Följ det här mönstret om du vill hämta filer som är större än 50 MB:

  * Använd en Azure Blob Storage-utlösare som returnerar fil egenskaper, till exempel **när en BLOB läggs till eller ändras (endast egenskaper)**.

  * Följ utlösaren med åtgärden Azure Blob Storage **Hämta BLOB-innehåll** , som läser den fullständiga filen och som implicit använder segment.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett [Azure Storage-konto och en lagrings behållare](../storage/blobs/storage-quickstart-blobs-portal.md)

* Den Logic-app där du behöver åtkomst till ditt Azure Blob Storage-konto. Om du vill starta din Logic-app med en Azure Blob Storage-utlösare behöver du en [Tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Lägg till Blob Storage-utlösare

I Azure Logic Apps måste varje Logi Kap par starta med en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts)som utlöses när en enskild händelse inträffar eller när ett särskilt villkor uppfylls. Varje gång utlösaren utlöses skapar Logic Apps-motorn en Logic App-instans och börjar köra appens arbets flöde.

Det här exemplet visar hur du kan starta ett Logic app-arbetsflöde med **när en BLOB läggs till eller ändras (endast egenskaper)** utlöses när en blobs egenskaper läggs till eller uppdateras i din lagrings behållare.

1. I [Azure Portal](https://portal.azure.com) eller Visual Studio skapar du en tom Logic-app som öppnar Logic App Designer. I det här exemplet används Azure Portal.

2. I rutan Sök anger du "Azure Blob" som filter. Välj den utlösare som du vill använda från listan utlösare.

   I det här exemplet används den här utlösaren: **när en BLOB läggs till eller ändras (endast egenskaper)**

   ![Välj Azure Blob Storage-utlösare](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. Om du uppmanas att ange anslutnings information [skapar du en Blob Storage-anslutning nu](#create-connection). Eller, om anslutningen redan finns, anger du den information som krävs för utlösaren.

   I det här exemplet väljer du den behållare och mapp som du vill övervaka.

   1. I rutan **behållare** väljer du mappikonen.

   2. I mapplistan väljer du höger vinkel paren tes ( **>** ) och bläddra tills du hittar och väljer den mapp som du vill använda.

      ![Välj lagringsmappen som ska användas med utlösare](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Välj intervall och frekvens för hur ofta du vill att utlösaren ska söka efter ändringar i mappen.

4. När du är klar väljer du **Spara** i verktygsfältet designer.

5. Fortsätt nu att lägga till en eller flera åtgärder i din Logic app för de uppgifter som du vill utföra med utlösnings resultaten.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Lägg till Blob Storage-åtgärd

I Azure Logic Apps är en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) ett steg i arbets flödet som följer en utlösare eller en annan åtgärd. I det här exemplet börjar Logic-appen med [upprepnings utlösaren](../connectors/connectors-native-recurrence.md).

1. Öppna din Logic app i Logic App Designer i [Azure Portal](https://portal.azure.com) eller Visual Studio. I det här exemplet används Azure Portal.

2. I Logic App Designer går du till utlösaren eller åtgärden och väljer **nytt steg**.

   ![Lägg till nytt steg i Logic app-arbetsflöde](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   Om du vill lägga till en åtgärd mellan befintliga steg flyttar du musen över den anslutande pilen. Välj plus tecknet ( **+** ) som visas och välj **Lägg till en åtgärd**.

3. I rutan Sök anger du "Azure Blob" som filter. Välj den åtgärd du vill använda i listan åtgärder.

   I det här exemplet används den här åtgärden: **Hämta BLOB-innehåll**

   ![Välj åtgärd för Azure Blob Storage](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. Om du uppmanas att ange anslutnings information [skapar du din Azure Blob Storage-anslutning nu](#create-connection).
Eller, om anslutningen redan finns, anger du den information som krävs för åtgärden.

   I det här exemplet väljer du den fil som du vill använda.

   1. Välj mappikonen från rutan **BLOB** .
  
      ![Välj lagringsmappen som ska användas med åtgärden](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Sök efter och välj den fil som du vill använda baserat på blobens **ID-** nummer. Du kan hitta detta **ID-** nummer i blobens metadata som returneras av den tidigare beskrivna Blob Storage-utlösaren.

5. När du är klar väljer du **Spara** i verktygsfältet designer.
Om du vill testa din Logic-App kontrollerar du att den valda mappen innehåller en blob.

I det här exemplet hämtas endast innehållet för en blob. Om du vill visa innehållet lägger du till en annan åtgärd som skapar en fil med bloben med hjälp av en annan koppling. Lägg till exempel till en OneDrive-åtgärd som skapar en fil baserat på BLOB-innehållet.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Anslut till lagrings konto

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. När du uppmanas att skapa anslutningen anger du den här informationen:

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Anslutnings namn** | Ja | <*anslutnings namn*> | Namnet som ska skapas för anslutningen |
   | **Lagrings konto** | Ja | <*lagring – konto*> | Välj ditt lagrings konto i listan. |
   ||||

   Exempel:

   ![Skapa Azure Blob Storage-konto anslutning](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png) 

1. När du är klar väljer du **skapa**

1. När du har skapat anslutningen fortsätter du med [Lägg till Blob Storage-utlösare](#add-trigger) eller [Lägg till Blob Storage-åtgärd](#add-action).

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här anslutningen, till exempel utlösare, åtgärder och begränsningar som beskrivs av kopplingens Swagger-fil finns på [kopplingens referens sida](/connectors/azureblobconnector/).

> [!NOTE]
> För logi Kap par i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandets gränser](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>Åtkomst till lagrings konton bakom brand väggar

Du kan lägga till nätverks säkerhet till ett Azure Storage-konto genom att begränsa åtkomsten med [brand Väggs-och brand Väggs regler](../storage/common/storage-network-security.md). Den här installationen skapar dock en utmaning för Azure och andra Microsoft-tjänster som behöver åtkomst till lagrings kontot. Lokal kommunikation i data centret är en sammanfattning av de interna IP-adresserna, så du kan inte konfigurera brand Väggs regler med IP-begränsningar. Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](../storage/common/storage-network-security.md).

Här följer flera alternativ för att komma åt lagrings konton bakom brand väggar från Azure Logic Apps med hjälp av Azure Blob Storage-anslutningen eller andra lösningar:

* Azure Storage Blob-anslutningsapp

  * [Åtkomst till lagrings konton i andra regioner](#access-other-regions)
  * [Åtkomst till lagrings konton via ett betrott virtuellt nätverk](#access-trusted-virtual-network)

* Andra lösningar

  * [Åtkomst till lagrings konton som en betrodd tjänst med hanterade identiteter](#access-trusted-service)
  * [Åtkomst till lagrings konton via Azure API Management](#access-api-management)

<a name="access-other-regions"></a>

### <a name="problems-accessing-storage-accounts-in-the-same-region"></a>Problem med att komma åt lagrings konton i samma region

Logi Kap par kan inte direkt komma åt lagrings konton bakom brand väggar när de är båda i samma region. Som en lösning kan du lägga in Logi Kap par i en region som skiljer sig från ditt lagrings konto och ge åtkomst till de [utgående IP-adresserna för de hanterade anslutningarna i din region](../logic-apps/logic-apps-limits-and-config.md#outbound).

> [!NOTE]
> Den här lösningen gäller inte Azure Table Storage Connector och Azure Queue Storage-anslutningen. Använd i stället den inbyggda HTTP-utlösaren och åtgärder för att få åtkomst till din Table Storage eller Queue Storage.

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>Åtkomst till lagrings konton via ett betrott virtuellt nätverk

Du kan lägga till lagrings kontot i ett virtuellt Azure-nätverk som du hanterar och sedan lägga till det virtuella nätverket i listan över betrodda virtuella nätverk. Om du vill att din Logic app ska ha åtkomst till lagrings kontot via ett [betrott virtuellt nätverk](../virtual-network/virtual-networks-overview.md)måste du distribuera den till en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)som kan ansluta till resurser i ett virtuellt nätverk. Du kan sedan lägga till undernät i denna ISE i listan över betrodda. Azure Storage-kopplingar, till exempel Blob Storages anslutning, kan komma åt lagrings behållaren direkt. Den här installationen är samma upplevelse som när du använder tjänstens slut punkter från en ISE.

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>Åtkomst till lagrings konton som en betrodd tjänst med hanterade identiteter

För att ge Microsoft-betrodda tjänster åtkomst till ett lagrings konto via en brand vägg kan du konfigurera ett undantag för det lagrings kontot för dessa tjänster. Den här lösningen tillåter Azure-tjänster som har stöd [för hanterade identiteter för autentisering](../active-directory/managed-identities-azure-resources/overview.md) för åtkomst till lagrings konton bakom brand väggar som betrodda tjänster. För att en Logic app i Global Azure med flera innehavare ska kunna komma åt dessa lagrings konton, [aktiverar du först hanterad identitets support](../logic-apps/create-managed-service-identity.md) i Logic app. Sedan använder du HTTP-åtgärden eller utlösaren i din Logic app och [anger deras autentiseringstyp för att använda din Logic Apps-hanterade identitet](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). I det här scenariot kan du *bara* använda http-åtgärden eller utlösaren.

Följ dessa allmänna steg om du vill ställa in undantag och hanterad identitets support:

1. Välj **brand väggar och virtuella nätverk** på ditt lagrings konto under **Inställningar**. Under **Tillåt åtkomst från** väljer du alternativet **valda nätverk** så att de relaterade inställningarna visas.

1. Under **undantag** väljer **du Tillåt att betrodda Microsoft-tjänster har åtkomst till det här lagrings kontot** och väljer sedan **Spara**.

   ![Välj undantag som tillåter Microsoft-betrodda tjänster](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. I din Logic app-inställningar [aktiverar du stöd för den hanterade identiteten](../logic-apps/create-managed-service-identity.md).

1. I din Logic app-arbetsflöde lägger du till och konfigurerar HTTP-åtgärden eller utlösaren för åtkomst till lagrings kontot eller entiteten.

   > [!IMPORTANT]
   > För utgående HTTP-åtgärder eller utlösnings anrop till Azure Storage konton, se till att rubriken för begäran innehåller `x-ms-version` egenskapen och API-versionen för den åtgärd som du vill köra på lagrings kontot. Mer information finns i [autentisera åtkomst med hanterad identitet](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) och [versions hantering för Azure Storage tjänster](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests).

1. [Välj den hanterade identitet](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) som ska användas för autentisering på den åtgärden.

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Åtkomst till lagrings konton via Azure API Management

Om du använder en dedikerad nivå för [API Management](../api-management/api-management-key-concepts.md)kan du följa lagrings-API: et genom att använda API Management och tillåta de senares IP-adresser genom brand väggen. I princip lägger du till det virtuella Azure-nätverket som används av API Management i lagrings kontots brand Väggs inställning. Du kan sedan använda antingen åtgärden API Management eller HTTP-åtgärden för att anropa API: erna för Azure Storage. Men om du väljer det här alternativet måste du hantera autentiseringsprocessen själv. Mer information finns i [enkel arkitektur för företags integrering](/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)