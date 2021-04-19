---
title: Azure HPC Cache användningsmodeller
description: Beskriver de olika cacheanvändningsmodellerna och hur du väljer bland dem för att ange skrivskyddade eller skrivskyddade cachelagring och styra andra inställningar för cachelagring
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/08/2021
ms.author: v-erkel
ms.openlocfilehash: 7e1b11fd15cca9b11fc627222318f08d31743336
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719195"
---
# <a name="understand-cache-usage-models"></a>Förstå cacheanvändningsmodeller

Med cacheanvändningsmodeller kan du anpassa hur Azure HPC Cache lagrar filer för att påskynda arbetsflödet.

## <a name="basic-file-caching-concepts"></a>Grundläggande begrepp för filcachelagring

Filcachelagring är Azure HPC Cache påskyndar klientbegäranden. Den använder följande grundläggande metoder:

* **Cachelagring av** läsning – Azure HPC Cache sparar en kopia av filer som klienter begär från lagringssystemet. Nästa gång en klient begär samma fil kan HPC Cache tillhandahålla versionen i cacheminnet i stället för att behöva hämta den från lagringssystemet på backend-nivå igen.

* **Cachelagring av skrivningar** – Om du Azure HPC Cache kan du lagra en kopia av ändrade filer som skickas från klientdatorerna. Om flera klienter gör ändringar i samma fil under en kort period kan cacheminnet samla in alla ändringar i cachen i stället för att behöva skriva varje ändring individuellt till backend-lagringssystemet.

  Efter en angiven tidsperiod utan ändringar flyttas filen till det långsiktiga lagringssystemet i cacheminnet.

  Om cachelagring av skrivningar är inaktiverat lagrar inte cacheminnet den ändrade filen och skriver den omedelbart till backend-lagringssystemet.

* **Fördröjning vid återskrivning** – För ett cacheminne med cachelagring av skrivning aktiverat är tillbakaskrivningsfördröjningen den tid som cacheminnet väntar på ytterligare filändringar innan filen kopieras till backend-lagringssystemet.

* **Backend-verifiering** – Inställningen för backend-verifiering avgör hur ofta cacheminnet jämför sin lokala kopia av en fil med fjärrversionen i backend-lagringssystemet. Om backend-kopian är nyare än den cachelagrade kopian hämtar cachen fjärrkopian och lagrar den för framtida begäranden.

  Inställningen för backend-verifiering visar när cachen *automatiskt* jämför sina filer med källfiler i fjärrlagring. Du kan dock tvinga Azure HPC Cache att jämföra filer genom att utföra en katalogåtgärd som innehåller en readdirplus-begäran. Readdirplus är ett NFS-standard-API (kallas även utökad läsning) som returnerar katalogmetadata, vilket gör att cachen jämför och uppdaterar filer.

De inbyggda användningsmodellerna Azure HPC Cache olika värden för de här inställningarna så att du kan välja den bästa kombinationen för din situation.

## <a name="choose-the-right-usage-model-for-your-workflow"></a>Välja rätt användningsmodell för arbetsflödet

Du måste välja en användningsmodell för varje NFS-protokolllagringsmål som du använder. Azure Blob Storage-mål har en inbyggd användningsmodell som inte kan anpassas.

HPC Cache användningsmodeller kan du välja hur du balanserar snabba svar med risken för att få inaktuella data. Om du vill optimera hastigheten för läsning av filer kanske du inte bryr dig om huruvida filerna i cacheminnet kontrolleras mot backend-filerna. Om du å andra sidan vill se till att dina filer alltid är uppdaterade med fjärrlagringen väljer du en modell som kontrollerar ofta.

Det här är alternativen för användningsmodellen:

* **Läs tunga, sfrekventa skrivningar** – Använd det här alternativet om du vill påskynda läsåtkomsten till filer som är statiska eller sällan har ändrats.

  Det här alternativet cachelagrar klientläsningar men cachelagrar inte skrivningar. Den skickar direkt skrivningar till backend-lagringen.
  
  Filer som lagras i cachen jämförs inte automatiskt med filerna på NFS-lagringsvolymen. (Läs beskrivningen av backend-verifiering ovan för att lära dig hur du jämför dem manuellt.)

  Använd inte det här alternativet om det finns en risk att en fil ändras direkt i lagringssystemet utan att först skriva den till cachen. Om det händer är den cachelagrade versionen av filen inte synkroniserad med backend-filen.

* **Mer än 15 % skrivningar** – det här alternativet påskyndar både läs- och skrivprestanda. När du använder det här alternativet måste alla klienter komma åt filer via Azure HPC Cache stället för att montera backend-lagringen direkt. De cachelagrade filerna har de senaste ändringarna som ännu inte har kopierats till backend-delen.

  I den här användningsmodellen kontrolleras endast filer i cacheminnet mot filerna på backend-lagring var åttonde timme. Den cachelagrade versionen av filen antas vara mer aktuell. En ändrad fil i cacheminnet skrivs till backend-lagringssystemet efter att den har varit i cacheminnet i 20 minuter<!-- an hour --> utan ytterligare ändringar.

* **Klienter skriver till NFS-målet och** kringgår cachen – Välj det här alternativet om några klienter i arbetsflödet skriver data direkt till lagringssystemet utan att först skriva till cachen, eller om du vill optimera datakonsekvensen. Filer som klienter begär cachelagras (läses), men eventuella ändringar av filerna från klienten (skrivningar) cachelagras inte. De skickas direkt till backend-lagringssystemet.

  Med den här användningsmodellen kontrolleras ofta filerna i cacheminnet mot backend-versionerna för uppdateringar – var 30:e sekund. Med den här verifieringen kan filer ändras utanför cacheminnet samtidigt som datakonsekvensen upprätthålls.

  > [!TIP]
  > De första tre grundläggande användningsmodellerna kan användas för att hantera de flesta Azure HPC Cache arbetsflöden. Nästa alternativ är för mindre vanliga scenarier.

* Större än 15 % skrivningar, kontrollerar att backing-servern har ändringar var **30:e** sekund och Större än 15 % skrivningar, kontrollerar att backing-servern har ändringar var **60:e** sekund – De här alternativen är utformade för arbetsflöden där du vill påskynda både läsningar och skrivningar, men det finns en risk att en annan användare skriver direkt till serverlagringssystemet. Om flera uppsättningar klienter till exempel arbetar med samma filer från olika platser kan dessa användningsmodeller vara meningsfulla för att balansera behovet av snabb filåtkomst med låg tolerans för inaktuellt innehåll från källan.

* **Mer än 15** % skrivningar, skriv tillbaka till servern var 30:e sekund – Det här alternativet är utformat för scenariot där flera klienter aktivt ändrar samma filer, eller om vissa klienter kommer åt serverlagringen direkt i stället för att montera cacheminnet.

  Vanliga skrivningar i backend-filer påverkar cacheprestanda, så du bör överväga att använda användningsmodellen Större än **15** % skrivningar om det finns en låg risk för filkonflikter, till exempel om du vet att olika klienter arbetar i olika områden i samma filuppsättning.

* **Läs hårt, kontrollerar backing-servern** var tredje timme – Det här alternativet prioriterar snabba läsningar på klientsidan, men uppdaterar  även cachelagrade filer från serverlagringssystemet regelbundet, till skillnad från användningsmodellen Läsa ofta.

I den här tabellen sammanfattas skillnaderna i användningsmodell:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

Om du har frågor om den bästa användningsmodellen för ditt Azure HPC Cache kan du prata med din Azure-representant eller öppna en supportbegäran om hjälp.

## <a name="know-when-to-remount-clients-for-nlm"></a>Veta när klienter ska återmonteras för NLM

I vissa situationer kan du behöva återmontera klienter om du ändrar användningsmodellen för ett lagringsmål. Detta är nödvändigt på grund av hur olika användningsmodeller hanterar nlm-begäranden (Network Lock Manager).

Den HPC Cache mellan klienter och backend-lagringssystemet. Vanligtvis skickar cacheminnet NLM-begäranden till backend-lagringssystemet, men i vissa fall bekräftar cachen själva NLM-begäran och returnerar ett värde till klienten. I Azure HPC Cache inträffar detta bara när du använder användningsmodellen Läs **tunga,** ovanliga skrivningar (eller med ett standardmål för bloblagring som inte har konfigurerbara användningsmodeller).

Det finns en liten risk för filkonflikt om du byter mellan användningsmodellen Läsa och sällan skrivningar och en annan användningsmodell.  Det går inte att överföra det aktuella NLM-tillståndet från cachen till lagringssystemet eller tvärtom. Klientens låsstatus är därför felaktig.

Återmontera klienterna för att se till att de har ett korrekt NLM-tillstånd med den nya låshanteraren.

Om klienterna skickar en NLM-begäran när användningsmodellen eller backend-lagringen inte stöder det, får de ett felmeddelande.

### <a name="disable-nlm-at-client-mount-time"></a>Inaktivera NLM vid klientmonteringstid

Det är inte alltid lätt att veta om dina klientsystem skickar NLM-begäranden.

Du kan inaktivera NLM när klienter monterar klustret med hjälp av ``-o nolock`` alternativet i ``mount`` kommandot .

Det exakta beteendet för alternativet beror på klientoperativsystemet, så läs monteringsdokumentationen ``nolock`` (man 5 nfs) för ditt klientoperativsystem. I de flesta fall flyttas låset lokalt till klienten. Var försiktig om programmet låser filer över flera klienter.

> [!NOTE]
> ADLS-NFS stöder inte NLM. Du bör inaktivera NLM med monteringsalternativet ovan när du använder ett ADLS-NFS-lagringsmål.

## <a name="next-steps"></a>Nästa steg

* [Lägga till lagringsmål](hpc-cache-add-storage.md) i Azure HPC Cache
