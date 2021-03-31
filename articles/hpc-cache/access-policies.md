---
title: Använda åtkomst principer i Azure HPC-cache
description: Skapa och använda anpassade åtkomst principer för att begränsa klient åtkomsten till lagrings mål i Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: eb9e71cc8ec463077e3b12b8738203a4945a2eab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103471779"
---
# <a name="control-client-access"></a>Kontrol lera klient åtkomst

Den här artikeln förklarar hur du skapar och använder anpassade klient åtkomst principer för dina lagrings mål.

Klient åtkomst principer styr hur klienter får ansluta till lagrings målets exporter. Du kan styra saker som rot-squash och Läs-/skriv åtkomst på klient värden eller på nätverks nivå.

Åtkomst principer tillämpas på en namn områdes Sök väg, vilket innebär att du kan använda olika åtkomst principer för två olika exporter i ett NFS-filsystem.

Den här funktionen gäller för arbets flöden där du behöver styra hur olika grupper av klienter får åtkomst till lagrings målen.

Om du inte behöver detaljerad kontroll över lagrings mål åtkomsten kan du använda standard principen, eller så kan du anpassa standard principen med ytterligare regler. Om du till exempel vill aktivera rot-squash för alla klienter som ansluter via cachen, kan du redigera principen med namnet **default** för att lägga till inställningen root squash.

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

Den här inställningen styr hur Azure HPC cache behandlar begär Anden från rot användaren på klient datorer. När rot-squash har Aktiver ATS mappas rot användare från en klient automatiskt till en icke-privilegie rad användare när de skickar begär Anden via Azure HPC-cachen. Det förhindrar också att klient begär Anden använder set-UID-behörighet bitar.

Om rot-squash är inaktive rad skickas en begäran från klientens rot användare (UID 0) till ett Server dels-NFS-lagrings system som rot. Den här konfigurationen kan tillåta olämplig fil åtkomst.

Att ange rot-squash för klient begär Anden kan hjälpa dig att kompensera för den nödvändiga ``no_root_squash`` inställningen på NAS-system som används som lagrings mål. (Läs mer om [NFS-mål krav för lagring](hpc-cache-prerequisites.md#nfs-storage-requirements).) Det kan också förbättra säkerheten när den används med Azure Blob Storage-mål.

Om du aktiverar rot-squash måste du också ange användar värde för anonyma ID. Portalen accepterar heltals värden mellan 0 och 4294967295. (De gamla värdena-2 och-1 stöds för bakåtkompatibilitet, men rekommenderas inte för nya konfigurationer.)

Dessa värden mappas till vissa användar värden:

* **-2** eller **65534** (ingen)
* **-1** eller **65535** (ingen åtkomst)
* **0** (ej privilegie rad rot)

Ditt lagrings system kan ha andra värden med speciell innebörd.

## <a name="update-access-policies"></a>Uppdatera åtkomst principer

Du kan redigera eller ta bort åtkomst principer från tabellen på sidan **principer för klient åtkomst** .

Klicka på princip namnet för att öppna den för redigering.

Om du vill ta bort en princip markerar du kryss rutan bredvid namnet i listan och klickar sedan på knappen **ta bort** högst upp i listan. Det går inte att ta bort principen med namnet "default".

> [!NOTE]
> Du kan inte ta bort en åtkomst princip som används. Ta bort principen från alla namn områdes Sök vägar som innehåller den innan du försöker ta bort den.

## <a name="next-steps"></a>Nästa steg

* Använd åtkomst principer i namn områdes Sök vägar för dina lagrings mål. Läs [Konfigurera den](add-namespace-paths.md) sammanställda namn rymden för att lära dig hur.
