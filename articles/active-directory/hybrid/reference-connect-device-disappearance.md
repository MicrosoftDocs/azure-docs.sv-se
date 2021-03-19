---
title: Förstå Azure AD Connect 1.4. xx. x och enhetens avvisade | Microsoft Docs
description: Det här dokumentet beskriver ett problem som uppstår i version 1.4. xx. x Azure AD Connect
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bc159452c81a673ca4a7ed46aa7eff19fd9209eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "73176022"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Förstå Azure AD Connect 1.4. xx. x och enheten försvinner
Med version 1.4. xx. x Azure AD Connect kan vissa kunder se vissa eller alla sina Windows-enheter försvinner från Azure AD. Detta är inte en orsak till betänkligheter eftersom dessa enhets identiteter inte används av Azure AD under auktoriseringen för villkorlig åtkomst. Den här ändringen tar inte bort några Windows-enheter som har registrerats korrekt med Azure AD för Hybrid Azure AD-anslutning.

Om du ser borttagningen av enhets objekt i Azure AD som överskrider tröskelvärdet för export borttagning, rekommenderar vi att kunden tillåter att borttagningarna går igenom. [Gör så här: Tillåt borttagningar som flödar när de överstiger tröskelvärdet för borttagning](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Bakgrund
Windows-enheter som har registrerats som hybrid Azure AD-anslutna representeras i Azure AD som enhets objekt. Dessa enhets objekt kan användas för villkorlig åtkomst. Windows 10-enheter synkroniseras till molnet via Azure AD Connect och Windows-enheter på nivån i nivå registreras direkt med antingen AD FS eller sömlös enkel inloggning.

## <a name="windows-10-devices"></a>Windows 10-enheter
Endast Windows 10-enheter med ett speciellt userCertificate som kon figurer ATS av hybrid Azure AD Join kommer att synkroniseras med molnet genom att Azure AD Connect. I tidigare versioner av Azure AD Connect det här kravet inte rigoröst tvingande, vilket resulterar i onödiga enhets objekt i Azure AD. Sådana enheter i Azure AD är alltid i vänte läge eftersom enheterna inte var avsedda att registreras med Azure AD. 

Den här versionen av Azure AD Connect synkroniserar bara Windows 10-enheter som är korrekt konfigurerade för att vara hybrid Azure AD-anslutna. Windows 10 enhets objekt utan Azure AD Join-userCertificate kommer att tas bort från Azure AD.

## <a name="down-level-windows-devices"></a>Down-Level Windows-enheter
Azure AD Connect bör aldrig synkronisera [Windows-enheter på detalj nivå](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices). Alla enheter i Azure AD som synkroniserades tidigare kommer nu att tas bort från Azure AD. Om Azure AD Connect försöker ta bort [Windows-enheter på äldre nivå](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)är enheten inte den som skapades av [Microsoft-Workplace Join för MSI-versioner för icke-Windows 10-datorer](https://www.microsoft.com/download/details.aspx?id=53554) och den kan inte användas av någon annan Azure AD-funktion.

Vissa kunder kan behöva gå med i gång till [hur man planerar din hybrid Azure Active Directory delta-implementering](../devices/hybrid-azuread-join-plan.md) för att få sina Windows-enheter registrerade korrekt och se till att sådana enheter helt kan delta i enhets villkorliga åtkomst. 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>Hur kan jag kontrol lera vilka enheter som tas bort med den här uppdateringen?

Du kan använda det här PowerShell-skriptet för att kontrol lera vilka enheter som tas bort: https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

Det här skriptet genererar en rapport om certifikat som lagras i Active Directory dator objekt, särskilt certifikat som utfärdats av funktionen hybrid Azure AD-anslutning.
Den kontrollerar certifikaten som finns i egenskapen UserCertificate för ett dator objekt i AD och kontrollerar om certifikatet har utfärdats för funktionen hybrid Azure AD Join (t. ex. ämnes namnet matchar CN = {ObjectGUID}).
Innan Azure AD Connect synkroniseras till Azure AD med en dator som innehåller minst ett giltigt certifikat, men som börjar på Azure AD Connect version 1,4, kan Synkroniseringsmotorn identifiera hybrid Azure AD Join-certifikat och kommer att "cloudfilter" datorobjektet från att synkronisera till Azure AD om det inte finns ett giltigt hybrid Azure AD Join-certifikat.
Azure AD-enheter som redan har synkroniserats till AD men inte har ett giltigt hybrid Azure AD Join-certifikat kommer att tas bort (CloudFiltered = TRUE) av Synkroniseringsmotorn.

## <a name="next-steps"></a>Nästa steg
- [Azure AD Connect versions historik](reference-connect-version-history.md)
