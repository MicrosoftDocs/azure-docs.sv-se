---
title: Felsöka villkorlig åtkomst med hjälp av What If-verktyget – Azure Active Directory
description: Var du hittar vilka villkorliga åtkomst principer som tillämpades och varför
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0112ab53c501d639d3f8e0d09d82ef3a12cb93a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94837254"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>Felsöka villkorlig åtkomst med hjälp av What If-verktyget

[What IFS verktyget](what-if-tool.md) i villkorlig åtkomst är kraftfullt vid försök att förstå varför en princip har eller inte tillämpats på en användare under en viss omständighet eller om en princip skulle gälla i ett känt tillstånd.

What If-verktyget finns i **Azure Portal**  >  **Azure Active Directory**  >  **villkorlig åtkomst**  >  **What If**.

![What If verktyget för villkorlig åtkomst i standard läge](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> What If verktyget utvärderar för närvarande inte principer i endast rapport läge.

## <a name="gathering-information"></a>Samlar in information

What If-verktyget kräver bara att en **användare** kommer igång. 

Följande ytterligare information är valfri, men kan hjälpa dig att begränsa omfattningen för specifika fall.

* Molnappar eller åtgärder
* IP-adress 
* Land/region
* Enhetsplattform
* Klient program (för hands version)
* Enhets tillstånd (för hands version) 
* Inloggningsrisk

Den här informationen kan samlas in från användaren, enheten eller inloggnings loggen för Azure AD.

## <a name="generating-results"></a>Genererar resultat

Ange de kriterier som samlats in i föregående avsnitt och välj **What If** för att generera en lista över resultat. 

Du kan när som helst välja **Återställ** för att rensa eventuella inmatade villkor och återgå till standard läget.

## <a name="evaluating-results"></a>Utvärderar resultat

### <a name="policies-that-will-apply"></a>Principer som ska gälla

I den här listan visas vilka principer för villkorlig åtkomst som gäller för villkoren. Listan innehåller både de bidrags kontroller och de sessionsnycklar som är tillämpliga. Exempel på detta är att kräva Multi-Factor Authentication för åtkomst till ett specifik program.

### <a name="policies-that-will-not-apply"></a>Principer som inte kommer att gälla

I den här listan visas principer för villkorlig åtkomst som inte gäller om villkoren tillämpas. Listan innehåller alla principer och orsaken till att de inte gäller. Exempel på detta är användare och grupper som kan uteslutas från en princip.

## <a name="use-case"></a>Användningsfall

Många organisationer skapar principer baserat på nätverks platser, som tillåter betrodda platser och blockerar platser där åtkomst inte ska ske.

För att verifiera att en konfiguration har gjorts på lämpligt sätt kan en administratör använda What If-verktyget för att efterlikna åtkomst, från en plats som ska tillåtas och från en plats som ska nekas.

[![What If verktyget visar resultat med blockera åtkomst](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png#lightbox)

I den här instansen skulle användaren blockeras från att komma åt en molnbaserad app i sin resa till Nord Korea eftersom contoso har blockerat åtkomst från den platsen.

Det här testet kan utökas för att omfatta andra data punkter för att begränsa omfattningen.

## <a name="next-steps"></a>Nästa steg

* [Vad är villkorsstyrd åtkomst?](overview.md)
* [Vad är Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)
* [Vad är en enhetsidentitet?](../devices/overview.md)
* [Så här fungerar det: Azure AD Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)