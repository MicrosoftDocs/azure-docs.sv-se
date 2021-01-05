---
title: Använda åtkomst principer i Azure HPC-cache
description: Skapa och använda anpassade åtkomst principer för att begränsa klient åtkomsten till lagrings mål i Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 12/28/2020
ms.author: v-erkel
ms.openlocfilehash: 795b194eb7cd31e633128c22ddffe808b32e07da
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802428"
---
# <a name="use-client-access-policies"></a>Använd klient åtkomst principer

Den här artikeln förklarar hur du skapar och använder anpassade klient åtkomst principer för dina lagrings mål.

Klient åtkomst principer styr hur klienter kan ansluta till lagrings målets exporter. Du kan styra saker som rot-squash och Läs-/skriv åtkomst på klient värden eller på nätverks nivå.

Åtkomst principer tillämpas på en namn områdes Sök väg, vilket innebär att du kan använda olika åtkomst principer för två olika exporter i ett NFS-filsystem.

Den här funktionen gäller för arbets flöden där du behöver styra hur olika grupper av klienter får åtkomst till lagrings målen.

Om du inte behöver detaljerad kontroll över lagrings mål åtkomsten kan du använda standard principen, eller så kan du anpassa standard principen med ytterligare regler.

## <a name="create-a-client-access-policy"></a>Skapa en klient åtkomst princip

Använd sidan **principer för klient åtkomst** i Azure Portal för att skapa och hantera principer. <!-- is there AZ CLI for this? -->

[![skärm bild av sidan för klient åtkomst principer. Flera principer är definierade och vissa expanderas för att visa sina regler](media/policies-overview.png)](media/policies-overview.png#lightbox)

Varje princip består av regler. Reglerna tillämpas på värdar i ordning från det minsta omfånget (värd) till störst (standard). Den första regeln som matchar används och senare regler ignoreras.

Om du vill skapa en ny åtkomst princip klickar du på knappen **+ Lägg till åtkomst princip** överst i listan. Ge den nya åtkomst principen ett namn och ange minst en regel.

![skärm bild av bladet redigera åtkomst principer med flera regler ifyllda. Spara regeln genom att klicka på OK.](media/add-policy.png)

I resten av det här avsnittet förklaras de värden som du kan använda i regler.

### <a name="scope"></a>Omfång

Omfattnings termen och adress filtret fungerar tillsammans för att definiera vilka klienter som påverkas av regeln.

Använd dem för att ange om regeln gäller för en enskild klient (värd), ett intervall med IP-adresser (nätverk) eller alla klienter (standard).

Välj lämpligt **omfattnings** värde för regeln:

* **Värd** – regeln gäller för en enskild klient
* **Nätverk** – regeln gäller för klienter i ett intervall med IP-adresser
* **Standard** – regeln gäller för alla klienter.

Reglerna i en princip utvärderas i den ordningen. När en klient Mount-begäran matchar en regel ignoreras de andra.

### <a name="address-filter"></a>Adress filter

Värdet för **adress filtret** anger vilka klienter som matchar regeln.

Om du ställer in scope som **värd** kan du bara ange en IP-adress i filtret. För inställningen **standard** kan du inte ange några IP-adresser i fältet **adress filter** eftersom standard omfånget matchar alla klienter.

Ange IP-adressen eller adress intervallet för den här regeln. Använd CIDR-notering (exempel: 0.1.0.0/16) för att ange ett adress intervall.

### <a name="access-level"></a>Åtkomstnivå

Ange vilka behörigheter som ska ge klienterna som matchar omfånget och filtret.

Alternativen är **läsa/skriva**, **skrivskyddad** eller **Ingen åtkomst**.

### <a name="suid"></a>SUID

Markera rutan **suid** om du vill tillåta att filer i lagrings utrymmen anger användar-ID vid åtkomst.

SUID används vanligt vis för att öka användarens behörighet tillfälligt så att användaren kan utföra en uppgift som är relaterad till filen.

### <a name="submount-access"></a>Under monterings åtkomst

Markera den här kryss rutan om du vill tillåta att de angivna klienterna direkt monterar exportens under kataloger.

### <a name="root-squash"></a>Rot-squash

Välj om du vill ange rot-squash för klienter som matchar den här regeln.

Med det här värdet kan du tillåta rot-squash på lagrings export nivån. Du kan också [Ange rot-squash på cachenivå](configuration.md#configure-root-squash).

Om du aktiverar rot-squash måste du också ange användar värde för anonyma ID till något av följande alternativ:

* **-2** (ingen)
* **65534** (ingen)
* **-1** (ingen åtkomst)
* **65535** (ingen åtkomst)
* **0** (ej privilegie rad rot)

## <a name="update-access-policies"></a>Uppdatera åtkomst principer

Du kan redigera eller ta bort åtkomst principer från tabellen på sidan **principer för klient åtkomst** .

Klicka på princip namnet för att öppna den för redigering.

Om du vill ta bort en princip markerar du kryss rutan bredvid namnet i listan och klickar sedan på knappen **ta bort** högst upp i listan. Det går inte att ta bort principen med namnet "default".

> [!NOTE]
> Du kan inte ta bort en åtkomst princip som används. Ta bort principen från alla namn områdes Sök vägar som innehåller den innan du försöker ta bort den.

## <a name="next-steps"></a>Nästa steg

* Använd åtkomst principer i namn områdes Sök vägar för dina lagrings mål. Läs [Konfigurera den](add-namespace-paths.md) sammanställda namn rymden för att lära dig hur.
