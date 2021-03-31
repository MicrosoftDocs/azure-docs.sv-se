---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 2b28cc742bb981e0db535d39cb0c7fbf027470ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "83673772"
---
### <a name="identity-tier"></a>Identitets nivå 

Med Microsoft-Oracle-partnerskapet kan du konfigurera en enhetlig identitet i Azure, OCI och Oracle-programmet. För JD Edwards EnterpriseOne-eller fjärrinstallationsserver-programsviten krävs en instans av Oracle HTTP-servern (OHS) för att konfigurera enkel inloggning mellan Azure AD och Oracle IDCS.

OHS fungerar som en omvänd proxy till program nivån, vilket innebär att alla begär anden till slut programmen går igenom den. Oracle Access Manager webgrind är ett OHS webb server-plugin-program som fångar varje begäran till slut programmet. Om en resurs som nås är skyddad (kräver en autentiserad session) initierar webgrind OIDC-autentiseringsschemat med identitets moln tjänsten via användarens webbläsare. Mer information om de flöden som stöds av OpenID Connect-webgrind finns i [dokumentationen för Oracle Access Manager](https://docs.oracle.com/cd/E52734_01/oam/AIAAG/GUID-1E927D1B-FB83-425B-8768-85DB441821A4.htm#AIAAG7327).

Med den här installationen kan en användare som redan är inloggad i Azure AD navigera till JD Edwards EnterpriseOne eller ett-program utan att logga in igen via Oracle Identity Cloud service. Kunder som distribuerar den här lösningen får fördelarna med enkel inloggning, inklusive en enda uppsättning autentiseringsuppgifter, en förbättrad inloggnings upplevelse, förbättrad säkerhet och minskad support kostnad.

Om du vill veta mer om hur du konfigurerar enkel inloggning för JD Edwards EnterpriseOne eller den andra sidan med Azure AD, se tillhör ande [Oracle-whitepaper](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf).