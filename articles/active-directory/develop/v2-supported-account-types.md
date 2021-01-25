---
title: Konto typer som stöds | Azure
titleSuffix: Microsoft identity platform
description: Konceptuell dokumentation om mål grupper och konto typer som stöds i program
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: d6c184e2983a072dec4b3021a1b58a61cd206dba
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755989"
---
# <a name="supported-account-types"></a>Kontotyper som stöds

I den här artikeln förklaras vilka konto typer (ibland kallade *mål grupper*) som stöds i Microsoft Identity Platform-program.

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="account-types-in-the-public-cloud"></a>Konto typer i det offentliga molnet

I det Microsoft Azure offentliga molnet kan de flesta typer av appar logga in användare med valfri mål grupp:

- Om du skriver ett LOB-program (Line-of-Business) kan du logga in användare i din egen organisation. Ett sådant program kallas ibland för en *enda klient*.
- Om du är en ISV kan du skriva ett program som loggar in användare:

  - I vilken organisation som helst. Ett sådant program kallas för ett webb program för flera *innehavare* . Du kommer ibland att läsa att den loggar in användare med deras arbets-eller skol konton.
  - Med sitt arbets-eller skol konto eller personliga Microsoft-konton.
  - Endast med personliga Microsoft-konton.
    
- Om du skriver ett företags-till-kund-program kan du också logga in användare med sina sociala identiteter genom att använda Azure Active Directory B2C (Azure AD B2C).

## <a name="account-type-support-in-authentication-flows"></a>Konto typ stöd i verifikations flöden

Vissa konto typer kan inte användas med vissa autentiseringsscheman. Till exempel, i Desktop-, UWP-eller daemon-program:

- Daemon-program kan bara användas med Azure AD-organisationer. Det är inte bra att försöka använda daemon-program för att manipulera Microsoft-personliga konton. Administratörs medgivande kommer aldrig att beviljas.
- Du kan bara använda det integrerade Windows-autentiseringsschemat med arbets-eller skol konton (i din organisation eller i en organisation). Integrerad Windows-autentisering fungerar med domän konton och kräver att datorerna är domänanslutna eller anslutna till Azure AD. Det här flödet passar inte för personliga Microsoft-konton.
- [Tilldelningen av lösen ord för resurs ägare](./v2-oauth-ropc.md) (username/Password) kan inte användas med personliga Microsoft-konton. Personliga Microsoft-konton kräver att användaren godkänner åtkomst till personliga resurser vid varje inloggnings session. Det är därför det här beteendet inte är kompatibelt med icke-interaktiva flöden.

## <a name="account-types-in-national-clouds"></a>Konto typer i nationella moln

Appar kan också logga in användare i [nationella moln](authentication-national-cloud.md). Microsoft-personliga konton stöds dock inte i dessa moln. Det är därför som de konto typer som stöds minskas, för dessa moln, till din organisation (en enskild klient) eller organisationer (flera klient program).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [innehav i Azure Active Directory](./single-and-multi-tenant-apps.md).
- Läs mer om [nationella moln](./authentication-national-cloud.md).
