---
title: Registrerings Portal referens för app | Azure
titleSuffix: Microsoft identity platform
description: En beskrivning av funktionerna i registrerings portalen för Microsoft-appar.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: ryanwi
ms.reviewer: lenalepa
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 6a33da602eaa9bee20f155eaa550e558e5dcbeca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755569"
---
# <a name="app-registration-reference"></a>Referens för registrering av appar

Det här dokumentet innehåller kontext och beskrivningar av de olika funktionerna som finns i [Appregistreringars](https://aka.ms/appregistrations) upplevelsen i Azure Portal.

## <a name="my-applications-or-converged-applications"></a>Mina program eller konvergerade program

Den här listan innehåller alla dina program som har registrerats för användning med Microsoft Identity Platform. Dessa program kan logga in användare med både personliga Microsoft-konton och arbets-eller skol konton från Azure Active Directory. Mer information om Microsoft Identity Platform finns i [Översikt över v 2.0](./v2-overview.md). Dessa program kan också användas för att integrera med slut punkten för Microsoft-konto-autentisering `https://login.live.com` .

## <a name="azure-ad-only-applications"></a>Endast Azure AD-program

Den här listan innehåller alla dina program som har registrerats för användning med Azure AD v 1.0-slutpunkten. Dessa program kan bara logga in användare med arbets-eller skol konton från Azure Active Directory. Den här listan innehåller program som har registrerats med hjälp av **Appregistreringars** upplevelsen i [Azure Portal](https://portal.azure.com).

## <a name="live-sdk-applications"></a>Live SDK-program

Den här listan innehåller alla dina program som är registrerade för användning enbart med Microsoft-konto. De är inte aktiverade för användning med Azure Active Directory. Här hittar du några program som tidigare har registrerats med MSA Developer-portalen på `https://account.live.com/developers/applications` . Alla funktioner som du har utfört tidigare `https://account.live.com/developers/applications` kan nu utföras i [Appregistreringar](https://aka.ms/appregistrations).

## <a name="application-secrets"></a>Program hemligheter

Program hemligheter är autentiseringsuppgifter som gör att ditt program kan utföra tillförlitlig [klientautentisering](https://tools.ietf.org/html/rfc6749#section-2.3) med Microsoft Identity Platform. I OAuth & OpenID Connect kallas en program hemlighet vanligt vis som en `client_secret` . I v 2.0-protokollet måste alla program som tar emot en säkerhetstoken på en webb adresserad plats (med ett `https` schema) använda en program hemlighet för att identifiera sig för Microsoft Identity Platform vid inlösen av denna säkerhetstoken. Dessutom kommer alla interna klienter som tar emot token på en enhet att förbjudas att använda en program hemlighet för att utföra klientautentisering. Detta förhindrar lagring av hemligheter i osäkra miljöer.

Varje app kan innehålla två giltiga program hemligheter vid en specifik tidpunkt. Genom att underhålla två hemligheter kan du utföra regelbunden nyckel förnyelse i hela programmets hela miljö. När du har migrerat hela programmet till en ny hemlighet kan du ta bort den gamla hemligheten och etablera en ny.

Just nu är det bara två typer av program hemligheter som tillåts i appens registrerings Portal. Om du väljer **Skapa nytt lösen ord** skapas och lagras en delad hemlighet i respektive data lager, som du kan använda i ditt program. Om du väljer **generera nytt nyckel par** skapas ett nytt offentligt/privat nyckel par som kan hämtas och användas för klientautentisering till Microsoft Identity Platform. Om du väljer **överför offentlig nyckel** kan du använda ett eget offentligt/privat nyckel par.
Du måste ladda upp ett certifikat som innehåller en offentlig nyckel.

## <a name="profile"></a>Profil

Profil avsnittet i appens registrerings Portal kan användas för att anpassa inloggnings sidan för ditt program. Nu kan du ändra inloggnings sidans program logo typ, villkor för tjänste-URL och URL för sekretess policy. Logo typen måste vara en genomskinlig 48 x 48-eller 50 x 50 pixel bild i en GIF-, PNG-eller JPEG-fil som är 15 KB eller mindre. Försök att ändra värdena och visa den resulterande inloggnings Sidan!

## <a name="live-sdk-support"></a>Live SDK-support

När du aktiverar Live SDK-stöd kommer alla program hemligheter som du skapar att tillhandahållas i data lager för både Azure AD och Microsoft-kontot. Detta gör att ditt program kan integreras direkt med Microsoft-login.live.com (Service Account service). Om du vill skapa en app med Microsoft-konto direkt (i stället för att använda v 2.0-slut punkten) bör du kontrol lera att Live SDK-stödet är aktiverat.

Genom att inaktivera Live SDK-stöd ser du till att program hemligheten bara skrivs till Azure AD-datalagret. Azure AD-dataarkivet införlivar regler i företags klass som gör det möjligt att uppfylla vissa standarder, t. ex. FISMA efterlevnad. Om du aktiverar Live SDK-stöd kanske programmet inte uppfyller vissa av dessa standarder.

Om du bara planerar att använda v 2.0-slutpunkten kan du inaktivera Live SDK-stödet på ett säkert sätt.