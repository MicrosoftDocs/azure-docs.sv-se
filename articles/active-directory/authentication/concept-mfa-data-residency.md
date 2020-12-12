---
title: Azure AD Multi-Factor Authentication data placering
description: Lär dig mer om personliga och organisatoriska data i Azure AD Multi-Factor Authentication butiker om dig och dina användare och vilka data som finns kvar i ursprungslandet/regionen.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 788512db242bf1a1c6f18ffc0ee773bd3372aa42
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355880"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multi-factor-authentication"></a>Data placering och kund information för Azure AD Multi-Factor Authentication

Kund information lagras av Azure AD på en geografisk plats baserat på den adress som tillhandahålls av din organisation när du prenumererar på en Microsoft Online-tjänst, till exempel Microsoft 365 och Azure. För information om var dina kunddata lagras kan du använda avsnittet var finns [dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) i Microsoft säkerhets Center.

Molnbaserad Azure AD Multi-Factor Authentication och Azure Multi-Factor Authentication-server process och lagra en mängd person uppgifter och organisations data. Den här artikeln beskriver vad och var data lagras.

Azure AD Multi-Factor Authentication-tjänsten har data Center i USA, Europa och Asien och stillahavsområdet. Följande aktiviteter kommer från de regionala data centren utom där det anges:

* Multi-Factor Authentication som använder telefonsamtal kommer från amerikanska data Center och dirigeras av globala leverantörer.
* Generell användning av autentiseringsbegäranden från andra regioner, till exempel Europa eller Australien bearbetas för närvarande baserat på användarens plats.
* Push-meddelanden med hjälp av Microsoft Authenticator-appen behandlas för närvarande i de regionala data centren baserat på användarens plats.
    * Enhets leverantörer – vissa tjänster, t. ex. Apple Push-meddelanden, kan finnas utanför användarens plats.

## <a name="personal-data-stored-by-azure-ad-multi-factor-authentication"></a>Personliga data som lagras av Azure AD Multi-Factor Authentication

Person uppgifter är information på användar nivå som är associerad med en speciell person. Följande data lager innehåller personlig information:

* Blockerade användare
* Förbigåde användare
* Microsoft Authenticator begär Anden om enhets-token
* Multi-Factor Authentication aktivitets rapporter
* Microsoft Authenticator aktiveringar

Den här informationen sparas i 90 dagar.

Azure AD Multi-Factor Authentication loggar inte personliga data, t. ex. användar namn, telefonnummer eller IP-adress, men det finns en *UserObjectId* som identifierar Multi-Factor Authentication försök till användare. Loggdata lagras i 30 dagar.

### <a name="azure-ad-multi-factor-authentication"></a>Azure AD-multifaktorautentisering

För offentliga Azure-moln, förutom Azure B2C-autentisering, NPS-tillägg och Windows Server 2016 eller 2019 AD FS adapter, lagras följande personliga data:

| Händelsetyp                           | Data lager typ |
|--------------------------------------|-----------------|
| OATH-token                           | I Multi-Factor Authentication loggar     |
| Enkelriktat SMS                          | I Multi-Factor Authentication loggar     |
| Röstsamtal                           | I Multi-Factor Authentication loggar<br />Data lager för Multi-Factor Authentication aktivitets rapport<br />Blockerade användare om bedrägeri rapporteras |
| Microsoft Authenticator meddelande | I Multi-Factor Authentication loggar<br />Data lager för Multi-Factor Authentication aktivitets rapport<br />Blockerade användare om bedrägeri rapporteras<br />Ändrings begär anden när Microsoft Authenticator enhets-token ändras |

> [!NOTE]
> Data lagret för Multi-Factor Authentication aktivitets rapporten lagras i USA för alla moln, oavsett vilken region som bearbetar autentiseringsbegäran. Microsoft Azure Tyskland, Microsoft Azure drivs av 21Vianet och Microsoft myndighets moln har sina egna oberoende data lager åtskilda från data lager i den offentliga moln regionen, men dessa data lagras alltid i USA. Dessa data lager innehåller personligt identifierbar information (PII) som User Principal Name (UPN) och fullständigt telefonnummer. 

För Microsoft Azure Government, Microsoft Azure Tyskland, Microsoft Azure som drivs av 21Vianet, Azure B2C-autentisering, NPS-tillägg och Windows Server 2016 eller 2019 AD FS adapter, lagras följande personliga data:

| Händelsetyp                           | Data lager typ |
|--------------------------------------|-----------------|
| OATH-token                           | I Multi-Factor Authentication loggar<br />Data lager för Multi-Factor Authentication aktivitets rapport |
| Enkelriktat SMS                          | I Multi-Factor Authentication loggar<br />Data lager för Multi-Factor Authentication aktivitets rapport |
| Röstsamtal                           | I Multi-Factor Authentication loggar<br />Data lager för Multi-Factor Authentication aktivitets rapport<br />Blockerade användare om bedrägeri rapporteras |
| Microsoft Authenticator meddelande | I Multi-Factor Authentication loggar<br />Data lager för Multi-Factor Authentication aktivitets rapport<br />Blockerade användare om bedrägeri rapporteras<br />Ändrings begär anden när Microsoft Authenticator enhets-token ändras |

### <a name="multi-factor-authentication-server"></a>Multi-Factor Authentication Server

Om du distribuerar och kör Azure Multi-Factor Authentication-server lagras följande personliga data:

> [!IMPORTANT]
> Från och med den 1 juli 2019 kommer Microsoft inte längre att erbjuda Multi-Factor Authentication-server för nya distributioner. Nya kunder som vill kräva Multi-Factor Authentication från sina användare bör använda molnbaserad Azure AD-Multi-Factor Authentication. Befintliga kunder som har aktiverat Multi-Factor Authentication-server före den 1 juli kommer att kunna ladda ned den senaste versionen, framtida uppdateringar och generera autentiseringsuppgifter för aktivering som vanligt.

| Händelsetyp                           | Data lager typ |
|--------------------------------------|-----------------|
| OATH-token                           | I Multi-Factor Authentication loggar<br />Data lager för Multi-Factor Authentication aktivitets rapport |
| Enkelriktat SMS                          | I Multi-Factor Authentication loggar<br />Data lager för Multi-Factor Authentication aktivitets rapport |
| Röstsamtal                           | I Multi-Factor Authentication loggar<br />Data lager för Multi-Factor Authentication aktivitets rapport<br />Blockerade användare om bedrägeri rapporteras |
| Microsoft Authenticator meddelande | I Multi-Factor Authentication loggar<br />Data lager för Multi-Factor Authentication aktivitets rapport<br />Blockerade användare om bedrägeri rapporteras<br />Ändrings begär anden när Microsoft Authenticator enhets-token ändras |

## <a name="organizational-data-stored-by-azure-ad-multi-factor-authentication"></a>Organisations data som lagras av Azure AD Multi-Factor Authentication

Organisations data är information om klient nivå som kan visa konfigurations-eller miljö konfiguration. Klient inställningar från följande Azure Portal Multi-Factor Authentication sidor kan lagra organisations data som utelåsnings trösklar eller information om uppringarens ID för inkommande begär Anden om telefonsamtal:

* Konto utelåsning
* Bedrägerivarning
* Meddelanden
* Telefonsamtals inställningar

För Azure Multi-Factor Authentication-server kan följande Azure Portal sidor innehålla organisations data:

* Serverinställningar

* Kringgå vid ett tillfälle
* Regler för cachelagring
* Multi-Factor Authentication-server status

## <a name="log-data-location"></a>Logg data plats

Var logg information lagras beror på vilken region de bearbetas i. De flesta geografiska områden har inbyggda Azure AD Multi-Factor Authentication-funktioner, så loggdata lagras i samma region som bearbetar Multi-Factor Authentication begäran. I geografiska områden utan inbyggt stöd för Azure AD-Multi-Factor Authentication betjänas de av antingen USA-eller Europa-geografiska områden och loggdata lagras i samma region som bearbetar Multi-Factor Authentication begäran.

Vissa grundläggande autentiseringsuppgifter för autentisering lagras bara i USA. Microsoft Azure Tyskland och Microsoft Azure som drivs av 21Vianet lagras alltid i deras respektive moln. Microsoft regerings loggdata lagras alltid i USA.

## <a name="next-steps"></a>Nästa steg

Mer information om vilka användar uppgifter som samlas in av molnbaserad Azure AD Multi-Factor Authentication och Azure Multi-Factor Authentication-server finns i [Azure ad Multi-Factor Authentication användar data insamling](howto-mfa-reporting-datacollection.md).
