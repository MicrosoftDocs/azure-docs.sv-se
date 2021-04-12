---
title: 'Azure AD Connect: Vad är ADConnectivityTool PowerShell-modulen | Microsoft Docs'
description: Det här dokumentet introducerar den nya ADConnectivity PowerShell-modulen och hur den kan användas för att felsöka.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 4/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7eed3e06ab42671d9674ad3893a88dfe9817e22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95973464"
---
# <a name="troubleshoot-azure-ad-connectivity-with-the-adconnectivitytool-powershell-module"></a>Felsöka Azure AD-anslutning med ADConnectivityTool PowerShell-modulen

Verktyget ADConnectivity är en PowerShell-modul som används i något av följande:

- Under installationen när ett problem med nätverks anslutningen förhindrar en lyckad verifiering av de Active Directory autentiseringsuppgifter som användaren tillhandahöll i guiden.
- Efter installation av en användare som anropar funktioner från en PowerShell-session.

Verktyget finns i: **C:\Program Files\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool. psm1** 

## <a name="adconnectivitytool-during-installation"></a>ADConnectivityTool under installationen

På sidan **Anslut dina kataloger** i guiden Azure AD Connect, om ett nätverks problem uppstår, använder ADConnectivityTool automatiskt en av dess funktioner för att avgöra vad som händer.  Något av följande kan anses vara nätverks problem:

- Namnet på den skog som användaren angav har skrivits felaktigt eller så finns inte skogen 
- UDP-port 389 är stängd i domän kontrol Lanterna som är kopplade till den skog som användaren tillhandahöll
- Autentiseringsuppgifterna som angavs i fönstret AD-skogs konto har inte behörighet att hämta de domänkontrollanter som är associerade med mål skogen
- Alla TCP-portarna 53, 88 eller 389 stängs i domän kontrol Lanterna som är kopplade till den skog som användaren tillhandahöll 
- Både UDP 389 och en TCP-port (eller portar) är stängda
- Det gick inte att matcha DNS för den angivna skogen och/eller tillhör ande domänkontrollanter

När något av dessa problem upptäcks visas ett relaterat fel meddelande i guiden AADConnect:


![Fel](media/how-to-connect-adconnectivitytools/error1.png)

När vi till exempel försöker lägga till en katalog på skärmen **Anslut dina kataloger** måste Azure AD Connect verifiera detta och förväntar sig att kunna kommunicera med en domänkontrollant via port 389.  Om den inte gör det ser vi det fel som visas i skärm bilden ovan.  

Vad som händer i bakgrunden, är att Azure AD Connect anropar `Start-NetworkConnectivityDiagnosisTools` funktionen.  Den här funktionen anropas när verifieringen av autentiseringsuppgifter Miss lyckas på grund av ett problem med nätverks anslutningen.

Slutligen skapas en detaljerad logg fil när verktyget anropas från guiden. Loggen finns i **C:\ProgramData\AADConnect\ADConnectivityTool- \<date> - \<time> . log**

## <a name="adconnectivitytools-post-installation"></a>ADConnectivityTools efter installation
När Azure AD Connect har installerats kan du använda någon av funktionerna i PowerShell-modulen ADConnectivityTools.  

Du hittar referensinformation om funktionerna i [ADConnectivityTools-referensen](reference-connect-adconnectivitytools.md)

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

Vi kommer att kunna anropa den här funktionen eftersom den **endast** kan anropas manuellt när ADConnectivityTool. psm1 har importer ATS till PowerShell. 

Den här funktionen kör samma logik som Azure AD Connects guiden kör för att verifiera de angivna AD-autentiseringsuppgifterna.  Det ger dock en mycket mer utförlig förklaring av problemet och en föreslagen lösning. 

Anslutnings verifieringen består av följande steg:
-   Hämta domän-FQDN (fullständigt kvalificerat domän namn)-objekt
-   Verifiera att om användaren har valt "Skapa nytt AD-konto" tillhör dessa autentiseringsuppgifter gruppen företags administratörer
-   Hämta FQDN-objekt för skog
-   Bekräfta att minst en domän som är kopplad till det tidigare hämtade domän namnet för skogen kan kommas åt
-   Kontrol lera att skogens funktions nivå är Windows Server 2003 eller senare.

Användaren kommer att kunna lägga till en katalog om alla dessa åtgärder har genomförts.

Om användaren kör den här funktionen när ett problem har lösts (eller om det inte finns något problem alls), kommer utdata att visas för användaren att gå tillbaka till Azure AD Connect guiden och försöka infoga autentiseringsuppgifterna igen.



## <a name="next-steps"></a>Nästa steg
- [Azure AD Connect: Konton och behörigheter](reference-connect-accounts-permissions.md)
- [Snabb installation](how-to-connect-install-express.md)
- [Anpassad installation](how-to-connect-install-custom.md)
- [Referens för ADConnectivityTools](reference-connect-adconnectivitytools.md)

