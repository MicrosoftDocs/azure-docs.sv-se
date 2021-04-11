---
title: Återställa Azure-blobbar
description: Lär dig hur du återställer Azure-blobbar (i för hands version).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 4cbd47ea654115f00095e30f7d5114aec0f2c85a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746495"
---
# <a name="restore-azure-blobs-in-preview"></a>Återställa Azure-blobbar (i förhands granskning)

Block-blobar i lagrings konton med aktive rad drifts säkerhets kopiering kan återställas till vilken tidpunkt som helst inom kvarhållningsintervallet. Du kan också begränsa dina återställningar till alla block-blobar i lagrings kontot eller till en delmängd av blobbar.

## <a name="before-you-start"></a>Innan du börjar

- Blobbar kommer att återställas till samma lagrings konto. Därför kommer blobbar som har genomgått ändringar sedan den tid som du återställer att skrivas över.
- Endast block-blobar i ett standard lagrings konto för allmän användning v2 kan återställas som en del av en återställnings åtgärd. Det går inte att lägga till blobar, Page blob-och Premium block-blobar.
- När ett återställnings jobb pågår går det inte att läsa eller skriva blobar i lagringen.
- Det går inte att återställa en blob med ett aktivt lån. Om en blob med ett aktivt lån ingår i det intervall med blobbar som ska återställas, Miss kommer återställnings åtgärden automatiskt. Bryt eventuella aktiva lån innan du påbörjar återställnings åtgärden.
- Ögonblicks bilder skapas eller tas inte bort som en del av en återställnings åtgärd. Endast bas-bloben återställs till sitt tidigare tillstånd.
- Om du tar bort en behållare från lagrings kontot genom att anropa åtgärden **ta bort behållare** , kan den behållaren inte återställas med en återställnings åtgärd. Ta bort enskilda blobbar i stället för att ta bort en hel behållare om du vill återställa dem senare. Microsoft rekommenderar också att du aktiverar mjuk borttagning för behållare förutom den operativa säkerhets kopian för att skydda mot oavsiktlig borttagning av behållare.
- Se [support mat ris](blob-backup-support-matrix.md) för alla begränsningar och scenarier som stöds.

## <a name="restore-blobs"></a>Återställa blobbar

Du kan starta en återställning via backup Center.

1. I säkerhets kopierings Center går du till **Återställ** i det översta fältet.

    ![Återställa i säkerhets kopierings Center](./media/blob-restore/backup-center-restore.png)

1. På fliken **initiera återställning** väljer du **Azure-blobbar (Azure Storage)** som data källans typ och väljer den **säkerhets kopierings instans** som du vill återställa. Säkerhets kopierings instansen här är det lagrings konto som innehåller de blobbar som du vill återställa.

     ![Välj säkerhets kopierings instans](./media/blob-restore/select-backup-instance.png)

1. På fliken **Välj återställnings punkt** väljer du det datum och den tid som du vill återställa data från. Du kan också använda skjutreglaget för att välja tidpunkten för att återställa från. Informations bubblan bredvid datumet visar den giltiga varaktigheten från vilken du kan återställa dina data. Använd säkerhets kopiering för blobbar som kontinuerlig säkerhets kopiering ger dig detaljerad kontroll över punkter för att återställa data från.

    >[!NOTE]
    > Den tid som beskrivs här är din lokala tid.

    ![Datum och tid för återställning](./media/blob-restore/date-and-time.png)

1. På fliken **återställnings parametrar** väljer du om du vill återställa alla blobar i lagrings kontot, i vissa behållare eller i en delmängd av blobbar med prefix matchning. När du använder prefix matchning kan du ange upp till 10 intervall av prefix eller Sök vägar. Mer information om hur du använder prefix matchning [här](#use-prefix-match-for-restoring-blobs).

    ![Återställ parametrar](./media/blob-restore/restore-parameters.png)

    Välj något av följande alternativ:

    - **Återställ alla blobbar i lagrings kontot**: med det här alternativet återställs alla block-blobar i lagrings kontot genom att de återställs tillbaka till den valda tidpunkten. Det kan ta längre tid att återställa lagrings konton som innehåller stora mängder data eller vittnes en hög omsättning.

    - **Bläddra och Återställ valda behållare**: med det här alternativet kan du bläddra och välja upp till 10 behållare som ska återställas. Du måste ha behörighet att Visa behållare i lagrings kontot, annars kanske du inte kan se innehållet i lagrings kontot.

    - **Välj blobbar som ska återställas med prefix matchning**: med det här alternativet kan du återställa en delmängd av blobbar med en prefix matchning. Du kan ange upp till 10 lexicographical-intervall med blobbar i en enda behållare eller över flera behållare för att returnera dessa blobbar till sitt tidigare tillstånd vid en viss tidpunkt. Här följer några saker att tänka på:

        - Du kan använda ett snedstreck (/) för att avgränsa behållar namnet från BLOB-prefixet
        - Början av det angivna intervallet är inkluderat, men det angivna intervallet är exklusivt.

    Mer information om hur du använder prefix för att återställa BLOB-intervall finns i [det här avsnittet](#use-prefix-match-for-restoring-blobs).

1. När du är klar med att ange vilka blobbar som ska återställas fortsätter du till fliken **Granska + Återställ** och väljer **Återställ** för att starta återställningen.

1. Spåra återställning: Använd vyn **säkerhets kopierings jobb** för att spåra information och status för **återställningar**. Det gör du genom att gå till säkerhets kopierings **Center**  >  **säkerhets kopierings jobb**. Statusen visas **som pågående** när återställningen utförs.

    ![Fliken säkerhets kopierings jobb](./media/blob-restore/backup-jobs.png)

    När återställnings åtgärden har slutförts ändras statusen till **slutförd**. När återställningen har slutförts kan du läsa och skriva blobar i lagrings kontot igen.

## <a name="additional-topics"></a>Ytterligare information

### <a name="use-prefix-match-for-restoring-blobs"></a>Använd prefix matchning för återställning av blobbar

Se följande exempel:

![Återställ med prefix matchning](./media/blob-restore/prefix-match.png)

Återställnings åtgärden som visas i avbildningen utför följande åtgärder:

- Det återställer det fullständiga innehållet i *container1*.
- Den återställer blobbar i lexicographical-intervallet *blob1* via *blob5* i *container2*. Det här intervallet återställer blobbar med namn som *blob1*, *blob11*, *blob100*, *blob2* och så vidare. Eftersom slutet på intervallet är exklusivt återställs blobbar vars namn börjar med *blob4*, men återställer inte blobbar vars namn börjar med *blob5*.
- Den återställer alla blobbar i *container3* och *container4*. Eftersom slutet på intervallet är exklusivt återställs inte det här intervallet *container5*.

## <a name="next-steps"></a>Nästa steg

- [Översikt över drift säkerhets kopiering för Azure-blobar (i för hands version)](blob-backup-overview.md)
