---
title: Autentisera med hanterade identiteter
description: Få åtkomst till resurser som skyddas Azure Active Directory utan att logga in med autentiseringsuppgifter eller hemligheter med hjälp av en hanterad identitet
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: 8e081257d70c9bc9c9f75df18b30f8dcf119e48e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763351"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Autentisera åtkomst till Azure-resurser med hjälp av hanterade identiteter i Azure Logic Apps

För att enkelt komma åt andra resurser som skyddas av Azure Active Directory (Azure AD) och autentisera din identitet kan logikappen använda en hanterad identitet [(tidigare](../active-directory/managed-identities-azure-resources/overview.md) hanterad tjänstidentitet eller MSI) i stället för autentiseringsuppgifter, hemligheter eller Azure AD-token. Azure hanterar den här identiteten åt dig och hjälper dig att skydda dina autentiseringsuppgifter eftersom du inte behöver hantera hemligheter eller använda Azure AD-token direkt.

Azure Logic Apps har stöd för [*både system-tilldelade*](../active-directory/managed-identities-azure-resources/overview.md) [*och användar tilldelade hanterade*](../active-directory/managed-identities-azure-resources/overview.md) identiteter. Logikappen eller enskilda anslutningar kan använda antingen  den system tilldelade identiteten eller en enskild användar tilldelad identitet, som du kan dela i en grupp med logikappar, men inte båda.

<a name="triggers-actions-managed-identity"></a>

## <a name="where-can-logic-apps-use-managed-identities"></a>Var kan logikappar använda hanterade identiteter?

För närvarande kan [endast specifika inbyggda utlösare](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions) och åtgärder och specifika hanterade anslutningsappar som stöder Azure AD OAuth använda en hanterad identitet för autentisering. [](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions) Här är till exempel ett val:

<a name="built-in-managed-identity"></a>

**Inbyggda utlösare och åtgärder**

* Azure API Management
* Azure App Services
* Azure Functions
* HTTP
* HTTP + Webhook

> [!NOTE]
> HTTP-utlösaren och åtgärden kan autentisera anslutningar till Azure Storage-konton bakom Azure-brandväggar med hjälp av den system tilldelade hanterade identiteten, men de kan inte använda den användar tilldelade hanterade identiteten för att autentisera samma anslutningar.

<a name="managed-connectors-managed-identity"></a>

**Hanterade anslutningsappar**

* Azure Automation
* Azure Event Grid
* Azure Key Vault
* Azure Resource Manager
* HTTP med Azure AD

Stöd för hanterade anslutningsappar är för närvarande i förhandsversion. Den aktuella listan finns i [Autentiseringstyper för utlösare och åtgärder som stöder autentisering.](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

Den här artikeln visar hur du ställer in båda typerna av hanterade identiteter för din logikapp. Mer information finns i de här ämnena:

* [Utlösare och åtgärder som stöder hanterade identiteter](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)
* [Begränsningar för hanterade identiteter för logikappar](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Azure-tjänster som stöder Azure AD-autentisering med hanterade identiteter](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Både den hanterade identiteten och azure-målresursen där du behöver åtkomst måste använda samma Azure-prenumeration.

* Om du vill ge en hanterad identitet åtkomst till en Azure-resurs måste du lägga till en roll i målresursen för den identiteten. Om du vill lägga till roller behöver [du Azure AD-administratörsbehörigheter](../active-directory/roles/permissions-reference.md) som kan tilldela roller till identiteter i motsvarande Azure AD-klientorganisation.

* Den Azure-målresurs som du vill komma åt. På den här resursen lägger du till en roll för den hanterade identiteten som hjälper logikappen att autentisera åtkomsten till målresursen.

* Logikappen där du vill använda utlösaren [eller åtgärderna som stöder hanterade identiteter](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

## <a name="enable-managed-identity"></a>Aktivera hanterad identitet

Om du vill konfigurera den hanterade identitet som du vill använda följer du länken för den identiteten:

* [System tilldelad identitet](#system-assigned)
* [Användar tilldelad identitet](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Aktivera system tilldelad identitet

Till skillnad från användar tilldelade identiteter behöver du inte skapa den system tilldelade identiteten manuellt. Här är de alternativ som du kan använda för att konfigurera den system tilldelade identiteten för logikappen:

* [Azure-portalen](#azure-portal-system-logic-app)
* [Azure Resource Manager-mallar](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Aktivera system tilldelad identitet i Azure Portal

1. I [Azure Portal](https://portal.azure.com)du logikappen i Logic App Designer.

1. På logikappmenyn går du till **Inställningar** och väljer **Identitet.** Välj **System tilldelad**  >  **På**  >  **Spara.** När Azure uppmanar dig att bekräfta väljer du **Ja.**

   ![Aktivera den system tilldelade identiteten](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Om du får ett felmeddelande om att du bara kan ha en enda hanterad identitet är logikappen redan associerad med den användar tilldelade identiteten. Innan du kan lägga till den system tilldelade identiteten måste du först *ta bort* den användar tilldelade identiteten från logikappen.

   Logikappen kan nu använda den system tilldelade identiteten, som är registrerad i Azure AD och representeras av ett objekt-ID.

   ![Objekt-ID för system tilldelad identitet](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Objekt-ID** | <*identity-resource-ID*> | En globalt unik identifierare (GUID) som representerar den system tilldelade identiteten för logikappen i en Azure AD-klientorganisation |
   ||||

1. Följ nu stegen [som ger identiteten åtkomst till resursen senare i](#access-other-resources) det här avsnittet.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Aktivera system tilldelad identitet i Azure Resource Manager mall

Om du vill automatisera skapandet och distributionen av Azure-resurser, till exempel logikappar, kan du [använda Azure Resource Manager mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Om du vill aktivera den system tilldelade hanterade identiteten för logikappen i mallen lägger du till objektet och den underordnade egenskapen i `identity` `type` logikappens resursdefinition i mallen, till exempel:

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

När Azure skapar en resursdefinition för logikappen `identity` hämtar objektet följande ytterligare egenskaper:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Egenskap (JSON) | Värde | Beskrivning |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | Globalt unik identifierare (GUID) för tjänstens huvudnamnsobjekt för den hanterade identiteten som representerar logikappen i Azure AD-klientorganisationen. Detta GUID visas ibland som ett "objekt-ID" eller `objectID` . |
| `tenantId` | <*Azure-AD-klientorganisations-ID*> | Globalt unik identifierare (GUID) som representerar Den Azure AD-klientorganisation där logikappen nu är medlem. I Azure AD-klientorganisationen har tjänstens huvudnamn samma namn som logikappinstansen. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Aktivera användar tilldelad identitet

Om du vill konfigurera en användar tilldelad hanterad identitet för logikappen måste du först skapa identiteten som en separat fristående Azure-resurs. Här är de alternativ som du kan använda:

* [Azure-portalen](#azure-portal-user-identity)
* [Azure Resource Manager-mallar](#template-user-identity)
* Azure PowerShell
  * [Skapa användar tilldelad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Lägg till rolltilldelning](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [Skapa användar tilldelad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Lägg till rolltilldelning](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Azure REST API
  * [Skapa användar tilldelad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Lägg till rolltilldelning](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Skapa användar tilldelad identitet i Azure Portal

1. I rutan [Azure Portal](https://portal.azure.com)i sökrutan på valfri sida anger du `managed identities` och väljer **Hanterade identiteter**.

   ![Leta upp och välj "Hanterade identiteter"](./media/create-managed-service-identity/find-select-managed-identities.png)

1. Under **Hanterade identiteter** väljer du Lägg **till**.

   ![Lägga till ny hanterad identitet](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Ange information om din hanterade identitet och välj **sedan Granska + skapa,** till exempel:

   ![Skapa användar tilldelad hanterad identitet](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Prenumeration** | Yes | <*Azure-prenumerationens namn*> | Namnet på Azure-prenumerationen som ska användas |
   | **Resursgrupp** | Yes | <*Azure-resource-group-name*> | Namnet på resursgruppen som ska användas. Skapa en ny grupp eller välj en befintlig grupp. I det här exemplet skapas en ny grupp med namnet `fabrikam-managed-identities-RG` . |
   | **Region** | Yes | <*Azure-region*> | Den Azure-region där information om din resurs ska lagras. I det här exemplet används "USA, västra". |
   | **Namn** | Yes | <*user-assigned-identity-name*> | Namnet som ska ge den användar tilldelade identiteten. I det här exemplet används `Fabrikam-user-assigned-identity`. |
   |||||

   När du har verifierat den här informationen skapar Azure din hanterade identitet. Nu kan du lägga till den användar tilldelade identiteten i logikappen. Du kan inte lägga till mer än en användar tilldelad identitet i logikappen.

1. I Azure Portal du och öppnar logikappen i Logic App Designer.

1. På logikappmenyn går du till **Inställningar,** väljer **Identitet** och sedan Användar **tilldelad Lägg**  >  **till.**

   ![Lägga till användar tilldelad hanterad identitet](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. I fönstret **Lägg till användar tilldelad hanterad** identitet går du **till listan** Prenumeration och väljer din Azure-prenumeration om du inte redan har valt den. I listan som visar alla *hanterade* identiteter i prenumerationen, hittar och väljer du den användar-tilldelade identitet som du vill använda. Filtrera listan genom att i **sökrutan Användartilldelningar för** hanterade identiteter ange namnet på identiteten eller resursgruppen. När du är klar väljer du Lägg **till**.

   ![Välj den användar tilldelade identitet som ska användas](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Om du får ett felmeddelande om att du bara kan ha en enda hanterad identitet är logikappen redan associerad med den system tilldelade identiteten. Innan du kan lägga till den användar tilldelade identiteten måste du först inaktivera den system tilldelade identiteten i logikappen.

   Logikappen är nu associerad med den användar tilldelade hanterade identiteten.

   ![Association med användar tilldelad identitet](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Följ nu stegen [som ger identiteten åtkomst till resursen senare i](#access-other-resources) det här avsnittet.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Skapa användar tilldelad identitet i en Azure Resource Manager mall

Om du vill automatisera skapandet och distributionen av Azure-resurser, till exempel [logikappar, kan](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)du använda Azure Resource Manager , som stöder [användardefinierade identiteter för autentisering](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md). I mallavsnittet `resources` kräver logikappens resursdefinition följande objekt:

* Ett `identity` objekt med egenskapen inställd `type` på `UserAssigned`

* Ett `userAssignedIdentities` underordnat objekt som anger den användar tilldelade resursen och namnet

Det här exemplet visar en resursdefinition för en logikapp för en HTTP PUT-begäran och innehåller ett icke-parametriserat `identity` objekt. Svaret på PUT-begäran och efterföljande GET-åtgärd har också det här `identity` objektet:

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

Om mallen även innehåller resursdefinitionen för den hanterade identiteten kan du parameterisera `identity` objektet. Det här exemplet visar hur det `userAssignedIdentities` underordnade objektet `userAssignedIdentity` refererar till en variabel som du definierar i mallens `variables` -avsnitt. Den här variabeln refererar till resurs-ID:t för din användartilldelningsidentitet.

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

## <a name="give-identity-access-to-resources"></a>Ge identitetsåtkomst till resurser

Innan du kan använda logikappens hanterade identitet för autentisering måste du konfigurera åtkomst för identiteten på den Azure-resurs där du planerar att använda identiteten. För att slutföra den här uppgiften tilldelar du lämplig roll till identiteten på Azure-målresursen. Här är de alternativ som du kan använda:

* [Azure-portalen](#azure-portal-assign-access)
* [Azure Resource Manager-mall](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)) – Mer information finns i Lägga till rolltilldelning med hjälp av [Azure RBAC och Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Azure CLI ([az role assignment create](/cli/azure/role/assignment#az_role_assignment_create)) – Mer information finns i Lägga till [rolltilldelning med hjälp av Azure RBAC och Azure CLI.](../role-based-access-control/role-assignments-cli.md)
* [Azure REST API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Tilldela åtkomst i Azure Portal

På den Azure-målresurs där du vill att den hanterade identiteten ska ha åtkomst ger du identiteten rollbaserad åtkomst till målresursen.

1. I den [Azure Portal](https://portal.azure.com)du till Den Azure-resurs där du vill att din hanterade identitet ska ha åtkomst.

1. På resursens meny väljer du **Åtkomstkontroll (IAM)** Rolltilldelningar där du kan granska de  >   aktuella rolltilldelningarna för resursen. I verktygsfältet väljer du Lägg **till Lägg till**  >  **rolltilldelning.**

   ![Välj "Lägg till" > "Lägg till rolltilldelning"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Om alternativet **Lägg till** rolltilldelning är inaktiverat har du förmodligen inte behörighet. Mer information om de behörigheter som gör att du kan hantera roller för resurser finns i [Administratörsrollbehörigheter i Azure Active Directory](../active-directory/roles/permissions-reference.md).

1. Under **Lägg till rolltilldelning** väljer du **en roll** som ger din identitet nödvändig åtkomst till målresursen.

   I det här avsnittets exempel behöver din identitet en roll som kan komma åt bloben i [en Azure Storage-container,](../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights)så välj rollen **Storage Blob Data-deltagare** för den hanterade identiteten.

   ![Välj rollen Storage Blob Data-deltagare](./media/create-managed-service-identity/select-role-for-identity.png)

1. Följ dessa steg för din hanterade identitet:

   * **System tilldelad identitet**

     1. I rutan **Tilldela åtkomst till** väljer du **Logikapp**. När egenskapen **Prenumeration** visas väljer du den Azure-prenumeration som är associerad med din identitet.

        ![Välj åtkomst för system tilldelad identitet](./media/create-managed-service-identity/assign-access-system.png)

     1. Under rutan **Välj** väljer du din logikapp i listan. Om listan är för lång använder du **rutan Välj** för att filtrera listan.

        ![Välj logikapp för system tilldelad identitet](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Användar tilldelad identitet**

     1. I rutan **Tilldela åtkomst till väljer** du Användar **tilldelad hanterad identitet**. När egenskapen **Prenumeration** visas väljer du den Azure-prenumeration som är associerad med din identitet.

        ![Välj åtkomst för användar tilldelad identitet](./media/create-managed-service-identity/assign-access-user.png)

     1. Under rutan **Välj** väljer du din identitet i listan. Om listan är för lång använder du **rutan Välj** för att filtrera listan.

        ![Välj din användar tilldelade identitet](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. När du är klar väljer du **Spara**.

   Målresursens lista över rolltilldelningar visar nu den valda hanterade identiteten och rollen. Det här exemplet visar hur du kan använda den system tilldelade identiteten för en logikapp och en användar tilldelad identitet för en grupp med andra logikappar.

   ![Hanterade identiteter och roller har lagts till för målresursen](./media/create-managed-service-identity/added-roles-for-identities.png)

   Mer information finns i [Tilldela en hanterad identitetsåtkomst till en resurs med hjälp av Azure Portal](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

1. Följ nu stegen [för att autentisera åtkomst med identiteten i en](#authenticate-access-with-identity) utlösare eller åtgärd som stöder hanterade identiteter.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Autentisera åtkomst med hanterad identitet

När du [aktiverar](#azure-portal-system-logic-app) den hanterade identiteten för logikappen och ger identiteten åtkomst till målresursen eller entiteten [kan](#access-other-resources)du använda den identiteten i utlösare och åtgärder som stöder [hanterade identiteter](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

> [!IMPORTANT]
> Om du har en Azure-funktion där du vill använda den system tilldelade identiteten aktiverar du [först autentisering för Azure Functions](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-functions).

De här stegen visar hur du använder den hanterade identiteten med en utlösare eller åtgärd via Azure Portal. Information om hur du anger den hanterade identiteten i en utlösare eller åtgärds underliggande JSON-definition finns i [Hanterad identitetsautentisering.](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)

1. I [Azure Portal](https://portal.azure.com)du logikappen i Logikappdesignern.

1. Om du inte har gjort det ännu lägger du till [utlösaren eller åtgärden som stöder hanterade identiteter](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

   > [!NOTE]
   > Det är inte alla utlösare och åtgärder som stöder att du lägger till en autentiseringstyp. Mer information finns i [Autentiseringstyper för utlösare och åtgärder som stöder autentisering.](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

1. Följ dessa steg för utlösaren eller åtgärden som du lade till:

   * **Inbyggda utlösare och åtgärder som stöder användning av en hanterad identitet**

     1. Lägg till **egenskapen** Autentisering om egenskapen inte redan visas.

     1. Under **Autentiseringstyp** väljer du **Hanterad identitet.**

     Mer information finns i [Exempel: Autentisera inbyggd utlösare eller åtgärd med en hanterad identitet.](#authenticate-built-in-managed-identity)
 
   * **Utlösare och åtgärder för hanterad anslutningsapp som stöder användning av en hanterad identitet**

     1. På sidan för val av klient väljer du **Anslut med hanterad identitet.**

     1. Ange ett anslutningsnamn på nästa sida.

        Som standard visar listan över hanterade identiteter endast den hanterade identiteten som är aktiverad för närvarande eftersom en logikapp stöder aktivering av endast en hanterad identitet i taget, till exempel:

        ![Skärmbild som visar sidan med anslutningsnamn och den valda hanterade identiteten.](./media/create-managed-service-identity/system-assigned-managed-identity.png)

     Mer information finns i [Exempel: Autentisera utlösare för hanterad anslutningsapp eller åtgärd med en hanterad identitet.](#authenticate-managed-connector-managed-identity)

<a name="authenticate-built-in-managed-identity"></a>

#### <a name="example-authenticate-built-in-trigger-or-action-with-a-managed-identity"></a>Exempel: Autentisera inbyggd utlösare eller åtgärd med en hanterad identitet

HTTP-utlösaren eller åtgärden kan använda den system tilldelade identitet som du har aktiverat för logikappen. I allmänhet använder HTTP-utlösaren eller åtgärden dessa egenskaper för att ange den resurs eller entitet som du vill komma åt:

| Egenskap | Krävs | Beskrivning |
|----------|----------|-------------|
| **Metod** | Yes | HTTP-metoden som används av den åtgärd som du vill köra |
| **URI** | Yes | Slutpunkts-URL för åtkomst till Azure-målresursen eller -entiteten. URI-syntaxen innehåller vanligtvis [resurs-ID:t](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) för Azure-resursen eller -tjänsten. |
| **Sidhuvuden** | No | Alla rubrikvärden som du behöver eller vill inkludera i den utgående begäran, till exempel innehållstypen |
| **Frågor** | No | Eventuella frågeparametrar som du behöver eller vill inkludera i begäran, till exempel parametern för en specifik åtgärd eller API-versionen för den åtgärd som du vill köra |
| **Autentisering** | Yes | Den autentiseringstyp som ska användas för att autentisera åtkomsten till målresursen eller entiteten |
||||

Anta till exempel att du vill köra åtgärden [Ögonblicksbildblob](/rest/api/storageservices/snapshot-blob) på en blob i det Azure Storage konto där du tidigare konfigurerade åtkomst för din identitet. Men den [Azure Blob Storage anslutningsappen](/connectors/azureblob/) erbjuder för närvarande inte den här åtgärden. I stället kan du köra den här åtgärden med hjälp av [HTTP-åtgärden](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) eller en annan [Blob Tjänst-REST-API åtgärd](/rest/api/storageservices/operations-on-blobs).

> [!IMPORTANT]
> Om du vill komma åt Azure Storage-konton bakom brandväggar med hjälp av HTTP-begäranden och hanterade identiteter måste du också konfigurera ditt lagringskonto med undantag som tillåter åtkomst av [betrodda Microsoft-tjänster](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

Om du vill [köra åtgärden Ögonblicksbildsblob](/rest/api/storageservices/snapshot-blob)anger HTTP-åtgärden följande egenskaper:

| Egenskap | Krävs | Exempelvärde | Description |
|----------|----------|---------------|-------------|
| **Metod** | Yes | `PUT`| HTTP-metoden som ögonblicksbildblobåtgärden använder |
| **URI** | Yes | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Resurs-ID för en Azure Blob Storage i Azure Global (offentlig) miljö, som använder den här syntaxen |
| **Sidhuvuden** | För Azure Storage | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` <p>`x-ms-date` = `@{formatDateTime(utcNow(),'r')}` | Värdena `x-ms-blob-type` , `x-ms-version` och `x-ms-date` sidhuvuden krävs för Azure Storage åtgärder. <p><p>**Viktigt!** I utgående HTTP-utlösare och åtgärdsbegäranden för Azure Storage kräver -huvudet egenskapen och API-versionen för den `x-ms-version` åtgärd som du vill köra. måste `x-ms-date` vara det aktuella datumet. Annars misslyckas logikappen med ett `403 FORBIDDEN` fel. Om du vill hämta aktuellt datum i det format som krävs kan du använda uttrycket i exempelvärdet. <p>Mer information finns i de här ämnena: <p><p>- [Begärandehuvuden – Ögonblicksbildsblob](/rest/api/storageservices/snapshot-blob#request) <br>- [Versionshantering för Azure Storage tjänster](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
| **Frågor** | Endast för åtgärden Snapshot Blob | `comp` = `snapshot` | Frågeparameterns namn och värde för åtgärden. |
|||||

Här är http-exempelåtgärden som visar alla dessa egenskapsvärden:

![Lägga till en HTTP-åtgärd för att få åtkomst till en Azure-resurs](./media/create-managed-service-identity/http-action-example.png)

1. När du har lagt till HTTP-åtgärden lägger du **till egenskapen Autentisering** i HTTP-åtgärden. I listan **Lägg till ny parameter** väljer du **Autentisering**.

   ![Lägg till egenskapen "Autentisering" i HTTP-åtgärden](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Det är inte alla utlösare och åtgärder som stöder att du lägger till en autentiseringstyp. Mer information finns i [Autentiseringstyper för utlösare och åtgärder som stöder autentisering.](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

1. I listan **Autentiseringstyp** väljer du **Hanterad identitet.**

   ![För "Autentisering" väljer du "Hanterad identitet"](./media/create-managed-service-identity/select-managed-identity.png)

1. I listan över hanterade identiteter väljer du bland de tillgängliga alternativen baserat på ditt scenario.

   * Om du ställer in den system tilldelade identiteten väljer du **System tilldelad hanterad identitet** om du inte redan har valt den.

     ![Välj "System assigned Managed Identity" (System tilldelad hanterad identitet)](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Om du ställer in en användar tilldelad identitet väljer du den identiteten om du inte redan har valt den.

     ![Välj den användar tilldelade identiteten](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Det här exemplet fortsätter med **den system tilldelade hanterade identiteten**.

1. På vissa utlösare och åtgärder visas **även egenskapen** Målgrupp där du kan ange målresurs-ID:t. Ange egenskapen **Målgrupp** till [resurs-ID:t för målresursen eller tjänsten](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). Annars använder egenskapen Målgrupp **som standard** `https://management.azure.com/` resurs-ID: t, som är resurs-ID:t för Azure Resource Manager.

   > [!IMPORTANT]
   > Kontrollera att målresurs-ID:t exakt matchar det värde Azure Active Directory (AD) förväntar sig, inklusive eventuella avslutande snedstreck som krävs.  Resurs-ID:t för alla Azure Blob Storage-konton kräver till exempel ett avslutande snedstreck. Resurs-ID:t för ett specifikt lagringskonto kräver dock inte något avslutande snedstreck. Kontrollera [resurs-ID:erna för de Azure-tjänster som stöder Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

   I det här exemplet anges **egenskapen** Målgrupp `https://storage.azure.com/` till så att de åtkomsttoken som används för autentisering är giltiga för alla lagringskonton. Du kan dock även ange rottjänstens URL, `https://fabrikamstorageaccount.blob.core.windows.net` , för ett specifikt lagringskonto.

   ![Ange målgruppsegenskapen till målresurs-ID](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Mer information om auktorisering av åtkomst med Azure AD för Azure Storage finns i följande avsnitt:

   * [Auktorisera åtkomst till Azure-blobar och -köer med hjälp av Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Ge åtkomst till Azure Storage med Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Fortsätt att skapa logikappen som du vill.

<a name="authenticate-managed-connector-managed-identity"></a>

#### <a name="example-authenticate-managed-connector-trigger-or-action-with-a-managed-identity"></a>Exempel: Autentisera utlösare för hanterad anslutningsapp eller åtgärd med en hanterad identitet

Åtgärden Azure Resource Manager, Läsa **en resurs**, kan använda den hanterade identitet som du har aktiverat för logikappen. Det här exemplet visar hur du använder den system tilldelade hanterade identiteten.

1. När du har lagt till åtgärden i arbetsflödet går du till sidan för klientval och väljer **Anslut med hanterad identitet.**

   ![Skärmbild som visar Azure Resource Manager och "Anslut med hanterad identitet" har valts.](./media/create-managed-service-identity/select-connect-managed-identity.png)

   Åtgärden visar nu sidan anslutningsnamn med listan över hanterade identiteter, som innehåller den hanterade identitetstyp som för närvarande är aktiverad i logikappen.

1. På sidan anslutningsnamn anger du ett namn för anslutningen. I listan över hanterade identiteter väljer du den hanterade identiteten, som är **System-tilldelad** hanterad identitet i det här exemplet, och väljer **Skapa**. Om du har aktiverat en användar tilldelad hanterad identitet väljer du den identiteten i stället.

   ![Skärmbild som visar Azure Resource Manager med anslutningsnamnet angivet och "System-tilldelad hanterad identitet" valt.](./media/create-managed-service-identity/system-assigned-managed-identity.png)

   Om den hanterade identiteten inte är aktiverad visas följande fel när du försöker skapa anslutningen:

   *Du måste aktivera hanterad identitet för logikappen och sedan bevilja nödvändig åtkomst till identiteten i målresursen.*

   ![Skärmbild som visar Azure Resource Manager åtgärd med fel när ingen hanterad identitet har aktiverats.](./media/create-managed-service-identity/system-assigned-managed-identity-disabled.png)

1. När anslutningen har skapats kan designern hämta dynamiska värden, innehåll eller scheman med hjälp av hanterad identitetsautentisering.

1. Fortsätt att skapa logikappen som du vill.

<a name="logic-app-resource-definition-connection-managed-identity"></a>

### <a name="logic-app-resource-definition-and-connections-that-use-a-managed-identity"></a>Resursdefinition och anslutningar för logikapp som använder en hanterad identitet

En anslutning som aktiverar och använder en hanterad identitet är en särskild anslutningstyp som endast fungerar med en hanterad identitet. Vid körning använder anslutningen den hanterade identitet som är aktiverad på logikappen. Den här konfigurationen sparas i logikappens resursdefinitionsobjekt, som innehåller objektet som innehåller pekare till anslutningens resurs-ID tillsammans med identitetens `parameters` resurs-ID, om den användar tilldelade identiteten är `$connections` aktiverad.

Det här exemplet visar hur konfigurationen ser ut när logikappen aktiverar den system tilldelade hanterade identiteten:

```json
"parameters": {
   "$connections": {
      "value": {
         "<action-name>": {
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/connections/{connection-name}",
            "connectionName": "{connection-name}",
            "connectionProperties": {
               "authentication": {
                  "type": "ManagedServiceIdentity"
               }
            },
            "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
         }
      }
   }
}
```

Det här exemplet visar hur konfigurationen ser ut när logikappen aktiverar en användar tilldelad hanterad identitet:

```json
"parameters": {
   "$connections": {
      "value": {
         "<action-name>": {
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/connections/{connection-name}",
            "connectionName": "{connection-name}",
            "connectionProperties": {
               "authentication": {
                  "identity": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/microsoft.managedidentity/userassignedidentities/{managed-identity-name}",
                  "type": "ManagedServiceIdentity"
               }
            },
            "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
         }
      }
   }
}
```

Under körning kontrollerar Logic Apps-tjänsten om någon utlösare för hanterad anslutningsapp och åtgärder i logikappen har ställts in för att använda den hanterade identiteten och att alla nödvändiga behörigheter har ställts in för att använda den hanterade identiteten för åtkomst till de målresurser som anges av utlösaren och åtgärderna. Om åtgärden lyckas hämtar Logic Apps-tjänsten den Azure AD-token som är associerad med den hanterade identiteten och använder den identiteten för att autentisera åtkomsten till målresursen och utföra den konfigurerade åtgärden i utlösare och åtgärder.

<a name="arm-templates-connection-resource-managed-identity"></a>

## <a name="arm-template-for-managed-connections-and-managed-identities"></a>ARM-mall för hanterade anslutningar och hanterade identiteter

Om du automatiserar distributionen med en ARM-mall och logikappen innehåller en utlösare eller åtgärd för en hanterad anslutningsapp som använder en hanterad identitet, bekräftar du att den underliggande anslutningsresursdefinitionen innehåller egenskapen med som `parameterValueType` `Alternative` egenskapsvärde. Annars kommer ARM-distributionen inte konfigurera anslutningen för att använda den hanterade identiteten för autentisering och anslutningen fungerar inte i logikappens arbetsflöde. Det här kravet gäller endast för [specifika utlösare för hanterade anslutningsappar och åtgärder](#managed-connectors-managed-identity) där du valde alternativet Anslut med [ **hanterad** identitet.](#authenticate-managed-connector-managed-identity)

Här är till exempel den underliggande definitionen av anslutningsresursen för en Azure Automation-åtgärd som använder en hanterad identitet där definitionen innehåller egenskapen , som anges till `parameterValueType` `Alternative` som egenskapsvärdet:

```json
{
    "type": "Microsoft.Web/connections",
    "name": "[variables('automationAccountApiConnectionName')]",
    "apiVersion": "2016-06-01",
    "location": "[parameters('location')]",
    "kind": "V1",
    "properties": {
        "api": {
            "id": "[subscriptionResourceId('Microsoft.Web/locations/managedApis', parameters('location'), 'azureautomation')]"
        },
        "customParameterValues": {},
        "displayName": "[variables('automationAccountApiConnectionName')]",
        "parameterValueType": "Alternative"
    }
},
```

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Inaktivera hanterad identitet

Om du vill sluta använda en hanterad identitet för logikappen har du följande alternativ:

* [Azure-portalen](#azure-portal-disable)
* [Azure Resource Manager-mallar](#template-disable)
* Azure PowerShell
  * [Ta bort rolltilldelning](../role-based-access-control/role-assignments-powershell.md)
  * [Ta bort användar tilldelad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [Ta bort rolltilldelning](../role-based-access-control/role-assignments-cli.md)
  * [Ta bort användar tilldelad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Azure REST API
  * [Ta bort rolltilldelning](../role-based-access-control/role-assignments-rest.md)
  * [Ta bort användar tilldelad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Om du tar bort logikappen tar Azure automatiskt bort den hanterade identiteten från Azure AD.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Inaktivera hanterad identitet i Azure Portal

I Azure Portal du först bort identitetens åtkomst till [målresursen](#disable-identity-target-resource). Sedan stänger du av den system tilldelade identiteten eller tar bort den användar tilldelade identiteten från [logikappen](#disable-identity-logic-app).

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Ta bort identitetsåtkomst från resurser

1. I den [Azure Portal](https://portal.azure.com)du till Azure-målresursen där du vill ta bort åtkomsten för den hanterade identiteten.

1. På målresursens meny väljer du **Åtkomstkontroll (IAM).** Under verktygsfältet väljer du **Rolltilldelningar.**

1. I listan över roller väljer du de hanterade identiteter som du vill ta bort. Välj Ta bort i **verktygsfältet.**

   > [!TIP]
   > Om alternativet **Ta** bort är inaktiverat har du förmodligen inte behörighet. Mer information om de behörigheter som gör att du kan hantera roller för resurser finns i [Behörigheter för administratörsroll i Azure Active Directory](../active-directory/roles/permissions-reference.md).

Den hanterade identiteten tas nu bort och har inte längre åtkomst till målresursen.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Inaktivera hanterad identitet i logikapp

1. I [Azure Portal](https://portal.azure.com)du logikappen i Logic App Designer.

1. På logikappmenyn under **Inställningar** väljer du **Identitet** och följer sedan stegen för din identitet:

   * Välj **System tilldelad**  >  **På**  >  **Spara.** När Azure uppmanar dig att bekräfta väljer du **Ja.**

     ![Inaktivera den system tilldelade identiteten](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Välj **Användar tilldelad** och den hanterade identiteten och välj sedan **Ta bort.** När Azure uppmanar dig att bekräfta väljer du **Ja.**

     ![Ta bort den användar tilldelade identiteten](./media/create-managed-service-identity/remove-user-assigned-identity.png)

Den hanterade identiteten är nu inaktiverad i logikappen.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Inaktivera hanterad identitet i Azure Resource Manager mall

Om du har skapat logikappens hanterade identitet med hjälp Azure Resource Manager en mall anger du `identity` objektets `type` underordnade egenskap till `None` .

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Nästa steg

* [Säker åtkomst och data i Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
