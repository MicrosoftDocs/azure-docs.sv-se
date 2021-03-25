---
title: Hantera enheter med Azure Portal | Microsoft Docs
description: Lär dig hur du använder Azure Portal för att hantera enheter.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 03/23/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: hafowler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18b43a99eb561cbfa340e0b3f318782bef2ca17c
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023443"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Hantera enhetsidentiteter med hjälp av Azure-portalen

Med Azure AD får du en central plats för att hantera enhets identiteter.

På sidan **alla enheter** kan du:

- Identifiera enheter, inklusive:
   - Enheter som har anslutits eller registrerats i Azure AD.
   - Enheter som distribueras med [Windows autopilot](/windows/deployment/windows-autopilot/windows-autopilot).
   - Skrivare som använder [Universal Print](/universal-print/fundamentals/universal-print-getting-started)
- Utföra hanterings uppgifter för enhets identitet, t. ex. Aktivera, inaktivera, ta bort eller hantera.
   - [Skrivare](/universal-print/fundamentals/) och [Windows autopilot](/windows/deployment/windows-autopilot/windows-autopilot) -enheter har begränsade hanterings alternativ i Azure AD. De måste hanteras från respektive administrations gränssnitt.
- Konfigurera enhetens identitets inställningar.
- Aktivera eller inaktivera Enterprise State Roaming.
- Granska enhets relaterade gransknings loggar
- Hämta enheter (förhands granskning)

[![Vyn alla enheter i Azure Portal](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox)

Du kan komma åt enhets portalen med följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Bläddra till **Azure Active Directory**  >  **enheter**.

## <a name="manage-devices"></a>Hantera enheter

Det finns två platser för att hantera enheter i Azure AD:

- **Azure Portal**  >  **Azure Active Directory**  >  **Enheter**
- **Azure Portal**  >  **Azure Active Directory**  >  **Användare** > välja en användare > **enheter**

Båda alternativen gör att administratörer kan:

- Sök efter enheter.
- Se enhets information inklusive:
    - Enhetsnamn
    - Enhets-ID
    - OS och version
    - Kopplingstyp
    - Ägare
    - Hantering och efterlevnad av mobila enheter
    - Återställnings nyckel för BitLocker
- Utföra hanterings uppgifter för enhets identitet, t. ex. Aktivera, inaktivera, ta bort eller hantera.
   - [Skrivare](/universal-print/fundamentals/) och [Windows autopilot](/windows/deployment/windows-autopilot/windows-autopilot) -enheter har begränsade hanterings alternativ i Azure AD. De måste hanteras från respektive administrations gränssnitt.

> [!TIP]
> - Hybrid Azure AD-anslutna Windows 10-enheter har inte någon ägare. Om du söker efter en enhet efter ägare och inte har hittat den, söker du efter enhets-ID.
>
> - Om du ser en enhet som är "hybrid Azure AD-ansluten" med ett tillstånd "väntar" i kolumnen registrerad, anger den att enheten har synkroniserats från Azure AD Connect och väntar på att slutföra registreringen från klienten. Läs mer om hur du [planerar din hybrid Azure AD Join-implementering](hybrid-azuread-join-plan.md). Ytterligare information finns i artikeln, [vanliga frågor och svar om enheter](faq.md).
>
> - För vissa iOS-enheter kan enhets namn som innehåller apostrofer eventuellt använda olika tecken som ser ut som apostrofer. Därför är det lite knepigt att söka efter sådana enheter – om du inte ser Sök resultaten korrekt kontrollerar du att Sök strängen innehåller matchande apostrof-tecken.

### <a name="manage-an-intune-device"></a>Hantera en Intune-enhet

Om du är Intune-administratör kan du hantera enheter där MDM är markerat **Microsoft Intune**. Om enheten inte har registrerats med Microsoft Intune blir alternativet "hantera" nedtonat.

### <a name="enable-or-disable-an-azure-ad-device"></a>Aktivera eller inaktivera en Azure AD-enhet

Om du vill aktivera eller inaktivera enheter har du två alternativ:

- Verktygsfältet på sidan **alla enheter** när du har valt en eller flera enheter.
- Verktygsfältet efter ökad detalj nivå i en speciell enhet.

> [!IMPORTANT]
> - Du måste vara global administratör eller en moln enhets administratör i Azure AD för att aktivera eller inaktivera en enhet. 
> - Att inaktivera en enhet förhindrar att en enhet autentiseras med Azure AD, vilket hindrar enheten från att komma åt dina Azure AD-resurser som skyddas av enhets villkorliga åtkomst eller med autentiseringsuppgifter för Windows Hello för företag.
> - Genom att inaktivera en enhet återkallar du både PRT (Primary Refresh token) och eventuella uppdateringstoken (RT) på enheten.
> - Det går inte att aktivera eller inaktivera skrivare i Azure AD.

### <a name="delete-an-azure-ad-device"></a>Ta bort en Azure AD-enhet

Om du vill ta bort en enhet har du två alternativ:

- Verktygsfältet på sidan **alla enheter** när du har valt en eller flera enheter.
- Verktygsfältet efter ökad detalj nivå i en speciell enhet.

> [!IMPORTANT]
> - Du måste vara tilldelad rollen som moln enhets administratör, Intune-administratör eller global administratör i Azure AD för att ta bort en enhet.
> - Skrivare och Windows autopilot-enheter kan inte tas bort i Azure AD
> - Ta bort en enhet:
>    - Förhindrar att en enhet får åtkomst till dina Azure AD-resurser.
>    - Tar bort alla uppgifter som är kopplade till enheten, till exempel BitLocker-nycklar för Windows-enheter.  
>    - Representerar en icke-återställnings bar aktivitet och rekommenderas inte om det inte krävs.

Om en enhet hanteras av en annan hanterings auktoritet (till exempel Microsoft Intune) ser du till att enheten har rensats/dragits tillbaka innan enheten tas bort i Azure AD. Granska hur du [hanterar inaktuella enheter](manage-stale-devices.md) innan du tar bort några enheter.

### <a name="view-or-copy-device-id"></a>Visa eller kopiera enhets-ID

Du kan använda ett enhets-ID för att kontrol lera enhetens ID-information på enheten eller använda PowerShell under fel sökning. Klicka på enheten för att komma åt kopierings alternativet.

![Visa ett enhets-ID](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Visa eller kopiera BitLocker-nycklar

Du kan visa och kopiera BitLocker-nycklarna så att användarna kan återställa krypterade enheter. Dessa nycklar är bara tillgängliga för Windows-enheter som är krypterade och har sina nycklar lagrade i Azure AD. Du kan hitta de här nycklarna när du använder information om en enhet genom att välja **Visa återställnings nyckel**. Om du väljer **Visa återställnings nyckel** skapas en Gransknings logg, som du hittar i `KeyManagement` kategorin.

![Visa BitLocker-nycklar](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

Om du vill visa eller kopiera BitLocker-nycklarna måste du antingen vara ägare till enheten eller en användare som har minst en av följande roller tilldelade:

- Moln enhets administratör
- Global administratör
- Support administratör
- Intune Service-administratör
- Säkerhetsadministratör
- Säkerhetsläsare

### <a name="device-list-filtering-preview"></a>Filtrering av enhets lista (för hands version)

Tidigare kunde du bara filtrera enhets listan efter aktivitet och aktiverat tillstånd. I den här för hands versionen kan du nu filtrera enhets listan efter följande attribut på en enhet:

- Aktiverat tillstånd
- Kompatibelt tillstånd
- Kopplings typ (Azure AD-ansluten, hybrid Azure AD-ansluten, Azure AD registrerad)
- Aktivitetstidsstämpel
- Operativsystem
- Enhets typ (skrivare, säkra virtuella datorer, delade enheter, registrerade enheter)

Så här aktiverar du funktionen för för hands versions filtrering i vyn **alla enheter** :

![Aktivera funktionen för förhands visning av filter](./media/device-management-azure-portal/device-filter-preview-enable.png)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Bläddra till **Azure Active Directory**  >  **enheter**.
1. Välj banderollen som visas och **prova de nya förbättringarna av enhets filtreringen. Klicka om du vill aktivera förhands granskningen.**

Du kommer nu att kunna **lägga till filter** i vyn **alla enheter** .

### <a name="download-devices-preview"></a>Hämta enheter (förhands granskning)

Moln enhets administratörer, Intune-administratörer och globala administratörer kan använda alternativet **Hämta enheter (förhands granskning)** för att exportera en CSV-fil med enheter baserat på eventuella filter som används. Om inga filter används i listan, kommer alla enheter att exporteras. En export kan köras under en period på upp till en timme beroende på 

Den exporterade listan innehåller följande enhets identitets attribut:

`accountEnabled, approximateLastLogonTimeStamp, deviceOSType, deviceOSVersion, deviceTrustType, dirSyncEnabled, displayName, isCompliant, isManaged, lastDirSyncTime, objectId, profileType, registeredOwners, systemLabels, registrationTime, mdmDisplayName`

## <a name="configure-device-settings"></a>Konfigurera enhetsinställningar

Om du vill hantera enhets identiteter med Azure AD-portalen måste dessa enheter antingen vara [registrerade eller anslutna](overview.md) till Azure AD. Som administratör kan du styra processen för att registrera och ansluta enheter genom att konfigurera följande enhets inställningar.

Du måste ha tilldelats någon av följande roller för att kunna visa eller hantera enhets inställningar i Azure Portal:

- Global administratör
- Moln enhets administratör
- Global läsare
- Katalog läsare

![Enhets inställningar som är relaterade till Azure AD](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **Användare kan ansluta enheter till Azure AD** – med den här inställningen kan du välja vilka användare som kan registrera sina enheter som Azure AD-anslutna enheter. Standardvärdet är **alla**.

> [!NOTE]
> **Användare kan ansluta enheter till Azure AD** -inställningen gäller bara för Azure AD Join i Windows 10. Den här inställningen gäller inte för Hybrid Azure AD-anslutna enheter, [Azure AD-anslutna virtuella datorer i Azure](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) -och Azure AD-anslutna enheter med hjälp av [själv distributions läge för Windows autopilot](/mem/autopilot/self-deploying) eftersom dessa metoder fungerar i en kontext som inte är en användare.

- **Ytterligare lokala administratörer på Azure AD-anslutna enheter** – du kan välja de användare som har behörighet till lokal administratör på en enhet. Dessa användare läggs till i rollen *enhets administratörer* i Azure AD. Globala administratörer i Azure AD och enhets ägare beviljas lokal administratörs behörighet som standard. Det här alternativet är en Premium Edition-funktion som är tillgänglig via produkter som Azure AD Premium eller Enterprise Mobility Suite (EMS).
- **Användare kan registrera sina enheter med Azure AD** – du måste konfigurera den här inställningen så att Windows 10 personal-, iOS-, Android-och MacOS-enheter kan registreras med Azure AD. Om du väljer **ingen**, tillåts inte enheter att registrera med Azure AD. Registrering med Microsoft Intune eller hantering av mobila enheter (MDM) för Microsoft 365 kräver registrering. Om du har konfigurerat någon av dessa tjänster är **alla** markerad och **ingen** är tillgänglig.
- **Enheter som ska vara Azure AD-anslutna eller registrerade i Azure AD kräver Multi-Factor Authentication** – du kan välja om användarna måste ange ytterligare en autentiseringsnivå för att ansluta till eller registrera enheten i Azure AD. Standardvärdet är **Nej**. Vi rekommenderar att du kräver Multi-Factor Authentication när du registrerar eller ansluter till en enhet. Innan du aktiverar Multi-Factor Authentication för den här tjänsten måste du se till att Multi-Factor Authentication har kon figurer ATS för de användare som registrerar sina enheter. Mer information om olika Azure AD Multi-Factor Authentication-tjänster finns i [komma igång med Azure ad Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md). 

> [!NOTE]
> **Enheter som är anslutna till Azure AD eller Azure AD-registrerade kräver Multi-Factor Authentication** inställningen gäller för enheter som antingen är Azure AD-anslutna (med vissa undantag) eller Azure AD registrerad. Den här inställningen gäller inte för Hybrid Azure AD-anslutna enheter, [Azure AD-anslutna virtuella datorer i Azure](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) -och Azure AD-anslutna enheter med hjälp av [själv distributions läge för Windows autopilot](/mem/autopilot/self-deploying).

> [!IMPORTANT]
> - Vi rekommenderar att du använder [användar åtgärder "Registrera eller ansluta enheter"](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions) i villkorlig åtkomst för att tvinga Multi-Factor Authentication för att ansluta till eller registrera en enhet. 
> - Du måste ange den här inställningen till **Nej** om du använder en princip för villkorlig åtkomst för att kräva Multi-Factor authencation. 

- **Maximalt antal enheter** – med den här inställningen kan du välja maximalt antal Azure AD-anslutna eller Azure AD-registrerade enheter som en användare kan ha i Azure AD. Om en användare når den här kvoten kan de inte lägga till fler enheter förrän en eller flera av de befintliga enheterna tas bort. Standardvärdet är **50**. Du kan öka värdet upp till 100 och om du anger ett värde över 100, kommer Azure AD att ställa in det på 100. Du kan också använda ett obegränsat värde för att tvinga fram ingen gräns förutom befintliga kvot gränser.

> [!NOTE]
> Inställningen för **maximalt antal enheter** gäller för enheter som antingen är Azure AD-anslutna eller Azure AD registrerade. Den här inställningen gäller inte för Hybrid Azure AD-anslutna enheter.

- [Företagsroaming](enterprise-state-roaming-overview.md)

## <a name="audit-logs"></a>Granskningsloggar

Enhets aktiviteter är tillgängliga via aktivitets loggarna. Dessa loggar innehåller aktiviteter som utlösts av registrerings tjänsten för enheter och användare:

- Skapa och lägga till ägare/användare på enheten
- Ändringar av enhets inställningar
- Enhets åtgärder som att ta bort eller uppdatera en enhet

Din start punkt till gransknings data är **gransknings loggar** i avsnittet **aktivitet** på sidan **enheter** .

Gransknings loggen har en Standardlistvy som visar:

- Datum och tid för förekomsten
- Målen
- Initieraren/aktören (som) för en aktivitet
- Aktiviteten (vad)

:::image type="content" source="./media/device-management-azure-portal/63.png" alt-text="Skärm bild av en tabell i avsnittet aktivitet på sidan enheter som visar datum, mål, aktör och aktivitet för fyra gransknings loggar." border="false":::

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

:::image type="content" source="./media/device-management-azure-portal/64.png" alt-text="Skärm bild som visar verktygsfältet på sidan enheter. Kolumn objekt är markerat." border="false":::

Om du vill begränsa de data som rapporteras till en nivå som passar dig kan du filtrera granskningsdata med hjälp av följande fält:

- Kategori
- Resurstyp för aktivitet
- Aktivitet
- Datumintervall
- Mål
- Initierad av (aktör)

Förutom filtren kan du söka efter vissa poster.

:::image type="content" source="./media/device-management-azure-portal/65.png" alt-text="Skärm bild av kontroller för gransknings data filter, med kategori, aktivitets resurs typ, aktivitet, datum intervall, mål och aktörs fält och ett sökfält." border="false":::

## <a name="next-steps"></a>Nästa steg

[Hantera inaktuella enheter i Azure AD](manage-stale-devices.md)

[Företagsroaming](enterprise-state-roaming-overview.md)
