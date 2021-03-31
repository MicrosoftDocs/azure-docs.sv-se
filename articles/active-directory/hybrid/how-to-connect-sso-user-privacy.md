---
title: Användar sekretess och sömlös Azure AD-Sign-On | Microsoft Docs
description: Den här artikeln behandlar Azure Active Directory (Azure AD) sömlös SSO-och GDPR-kompatibilitet.
services: active-directory
keywords: Vad är Azure AD Connect, GDPR, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aa1a30c548ef60cd9b596031f4115297dd20844
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89278606"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Användarsekretess och sömlös enkel inloggning för Azure AD

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Översikt


Azure AD sömlös SSO skapar följande loggtyp, som kan innehålla personliga data: 

- Azure AD Connect spåra loggfiler.

Förbättra användar sekretessen för sömlös enkel inloggning på två sätt:

1.  På begäran extraherar du data för en person och tar bort data från den personen från installationerna.
2.  Se till att inga data behålls efter 48 timmar.

Vi rekommenderar starkt det andra alternativet eftersom det är enklare att implementera och underhålla. Se följande instruktioner för varje loggtyp:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Ta bort Azure AD Connect spåra loggfiler

Kontrol lera innehållet i **%programdata%\AADConnect** -mappen och ta bort spårnings logg innehållet (**trace- \* . log-** filer) för den här mappen inom 48 timmar efter att du har installerat eller uppgraderat Azure AD Connect eller modifierar sömlös SSO-konfiguration, eftersom den här åtgärden kan skapa data som omfattas av GDPR.

>[!IMPORTANT]
>Ta inte bort **PersistedState.xml** filen i den här mappen eftersom den här filen används för att underhålla den tidigare installationen av Azure AD Connect och används när en uppgraderings installation görs. Den här filen kommer aldrig att innehålla data om en person och ska aldrig tas bort.

Du kan antingen granska och ta bort dessa spårnings loggar med hjälp av Utforskaren i Windows, eller så kan du använda följande PowerShell-skript för att utföra nödvändiga åtgärder:

```powershell
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Spara skriptet i en fil med ". PS1 "Extension. Kör det här skriptet efter behov.

Mer information om relaterade Azure AD Connect GDPR-krav finns i [den här artikeln](reference-connect-user-privacy.md).

### <a name="note-about-domain-controller-logs"></a>Observera om domänkontrollanter loggar

Om gransknings loggning har Aktiver ATS kan den här produkten generera säkerhets loggar för dina domänkontrollanter. Läs den här [artikeln](/previous-versions/tn-archive/dd277403(v=technet.10))om du vill veta mer om hur du konfigurerar gransknings principer.

## <a name="next-steps"></a>Nästa steg

* [Läs sekretess policyn för Microsoft på säkerhets Center](https://www.microsoft.com/trustcenter)
  - [**Felsök**](tshoot-connect-sso.md) – lär dig hur du löser vanliga problem med funktionen.
  - [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för att arkivera nya funktions begär Anden.