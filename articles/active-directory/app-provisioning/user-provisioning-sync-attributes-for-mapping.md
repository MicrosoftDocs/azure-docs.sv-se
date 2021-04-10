---
title: Synkronisera attribut till Azure AD för mappning
description: När du konfigurerar användar etablering för SaaS-appar använder du funktionen katalog tillägg för att lägga till källattribut som inte synkroniseras som standard.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/17/2021
ms.author: kenwith
ms.openlocfilehash: 52f34cdafac76a9bca2b4ff0b00e0b3efaa63f5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579441"
---
# <a name="syncing-extension-attributes-attributes"></a>Attribut för attribut för synkronisering av tillägg

När du anpassar mappningar för attribut för användar etablering kan du se att det attribut som du vill mappa inte visas i listan **källattribut** . Den här artikeln visar hur du lägger till attributet som saknas genom att synkronisera det från din lokala Active Directory (AD) till Azure Active Directory (Azure AD) eller genom att skapa tilläggets attribut i Azure AD för en endast moln användare. 

Azure AD måste innehålla alla data som krävs för att skapa en användar profil vid etablering av användar konton från Azure AD till en SaaS-app. I vissa fall kan du behöva synkronisera attribut från din lokala AD till Azure AD för att kunna göra data tillgängliga. Azure AD Connect synkroniserar automatiskt vissa attribut till Azure AD, men inte alla attribut. Dessutom kanske vissa attribut (till exempel SAMAccountName) som synkroniseras som standard inte exponeras med Azure AD-Graph API. I dessa fall kan du använda funktionen Azure AD Connect katalog tillägg för att synkronisera attributet med Azure AD. På så sätt blir attributet synligt för Azure AD-Graph API och Azure AD Provisioning-tjänsten. Om de data du behöver för etablering är i Active Directory men inte är tillgängliga för etablering på grund av de orsaker som beskrivs ovan, kan du använda Azure AD Connect för att skapa attribut för tillägg. 

De flesta användare är troligen Hybrid användare som synkroniseras från Active Directory, men du kan också skapa tillägg på användare som endast är molnbaserade utan att använda Azure AD Connect. Med PowerShell eller Microsoft Graph kan du utöka schemat för en användare som endast har en moln användare. 

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

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Skapa ett attribut för tillägg i en användare som endast har en moln tjänst
Kunder kan använda Microsoft Graph och PowerShell för att utöka användar schemat. Dessa tilläggsfiler identifieras automatiskt i de flesta fall, men kunder som har fler än 1000 tjänstens huvud namn kan hitta tillägg som saknas i källistan. Om ett attribut som du skapar med stegen nedan inte visas automatiskt i listan över källfiler, verifierar du med Graph att attributet Extension har skapats och lägger sedan till det i schemat [manuellt](https://docs.microsoft.com/azure/active-directory/app-provisioning/customize-application-attributes#editing-the-list-of-supported-attributes). När du gör diagram förfrågningarna nedan klickar du på Läs mer för att kontrol lera de behörigheter som krävs för att göra förfrågningarna. Du kan använda [Graph Explorer](https://docs.microsoft.com/graph/graph-explorer/graph-explorer-overview) för att göra förfrågningarna. 

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Skapa ett tilläggs-attribut i en endast moln användare som använder Microsoft Graph
Du måste använda ett program för att utöka schemat för dina användare. Lista apparna i din klient organisation för att identifiera ID: t för det program som du vill använda för att utöka användar schemat. [Läs mer.](https://docs.microsoft.com/graph/api/application-list?view=graph-rest-1.0&tabs=http)

```json
GET https://graph.microsoft.com/v1.0/applications
```

Skapa attributet Extension. Ersätt **ID-** egenskapen nedan med det **ID** som hämtades i föregående steg. Du måste använda attributet **"ID"** och inte "appId". [Läs mer.](https://docs.microsoft.com/graph/api/application-post-extensionproperty?view=graph-rest-1.0&tabs=http)
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

Den tidigare begäran skapade ett tilläggs-attribut med formatet extension_appID_extensionName. Uppdatera en användare med attributet Extension. [Läs mer.](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0&tabs=http)
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Kontrol lera att attributet har uppdaterats genom att kontrol lera användaren. [Läs mer.](https://docs.microsoft.com/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select)

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

#Set a value for the extension property on the user. Replace the objectid with the id of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="next-steps"></a>Nästa steg

* [Definiera vem som är inom omfånget för etablering](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
