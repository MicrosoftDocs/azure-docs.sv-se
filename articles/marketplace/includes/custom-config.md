---
title: ta med fil
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: a1bc7cf1fd339ca3660c7b39326f37d2763c74b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92284831"
---
Om du behöver ytterligare konfiguration använder du en schemalagd aktivitet som körs vid start för att göra slutliga ändringar i den virtuella datorn efter att den har distribuerats. Tänk också på följande:

- Om det är en körnings aktivitet ska aktiviteten Ta bort sig själv när den har slutförts.
- Konfigurationer bör inte förlita sig på andra enheter än C eller D, eftersom endast dessa två enheter alltid garanterar att de finns (enhet C är operativ system disken och enhet D är den temporära lokala disken).

Mer information om Linux-anpassningar finns i [tillägg och funktioner för virtuella datorer för Linux](../../virtual-machines/extensions/features-linux.md).