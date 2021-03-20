---
title: Villkorlig åtkomst – kombinerad säkerhets information – Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst för registrering av säkerhets information
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e99f7466bd3b7ed5517157ca3fa45e7c3241217
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98599767"
---
# <a name="conditional-access-securing-security-info-registration"></a>Villkorsstyrd åtkomst: Säkra registrering av säkerhetsinformation

Nu kan du skydda när och hur användare registrerar sig för Azure AD Multi-Factor Authentication och återställning av lösen ord för självbetjäning är nu möjligt med användar åtgärder i principen för villkorlig åtkomst. Den här förhands gransknings funktionen är tillgänglig för organisationer som har aktiverat den [kombinerade förhands granskningen](../authentication/concept-registration-mfa-sspr-combined.md). Den här funktionen kan vara aktive rad i organisationer där de vill använda villkor som betrott nätverks plats för att begränsa åtkomsten till registrering för Azure AD Multi-Factor Authentication och lösen ords återställning via självbetjäning (SSPR). Mer information om användbara villkor finns i artikeln [villkorlig åtkomst: villkor](concept-conditional-access-conditions.md).

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Skapa en princip för att kräva registrering från en betrodd plats

Följande princip gäller för alla valda användare, som försöker registrera sig med den kombinerade registrerings upplevelsen och blockerar åtkomsten om de inte ansluter från en plats som har marker ATS som ett betrott nätverk.

1. I **Azure Portal** bläddrar du till **Azure Active Directory**  >  **säkerhet** för  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. I namn anger du ett namn för den här principen. Till exempel **kombinerad säkerhets informations registrering på betrodda nätverk**.
1. Under **tilldelningar** väljer du **användare och grupper** och väljer de användare och grupper som du vill att den här principen ska tillämpas på.

   > [!WARNING]
   > Användare måste aktive ras för den [kombinerade registreringen](../authentication/howto-registration-mfa-sspr-combined.md).

   1. Under **exkludera** väljer **du användare och grupper** och väljer organisationens nödfalls åtkomst eller Bryt glas konton. 
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder** väljer du **användar åtgärder**, markera **Registrera säkerhets information**.
1. Under **villkor**  >  **platser**.
   1. Konfigurera **Ja**.
   1. Ta med **vilken plats som helst**.
   1. Undanta **alla betrodda platser**.
   1. Välj **gör** på bladet platser.
   1. Välj **färdig** på bladet villkor.
1. Under **villkor**  >  **klient program (för hands version)** anger du **Konfigurera** till **Ja** och väljer **klart**.
1. Under **åtkomst kontroller**  >  **beviljar**.
   1. Välj **blockera åtkomst**.
   1. Klicka sedan på **Välj**.
1. Ange **Aktivera princip** till **På**.
1. Välj sedan **Spara**.

I steg 6 i den här policyn har organisationer alternativ som de kan göra. Principen ovan kräver registrering från en betrodd nätverks plats. Organisationer kan välja att använda alla tillgängliga villkor i stället för **platser**. Kom ihåg att den här principen är en spärr princip, så att allt som inkluderas blockeras och allt som inte matchar include är tillåtet. 

Vissa kan välja att använda enhets tillstånd i stället för plats i steg 6 ovan:

6. Under **villkor**  >  **enhets tillstånd (för hands version)**.
   1. Konfigurera **Ja**.
   1. Ta med **all enhets status**.
   1. Undanta **enheten hybrid Azure AD** och/eller **enhet markerad som kompatibel**
   1. Välj **gör** på bladet platser.
   1. Välj **färdig** på bladet villkor.

> [!WARNING]
> Om du använder enhets tillstånd som ett villkor i principen kan det påverka gäst användare i katalogen. [Endast rapport läge](concept-conditional-access-report-only.md) kan hjälpa dig att fastställa konsekvenserna av princip beslut.
> Observera att endast rapport läge är tillämpligt för principer för villkorlig åtkomst med "användar åtgärder"-omfattningen.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Bestäm inverkan med endast rapport läge för villkorlig åtkomst](howto-conditional-access-insights-reporting.md)

[Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst](troubleshoot-conditional-access-what-if.md)
