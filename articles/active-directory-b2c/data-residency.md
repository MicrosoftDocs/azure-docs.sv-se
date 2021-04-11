---
title: Regionstillgänglighet och datahemvist
titleSuffix: Azure AD B2C
description: Regions tillgänglighet, data placering och information om Azure Active Directory B2C för hands versioner av klienter.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/31/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: 6e724b3517d9e5a63d8699e9f66c51cf41f02012
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106092525"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: regions tillgänglighet & data placering

Regions tillgänglighet och data placering är två mycket olika koncept som fungerar annorlunda för Azure AD B2C från resten av Azure. Den här artikeln förklarar skillnaderna mellan dessa två begrepp och jämför hur de gäller för Azure jämfört med Azure AD B2C.

Azure AD B2C är **allmänt tillgänglig i hela världen** med alternativet för **data placering** i **USA, Europa eller Asien och Stillahavsområdet**. Azure AD B2C finns i **offentlig för hands version** i Australien.

[Region tillgänglighet](#region-availability) syftar på var en tjänst är tillgänglig för användning.

[Data placering](#data-residency) refererar till var användar data lagras.

## <a name="region-availability"></a>Regional tillgänglighet

Azure AD B2C är tillgängligt i hela världen via det offentliga Azure-molnet.

Detta skiljer sig från modellen följt av de flesta andra Azure-tjänster, som vanligt vis är *tillgängliga* med *data placering*. Du kan se exempel på detta i båda Azures [produkter som är tillgängliga på sidan region](https://azure.microsoft.com/regions/services/) och [Active Directory B2C pris kalkylatorn](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Dataplacering

Azure AD B2C lagrar användar data i antingen USA, Europa eller Asien och stillahavsområdet region.

Data placering bestäms av landet/regionen som du väljer när du [skapar en Azure AD B2C klient](tutorial-create-tenant.md):

![Skärm bild av formuläret Skapa klient organisation, Välj land eller region.](./media/data-residency/data-residency-b2c-tenant.png)

Data finns i **USA** för följande länder/regioner:

> USA (US), Kanada (CA), Costa Rica (CR), Dominikanska republiken (a), El Salvador (sa), Guatemala (GT), Mexiko (MX), Panama (PA), Puerto Rico (PR) och Trinidade & Tobago (TT)

Data finns i **Europa** för följande länder/regioner:

> Algeriet (DZ), Österrike (AT), Azerbajdzjan (AZ), Bahrain (BH), Vitryssland (av), Belgien (vara), Bulgarien (BG), Kroatien (HR), Cypern (CY), Tjeckien (CZ), Danmark (DK), Egypten (EG), Estland (EE), Frankrike (FR). Tyskland (DE), Grekland (GR), Ungern (Slovakien), Island (är), Irland (IE), Israel (IL), Italien (IT), Jordanien (JO), Kazakstan (KZ), Kenya (KE), Kuwait (KW), Lettland (LV), Libanon (LI), Litauen (LT) , Luxemburg (LU), Nord Makedonien (ML), Malta (MT), Montenegro (ME), Marocko (MA), Nederländerna (NL), Nigeria (naturgas), Norge (NO), Oman (OM), Pakistan (PK), Polen (PL), Portugal (PT), Qatar (frågor och svar), Rumänien (RO). Ryssland (RU), Saudiarabien (SA), Serbien (RS), Slovakien (SK), Slovenien (ST), södra Afrika (ZA), Spanien (SE), Schweiz (SE), Schweiz (TN), Turkiet (TR), Ukraina (UA), Förenade Arabemiraten (AE) och Storbritannien (GB)

Data finns i **Asien och Stillahavsområdet** för följande länder/regioner:

> Afghanistan (AF), Hongkong SAR (HK), Indien (i), Indonesien (ID), Japan (JP), Korea (KR), Malaysia (MY), Filippinerna (PH), Singapore (TG), Sri Lanka (LK), Taiwan (TW) och Thailand (TH)

Data finns i **Australien** (för hands version) för följande länder/regioner:

> Australien och nya Zeeland

Följande länder/regioner håller på att läggas till i listan. Nu kan du fortfarande använda Azure AD B2C genom att välja någon av de länder/regioner som finns ovan.

> Argentina, Brasilien, Chile, Colombia, Ecuador, Irak, Paraguay, Peru, Uruguay och Venezuela

## <a name="remote-profile-solution"></a>Fjär profils lösning

Med Azure AD B2C [anpassade principer](custom-policy-overview.md)kan du integrera med [RESTful API-tjänster](custom-policy-rest-api-intro.md), vilket gör att du kan lagra och läsa användar profiler från en fjärrdatabas (till exempel en marknadsförings databas, ett CRM-system eller ett affärs program).  
- Under redigeringen av registrering och profil, anropar Azure AD B2C en anpassad REST API för att spara användar profilen till fjärrdatakällan. Användarens autentiseringsuppgifter lagras i Azure AD B2C katalog. 
- Vid inloggning, efter verifiering av autentiseringsuppgifter med ett lokalt eller socialt konto, anropar Azure AD B2C REST API, som skickar användarens unika identifierare som primär nyckel för användare (e-postadress eller användarens objectId). REST API läser data från fjärrdatabasen och returnerar användar profilen.  

När registreringen, profil redigeringen eller inloggningen har slutförts innehåller Azure AD B2C användar profilen i den åtkomsttoken som returneras till programmet. Mer information finns i [exempel lösningen Azure AD B2C Remote Profile](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) i GitHub.

## <a name="next-steps"></a>Nästa steg

- [Skapa en Azure AD B2C klient](tutorial-create-tenant.md).
