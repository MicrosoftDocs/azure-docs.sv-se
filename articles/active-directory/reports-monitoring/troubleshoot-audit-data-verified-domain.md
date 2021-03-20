---
title: Felsöka gransknings data för verifierad domän ändring | Microsoft Docs
description: Innehåller information som visas i Azure Active Directory aktivitets loggar när du ändrar en domän som verifieras av användare.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/22/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3c9ec3b1e96e47dbf46c6acb2c81147b614d069
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87117439"
---
# <a name="troubleshoot-audit-data-on-verified-domain-change"></a>Felsöka: gransknings data på verifierad domän ändring 


## <a name="i-have-a-lot-of-changes-to-my-users-and-i-am-not-sure-what-the-cause-of-it-is"></a>Jag har många ändringar av mina användare och jag är inte säker på vad orsaken är.

### <a name="symptoms"></a>Symtom

Jag kontrollerar Azure AD audit-loggarna och ser flera användar uppdateringar som inträffar i min Azure AD-klient. Dessa **uppdaterings användar** händelser visar inte **aktörs** information, vilket orsakar osäkerhet vad gäller vad/vem som utlöste Mass ändringarna för användarna. 

### <a name="cause"></a>Orsak

 En vanlig orsak bakom ändringar i Mass objekt är en icke-synkron Server dels åtgärd som kallas **ProxyCalc**.  **ProxyCalc** är den logik som avgör lämpliga **userPrincipalName** -och **PROXYADRESSER** som uppdateras i Azure AD-användare, grupper eller kontakter. Designen bakom **ProxyCalc** är att se till att alla **userPrincipalName** -och **proxyadresser-adresser** är konsekventa i Azure AD när som helst. **ProxyCalc** måste utlösas av en explicit ändring som en verifierad domän ändring och körs inte permanent i bakgrunden som en uppgift. 

  

#### <a name="what-does-userprincipalname-consistency-mean"></a>Vad innebär UserPrincipalName konsekvens? 

För endast molnbaserade användare innebär konsekvens att **userPrincipalName** är inställt på ett verifierat domänsuffix. När ett inkonsekvent **userPrincipalName** bearbetas, kommer **ProxyCalc** att konvertera det till standard-onmicrosoft.com-suffixet, till exempel: username@Contoso.onmicrosoft.com 

För synkroniserade användare innebär konsekvens att **userPrincipalName** är inställt på ett verifierat domänsuffix och matchar det lokala **userPrincipalName** -värdet (ShadowUserPrincipalName). När ett inkonsekvent **userPrincipalName** bearbetas, kommer **ProxyCalc** att återgå till samma värde som **ShadowUserPrincipalName** eller, om domänsuffix har tagits bort från klienten, konverteras det till standard-*. onmicrosoft.com-domänsuffix. 

  

#### <a name="what-does-proxy-address-consistency-mean"></a>Vad betyder konsekvens för proxy? 

För endast molnbaserade användare innebär konsekvens att proxyservrarna matchar ett verifierat domänsuffix. När en inkonsekvent proxyadress bearbetas, kommer **ProxyCalc** att konvertera den till standardvärdet för *. onmicrosoft.com-domänsuffix, till exempel: SMTP:username@Contoso.onmicrosoft.com 

För synkroniserade användare innebär konsekvens att proxyservrarna matchar den lokala proxyns adress (er)-värden (dvs. ShadowProxyAddresses). **ProxyAddresses** förväntas vara synkroniserade med **ShadowProxyAddresses**. Om den synkroniserade användaren har en tilldelad Exchange-licens måste proxyadresser matcha de lokala proxy-adresserna (ES) och måste också matcha ett verifierat domänsuffix. I det här scenariot kommer **ProxyCalc** att sanera den inkonsekventa proxy-adressen med ett overifierat domänsuffix och tas bort från objektet i Azure AD. Om den overifierade domänen verifieras senare, kommer **ProxyCalc** att beräkna om och lägga till proxyadress från **ShadowProxyAddresses** tillbaka till objektet i Azure AD.  

> [!NOTE]
> För synkroniserade objekt, för att undvika **ProxyCalc** Logic att beräkna oväntade resultat, är det bäst att ange proxyadresser till en verifierad Azure AD-domän på det lokala objektet.  

  
En av de administratörs uppgifter som kan utlösa **ProxyCalc** är när en verifierad domän ändras. Den här uppgiften inträffar varje gång en verifierad domän läggs till/tas bort från en Azure AD-klient, som internt utlöser **ProxyCalc**.  

Om du till exempel lägger till en verifierad domän Fabrikam.com till din Contoso.onmicrosoft.com-klient, utlöser den här åtgärden en ProxyCalc-åtgärd för alla objekt i klient organisationen. Den här händelsen registreras i gransknings loggarna i Azure AD som **uppdaterings användar** händelser som föregås av en **Lägg till verifierad domän** händelse. Å andra sidan, om Fabrikam.com har tagits bort från Contoso.onmicrosoft.com-klienten, kommer alla **uppdaterings användar** händelser att föregås av en **ta bort verifierad domän** händelse.   

#### <a name="additional-notes"></a>Ytterligare kommentarer:

ProxyCalc orsakar inte ändringar i vissa objekt som: 

- har ingen aktiv Exchange-licens 
- ha **MSExchRemoteRecipientType** inställt på null 
- anses inte vara en delad resurs. Delad resurs är när **CloudMSExchRecipientDisplayType** innehåller något av följande värden: **MailboxUser (delad)**, **PublicFolder**, **ConferenceRoomMailbox**, **EquipmentMailbox**, **ArbitrationMailbox**, **RoomList**, **TeamMailboxUser**, **grupp post låda**, **schema post låda**, **ACLableMailboxUser**, **ACLableTeamMailboxUser** 
  
 För att kunna bygga mer korrelation mellan dessa två olika händelser arbetar Microsoft med att uppdatera **aktörs** informationen i gransknings loggarna för att identifiera ändringarna som utlöses av en verifierad domän ändring. Den här åtgärden hjälper dig att kontrol lera när den verifierade domän ändrings händelsen ägde rum och började uppdatera objekt i klienten. 

I de flesta fall finns det dessutom inga ändringar av användarna eftersom deras **userPrincipalName** och **proxyadresser** är konsekventa, så vi arbetar med att visa i gransknings loggar endast de uppdateringar som orsakade en riktig ändring av objektet. Den här åtgärden förhindrar brus i gransknings loggarna och hjälper administratörer att korrelera de återstående användar ändringarna till den verifierade ändringen av domän ändringar enligt beskrivningen ovan. 

## <a name="next-steps"></a>Nästa steg

[Azure AD Connect Shadow-attribut för synkroniseringstjänst](../hybrid/how-to-connect-syncservice-shadow-attributes.md)
