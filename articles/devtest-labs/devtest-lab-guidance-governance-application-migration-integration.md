---
title: Migrering och integrering av program i Azure DevTest Labs
description: Den här artikeln innehåller rikt linjer för styrning av Azure DevTest Labs infrastruktur i samband med migrering och integrering av program.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8d86728eefc46c74b49ac610e2207ce5e7ae6a9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87289352"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Styrning av Azure DevTest Labs infrastruktur – migrering och integrering av program
När din utvecklings-/test labb miljö har upprättats måste du tänka på följande frågor:

- Hur använder du miljön i ditt projekt team?
- Hur ser du till att du följer de organisations principer som krävs och att du får en smidigare att lägga till värde i ditt program?

## <a name="azure-marketplace-images-vs-custom-images"></a>Azure Marketplace-avbildningar eller anpassade avbildningar

### <a name="question"></a>Fråga
När ska jag använda en Azure Marketplace-avbildning eller en egen anpassad organisations avbildning?

### <a name="answer"></a>Svar
Azure Marketplace bör användas som standard om du inte har särskilda problem eller organisations krav. Några vanliga exempel är:

- Komplex program vara som kräver att ett program ingår som en del av bas avbildningen.
- Det kan ta flera timmar att installera och konfigurera ett program, vilket inte är en effektiv användning av beräknings tiden som ska läggas till på en Azure Marketplace-avbildning.
- Utvecklare och testare behöver snabbt åtkomst till en virtuell dator och vill minimera installations tiden för en ny virtuell dator.
- Efterlevnads-eller reglerings villkor (till exempel säkerhets principer) som måste finnas på plats för alla datorer.

Att använda anpassade bilder bör inte anses vara lätt. De introducerar extra komplexitet eftersom du nu måste hantera VHD-filer för de underliggande bas avbildningarna. Du måste också regelbundet korrigera de grundläggande avbildningarna med program uppdateringar. Dessa uppdateringar omfattar nya operativ Systems uppdateringar och uppdateringar eller konfigurations ändringar som krävs för själva programpaketet.

## <a name="formula-vs-custom-image"></a>Formel kontra anpassad bild

### <a name="question"></a>Fråga
När ska jag använda en formel eller en anpassad avbildning?

### <a name="answer"></a>Svar
Normalt är den avgörande faktorn i det här scenariot kostnad och åter användning.

Om du har ett scenario där många användare/labb kräver en avbildning med mycket program vara ovanpå bas avbildningen kan du minska kostnaderna genom att skapa en anpassad avbildning. Det innebär att avbildningen skapas en gång. Det minskar installations tiden för den virtuella datorn och kostnaden som uppstått på grund av att den virtuella datorn körs när installationen sker.

En extra faktor att notera är dock frekvensen av ändringar i ditt program varu paket. Om du kör dagliga versioner och kräver att programmet finns på användarnas virtuella datorer, bör du överväga att använda en formel i stället för en anpassad avbildning.

## <a name="use-custom-organizational-images"></a>Använd anpassade organisations avbildningar

### <a name="question"></a>Fråga
Hur kan jag skapa en lätt upprepnings bar process för att överföra mina anpassade organisations bilder till en DevTest Labs-miljö?

### <a name="answer"></a>Svar
Se [det här videoklippet om bild fabriks mönster](./devtest-lab-faq.md#blog-post). Det här scenariot är ett avancerat scenario och de angivna skripten är endast exempel skript. Om det krävs några ändringar måste du hantera och underhålla skripten som används i din miljö.

Använda DevTest Labs för att skapa en anpassad avbildnings pipeline i Azure-pipelines:

- [Introduktion: Hämta virtuella datorer på några minuter genom att konfigurera en avbildnings fabrik i Azure DevTest Labs](./devtest-lab-faq.md#blog-post)
- [Bild fabrik – del 2! Konfigurera Azure-pipelines och Factory Lab för att skapa virtuella datorer](./devtest-lab-faq.md#blog-post)
- [Bild fabrik – del 3: Spara anpassade bilder och distribuera dem till flera labb](./devtest-lab-faq.md#blog-post)
- [Video: anpassad avbildnings fabrik med Azure DevTest Labs](./devtest-lab-faq.md#blog-post)

## <a name="patterns-to-set-up-network-configuration"></a>Mönster för att konfigurera nätverks konfiguration

### <a name="question"></a>Fråga
Hur gör jag för att se till att det inte går att komma åt de virtuella datorerna i utvecklings-och test miljön? Finns det några rekommenderade mönster för att konfigurera nätverks konfigurationen?

### <a name="answer"></a>Svar
Ja. Det finns två aspekter att överväga – inkommande och utgående trafik.

**Inkommande trafik** – om den virtuella datorn inte har en offentlig IP-adress kan den inte nås via Internet. En vanlig metod är att se till att en princip för en prenumerations nivå anges, så att ingen användare kan skapa en offentlig IP-adress.

**Utgående trafik** – om du vill förhindra att virtuella datorer går direkt till offentlig Internet och tvinga trafik genom en företags brand vägg, kan du dirigera trafik lokalt via Express Route eller VPN genom att använda Tvingad routning.

> [!NOTE]
> Om du har en proxyserver som blockerar trafik utan proxyinställningar, ska du inte glömma att lägga till undantag till Labbets artefakt lagrings konto.

Du kan också använda nätverks säkerhets grupper för virtuella datorer eller undernät. Det här steget lägger till ett extra skydds lager för att tillåta/blockera trafik.

## <a name="new-vs-existing-virtual-network"></a>Nytt eller befintligt virtuellt nätverk

### <a name="question"></a>Fråga
När ska jag skapa ett nytt virtuellt nätverk för min DevTest Labs-miljö jämfört med att använda ett befintligt virtuellt nätverk?

### <a name="answer"></a>Svar
Om dina virtuella datorer behöver interagera med en befintlig infrastruktur bör du överväga att använda ett befintligt virtuellt nätverk i din DevTest Labs-miljö. Om du använder ExpressRoute kan du dessutom behöva minimera mängden virtuella nätverk/undernät så att du inte fragmenterar ditt IP-adressutrymme som tilldelas för användning i prenumerationerna. Du bör också överväga att använda VNet-peering-mönstret här (hubb-eker modell). Den här metoden aktiverar VNet/undernät-kommunikation mellan prenumerationer inom en angiven region, även om peering mellan regioner är en funktion i Azure-nätverk.

I annat fall kan varje DevTest Labs-miljö ha sitt eget virtuella nätverk. Observera dock att det finns [gränser](../azure-resource-manager/management/azure-subscription-service-limits.md) för antalet virtuella nätverk per prenumeration. Standardvärdet är 50, men den här gränsen kan höjas till 100.

## <a name="shared-public-or-private-ip"></a>Delad, offentlig eller privat IP-adress

### <a name="question"></a>Fråga
När ska jag använda en delad IP-adress eller offentlig IP-adress jämfört med privat IP?

### <a name="answer"></a>Svar
Om du använder en plats-till-plats-VPN eller Express Route bör du överväga att använda privata IP-adresser så att datorerna kan nås via det interna nätverket och inte kan nås via offentliga Internet.

> [!NOTE]
> Labb ägare kan ändra den här under näts principen så att ingen av misstag skapar offentliga IP-adresser för sina virtuella datorer. Prenumerationens ägare bör skapa en prenumerations princip som förhindrar att offentliga IP-adresser skapas.

När du använder delade offentliga IP-adresser delar de virtuella datorerna i ett labb en offentlig IP-adress. Den här metoden kan vara till hjälp när du behöver undvika att lösa begränsningar för offentliga IP-adresser för en specifik prenumeration.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Gränser för antalet virtuella datorer per användare eller labb

### <a name="question"></a>Fråga
Finns det en regel vad gäller hur många virtuella datorer som jag ska ange per användare, eller per labb?

### <a name="answer"></a>Svar
När du överväger antalet virtuella datorer per användare eller per labb finns det tre huvudsakliga problem:

- Den **totala kostnaden** som teamet kan spendera på resurser i labbet. Det är enkelt att sätta upp många datorer. För att kontrol lera kostnaderna är en mekanism att begränsa antalet virtuella datorer per användare och/eller per labb
- Det totala antalet virtuella datorer i ett labb påverkas av [prenumerations nivå kvoterna](../azure-resource-manager/management/azure-subscription-service-limits.md) som är tillgängliga. En av de övre gränserna är 800 resurs grupper per prenumeration. DevTest Labs skapar för närvarande en ny resurs grupp för varje virtuell dator (såvida inte delade offentliga IP-adresser används). Om det finns 10 labb i en prenumeration kan labben passa cirka 79 virtuella datorer i varje labb (800 övre gräns – 10 resurs grupper för de 10 labben själva) = 79 Virtual Machines per labb.
- Om labbet är anslutet till lokalt via Express Route (till exempel) finns det **definierade IP-** adressutrymme för VNet/under nätet. För att se till att det inte går att skapa virtuella datorer i labbet (fel: det går inte att hämta IP-adress) kan Lab-ägare ange maximalt antal virtuella datorer per labb med det tillgängliga IP-adressutrymmet.

## <a name="use-resource-manager-templates"></a>Använda Resource Manager-mallar

### <a name="question"></a>Fråga
Hur kan jag använda Resource Manager-mallar i min DevTest Labs-miljö?

### <a name="answer"></a>Svar
Du distribuerar dina Resource Manager-mallar till en DevTest labb miljö med hjälp av de steg som beskrivs i [miljö funktionen i DevTest Labs](devtest-lab-test-env.md) -artikeln. I princip kan du kontrol lera dina Resource Manager-mallar i en git-lagringsplats (antingen Azure databaser eller GitHub) och lägga till ett [privat lager för dina mallar](devtest-lab-test-env.md) i labbet.

Det här scenariot kanske inte är användbart om du använder DevTest Labs för att hantera utvecklings datorer, men det kan vara användbart om du skapar en fristående miljö som är representativ för produktion.

Det är också värt att Observera att alternativet antal virtuella datorer per labb eller per användare endast begränsar antalet datorer internt som skapats i själva labbet, och inte av några miljöer (Resource Manager-mallar).

## <a name="next-steps"></a>Nästa steg
Se [använda miljöer i DevTest Labs](devtest-lab-test-env.md).
