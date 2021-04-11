---
title: Felsöka äldre hybrid Azure Active Directory anslutna enheter
description: Felsöka hybrid Azure Active Directory anslutna enheter på äldre nivå.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbf936370b401a76479c7679499103d083e0752a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551755"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Felsöka hybrid Azure Active Directory anslutna enheter med äldre versioner 

Den här artikeln gäller endast för följande enheter: 

- Windows 7 
- Windows 8,1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 

För Windows 10 eller Windows Server 2016, se [Felsöka hybrid Azure Active Directory anslutna Windows 10-och Windows server 2016-enheter](troubleshoot-hybrid-join-windows-current.md).

I den här artikeln förutsätter vi att du har [konfigurerat hybrid Azure Active Directory anslutna enheter](hybrid-azuread-join-plan.md) som stöd för följande scenarier:

- Enhetsbaserad villkorlig åtkomst

Den här artikeln innehåller fel söknings vägledning för hur du löser eventuella problem.  

**Vad du bör känna till:** 

- Hybrid Azure AD-anslutning för tidigare Windows-enheter fungerar något annorlunda än i Windows 10. Många kunder inser inte att de behöver AD FS (för federerade domäner) eller sömlös SSO-konfiguration (för hanterade domäner).
- Sömlös SSO fungerar inte i privat bläddringsläge i Firefox och Microsoft Edge-webbläsare. Det fungerar inte heller i Internet Explorer om webbläsaren körs i utökat kernelläge eller om förbättrad säkerhets konfiguration är aktive rad.
- För kunder med federerade domäner, om tjänst anslutnings punkten (SCP) har kon figurer ATS så att den pekar på det hanterade domän namnet (t. ex. contoso.onmicrosoft.com, i stället för contoso.com), fungerar inte hybrid Azure AD Join för tidigare Windows-enheter.
- Samma fysiska enhet visas flera gånger i Azure AD när flera domän användare loggar in i tidigare hybrid Azure AD-anslutna enheter.  Om t. ex. *jdoe* och *jharnett* loggar in på en enhet, skapas en separat registrering (DeviceID) för var och en av dem på fliken **användar** information. 
- Du kan också hämta flera poster för en enhet på fliken Användar information på grund av en ominstallation av operativ systemet eller manuell omregistrering.
- Inledande registrering/anslutning av enheter är konfigurerad för att utföra ett försök antingen på inloggning eller lås/Lås upp. Det kan finnas 5 minuters fördröjning som utlöses av en aktivitet i Schemaläggaren. 
- Kontrol lera att [KB4284842](https://support.microsoft.com/help/4284842) har installerats, om det är Windows 7 SP1 eller windows Server 2008 R2 SP1. Den här uppdateringen förhindrar framtida autentiseringsfel på grund av kundens åtkomst förlust till skyddade nycklar när lösen ordet har ändrats.
- Hybrid Azure AD Join kan Miss lyckas när en användare har ändrat sina UPN, vilket bryter sömlös SSO-autentiseringsprocessen. Under anslutnings processen kan du se att den fortfarande skickar det gamla UPN till Azure AD, såvida inte cookies för webbläsarsessionen rensas eller användaren uttryckligen loggar ut och tar bort gamla UPN.

## <a name="step-1-retrieve-the-registration-status"></a>Steg 1: Hämta registrerings status 

**Så här kontrollerar du registrerings statusen:**  

1. Logga in med det användar konto som har genomfört en hybrid Azure AD-anslutning.
1. Öppna kommando tolken 
1. Skriv `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Det här kommandot visar en dialog ruta som innehåller information om anslutnings status.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/01.png" alt-text="Skärm bild av dialog rutan Workplace Join för Windows. Text som innehåller en e-postadress som anger att en viss enhet är ansluten till en arbets plats." border="false":::

## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Steg 2: utvärdera status för Hybrid Azure AD-anslutning 

Om enheten inte var hybrid Azure AD-ansluten kan du försöka göra en hybrid Azure AD-anslutning genom att klicka på knappen Anslut. Om försöket att göra en hybrid Azure AD-anslutning Miss lyckas visas information om fel meddelandet.

**De vanligaste problemen är:**

- Ett felkonfigurerat AD FS-eller Azure AD-eller nätverks problem

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/02.png" alt-text="Skärm bild av dialog rutan Workplace Join för Windows. Text rapporter som ett fel uppstod under autentiseringen av kontot." border="false":::
    
   - Autoworkplace.exe kan inte tyst autentisera med Azure AD eller AD FS. Detta kan orsakas av saknade eller felkonfigurerade AD FS (för federerade domäner) eller saknade eller felkonfigurerade Azure AD sömlösa enskilda Sign-On (för hanterade domäner) eller nätverks problem. 
   - Det kan bero på att Multi-Factor Authentication (MFA) är aktiverat/konfigurerat för användaren och WIAORMULTIAUTHN inte har kon figurer ATS på den AD FS servern. 
   - En annan möjlighet är att sidan för identifiering av start sfär (HRD) väntar på användar interaktion, vilket förhindrar **autoworkplace.exe** från att tyst begära en token.
   - Det kan bero på att AD FS och Azure AD-URL: er saknas i Internet Explorers intranäts zon på klienten.
   - Problem med nätverks anslutningen kan hindra **autoworkplace.exe** från att nå AD FS eller Azure AD-URL: er. 
   - **Autoworkplace.exe** kräver att klienten har direkt insikter från klienten till organisationens lokala AD-domänkontrollant, vilket innebär att hybrid Azure AD Join bara lyckas när klienten är ansluten till organisationens intranät.
   - Din organisation använder Azure AD sömlös enkel inloggning `https://autologon.microsoftazuread-sso.com` eller finns `https://aadg.windows.net.nsatc.net` inte på enhetens intranäts inställningar på Internet och **Tillåt att uppdateringar av statusfältet via skript** inte har Aktiver ATS för zonen Intranät.
- Du är inte inloggad som domän användare

   :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/03.png" alt-text="Skärm bild av dialog rutan Workplace Join för Windows. Text rapporter som ett fel uppstod under verifieringen av kontot." border="false":::

   Det finns några olika orsaker till varför detta kan inträffa:

   - Den inloggade användaren är inte en domän användare (till exempel en lokal användare). Hybrid Azure AD-anslutning på lågnivå enheter stöds bara för domän användare.
   - Klienten kan inte ansluta till en domänkontrollant.    
- En kvot har nåtts

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/04.png" alt-text="Skärm bild av dialog rutan Workplace Join för Windows. Text rapporterar ett fel eftersom användaren har nått det maximala antalet anslutna enheter." border="false":::

- Tjänsten svarar inte 

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/05.png" alt-text="Skärm bild av dialog rutan Workplace Join för Windows. Text rapporterar att det uppstod ett fel på grund av att servern inte svarade." border="false":::

Du kan också hitta statusinformation i händelse loggen under: **program och tjänster Log\Microsoft-Workplace Join**
  
**De vanligaste orsakerna till en misslyckad hybrid Azure AD-anslutning är:** 

- Datorn är inte ansluten till din organisations interna nätverk eller till ett VPN med en anslutning till din lokala AD-domänkontrollant.
- Du är inloggad på datorn med ett lokalt dator konto. 
- Tjänst konfigurations problem: 
   - AD FS-servern har inte kon figurer ATS för att stödja **WIAORMULTIAUTHN**. 
   - Datorns skog har inget objekt för tjänst anslutnings punkt som pekar på ditt verifierade domän namn i Azure AD 
   - Eller om din domän hanteras, har sömlöst SSO inte kon figurer ATS eller fungerar.
   - En användare har nått gränsen för enheter. 

## <a name="next-steps"></a>Nästa steg

Frågor finns i [vanliga frågor och svar om enhets hantering](faq.yml)  
