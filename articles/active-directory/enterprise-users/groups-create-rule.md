---
title: Skapa eller redigera en dynamisk grupp och hämta status – Azure AD | Microsoft Docs
description: Hur du skapar eller uppdaterar en grupp medlemskaps regel i Azure Portal och kontrollerar dess bearbetnings status.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8525e9a76fa2439692cdb26b36c0bb761d63177c
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550237"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Skapa eller uppdatera en dynamisk grupp i Azure Active Directory

I Azure Active Directory (Azure AD) kan du använda regler för att fastställa grupp medlemskap baserat på användar-eller enhets egenskaper. Den här artikeln beskriver hur du konfigurerar en regel för en dynamisk grupp i Azure Portal.
Dynamiskt medlemskap stöds för säkerhets grupper eller Microsoft 365 grupper. När en grupp medlemskaps regel används utvärderas användar-och enhets attribut för matchningar med medlemskaps regeln. När ett attributändringar ändras för en användare eller enhet bearbetas alla dynamiska grupp regler i organisationen för medlemskaps ändringar. Användare och enheter läggs till eller tas bort om de uppfyller villkoren för en grupp. Säkerhets grupper kan användas för antingen enheter eller användare, men Microsoft 365 grupper kan bara vara användar grupper. Användning av dynamiska grupper kräver Azure AD Premium P1-licens. Se [regler för dynamiskt medlemskap för grupper](./groups-dynamic-membership.md) för mer information. 

## <a name="rule-builder-in-the-azure-portal"></a>Regel verktyg i Azure Portal

Azure AD tillhandahåller ett regel verktyg för att skapa och uppdatera viktiga regler snabbare. Regel verktyget stöder konstruktion upp till fem uttryck. Regel verktyget gör det lättare att skapa en regel med några enkla uttryck, men den kan inte användas för att återskapa varje regel. Om regel verktyget inte stöder den regel som du vill skapa kan du använda text rutan.

Här följer några exempel på avancerade regler eller syntax som vi rekommenderar att du skapar med hjälp av text rutan:

- Regel med fler än fem uttryck
- Regeln för direkt rapporter
- Ställer in [operator prioritet](groups-dynamic-membership.md#operator-precedence)
- [Regler med komplexa uttryck](groups-dynamic-membership.md#rules-with-complex-expressions); till exempel `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Regel verktyget kanske inte kan visa vissa regler som skapats i text rutan. Ett meddelande kan visas om regel verktyget inte kan visa regeln. Regel verktyget ändrar inte den syntax, validering eller bearbetning av dynamiska grupp regler som stöds på något sätt.

![Skärm bild som visar sidan "dynamiska medlemskaps regler" med åtgärden "Lägg till uttryck" på fliken "Konfigurera regler" vald.](./media/groups-create-rule/update-dynamic-group-rule.png)

Exempel på syntax, stödda egenskaper, operatorer och värden för en medlemskaps regel finns i [regler för dynamiska medlemskap för grupper i Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Så här skapar du en grupp medlemskaps regel

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med ett konto som finns i rollen global administratör, Intune-administratör eller användar administratör i Azure AD-organisationen.
1. Sök efter och välj **grupper**.
1. Välj **alla grupper** och välj sedan **ny grupp**.

   ![Välj kommandot för att lägga till en ny grupp](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. På sidan **grupp** anger du ett namn och en beskrivning för den nya gruppen. Välj en **medlemskaps typ** för antingen användare eller enheter och välj sedan **Lägg till dynamisk fråga**. Regel verktyget stöder upp till fem uttryck. Om du vill lägga till fler än fem uttryck måste du använda text rutan.

   ![Skärm bild som visar sidan "alla grupper" med åtgärden "ny grupp" vald.](./media/groups-create-rule/add-dynamic-group-rule.png)

1. För att se de anpassade tilläggs egenskaper som är tillgängliga för din medlemskaps fråga:
   1. Välj **Hämta anpassade tilläggs egenskaper**
   1. Ange program-ID och välj sedan **Uppdatera egenskaper**.
1. När du har skapat regeln väljer du **Spara**.
1. Skapa gruppen genom att välja **skapa** på sidan **ny grupp** .

Om regeln du angav inte är giltig visas en förklaring av varför regeln inte kunde bearbetas i ett Azure-meddelande i portalen. Läs det noggrant och lär dig hur du åtgärdar regeln.

## <a name="to-update-an-existing-rule"></a>Så här uppdaterar du en befintlig regel

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med ett konto som finns i rollen global administratör, grupp administratör, Intune-administratör eller användar administratör i Azure AD-organisationen.
1. Välj **grupper**  >  **alla grupper**.
1. Välj en grupp för att öppna dess profil.
1. På sidan profil för gruppen väljer du regler för **dynamiskt medlemskap**. Regel verktyget stöder upp till fem uttryck. Om du vill lägga till fler än fem uttryck måste du använda text rutan.

   ![Lägg till medlemskaps regel för en dynamisk grupp](./media/groups-create-rule/update-dynamic-group-rule.png)

1. För att se de anpassade tilläggs egenskaper som är tillgängliga för medlemskaps regeln:
   1. Välj **Hämta anpassade tilläggs egenskaper**
   1. Ange program-ID och välj sedan **Uppdatera egenskaper**.
1. När du har uppdaterat regeln väljer du **Spara**.

## <a name="turn-on-or-off-welcome-email"></a>Aktivera eller inaktivera välkomst meddelande

När en ny Microsoft 365-grupp skapas, skickas ett välkomst meddelande till de användare som läggs till i gruppen. Senare, om några attribut för en användare eller enhet ändras, bearbetas alla dynamiska grupp regler i organisationen för medlemskaps ändringar. Användare som läggs till får även välkomst meddelandet. Du kan inaktivera det här beteendet i [Exchange PowerShell](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup).

## <a name="check-processing-status-for-a-rule"></a>Kontrol lera bearbetnings status för en regel

Du kan se status för medlemskaps bearbetning och senaste uppdaterade datum på **översikts** sidan för gruppen.
  
  ![visning av status för dynamisk grupp](./media/groups-create-rule/group-status.png)

Följande status meddelanden kan visas för status för **medlemskaps bearbetning** :

- **Utvärdering**: grupp ändringen har mottagits och uppdateringarna utvärderas.
- **Bearbetar**: uppdateringar bearbetas.
- **Uppdateringen är klar**: bearbetningen har slutförts och alla tillämpliga uppdateringar har gjorts.
- **Bearbetnings fel**: bearbetningen kunde inte slutföras på grund av ett fel när medlemskaps regeln utvärderades.
- **Uppdateringen har pausats**: uppdateringar av dynamiska medlemskaps regler har pausats av administratören. MembershipRuleProcessingState är inställd på pausad.

Följande status meddelanden kan visas för **medlemskapets senast uppdaterade** status:

- &lt;**Datum och tid** &gt; : när medlemskapet senast uppdaterades.
- **Pågår**: uppdateringar pågår just nu.
- **Okänd**: det går inte att hämta den senaste uppdaterings tiden. Gruppen kan vara ny.

Om ett fel inträffar när medlemskaps regeln för en grupp bearbetas visas en avisering överst på **sidan Översikt** för gruppen. Om inga väntande dynamiska medlemskaps uppdateringar kan bearbetas för alla grupper inom organisationen i mer än 24 timmar visas en avisering överst i **alla grupper**.

![bearbetar aviseringar om fel meddelande](./media/groups-create-rule/processing-error.png)

De här artiklarna innehåller ytterligare information om grupper i Azure Active Directory.

- [Se befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Skapa en ny grupp och lägga till medlemmar](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Hantera inställningar för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Hantera dynamiska regler för användare i en grupp](groups-dynamic-membership.md)