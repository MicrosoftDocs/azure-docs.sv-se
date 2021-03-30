---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "67187974"
---
SPF-poster (Sender Policy Framework) används för att ange vilka e-postservrar som kan skicka e-post på uppdrag av ett domän namn. Korrekt konfiguration av SPF-poster är viktigt för att förhindra mottagare från att markera din e-post som skräp post.

DNS-RFC: er introducerade ursprungligen en ny SPF-posttyp för att stödja det här scenariot. För att stödja äldre namnservrar, tillåts de också använda TXT-posttypen för att ange SPF-poster. Denna tvetydighet ledde till förvirring, som löstes av [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1). Det anger att SPF-poster måste skapas med hjälp av post typen TXT. Det anger också att SPF-posttypen är föråldrad.

**SPF-poster stöds av Azure DNS och måste skapas med hjälp av TXT-posttypen.** Den föråldrade SPF-posttypen stöds inte. När du [importerar en DNS-zonfil](../articles/dns/dns-import-export.md)konverteras alla SPF-poster som använder typen SPF-post till post typen txt.
