---
title: Funktionsbaserad jämförelse av Azure-API Management på | Microsoft Docs
description: Jämför API Management nivåer baserat på de funktioner som de erbjuder. Se en tabell som sammanfattar de viktigaste funktionerna som är tillgängliga på varje prisnivå.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 04/13/2021
ms.author: apimpm
ms.openlocfilehash: f111729d7d7707ed4f40ce8f89ce76975fb47400
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536457"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Funktionsbaserad jämförelse av Azures API Management nivåer

Varje API Management [prisnivå erbjuder](https://aka.ms/apimpricing) en distinkt uppsättning funktioner och kapacitet per [enhet.](api-management-capacity.md) I följande tabell sammanfattas de viktigaste funktionerna som är tillgängliga i var och en av nivåerna. Vissa funktioner kan fungera annorlunda eller ha olika funktioner beroende på nivå. I sådana fall visas skillnaderna i dokumentationsartiklarna som beskriver dessa enskilda funktioner.

> [!IMPORTANT]
> Observera att developer-nivån är för icke-produktionsanvändningsfall och utvärderingar. Det erbjuder inte serviceavtal.

| Funktion                                                                                      | Förbrukning | Utvecklare | Basic | Standard | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Azure AD-integrering<sup>1</sup>                                                             | Inga          | Ja       | Inga    | Ja      | Ja     |
| Virtual Network (VNet)                                                               | Inga          | Ja       | Inga    | Inga       | Ja     |
| Distribution i flera regioner                                                                      | Inga          | Inga        | Inga    | Inga       | Ja     |
| Tillgänglighetszoner                                                                           | Inga          | Inga        | Inga    | Inga       | Ja     |
| Flera anpassade domännamn                                                                 | Inga          | Ja        | Inga    | Inga       | Ja     |
| Utvecklarportal<sup>2</sup>                                                                 | Inga          | Ja       | Ja   | Ja      | Ja     |
| Inbyggd cache                                                                               | Inga          | Ja       | Ja   | Ja      | Ja     |
| Inbyggd analys                                                                           | Inga          | Ja       | Ja   | Ja      | Ja     |
| [Gateway med egen värd](self-hosted-gateway-overview.md)<sup>3</sup>                           | Inga          | Ja       | Inga    | Inga       | Ja     |
| [TLS-inställningar](api-management-howto-manage-protocols-ciphers.md)                             | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Extern cache](./api-management-howto-cache-external.md)                                                    | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Autentisering av klientcertifikat](api-management-howto-mutual-certificates-for-clients.md) | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Säkerhetskopiering och återställning](api-management-howto-disaster-recovery-backup-restore.md)               | Inga          | Ja       | Ja   | Ja      | Ja     |
| [Hantering över Git](api-management-configuration-repository-git.md)                        | Inga          | Ja       | Ja   | Ja      | Ja     |
| API för direkthantering                                                                        | Inga          | Ja       | Ja   | Ja      | Ja     |
| Azure Monitor loggar och mått                                                               | Inga          | Ja       | Ja   | Ja      | Ja     |
| Statisk IP                                                                                    | Inga          | Ja       | Ja   | Ja      | Ja     |

<sup>1</sup> Tillåter användning av Azure AD (och Azure AD B2C) som identitetsprovider för användar inloggning på utvecklarportalen.<br/>
<sup>2</sup> Inklusive relaterade funktioner, t.ex. användare, grupper, problem, program, e-postmallar och meddelanden.<br/>
<sup>3</sup> På developer-nivån är gatewayer med egen värd begränsade till en enda gatewaynod.<br/>