---
title: Synkronisera attribut till Azure AD för mappning
description: Lär dig hur du synkroniserar attribut från din lokala Active Directory till Azure AD. När du konfigurerar användar etablering för SaaS-appar använder du funktionen katalog tillägg för att lägga till källattribut som inte synkroniseras som standard.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/13/2019
ms.author: kenwith
ms.openlocfilehash: 62d035b85850f8ac455a85fd93e4d081bbd386e1
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256093"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Synkronisera ett attribut från din lokala Active Directory till Azure AD för etablering till ett program

När du anpassar mappningar för attribut för användar etablering kan du se att det attribut som du vill mappa inte visas i listan **källattribut** . Den här artikeln visar hur du lägger till attributet som saknas genom att synkronisera det från din lokala Active Directory (AD) till Azure Active Directory (Azure AD).

Azure AD måste innehålla alla data som krävs för att skapa en användar profil vid etablering av användar konton från Azure AD till en SaaS-app. I vissa fall kan du behöva synkronisera attribut från din lokala AD till Azure AD för att kunna göra data tillgängliga. Azure AD Connect synkroniserar automatiskt vissa attribut till Azure AD, men inte alla attribut. Dessutom kanske vissa attribut (till exempel SAMAccountName) som synkroniseras som standard inte exponeras med hjälp av Microsoft Graph API. I dessa fall kan du använda funktionen Azure AD Connect katalog tillägg för att synkronisera attributet med Azure AD. På så sätt blir attributet synligt för Microsoft Graph API och Azure AD Provisioning-tjänsten.

Om de data du behöver för etablering är i Active Directory men inte är tillgängliga för etablering på grund av de orsaker som beskrivs ovan, följer du de här stegen.
 
## <a name="sync-an-attribute"></a>Synkronisera ett attribut 

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
