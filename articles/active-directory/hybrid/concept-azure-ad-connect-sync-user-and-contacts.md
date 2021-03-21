---
title: 'Azure AD Connect synkronisering: förstå användare, grupper och kontakter | Microsoft Docs'
description: Förklarar användare, grupper och kontakter i Azure AD Connect Sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e015f7937db6788aa4473a8a04434121299901e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861790"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Azure AD Connect synkronisering: förstå användare, grupper och kontakter
Det finns flera olika anledningar till varför du skulle ha flera Active Directory skogar och det finns flera olika distributions topologier. Vanliga modeller är en konto-resurs distribution och GAL sync'ed-skogar efter en sammanslagning & förvärv. Även om det finns rena modeller är även hybrid modeller vanliga. Standard konfigurationen i Azure AD Connect Sync förutsätter ingen viss modell, men beroende på hur användar matchning har valts i installations guiden kan olika beteenden observeras.

I det här avsnittet ska vi gå igenom hur standard konfigurationen beter sig i vissa topologier. Vi går igenom konfigurationen och redigeraren för Synkroniseringsregel kan användas för att titta på konfigurationen.

Konfigurationen förutsätter några allmänna regler:
* Oavsett vilken ordning vi importerar från källans aktiva kataloger bör slut resultatet alltid vara detsamma.
* Ett aktivt konto kommer alltid att bidra med inloggnings information, inklusive **userPrincipalName** och **sourceAnchor**.
* Ett inaktiverat konto kommer att bidra med userPrincipalName och sourceAnchor, om det inte är en länkad post låda, om det inte finns något aktivt konto att hitta.
* Ett konto med en länkad post låda används aldrig för userPrincipalName och sourceAnchor. Det förutsätts att ett aktivt konto kommer att hittas senare.
* Ett kontakt objekt kan tillhandahållas till Azure AD som en kontakt eller som en användare. Du vet inte riktigt förrän alla käll Active Directorys skogar har bearbetats.

## <a name="groups"></a>Grupper
Viktiga saker att tänka på när du synkroniserar grupper från Active Directory till Azure AD:

* Azure AD Connect exkluderar inbyggda säkerhets grupper från katalog synkronisering.

* Azure AD Connect stöder inte synkronisering av [primära grupp medlemskap](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771489(v=ws.11)) till Azure AD.

* Azure AD Connect stöder inte synkronisering av [medlemskap i dynamiska distributions grupper](/Exchange/recipients/dynamic-distribution-groups/dynamic-distribution-groups) till Azure AD.

* Så här synkroniserar du en Active Directory grupp till Azure AD som en e-postaktiverad grupp:

    * Om gruppens *proxyAddress* -attribut är tomt måste dess *e-* postattribut ha ett värde

    * Om gruppens *proxyAddress* -attribut inte är tomt måste det innehålla minst ett SMTP-proxy-adress värde. Här är några exempel:
    
      * En Active Directory grupp vars proxyAddress-attribut har värdet *{"X500:/0 = contoso. com/ou = Users/CN = testgroup"}* kommer inte att vara e-postaktiverade i Azure AD. Den har ingen SMTP-adress.
      
      * En Active Directory grupp vars proxyAddress-attribut har värden *{"X500:/0 = contoso. com/ou = Users/CN = testgroup", "SMTP: johndoe \@ contoso.com"}* kommer att vara e-postaktiverade i Azure AD.
      
      * En Active Directory grupp vars proxyAddress-attribut har värden *{"X500:/0 = contoso. com/ou = Users/CN = testgroup", "SMTP: johndoe \@ contoso.com"}* kommer också att vara e-postaktiverade i Azure AD.

## <a name="contacts"></a>Kontakter
Att ha kontakter som representerar en användare i en annan skog är vanligt efter en sammanslagning & förvärv där en GALSync-lösning är bryggning av två eller flera Exchange-skogar. Objektet kontakt är alltid förenat från anslutnings utrymmet till metaversum med hjälp av e-postattributet. Om det redan finns ett kontakt objekt eller ett användar objekt med samma e-postadress kopplas objekten samman. Detta konfigureras i regeln **i från AD – kontakta Join**. Det finns också en regel med namnet **i från AD – kontakta common** med ett attributarkiv till metaversum-attributet **sourceObjectType** med den ständige **kontakten**. Den här regeln har en mycket låg prioritet, så om ett användar objekt är anslutet till samma metaversum-objekt, kommer regeln **i från AD – User common** att bidra till värde användaren för det här attributet. Med den här regeln kommer det här attributet att ha värdet kontakt om ingen användare har anslutits och värdet användare om minst en användare har hittats.

Vid etablering av ett objekt till Azure AD, skapar den utgående regeln **ut till AAD – kontakta Join** att ett kontakt objekt skapas om attributet metaversum **sourceObjectType** har angetts till **Contact**. Om det här attributet är inställt på **användare**, kommer regeln **ut till AAD – User Join** att skapa ett användar objekt i stället.
Det är möjligt att ett objekt höjs från kontakt till användare när fler käll aktiva kataloger importeras och synkroniseras.

I en GALSync-topologi kommer vi till exempel att hitta kontakt objekt för alla i den andra skogen när vi importerar den första skogen. Detta kommer att mellanlagra nya kontakt objekt i AAD-anslutningsprogrammet. När vi senare importerar och synkroniserar den andra skogen, hittar vi riktiga användare och kopplar dem till de befintliga metaversum-objekten. Vi tar sedan bort objektet kontakt i AAD och skapar ett nytt användar objekt i stället.

Om du har en topologi där användare representeras som kontakter, se till att du väljer att matcha användare på e-postattributet i installations guiden. Om du väljer ett annat alternativ kommer du att ha en order beroende konfiguration. Kontakt objekt kommer alltid att anslutas till e-postattributet, men användar objekt kommer bara att ansluta till attributet mail om det här alternativet har valts i installations guiden. Du kan sedan avsluta med två olika objekt i metaversum med samma e-postattribut om objektet importerades före användarobjektet. Vid export till Azure AD kommer ett fel att genereras. Det här beteendet är avsiktligt och indikerar felaktiga data eller att topologin inte identifierades korrekt under installationen.

## <a name="disabled-accounts"></a>Inaktiverade konton
Inaktiverade konton synkroniseras också med Azure AD. Inaktiverade konton är vanliga för att representera resurser i Exchange, till exempel konferens rum. Undantaget är användare med en länkad post låda. som tidigare nämnts kommer dessa aldrig att etablera ett konto till Azure AD.

Antagande är att om ett inaktiverat användar konto hittas kommer vi inte att hitta något annat aktivt konto senare och objektet tillhandahålls till Azure AD med userPrincipalName och sourceAnchor som hittas. Om ett annat aktivt konto kommer att ansluta till samma metaversum-objekt, kommer dess userPrincipalName och sourceAnchor att användas.

## <a name="changing-sourceanchor"></a>Ändra sourceAnchor
När ett objekt har exporter ATS till Azure AD är det inte tillåtet att ändra sourceAnchor längre. När objektet har exporter ATS metaversum-attributet **cloudSourceAnchor** anges med **sourceAnchor** -värdet som godkänts av Azure AD. Om **sourceAnchor** har ändrats och inte matchar **cloudSourceAnchor**, så kommer regeln **ut till AAD – User Join** att resultera i att sourceAnchor-attributet för fel **har ändrats**. I det här fallet måste konfigurationen eller data korrigeras så att samma sourceAnchor finns i metaversum igen innan objektet kan synkroniseras igen.

## <a name="additional-resources"></a>Ytterligare resurser
* [Azure AD Connect synkronisering: Anpassa synkroniseringsalternativ](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
