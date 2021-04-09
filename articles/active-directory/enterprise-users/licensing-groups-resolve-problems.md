---
title: Lös problem med grupp licens tilldelningar – Azure Active Directory | Microsoft Docs
description: Så här identifierar och löser du licens tilldelnings problem när du använder Azure Active Directory gruppbaserad licensiering
services: active-directory
keywords: Azure AD-licensiering
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ceea2dbcf408b6b35ef7c26580278d3979244119
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551546"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identifiera och lösa licens tilldelnings problem för en grupp i Azure Active Directory

Gruppbaserad licensiering i Azure Active Directory (Azure AD) introducerar begreppet användare i ett licensierings fel tillstånd. I den här artikeln förklarar vi varför användare kan komma i det här läget.

När du tilldelar licenser direkt till enskilda användare, utan att använda gruppbaserad licensiering, kan det hända att tilldelnings åtgärden Miss känner. När du till exempel kör PowerShell-cmdleten `Set-MsolUserLicense` på ett användar system kan cmdleten inte köras av många orsaker som är relaterade till affärs logik. Det kan till exempel finnas ett otillräckligt antal licenser eller en konflikt mellan två tjänst planer som inte kan tilldelas samtidigt. Problemet rapporteras omedelbart tillbaka till dig.

När du använder gruppbaserad licensiering kan samma fel inträffa, men de inträffar i bakgrunden medan Azure AD-tjänsten tilldelar licenser. Därför kan felen inte kommunicera direkt med dig. De registreras i stället på användarobjektet och rapporteras sedan via administrations portalen. Det ursprungliga syftet med att licensiera användaren går aldrig förlorat, men det registreras i ett fel tillstånd för framtida undersökning och lösning.

## <a name="find-license-assignment-errors"></a>Hitta fel vid licens tilldelning

### <a name="to-find-users-in-an-error-state-in-a-group"></a>Hitta användare i ett fel tillstånd i en grupp

1. Öppna gruppen på sidan Översikt och välj **licenser**. Ett meddelande visas om det finns några användare i ett fel tillstånd.

   ![Meddelande om grupp-och fel meddelanden](./media/licensing-groups-resolve-problems/group-error-notification.png)

1. Välj meddelandet för att öppna en lista över alla berörda användare. Du kan välja varje användare individuellt för att se mer information.

   ![lista över användare i grupp licensierings fel tillstånd](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

1. Om du vill hitta alla grupper som innehåller minst ett fel klickar du på bladet **Azure Active Directory** väljer **licenser** och väljer sedan **Översikt**. En informations ruta visas när grupper kräver din uppmärksamhet.

   ![Översikt och information om grupper med fel tillstånd](./media/licensing-groups-resolve-problems/group-errors-widget.png)

1. Markera rutan om du vill visa en lista över alla grupper med fel. Du kan välja varje grupp för mer information.

   ![Översikt och lista över grupper med fel](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)

I följande avsnitt får du en beskrivning av varje möjligt problem och hur du kan lösa det.

## <a name="not-enough-licenses"></a>Inte tillräckligt med licenser

**Problem:** Det finns inte tillräckligt många tillgängliga licenser för en av de produkter som anges i gruppen. Du behöver köpa fler licenser för produkten eller frigöra oanvända licenser från andra användare eller grupper.

Om du vill se hur många licenser som är tillgängliga går du till **Azure Active Directory**  >  **licenser**  >  **alla produkter**.

Om du vill se vilka användare och grupper som använder licenser väljer du en produkt. Under **licensierade användare** visas en lista över alla användare som har haft licenser som tilldelats direkt eller via en eller flera grupper. Under **licensierade grupper** visas alla grupper som har tilldelats dessa produkter.

**PowerShell:** PowerShell-cmdlets rapporterar felet som _CountViolation_.

## <a name="conflicting-service-plans"></a>Tjänst planer som står i konflikt

**Problem:** En av de produkter som anges i gruppen innehåller en tjänst plan som står i konflikt med en annan tjänst plan som redan har tilldelats användaren via en annan produkt. Vissa tjänste planer är konfigurerade på ett sätt som de inte kan tilldelas samma användare som en annan, relaterad service plan.

Betänk följande exempel. En användare har en licens för Office 365 Enterprise *E1* som tilldelas direkt, med alla aktiverade planer. Användaren har lagts till i en grupp som har tilldelats Office 365 Enterprise *E3* -produkten. E3-produkten innehåller tjänst planer som inte överlappar de planer som ingår i E1, så grupp licens tilldelningen Miss lyckas med fel meddelandet "tjänst planer som står i konflikt". I det här exemplet är de motstridiga service planerna:

- Exchange Online (plan 2) står i konflikt med Exchange Online (plan 1).

För att lösa den här konflikten måste du inaktivera två av planerna. Du kan inaktivera E1-licensen som är direkt tilldelad användaren. Eller så måste du ändra hela grupp licens tilldelningen och inaktivera planerna i E3-licensen. Alternativt kan du välja att ta bort E1-licensen från användaren om den är redundant i samband med E3-licensen.

Beslutet om hur du löser konflikter mellan produkt licenser tillhör alltid-administratören. Azure AD löser inte automatiskt licens konflikter.

**PowerShell:** PowerShell-cmdlets rapporterar felet som _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Andra produkter är beroende av den här licensen

**Problem:** En av de produkter som anges i gruppen innehåller en service plan som måste vara aktive rad för en annan tjänst plan, i en annan produkt, för att fungera. Det här felet uppstår när Azure AD försöker ta bort den underliggande tjänst planen. Detta kan till exempel inträffa när du tar bort användaren från gruppen.

För att lösa det här problemet måste du kontrol lera att den nödvändiga planen fortfarande är tilldelad till användarna via någon annan metod eller att de beroende tjänsterna är inaktiverade för dessa användare. När du har gjort det kan du ta bort grupp licensen på rätt sätt från dessa användare.

**PowerShell:** PowerShell-cmdlets rapporterar felet som _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>Användnings platsen är inte tillåten

**Problem:** Vissa Microsoft-tjänster är inte tillgängliga på alla platser på grund av lokala lagar och föreskrifter. Innan du kan tilldela en licens till en användare måste du ange egenskapen **användnings plats** för användaren. Du kan ange platsen under redigera **användar**  >  **profil**  >   avsnittet i Azure Portal.

När Azure AD försöker tilldela en grupp licens till en användare vars användnings plats inte stöds, Miss lyckas den och registrerar ett fel för användaren.

Lös problemet genom att ta bort användare från platser som inte stöds från den licensierade gruppen. Alternativt, om värdena för den aktuella användnings platsen inte representerar den faktiska användar platsen, kan du ändra dem så att licenserna tilldelas korrekt nästa tid (om den nya platsen stöds).

**PowerShell:** PowerShell-cmdlets rapporterar felet som _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> När Azure AD tilldelar grupp licenser, ärver alla användare som saknar angiven användnings plats platsen för katalogen. Vi rekommenderar att administratörer anger rätt värden för användnings platsen för användarna innan de använder gruppbaserad licensiering för att följa lokala lagar och föreskrifter.

## <a name="duplicate-proxy-addresses"></a>Dubbla proxyadresser

Om du använder Exchange Online kan vissa användare i din organisation vara felaktigt konfigurerade med samma proxy-adress värde. När gruppbaserad licensiering försöker tilldela en licens till en sådan användare Miss lyckas den och visar att "proxy-adressen redan används".

> [!TIP]
> Om du vill se om det finns en dubblett av proxyservern kör du följande PowerShell-cmdlet mot Exchange Online:
> ```
> Get-Recipient -ResultSize unlimited | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
> ```
> Mer information om det här problemet finns i ["meddelande om att proxy-adressen redan används" i Exchange Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). Artikeln innehåller också information om [hur du ansluter till Exchange Online med hjälp av fjärr-PowerShell](/powershell/exchange/connect-to-exchange-online-powershell).

När du har löst eventuella problem med proxyservern för de berörda användarna, se till att tvinga licens bearbetning på gruppen att se till att licenserna nu kan tillämpas.

## <a name="azure-ad-mail-and-proxyaddresses-attribute-change"></a>Ändring av attribut för Azure AD mail och ProxyAddresses

**Problem:** När du uppdaterar licens tilldelningen för en användare eller grupp kan du se att Azure AD mail och ProxyAddresses-attributet för vissa användare har ändrats.

Vid uppdatering av licens tilldelningen för en användare utlöses att proxy-adress beräkningen har utlösts, vilket kan ändra användarattribut. Information om den exakta orsaken till ändringen och hur du löser problemet finns i den här artikeln om [hur proxyAddresses-attributet fylls i i Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

## <a name="licenseassignmentattributeconcurrencyexception-in-audit-logs"></a>LicenseAssignmentAttributeConcurrencyException i gransknings loggar

**Problem:** Användaren har LicenseAssignmentAttributeConcurrencyException för licens tilldelning i gransknings loggar.
När gruppbaserad licensiering försöker bearbeta samtidig licens tilldelning av samma licens till en användare, registreras detta undantag på användaren. Detta inträffar vanligt vis när en användare är medlem i mer än en grupp med samma tilldelade licens. Azure AD försöker att bearbeta användar licensen igen och kommer att lösa problemet. Kunden behöver inte vidta några åtgärder för att åtgärda problemet.

## <a name="more-than-one-product-license-assigned-to-a-group"></a>Fler än en produkt licens tilldelad till en grupp

Du kan tilldela fler än en produkt licens till en grupp. Du kan till exempel tilldela Office 365 Enterprise E3 och Enterprise Mobility + Security till en grupp för att enkelt aktivera alla inkluderade tjänster för användare.

Azure AD försöker tilldela alla licenser som anges i gruppen till varje användare. Om Azure AD inte kan tilldela någon av produkterna på grund av affärs logiks problem, tilldelar den inte de andra licenserna i gruppen. Ett exempel är om det inte finns tillräckligt med licenser för alla, eller om det finns konflikter med andra tjänster som är aktiverade för användaren.

Du kan se de användare som inte kunde tilldelas och kontrol lera vilka produkter som påverkas av det här problemet.

## <a name="when-a-licensed-group-is-deleted"></a>När en licensierad grupp tas bort

Du måste ta bort alla licenser som har tilldelats en grupp innan du kan ta bort gruppen. Det kan dock ta tid att ta bort licenser från alla användare i gruppen. När du tar bort licens tilldelningar från en grupp kan det uppstå fel om användaren har en beroende licens tilldelad eller om det finns ett problem med en proxy-konflikt som förhindrar licens borttagningen. Om en användare har en licens som är beroende av en licens som tas bort på grund av att gruppen tas bort, konverteras licens tilldelningen till användaren från ärvd till Direct.

Anta till exempel att en grupp med Office 365 E3/E5 har tilldelats en Skype for Business-tjänst plan aktive rad. Tänk också på att ett fåtal medlemmar i gruppen har licenser för ljud konferens som tilldelas direkt. När gruppen tas bort kommer gruppbaserad licensiering att försöka ta bort Office 365 E3/E5 från alla användare. Eftersom ljud konferens är beroende av Skype för företag, konverterar gruppbaserad licensiering Office 365 E3/E5-licenser till direkt licens tilldelning för alla användare med en tilldelad ljud konferens.

## <a name="manage-licenses-for-products-with-prerequisites"></a>Hantera licenser för produkter med krav

Vissa Microsoft Online-produkter som du kanske äger är *tillägg*. Tillägg kräver att en nödvändig tjänst plan aktive ras för en användare eller en grupp innan de kan tilldelas en licens. Med gruppbaserad licensiering kräver systemet att både tjänst avtalen för nödvändiga tjänster och tillägg finns i samma grupp. Detta görs för att se till att alla användare som läggs till i gruppen kan ta emot den fullständigt fungerande produkten. Vi tittar på följande exempel:

Microsoft Workplace Analytics är en tilläggs produkt. Den innehåller en enda service plan med samma namn. Vi kan bara tilldela den här tjänst planen till en användare eller grupp, när någon av följande förutsättningar också är tilldelad:

- Exchange Online (abonnemang 1)
- Exchange Online (plan 2)

Om vi försöker tilldela den här produkten till en grupp, returnerar portalen ett meddelande. Om vi väljer objekt information visas följande fel meddelande:

  "Licens åtgärden misslyckades. Kontrol lera att gruppen har nödvändiga tjänster innan du lägger till eller tar bort en beroende tjänst. **Tjänsten Microsoft Workplace Analytics kräver att Exchange Online (plan 2) också aktive ras.**"

För att tilldela den här tilläggs licensen till en grupp, måste du se till att gruppen även innehåller den nödvändiga service planen. Vi kan till exempel uppdatera en befintlig grupp som redan innehåller den fullständiga Office 365 E3-produkten och sedan lägga till produkten till den.

Det är också möjligt att skapa en fristående grupp som bara innehåller de minimi krav som krävs för att göra tillägget. Den kan endast användas för att licensiera valda användare för tilläggs produkten. Baserat på föregående exempel tilldelar du följande produkter till samma grupp:

- Office 365 Enterprise E3 med endast tjänsten Exchange Online (plan 2) aktive rad
- Microsoft Workplace Analytics

Från och med nu förbrukar alla användare som läggs till i den här gruppen en licens för E3-produkten och en licens för produkt för arbets plats analys. På samma sätt kan dessa användare vara medlemmar i en annan grupp som ger dem fullständig E3-produkt, och de använder fortfarande bara en licens för produkten.

> [!TIP]
> Du kan skapa flera grupper för varje tjänst plan som krävs. Om du till exempel använder både Office 365 Enterprise E1 och Office 365 Enterprise E3 för dina användare, kan du skapa två grupper för att licensiera Microsoft arbets plats analys: en som använder E1 som en förutsättning och den andra som använder E3. På så sätt kan du distribuera tillägget till E1-och E3-användare utan att använda fler licenser.

## <a name="force-group-license-processing-to-resolve-errors"></a>Framtvinga bearbetning av grup licens för att lösa fel

Beroende på vilka steg du har vidtagit för att lösa felen kan det vara nödvändigt att utlösa bearbetningen av en grupp manuellt för att uppdatera användar statusen.

Om du till exempel frigör några licenser genom att ta bort direkta licens tilldelningar från användarna, måste du utlösa bearbetningen av grupper som tidigare inte har kunnat få fullständig licens för alla användar medlemmar. Om du vill bearbeta om en grupp går du till fönstret grupp, öppnar **licenser** och väljer sedan knappen **bearbeta** i verktygsfältet.

## <a name="force-user-license-processing-to-resolve-errors"></a>Tvinga användar licens bearbetning för att lösa fel

Beroende på vilka steg du har vidtagit för att lösa felen kan det vara nödvändigt att utlösa bearbetningen av en användare manuellt för att uppdatera användarens tillstånd.

När du till exempel har löst problemet med dubblerad proxy-adress för en berörd användare måste du utlösa bearbetningen av användaren. Om du vill bearbeta om en användare går du till fönstret användare, öppnar **licenser** och väljer sedan knappen **bearbeta** i verktygsfältet.

## <a name="next-steps"></a>Nästa steg

Mer information om andra scenarier för licens hantering genom grupper finns i följande avsnitt:

* [Vad är gruppbaserad licensiering i Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Tilldela licenser till en grupp i Azure Active Directory](./licensing-groups-assign.md)
* [Migrera enskilda licensierade användare till gruppbaserad licensiering i Azure Active Directory](licensing-groups-migrate-users.md)
* [Så här migrerar du användare mellan produkt licenser med gruppbaserad licensiering i Azure Active Directory](licensing-groups-change-licenses.md)
* [Fler scenarier med gruppbaserad licensiering i Azure Active Directory](./licensing-group-advanced.md)
* [PowerShell-exempel för gruppbaserad licensiering i Azure Active Directory](licensing-ps-examples.md)