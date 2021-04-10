---
title: 'Snabb start: skapa en Azure Active Directory klient'
titleSuffix: Microsoft identity platform
description: I den här snabb starten får du lära dig hur du skapar en Azure Active Directory-klient som används för att utveckla program som använder Microsoft Identity Platform för autentisering och auktorisering.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 02/15/2021
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: cb4995c0c71d0bcebb3cdd8ec471a92b7820cb9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100535169"
---
# <a name="quickstart-set-up-a-tenant"></a>Snabbstart: Konfigurera en klientorganisation

Om du vill bygga appar som använder Microsoft Identity Platform för identitets-och åtkomst hantering behöver du åtkomst till en Azure Active Directory (Azure AD)- *klient*. Det finns i Azure AD-klienten som du registrerar och hanterar dina appar, konfigurerar deras åtkomst till data i Microsoft 365 och andra webb-API: er och aktiverar funktioner som villkorlig åtkomst.

En klientorganisation representerar en organisation. Det är en dedikerad instans av Azure AD som en organisation eller app-utvecklare tar emot i början av en relation med Microsoft. Relationen kan börja med att registrera sig för Azure, Microsoft Intune eller Microsoft 365 till exempel.

Varje Azure AD-klient är unik och avgränsad från andra Azure AD-klienter. Det har en egen representation av identiteter för arbets-och skol identiteter, konsument identiteter (om det är en Azure AD B2C klient) och app-registreringar. En app-registrering i din klient organisation kan bara tillåta autentiseringar från konton inom din klient organisation eller alla klienter.

## <a name="prerequisites"></a>Förutsättningar

Ett Azure-konto som har en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="determining-the-environment-type"></a>Fastställa miljö typ

Du kan skapa två typer av miljöer. Miljön beror bara på vilka typer av användare som din app autentiserar. 

Den här snabb starten åtgärdar två scenarier för den typ av app som du vill bygga:

* Arbets-och skol konton (Azure AD) eller Microsoft-konton (till exempel Outlook.com och Live.com)
* Sociala och lokala konton (Azure AD B2C)

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Arbets- och skolkonton eller personliga Microsoft-konton

Om du vill skapa en miljö för antingen arbets-och skol konton eller personliga Microsoft-konton kan du använda en befintlig Azure AD-klient eller skapa en ny.
### <a name="use-an-existing-azure-ad-tenant"></a>Använd en befintlig Azure AD-klient

Många utvecklare har redan klient organisationer via tjänster eller prenumerationer som är kopplade till Azure AD-klienter, till exempel Microsoft 365 eller Azure-prenumerationer.

Så här kontrollerar du klienten:

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>. Använd det konto som du använder för att hantera ditt program.
1. Markera det övre högra hörnet. Om du har en klient, loggas du in automatiskt. Du ser klient namnet direkt under ditt konto namn.
   * Hovra över ditt konto namn för att se namn, e-postadress, katalog eller klient-ID (ett GUID) och domän.
   * Om ditt konto är kopplat till flera klienter måste du välja namnet på ditt konto för att öppna en meny där du kan växla mellan klienter. Varje klient har sitt eget klient-ID.

> [!TIP]
> För att hitta klient-ID: t kan du:
> * Hovra över ditt konto namn för att hämta katalogen eller klient-ID: t.
> * Sök och välj **Azure Active Directory**  >  **Egenskaper**  >  **klient-ID** i Azure Portal.

Om du inte har en klient som är kopplad till ditt konto visas ett GUID under ditt konto namn. Du kommer inte att kunna utföra åtgärder som att registrera appar förrän du har skapat en Azure AD-klient.

### <a name="create-a-new-azure-ad-tenant"></a>Skapa en ny Azure AD-klient

Om du inte redan har en Azure AD-klient eller om du vill skapa en ny för utveckling, se [skapa en ny klient i Azure AD](../fundamentals/active-directory-access-create-new-tenant.md). Eller Använd den här funktionen för att [skapa en katalog](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) i Azure Portal. 

Du kommer att ange följande information för att skapa din nya klient:

- **Organisationsnamn**
- **Första domän** – den här domänen ingår i *. onmicrosoft.com. Du kan anpassa domänen senare.
- **Land eller region**

> [!NOTE]
> När du namnger din klientorganisation ska du använda alfanumeriska tecken. Specialtecken är inte tillåtna. Namnet får inte överskrida 256 tecken.

## <a name="social-and-local-accounts"></a>Sociala och lokala konton

Skapa en Azure AD B2C klient för att börja skapa appar som loggar in på sociala och lokala konton. För att börja, se [skapa en Azure AD B2C klient](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Registrera en app](quickstart-register-app.md) för integrering med Microsoft Identity Platform.
