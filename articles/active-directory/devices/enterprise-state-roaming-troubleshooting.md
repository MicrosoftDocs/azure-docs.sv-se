---
title: Felsöka Enterprise State Roaming i Azure Active Directory
description: Ger svar på vissa frågor som IT-administratörer kan ha om inställningar och synkronisering av AppData.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tanning
ms.collection: M365-identity-device-management
ms.openlocfilehash: df70891ef090d44769aadbc235273e3193bc780e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94837217"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Felsöka Enterprise State Roaming inställningar i Azure Active Directory

Det här avsnittet innehåller information om hur du felsöker och diagnostiserar problem med Enterprise State Roaming och innehåller en lista över kända problem.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> Den här artikeln gäller Microsoft Edge äldre HTML-baserad webbläsare lanserad med Windows 10 i juli 2015. Artikeln gäller inte den nya Microsoft Edge krom-baserade webbläsaren som lanserades den 15 januari 2020. Mer information om hur synkronisering fungerar för den nya Microsoft Edge finns i artikeln [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="preliminary-steps-for-troubleshooting"></a>Preliminära steg för fel sökning 

Innan du börjar felsöka kontrollerar du att användaren och enheten har kon figurer ATS korrekt och att alla krav i Enterprise State Roaming uppfylls av enheten och användaren. 

1. Windows 10, med de senaste uppdateringarna och minst version 1511 (OS Build 10586 eller senare) har installerats på enheten. 
1. Enheten är Azure AD-ansluten eller hybrid Azure AD-ansluten. Mer information finns i [så här hämtar du en enhet under kontrollen av Azure AD](overview.md).
1. Se till att **Enterprise State roaming** har Aktiver ATS för klienten i Azure AD enligt beskrivningen i [för att aktivera Enterprise State roaming](enterprise-state-roaming-enable.md). Du kan aktivera roaming för alla användare eller bara för en viss grupp av användare.
1. Användaren tilldelas en Azure Active Directory Premium-licens.  
1. Enheten måste startas om och användaren måste logga in igen för att få åtkomst till Enterprise State Roaming funktioner.

## <a name="information-to-include-when-you-need-help"></a>Information som ska inkluderas om du behöver hjälp
Om du inte kan lösa problemet genom att följa anvisningarna nedan kan du kontakta våra support tekniker. Ta med följande information när du kontaktar dem:

* **Allmän beskrivning av felet**: finns det fel meddelanden som visas av användaren? Om det inte fanns något fel meddelande beskriver du det oväntade beteende som du noterade i detalj. Vilka funktioner är aktiverade för synkronisering och vad väntar användaren på att synkronisera? Är flera funktioner inte synkroniserade eller är de isolerade till en?
* **Användare som påverkas** – är synkronisering fungerar/fungerar inte för en eller flera användare? Hur många enheter ingår per användare? Är alla inte synkroniserade, eller så är några av dem synkroniserade och några synkroniserar inte?
* **Information om användaren** – vilken identitet är användaren som använder för att logga in på enheten? Hur loggar användaren in på enheten? Är de en del av en vald säkerhets grupp som tillåts att synkronisera? 
* **Information om enheten** – är den här enheten Azure AD-ansluten eller domänansluten? Vilken version är enheten på? Vilka är de senaste uppdateringarna?
* **Datum/tid/tidszon** – vad var det exakta datumet och tiden som du såg felet (inklusive timezone)?

Med den här informationen kan vi hjälpa oss att lösa problemet så snabbt som möjligt.

## <a name="troubleshooting-and-diagnosing-issues"></a>Felsökning och diagnostisera problem

Det här avsnittet innehåller förslag på hur du felsöker och diagnostiserar problem som rör Enterprise State Roaming.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Verifiera synkronisering och inställnings sidan "synkronisera inställningar" 

1. När du har anslutit till din Windows 10-dator till en domän som är konfigurerad för att tillåta Enterprise State Roaming loggar du in med ditt arbets konto. Gå till **Inställningar**  >  **konton**  >  **Synkronisera dina inställningar** och bekräfta att synkroniseringen och de enskilda inställningarna är på, och att du synkroniserar med ditt arbets konto i överst på sidan Inställningar. Bekräfta att samma konto också används som inloggnings konto i **Inställningar**  >  **konton**  >  **din information**. 
1. Kontrol lera att synkroniseringen fungerar på flera datorer genom att göra vissa ändringar på den ursprungliga datorn, till exempel flytta aktivitets fältet till höger eller överst på skärmen. Se till att ändringen sprids till den andra datorn inom fem minuter. 

   * Genom att låsa och låsa upp skärmen (Win + L) kan du utlösa en synkronisering.
   * Du måste logga in med samma konto på båda datorerna för att synkroniseringen ska fungera – eftersom Enterprise State Roaming är knuten till användar kontot och inte dator kontot.

**Möjligt problem**: om kontrollerna på sidan **Inställningar** inte är tillgängliga och du ser meddelandet "vissa Windows-funktioner är bara tillgängliga om du använder ett Microsoft-konto eller arbets konto". Det här problemet kan uppstå för enheter som har kon figurer ATS för att vara domänanslutna och registrerade i Azure AD, men enheten har ännu inte autentiserats till Azure AD. En möjlig orsak är att enhets principen måste tillämpas, men det här programmet sker asynkront och kan fördröjas med några timmar. 

### <a name="verify-the-device-registration-status"></a>Verifiera status för enhets registrering

Enterprise State Roaming kräver att enheten registreras med Azure AD. Även om det inte är särskilt för Enterprise State Roaming kan du följa anvisningarna nedan för att bekräfta att Windows 10-klienten är registrerad och bekräfta tumavtryck, Azure AD-inställningar URL, NGC-status och annan information.

1. Öppna kommando tolken utan förhöjd behörighet. Om du vill göra detta i Windows öppnar du kör Start programmet (Win + R) och skriver "cmd" för att öppna.
1. När kommando tolken är öppen skriver du "*dsregcmd.exe/status*".
1. För förväntade utdata ska fältet **AzureAdJoined** vara "Ja", **WamDefaultSet** Field-värde ska vara "Yes" och värdet för fältet **WamDefaultGUID** måste vara ett GUID med "(AzureAd)" i slutet.

**Potentiellt problem**: **WamDefaultSet** och **AzureAdJoined** har både "nej" i fältvärdet, enheten var domänansluten och registrerad i Azure AD, och enheten synkroniserar inte. Om detta visas kan enheten behöva vänta på att en princip ska tillämpas eller att enhetens autentisering misslyckades vid anslutning till Azure AD. Användaren kan behöva vänta några timmar innan principen tillämpas. Andra fel söknings steg kan vara att försöka utföra automatisk registrering genom att logga ut och in eller starta uppgiften i Schemaläggaren. I vissa fall körs "*dsregcmd.exe/Leave*" i ett kommando tolks fönster med förhöjd behörighet, startas om och försök att registrera igen kan hjälpa dig med det här problemet.

**Möjligt problem**: fältet för **SettingsUrl** är tomt och enheten synkroniserar inte. Användaren kanske senast loggade in på enheten innan Enterprise State Roaming aktiverades i Azure Active Directory portalen. Starta om enheten och låt användaren logga in. I portalen kan du prova att be IT-administratören att navigera till **Azure Active Directory**  >  **enheter**  >  **Enterprise State roaming** inaktivera och återaktivera **användare kan synkronisera inställningar och AppData på alla enheter**. När du har aktiverat igen startar du om enheten och användaren måste logga in. Om detta inte löser problemet kan **SettingsUrl** vara tomt om det finns ett felaktigt enhets certifikat. I det här fallet körs "*dsregcmd.exe/Leave*" i ett kommando tolks fönster med förhöjd behörighet, startas om och försök att registrera igen kan hjälpa dig med det här problemet.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Enterprise State Roaming och Multi-Factor Authentication 

Under vissa omständigheter kan Enterprise State Roaming inte synkronisera data om Azure AD Multi-Factor Authentication har kon figurer ATS. Mer information om dessa problem finns i support dokumentet [KB3193683](https://support.microsoft.com/kb/3193683). 

**Möjligt problem**: om enheten har kon figurer ATS för att kräva Multi-Factor Authentication på Azure Active Directory-portalen kan du inte synkronisera inställningarna när du loggar in på en Windows 10-enhet med ett lösen ord. Den här typen av Multi-Factor Authentication konfiguration är avsedd att skydda ett Azure-administratörskonto. Administratörs användare kan fortfarande synkronisera genom att logga in på sina Windows 10-enheter med sin Microsoft Passport for Work PIN-kod eller genom att slutföra Multi-Factor Authentication vid åtkomst till andra Azure-tjänster som Microsoft 365.

**Möjligt problem**: synkroniseringen kan inte utföras om administratören konfigurerar Active Directory Federation Services (AD FS) Multi-Factor Authentication princip för villkorlig åtkomst och åtkomsttoken på enheten upphör att gälla. Se till att du loggar in och loggar ut med hjälp av Microsoft Passport for Work PIN-kod eller fullständig Multi-Factor Authentication vid åtkomst till andra Azure-tjänster som Microsoft 365.

### <a name="event-viewer"></a>Loggboken

För avancerad fel sökning kan Loggboken användas för att hitta vissa fel. Dessa dokumenteras i tabellen nedan. Händelserna kan hittas under Loggboken > **program och tjänster loggar**  >  **Microsoft**  >  **Windows**  >  **SettingSync – Azure** och för problem med identiteter med Sync- **program och tjänst loggar**  >  **Microsoft**  >  **Windows**  >  **AAD**.

## <a name="known-issues"></a>Kända problem

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>Synkronisering fungerar inte på enheter som har inlästa appar på sidan med MDM-programvara

Påverkar enheter som kör Windows 10 jubileums uppdatering (version 1607). I Loggboken under SettingSync-Azure loggarna visas händelse-ID 6013 med fel 80070259 ofta.

**Rekommenderad åtgärd**  
Se till att Windows 10 v1607-klienten har den 23 augusti 2016 kumulativa uppdateringen ([KB3176934](https://support.microsoft.com/kb/3176934) OS Build 14393,82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Favoriter i Internet Explorer synkroniseras inte

Påverkar enheter som kör Windows 10 november Update (version 1511).

**Rekommenderad åtgärd**  
Se till att Windows 10 v1511-klienten har den kumulativa uppdateringen juli 2016 ([KB3172985](https://support.microsoft.com/kb/3172985) OS Build 10586,494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>Temat synkroniseras inte, samt data som skyddas med Windows Information Protection 

För att förhindra data läckage, kommer data som skyddas med [windows information Protection](/windows/security/information-protection/windows-information-protection/protect-enterprise-data-using-wip) inte att synkroniseras via Enterprise State roaming för enheter som använder uppdaterings uppdatering för Windows 10.

**Rekommenderad åtgärd**  
Inga. Framtida uppdateringar av Windows kan lösa det här problemet.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>Inställningarna för datum, tid och region synkroniseras inte på domänanslutna enheter 
  
Enheter som är domänanslutna kommer inte att uppleva synkronisering för inställningens datum, tid och region: automatisk tid. Om du använder automatisk tid kan det hända att andra inställningar för datum, tid och region åsidosätts och att dessa inställningar inte synkroniseras. 

**Rekommenderad åtgärd**  
Inga. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>UAC-meddelanden vid synkronisering av lösen ord

Påverkar enheter som kör Windows 10 november Update (version 1511) med ett trådlöst nätverkskort som är konfigurerat för att synkronisera lösen ord.

**Rekommenderad åtgärd**  
Kontrol lera att Windows 10 v1511-klienten har den kumulativa uppdateringen ([KB3140743](https://support.microsoft.com/kb/3140743) OS Build 10586,494).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>Synkronisering fungerar inte på enheter som använder smartkort för inloggning

Om du försöker logga in på Windows-enheten med ett smartkort eller virtuellt smartkort slutar inställningarna att fungera.     

**Rekommenderad åtgärd**  
Inga. Framtida uppdateringar av Windows kan lösa det här problemet.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>En domänansluten enhet synkroniseras inte efter att företags nätverket har lämnats     

Domänanslutna enheter som är registrerade för Azure AD kan uppleva synkroniseringsfel om enheten är utanför platsen under en längre tid och domänautentisering inte kan slutföras.

**Rekommenderad åtgärd**  
Anslut enheten till ett företags nätverk så att synkroniseringen kan återupptas.

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Den Azure AD-anslutna enheten synkroniseras inte och användaren har ett blandat Case User huvud namn.

Om användaren har ett blandat Skift läges-UPN (t. ex. användar namn i stället för användar namn) och användaren finns på en Azure AD-ansluten enhet, som har uppgraderat från Windows 10 version 10586 till 14393, kan det hända att användarens enhet inte kan synkronisera. 

**Rekommenderad åtgärd**  
Användaren måste koppla från och återansluta enheten till molnet. Det gör du genom att logga in som lokal administratörs användare och ta bort enheten genom att gå till **Inställningar**  >  **system**  >  **om** och välja "hantera eller koppla från arbetet eller skolan". Rensa filerna nedan och Anslut sedan enheten igen i **Inställningar**  >  **system**  >  **om** och välj "Anslut till arbets plats eller skola". Fortsätt att ansluta enheten till Azure Active Directory och slutför flödet.

I rensnings steget rensar du följande filer:
- Settings. dat i `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Alla filer under mappen `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>Händelse-ID 6065:80070533 den här användaren kan inte logga in eftersom det här kontot är inaktiverat för tillfället  

I Loggboken under SettingSync/fel söknings loggarna kan det här felet visas när användarens autentiseringsuppgifter har upphört att gälla. Det kan dessutom inträffa när klienten inte har AzureRMS automatiskt. 

**Rekommenderad åtgärd**  
I det första fallet måste användaren uppdatera sina autentiseringsuppgifter och logga in på enheten med de nya autentiseringsuppgifterna. För att lösa problemet med AzureRMS går du vidare med stegen som anges i [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>Händelse-ID 1098: fel: det gick inte att utföra 0xCAA5001C  

I Loggboken under AAD/Operational-loggarna kan det här felet visas med händelse 1104: AAD Cloud AP plugin anrop get token returnerade fel: 0xC000005F. Det här problemet uppstår om behörigheter eller ägar attribut saknas.  

**Rekommenderad åtgärd**  
Fortsätt med stegen i listan [KB3196528](https://support.microsoft.com/kb/3196528).  

## <a name="next-steps"></a>Nästa steg

En översikt finns i [Översikt över företags tillstånds växling](enterprise-state-roaming-overview.md).