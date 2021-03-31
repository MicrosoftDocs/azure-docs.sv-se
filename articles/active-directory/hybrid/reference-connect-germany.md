---
title: Azure AD Connect i Microsoft Cloud Tyskland
description: Azure AD Connect integrerar dina lokala kataloger med Azure Active Directory. På så sätt kan du tillhandahålla en gemensam identitet för Microsoft 365-, Azure-och SaaS-program som är integrerade med Azure AD.
keywords: introduktion till Azure AD Connect, översikt över Azure AD Connect, vad är Azure AD Connect, installera Active Directory, Tyskland, Schwarzwald
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 246f8326f7112825de98ee824cf4bab7952a7878
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90014595"
---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Azure AD Connect i Microsoft Cloud Tyskland –offentlig förhandsversion
## <a name="introduction"></a>Introduktion
Azure AD Connect tillhandahåller synkronisering mellan din lokala Active Directory och Azure Active Directory.
För närvarande måste många scenarier i [Microsoft Cloud Tyskland](https://azure.microsoft.com/global-infrastructure/germany/
) utföras av operatören. När du använder Microsoft Cloud Tyskland måste du vara medveten om följande information:

* Följande URL: er måste vara öppna på en proxyserver för att synkroniseringen ska lyckas:
  
  * *.microsoftonline.de
  * *.windows.net
  * * Listor över återkallade certifikat
* När du loggar in på din Azure AD-katalog måste du använda ett konto i domänen onmicrosoft.de.

 
## <a name="download"></a>Ladda ned
Du kan hämta Azure AD Connect från Azure AD Connect-bladet på portalen.  Följ anvisningarna nedan för att hitta Azure AD Connect-bladet.

### <a name="the-azure-ad-connect-blade"></a>Azure AD Connect-bladet
När du har loggat in på Azure-portalen:

1. Gå till Bläddra
2. Välj Azure Active Directory
3. Välj sedan Azure AD Connect

Följande information visas:

![Azure AD Connect-bladet](./media/reference-connect-germany/germany1.png)

I följande tabell beskrivs de funktioner som visas på bladet.

| Rubrik | Beskrivning |
| --- | --- |
| SYNKRONISERINGSSTATUS |Visar om synkronisering är aktiverat eller inaktiverat. |
| SENASTE SYNKRONISERING |Senast tidpunkten då en lyckad synkronisering slutfördes. |
| FEDERERADE DOMÄNER |Visar antalet federerade domäner som är konfigurerade för tillfället. |

## <a name="installation"></a>Installation
Du kan använda dokumentationen [här](how-to-connect-install-roadmap.md) för att installera Azure AD Connect.

## <a name="advanced-features-and-additional-information"></a>Avancerade funktioner och ytterligare information
Ytterligare information om anpassade inställningar och avancerade konfigurationer finns i [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md). Den här sidan innehåller information och länkar till ytterligare vägledning.

