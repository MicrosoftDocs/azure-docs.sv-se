---
title: Synkronisera attribut till Azure Active Directory för mappning
description: När du konfigurerar användareetablering med Azure Active Directory- och SaaS-appar använder du katalogtilläggsfunktionen för att lägga till källattribut som inte synkroniseras som standard.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/31/2021
ms.author: kenwith
ms.openlocfilehash: f7a2429161cebe867d844b4ca7aa08ec3613edcd
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388218"
---
# <a name="syncing-extension-attributes-for-app-provisioning"></a>Synkronisera tilläggsattribut för appetablering

Azure Active Directory (Azure AD) måste innehålla alla data (attribut) som krävs för att skapa en användarprofil vid etablering av användarkonton från Azure AD till en [SaaS-app](../saas-apps/tutorial-list.md). När du anpassar attributmappningar för användareetablering kanske du upptäcker att attributet som du vill mappa inte visas i **listan över källattribut.** Den här artikeln visar hur du lägger till attributet som saknas.

För användare endast i Azure AD kan du skapa [schematillägg med hjälp av PowerShell eller Microsoft Graph](#create-an-extension-attribute-on-a-cloud-only-user).

För användare lokal Active Directory måste du synkronisera användarna med Azure AD. Du kan synkronisera användare och attribut med hjälp [av Azure AD Connect](../hybrid/whatis-azure-ad-connect.md). Azure AD Connect synkroniserar automatiskt vissa attribut till Azure AD, men inte alla attribut. Dessutom kanske vissa attribut (till exempel SAMAccountName) som synkroniseras som standard inte exponeras med hjälp av Azure AD-Graph API. I dessa fall kan du använda [funktionen Azure AD Connect katalogtillägg för att synkronisera attributet till Azure AD.](#create-an-extension-attribute-using-azure-ad-connect) På så sätt blir attributet synligt för Azure AD-Graph API och Azure AD-etableringstjänsten.

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Skapa ett tilläggsattribut på en molnanvändare
Du kan använda Microsoft Graph och PowerShell för att utöka användarschemat för användare i Azure AD. Dessa tilläggsattribut identifieras automatiskt i de flesta fall.

Om du har fler än 1 000 tjänsthuvudnamn kanske tillägg saknas i källattributlistan. Om ett attribut som du har skapat inte visas automatiskt kontrollerar du att attributet har skapats och lägger till det manuellt i schemat. Kontrollera att den har skapats med hjälp Microsoft Graph och [Graph Explorer](/graph/graph-explorer/graph-explorer-overview). Information om hur du lägger till det manuellt i schemat [finns i Redigera listan över attribut som stöds.](customize-application-attributes.md#editing-the-list-of-supported-attributes)

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Skapa ett tilläggsattribut för en molnanvändare med hjälp av Microsoft Graph
Du kan utöka schemat för Azure AD-användare med [hjälp av Microsoft Graph](/graph/overview). 

Börja med att lista apparna i din klientorganisation för att hämta ID:t för den app som du arbetar med. Mer information finns i [List extensionProperties](/graph/api/application-list-extensionproperty?view=graph-rest-1.0&tabs=http&preserve-view=true).

```json
GET https://graph.microsoft.com/v1.0/applications
```

Skapa sedan tilläggsattributet. Ersätt **ID-egenskapen** nedan med **det ID** som hämtades i föregående steg. Du måste använda attributet **"ID"** och inte "appId". Mer information finns i [Skapa extensionProperty]/graph/api/application-post-extensionproperty.md?view=graph-rest-1.0&tabs=http&preserve-view=true).

```json
POST https://graph.microsoft.com/v1.0/applications/{id}/extensionProperties
Content-type: application/json

{
    "name": "extensionName",
    "dataType": "string",
    "targetObjects": [
        "User"
    ]
}
```

I föregående begäran skapades ett tilläggsattribut med formatet `extension_appID_extensionName` . Nu kan du uppdatera en användare med det här tilläggsattributet. Mer information finns i [Uppdatera användare.](/graph/api/user-update?view=graph-rest-1.0&tabs=http&preserve-view=true)
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Kontrollera slutligen attributet för användaren. Mer information finns i [Hämta en användare.](/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select&preserve-view=true)

```json
GET https://graph.microsoft.com/v1.0/users/{id}?$select=displayName,extension_inputAppId_extensionName
```


### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-powershell"></a>Skapa ett tilläggsattribut på en användare som endast använder PowerShell i molnet
Skapa ett anpassat tillägg med PowerShell och tilldela ett värde till en användare. 

```
#Connect to your Azure AD tenant   
Connect-AzureAD

#Create an application (you can instead use an existing application if you would like)
$App = New-AzureADApplication -DisplayName “test app name” -IdentifierUris https://testapp

#Create a service principal
New-AzureADServicePrincipal -AppId $App.AppId

#Create an extension property
New-AzureADApplicationExtensionProperty -ObjectId $App.ObjectId -Name “TestAttributeName” -DataType “String” -TargetObjects “User”

#List users in your tenant to determine the objectid for your user
Get-AzureADUser

#Set a value for the extension property on the user. Replace the objectid with the ID of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="create-an-extension-attribute-using-azure-ad-connect"></a>Skapa ett tilläggsattribut med Azure AD Connect

1. Öppna guiden Azure AD Connect, välj Uppgifter och välj sedan **Anpassa synkroniseringsalternativ.**

   ![Azure Active Directory Connect sidan Ytterligare uppgifter](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Logga in som global Administratör för Azure AD. 

3. På sidan **Valfria funktioner** väljer du **Synkronisering av katalogtilläggsattribut.**
 
   ![Azure Active Directory Connect sidan Valfria funktioner](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Välj de attribut som du vill utöka till Azure AD.
   > [!NOTE]
   > Sökningen under Tillgängliga **attribut är fallkänslig.**

   ![Skärmbild som visar sidan för val av katalogtillägg](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Slutför guiden Azure AD Connect och låt en fullständig synkroniseringscykel köras. När cykeln är klar utökas schemat och de nya värdena synkroniseras mellan din lokala AD och Azure AD.
 
6. När du Azure Portal mappningar av [](customize-application-attributes.md)användarattribut i -listan innehåller källattributlistan nu det tillagda attributet i formatet  `<attributename> (extension_<appID>_<attributename>)` . Välj attributet och mappa det till målprogrammet för etablering.

   ![Azure Active Directory Connect sida för val av katalogtillägg](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> Möjligheten att etablera referensattribut från lokal AD, till exempel **managedby** eller **DN/DistinguishedName,** stöds inte i dag. Du kan begära den här funktionen på [User Voice.](https://feedback.azure.com/forums/169401-azure-active-directory) 


## <a name="next-steps"></a>Nästa steg

* [Definiera vem som ingår i etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
