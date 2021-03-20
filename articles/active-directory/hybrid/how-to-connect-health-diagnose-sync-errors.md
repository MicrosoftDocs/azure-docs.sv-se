---
title: Azure AD Connect Health-diagnostisera synkroniseringsfel | Microsoft Docs
description: Det här dokumentet beskriver diagnos processen för dubblerade synkroniseringsfel och en eventuell korrigering av de överblivna objekt scenarierna direkt från Azure Portal.
services: active-directory
documentationcenter: ''
author: billmath
manager: maheshu
editor: billmath
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/11/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fe6af43c9ca44095c328356e8171da10717875e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98728245"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Diagnostisera och åtgärda synkroniseringsfel med duplicerade attribut

## <a name="overview"></a>Översikt
Ta ett steg längre för att markera synkroniseringsfel, Azure Active Directory (Azure AD) Connect Health introducerar självbetjänings reparation. Den felsöker dubbletten av synkroniseringsfel och korrigerar objekt som är överblivna från Azure AD.
Diagnos funktionen har följande fördelar:
- Den innehåller en diagnostisk procedur som begränsar synkroniseringsfel för dubblerade attribut. Och ger vissa korrigeringar.
- Den tillämpar en korrigering för dedikerade scenarier från Azure AD för att lösa felet i ett enda steg.
- Det krävs ingen uppgradering eller konfiguration för att aktivera den här funktionen.
Mer information om Azure AD finns i [Identitetssynkronisering och duplicerat attribut återhämtning](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="problems"></a>Problem
### <a name="a-common-scenario"></a>Ett vanligt scenario
När **QuarantinedAttributeValueMustBeUnique** -och **AttributeValueMustBeUnique** -Synkroniseringsfel inträffar är det vanligt att se en konflikt i en **userPrincipalName** -eller **proxy-adress** i Azure AD. Du kan lösa synkroniseringsfel genom att uppdatera det motstridiga källobjektet från den lokala sidan. Synkroniseringsfel kommer att lösas efter nästa synkronisering. Den här bilden visar till exempel att två användare har en konflikt mellan **userPrincipalName**. Båda är **Joe. J \@ contoso.com**. Objekt som är i konflikt i karantän i Azure AD.

![Felsöka vanliga scenarion vid synkroniseringsfel](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Scenario för överblivna objekt
Ibland kanske du upptäcker att en befintlig användare förlorar **käll ankaret**. Borttagningen av källobjektet inträffade i lokala Active Directory. Men ändringen av borttagnings signalen synkroniserades aldrig till Azure AD. Den här förlusten sker av orsaker som problem med synkroniseringsproblem eller domän migrering. När samma objekt återställs eller återskapas logiskt, ska en befintlig användare vara den användare som ska synkroniseras från **käll fäst punkten**. 

När en befintlig användare är ett moln objekt kan du också se den motstridiga användaren som synkroniseras med Azure AD. Användaren kan inte matchas i synkronisering med det befintliga objektet. Det finns inget direkt sätt att mappa om **käll ankaret**. Läs mer om den [befintliga kunskaps basen](https://support.microsoft.com/help/2647098). 

Det befintliga objektet i Azure AD bevarar till exempel licensen för Johan. Ett nyligen synkroniserat objekt med en annan **käll ankare** sker i ett duplicerat attribut tillstånd i Azure AD. Ändringar för Joe i lokala Active Directory tillämpas inte på Joe ' s ursprungliga användare (befintligt objekt) i Azure AD.  

![Diagnostisera synkroniseringsfel överblivna objekt scenario](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Diagnostik-och fel söknings steg i Connect Health 
Funktionen diagnostisera stöder användar objekt med följande duplicerade attribut:

| Attributnamn | Typer av synkroniseringsfel|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique eller AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique eller AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> För att få åtkomst till den här funktionen är **Global administratörs** behörighet eller **deltagar** behörighet från Azure RBAC obligatorisk.
>

Följ stegen från Azure Portal för att begränsa synkroniseringsfel och tillhandahålla mer specifika lösningar:

![Steg vid diagnos av synkroniseringsfel](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

Ta några steg för att identifiera vissa fixable-scenarier från Azure Portal:  
1.  Kontrol lera kolumnen **diagnostisera status** . Statusen visar om det finns ett möjligt sätt att åtgärda ett synkroniseringsfel direkt från Azure Active Directory. Ett fel söknings flöde finns med andra ord som kan begränsa fel fallet och eventuellt åtgärda det.

| Status | Vad betyder det? |
| ------------------ | -----------------|
| Inte startat | Du har inte besökt den här diagnos processen. Det finns ett möjligt sätt att åtgärda synkroniseringsfel direkt från portalen, beroende på det diagnostiska resultatet. |
| Manuell korrigering krävs | Felet passar inte villkoren för tillgängliga korrigeringar från portalen. Objekt typerna som är i konflikt med varandra är inte användare, eller så har du redan gått igenom de diagnostiska stegen och ingen korrigerings lösning var tillgänglig från portalen. I det senare fallet är en åtgärd från den lokala sidan fortfarande en av lösningarna. [Läs mer om lokala korrigeringar](https://support.microsoft.com/help/2647098). | 
| Väntande synkronisering | En korrigering tillämpades. Portalen väntar på nästa Sync-cykel för att rensa felet. |

  >[!IMPORTANT]
  > Kolumnen diagnostisk status återställs efter varje synkronisering. 
  >

1. Välj knappen **diagnostisera** under fel informationen. Du kommer att besvara några frågor och identifiera informationen om synkroniseringsfel. Svar på frågor hjälper dig att identifiera ett överblivna objekt.

1. Om en **stängnings** knapp visas i slutet av diagnostiken finns det ingen snabb korrigering tillgänglig från portalen, baserat på dina svar. Läs den lösning som visas i det sista steget. Korrigeringar från lokala lösningar är fortfarande lösningarna. Välj knappen **Stäng** . Status för det aktuella synkroniseringsfel växlar till **Manuell korrigering krävs**. Statusen ligger kvar under den aktuella synkroniseringen.

1. När ett överblivna objekt har identifierats kan du åtgärda synkroniseringsfel direkt från portalen. Om du vill utlösa processen väljer du knappen **Använd korrigering** . Status för det aktuella synkroniseringsfel uppdateras till **väntande synkronisering**.

1. Efter nästa Sync-cykel bör felet tas bort från listan.

## <a name="how-to-answer-the-diagnosis-questions"></a>Hur du besvarar diagnos frågor 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Finns användaren i din lokala Active Directory?

Den här frågan försöker identifiera källobjektet för den befintliga användaren från den lokala Active Directory.  
1. Kontrol lera om Azure Active Directory har ett objekt med angivet **userPrincipalName**. Om inte, svara på **Nej**.
2. Om det gör det kontrollerar du om objektet fortfarande finns i omfånget för synkronisering.  
   - Sök i Azure AD Connector-utrymmet genom att använda DN.
   - Om objektet finns i **väntande Lägg till** -tillstånd, svara **Nej**. Azure AD Connect kan inte ansluta objektet till rätt Azure AD-objekt.
   - Om objektet inte hittas svarar du **Ja**.

I de här exemplen försöker frågan identifiera om **Joe Jackson** fortfarande finns i lokala Active Directory.
För det **vanliga scenariot** finns både användarna **Joe Johnson** och **Joe Jackson** i lokala Active Directory. Objekt i karantän är två olika användare.

![Felsöka vanliga scenarion vid synkroniseringsfel](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

För det **överblivna objekt scenariot** finns det bara en användare med **Joe-Johnson** i lokala Active Directory:

![Diagnostisera synkroniseringsfel överblivna objekt * finns användaren * scenariot](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Tillhör båda dessa konton samma användare?
Den här frågan kontrollerar en inkommande motstridig användare och det befintliga användarobjektet i Azure AD för att se om de tillhör samma användare.  
1. Objektet som krockar är nyligen synkroniserat med Azure Active Directory. Jämför objektets attribut:  
   - Visningsnamn
   - UPN (User Principal Name)
   - Objekt-ID
2. Om det inte går att jämföra dem med Azure AD kontrollerar du om Active Directory har objekt med angiven **UserPrincipalNames**. Svara **Nej** om du hittar båda.

I följande exempel tillhör de två objekten till samma användar- **Johan Johnson**.

![Diagnostisera synkroniseringsfel överblivna objekt * samma användare * scenario](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Vad som händer när korrigeringen har tillämpats i scenariot för överblivna objekt
Utifrån svaren på föregående frågor visas knappen **Använd korrigering** när det finns en korrigerings fil från Azure AD. I det här fallet synkroniseras det lokala objektet med ett oväntat Azure AD-objekt. De två objekten mappas med hjälp av **käll ankaret**. **Använd korrigerings** ändring tar dessa eller liknande steg:
1. Uppdaterar **käll fäst punkten** till rätt objekt i Azure AD.
2. Tar bort objektet i konflikt i Azure AD om det finns.

![Diagnostisera synkroniseringsfel efter korrigeringen](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> **Tillämpa korrigering** av ändringar gäller endast överblivna objekt.
>

Efter föregående steg kan användaren komma åt den ursprungliga resursen, som är en länk till ett befintligt objekt. Värdet **diagnostisera status** i listan uppdateras till **väntar på synkronisering**. Synkroniseringsfel kommer att lösas efter nästa synkronisering. Connect Health visar inte längre det lösta synkroniseringsfel i list visningen.

## <a name="failures-and-error-messages"></a>Fel och fel meddelanden
**Användaren med ett attribut som är i konflikt är mjuk borttagning i Azure Active Directory. Se till att användaren är hårt borttagen innan du försöker igen.**  
Användaren med ett attribut i konflikt i Azure AD bör rensas innan du kan använda Fix. Ta en titt på [hur du tar bort användaren permanent i Azure AD](../fundamentals/active-directory-users-restore.md) innan du försöker utföra korrigeringen igen. Användaren tas också bort automatiskt efter 30 dagar i läget Soft Deleted. 

**Det går inte att uppdatera käll ankare till molnbaserad användare i din klient organisation.**  
Molnbaserad användare i Azure AD ska inte ha käll ankare. Uppdatering av käll ankare stöds inte i det här fallet. Manuell korrigering krävs från lokalt. 

**Det gick inte att uppdatera värdena i korrigerings processen.**
De angivna inställningarna som [UserWriteback i Azure AD Connect](./how-to-connect-preview.md#user-writeback) stöds inte. Inaktivera i inställningarna. 

## <a name="faq"></a>Vanliga frågor
**F.** Vad händer om det inte går att **använda Fix** -åtgärden?  
**En.** Om körningen Miss lyckas är det möjligt att Azure AD Connect kör ett export fel. Uppdatera portal sidan och försök igen efter nästa synkronisering. Standard cykeln för synkronisering är 30 minuter. 


**C.** Vad händer om det **befintliga objektet** ska vara det objekt som ska tas bort?  
**En.** Om det **befintliga objektet** ska tas bort, innebär det inte att **käll ankaret** ändras. Vanligt vis kan du åtgärda det från lokala Active Directory. 


**C.** Vilken behörighet behöver användaren för att kunna tillämpa korrigeringen?  
**En.** **Global administratör** eller **deltagare** från Azure RBAC har behörighet att komma åt diagnostik-och fel söknings processen.


**C.** Måste jag konfigurera Azure AD Connect eller uppdatera Azure AD Connect Health Agent för den här funktionen?  
**En.** Nej, diagnos processen är en fullständig molnbaserad funktion.


**C.** Om det befintliga objektet är mjukt Borttaget, gör diagnosen att objektet aktive ras igen?  
**En.** Nej, den här korrigeringen kommer inte att uppdatera objektattribut som inte är **käll ankare**.