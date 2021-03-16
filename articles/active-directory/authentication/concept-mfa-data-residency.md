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
ms.openlocfilehash: 7381ab62eb39c555c6b4eb34f150fc71bea1f10f
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103561472"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Data placering och kund information för Azure AD multifaktor-autentisering

Azure Active Directory (Azure AD) lagrar kund information på en geografisk plats baserat på den adress som en organisation tillhandahåller när den prenumererar på en Microsoft Online-tjänst, till exempel Microsoft 365 eller Azure. Information om var din kund information lagras finns i var finns [dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) i Microsoft säkerhets Center.

Molnbaserad Azure AD multifaktor-autentisering och Azure multifaktor-serverautentisering och lagra personliga data och organisations data. Den här artikeln beskriver vad och var data lagras.

Tjänsten Azure AD multifakta-autentisering har data Center i USA, Europa och Asien och stillahavsområdet. Följande aktiviteter kommer från de regionala data centren utom där det anges:

* Telefonsamtal för multifaktorns autentisering kommer från USA Data Center och dirigeras av globala leverantörer.
* Generella användar autentiseringsbegäranden från andra regioner bearbetas för närvarande baserat på användarens plats.
* Push-meddelanden som använder Microsoft Authenticator-appen behandlas för närvarande i regionala Data Center baserat på användarens plats. Leverantörsspecifika enhets tjänster, till exempel Apple Push Notification Service, kan vara utanför användarens plats.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Person uppgifter lagrade av multifaktorautentisering i Azure AD

Person uppgifter är information på användar nivå som är associerad med en speciell person. Följande data lager innehåller personlig information:

* Blockerade användare
* Förbigåde användare
* Microsoft Authenticator begär Anden om enhets-token
* Aktivitets rapporter för multifaktorautentisering
* Microsoft Authenticator aktiveringar

Den här informationen sparas i 90 dagar.

Azure AD multifaktor-autentisering loggar inte personliga data, till exempel användar namn, telefonnummer eller IP-adresser. *UserObjectId* identifierar dock autentiseringsförsök till användarna. Loggdata lagras i 30 dagar.

### <a name="data-stored-by-azure-ad-multifactor-authentication"></a>Data som lagras av multifaktorautentisering i Azure AD

För offentliga Azure-moln, förutom Azure AD B2C autentisering, NPS-tillägget och Windows Server 2016-eller 2019 Active Directory Federation Services (AD FS)-kortet (AD FS), lagras följande personliga data:

| Händelsetyp                           | Data lager typ |
|--------------------------------------|-----------------|
| OATH-token                           | Loggar för multifaktorautentisering     |
| Enkelriktat SMS                          | Loggar för multifaktorautentisering     |
| Röstsamtal                           | Loggar för multifaktorautentisering<br/>Data lager för aktivitets rapport för multifaktorautentisering<br/>Blockerade användare (om bedrägerier rapporterades) |
| Microsoft Authenticator meddelande | Loggar för multifaktorautentisering<br/>Data lager för aktivitets rapport för multifaktorautentisering<br/>Blockerade användare (om bedrägerier rapporterades)<br/>Ändrings begär anden när Microsoft Authenticator enhetens token ändras |

För Microsoft Azure Government, Microsoft Azure Tyskland, Microsoft Azure som drivs av 21Vianet, Azure AD B2C autentisering, NPS-tillägget och Windows Server 2016 eller 2019 AD FS adapter, lagras följande personliga data:

| Händelsetyp                           | Data lager typ |
|--------------------------------------|-----------------|
| OATH-token                           | Loggar för multifaktorautentisering<br/>Data lager för aktivitets rapport för multifaktorautentisering |
| Enkelriktat SMS                          | Loggar för multifaktorautentisering<br/>Data lager för aktivitets rapport för multifaktorautentisering |
| Röstsamtal                           | Loggar för multifaktorautentisering<br/>Data lager för aktivitets rapport för multifaktorautentisering<br/>Blockerade användare (om bedrägerier rapporterades) |
| Microsoft Authenticator meddelande | Loggar för multifaktorautentisering<br/>Data lager för aktivitets rapport för multifaktorautentisering<br/>Blockerade användare (om bedrägerier rapporterades)<br/>Ändrings begär anden när Microsoft Authenticator enhetens token ändras |

### <a name="data-stored-by-azure-multifactor-authentication-server"></a>Data som lagras av Azure multifaktor-autentiseringsservern

Om du använder Azure multifaktor-autentiseringsservern lagras följande personliga data.

> [!IMPORTANT]
> Från och med den 1 juli 2019 erbjuder Microsoft inte längre någon Authentication-autentiseringsservern för nya distributioner. Nya kunder som vill kräva multifaktorautentisering från sina användare bör använda molnbaserad Azure AD multifaktor-autentisering. Befintliga kunder som aktiverade multifaktor-autentiseringsservern före den 1 juli 2019 kan ladda ned den senaste versionen och uppdateringarna och generera autentiseringsuppgifter för aktivering som vanligt.

| Händelsetyp                           | Data lager typ |
|--------------------------------------|-----------------|
| OATH-token                           | Loggar för multifaktorautentisering<br />Data lager för aktivitets rapport för multifaktorautentisering |
| Enkelriktat SMS                          | Loggar för multifaktorautentisering<br />Data lager för aktivitets rapport för multifaktorautentisering |
| Röstsamtal                           | Loggar för multifaktorautentisering<br />Data lager för aktivitets rapport för multifaktorautentisering<br />Blockerade användare (om bedrägerier rapporterades) |
| Microsoft Authenticator meddelande | Loggar för multifaktorautentisering<br />Data lager för aktivitets rapport för multifaktorautentisering<br />Blockerade användare (om bedrägerier rapporterades)<br />Ändrings begär anden när Microsoft Authenticator enhets-token ändras |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Organisations data som lagras av multifaktorautentisering i Azure AD

Organisations data är information om klient nivå som kan visa konfigurations-eller miljö konfiguration. Klient inställningar från följande Azure Portal multifaktas Authentication-sidor kan lagra organisations data som utelåsnings trösklar eller information om uppringarens ID för inkommande begär Anden om autentiseringsbegäranden:

* Konto utelåsning
* Bedrägerivarning
* Meddelanden
* Telefonsamtals inställningar

För Azure multifaktor-autentiseringsservern kan följande Azure Portal sidor innehålla organisations data:

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
| Australien   | Australien    | USA<sup>1</sup>                         | Australien <sup>2</sup> |

<sup>1</sup> OATH-kod loggar lagras i Australien.

<sup>2</sup> Röst samtals loggar för multifaktor-autentisering lagras i USA.

I följande tabell visas platsen för tjänst loggar för suveräna moln.

| Nationellt moln                      | Inloggningsloggar                         | Aktivitets rapport för multifaktorautentisering (innehåller person uppgifter)| Loggar för multifaktas autentiseringstjänst |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure Tyskland              | Tyskland                              | USA                            | USA               |
| Azure Kina 21Vianet                 | Kina                                | USA                            | USA               |
| Microsoft regerings moln           | USA                                   | USA                            | USA               |

Aktivitets rapporterna för multifaktorautentisering innehåller personliga data, till exempel UPN (User Principal Name) och fullständigt telefonnummer.

Loggar för tjänsten multifaktor-autentisering används för att köra tjänsten.

## <a name="next-steps"></a>Nästa steg

Mer information om vilka användar uppgifter som samlas in av molnbaserad Azure AD multifaktor-autentisering och Azure multifaktor-autentiseringsservern finns i Azure AD multifaktor- [autentisering användar data insamling](howto-mfa-reporting-datacollection.md).
