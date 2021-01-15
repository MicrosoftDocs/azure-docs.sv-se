---
title: Placering för Azure AD multifaktaor-autentisering
description: Lär dig mer om de personliga och organisations data som Azure AD multifaktor-autentisering lagrar för dig och dina användare och vilka data som finns kvar i ursprungslandet/regionen.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 788a666e8ec509bbd29a8dbd503a60b3dddefd6b
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208360"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Data placering och kund information för Azure AD multifaktor-autentisering

Kund information lagras av Azure AD på en geografisk plats baserat på den adress som tillhandahålls av din organisation när du prenumererar på en Microsoft Online-tjänst, till exempel Microsoft 365 och Azure. För information om var dina kunddata lagras kan du använda avsnittet var finns [dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) i Microsoft säkerhets Center.

Molnbaserad Azure AD multifaktor-autentisering och Azure AD Multifactor Authentication Server process och lagra en mängd person uppgifter och organisations data. Den här artikeln beskriver vad och var data lagras.

Azure AD multimetrisk Authentication-tjänsten har data Center i USA, Europa och Asien och stillahavsområdet. Följande aktiviteter kommer från de regionala data centren utom där det anges:

* Multifaktor-autentisering med telefonsamtal kommer från amerikanska data Center och dirigeras av globala leverantörer.
* Generell användning av autentiseringsbegäranden från andra regioner, till exempel Europa eller Australien bearbetas för närvarande baserat på användarens plats.
* Push-meddelanden med hjälp av Microsoft Authenticator-appen behandlas för närvarande i de regionala data centren baserat på användarens plats.
    * Enhets leverantörer – vissa tjänster, t. ex. Apple Push-meddelanden, kan finnas utanför användarens plats.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Person uppgifter lagrade av multifaktorautentisering i Azure AD

Person uppgifter är information på användar nivå som är associerad med en speciell person. Följande data lager innehåller personlig information:

* Blockerade användare
* Förbigåde användare
* Microsoft Authenticator begär Anden om enhets-token
* Aktivitets rapporter för multifaktorautentisering
* Microsoft Authenticator aktiveringar

Den här informationen sparas i 90 dagar.

Azure AD multifaktor-autentiseringen loggar inte personliga data, till exempel användar namn, telefonnummer eller IP-adress, men det finns en *UserObjectId* som identifierar multifaktorautentisering för användare. Loggdata lagras i 30 dagar.

### <a name="azure-ad-multifactor-authentication"></a>Azure AD multifaktor-autentisering

För offentliga Azure-moln, förutom Azure B2C-autentisering, NPS-tillägg och Windows Server 2016 eller 2019 AD FS adapter, lagras följande personliga data:

| Händelsetyp                           | Data lager typ |
|--------------------------------------|-----------------|
| OATH-token                           | I loggar med multifaktorautentisering     |
| Enkelriktat SMS                          | I loggar med multifaktorautentisering     |
| Röstsamtal                           | I loggar med multifaktorautentisering<br />Data lager för aktivitets rapport för multifaktorautentisering<br />Blockerade användare om bedrägeri rapporteras |
| Microsoft Authenticator meddelande | I loggar med multifaktorautentisering<br />Data lager för aktivitets rapport för multifaktorautentisering<br />Blockerade användare om bedrägeri rapporteras<br />Ändrings begär anden när Microsoft Authenticator enhets-token ändras |

För Microsoft Azure Government, Microsoft Azure Tyskland, Microsoft Azure som drivs av 21Vianet, Azure B2C-autentisering, NPS-tillägg och Windows Server 2016 eller 2019 AD FS adapter, lagras följande personliga data:

| Händelsetyp                           | Data lager typ |
|--------------------------------------|-----------------|
| OATH-token                           | I loggar med multifaktorautentisering<br />Data lager för aktivitets rapport för multifaktorautentisering |
| Enkelriktat SMS                          | I loggar med multifaktorautentisering<br />Data lager för aktivitets rapport för multifaktorautentisering |
| Röstsamtal                           | I loggar med multifaktorautentisering<br />Data lager för aktivitets rapport för multifaktorautentisering<br />Blockerade användare om bedrägeri rapporteras |
| Microsoft Authenticator meddelande | I loggar med multifaktorautentisering<br />Data lager för aktivitets rapport för multifaktorautentisering<br />Blockerade användare om bedrägeri rapporteras<br />Ändrings begär anden när Microsoft Authenticator enhets-token ändras |

### <a name="multifactor-authentication-server"></a>Server för multifaktorautentisering

Om du distribuerar och kör Azure AD multifaktor-autentiseringsservern lagras följande personliga data:

> [!IMPORTANT]
> Från och med den 1 juli 2019 kommer Microsoft inte längre att erbjuda multifaktorautentisering för nya distributioner. Nya kunder som vill kräva multifaktorautentisering från sina användare bör använda molnbaserad Azure AD multifaktor-autentisering. Befintliga kunder som har aktiverat multifaktor-autentiseringsservern före den 1 juli kommer att kunna ladda ned den senaste versionen, framtida uppdateringar och generera autentiseringsuppgifter för aktivering som vanligt.

| Händelsetyp                           | Data lager typ |
|--------------------------------------|-----------------|
| OATH-token                           | I loggar med multifaktorautentisering<br />Data lager för aktivitets rapport för multifaktorautentisering |
| Enkelriktat SMS                          | I loggar med multifaktorautentisering<br />Data lager för aktivitets rapport för multifaktorautentisering |
| Röstsamtal                           | I loggar med multifaktorautentisering<br />Data lager för aktivitets rapport för multifaktorautentisering<br />Blockerade användare om bedrägeri rapporteras |
| Microsoft Authenticator meddelande | I loggar med multifaktorautentisering<br />Data lager för aktivitets rapport för multifaktorautentisering<br />Blockerade användare om bedrägeri rapporteras<br />Ändrings begär anden när Microsoft Authenticator enhets-token ändras |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Organisations data som lagras av multifaktorautentisering i Azure AD

Organisations data är information om klient nivå som kan visa konfigurations-eller miljö konfiguration. Klient inställningar från följande Azure Portal multifaktor-autentiseringsinställningar kan lagra organisations data, till exempel utelåsnings trösklar eller information om uppringarens ID för inkommande begär Anden om autentisering av telefonsamtal:

* Konto utelåsning
* Bedrägerivarning
* Meddelanden
* Telefonsamtals inställningar

Och för Azure AD multifakta-autentiseringsservern kan följande Azure Portal sidor innehålla organisations data:

* Serverinställningar

* Kringgå vid ett tillfälle
* Regler för cachelagring
* Server status för multifaktorautentisering

## <a name="multifactor-authentication-logs-location"></a>Plats för loggar för multifaktorautentisering

I följande tabell visas platsen för tjänst loggar för offentliga moln.

| Offentligt moln| Inloggningsloggar | Aktivitets rapport för multifaktorautentisering        | Loggar för multifaktas autentiseringstjänst       |
|-------------|--------------|----------------------------------------|------------------------|
| USA          | USA           | USA                                     | USA                     |
| Europa      | Europa       | USA                                     | Europa <sup>2</sup>    |
| Australien   | Australien    | US<sup>1</sup>                         | Australien <sup>2</sup> |

<sup>1</sup> OATH-kod loggar lagras i Australien

<sup>2</sup> Röst samtal loggar för multifaktor-autentisering lagras i USA

I följande tabell visas platsen för tjänst loggar för suveräna moln.

| Nationellt moln                      | Inloggningsloggar                         | Aktivitets rapport för multifaktorautentisering (innehåller person uppgifter)| Loggar för multifaktas autentiseringstjänst |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure Tyskland              | Tyskland                              | USA                            | USA               |
| Microsoft Azure som drivs av 21Vianet | Kina                                | USA                            | USA               |
| Microsoft regerings moln           | USA                                   | USA                            | USA               |

Aktivitets rapport data för multifaktorautentisering innehåller personliga data som User Principal Name (UPN) och fullständigt telefonnummer.

Loggar för tjänsten multifaktor-autentisering används för att köra tjänsten.

## <a name="next-steps"></a>Nästa steg

Mer information om vilka användar uppgifter som samlas in av molnbaserad Azure AD multifaktor-autentisering och Azure AD multifaktor-autentiseringsservern finns i Azure AD multifaktor- [autentisering användar data insamling](howto-mfa-reporting-datacollection.md).
