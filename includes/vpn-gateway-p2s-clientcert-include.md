---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.openlocfilehash: 34986ac80a309bcfd495e5782496ba560f84c5f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041560"
---
Varje klientdator som du ansluter till ett virtuellt nätverk med punkt-till-plats-anslutning måste ha ett klientcertifikat installerat. Du kan generera det från rotcertifikatet och installera det på varje klientdator. Om du inte installerar ett giltigt klientcertifikat misslyckas autentiseringen när klienten försöker ansluta till det virtuella nätverket.

Du kan antingen generera ett unikt certifikat för varje klient eller använda samma certifikat för flera klienter. Fördelen med att generera unika klientcertifikat är möjligheten att återkalla ett enskilt certifikat. Om flera klienter i stället använder samma klientcertifikat och du behöver återkalla det, så måste du generera och installera nya certifikat för varje klient som använder certifikatet.

Du kan generera klientcertifikat på följande sätt:

* **Företags certifikat:**

  * Om du använder en företags certifikat lösning genererar du ett klient certifikat med det unika namn värde formatet *namn \@ yourdomain.com*. Använd det här formatet i stället för formatet *domännamn\användarnamn*.

  * Se till att klientcertifikatet baseras på en användarcertifikatmall där *Klientautentisering* är den första posten i användningslistan. Du kan kontrollera certifikatet genom att dubbelklicka på det och visa **Förbättrad nyckelanvändning** på fliken **Information**.

* **Självsignerat rot certifikat:** Följ stegen i någon av följande artiklar om P2S-certifikat så att de klient certifikat som du skapar är kompatibla med dina P2S-anslutningar.

  När du skapar ett klientcertifikat från ett självsignerat rotcertifikat installeras det automatiskt på den dator som du använde för att skapa det. Om du vill installera klientcertifikatet på en annan klientdator ska du exportera det som .pfx tillsammans med hela certifikatkedjan. Då skapas en .pfx-fil som innehåller den rotcertifikatinformation som krävs för att autentisera klienten.

  Stegen i de här artiklarna genererar ett kompatibelt klient certifikat, som du sedan kan exportera och distribuera.

  * [Windows 10 PowerShell-anvisningar](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): För de här anvisningarna krävs Windows 10 och PowerShell för att skapa certifikat. Certifikaten som skapas kan installeras på valfri P2S-klient som stöds.

  * [MakeCert-anvisningar](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Använd MakeCert om du inte har åtkomst till en Windows 10-dator för att skapa certifikat. MakeCert är inaktuellt, men du kan fortfarande använda det för att generera certifikat. Du kan installera de genererade certifikaten på valfri P2S-klient som stöds.

  * [Linux-instruktioner](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md).