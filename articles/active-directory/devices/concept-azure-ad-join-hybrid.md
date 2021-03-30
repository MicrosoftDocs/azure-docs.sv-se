---
title: Vad är en hybrid Azure AD-ansluten enhet?
description: Lär dig hur enhets identitets hantering kan hjälpa dig att hantera enheter som har åtkomst till resurser i din miljö.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 259a1324c412dad40d32a8b8e026d84e6f5aa066
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85554915"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Hybrid Azure AD-anslutna enheter

I över ett årtionde har många organisationer använt domänanslutningen för att ansluta till sin lokala Active Directory för att göra det möjligt för:

- IT-avdelningar att hantera företagsägda enheter från en central plats.
- Användare att logga in på sina enheter med sina arbets- eller skolkonton i Active Directory.

Organisationer med lokal storlek förlitar sig vanligt vis på avbildnings metoder för att etablera enheter och de använder ofta **Configuration Manager** eller **grup principer** för att hantera dem.

Om din miljö har ett lokalt AD-fotavtryck och du vill dra nytta av funktionerna i Azure Active Directory kan du implementera Hybrid Azure AD-anslutna enheter. Dessa enheter är enheter som är anslutna till din lokala Active Directory och registrerade med din Azure Active Directory.

| Hybrid Azure AD-anslutning | Beskrivning |
| --- | --- |
| **Definition** | Ansluten till en lokal AD och Azure AD som kräver organisations konto för att logga in på enheten |
| **Primär mål grupp** | Lämpligt för Hybrid organisationer med befintlig lokal AD-infrastruktur |
|   | Gäller för alla användare i en organisation |
| **Äganderätt till enhet** | Organisation |
| **Operativ system** | Windows 10, 8,1 och 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 och 2019 |
| **Etablering** | Windows 10, Windows Server 2016/2019 |
|   | Domän anslutning och autokoppling via Azure AD Connect eller ADFS-konfiguration |
|   | Domän anslutning av Windows autopilot och Autojoin via Azure AD Connect eller ADFS-konfiguration |
|   | Windows 8,1, Windows 7, Windows Server 2012 R2, Windows Server 2012 och Windows Server 2008 R2-Kräv MSI |
| **Alternativ för enhets inloggning** | Organisations konton med: |
|   | Lösenord |
|   | Windows Hello för företag för Win10 |
| **Enhetshantering** | Grupprincip |
|   | Configuration Manager fristående eller samtidig hantering med Microsoft Intune |
| **De viktigaste funktionerna** | Enkel inloggning till både moln resurser och lokala resurser |
|   | Villkorlig åtkomst genom domän anslutning eller via Intune om samhanterad |
|   | Återställning av lösen ord för självbetjäning och Windows Hello-PIN-återställning på Lås skärmen |
|   | Enterprise State Roaming över enheter |

![Hybrid Azure AD-anslutna enheter](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Scenarier

Använd Azure AD hybrid-anslutna enheter om:

- Du har Win32-appar som är distribuerade till enheterna som förlitar sig på Active Directory-autentisering för datorn.
- Du vill fortsätta att använda grupprincip för att hantera enhets konfigurationen.
- Du vill fortsätta att använda befintliga avbildnings lösningar för att distribuera och konfigurera enheter.
- Du måste ha stöd för äldre Windows 7-och 8,1-enheter utöver Windows 10

## <a name="next-steps"></a>Nästa steg

- [Planera implementering av Azure AD-anslutningshybriden](hybrid-azuread-join-plan.md)
- [Hantera enhetsidentiteter med hjälp av Azure-portalen](device-management-azure-portal.md)
- [Hantera inaktuella enheter i Azure AD](manage-stale-devices.md)
