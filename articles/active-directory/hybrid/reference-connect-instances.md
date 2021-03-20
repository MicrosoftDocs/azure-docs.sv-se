---
title: 'Azure AD Connect: Sync service instances | Microsoft Docs'
description: Den här sidan dokumenterar särskilda överväganden för Azure AD-instanser.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c4d8b0a33763a967550453d8a205258f7583084
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90015270"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: särskilda överväganden för instanser
Azure AD Connect används oftast med den världs omfattande instansen av Azure AD och Microsoft 365. Men det finns också andra instanser och de har olika krav för URL: er och andra särskilda överväganden.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Tyskland
[Microsoft Cloud Tyskland](https://www.microsoft.de/cloud-deutschland) är ett suveränt moln som drivs av en tysk data förvaltare.

| URL: er som ska öppnas i proxyservern |
| --- |
| \*. microsoftonline.de |
| \*.windows.net |
| + Listor över återkallade certifikat |

När du loggar in på Azure AD-klienten måste du använda ett konto i onmicrosoft.de-domänen.

Funktioner som för närvarande inte finns i Microsoft Cloud Tyskland:

* **Tillbakaskrivning av lösen ord** är tillgänglig för för hands version med Azure AD Connect version 1.1.570.0 och senare.
* Andra Azure AD Premium-tjänster är inte tillgängliga.

## <a name="microsoft-azure-government"></a>Microsoft Azure Government
[Microsoft Azure Government molnet](https://azure.microsoft.com/features/gov/) är ett moln för amerikanska myndigheter.

Det här molnet har stöd av tidigare versioner av DirSync. Från build-1.1.180 av Azure AD Connect stöds nästa generation av molnet. Den här generationen använder endast amerikanska-baserade slut punkter och har en annan lista med URL: er som kan öppnas i proxyservern.

| URL: er som ska öppnas i proxyservern |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*. windows.net (krävs för automatisk identifiering av Azure Government klient) |
| \*. gov.us.microsoftonline.com |
| + Listor över återkallade certifikat |

> [!NOTE]
> Från och med Azure AD Connect version 1.1.647.0 krävs inte längre att ange AzureInstance-värdet i registret förutsatt att *. windows.net är öppen på dina proxyservrar. För kunder som inte tillåter Internet anslutning från sina Azure AD Connect-servrar kan du dock använda följande manuella konfiguration.

### <a name="manual-configuration"></a>Manuell konfiguration

Följande manuella konfigurations steg används för att säkerställa att Azure AD Connect använder Azure Government synkronisering av slut punkter.

1. Starta installationen av Azure AD Connect.
2. När du ser den första sidan där du ska godkänna licens avtalet ska du inte fortsätta utan att låta installations guiden köras.
3. Starta regedit och ändra register nyckeln `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` till värdet `4` .
4. Gå tillbaka till installations guiden för Azure AD Connect, Godkänn licens avtalet och fortsätt. Under installationen ser du till att använda installations Sök vägen för **Anpassad konfiguration** (och inte Express installation) och fortsätter sedan installationen som vanligt.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
