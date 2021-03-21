---
title: Översätt bakom brand väggar – översättare
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Translator kan översätta bakom brand väggar med antingen domän namn eller IP-filtrering.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: f5dd72328180574809c812d670f8165ad84963ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897755"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>Översätta bakom IP-brandväggar med Translator

Translator kan översätta bakom brand väggar med antingen domän namn eller IP-filtrering. Filtrering av domän namn är den bästa metoden. Vi **rekommenderar inte** att du kör Microsoft Translator bakom en IP-filtrerad brand vägg. Installations programmet kommer sannolikt att brytas i framtiden utan föregående meddelande.

## <a name="translator-ip-addresses"></a>Översättnings-IP-adresser
IP-adresserna för api.cognitive.microsofttranslator.com-Translator från och med den 21 augusti 2019:

* **Asien och Stillahavsområdet:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **Nordamerika:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Översätt bakom IP-brandväggar i Translator](reference/v3-0-translate.md)
