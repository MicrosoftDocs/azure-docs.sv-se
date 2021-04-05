---
title: Kund data lagring för australiska och nya Zeeland-kunder – Azure AD
description: Lär dig mer om var Azure Active Directory lagrar kundrelaterade data för de australiska och nya Zeeland-kunderna.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 7/21/2020
ms.custom: it-pro, seodec18, references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d7c37e64e4f1b339ae66fe3d9135b1874476eb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836979"
---
# <a name="customer-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Kund data lagring för australiska och nya Zeeland-kunder i Azure Active Directory 

Azure Active Directory (Azure AD) lagrar kund information på en geografisk plats baserat på det land du angav när du registrerade dig för en Microsoft-onlinetjänst. Microsoft Online Services innehåller Microsoft 365 och Azure. 

Information om var Azure AD och andra data från Microsoft-tjänster finns i avsnittet var finns [dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) i Microsoft säkerhets Center.

Från den 26 februari 2020 lagrar Microsoft Azure ADs kunddata för nya klienter med en ny fakturerings adress i Australien eller nya Zeeland i de australiska data centren. Mellan den 1 maj 2020 och den 31 mars 2021 kommer Microsoft att migrera befintliga klienter som har en produkt som har en australiska eller ny Zeelands fakturerings adress till de australiska data centren utan att kräva någon kund åtgärd. Migreringsprocessen omfattar inga stillestånds tider för kunder och påverkar inte några funktioner i en klient under migreringen.

Dessutom stöder vissa Azure AD-funktioner ännu inte lagring av kunddata i Australien. Gå till [Azure AD-datakartan](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)om du vill ha detaljerad funktions information. Microsoft Azure AD Multi-Factor Authentication lagrar till exempel kund information i USA och bearbetar den globalt. Se [data placering och kund information för Azure AD Multi-Factor Authentication](../authentication/concept-mfa-data-residency.md).

> [!NOTE]
> Microsoft-produkter, tjänster och tredjepartsprogram som integreras med Azure AD har åtkomst till kund information. Utvärdera varje produkt, tjänst och program som du använder för att avgöra hur kunddata bearbetas av den specifika produkten, tjänsten och programmet och om de uppfyller företagets data lagrings krav. Läs mer om Microsoft-tjänsternas datahemvist i avsnittet [Var finns dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) på Microsoft Trust Center.