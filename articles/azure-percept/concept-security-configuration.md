---
title: Konfiguration och säkerhets rekommendationer för Azure percept-brandväggen
description: Läs mer om konfiguration av och säkerhets rekommendationer för Azure percept-brandväggen
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: cfc20a30104e24a3950c71bdd8377544803d2f25
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604421"
---
# <a name="azure-percept-firewall-configuration-and-security-recommendations"></a>Konfiguration och säkerhets rekommendationer för Azure percept-brandväggen

Läs rikt linjerna nedan om du vill ha information om hur du konfigurerar brand väggar och allmänna säkerhets metoder med Azure percept.

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Konfigurera brand väggar för Azure percept DK

Om nätverks konfigurationen kräver att du uttryckligen tillåter anslutningar från Azure percept DK-enheter kan du läsa igenom följande lista över komponenter.

Den här check listan är en start punkt för brand Väggs regler:

|URL (* = jokertecken)|Utgående TCP-portar|Användning|
|-------------------|------------------|---------|
|*. auth.azureperceptdk.azure.net|443|Azure DK som autentisering och auktorisering|
|*. auth.projectsantacruz.azure.net|443|Azure DK som autentisering och auktorisering|

Granska dessutom listan över anslutningar som [används av Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/production-checklist#allow-connections-from-iot-edge-devices).

## <a name="additional-recommendations-for-deployment-to-production"></a>Ytterligare rekommendationer för distribution till produktion

Azure percept DK erbjuder ett stort utbud av säkerhetsfunktionerna i kartongen. Förutom de kraftfulla säkerhetsfunktioner som ingår i den aktuella versionen, rekommenderar Microsoft även följande rikt linjer när du överväger produktions distributioner:

- Starkt fysiskt skydd av själva enheten
- Se till att data-at-rest-kryptering är aktiverat
- Övervaka kontinuerligt enhets position och svara snabbt på aviseringar
- Begränsa antalet administratörer som har åtkomst till enheten

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure percept Security](./overview-percept-security.md)