---
title: Enterprise State Roaming FAQ – Azure Active Directory
description: Vanliga frågor och svar om ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb889298a09c30a629c69442ebf31bc735af31d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96173132"
---
# <a name="settings-and-data-roaming-faq"></a>Vanliga frågor och svar om inställningar och dataväxling

I den här artikeln besvaras några frågor som IT-administratörer kan ha om inställningar och synkronisering av AppData.

## <a name="what-data-roams"></a>Vilka data är nätverks växlingar?

**Windows-inställningar**: dator inställningar som är inbyggda i operativ systemet Windows. Detta är vanligt vis inställningar som anpassar din dator och de omfattar följande breda kategorier:

* *Tema*, som inbegriper funktioner såsom skrivbordstema och inställningar för aktivitetsfältet.
* *Internet Explorer-inställningar*, inklusive nyligen öppnade flikar och favoriter.
* *Inställningar för Microsoft Edge-webbläsare*, till exempel favoriter och läslista.
* *Lösen ord*, inklusive Internet lösen ord, Wi-Fi profiler och andra.
* *Språkinställningar* såsom inställningar för tangentbordslayouter, systemspråk, datum och tid med mera.
* *Hjälpmedelsfunktioner* som högkontrasttema, skärmläsaren och förstoringsglaset.
* *Andra Windows-inställningar*, till exempel musinställningar.

> [!NOTE]
> Den här artikeln gäller Microsoft Edge äldre HTML-baserad webbläsare lanserad med Windows 10 i juli 2015. Artikeln gäller inte den nya Microsoft Edge krom-baserade webbläsaren som lanserades den 15 januari 2020. Mer information om hur synkronisering fungerar för den nya Microsoft Edge finns i artikeln [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

**Program data**: universella Windows-appar kan skriva inställnings data till en central mapp, och alla data som skrivs till den här mappen synkroniseras automatiskt. Det är upp till den enskilda appens utvecklare att utforma en app för att dra nytta av den här funktionen. Mer information om hur du utvecklar en universell Windows-app som använder roaming finns i [API för lagrings-API för AppData](/windows/uwp/design/app-settings/store-and-retrieve-app-data) och [Windows 8 AppData-Roaming-bloggen för utvecklare](https://blogs.windows.com/windowsdeveloper/2016/05/04/roaming-app-data-and-the-user-experience/).

## <a name="what-account-is-used-for-settings-sync"></a>Vilket konto används för synkronisering av inställningar?

I Windows 8,1 används alltid vanliga konsument Microsoft-konton för synkronisering av inställningar. Företags användare hade möjlighet att ansluta en Microsoft-konto till sitt Active Directory domän konto för att få åtkomst till synkronisering av inställningar. I Windows 10 ersätts den här anslutna Microsoft-konto funktionen med ett primärt/sekundärt konto ramverk.

Det primära kontot definieras som det konto som används för att logga in på Windows. Detta kan vara ett Microsoft-konto, ett Azure Active Directory (Azure AD)-konto, ett lokalt Active Directory-konto eller ett lokalt konto. Förutom det primära kontot kan Windows 10-användare lägga till ett eller flera sekundära moln konton på sina enheter. Ett sekundärt konto är vanligt vis ett Microsoft-konto, ett Azure AD-konto eller något annat konto, till exempel Gmail eller Facebook. Dessa sekundära konton ger till gång till ytterligare tjänster, till exempel enkel inloggning och Windows Store, men de kan inte synkronisera inställningar.

I Windows 10 kan endast det primära kontot för enheten användas för synkronisering av inställningar (se [Hur gör jag för att uppgradering från Microsoft-konto inställningar synkronisera i Windows 8 till Azure AD Settings Sync i Windows 10?](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)).

Data är aldrig blandade mellan de olika användar kontona på enheten. Det finns två regler för synkronisering av inställningar:

* Windows-inställningar kommer alltid att roamas med det primära kontot.
* AppData kommer att märkas med det konto som används för att hämta appen. Endast appar som är taggade med det primära kontot synkroniseras. Taggning av apparnas ägarskap fastställs när en app läses in i Windows Store eller hantering av mobila enheter (MDM).

Om det inte går att identifiera en Apps ägare, kommer den att roamas med det primära kontot. Om en enhet uppgraderas från Windows 8 eller Windows 8,1 till Windows 10, kommer alla appar att märkas som köpta av Microsoft-konto. Detta beror på att de flesta användare får appar via Windows Store och det fanns inget Windows Store-stöd för Azure AD-konton före Windows 10. Om en app installeras via en offline-licens kommer appen att märkas med det primära kontot på enheten.

> [!NOTE]
> Windows 10-enheter som ägs av företaget och är anslutna till Azure AD kan inte längre ansluta sina Microsoft-konton till ett domän konto. Möjligheten att ansluta en Microsoft-konto till ett domän konto och att alla användares data ska synkroniseras till Microsoft-konto (det vill säga att Microsoft-konto roaming via den anslutna Microsoft-konto och Active Directory funktioner) tas bort från Windows 10-enheter som är anslutna till en ansluten Active Directory eller Azure AD-miljö.

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Hur gör jag för att uppgradering från Microsoft-konto inställningar synkronisera i Windows 8 till Azure AD-inställningar synkronisering i Windows 10?

Om du är ansluten till den Active Directory domän som kör Windows 8,1 med en ansluten Microsoft-konto, synkroniserar du inställningarna via din Microsoft-konto. När du har uppgraderat till Windows 10 fortsätter du att synkronisera användar inställningar via Microsoft-konto så länge du är en domänansluten användare och Active Directory domän inte ansluter till Azure AD.

Om den lokala Active Directorys domänen ansluter med Azure AD försöker enheten synkronisera inställningarna med det anslutna Azure AD-kontot. Om Azure AD-administratören inte aktiverar Enterprise State Roaming kommer ditt anslutna Azure AD-konto att sluta synkronisera inställningar. Om du är en Windows 10-användare och loggar in med en Azure AD-identitet börjar du synkronisera Windows-inställningar så snart din administratör aktiverar inställningar synkronisering via Azure AD.

Om du har lagrat personliga data på din företags enhet bör du vara medveten om att Windows operativ system-och program data kommer att börja synkroniseras med Azure AD. Detta har följande konsekvenser:

* Dina personliga Microsoft-konto inställningar påverkar inte inställningarna på ditt arbets-eller skol Azure AD-konto. Detta beror på att synkroniseringen av Microsoft-konto och Azure AD-inställningar nu använder separata konton.
* Personliga data, till exempel Wi-Fi lösen ord, webbautentiseringsuppgifter och Internet Explorer-favoriter som tidigare har synkroniserats via en ansluten Microsoft-konto, kommer att synkroniseras via Azure AD.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Hur fungerar Microsoft-konto och Azure AD Enterprise State Roaming-interoperabilitet?

I november 2015 eller senare versioner av Windows 10 stöds Enterprise State Roaming bara för ett enda konto i taget. Om du loggar in i Windows med ett arbets-eller skol Azure AD-konto kommer alla data att synkroniseras via Azure AD. Om du loggar in i Windows med hjälp av en personlig Microsoft-konto kommer alla data att synkroniseras via Microsoft-konto. Universellt AppData kommer att roamas med bara det primära inloggnings kontot på enheten och den får bara roaming om appens licens ägs av det primära kontot. Universellt AppData för appar som ägs av ett sekundärt konto kommer inte att synkroniseras.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Synkroniserar inställningarna för Azure AD-konton från flera klienter?

När flera Azure AD-konton från olika Azure AD-klienter finns på samma enhet måste du uppdatera enhetens register för att kommunicera med Azure Rights Management-tjänsten för varje Azure AD-klient.  

1. Hitta GUID för varje Azure AD-klient. Öppna Azure Portal och välj en Azure AD-klient. Klientens GUID finns på egenskaps sidan för den valda klient organisationen ( https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) och märkt **katalog-ID**. 
2. När du har GUID måste du lägga till register nyckeln **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<tenant ID GUID>**.
   Skapa ett nytt multi-sträng-värde (REG-MULTI-SZ) med namnet **AllowedRMSServerUrls** från ID-nyckeln för **klient-ID** : t. Ange URL: er för licens distributions platsen för de andra Azure-klienter som enheten har åtkomst till för dess data.
3. Du hittar URL: erna för licens distributions platsen genom att köra cmdleten **Get-AadrmConfiguration** från AADRM-modulen. Ange båda värdena om värdena för **LicensingIntranetDistributionPointUrl** och **LicensingExtranetDistributionPointUrl** skiljer sig. Om värdena är desamma anger du värdet bara en gång.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Vad är alternativen för nätverks växlings inställningar för befintliga Windows-baserade Skriv bords program?

Roaming fungerar bara för universella Windows-appar. Det finns två alternativ för att aktivera roaming i ett befintligt Windows Desktop-program:

* [Desktop-bryggan](/windows/msix/desktop/source-code-overview) hjälper dig att ta dina befintliga Windows-skrivbordsappar till universell Windows-plattform. Härifrån kommer minimal kod ändringar att krävas för att dra nytta av nätverks växling av Azure AD-AppData. Desktop-bryggan ger dina appar en app-identitet, vilket krävs för att aktivera centrala AppData för befintliga skrivbordsappar.
* Med [User Experience Virtualization (UE-V)](/previous-versions//dn458947(v=vs.85)) kan du skapa en anpassad inställnings mall för befintliga Windows-skrivbordsappar och aktivera roaming för Win32-appar. Det här alternativet kräver inte att appens utvecklare ändrar koden för appen. UE-V är begränsad till lokal Active Directory nätverks växling för kunder som har köpt Microsoft Desktop Optimization Pack.

Administratörer kan konfigurera UE-V till nätverks växling av Windows-AppData genom att ändra roaming av Windows OS-inställningar och universella AppData via [UE-v grup principer](/microsoft-desktop-optimization-pack/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), inklusive:

* Grup princip för centrala Windows-inställningar
* Synkronisera inte grup princip för Windows-appar
* Nätverks växling i Internet Explorer i avsnittet program

I framtiden kan Microsoft undersöka sätt att göra UE-V djupt integrerat i Windows och utöka UE-V till roaming-inställningar via Azure AD-molnet.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Kan jag lagra synkroniserade inställningar och data lokalt?

Enterprise State Roaming lagrar alla synkroniserade data i Microsoft-molnet. UE-V erbjuder en lokal nätverks växlings lösning.

## <a name="who-owns-the-data-thats-being-roamed"></a>Vem äger de data som roamas?

Företagen äger de data som har centraliserats via Enterprise State Roaming. Data lagras i ett Azure-datacenter. Alla användar data krypteras både under överföring och i vila i molnet med hjälp av Azure Rights Management-tjänsten från Azure Information Protection. Detta är en förbättring jämfört med Microsoft-konto-baserade inställningar synkronisering, som krypterar endast vissa känsliga data, till exempel autentiseringsuppgifter innan de lämnar enheten.

Microsoft strävar efter att skydda kund information. En företags användares inställnings data krypteras automatiskt av Azure Rights Management-tjänsten innan den lämnar en Windows 10-enhet, så inga andra användare kan läsa dessa data. Om din organisation har en betald prenumeration för Azure Rights Management-tjänsten kan du använda andra skydds funktioner, till exempel spåra och återkalla dokument, automatiskt skydda e-postmeddelanden som innehåller känslig information och hantera dina egna nycklar ("ta med din egen nyckel"-lösning, även kallat BYOK). Mer information om de här funktionerna och hur den här skydds tjänsten fungerar finns i [Vad är Azure Rights Management](/azure/information-protection/what-is-information-protection).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Kan jag hantera synkronisering för en speciell app eller inställning?

I Windows 10 finns det ingen MDM-eller grupprincip-inställning för att inaktivera roaming för ett enskilt program. Klient organisations administratörer kan inaktivera AppData-synkronisering för alla appar på en hanterad enhet, men det finns ingen bättre kontroll på per app eller på-App-nivå.

## <a name="how-can-i-enable-or-disable-roaming"></a>Hur kan jag aktivera eller inaktivera roaming?

I appen **Inställningar** går du till **konton**  >  **Synkronisera dina inställningar**. På den här sidan kan du se vilket konto som används för att använda roaming-inställningar och du kan aktivera eller inaktivera enskilda grupper av inställningar för nätverks växling.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Vad är Microsofts rekommendation för att aktivera roaming i Windows 10?

Microsoft har några olika inställningar för nätverks växlings lösningar som är tillgängliga, inklusive centrala användar profiler, UE-V och Enterprise State Roaming.  Microsoft strävar efter att göra en investering i Enterprise State Roaming i framtida versioner av Windows. Om din organisation inte är klar eller bekvämt med att flytta data till molnet rekommenderar vi att du använder UE-V som din primära centrala teknik. Om din organisation kräver roaming-stöd för befintliga Windows-skrivbordsprogram, men är Eager att flytta till molnet, rekommenderar vi att du använder både Enterprise State Roaming och UE-V. Även om UE-V och Enterprise State Roaming är mycket likartade tekniker, är de inte ömsesidigt uteslutande. De kompletterar varandra för att se till att din organisation tillhandahåller de centrala tjänster som användarna behöver.  

När du använder både Enterprise State Roaming och UE-V gäller följande regler:

* Enterprise State Roaming är den primära centrala agenten på enheten. UE-V används för att komplettera "Win32-gap".
* UE-V-roaming för Windows-inställningar och modern UWP-AppData bör inaktive ras när du använder grup principer för UE-V. De är redan täckta av Enterprise State Roaming.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Hur stöder Enterprise State Roaming Virtual Desktop Infrastructure (VDI)?

Enterprise State Roaming stöds på Windows 10-klient-SKU: er, men inte på Server-SKU: er. Om en virtuell klient dator finns på en hypervisor-dator och du loggar in på en fjärran sluten dator på den virtuella datorn, kommer dina data att växlas. Om flera användare delar samma operativ system och användare via fjärr anslutning till en server för att få en fullständig Skriv bords upplevelse kanske nätverks växlingen inte fungerar. Det senare session-baserade scenariot stöds inte officiellt.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>Vad händer när min organisation köper en prenumeration som innehåller Azure Rights Management efter att ha använt nätverks växling?

Om din organisation redan använder roaming i Windows 10 med Azure Rights Management-prenumeration med begränsad användning kan du köpa en [betald prenumeration](https://azure.microsoft.com/pricing/details/information-protection/) som omfattar Azure Rights Management Protection-tjänsten påverkar inte funktionerna i funktionen för nätverks växling, och inga konfigurations ändringar krävs av IT-administratören.

## <a name="known-issues"></a>Kända problem

I dokumentationen i [fel söknings](enterprise-state-roaming-troubleshooting.md) avsnittet finns en lista över kända problem. 

## <a name="next-steps"></a>Nästa steg 

En översikt finns i [Översikt över företags tillstånds växling](enterprise-state-roaming-overview.md)