---
title: Användnings modeller för Azure HPC cache
description: Beskriver de olika användnings modellerna för cache och hur du väljer bland dem för att ange skrivskyddad eller Läs/skriv-cachelagring och kontrol lera andra inställningar för cachelagring
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: b23afb17b9b7152e82049ca4f6127e2811913296
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563461"
---
# <a name="understand-cache-usage-models"></a>Förstå användnings modeller för cache

Med användnings modeller för cache kan du anpassa hur Azure HPC cache lagrar filer för att påskynda arbets flödet.

## <a name="basic-file-caching-concepts"></a>Grundläggande metoder för cachelagring av filer

Cachelagring av filer är hur Azure HPC-cachen påskyndar klient förfrågningar. Den använder dessa grundläggande metoder:

* **Läsning av cachelagring** – Azure HPC cache behåller en kopia av filer som klienter begär från lagrings systemet. Nästa gång en klient begär samma fil kan HPC-cache tillhandahålla versionen i cacheminnet i stället för att behöva hämta den från Server dels lagrings systemet igen.

* **Skrivcache** – alternativt kan Azure HPC-cache lagra en kopia av eventuella ändrade filer som skickas från klient datorerna. Om flera klienter gör ändringar i samma fil under en kort period kan cachen samla in alla ändringar i cacheminnet i stället för att behöva skriva varje ändring individuellt till Server dels lagrings systemet.

  Efter en viss tid utan ändringar flyttar cacheminnet filen till det långsiktiga lagrings systemet.

  Om skrivcache är inaktiverat, lagrar cacheminnet inte den ändrade filen och skriver den omedelbart till Server dels lagrings systemet.

* **Skriv** åtgärds fördröjning – för en cache med skrivcache aktive rad är Skriv-tillbaka-fördröjning den tid som cachen väntar på ytterligare fil ändringar innan filen kopieras till Server delens lagrings system.

* **Verifiering på Server** sidan – inställningen för Server dels verifiering anger hur ofta cachen jämför sin lokala kopia av en fil med fjärrversionen på backend-startsystemet. Om backend-kopian är nyare än den cachelagrade kopian, hämtar cachen fjär kopian och lagrar den för framtida begär Anden.

  Verifierings inställningen för Server delen visar när cachen *automatiskt* jämför sina filer med källfiler i Fjärrlagring. Du kan dock tvinga Azure HPC-cache att jämföra filer genom att utföra en katalog åtgärd som innehåller en readdirplus-begäran. Readdirplus är ett standard-NFS-API (kallas även utökad läsning) som returnerar katalogens metadata, vilket gör att cachen jämför och uppdaterar filer.

De användnings modeller som är inbyggda i Azure HPC cache har olika värden för de här inställningarna så att du kan välja den bästa kombinationen för din situation.

## <a name="choose-the-right-usage-model-for-your-workflow"></a>Välj rätt användnings modell för ditt arbets flöde

Du måste välja en användnings modell för varje NFS-monterat lagrings mål som du använder. Azure Blob Storage-mål har en inbyggd användnings modell som inte kan anpassas.

Med användnings modeller för HPC-cache kan du välja hur du ska utjämna snabba svar med risken för att få inaktuella data. Om du vill optimera hastigheten för att läsa filer kanske du inte bryr dig om filerna i cacheminnet kontrol leras mot backend-filerna. Å andra sidan, om du vill vara säker på att filerna alltid är uppdaterade med Fjärrlagring, väljer du en modell som söker ofta.

Detta är alternativen för användnings modell:

* **Läs tung, ovanliga skrivningar** – Använd det här alternativet om du vill påskynda Läs åtkomsten till filer som är statiska eller sällan ändrade.

  Det här alternativet cachelagrar klient läsningar men cachelagrar inte skrivningar. Den skickar omedelbart skrivningar till backend-lagringen.
  
  Filer som lagras i cacheminnet jämförs inte automatiskt med filerna på NFS-lagrings volymen. (Läs beskrivningen av Server dels verifieringen ovan om du vill lära dig att jämföra dem manuellt.)

  Använd inte det här alternativet om det finns en risk att en fil kan ändras direkt på lagrings systemet utan att först skriva den till cacheminnet. Om det händer kommer den cachelagrade versionen av filen inte att synkroniseras med backend-filen.

* **Större än 15% skrivningar** – det här alternativet påskyndar både Läs-och skriv prestanda. När du använder det här alternativet måste alla klienter komma åt filer via Azure HPC-cachen i stället för att montera Server dels lagringen direkt. De cachelagrade filerna kommer att ha nya ändringar som ännu inte har kopierats till Server delen.

  I den här användnings modellen kontrol leras bara filer i cacheminnet mot filerna på backend-lagringsplatsen var åttonde timme. Den cachelagrade versionen av filen antas vara mer aktuell. En ändrad fil i cachen skrivs till Server dels lagrings systemet när den har varit i cacheminnet i 20 minuter<!-- an hour --> utan ytterligare ändringar.

* **Klienter skriver till NFS-målet, vilket kringgår cachen** – Välj det här alternativet om några klienter i arbets flödet skriver data direkt till lagrings systemet utan att först skriva till cachen eller om du vill optimera data konsekvens. Filer som klienten begär cachelagras (läsningar), men ändringar av filerna från klienten (skrivningar) cachelagras inte. De skickas direkt till Server dels lagrings systemet.

  Med den här användnings modellen kontrol leras ofta filerna i cacheminnet mot backend-versionerna för uppdateringar – var 30: e sekund. Den här verifieringen tillåter att filer ändras utanför cachen och samtidigt bibehåller sig data konsekvens.

  > [!TIP]
  > De första tre bas användnings modellerna kan användas för att hantera de flesta Azure HPC-arbetsflöden. Följande alternativ är för mindre vanliga scenarier.

* **Större än 15% skrivningar, kontroll av Server för ändringar var 30: e sekund** och **större än 15% skrivningar, kontroll av den säkerhetskopierade servern för ändringar var 60 sekund** : dessa alternativ är utformade för arbets flöden där du vill påskynda både läsningar och skrivningar, men det finns en risk att en annan användare skriver direkt till Server dels lagrings systemet. Om till exempel flera klient grupper arbetar med samma filer från olika platser, kan dessa användnings modeller vara begripliga för att balansera behovet av snabb fil åtkomst med låg tolerans för inaktuellt innehåll från källan.

* **Större än 15% skrivningar, skriv tillbaka till servern var 30: e sekund** – det här alternativet är utformat för scenariot där flera klienter aktivt ändrar samma filer, eller om vissa klienter får åtkomst till backend-lagringen direkt i stället för att montera cacheminnet.

  De frekventa backend-skrivningar påverkar cache-prestanda, så du bör överväga att använda **mer än 15% skrivningar** användnings modellen om det finns en låg risk för fil konflikter, till exempel om du vet att olika klienter arbetar i olika områden i samma fil uppsättning.

* **Läs tung, kontrol lera servern var 3: e timme – med** det här alternativet prioriteras snabba läsningar på klient sidan, men dessutom uppdateras cachelagrade filer från backend-lagrings systemet regelbundet, till skillnad från den **Read-och frekventa skrivningens** användnings modell.

I den här tabellen sammanfattas skillnaderna mellan användnings modeller:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

<!-- | Usage model                   | Caching mode | Back-end verification | Maximum write-back delay |
|-------------------------------|--------------|-----------------------|--------------------------|
| Read heavy, infrequent writes | Read         | Never                 | None                     |
| Greater than 15% writes       | Read/write   | 8 hours               | 20 minutes               |
| Clients bypass the cache      | Read         | 30 seconds            | None                     |
| Greater than 15% writes, frequent back-end checking (30 seconds) | Read/write | 30 seconds | 20 minutes |
| Greater than 15% writes, frequent back-end checking (60 seconds) | Read/write | 60 seconds | 20 minutes |
| Greater than 15% writes, frequent write-back | Read/write | 30 seconds | 30 seconds |
| Read heavy, checking the backing server every 3 hours | Read | 3 hours | None |
-->
Om du har frågor om den bästa användnings modellen för ditt Azure HPC cache-arbetsflöde, pratar du med Azure-representanten eller öppnar en support förfrågan om hjälp.

## <a name="next-steps"></a>Nästa steg

* [Lägg till lagrings mål](hpc-cache-add-storage.md) i Azure HPC-cachen
