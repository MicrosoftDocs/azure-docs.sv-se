---
title: Översikt över drift säkerhets kopiering för Azure-blobar
description: Lär dig om drift säkerhets kopiering för Azure-blobar (i för hands version).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 1f6bc98b92a2af4b05cae766a2186f2970b7133c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558762"
---
# <a name="overview-of-operational-backup-for-azure-blobs-in-preview"></a>Översikt över drift säkerhets kopiering för Azure-blobar (i för hands version)

Drift säkerhets kopiering för blobbar är en hanterad, lokal data skydds lösning som gör att du kan skydda dina block-blobar från olika data förlust scenarier som skador, BLOB-borttagningar och radering av lagrings kontot. Data lagras lokalt i själva käll lagrings kontot och kan återställas till en vald tidpunkt när det behövs. Det ger dig ett enkelt, säkert och kostnads effektivt sätt att skydda dina blobbar.

Den operativa säkerhets kopieringen för blobbar integreras med [Backup Center](backup-center-overview.md), bland annat funktioner för säkerhets kopierings hantering, för att tillhandahålla ett fönster med glas som kan hjälpa dig att styra, övervaka, hantera och analysera säkerhets kopior i stor skala.

## <a name="how-operational-backup-works"></a>Så här fungerar drift säkerhets kopiering

Drift säkerhets kopiering av blobbar är en **lokal säkerhets kopierings** lösning. Därför överförs inte säkerhetskopierade data till säkerhets kopierings valvet, utan lagras i själva käll lagrings kontot. Men säkerhets kopierings valvet fungerar fortfarande som enhet för att hantera säkerhets kopior. Detta är också en **kontinuerlig säkerhets kopierings** lösning, vilket innebär att du inte behöver schemalägga några säkerhets kopior och att alla ändringar kommer att behållas och återställas från statusen vid en vald tidpunkt.

Drift säkerhets kopiering använder BLOB-plattformens funktioner för att skydda dina data och tillåta återställning vid behov:

- **Återställning** av tidpunkt: [blobb-](../storage/blobs/point-in-time-restore-overview.md) till-tid-återställning gör det möjligt att återställa BLOB-data till ett tidigare tillstånd. Detta, i sin tur, använder **mjuk borttagning**, **ändring av flöde** och blob- **versioner** för att bevara data för den angivna varaktigheten. Med den operativa säkerhets kopieringen kan du aktivera återställning av tidpunkter och de underliggande funktionerna för att säkerställa att data bevaras under den angivna varaktigheten.

- **Ta bort lås**: borttagnings låset förhindrar att lagrings kontot tas bort av misstag eller av obehöriga användare. Den operativa säkerhets kopieringen som kon figurer ATS använder också automatiskt ett borttagnings lås för att minska risken för data förlust på grund av borttagning av lagrings kontot.

Se [support mat ris](blob-backup-support-matrix.md) för att lära dig mer om begränsningarna för den aktuella lösningen.

### <a name="protection"></a>Skydd

Den operativa säkerhets kopieringen konfigureras och hanteras på **lagrings konto** nivå och gäller för alla block-blobar i lagrings kontot. Använd säkerhets kopiering använder en **säkerhets kopierings princip** för att hantera varaktigheten för vilken säkerhetskopierade data (inklusive äldre versioner och borttagna blobbar) ska behållas, på det sättet som definierar den period som du kan återställa data från. Säkerhets kopierings policyn kan ha en maximal kvarhållning på 360 dagar eller motsvarande antal fullständiga veckor (51) eller månader (11).

När du konfigurerar säkerhets kopiering för ett lagrings konto och tilldelar en säkerhets kopierings princip med kvarhållning av "n" dagar anges de underliggande egenskaperna enligt beskrivningen nedan. Du kan visa dessa egenskaper på fliken **data skydd** i BLOB service i ditt lagrings konto.

- **Återställning** av tidpunkt: inställt på "n" dagar, enligt definitionen i säkerhets kopierings principen. Om lagrings kontot redan hade aktiverat punkt-i tid med kvarhållning, t. ex. "x" dagar, innan du konfigurerar säkerhets kopiering, anges varaktigheten för återställningen av tidpunkt till den större av de två värdena, som är max (n, x). Om du redan har aktiverat återställning vid tidpunkt och angett att kvarhållning ska vara större än det i säkerhets kopierings principen, förblir den oförändrad.

- **Mjuk borttagning**: Ställ in på "n + 5" dagar, det vill säga fem dagar utöver den varaktighet som anges i säkerhets kopierings principen. Om det lagrings konto som har kon figurer ATS för drift säkerhets kopiering redan hade mjuk borttagning aktiverat med kvarhållning av, säg "y" dagar, kommer den mjuka borttagningen att ställas in på max värdet för de två värdena, det vill säga Max (n + 5, y). Om du redan har aktiverat mjuk borttagning och angett att kvarhållning ska vara större än det enligt säkerhets kopierings principen, förblir den oförändrad.

- **Versions hantering för blobbar och blob Change-feed**: versions-och ändrings flöden har Aktiver ATS för lagrings konton som har kon figurer ATS för drift säkerhets kopiering.

- **Ta bort lås**: om du konfigurerar drift säkerhets kopiering på ett lagrings konto används även ett borttagnings lås på lagrings kontot. Borttagnings låset som används av säkerhets kopian kan visas under fliken **Lås** på lagrings kontot.

Om du vill tillåta säkerhets kopiering så att dessa egenskaper på lagrings kontona skyddas måste säkerhets kopierings valvet beviljas rollen **lagrings konto säkerhets kopierings deltagare** på respektive lagrings konto.

>[!NOTE]
>Drift säkerhets kopiering stöder endast åtgärder på block-blobbar och åtgärder på behållare kan inte återställas. Om du tar bort en behållare från lagrings kontot genom att anropa åtgärden **ta bort behållare** , kan den behållaren inte återställas med en återställnings åtgärd. Vi rekommenderar att du aktiverar mjuk borttagning för att förbättra skyddet av data och återställning.

### <a name="management"></a>Hantering

När du har aktiverat säkerhets kopiering på ett lagrings konto skapas en säkerhets kopierings instans som motsvarar lagrings kontot i säkerhets kopierings valvet. Du kan utföra alla säkerhetskopierade åtgärder för ett lagrings konto som att initiera återställningar, övervaka, stoppa skydd och så vidare genom dess motsvarande säkerhets kopierings instans.

Drift säkerhets kopiering integreras också direkt med Backup Center för att hjälpa dig att hantera skyddet av alla dina lagrings konton centralt, tillsammans med alla andra arbets belastningar som stöds av säkerhets kopiering. Backup Center är ditt enda fönster ruta för alla dina säkerhets kopierings krav som att övervaka jobb och tillstånd för säkerhets kopieringar och återställningar, säkerställa efterlevnad och styrning, analysera säkerhets kopierings användning och utföra åtgärder som rör säkerhets kopiering och återställning av data.

### <a name="restore"></a>Återställ

Du kan återställa data från alla tidpunkter som det finns en återställnings punkt för. En återställnings punkt skapas när ett lagrings konto är i skyddat tillstånd, och kan användas för att återställa data så länge det ligger i den kvarhållningsperiod som definieras av säkerhets kopierings principen (och därför funktionen för återställning av tidpunkter för BLOB service i lagrings kontot). Vid drift säkerhets kopiering används BLOB-återställning för att återställa data från en återställnings punkt.

Med den operativa säkerhets kopian kan du återställa alla block blobbar i lagrings kontot, bläddra bland och återställa vissa behållare eller använda prefix matchningar för att återställa en delmängd av blobbar. Alla återställningar kan endast utföras på käll lagrings kontot.

## <a name="pricing"></a>Priser

Du debiteras inga hanterings avgifter eller instans avgifter när du använder drift säkerhets kopiering för blobbar. Du kommer dock att debiteras följande avgifter:

- Återställningar görs med hjälp av återställnings-och ingångs kostnader för BLOB-platser baserat på mängden bearbetade data. Mer information finns i [prissättning för tidpunkts återställning](../storage/blobs/point-in-time-restore-overview.md#pricing-and-billing).

- Kvarhållning av data på grund av [mjuk borttagning för blobbar](../storage/blobs/soft-delete-blob-overview.md), [ändring av feed-stöd i Azure Blob Storage](../storage/blobs/storage-blob-change-feed.md)och [BLOB-versioner](../storage/blobs/versioning-overview.md).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera och hantera säkerhets kopiering av Azure-blobbar](blob-backup-configure-manage.md)