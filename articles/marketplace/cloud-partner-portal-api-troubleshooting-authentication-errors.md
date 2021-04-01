---
title: Felsöka vanliga autentiseringsfel, Azure Marketplace
description: 'Ger hjälp med vanliga autentiseringsfel när du använder Cloud Partner Portal API: er i Azure Marketplace.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 01af5357e4ae2f4dfb317a0931a8d0bc2b2d54e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89487325"
---
# <a name="troubleshooting-common-authentication-errors"></a>Felsöka vanliga autentiseringsfel

> [!NOTE]
> Cloud Partner Portal API: er är integrerade i och fortsätter att fungera i Partner Center. Över gången introducerar små ändringar. Granska ändringarna som anges i [Cloud Partner Portal API-referensen](./cloud-partner-portal-api-overview.md) för att se till att koden fortsätter att fungera efter över gången till Partner Center. CPP-API: er bör endast användas för befintliga produkter som redan har integrerats före över gången till Partner Center. nya produkter bör använda API: er för överföring av Partner Center.

Den här artikeln ger hjälp med vanliga autentiseringsfel när du använder Cloud Partner Portal API: er.

## <a name="unauthorized-error"></a>Obehörigt fel

Om du får `401 unauthorized` fel meddelanden måste du kontrol lera att du har en giltig åtkomsttoken.  Om du inte redan har gjort det skapar du ett grundläggande Azure Active Directory (Azure AD)-program och ett tjänst huvud namn enligt beskrivningen i [använda portalen för att skapa ett Azure Active Directory program och tjänstens huvud namn som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md). Använd sedan programmet eller en enkel HTTP POST-begäran för att verifiera din åtkomst.  Du kommer att inkludera klient-ID, program-ID, objekt-ID och den hemliga nyckeln för att hämta åtkomsttoken.

## <a name="forbidden-error"></a>Förbjudet fel

Om du får ett `403 forbidden` fel meddelande, se till att rätt tjänst huvud namn har lagts till i utgivar kontot i Cloud Partner Portal. Följ stegen på sidan [förutsättningar](./cloud-partner-portal-api-prerequisites.md) för att lägga till tjänstens huvud namn i portalen.

Om rätt huvud namn för tjänsten har lagts till kontrollerar du all övrig information. Var noga med det objekt-ID som angavs i portalen. Det finns två objekt-ID på sidan Azure Active Directory app Registration och du måste använda det lokala objekt-ID: t. Du kan hitta rätt värde genom att gå till **Appregistreringar** sidan för appen och klicka på appens namn under **hanterat program i lokal katalog**. Då går du till appens lokala egenskaper, där du hittar rätt objekt-ID på sidan **Egenskaper** , som du ser i följande bild. Se också till att du använder rätt utgivare-ID när du lägger till tjänstens huvud namn och gör API-anropet.
