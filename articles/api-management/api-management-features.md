---
title: Funktions-baserad jämförelse av Azure API Management-nivåerna | Microsoft Docs
description: Jämför API Management nivåer baserat på de funktioner som de erbjuder. Se en tabell som sammanfattar viktiga funktioner som är tillgängliga på varje pris nivå.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/15/2020
ms.author: apimpm
ms.openlocfilehash: eec913237ba978e52bb64fbd4c1f043a9214ffc8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92077855"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Funktions-baserad jämförelse av Azure API Management-nivåer

Varje API Management [pris nivå](https://aka.ms/apimpricing) erbjuder en distinkt uppsättning funktioner och per enhets [kapacitet](api-management-capacity.md). I följande tabell sammanfattas viktiga funktioner som är tillgängliga på varje nivå. Vissa funktioner kan fungera annorlunda eller ha olika funktioner beroende på nivån. I sådana fall kallas skillnaderna i dokumentations artiklarna som beskriver dessa enskilda funktioner.

> [!IMPORTANT]
> Observera att Developer-nivån är för användnings fall och utvärderings versioner som inte är i produktion. Den erbjuder inte service avtal.

| Funktion                                                                                      | Förbrukning | Utvecklare | Basic | Standard | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Azure AD-integrering<sup>1</sup>                                                             | Inga          | Ja       | Inga    | Ja      | Ja     |
| Stöd för Virtual Network (VNet)                                                               | Inga          | Ja       | Inga    | Inga       | Ja     |
| Distribution i flera regioner                                                                      | Inga          | Inga        | Inga    | Inga       | Ja     |
| Flera anpassade domän namn                                                                 | Inga          | Ja        | Inga    | Inga       | Ja     |
| Utvecklings Portal<sup>2</sup>                                                                 | Inga          | Ja       | Ja   | Ja      | Ja     |
| Inbyggd cache                                                                               | Inga          | Ja       | Ja   | Ja      | Ja     |
| Inbyggd analys                                                                           | Inga          | Ja       | Ja   | Ja      | Ja     |
| [Egen värd-Gateway](self-hosted-gateway-overview.md)<sup>3</sup>                           | Inga          | Ja       | Inga    | Inga       | Ja     |
| [TLS-inställningar](api-management-howto-manage-protocols-ciphers.md)                             | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Extern cache](./api-management-howto-cache-external.md)                                                    | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Autentisering av klient certifikat](api-management-howto-mutual-certificates-for-clients.md) | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Säkerhetskopiering och återställning](api-management-howto-disaster-recovery-backup-restore.md)               | Inga          | Ja       | Ja   | Ja      | Ja     |
| [Hantering över git](api-management-configuration-repository-git.md)                        | Inga          | Ja       | Ja   | Ja      | Ja     |
| API för direkt hantering                                                                        | Inga          | Ja       | Ja   | Ja      | Ja     |
| Azure Monitor loggar och mått                                                               | Ja         | Ja       | Ja   | Ja      | Ja     |
| Statisk IP                                                                                    | Inga          | Ja       | Ja   | Ja      | Ja     |

<sup>1</sup> gör det möjligt att använda Azure AD (och Azure AD B2C) som identitets leverantör för användare som loggar in på Developer-portalen.<br/>
<sup>2</sup> inklusive relaterade funktioner, t. ex. användare, grupper, problem, program och e-postmallar och meddelanden.<br/>
<sup>3</sup> i dina egen värdbaserade gatewayer för utvecklings nivån är begränsade till en enda Gateway-nod.<br/>