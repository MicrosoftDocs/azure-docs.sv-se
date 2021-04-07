---
title: Azure App konfiguration REST API-auktorisering
description: Referens sidor för auktorisering med Azure App konfigurations REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 70f05799ce2856ad490937a17b456e78789088f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932650"
---
# <a name="authorization"></a>Auktorisering

Auktorisering syftar på den procedur som används för att fastställa vilka behörigheter en anropare har när en begäran görs. Det finns flera auktoriserings modeller. Den auktoriserings modell som används för en begäran beror på vilken [autentiseringsmetod](./rest-api-authentication-index.md) som används. Auktoriserings modeller visas nedan.

## <a name="hmac"></a>HMAC

Modellen för [auktoriserings modell](./rest-api-authorization-hmac.md) som är ASSOCIERAD med HMAC-autentisering delar upp behörigheter i skrivskyddad eller Läs-och Skriv behörighet. Mer information finns på sidan för [HMAC-auktorisering](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

Den [auktoriserings modell](./rest-api-authorization-azure-ad.md) som är kopplad till Azure Active Directory (Azure AD)-autentisering använder Azure RBAC för att kontrol lera behörigheter. Mer information finns på sidan [Azure AD-auktorisering](./rest-api-authorization-azure-ad.md) .
