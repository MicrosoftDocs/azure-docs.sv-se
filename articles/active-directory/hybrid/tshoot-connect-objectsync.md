---
title: 'Azure AD Connect: Felsöka synkronisering av objekt | Microsoft Docs'
description: Det här avsnittet innehåller anvisningar för hur du felsöker problem med synkronisering av objekt med hjälp av fel söknings aktiviteten.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73d4239dd34f2a64aa7b3edbf88bad4348e01291
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85356210"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Felsöka objektsynkronisering med Azure AD Connect-synkronisering
Den här artikeln innehåller steg för fel sökning av problem med synkronisering av objekt med hjälp av fel söknings aktiviteten. Titta på [den här korta videon](https://aka.ms/AADCTSVideo)om du vill se hur fel sökning fungerar i Azure Active Directory (Azure AD) Connect.

## <a name="troubleshooting-task"></a>Felsöka aktivitet
För Azure AD Connect-distribution med version 1.1.749.0 eller högreanvänder du felsökningsaktiviteten i guiden  för att felsöka problem med synkronisering av objekt. För tidigare versioner felsöker du manuellt enligt beskrivningen [här](tshoot-connect-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Köra felsökningsuppgiften i guiden
Kör fel söknings aktiviteten i guiden genom att utföra följande steg:

1.  Öppna en ny Windows PowerShell-session på din Azure AD Connect-Server med alternativet Kör som administratör.
2.  Kör `Set-ExecutionPolicy RemoteSigned` eller `Set-ExecutionPolicy Unrestricted` .
3.  Starta guiden Azure AD Connect.
4.  Gå till sidan Ytterligare aktiviteter, Välj Felsök och klicka på Nästa.
5.  På sidan fel sökning klickar du på Starta för att starta fel söknings menyn i PowerShell.
6.  I huvud menyn väljer du Felsök synkronisering av objekt.
![Felsöka synkronisering av objekt](media/tshoot-connect-objectsync/objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Felsöka indataparametrar
Följande indataparametrar krävs av fel söknings aktiviteten:
1.  **Unikt objekt namn** – det här är det unika namnet på det objekt som behöver fel sökning
2.  **Namn på AD-koppling** – det här är namnet på den AD-skog där ovanstående objekt finns.
3.  Autentiseringsuppgifter för global administratör för Azure AD-klientens globala administratör ![](media/tshoot-connect-objectsync/objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Förstå resultaten av fel söknings aktiviteten
Fel söknings aktiviteten utför följande kontroller:

1.  Identifiera UPN-matchningsfel om objektet synkroniseras till Azure Active Directory
2.  Kontrol lera om objektet är filtrerat på grund av domän filtrering
3.  Kontrol lera om objektet är filtrerat på grund av ORGANISATIONSENHETs filtrering
4.  Kontrol lera om synkronisering av objekt blockeras på grund av en länkad post låda
5. Kontrol lera om objektet är en dynamisk distributions grupp som inte ska synkroniseras

I resten av det här avsnittet beskrivs vissa resultat som returneras av uppgiften. I varje fall tillhandahåller aktiviteten en analys som följs av rekommenderade åtgärder för att lösa problemet.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Identifiera UPN-matchningsfel om objektet synkroniseras till Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>UPN-suffixet verifieras inte med Azure AD-klienten
När UserPrincipalName (UPN)/Alternate inloggnings-ID-suffixet inte har verifierats med Azure AD-klienten, ersätter Azure Active Directory UPN-suffixen med standard domän namnet "onmicrosoft.com".

![Azure AD ersätter UPN](media/tshoot-connect-objectsync/objsynch2.png)

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Azure AD-klientens DirSync-funktion ' SynchronizeUpnForManagedUsers ' är inaktive rad
När Azure AD-klienten DirSync-funktionen SynchronizeUpnForManagedUsers är inaktive rad tillåter Azure Active Directory inte synkronisering av uppdateringar till UserPrincipalName/alternativt inloggnings-ID för licensierade användar konton med hanterad autentisering.

![SynchronizeUpnForManagedUsers](media/tshoot-connect-objectsync/objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Objektet är filtrerat på grund av domän filtrering
### <a name="domain-is-not-configured-to-sync"></a>Domänen är inte konfigurerad för synkronisering
Objektet ligger utanför omfånget på grund av att domänen inte har kon figurer ATS. I exemplet nedan är objektet utanför Sync-omfånget som den domän som det tillhör är filtrerat från synkronisering.

![Domänen är inte konfigurerad för synkronisering](media/tshoot-connect-objectsync/objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Domänen har kon figurer ATS för synkronisering men saknar körnings profiler/körnings steg
Objektet ligger utanför omfånget eftersom domänen saknar körnings profiler/körnings steg. I exemplet nedan är objektet utanför Sync-omfånget som den domän som det tillhör saknar körnings steg för den fullständiga import körnings profilen.
![körnings profiler saknas](media/tshoot-connect-objectsync/objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>Objektet är filtrerat på grund av ORGANISATIONSENHETs filtrering
Objektet är utanför Sync-omfånget på grund av konfigurationen av ORGANISATIONSENHETs filtrering. I exemplet nedan tillhör objektet OU = nosync, DC = bvtadwbackdc, DC = com.  ORGANISATIONSENHETen ingår inte i Sync-omfånget.</br>

![OU](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>Problem med länkad post låda
En länkad post låda ska vara kopplad till ett externt huvud konto som finns i en annan betrodd konto skog. Om det inte finns något sådant externt huvud konto kommer Azure AD Connect inte att synkronisera användar kontot motsvarar den länkade post lådan i Exchange-skogen till Azure AD-klienten.</br>
![Länkad post låda](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Problem med dynamisk distributions grupp
På grund av olika skillnader mellan lokala Active Directory och Azure Active Directory, synkroniserar Azure AD Connect inte dynamiska distributions grupper till Azure AD-klienten.

![Dynamisk distributions grupp](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>HTML-rapport
Förutom att analysera objektet genererar fel söknings aktiviteten även en HTML-rapport som har allt känt om objektet. Den här HTML-rapporten kan delas med support teamet för ytterligare fel sökning, om det behövs.

![HTML-rapport](media/tshoot-connect-objectsync/objsynch8.png)

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
