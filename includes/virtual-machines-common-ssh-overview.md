---
title: ta med fil
description: ta med fil
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/09/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 612e7dc2f3d1ef6aa5d3598999aa214a2f377e1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016383"
---
## <a name="overview-of-ssh-and-keys"></a>Översikt över SSH och nycklar

[SSH](https://www.ssh.com/ssh/) är ett krypterat anslutnings protokoll som ger säkra inloggningar över oskyddade anslutningar. SSH är standard anslutnings protokollet för virtuella Linux-datorer som finns i Azure. Även om SSH tillhandahåller en krypterad anslutning lämnar lösen ord med SSH-anslutningar fortfarande den virtuella datorn som är sårbar för angrepp med brute force. Vi rekommenderar att du ansluter till en virtuell dator via SSH med ett offentligt privat nyckel par, även kallat *SSH-nycklar*. 

- Den *offentliga nyckeln* placeras på din virtuella Linux-dator.

- Den *privata nyckeln* finns kvar i det lokala systemet. Skydda den privata nyckeln. Dela den inte med andra.

När du använder en SSH-klient för att ansluta till din virtuella Linux-dator (som har den offentliga nyckeln) testar den virtuella fjärrdatorn klienten för att kontrol lera att den har rätt privat nyckel. Om klienten har den privata nyckeln beviljas den åtkomst till den virtuella datorn. 

Beroende på din organisations säkerhets principer kan du återanvända ett enda offentligt-privat nyckel par för åtkomst till flera virtuella Azure-datorer och-tjänster. Du behöver inte ett separat nyckel par för varje virtuell dator eller tjänst som du vill komma åt. 

Din offentliga nyckel kan delas med vem som helst, men bara du (eller din lokala säkerhets infrastruktur) bör ha åtkomst till din privata nyckel.