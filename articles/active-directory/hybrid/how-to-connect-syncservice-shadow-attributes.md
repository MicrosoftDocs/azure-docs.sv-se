---
title: Azure AD Connect Shadow-attribut för synkroniseringstjänst | Microsoft Docs
description: Beskriver hur skugg-attribut fungerar i Azure AD Connect Sync service.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 128303cb51b39db8442fdda71f949db17923bfa2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90088978"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Azure AD Connect Shadow-attribut för synkroniseringstjänst
De flesta attributen visas på samma sätt i Azure AD som i din lokala Active Directory. Men vissa attribut har viss specifik hantering och attributvärdet i Azure AD kan skilja sig från vad som Azure AD Connect synkroniseras.

## <a name="introducing-shadow-attributes"></a>Introduktion till skugg egenskaper
Vissa attribut har två representationer i Azure AD. Både det lokala värdet och ett beräknat värde lagras. Dessa extra attribut kallas skugg egenskaper. De två vanligaste attributen där du ser det här beteendet är **userPrincipalName** och **proxyAddress**. Värdena för ändring av attribut inträffar när det finns värden i dessa attribut som representerar icke-verifierade domäner. Men Synkroniseringsmotorn i Connect läser in värdet i attributet Shadow, så från dess perspektiv, attributet har bekräftats av Azure AD.

Du kan inte se Shadow-attributen med hjälp av Azure Portal eller med PowerShell. Men om du förstår konceptet kan du felsöka vissa scenarier där attributet har olika värden lokalt och i molnet.

För att bättre förstå beteendet kan du titta på det här exemplet från Fabrikam:  
![Skärm bild som visar Active Directory UPN-suffix för flera exempel med ett motsvarande Azure AD-domän värde som inte har lagts till, inte verifierats och verifierats.](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
De har flera UPN-suffix i sina lokala Active Directory, men de har bara verifierat ett.

### <a name="userprincipalname"></a>userPrincipalName
En användare har följande attributvärden i en icke-verifierad domän:

| Attribut | Värde |
| --- | --- |
| lokalt userPrincipalName | lee.sperry@fabrikam.com |
| Azure AD-shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Azure AD userPrincipalName | lee.sperry@fabrikam.onmicrosoft.com |

Attributet userPrincipalName är det värde som visas när du använder PowerShell.

Eftersom det verkliga värdet för det lokala attributvärdet lagras i Azure AD, uppdaterar Azure AD attributet userPrincipalName med värdet från shadowUserPrincipalName. Du behöver inte synkronisera några ändringar från Azure AD Connect för att dessa värden ska uppdateras.

### <a name="proxyaddresses"></a>proxyAddresses
Samma process för att bara inkludera verifierade domäner sker också för proxyAddresses, men med en viss ytterligare logik. Kontrollen av verifierade domäner sker bara för Mailbox-användare. En e-postaktiverad användare eller kontakt representerar en användare i en annan Exchange-organisation och du kan lägga till värden i proxyAddresses till dessa objekt.

För en Mailbox-användare, antingen lokalt eller i Exchange Online visas bara värden för verifierade domäner. Det kan se ut så här:

| Attribut | Värde |
| --- | --- |
| lokala proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Exchange Online-proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

I det här fallet har **SMTP: Abbie. Spencer- \@ fabrikam.com** tagits bort eftersom domänen inte har verifierats. Men Exchange har även lagt till **SIP: Abbie. spencer \@ fabrikamonline.com**. Fabrikam har inte använt Lync/Skype lokalt, men Azure AD och Exchange Online Förbered dig för det.

Den här logiken för proxyAddresses kallas **ProxyCalc**. ProxyCalc anropas med varje ändring av en användare när:

- Användaren har tilldelats en service plan som innehåller Exchange Online även om användaren inte har licens för Exchange. Till exempel om användaren har tilldelats Office E3 SKU, men bara tilldelats SharePoint Online. Detta gäller även om din post låda fortfarande finns lokalt.
- Attributet msExchRecipientTypeDetails har ett värde.
- Du gör en ändring i proxyAddresses eller userPrincipalName.

ProxyCalc kan ta lite tid att bearbeta en ändring av en användare och är inte synkron med Azure AD Connect export processen.

> [!NOTE]
> ProxyCalc Logic har vissa ytterligare beteenden för avancerade scenarier som inte dokumenterats i det här avsnittet. I det här avsnittet finns information om hur du kan förstå beteendet och inte dokumentera all intern logik.

### <a name="quarantined-attribute-values"></a>Attributvärden i karantän
Skugg-attribut används också när det finns duplicerade attributvärden. Mer information finns i [duplicerat attribut återhämtning](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Se även
* [Azure AD Connect synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
