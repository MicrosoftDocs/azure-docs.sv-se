---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 393b29245141b2970e7c1a227d6e8b1b131c445c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93061664"
---
Om du vill skapa en P2S-anslutning från en annan klientdator än den som du använde för att generera klientcertifikat, måste du installera ett klientcertifikat. När du installerar ett klientcertifikat behöver du lösenordet som skapades när klientcertifikatet exporterades.

1. Leta upp och kopiera *.pfx*-filen till klientdatorn. Installera *.pfx*-filen genom att dubbelklicka på den på klientdatorn. Lämna **lagrings platsen** som **Aktuell användare** och välj sedan **Nästa**.
1. Gör inga ändringar på sidan för den **Fil** som ska importeras. Välj **Nästa**.
1. På sidan **skydd av privat nyckel** anger du lösen ordet för certifikatet eller kontrollerar att säkerhetsobjektet är korrekt och väljer sedan **Nästa**.
1. På sidan **certifikat Arkiv** lämnar du standard platsen och väljer sedan **Nästa**.
1. Välj **Slutför**. Välj **Ja** i **säkerhets varningen** för certifikat installationen. Du kan bekvämt välja Ja för den här säkerhets varningen eftersom du har genererat certifikatet.
1. Certifikatet har nu importerats.
