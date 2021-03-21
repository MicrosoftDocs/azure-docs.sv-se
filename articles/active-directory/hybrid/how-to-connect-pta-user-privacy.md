---
title: Användar sekretess och Azure Active Directory direktautentisering | Microsoft Docs
description: Den här artikeln behandlar Azure Active Directory (Azure AD) genom strömnings-och GDPR-kompatibilitet.
services: active-directory
keywords: Azure AD Connect direktautentisering, GDPR, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 396344ba90aa3850d7d23dc40d6df95f6d1f6c3f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996584"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Användarsekretess och Azure Active Directory-direktautentisering


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Översikt

Genom strömnings autentisering i Azure AD skapas följande loggtyp, som kan innehålla personliga data:

- Azure AD Connect spåra loggfiler.
- Spåra loggfiler för Authentication agent.
- Windows-händelseloggar.

Förbättra användar sekretessen för direktautentisering på två sätt:

1.  På begäran extraherar du data för en person och tar bort data från den personen från installationerna.
2.  Se till att inga data behålls efter 48 timmar.

Vi rekommenderar starkt det andra alternativet eftersom det är enklare att implementera och underhålla. Nedan följer anvisningar för varje loggtyp:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Ta bort Azure AD Connect spåra loggfiler

Kontrol lera innehållet i **%programdata%\AADConnect** -mappen och ta bort spårnings logg innehållet (**spår- \* . loggfiler** ) för den här mappen inom 48 timmar efter installation eller uppgradering av Azure AD Connect eller ändra konfigurationen för vidarekoppling, eftersom den här åtgärden kan skapa data som omfattas av GDPR.

>[!IMPORTANT]
>Ta inte bort **PersistedState.xml** filen i den här mappen eftersom den här filen används för att underhålla den tidigare installationen av Azure AD Connect och används när en uppgraderings installation görs. Den här filen kommer aldrig att innehålla data om en person och ska aldrig tas bort.

Du kan antingen granska och ta bort dessa spårnings loggar med hjälp av Utforskaren i Windows, eller så kan du använda följande PowerShell-skript för att utföra nödvändiga åtgärder:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Spara skriptet i en fil med ". PS1 "Extension. Kör det här skriptet efter behov.

Mer information om relaterade Azure AD Connect GDPR-krav finns i [den här artikeln](reference-connect-user-privacy.md).

### <a name="delete-authentication-agent-event-logs"></a>Ta bort händelse loggar för autentiserings agent

Den här produkten kan också skapa **händelse loggar i Windows**. Läs [den här artikeln](/windows/win32/wes/windows-event-log)om du vill veta mer.

Om du vill visa loggar som är relaterade till genom strömnings agenten öppnar du **Loggboken** programmet på-servern och kontrollerar under **program-och tjänst Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

### <a name="delete-authentication-agent-trace-log-files"></a>Ta bort loggfiler för autentiseringsprinciper

Du bör regelbundet kontrol lera innehållet i **%programdata%\MICROSOFT\AZURE AD Connect Authentication Agent\Trace** och ta bort innehållet i den här mappen var 48: e timme. 

>[!IMPORTANT]
>Om tjänsten Authentication Agent körs kan du inte ta bort den aktuella logg filen i mappen. Stoppa tjänsten innan du försöker igen. För att undvika användar inloggnings problem bör du redan ha konfigurerat direktautentisering för [hög tillgänglighet](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

Du kan antingen granska och ta bort dessa filer med hjälp av Utforskaren i Windows, eller så kan du använda följande skript för att utföra de åtgärder som krävs:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Följ dessa steg om du vill schemalägga att skriptet ska köras var 48: e timme:

1.  Spara skriptet i en fil med ". PS1 "Extension.
2.  Öppna **kontroll panelen** och klicka på **system och säkerhet**.
3.  Klicka på **Schemalägg aktiviteter** under **administrations verktyg** .
4.  I **Schemaläggaren** högerklickar du på "**aktivitets schema bibliotek**" och klickar på "**skapa grundläggande aktivitet...**".
5.  Ange namnet på den nya aktiviteten och klicka på **Nästa**.
6.  Välj "**dagligen**" för **aktivitets utlösaren** och klicka på **Nästa**.
7.  Ställ in upprepningen på två dagar och klicka på **Nästa**.
8.  Välj "**starta ett program**" som åtgärd och klicka på **Nästa**.
9.  Skriv "**PowerShell**" i rutan för programmet/skriptet och i rutan "**Lägg till argument (valfritt)**", ange den fullständiga sökvägen till skriptet som du skapade tidigare och klicka sedan på **Nästa**.
10. Nästa skärm visar en sammanfattning av den uppgift som du håller på att skapa. Verifiera värdena och klicka på **Slutför** för att skapa uppgiften:
 
### <a name="note-about-domain-controller-logs"></a>Observera om domänkontrollanter loggar

Om gransknings loggning har Aktiver ATS kan den här produkten generera säkerhets loggar för dina domänkontrollanter. Läs den här [artikeln](/previous-versions/tn-archive/dd277403(v=technet.10))om du vill veta mer om hur du konfigurerar gransknings principer.

## <a name="next-steps"></a>Nästa steg
* [Läs sekretess policyn för Microsoft på säkerhets Center](https://www.microsoft.com/trustcenter)
* [**Felsök**](tshoot-connect-pass-through-authentication.md) – lär dig hur du löser vanliga problem med funktionen.