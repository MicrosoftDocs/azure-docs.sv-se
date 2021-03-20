---
title: Villkorlig åtkomst kräver hanterad enhet – Azure Active Directory
description: Lär dig hur du konfigurerar Azure Active Directory (Azure AD) enhets principer för villkorlig åtkomst som kräver hanterade enheter för åtkomst till Cloud App.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: c11b58954eefda67f981d618b04ab2bd69fa6b43
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93077769"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Gör så här: Kräv hanterade enheter för Cloud app-åtkomst med villkorlig åtkomst

I en mobil-och mellanliggande värld, kan Azure Active Directory (Azure AD) använda enkel inloggning till appar och tjänster var som helst. Behöriga användare kan komma åt dina molnappar från ett brett utbud av enheter, inklusive mobila och personliga enheter. Många miljöer har dock minst ett fåtal appar som endast ska kunna nås av enheter som uppfyller dina standarder för säkerhet och efterlevnad. Dessa enheter kallas även hanterade enheter. 

Den här artikeln förklarar hur du kan konfigurera principer för villkorlig åtkomst som kräver att hanterade enheter får åtkomst till vissa molnappar i din miljö. 

## <a name="prerequisites"></a>Förutsättningar

Krav på hanterade enheter för åtkomst till molnbaserad **Azure AD-villkorlig åtkomst** och **Azure AD-enhets hantering** . Om du inte är bekant med något av dessa områden än bör du läsa följande avsnitt, först:

- **[Villkorlig åtkomst i Azure Active Directory](./overview.md)** – den här artikeln ger en konceptuell översikt över villkorlig åtkomst och den relaterade terminologin.
- **[Introduktion till enhets hantering i Azure Active Directory](../devices/overview.md)** – den här artikeln ger dig en översikt över de olika alternativen för att hämta enheter under organisations kontroll. 
- För Chrome-stöd i **Windows 10 Creators Update (version 1703)** eller senare installerar du [tillägget Windows 10-konton](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Det här tillägget krävs när en princip för villkorlig åtkomst kräver enhetsspecifika uppgifter.

>[!NOTE] 
> Vi rekommenderar att du använder Azure AD Device-baserad princip för villkorlig åtkomst för att få bästa möjliga tillämpning efter den första autentiseringen av enheten. Detta inkluderar sessioner som stänger om enheten faller utanför efterlevnad och enhets kod flödet.


## <a name="scenario-description"></a>Scenariobeskrivning

Det är en utmaning att hantera balansen mellan säkerhet och produktivitet. Spridningen av enheter som stöds för att komma åt moln resurser hjälper till att förbättra användarnas produktivitet. På sidan vänd vill du förmodligen inte att vissa resurser i din miljö ska kunna nås av enheter med en okänd skydds nivå. För de berörda resurserna bör du kräva att användarna bara kan komma åt dem med hjälp av en hanterad enhet. 

Med villkorlig åtkomst i Azure AD kan du hantera det här kravet med en enda princip som ger åtkomst:

- Till valda molnappar
- För valda användare och grupper
- Kräver en hanterad enhet

## <a name="managed-devices"></a>Hanterade enheter  

I enkla termer är hanterade enheter enheter som har *en viss typ* av organisations kontroll. I Azure AD är förutsättningen för en hanterad enhet att den har registrerats med Azure AD. När du registrerar en enhet skapas en identitet för enheten i form av ett enhets objekt. Det här objektet används av Azure för att spåra statusinformation om en enhet. Som Azure AD-administratör kan du redan använda det här objektet för att växla (aktivera/inaktivera) status för en enhet.
  
:::image type="content" source="./media/require-managed-devices/32.png" alt-text="Skärm bild av enhets fönstret i Azure A D. Aktivera och inaktivera objekt är markerade." border="false":::

Om du vill hämta en enhet som är registrerad i Azure AD har du tre alternativ: 

- **Registrerade enheter för Azure AD** – så här hämtar du en personlig enhet som är registrerad i Azure AD
- **Azure AD-anslutna enheter** – för att få en organisations Windows 10-enhet som inte är ansluten till en lokal AD som är registrerad i Azure AD. 
- **Hybrid Azure AD-anslutna enheter** – för att få en Windows 10-enhet eller stöd för en äldre enhet som är ansluten till en lokal AD som är registrerad i Azure AD.

De här tre alternativen beskrivs i artikeln [Vad är en enhets identitet?](../devices/overview.md)

För att bli en hanterad enhet måste en registrerad enhet antingen vara en **hybrid Azure AD-ansluten enhet** eller en **enhet som har marker ATS som kompatibel**.  

:::image type="content" source="./media/require-managed-devices/47.png" alt-text="Skärm bild av fönstret Azure A D Grant. Bevilja åtkomst är markerat, som är kryss rutor för enheter som ska vara kompatibla och hybrid Azure A D-anslutna." border="false":::
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Kräv hybrid Azure AD-anslutna enheter

I din princip för villkorlig åtkomst kan du välja **Kräv att hybrid Azure AD-ansluten enhet** ska ange att de valda molnappar bara kan nås med en hanterad enhet. 

:::image type="content" source="./media/require-managed-devices/10.png" alt-text="Skärm bild av fönstret Azure A D Grant. Bevilja åtkomst är markerat. En kryss ruta kräver att enheter som är hybrid Azure A D-anslutna också är markerade." border="false":::

Den här inställningen gäller endast för Windows 10-enheter eller äldre enheter, till exempel Windows 7 eller Windows 8 som är anslutna till en lokal AD. Du kan bara registrera dessa enheter med Azure AD med en hybrid Azure AD-anslutning, som är en [automatiserad process](../devices/hybrid-azuread-join-plan.md) för att få en Windows 10-enhet registrerad. 

:::image type="content" source="./media/require-managed-devices/45.png" alt-text="Tabell som visar namn, aktive rad status, O S, version, kopplings typ, ägare, M D M och kompatibel status för en enhet. Status för kompatibel är nej." border="false":::

Vad gör en hybrid Azure AD-ansluten enhet till en hanterad enhet?  För enheter som är anslutna till en lokal AD antas det att kontrollen över dessa enheter upprätthålls med hjälp av hanterings lösningar som **Configuration Manager** eller **grup princip (GP)** för att hantera dem. Eftersom det inte finns någon metod för Azure AD för att avgöra om någon av dessa metoder har tillämpats på en enhet, är en hybrid Azure AD-ansluten enhet en relativt svag mekanism för att kräva en hanterad enhet. Det är upp till dig som administratör att bedöma om de metoder som tillämpas på dina lokala domänanslutna enheter är tillräckligt starka för att utgöra en hanterad enhet om en sådan enhet också är en hybrid Azure AD-ansluten enhet.

## <a name="require-device-to-be-marked-as-compliant"></a>Kräv att enheten ska markeras som kompatibel

Alternativet att *kräva att en enhet markeras som kompatibel* är den starkaste formen för att begära en hanterad enhet.

:::image type="content" source="./media/require-managed-devices/11.png" alt-text="Skärm bild av fönstret Azure A D Grant. Bevilja åtkomst är markerat. En kryss ruta som kräver att en enhet markeras som kompatibel väljs också." border="false":::

Det här alternativet kräver att en enhet registreras med Azure AD och också markeras som kompatibel av:
         
- Intune
- Ett MDM-system (Mobile Device Management) från tredje part som hanterar Windows 10-enheter via Azure AD-integrering. MDM-system från tredje part för andra typer av enhets operativ system än Windows 10 stöds inte.
 
:::image type="content" source="./media/require-managed-devices/46.png" alt-text="Tabell som visar namn, aktive rad status, O S, version, kopplings typ, ägare, M D M och kompatibel status för en enhet. Statusen kompatibel är markerad." border="false":::

För en enhet som har marker ATS som kompatibel kan du anta att: 

- De mobila enheter som din personal använder för att komma åt företags data hanteras
- Mobila appar som används av din personal hanteras
- Företagets information skyddas genom att hjälpa till att kontrol lera hur din personal får åtkomst till och delar den
- Enheten och dess appar är kompatibla med företagets säkerhets krav

### <a name="scenario-require-device-enrollment-for-ios-and-android-devices"></a>Scenario: Kräv registrering av enheter för iOS-och Android-enheter

I det här scenariot har contoso beslutat att all mobil åtkomst till Microsoft 365 resurser måste använda en registrerad enhet. Alla användare har redan loggat in med autentiseringsuppgifter för Azure AD och har tilldelade licenser som innehåller Azure AD Premium P1 eller P2 och Microsoft Intune.

Organisationer måste utföra följande steg för att kräva att en registrerad mobil enhet används.

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **tilldelningar** väljer **du användare och grupper**
   1. Under **Inkludera** väljer du **alla användare** eller de enskilda **användare och grupper** som du vill tillämpa principen på. 
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder**  >  **inkluderar** väljer du **Office 365**.
1. Under **villkor** väljer du **enhets plattformar**.
   1. **Konfigurera** till **Ja**.
   1. Ta med **Android** och **iOS**.
1. Under **åtkomst kontrolls**  >  **beviljande** väljer du följande alternativ:
   - **Kräv att enheten ska markeras som kompatibel**
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa och aktivera din princip.

### <a name="known-behavior"></a>Känt beteende

När du använder [enhets kod OAuth-flödet](../develop/v2-oauth2-device-code.md), så stöds inte kontrollen Kräv hanterad enhets beviljande kontroll eller enhets tillstånd. Detta beror på att enheten som utför autentisering inte kan ge enhets status till enheten som tillhandahåller en kod och enhetens tillstånd i token är låst till enheten som utför autentiseringen. Använd kontrollen Kräv Multi-Factor Authentication i stället.

På Windows 7, iOS, Android, macOS och vissa tredjeparts webbläsare i Azure AD identifierar enheten med ett klient certifikat som är etablerad när enheten har registrerats med Azure AD. När en användare först loggar in via webbläsaren uppmanas användaren att välja certifikatet. Slutanvändaren måste välja det här certifikatet innan de kan fortsätta att använda webbläsaren.

## <a name="next-steps"></a>Nästa steg

[Utvärdera effekten av principer för villkorlig åtkomst innan du aktiverar dem i stor utsträckning med endast rapport läge](concept-conditional-access-report-only.md).
