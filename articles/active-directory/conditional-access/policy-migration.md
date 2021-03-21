---
title: Migrera principer för villkorlig åtkomst – Azure Active Directory
description: Lär dig vad du behöver veta för att migrera klassiska principer i Azure Portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1811d5b9ae4d3e34b48e1cdc156438f2bad98d1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90601664"
---
# <a name="conditional-access-classic-policy-migration"></a>Migrering av klassisk princip för villkorlig åtkomst

Villkorlig åtkomst är det verktyg som används av Azure Active Directory för att ta fram signaler, fatta beslut och tillämpa organisations principer. Villkorlig åtkomst är kärnan i det nya identitets drivna kontroll planet. Även om syftet fortfarande är detsamma, har lanseringen av den nya Azure Portal infört betydande förbättringar av hur villkorlig åtkomst fungerar.

Överväg att migrera principerna som du inte har skapat i Azure Portal eftersom:

- Nu kan du adressera scenarier som du inte kan hantera tidigare.
- Du kan minska antalet principer som du måste hantera genom att konsolidera dem.
- Du kan hantera alla principer för villkorlig åtkomst på en central plats.
- Den klassiska Azure-portalen kommer att dras tillbaka.

Den här artikeln förklarar vad du behöver veta för att migrera dina befintliga principer för villkorlig åtkomst till det nya ramverket.

## <a name="classic-policies"></a>Klassiska principer

I [Azure Portal](https://portal.azure.com)finns villkorliga åtkomst principer under **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**. Din organisation kan också ha äldre principer för villkorlig åtkomst som inte skapas på den här sidan. Dessa principer kallas *klassiska principer*. Klassiska principer är principer för villkorlig åtkomst, du har skapat i:

- Den klassiska Azure-portalen
- Den klassiska Intune-portalen
- Intune-appskydd Portal

På sidan **villkorlig åtkomst** kan du komma åt dina klassiska principer genom att klicka på [**klassiska principer**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) i avsnittet **Hantera** . 

![Villkorlig åtkomst i Azure AD som visar klassisk policys-vy](./media/policy-migration/71.png)

Vyn **klassiska principer** ger dig ett alternativ för att:

- Filtrera dina klassiska principer.
- Inaktivera klassiska principer.
- Granska inställningarna för en klassisk princip och inaktivera den.

   ![Princip information för klassisk inklusive befintlig princip konfiguration](./media/policy-migration/74.png)

> [!WARNING]
> När inaktiverat en klassisk princip kan inte aktive ras igen.

I vyn detaljer i en klassisk princip kan du dokumentera inställningarna, ändra de inkluderade eller exkluderade grupperna och inaktivera principen.

![Princip information – grupper som ska tas med eller undantas](./media/policy-migration/75.png)

Genom att ändra de valda grupperna eller genom att exkludera vissa grupper kan du testa resultatet av en inaktive rad klassisk princip för några test användare innan du inaktiverar principen för alla inkluderade användare och grupper.
 
## <a name="migration-considerations"></a>Överväganden vid migrering

I den här artikeln kallas principer för villkorlig åtkomst för Azure AD också *nya principer*.
De klassiska principerna fortsätter att fungera sida vid sida med dina nya principer tills du inaktiverar eller tar bort dem. 

Följande aspekter är viktiga i samband med en princip konsolidering:

- Även om de klassiska principerna är knutna till en speciell molnbaserad app, kan du välja så många molnappar som du behöver i en ny princip.
- Kontroller av en klassisk princip och en ny princip för en molnbaserad app kräver att alla kontroller (*och*) är uppfyllda. 
- I en ny princip kan du:
   - Kombinera flera villkor om ditt scenario kräver det. 
   - Välj flera bidrags krav som åtkomst kontroll och kombinera dem med ett logiskt *eller* (Kräv en av de valda kontrollerna) eller med ett logiskt *och* (Kräv alla valda kontroller).

### <a name="exchange-online"></a>Exchange online

Om du vill migrera klassiska principer för **Exchange Online** som innehåller **Exchange Active Sync** som klient program villkor, kanske du inte kan konsolidera dem till en ny princip. 

Detta är till exempel fallet om du vill stödja alla typer av klient program. I en ny princip som har **Exchange Active Sync** som klient program villkor kan du inte välja andra klient program.

![Villkorlig åtkomst val av klient program](./media/policy-migration/64.png)

Det går inte heller att utföra en konsolidering i en ny princip om de klassiska principerna innehåller flera villkor. En ny princip som har kon figurer ATS för **Exchange Active Sync** som klient program inte stöder andra villkor:   

![Exchange ActiveSync stöder inte de valda villkoren](./media/policy-migration/08.png)

Om du har en ny princip som har konfigurerat villkor för **Exchange Active Sync** som klient program, måste du se till att alla andra villkor inte är konfigurerade. 

![Villkor för villkorlig åtkomst](./media/policy-migration/16.png)
 
App-baserade klassiska principer för Exchange Online som omfattar **Exchange Active Sync** som klient program villkor tillåter **stöd** för plattformar som stöds och **inte stöds** . Även om du inte kan konfigurera enskilda enhets plattformar i en relaterad ny princip kan du begränsa stödet till endast [plattformar som stöds](concept-conditional-access-conditions.md#device-platforms) . 

![Villkorlig åtkomst Välj Exchange ActiveSync](./media/policy-migration/65.png)

Du kan konsolidera flera klassiska principer som omfattar **Exchange Active Sync** som klient program villkor om de har:

- Endast **Exchange Active Sync** som villkor 
- Flera krav för att bevilja åtkomst konfigurerat

Ett vanligt scenario är konsolideringen av:

- En enhets beroende klassisk princip från den klassiska Azure-portalen 
- En app-baserad klassisk princip i Intunes app Protection-Portal 
 
I det här fallet kan du konsolidera dina klassiska principer till en ny princip som har båda de krav som valts.

![Kontroller för villkorlig åtkomst tilldelning](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Enhetsplattformar

Klassiska principer med app-baserade kontroller är förkonfigurerade med iOS och Android som enhets plattforms villkor. 

I en ny princip måste du välja de [enhets plattformar](concept-conditional-access-conditions.md#device-platforms) som du vill stödja individuellt.

![Val av enhets plattformar för villkorlig åtkomst](./media/policy-migration/41.png)

## <a name="next-steps"></a>Nästa steg

- [Använd endast rapport läge för villkorlig åtkomst för att fastställa effekten av nya princip beslut.](concept-conditional-access-report-only.md)
- Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst, se [vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md).
- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö kan du läsa artikeln [så här: planera din distribution av villkorlig åtkomst i Azure Active Directory](plan-conditional-access.md). 
