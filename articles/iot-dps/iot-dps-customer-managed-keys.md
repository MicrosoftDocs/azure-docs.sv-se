---
title: Data kryptering i Azure Device Provisioning-tjänsten i vila via Kundhanterade nycklar | Microsoft Docs
description: Kryptering av data i vila med Kundhanterade nycklar för enhets etablerings tjänsten
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: d22a01bab81fc330484e7715a65c89a1cfd7802c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94967184"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Kryptering av data i vila med Kundhanterade nycklar för enhets etablerings tjänsten

## <a name="overview"></a>Översikt

Enhets etablerings tjänsten (DPS) stöder kryptering av data i vila med Kundhanterade nycklar (CMK), även kallat ta med din egen nyckel (BYOK). DPS tillhandahåller kryptering av data i vila och under överföring när de skrivs i våra data Center och dekrypterar dem åt dig, när du har åtkomst till den. Som standard använder DPS Microsoft-hanterade nycklar för att kryptera data i vila. Med CMK kan du få ett ytterligare lager av kryptering ovanpå standard plattforms kryptering genom att välja att kryptera data i vila med en nyckel krypterings nyckel, som hanteras via din [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Detta ger dig flexibiliteten att skapa, rotera, inaktivera och återkalla nycklar. Om CMK har kon figurer ATS för din DPS innebär det att dubbel kryptering är aktiverat med två skydds lager som aktivt skyddar dina data. 

Den här funktionen kräver att en ny DPS skapas. Kontakta oss via [Microsoft Support](https://azure.microsoft.com/support/create-ticket/)om du vill testa den här funktionen. Dela företagets namn och prenumerations-ID när du kontaktar Microsoft support.


## <a name="next-steps"></a>Nästa steg

* [Läs mer om enhets etablerings tjänsten](./index.yml)

* [Läs mer om Azure Key Vault](../key-vault/general/overview.md)