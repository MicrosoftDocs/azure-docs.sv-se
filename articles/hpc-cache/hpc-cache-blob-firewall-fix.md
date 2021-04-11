---
title: Undvik inställningar för lagrings brand väggen
description: Nätverks brand Väggs inställningen för ett lagrings konto kan orsaka fel när du skapar ett Azure Blob Storage-mål i Azure HPC cache. Den här artikeln innehåller en lösning för begränsningen tills en program korrigering är på plats.
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2021
ms.author: v-erkel
ms.openlocfilehash: 10d68ce679fe42f5deeaae364bc46adb23436a27
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587159"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>Gå runt brandväggsinställningar för bloblagringskonto

En viss inställning som används i lagrings kontots brand väggar kan orsaka att det inte går att skapa Blob Storage-mål. Azure HPC cache-teamet arbetar med en program varu lösning för det här problemet, men du kan lösa det genom att följa anvisningarna i den här artikeln.

Brand Väggs inställningen som tillåter åtkomst enbart från valda nätverk kan förhindra att cachen skapar eller ändrar ett Blob Storage-mål. Den här konfigurationen finns på sidan Inställningar för lagrings kontots **brand väggar och virtuella nätverk** .

Problemet är att cache-tjänsten använder ett dolt tjänst nätverk som är skilt från kund miljöer. Det går inte att uttryckligen ge nätverket åtkomst till ditt lagrings konto.

När du skapar ett Blob Storage-mål använder Cache tjänsten det här nätverket för att kontrol lera om behållaren är tom eller inte. Om brand väggen inte tillåter åtkomst från det dolda nätverket, Miss lyckas kontrollen och det går inte att skapa lagrings målet.

Brand väggen kan även blockera ändringar i sökvägar för Blob Storage-målets namn område.

Undvik problemet genom att tillfälligt ändra brand Väggs inställningarna när du skapar lagrings målet:

1. Gå till sidan lagrings konto **brand väggar och virtuella nätverk** och ändra inställningen "Tillåt åtkomst från" till **alla nätverk**.
1. Skapa Blob Storage-målet i din Azure HPC-cache.
1. Skapa lagrings målets namn områdes sökväg.
1. När lagrings målet och sökvägen har skapats ändrar du kontots brand Väggs inställning tillbaka till **valda nätverk**.

Azure HPC cache använder inte tjänstens virtuella nätverk för att komma åt det färdiga lagrings målet.

[Kontakta Microsoft service och support](hpc-cache-support-ticket.md)om du behöver hjälp med den här lösningen.
