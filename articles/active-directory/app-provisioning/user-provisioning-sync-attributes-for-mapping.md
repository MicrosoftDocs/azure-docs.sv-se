---
title: Synkronisera attribut till Azure Active Directory för mappning
description: När du konfigurerar användar etablering med Azure Active Directory-och SaaS-appar använder du funktionen katalog tillägg för att lägga till källattribut som inte synkroniseras som standard.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/31/2021
ms.author: kenwith
ms.openlocfilehash: 102c0f7363b8d4f635762a33b82825e9ae71dfc6
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120800"
---
# <a name="syncing-extension-attributes-for-app-provisioning"></a>Synkroniserar tilläggs-attribut för app-etablering

Azure Active Directory (Azure AD) måste innehålla alla data (attribut) som krävs för att skapa en användar profil vid etablering av användar konton från Azure AD till en [SaaS-app](../saas-apps/tutorial-list.md). När du anpassar mappningar för mappar för användar etablering, kanske du upptäcker att attributet som du vill mappa inte visas i listan **källattribut** . Den här artikeln visar hur du lägger till attributet som saknas.

Endast för användare i Azure AD kan du [skapa schema tillägg med PowerShell eller Microsoft Graph](#create-an-extension-attribute-on-a-cloud-only-user).

För användare i lokala Active Directory måste du synkronisera användarna till Azure AD. Du kan synkronisera användare och attribut med [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md). Azure AD Connect synkroniserar automatiskt vissa attribut till Azure AD, men inte alla attribut. Dessutom kanske vissa attribut (till exempel SAMAccountName) som synkroniseras som standard inte exponeras med Azure AD-Graph API. I dessa fall kan du [använda funktionen Azure AD Connect katalog tillägg för att synkronisera attributet med Azure AD](#create-an-extension-attribute-using-azure-ad-connect). På så sätt blir attributet synligt för Azure AD-Graph API och Azure AD Provisioning-tjänsten.

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Skapa ett attribut för tillägg i en användare som endast har en moln tjänst
Du kan använda Microsoft Graph och PowerShell för att utöka användar schemat för användare i Azure AD. De här tilläggs attributen identifieras automatiskt i de flesta fall.

Om du har fler än 1000 tjänstens huvud namn kan du hitta tillägg som saknas i listan över källattribut. Om ett attribut som du har skapat inte visas automatiskt, kontrollerar du att attributet har skapats och lägger till det manuellt i schemat. Använd Microsoft Graph och [Graph Explorer](/graph/graph-explorer/graph-explorer-overview.md)för att kontrol lera att det har skapats. Information om hur du lägger till den manuellt i schemat finns i [Redigera listan över attribut som stöds](customize-application-attributes.md#editing-the-list-of-supported-attributes).

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Skapa ett tilläggs-attribut i en endast moln användare som använder Microsoft Graph
Du kan utöka schemat för Azure AD-användare med hjälp av [Microsoft Graph](/graph/overview.md). 

Börja med att lista apparna i din klient organisation för att hämta ID för den app som du arbetar med. Mer information finns i [lista extensionProperties](/graph/api/application-list-extensionproperty?view=graph-rest-1.0&tabs=http&preserve-view=true).

```json
GET https://graph.microsoft.com/v1.0/applications
```

Skapa sedan attributet Extension. Ersätt **ID-** egenskapen nedan med det **ID** som hämtades i föregående steg. Du måste använda attributet **"ID"** och inte "appId". Mer information finns i [skapa extensionProperty]/Graph/API/application-post-extensionproperty.MD? View = Graph-rest-1,0&flikar = http&bevara-View = true).

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

Den tidigare begäran skapade ett tillägg-attribut med formatet `extension_appID_extensionName` . Nu kan du uppdatera en användare med detta tillägg-attribut. Mer information finns i [uppdatera användare](/graph/api/user-update.md?view=graph-rest-1.0&tabs=http&preserve-view=true).
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Bekräfta slutligen attributet för användaren. Mer information finns i [Hämta en användare](/graph/api/user-get.md?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select&preserve-view=true).

```json
GET https://graph.microsoft.com/v1.0/users/{id}?$select=displayName,extension_inputAppId_extensionName
```


### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-powershell"></a>Skapa ett attribut för tillägg i en enda moln användare med hjälp av PowerShell
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

## <a name="create-an-extension-attribute-using-azure-ad-connect"></a>Skapa ett tilläggs attribut med Azure AD Connect

1. Öppna guiden Azure AD Connect, Välj uppgifter och välj sedan **Anpassa synkroniseringsalternativ**.

   ![Sidan ytterligare aktiviteter för Azure Active Directory Connect guiden](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Logga in som global administratör för Azure AD. 

3. På sidan **valfria funktioner** väljer du **katalog tillägg attribut Sync**.
 
   ![Sidan med valfria funktioner i Azure Active Directory Connect guiden](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Välj det eller de attribut som du vill utöka till Azure AD.
   > [!NOTE]
   > Sökningen under **tillgängliga attribut** är Skift läges känslig.

   ![Skärm bild som visar val sidan "katalog tillägg"](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Slutför guiden Azure AD Connect och låt en fullständig synkronisering köras. När cykeln har slutförts utökas schemat och de nya värdena synkroniseras mellan din lokala AD och Azure AD.
 
6. När du [redigerar mappningar för användarattribut](customize-application-attributes.md)i Azure Portal, kommer listan över **källattribut** nu att innehålla attributet Added i formatet `<attributename> (extension_<appID>_<attributename>)` . Välj attributet och mappa det till mål programmet för etablering.

   ![Sidan val av katalog tillägg för Azure Active Directory Connect guide](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> Möjligheten att etablera referenser från lokala AD, till exempel **ManagedBy** eller **DN/DistinguishedName**, stöds inte idag. Du kan begära den här funktionen på [användarens röst](https://feedback.azure.com/forums/169401-azure-active-directory). 


## <a name="next-steps"></a>Nästa steg

* [Definiera vem som är inom omfånget för etablering](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
