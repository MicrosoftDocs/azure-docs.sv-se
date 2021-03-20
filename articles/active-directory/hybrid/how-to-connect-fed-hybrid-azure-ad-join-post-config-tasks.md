---
title: 'Azure AD Connect: hybrid Azure AD-anslutning efter konfigurations uppgifter | Microsoft Docs'
description: Den här dokument informationen innehåller konfigurations uppgifter som krävs för att slutföra hybrid Azure AD-anslutningen
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da5cefbacbd3851d2609a687c1948d9bcba5ffae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88612477"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Uppgifter efter konfiguration för Hybrid Azure AD-anslutning

När du har kört Azure AD Connect för att konfigurera din organisation för Hybrid Azure AD-anslutning finns det några ytterligare steg som du måste utföra för att slutföra installationen.  Utför bara de steg som gäller för dina enheter.

## <a name="1-configure-controlled-rollout-optional"></a>1. Konfigurera styrd distribution (valfritt)
Alla domänanslutna enheter som kör Windows 10 och Windows Server 2016 registreras automatiskt med Azure AD när alla konfigurations steg har slutförts. Om du föredrar en kontrollerad distribution i stället för den här automatiska registreringen kan du använda grup principer för att selektivt aktivera eller inaktivera automatisk distribution.  Du måste ange den här grup principen innan du startar de andra konfigurations stegen:
* Skapa ett grup princip objekt i din Active Directory.
* Ge den namnet (till exempel hybrid Azure AD Join).
* Redigera och gå till: dator konfiguration > principer > Administrativa mallar > Windows-komponenter > enhets registrering.

>[!NOTE]
>För 2012R2 finns princip inställningarna i **dator konfiguration > principer > Administrativa mallar > Windows-komponenter > Workplace Join > automatiskt ansluta klient datorer på arbets plats**

* Aktivera den här inställningen: registrera domänanslutna datorer som enheter.
* Verkställ och klicka på OK.
* Länka GRUPPRINCIPOBJEKTet till valfri plats (organisationsenhet, säkerhets grupp eller domän för alla enheter).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Konfigurera nätverk med slut punkter för enhets registrering
Kontrol lera att följande URL: er är tillgängliga från datorer i ditt organisations nätverk för registrering till Azure AD:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com` 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. implementera WPAD för Windows 10-enheter
Om din organisation har åtkomst till Internet via en utgående proxy implementerar du Web Proxy Auto-Discovery (WPAD) för att aktivera att Windows 10-datorer registreras i Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Konfigurera SCP i alla skogar som inte har kon figurer ATS av Azure AD Connect 

Tjänst anslutnings punkten (SCP) innehåller din Azure AD-klient information som används av enheterna för automatisk registrering.  Kör PowerShell-skriptet ConfigureSCP.ps1, som du laddade ned från Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Konfigurera en Federations tjänst som inte har kon figurer ATS av Azure AD Connect

Om din organisation använder en Federations tjänst för att logga in på Azure AD, måste anspråks reglerna i ditt Azure AD-förtroende för förlitande part tillåta enhetsautentisering. Om du använder Federation med AD FS går du till [AD FS hjälp](https://aka.ms/aadrptclaimrules) för att generera anspråks reglerna. Om du använder en Federations lösning som inte kommer från Microsoft kontaktar du leverantören för vägledning.  

>[!NOTE]
>Om du har Windows-enheter med äldre versioner måste tjänsten stödja utfärdande av AuthenticationMethod-och wiaormultiauthn-anspråk när du tar emot förfrågningar till Azure AD-förtroendet. I AD FS bör du lägga till en regel för omvandling av utfärdande som passerar genom autentiseringsmetoden.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Aktivera Azure AD sömlös SSO för Windows-enheter på äldre nivå

Om din organisation använder hash-synkronisering av lösen ord eller direktautentisering för att logga in på Azure AD, aktiverar du Azure AD sömlös SSO med denna inloggnings metod för att autentisera Windows-enheter på den äldre nivån:  https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso . 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Ange Azure AD-princip för Windows-enheter på äldre nivå

För att registrera Windows-enheter på en äldre enhet måste du kontrol lera att Azure AD-principen tillåter användare att registrera enheter. 

* Logga in på ditt konto i Azure Portal.
* Gå till: Azure Active Directory > enheter > enhets inställningar
* Ange "användare kan registrera sina enheter med Azure AD" till alla.
* Klicka på Spara

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Lägg till Azure AD-slutpunkt i Windows-enheter på nivån

Lägg till Azure AD Device Authentication-slutpunkten i zonerna Lokalt intranät på dina Windows-enheter för att undvika certifikat meddelanden vid autentisering av enheterna: `https://device.login.microsoftonline.com` 

Om du använder [sömlös SSO](how-to-connect-sso.md)aktiverar du även Tillåt uppdatering av statusfältet via skriptet på den zonen och lägger till följande slut punkt: `https://autologon.microsoftazuread-sso.com` 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Installera Microsoft Workplace Join på Windows-enheter på nivån

Det här installations programmet skapar en schemalagd aktivitet på enhets systemet som körs i användarens kontext. Aktiviteten utlöses när användaren loggar in i Windows. Uppgiften ansluter tyst till enheten med Azure AD med användarautentiseringsuppgifterna efter autentisering med integrerad Windows-autentisering. Download Center finns på https://www.microsoft.com/download/details.aspx?id=53554 . 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Konfigurera grup princip för att tillåta enhets registrering

Information om hur du tillåter hybrid Azure AD-anslutning för enskilda enheter finns i [kontrollerad validering av hybrid Azure AD-anslutning](../devices/hybrid-azuread-join-control.md).

## <a name="next-steps"></a>Nästa steg
[Konfigurera tillbakaskrivning av enhet](how-to-connect-device-writeback.md)
