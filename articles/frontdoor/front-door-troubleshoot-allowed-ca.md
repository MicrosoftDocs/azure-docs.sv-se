---
title: Tillåten certifikat utfärdare för att aktivera anpassad HTTPS på Azures front dörr
description: Om du använder ett eget certifikat för att aktivera HTTPS på en anpassad domän i Azure-dörren, måste du använda en tillåten certifikat utfärdare (CA) för att skapa det.
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 20c5d611272ee2159ce8ddcc2865797a225a7ebb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91613687"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Tillåtna certifikat utfärdare för att aktivera anpassad HTTPS på Azures front dörr

När du [aktiverar https-funktionen med hjälp av ditt eget certifikat](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate) för en anpassad domän i Azure-dörren. Du behöver en tillåten certifikat utfärdare (CA) för att skapa TLS/SSL-certifikatet. Annars, om du använder en icke-tillåten CA eller ett självsignerat certifikat, kommer din begäran att avvisas.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
