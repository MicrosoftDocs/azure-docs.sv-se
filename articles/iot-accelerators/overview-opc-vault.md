---
title: Vad är OPC Vault – Azure | Microsoft Docs
description: Den här artikeln innehåller en översikt över OPC-valvet. Den kan konfigurera, registrera och hantera certifikat livs cykeln för OPC UA-program i molnet.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 715ed204e28d6260c28fa099b40fc78aa12de44d
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646925"
---
# <a name="what-is-opc-vault"></a>Vad är OPC Vault?

> [!IMPORTANT]
> Medan vi uppdaterar den här artikeln kan du läsa mer i [Azures industriella IoT](https://azure.github.io/Industrial-IoT/) .

OPC-valvet är en mikrotjänst som kan konfigurera, registrera och hantera certifikat livs cykeln för OPC UA-Server och klient program i molnet. I den här artikeln beskrivs de enkla användnings fallen i OPC Vault.

## <a name="certificate-management"></a>Certifikathantering

Ett tillverknings företag måste till exempel ansluta sin OPC UA Server-dator till sitt nyskapade klient program. När tillverkaren får den första åtkomsten till serverdatorn visas ett fel meddelande omedelbart på OPC UA-serverprogrammet för att indikera att klient programmet inte är säkert. Den här mekanismen är inbyggd i OPC UA Server-datorn för att förhindra obehörig åtkomst till program, vilket förhindrar ond hackning på arbets ytan.

## <a name="application-security-management"></a>Hantering av program säkerhet
En säkerhetsrelaterad person använder OPC Vault mikrotjänst för att enkelt aktivera OPC UA-Server för kommunikation med alla klient program, eftersom OPC-valvet har alla funktioner för certifikat register, lagring och livs cykel hantering. Nu är OPC UA-servern ansluten, den kan kommunicera med det nyskapade klient programmet

## <a name="the-complete-opc-vault-architecture"></a>Den kompletta OPC Vault-arkitekturen
Följande diagram illustrerar den kompletta OPC Vault-arkitekturen.

![Arkitektur för OPC Vault](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om OPC-valvet och dess användning, är här det föreslagna nästa steg:

[Arkitektur för OPC Vault](overview-opc-vault-architecture.md)