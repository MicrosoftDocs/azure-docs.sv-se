---
title: Autentisera med hanterade identiteter
description: Åtkomst till resurser som skyddas av Azure Active Directory utan att logga in med autentiseringsuppgifter eller hemligheter med hjälp av en hanterad identitet
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 95d892bf7a0c0e395289d4a5535cd9b6b789b055
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565935"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Autentisera åtkomst till Azure-resurser med hjälp av hanterade identiteter i Azure Logic Apps

För att enkelt komma åt andra resurser som skyddas av Azure Active Directory (Azure AD) och autentisera din identitet utan att logga in, kan din Logic app använda en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) (tidigare HANTERAD TJÄNSTIDENTITET eller MSI) i stället för autentiseringsuppgifter eller hemligheter. Azure hanterar den här identiteten åt dig och hjälper till att skydda dina autentiseringsuppgifter eftersom du inte måste ange eller rotera hemligheter.

Azure Logic Apps stöder både [*systemtilldelade*](../active-directory/managed-identities-azure-resources/overview.md) och [*användarspecifika*](../active-directory/managed-identities-azure-resources/overview.md) hanterade identiteter. Din Logi Kap par kan använda antingen den systemtilldelade identiteten eller en *enskild* användardefinierad identitet, som du kan dela i en grupp av logi Kap par, men inte båda. För närvarande har endast [vissa inbyggda utlösare och åtgärder](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) stöd för hanterade identiteter, inte hanterade anslutningar eller anslutningar, till exempel:

* HTTP
* Azure Functions
* Azure API Management
* Azure App Services

Den här artikeln visar hur du konfigurerar båda typerna av hanterade identiteter för din Logic app. Mer information finns i de här ämnena:

* [Utlösare och åtgärder som har stöd för hanterade identiteter](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Autentiseringstyper som stöds för utgående anrop](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Begränsningar för hanterade identiteter för Logic Apps](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Azure-tjänster som stöder Azure AD-autentisering med hanterade identiteter](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Både den hanterade identiteten och den Azure-resurs där du behöver åtkomst måste använda samma Azure-prenumeration.

* Om du vill ge en hanterad identitets åtkomst till en Azure-resurs måste du lägga till en roll i mål resursen för den identiteten. Om du vill lägga till roller måste du ha [administratörs behörighet för Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md) som kan tilldela roller till identiteter i motsvarande Azure AD-klient.

* Den Azure-resurs som du vill få åtkomst till. På den här resursen lägger du till en roll för den hanterade identiteten som hjälper Logic-appen att autentisera åtkomsten till mål resursen.

* Den Logic app där du vill använda [utlösaren eller åtgärder som har stöd för hanterade identiteter](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

## <a name="enable-managed-identity"></a>Aktivera hanterad identitet

Om du vill ställa in den hanterade identitet som du vill använda följer du länken för den identiteten:

* [Systemtilldelad identitet](#system-assigned)
* [Användare tilldelad identitet](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Aktivera systemtilldelad identitet

Till skillnad från användarens tilldelade identiteter behöver du inte skapa den systemtilldelade identiteten manuellt. För att ställa in den systemtilldelade identiteten för din Logi Kap par, är följande alternativ som du kan använda:

* [Azure-portalen](#azure-portal-system-logic-app)
* [Azure Resource Manager-mallar](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Aktivera systemtilldelad identitet i Azure Portal

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic App Designer.

1. På menyn Logic app, under **Inställningar**, väljer du **identitet**. Välj **system som har tilldelats**  >  **On**  >  **Spara**. När du blir ombedd att bekräfta väljer du **Ja**.

   ![Aktivera den systemtilldelade identiteten](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Om du får ett fel meddelande om att du bara kan ha en enda hanterad identitet, är din Logic app redan kopplad till den tilldelade identiteten. Innan du kan lägga till den systemtilldelade identiteten måste du först *ta bort* den användare som tilldelats identiteten från din Logic app.

   Din Logic app kan nu använda den systemtilldelade identiteten, som registreras med Azure Active Directory och representeras av ett objekt-ID.

   ![Objekt-ID för systemtilldelad identitet](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Objekt-ID** | <*identitet-resurs-ID*> | En globalt unik identifierare (GUID) som representerar den systemtilldelade identiteten för din Logic app i en Azure AD-klient |
   ||||

1. Följ nu [stegen som ger identiteten åtkomst till resursen](#access-other-resources) senare i det här avsnittet.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Aktivera systemtilldelad identitet i Azure Resource Manager mall

Om du vill automatisera skapandet och distributionen av Azure-resurser, till exempel Logic Apps, kan du använda [Azure Resource Manager mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Om du vill aktivera den systemtilldelade hanterade identiteten för din Logic app i mallen lägger du till `identity` objektet och den `type` underordnade egenskapen till logi kap Apps resurs definition i mallen, till exempel:

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

När Azure skapar din resurs definition för Logic app `identity` får objektet följande ytterligare egenskaper:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Egenskap (JSON) | Värde | Beskrivning |
|-----------------|-------|-------------|
| `principalId` | <*huvud-ID*> | GUID (globalt unik identifierare) för tjänstens huvud namn för den hanterade identitet som representerar din Logic-app i Azure AD-klienten. Detta GUID visas ibland som "objekt-ID" eller `objectID` . |
| `tenantId` | <*Azure-AD-Tenant-ID*> | GUID (globalt unik identifierare) som representerar Azure AD-klienten där Logic app nu är medlem. I Azure AD-klienten har tjänstens huvud namn samma namn som Logic App-instansen. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Aktivera användardefinierad identitet

Om du vill konfigurera en användardefinierad hanterad identitet för din Logic app måste du först skapa den identiteten som en separat fristående Azure-resurs. Här följer de alternativ som du kan använda:

* [Azure-portalen](#azure-portal-user-identity)
* [Azure Resource Manager-mallar](#template-user-identity)
* Azure PowerShell
  * [Skapa användare tilldelad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Lägg till rolltilldelning](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [Skapa användare tilldelad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Lägg till rolltilldelning](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* REST-API för Azure
  * [Skapa användare tilldelad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Lägg till rolltilldelning](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Skapa användare tilldelad identitet i Azure Portal

1. I rutan [Azure Portal](https://portal.azure.com), i sökrutan på vilken sida som helst, anger `managed identities` du och väljer **hanterade identiteter**.

   ![Sök efter och välj "hanterade identiteter"](./media/create-managed-service-identity/find-select-managed-identities.png)

1. Under **hanterade identiteter**väljer du **Lägg till**.

   ![Lägg till ny hanterad identitet](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Ange information om din hanterade identitet och välj sedan **skapa**, till exempel:

   ![Skapa användardefinierad hanterad identitet](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Resursnamn** | Yes | <*användarens tilldelad identitet-namn*> | Namnet för att ge din användar tilldelnings identitet. I det här exemplet används "Fabrikam-User-Assigned-Identity". |
   | **Prenumeration** | Yes | <*Azure-prenumeration-namn*> | Namnet på den Azure-prenumeration som ska användas |
   | **Resursgrupp** | Yes | <*Azure-resurs-grupp-namn*> | Namnet på resurs gruppen som ska användas. Skapa en ny grupp eller Välj en befintlig grupp. I det här exemplet skapas en ny grupp med namnet "Fabrikam-Managed-Identities-RG". |
   | **Plats** | Yes | <*Azure-region*> | Den Azure-region där information om din resurs ska lagras. I det här exemplet används "västra USA". |
   |||||

   Nu kan du lägga till den användardefinierade identiteten i din Logic app. Du kan inte lägga till fler än en användardefinierad identitet i din Logic app.

1. I Azure Portal kan du söka efter och öppna din Logic app i Logic App Designer.

1. Välj **identitet**under **Inställningar**på menyn Logic app och välj sedan **tilldelad användare**  >  **Add**.

   ![Lägg till användardefinierad hanterad identitet](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. I rutan **Lägg till användare som tilldelats hanterad identitet** väljer du din Azure-prenumeration i listan **prenumeration** om den inte redan är vald. I listan som visar *alla* hanterade identiteter i den prenumerationen letar du reda på och väljer den användarspecifika identitet som du vill använda. Om du vill filtrera listan anger du namnet på identiteten eller resurs gruppen i sökrutan **användare tilldelade hanterade identiteter** . När du är klar väljer du **Lägg till**.

   ![Välj den användar tilldelnings identitet som ska användas](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Om du får ett fel meddelande om att du bara kan ha en enda hanterad identitet, är din Logic app redan kopplad till den systemtilldelade identiteten. Innan du kan lägga till en användardefinierad identitet måste du först inaktivera den systemtilldelade identiteten på din Logic-app.

   Din Logi Kap par är nu kopplad till den användare som tilldelats den hanterade identiteten.

   ![Association med användardefinierad identitet](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Följ nu [stegen som ger identiteten åtkomst till resursen](#access-other-resources) senare i det här avsnittet.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Skapa användare tilldelad identitet i en Azure Resource Manager mall

Om du vill automatisera skapandet och distributionen av Azure-resurser, till exempel Logic Apps, kan du använda [Azure Resource Manager mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)som stöder [användarspecifika identiteter för autentisering](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md). I mallens `resources` avsnitt kräver din Logic Apps-resurs definitionen följande objekt:

* Ett `identity` objekt med `type` egenskapen inställd på `UserAssigned`

* Ett underordnat `userAssignedIdentities` objekt som anger den användardefinierade resursen och namnet

Det här exemplet visar en resurs definition för en logisk app för en HTTP-begäran och innehåller ett icke-parametriserade `identity` objekt. Svaret på Request-begäran och efterföljande GET-åtgärd har också följande `identity` objekt:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

Om din mall även omfattar resurs definitionen för den hanterade identiteten kan du Parameterisera `identity` objektet. Det här exemplet visar hur det underordnade `userAssignedIdentities` objektet refererar till en `userAssignedIdentity` variabel som du definierar i mallens `variables` avsnitt. Den här variabeln refererar till resurs-ID: t för din användar tilldelade identitet.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {}
      }
  ]
}
```

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Ge identitets åtkomst till resurser

Innan du kan använda din Logic Apps-hanterade identitet för autentisering ska du konfigurera åtkomst för den identiteten på den Azure-resurs där du planerar att använda identiteten. För att slutföra den här uppgiften tilldelar du den aktuella rollen till den identiteten på Azure-resursen. Här följer de alternativ som du kan använda:

* [Azure-portalen](#azure-portal-assign-access)
* [Azure Resource Manager-mall](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)) – mer information finns i [lägga till roll tilldelning med hjälp av Azure RBAC och Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Azure CLI ([AZ Role Assignment Create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)) – mer information finns i [lägga till roll tilldelning med hjälp av Azure RBAC och Azure CLI](../role-based-access-control/role-assignments-cli.md).
* [REST-API för Azure](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Tilldela åtkomst i Azure Portal

1. I [Azure Portal](https://portal.azure.com)går du till den Azure-resurs där du vill att den hanterade identiteten ska ha åtkomst.

1. På resurs menyn väljer du behörighet för **åtkomst kontroll (IAM)**  >  **Role assignments** där du kan granska de aktuella roll tilldelningarna för resursen. Välj **Lägg till**  >  **Lägg till roll tilldelning**i verktygsfältet.

   ![Välj Lägg till > Lägg till roll tilldelning](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Om alternativet **Lägg till roll tilldelning** är inaktiverat har du förmodligen inte behörighet. Mer information om behörigheter som låter dig hantera roller för resurser finns i [Administratörs roll behörigheter i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. Under **Lägg till roll tilldelning**väljer du en **roll** som ger din identitet nödvändig åtkomst till mål resursen.

   För det här avsnittets exempel behöver din identitet en [roll som kan komma åt blobben i en Azure Storage-behållare](../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights).

   ![Välj rollen "Storage BLOB data Contributor"](./media/create-managed-service-identity/select-role-for-identity.png)

1. Följ dessa steg för din hanterade identitet:

   * **Systemtilldelad identitet**

     1. I rutan **tilldela åtkomst till väljer du** **Logic app**. När egenskapen **prenumeration** visas väljer du den Azure-prenumeration som är associerad med din identitet.

        ![Välj åtkomst för systemtilldelad identitet](./media/create-managed-service-identity/assign-access-system.png)

     1. Under rutan **Välj** väljer du din Logic app i listan. Om listan är för lång använder du rutan **Välj** för att filtrera listan.

        ![Välj Logic app för systemtilldelad identitet](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Användare tilldelad identitet**

     1. I rutan **tilldela åtkomst till väljer du** **tilldelad hanterad identitet**. När egenskapen **prenumeration** visas väljer du den Azure-prenumeration som är associerad med din identitet.

        ![Välj åtkomst för användarens tilldelade identitet](./media/create-managed-service-identity/assign-access-user.png)

     1. Under rutan **Välj** väljer du din identitet i listan. Om listan är för lång använder du rutan **Välj** för att filtrera listan.

        ![Välj din användar tilldelad identitet](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. När du är klar väljer du **Spara**.

   Mål resursens roll tilldelnings lista visar nu den valda hanterade identiteten och rollen. Det här exemplet visar hur du kan använda den systemtilldelade identiteten för en Logic app och en användardefinierad identitet för en grupp andra Logic Apps.

   ![Hanterade identiteter och roller har lagts till i mål resursen](./media/create-managed-service-identity/added-roles-for-identities.png)

   Mer information får du [genom att tilldela en hanterad identitets åtkomst till en resurs med hjälp av Azure Portal](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

1. Följ nu [stegen för att autentisera åtkomst med identiteten](#authenticate-access-with-identity) i en utlösare eller åtgärd som stöder hanterade identiteter.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Autentisera åtkomst med hanterad identitet

När du har [aktiverat den hanterade identiteten för din Logic app](#azure-portal-system-logic-app) och [ger den identitets åtkomst till mål resursen eller entiteten](#access-other-resources)kan du använda identiteten i [utlösare och åtgärder som stöder hanterade identiteter](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

> [!IMPORTANT]
> Om du har en Azure-funktion där du vill använda den systemtilldelade identiteten aktiverar du först [autentisering för Azure Functions](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

De här stegen visar hur du använder den hanterade identiteten med en utlösare eller åtgärd via Azure Portal. Information om hur du anger den hanterade identiteten i en utlösare eller åtgärds underliggande JSON-definition finns i [hanterad identitets autentisering](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic Apps designer.

1. Om du inte har gjort det lägger du till [utlösaren eller åtgärden som har stöd för hanterade identiteter](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   HTTP-utlösaren eller åtgärden kan till exempel använda den systemtilldelade identitet som du har aktiverat för din Logic app. I allmänhet använder HTTP-utlösaren eller åtgärden dessa egenskaper för att ange den resurs eller entitet som du vill få åtkomst till:

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Metod** | Yes | HTTP-metoden som används av den åtgärd som du vill köra |
   | **URI** | Yes | Slut punkts-URL för åtkomst till Azure-resursen eller-entiteten. URI-syntaxen innehåller vanligt vis [resurs-ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) för Azure-resursen eller-tjänsten. |
   | **Sidhuvuden** | No | Eventuella rubrik värden som du behöver eller vill inkludera i den utgående begäran, till exempel innehålls typen |
   | **Frågor** | No | Alla frågeparametrar som du behöver eller vill inkludera i begäran, till exempel parametern för en åtgärd eller API-versionen för den åtgärd som du vill köra |
   | **Autentisering** | Yes | Autentiseringstypen som används för att autentisera åtkomsten till mål resursen eller entiteten |
   ||||

   Som ett särskilt exempel förutsätter vi att du vill köra [ögonblicks bilds-bloben](/rest/api/storageservices/snapshot-blob) på en BLOB i det Azure Storage konto där du tidigare har konfigurerat åtkomst till din identitet. Men [Azure Blob Storage-anslutningen](/connectors/azureblob/) har för närvarande inte den här åtgärden. I stället kan du köra den här åtgärden med hjälp av [http-åtgärden](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) eller någon annan [REST API åtgärd för BLOB service](/rest/api/storageservices/operations-on-blobs).

   > [!IMPORTANT]
   > Om du vill komma åt Azure Storage-konton bakom brand väggar genom att använda HTTP-förfrågningar och hanterade identiteter, kontrollerar du att du även konfigurerar ditt lagrings konto med [undantaget som ger åtkomst av betrodda Microsoft-tjänster](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

   För att köra [ögonblicks bildens BLOB](/rest/api/storageservices/snapshot-blob)-åtgärd anger http-åtgärden följande egenskaper:

   | Egenskap | Krävs | Exempelvärde | Description |
   |----------|----------|---------------|-------------|
   | **Metod** | Yes | `PUT`| HTTP-metoden som ögonblicks bildens BLOB-åtgärd använder |
   | **URI** | Yes | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Resurs-ID för en Azure Blob Storage-fil i den globala Azure-miljön (offentlig) som använder den här syntaxen |
   | **Sidhuvuden** | Ja, för Azure Storage | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | De- `x-ms-blob-type` och- `x-ms-version` huvud värden som krävs för Azure Storage åtgärder. <p><p>**Viktigt**: i utgående http-utlösare och åtgärds begär anden för Azure Storage, kräver huvudet `x-ms-version` egenskapen och API-versionen för den åtgärd som du vill köra. <p>Mer information finns i de här ämnena: <p><p>- [Begärandehuvuden – ögonblicks bilds-BLOB](/rest/api/storageservices/snapshot-blob#request) <br>- [Versions hantering för Azure Storage tjänster](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Frågor** | Ja, för den här åtgärden | `comp` = `snapshot` | Frågeparametern och värdet för ögonblicks bildens BLOB-åtgärd. |
   |||||

   Här är exempel på HTTP-åtgärd som visar alla dessa egenskaps värden:

   ![Lägg till en HTTP-åtgärd för att få åtkomst till en Azure-resurs](./media/create-managed-service-identity/http-action-example.png)

1. Lägg nu till egenskapen **autentisering** i http-åtgärden. I listan **Lägg till ny parameter** väljer du **autentisering**.

   ![Lägg till egenskapen "autentisering" till HTTP-åtgärd](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Det är inte alla utlösare och åtgärder som har stöd för att lägga till en autentiseringstyp. Mer information finns i [lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. I listan **Autentiseringstyp** väljer du **hanterad identitet**.

   ![För "autentisering" väljer du hanterad identitet](./media/create-managed-service-identity/select-managed-identity.png)

1. Från listan över hanterade identiteter väljer du bland de tillgängliga alternativen baserat på ditt scenario.

   * Om du konfigurerar den systemtilldelade identiteten väljer du **systemtilldelad hanterad identitet** om den inte redan har valts.

     ![Välj "systemtilldelad hanterad identitet"](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Om du konfigurerar en användardefinierad identitet väljer du den identiteten om den inte redan har valts.

     ![Välj den användare-tilldelade identiteten](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Det här exemplet fortsätter med **systemtilldelad hanterad identitet**.

1. På vissa utlösare och åtgärder visas även egenskapen **Audience** där du anger mål resurs-ID: t. Ange egenskapen **Audience** till [resurs-ID för mål resursen eller tjänsten](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). Annars använder egenskapen **Audience** som standard `https://management.azure.com/` resurs-ID: t, som är resurs-ID: t för Azure Resource Manager.

   > [!IMPORTANT]
   > Kontrol lera att mål resurs-ID: t *exakt matchar* det värde som Azure Active Directory (AD) förväntar sig, inklusive eventuella avslutande snedstreck. Resurs-ID för alla Azure Blob Storage-konton kräver till exempel ett avslutande snedstreck. Resurs-ID för ett angivet lagrings konto kräver dock inte något avslutande snedstreck. Kontrol lera [resurs-ID: na för de Azure-tjänster som stöder Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   I det här exemplet anges egenskapen **Audience** till `https://storage.azure.com/` så att åtkomsttoken som används för autentisering är giltiga för alla lagrings konton. Du kan dock även ange rot tjänstens URL, `https://fabrikamstorageaccount.blob.core.windows.net` för ett angivet lagrings konto.

   ![Ange Audience-egenskapen till mål resurs-ID](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Mer information om hur du auktoriserar åtkomst med Azure AD för Azure Storage finns i följande avsnitt:

   * [Ge åtkomst till Azure-blobbar och-köer med hjälp av Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Ge åtkomst till Azure Storage med Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Fortsätt att skapa Logic-appen på det sätt som du vill.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Inaktivera hanterad identitet

Om du vill sluta använda en hanterad identitet för din Logic app har du följande alternativ:

* [Azure-portalen](#azure-portal-disable)
* [Azure Resource Manager-mallar](#template-disable)
* Azure PowerShell
  * [Ta bort roll tilldelning](../role-based-access-control/role-assignments-powershell.md)
  * [Ta bort användardefinierad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [Ta bort roll tilldelning](../role-based-access-control/role-assignments-cli.md)
  * [Ta bort användardefinierad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* REST-API för Azure
  * [Ta bort roll tilldelning](../role-based-access-control/role-assignments-rest.md)
  * [Ta bort användardefinierad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Om du tar bort din Logic app tar Azure automatiskt bort den hanterade identiteten från Azure AD.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Inaktivera hanterad identitet i Azure Portal

I Azure Portal tar du först bort identitetens åtkomst till [mål resursen](#disable-identity-target-resource). Stäng sedan av den systemtilldelade identiteten eller ta bort den användare som tilldelats identiteten från [din Logic app](#disable-identity-logic-app).

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Ta bort identitets åtkomst från resurser

1. I [Azure Portal](https://portal.azure.com)går du till den Azure-resurs där du vill ta bort åtkomsten för den hanterade identiteten.

1. Från mål resursens meny väljer du **åtkomst kontroll (IAM)**. Under verktygsfältet väljer du **roll tilldelningar**.

1. I listan Roller väljer du de hanterade identiteter som du vill ta bort. Välj **ta bort**i verktygsfältet.

   > [!TIP]
   > Om alternativet **ta bort** är inaktiverat har du förmodligen inte behörighet. Mer information om behörigheter som låter dig hantera roller för resurser finns i [Administratörs roll behörigheter i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Den hanterade identiteten tas nu bort och har inte längre åtkomst till mål resursen.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Inaktivera hanterad identitet i Logic app

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic App Designer.

1. På menyn Logic app, under **Inställningar**, väljer du **identitet**och följer sedan stegen för din identitet:

   * Välj **system som har tilldelats**  >  **On**  >  **Spara**. När du blir ombedd att bekräfta väljer du **Ja**.

     ![Inaktivera den systemtilldelade identiteten](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Välj **tilldelad användare** och hanterad identitet och välj sedan **ta bort**. När du blir ombedd att bekräfta väljer du **Ja**.

     ![Ta bort den användare som tilldelats identiteten](./media/create-managed-service-identity/remove-user-assigned-identity.png)

Den hanterade identiteten är nu inaktive rad på din Logic app.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Inaktivera hanterad identitet i Azure Resource Manager mall

Om du har skapat logik appens hanterade identitet med hjälp av en Azure Resource Manager mall ställer du in `identity` objektets `type` underordnade egenskap till `None` .

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Nästa steg

* [Säker åtkomst och data i Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
