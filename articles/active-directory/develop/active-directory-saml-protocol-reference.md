---
title: Hur Microsoft Identity Platform använder SAML-protokollet
description: Den här artikeln innehåller en översikt över SAML-profilerna för enskilda Sign-On och Sign-Out i Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 4e9d63b8fe7fc281a87deb27ddadd794e14fa04d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755633"
---
# <a name="how-the-microsoft-identity-platform-uses-the-saml-protocol"></a>Hur Microsoft Identity Platform använder SAML-protokollet

Microsoft Identity Platform använder SAML 2,0-protokollet för att göra det möjligt för program att tillhandahålla enkel inloggning till sina användare. SAML-profilerna för [enkel inloggning](single-sign-on-saml-protocol.md) och [enkel utloggning](single-sign-out-saml-protocol.md) i Azure AD förklarar hur SAML-kontroller, protokoll och bindningar används i tjänsten för identitets leverantör.

SAML-protokollet kräver identitets leverantören (Microsoft Identity Platform) och tjänst leverantören (programmet) för att utbyta information om sig själva.

När ett program har registrerats med Azure AD registrerar appens utvecklare en federationsserverproxy med Azure AD. Den här informationen inkluderar **omdirigerings-URI** och **metadata-URI** för programmet.

Microsoft Identity Platform använder moln tjänstens metadata- **URI** för att hämta signerings nyckeln och utloggnings-URI: n. Kunden kan öppna appen i **Azure AD-> app Registration** och sedan i **Inställningar-> egenskaper** kan de uppdatera UTloggnings-URL: en. På så sätt kan Microsoft Identity Platform skicka svaret till rätt URL. 

Azure Active Directory exponerar klient-och common (klient oberoende) enkel inloggning och enkel inloggnings slut punkter. Dessa URL: er representerar adresser bara platser – de är inte bara identifierare – så att du kan gå till slut punkten för att läsa metadata.

* Den klient-/regionsspecifika slut punkten finns på `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml` . *\<TenantDomainName>* Plats hållaren representerar ett registrerat domän namn eller ett TenantID-GUID för en Azure AD-klient. Contoso.com-klientens federationsmetadata är till exempel: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Klient oberoende slut punkt finns på `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml` . I den här slut punkts adressen visas **vanligt** i stället för ett klient domän namn eller-ID.

Information om de dokument för federationsmetadata som Azure AD publicerar finns i [federationsmetadata](../azuread-dev/azure-ad-federation-metadata.md).
