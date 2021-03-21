---
title: Tillåtna certifikat utfärdare för Azure front dörr standard/Premium (för hands version)
description: I den här artikeln visas alla certifikat utfärdare som tillåts när du skapar ett eget certifikat.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: e31ad0734630fbd0b4960c26f8d562cea66ae675
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434875"
---
# <a name="allowed-certificate-authorities-for-azure-front-door-standardpremium-preview"></a>Tillåtna certifikat utfärdare för Azure front dörr standard/Premium (för hands version)

När du aktiverar HTTPS-funktionen med hjälp av ditt eget certifikat för en anpassad Azure front dörr standard/Premium-domän. Du behöver en tillåten certifikat utfärdare (CA) för att skapa TLS/SSL-certifikatet. Annars, om du använder en icke-tillåten CA eller ett självsignerat certifikat, kommer din begäran att avvisas.

[!INCLUDE [cdn-front-door-allowed-ca](../../../includes/cdn-front-door-allowed-ca.md)]
