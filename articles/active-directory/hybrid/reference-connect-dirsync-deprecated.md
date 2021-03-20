---
title: Uppgradera från DirSync och Azure AD Sync | Microsoft Docs
description: Beskriver hur du uppgraderar från DirSync och Azure AD Sync till Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: bd68fb88-110b-4d76-978a-233e15590803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53d9e2ea10aefc96c2d4d72ad0020704ba48709b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539424"
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Uppgradera Windows Azure Active Directory Sync och Azure Active Directory Sync
Azure AD Connect är det bästa sättet att ansluta din lokala katalog med Azure AD och Microsoft 365. Det här är ett bra tillfälle att uppgradera till Azure AD Connect från Windows Azure Active Directory Sync (DirSync) eller Azure AD Sync (AADSync) eftersom dessa verktyg nu är föråldrade och inte längre stöds från och med 13 april 2017.

De två verktygen för synkronisering av identiteter som är föråldrade erbjöds för enskilda skogs kunder (DirSync) och för flera skogar och andra avancerade kunder (Azure AD Sync). Dessa äldre verktyg har ersatts med en enda lösning som är tillgänglig för alla scenarier: Azure AD Connect. Den erbjuder nya funktioner, funktions förbättringar och stöd för nya scenarier. För att kunna fortsätta att synkronisera dina lokala identitets data till Azure AD och Microsoft 365 rekommenderar vi starkt att du uppgraderar till Azure AD Connect. Microsoft garanterar inte att dessa äldre versioner fungerar efter den 31 december 2017.

Den senaste versionen av DirSync släpptes i juli 2014 och den senaste versionen av Azure AD Sync släpptes i maj 2015.

## <a name="what-is-azure-ad-connect"></a>Vad är Azure AD Connect?
Azure AD Connect är efterföljande till DirSync och Azure AD Sync. Det kombinerar alla scenarier som stöds av dessa två. Du kan läsa mer om det i [integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).

## <a name="deprecation-schedule"></a>Schema för utfasning
| Datum | Kommentar |
| --- | --- |
| 13 april 2016 |Windows Azure Active Directory Sync ("DirSync") och Microsoft Azure Active Directory Sync ("Azure AD Sync") annonseras som föråldrade. |
| 13 april 2017 |Support upphör. Kunderna kommer inte längre att kunna öppna ett support ärende utan att uppgradera till Azure AD Connect först. |
|31 december 2017|Azure AD kan inte längre acceptera kommunikation från Windows Azure Active Directory Sync ("DirSync") och Microsoft Azure Active Directory Sync ("Azure AD Sync").
|1 april, 2021| Windows Azure Active Directory Sync ("DirSync") och Microsoft Azure Active Directory Sync ("Azure AD Sync") fungerar inte längre |

## <a name="how-to-transition-to-azure-ad-connect"></a>Hur du övergår till Azure AD Connect
Om du kör DirSync finns det två sätt att uppgradera: uppgradering på plats och parallell distribution. En uppgradering på plats rekommenderas för de flesta kunder och om du har ett nyare operativ system och färre än 50 000 objekt. I andra fall rekommenderar vi att du utför en parallell distribution där DirSync-konfigurationen flyttas till en ny server som kör Azure AD Connect.

| Lösning | Scenario |
| --- | --- |
| [Uppgradera från DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Om du redan kör en befintlig DirSync-Server.</li> |
| [Uppgradera från Azure AD Sync](how-to-upgrade-previous-version.md) |<li>Om du flyttar från Azure AD Sync.</li> |

Om du vill se hur du gör en uppgradering på plats från DirSync till Azure AD Connect, kan du läsa denna kanal 9-video:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>Vanliga frågor
**F: Jag har fått ett e-postmeddelande från Azure-teamet och/eller ett meddelande från Microsoft 365 meddelande Center, men jag använder Connect.**  
Meddelandet skickades även till kunder som använder Azure AD Connect med ett build-nummer 1,0. \* . 0 (med en tidigare version än 1,1). Microsoft rekommenderar att kunderna hålls uppdaterade med Azure AD Connect-versioner. Funktionen [automatisk uppgradering](how-to-connect-install-automatic-upgrade.md) som introducerades i 1,1 gör det enkelt att alltid ha en nyare version av Azure AD Connect installerad.

**F: kommer DirSync/Azure AD Sync sluta fungera den 13 april 2017?**  
DirSync/Azure AD Sync fortsätter att fungera den 13 april 2017.  Azure AD kan dock inte längre acceptera kommunikation från DirSync/Azure AD Sync efter 31 december 2017. DirSync och Azure AD Sync kommer inte längre att fungera efter den 1 april 2021

**F: vilka DirSync-versioner kan jag uppgradera från?**  
Det finns stöd för att uppgradera från alla DirSync-utgåvor som används för närvarande. 

**F: vad gäller Azure AD Connector för FIM/MIM?**  
Azure AD-anslutaren för FIM/MIM har **inte** annonser ATS som inaktuell. **Funktionen är låst**; inga nya funktioner läggs till och den får inga fel korrigeringar. Microsoft rekommenderar kunder som använder den för att planera att flytta från den till Azure AD Connect. Vi rekommenderar starkt att inte starta nya distributioner med hjälp av det. Den här kopplingen meddelas längre fram i framtiden.

## <a name="additional-resources"></a>Ytterligare resurser
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
