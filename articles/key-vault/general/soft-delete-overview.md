---
title: Azure Key Vault mjuk borttagning | Microsoft Docs
description: Med mjuk borttagning i Azure Key Vault kan du återställa borttagna nyckelvalv och nyckelvalvsobjekt, till exempel nycklar, hemligheter och certifikat.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.openlocfilehash: 52cd7742f3c6961350f907cde8ffe19235cff9b8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753262"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Översikt av mjuk borttagning för Azure Key Vault

> [!IMPORTANT]
> Du måste aktivera mjuk borttagning för dina nyckelvalv omedelbart. Möjligheten att välja bort mjuk borttagning kommer snart att bli inaktuell. Fullständig information [finns här](soft-delete-change.md)

> [!IMPORTANT]
> Mjukt borttagna valvutlösare tar bort inställningar för integrerade med Key Vault tjänster, t.ex. rolltilldelningar för Azure RBAC Event Grid prenumerationer. Efter återställning av mjukt borttagna Key Vault för integrerade tjänster måste återskapas manuellt. 

Key Vault funktionen mjuk borttagning gör det möjligt att återställa borttagna valv och borttagna nyckelvalvsobjekt (till exempel nycklar, hemligheter, certifikat), som kallas mjuk borttagning. Mer specifikt tar vi upp följande scenarier: Det här skyddet erbjuder följande skydd:

- När en hemlighet, nyckel, ett certifikat eller ett nyckelvalv har tagits bort förblir det återställningsbart under en konfigurerbar period på 7 till 90 kalenderdagar. Om ingen konfiguration anges anges standardåterställningsperioden till 90 dagar. Detta ger användarna tillräckligt med tid för att lägga märke till en oavsiktlig borttagning och svara på en hemlighet.
- Två åtgärder måste göras för att permanent ta bort en hemlighet. Först måste en användare ta bort objektet, vilket försätter det i läget för mjuk borttagning. För det andra måste en användare rensa objektet i läget för mjuk borttagning. Rensningsåtgärden kräver ytterligare behörigheter för åtkomstprincipen. Dessa ytterligare skydd minskar risken för att en användare oavsiktligt eller illvilligt tar bort en hemlighet eller ett nyckelvalv.  
- Om du vill rensa en hemlighet i läget för mjuk borttagning måste tjänstens huvudnamn beviljas ytterligare behörighet för "rensa" åtkomstprincip. Behörigheten rensa åtkomstprincip beviljas inte som standard till tjänstens huvudnamn, inklusive nyckelvalv och prenumerationsägare och måste anges avsiktligt. Genom att kräva en förhöjd åtkomstprincipbehörighet för att rensa en mjukt borttagna hemlighet minskar sannolikheten för att en hemlighet tas bort av misstag.

## <a name="supporting-interfaces"></a>Stödgränssnitt

Funktionen för mjuk borttagning är tillgänglig [via gränssnitten REST API](/rest/api/keyvault/), [Azure CLI,](./key-vault-recovery.md) [Azure PowerShell](./key-vault-recovery.md)och [.NET/C#,](/dotnet/api/microsoft.azure.keyvault) samt [ARM-mallar.](/azure/templates/microsoft.keyvault/2019-09-01/vaults)

## <a name="scenarios"></a>Scenarier

Azure Key Vaults spåras resurser som hanteras av Azure Resource Manager. Azure Resource Manager också ett väldefinierat beteende för borttagning, vilket kräver att en lyckad DELETE-åtgärd måste resultera i att resursen inte längre är tillgänglig. Funktionen för mjuk borttagning åtgärdar återställningen av det borttagna objektet, oavsett om borttagningen var oavsiktlig eller avsiktlig.

1. I det vanliga scenariot kan en användare oavsiktligt ha tagit bort ett nyckelvalv eller ett nyckelvalvsobjekt. Om nyckelvalvet eller nyckelvalvsobjektet skulle kunna återställas under en fördefinierad period kan användaren ångra borttagningen och återställa sina data.

2. I ett annat scenario kan en obehörig användare försöka ta bort ett nyckelvalv eller ett nyckelvalvsobjekt, till exempel en nyckel i ett valv, för att orsaka verksamhetsavbrott. Att separera borttagningen av nyckelvalvet eller nyckelvalvsobjektet från den faktiska borttagningen av underliggande data kan användas som en säkerhetsåtgärd genom att till exempel begränsa behörigheter för databorttagning till en annan betrodd roll. Den här metoden kräver i praktiken kvorum för en åtgärd som annars kan leda till en omedelbar dataförlust.

### <a name="soft-delete-behavior"></a>Beteende vid mjuk borttagning

När mjuk borttagning är aktiverat bevaras resurser som markerats som borttagna resurser under en angiven period (90 dagar som standard). Tjänsten tillhandahåller ytterligare en mekanism för att återställa det borttagna objektet, vilket i princip ångrar borttagningen.

När du skapar ett nytt nyckelvalv är mjuk borttagning på som standard. Du kan skapa ett nyckelvalv utan mjuk borttagning via [Azure CLI eller](./key-vault-recovery.md) [Azure PowerShell](./key-vault-recovery.md). När mjuk borttagning har aktiverats för ett nyckelvalv kan det inte inaktiveras

Standardperioden för kvarhållning är 90 dagar, men när nyckelvalvet skapas är det möjligt att ange kvarhållningsprincipintervallet till ett värde från 7 till 90 dagar Azure Portal. Bevarandeprincipen för rensningsskydd använder samma intervall. När det har angetts går det inte att ändra kvarhållningsprincipintervallet.

Du kan inte återanvända namnet på ett nyckelvalv som har mjuk borttagning förrän kvarhållningsperioden har passerat.

### <a name="purge-protection"></a>Rensningsskydd

Rensningsskydd är ett valfritt Key Vault beteende och är **inte aktiverat som standard.** Rensningsskydd kan bara aktiveras när mjuk borttagning har aktiverats.  Den kan aktiveras via [CLI eller](./key-vault-recovery.md?tabs=azure-cli) [PowerShell](./key-vault-recovery.md?tabs=azure-powershell).

När rensningsskydd är på kan ett valv eller ett objekt i borttagna tillstånd inte rensas förrän kvarhållningsperioden har passerat. Mjukt borttagna valv och objekt kan fortfarande återställas, vilket säkerställer att bevarandeprincipen följs.

Standardvärdet för kvarhållningsperioden är 90 dagar, men det är möjligt att ange kvarhållningsprincipintervallet till ett värde från 7 till 90 dagar Azure Portal. När kvarhållningsprincipintervallet har angetts och sparats går det inte att ändra det för valvet.

### <a name="permitted-purge"></a>Tillåten rensning

Permanent borttagning, rensning och borttagning av ett nyckelvalv är möjligt via en POST-åtgärd på proxyresursen och kräver särskilda privilegier. I allmänhet kan endast prenumerationsägaren rensa ett nyckelvalv. POST-åtgärden utlöser den omedelbara och oåterkalleliga borttagningen av valvet. 

Undantag är:
- När Azure-prenumerationen har markerats som *ouppklarbar.* I det här fallet kan endast tjänsten utföra den faktiska borttagningen och göra det enligt en schemalagd process. 
- När `--enable-purge-protection flag` är aktiverat på själva valvet. I det här Key Vault vänta i 90 dagar från det att det ursprungliga hemliga objektet markerades för borttagning för att ta bort objektet permanent.

Anvisningar finns i Så här använder du Key Vault mjuk borttagning med [CLI:](./key-vault-recovery.md?tabs=azure-cli#key-vault-cli) Rensa ett nyckelvalv eller Så här använder du [Key Vault mjuk borttagning med PowerShell: Rensa ett nyckelvalv.](./key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)

### <a name="key-vault-recovery"></a>Återställning av nyckelvalv

När du tar bort ett nyckelvalv skapar tjänsten en proxyresurs under prenumerationen och lägger till tillräckliga metadata för återställning. Proxyresursen är ett lagrat objekt som finns på samma plats som det borttagna nyckelvalvet. 

### <a name="key-vault-object-recovery"></a>Återställning av Key Vault-objekt

När du tar bort ett nyckelvalvsobjekt, till exempel en nyckel, placerar tjänsten objektet i ett borttagbart tillstånd, vilket gör det otillgängligt för alla hämtningsåtgärder. I det här tillståndet kan nyckelvalvsobjektet endast visas, återställas eller tas bort med tvång/permanent. Om du vill visa objekten använder du Azure `az keyvault key list-deleted` [CLI-kommandot](./key-vault-recovery.md)(som beskrivs i Så här använder du Key Vault mjuk borttagning med CLI ), eller parametern Azure PowerShell (enligt beskrivningen i Så här använder du `-InRemovedState` Key Vault mjuk borttagning med [PowerShell](./key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)).  

Samtidigt schemalägger Key Vault borttagning av underliggande data som motsvarar det borttagna nyckelvalvet eller nyckelvalvsobjektet för körning efter ett förbestämt kvarhållningsintervall. DNS-posten som motsvarar valvet bevaras också under kvarhållningsintervallet.

### <a name="soft-delete-retention-period"></a>Kvarhållningsperiod för mjuk borttagning

Mjukt borttagna resurser bevaras under en viss tidsperiod, 90 dagar. Under kvarhållningsintervallet för mjuk borttagning gäller följande:

- Du kan lista alla nyckelvalv och nyckelvalvsobjekt i mjuk borttagningstillstånd för din prenumeration samt åtkomstborttagning och återställningsinformation om dem.
  - Endast användare med särskilda behörigheter kan lista borttagna valv. Vi rekommenderar att våra användare skapar en anpassad roll med dessa särskilda behörigheter för hantering av borttagna valv.
- Det går inte att skapa ett nyckelvalv med samma namn på samma plats. På motsvarande sätt går det inte att skapa ett nyckelvalvsobjekt i ett visst valv om nyckelvalvet innehåller ett objekt med samma namn och som är i ett borttagna tillstånd.
- Endast en specifikt privilegierad användare kan återställa ett nyckelvalv eller nyckelvalvsobjekt genom att utfärda ett återställningskommando på motsvarande proxyresurs.
  - Användaren, medlem i den anpassade rollen, som har behörighet att skapa ett nyckelvalv under resursgruppen kan återställa valvet.
- Endast en specifikt privilegierad användare kan framtrigt ta bort ett nyckelvalv eller nyckelvalvsobjekt genom att utfärda ett borttagningskommando på motsvarande proxyresurs.

Om inte ett nyckelvalv eller nyckelvalvsobjekt återställs utför tjänsten i slutet av kvarhållningsintervallet en rensning av det mjukt borttagna nyckelvalvet eller nyckelvalvsobjektet och dess innehåll. Resursborttagning kanske inte schemalades om.

### <a name="billing-implications"></a>Faktureringskonsekvenser

När ett objekt (ett nyckelvalv eller en nyckel eller en hemlighet) i allmänhet är i borttagna tillstånd finns det bara två möjliga åtgärder: rensa och återställ. Alla andra åtgärder misslyckas. Det innebär att även om objektet finns kan inga åtgärder utföras och därför sker ingen användning, så ingen faktura. Det finns dock följande undantag:

- Åtgärderna "rensa" och "återställ" räknas mot normala åtgärder i nyckelvalvet och faktureras.
- Om objektet är en HSM-nyckel debiteras avgiften "HSM Protected key" per nyckelversion och månad om en nyckelversion har använts under de senaste 30 dagarna. Efter det kan inga åtgärder utföras mot objektet eftersom objektet är i borttagna tillstånd, så inga avgifter tillkommer.

## <a name="next-steps"></a>Nästa steg

Följande två guider erbjuder de primära användningsscenarierna för att använda mjuk borttagning.

- [Så här använder Key Vault mjuk borttagning med portalen](./key-vault-recovery.md?tabs=azure-portal)
- [Så här använder du Key Vault mjuk borttagning med PowerShell](./key-vault-recovery.md) 
- [Så här använder du Key Vault mjuk borttagning med CLI](./key-vault-recovery.md)
