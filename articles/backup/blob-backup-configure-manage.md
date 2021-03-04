---
title: Konfigurera drift säkerhets kopiering för Azure-blobar
description: Lär dig hur du konfigurerar och hanterar drift säkerhets kopiering för Azure-blobar (i för hands version)
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 0dc490842389ba9286799aef5d37c1cf7c1ba64e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051080"
---
# <a name="configure-operational-backup-for-azure-blobs-in-preview"></a>Konfigurera drift säkerhets kopiering för Azure-blobar (i för hands version)

Med Azure Backup kan du enkelt konfigurera drift säkerhets kopiering för att skydda block blobbar i dina lagrings konton. Den här artikeln förklarar hur du konfigurerar drift säkerhets kopiering på ett eller flera lagrings konton med hjälp av Azure Portal. I artikeln beskrivs följande:

- Saker att känna till innan du börjar
- Skapa ett säkerhets kopierings valv
- Bevilja behörighet till säkerhets kopierings valvet på de lagrings konton som ska skyddas
- Skapa en säkerhets kopierings princip
- Konfigurera drift säkerhets kopiering på ett eller flera lagrings konton
- Effekter på säkerhets kopieringen av lagrings konton

## <a name="before-you-start"></a>Innan du börjar

- Drift säkerhets kopiering av blobbar är en lokal säkerhets kopierings lösning som upprätthåller data för en angiven varaktighet i själva käll lagrings kontot. Den här lösningen underhåller inte ytterligare en kopia av data i valvet.
- Med den här lösningen kan du behålla dina data för återställning i upp till 360 dagar. Långa bevarande varaktigheter kan dock leda till längre tid under återställnings åtgärden.
- Lösningen kan bara användas för att utföra återställningar till käll lagrings kontot och kan resultera i att data skrivs över.
- Om du tar bort en behållare från lagrings kontot genom att anropa åtgärden ta bort behållare, kan den behållaren inte återställas med en återställnings åtgärd. Ta bort enskilda blobbar i stället för att ta bort en hel behållare om du vill återställa dem senare. Microsoft rekommenderar också att du aktiverar mjuk borttagning för behållare, förutom den operativa säkerhets kopieringen, för att skydda mot oavsiktlig borttagning av behållare.
- Se [support mat ris](blob-backup-support-matrix.md) för att lära dig mer om de scenarier, begränsningar och tillgänglighet som stöds.

## <a name="create-a-backup-vault"></a>Skapa ett säkerhets kopierings valv

Ett [säkerhets kopierings valv](backup-vault-overview.md) är en hanterings enhet som lagrar återställnings punkter som skapats med tiden och som tillhandahåller ett gränssnitt för att utföra säkerhets kopierings åtgärder. Dessa inkluderar säkerhetskopieringar på begäran, återställningar och att skapa säkerhetskopieringsprinciper. Även om den operativa säkerhets kopieringen av blobbar är en lokal säkerhets kopia och inte lagrar data i valvet, krävs valvet för olika hanterings åtgärder.

>[!NOTE]
>Säkerhets kopierings valvet är en ny resurs som används för att säkerhetskopiera nya arbets belastningar som stöds och skiljer sig från det redan befintliga Recovery Services-valvet.

Instruktioner för hur du skapar ett säkerhets kopierings valv finns i [dokumentationen för säkerhets kopierings valvet](backup-vault-overview.md#create-a-backup-vault).

## <a name="grant-permissions-to-the-backup-vault-on-storage-accounts"></a>Bevilja behörighet till säkerhets kopierings valvet på lagrings konton

Den operativa säkerhets kopieringen skyddar också lagrings kontot (som innehåller de blobbar som ska skyddas) från oavsiktliga borttagningar genom att använda ett säkerhetskopierat borttagnings lås. Detta kräver att säkerhets kopierings valvet har vissa behörigheter för de lagrings konton som behöver skyddas. För enkelhetens skull har dessa behörigheter sammanställts under rollen lagrings konto säkerhets kopierings deltagare. Följ instruktionerna nedan för lagrings konton som måste skyddas:

1. I det lagrings konto som ska skyddas navigerar du till **fliken Access Control (IAM)** i det vänstra navigerings fönstret.
1. Välj **Lägg till roll tilldelningar** för att tilldela den roll som krävs.

    ![Lägg till roll tilldelningar](./media/blob-backup-configure-manage/add-role-assignments.png)

1. I fönstret Lägg till roll tilldelning:

    1. Under **roll** väljer du **säkerhets kopierings deltagare för lagrings konto**.
    1. Under **tilldela åtkomst till** väljer du **användare, grupp eller tjänstens huvud namn**.
    1. Skriv **namnet på det säkerhets kopierings valv** som du vill skydda Blobbarna i det här lagrings kontot och välj samma från Sök resultaten.
    1. När du är färdig väljer du **Spara**.

        ![Alternativ för roll tilldelning](./media/blob-backup-configure-manage/role-assignment-options.png)

        >[!NOTE]
        >Tillåt upp till 10 minuter innan roll tilldelningen börjar gälla.

## <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy

En säkerhets kopierings princip styr vanligt vis kvarhållning och schema för dina säkerhets kopieringar. Eftersom den operativa säkerhets kopieringen för blobbar är kontinuerlig, behöver du inget schema för att säkerhetskopiera. Principen behövs i princip för att ange kvarhållningsperioden. Du kan använda och återanvända säkerhets kopierings policyn för att konfigurera säkerhets kopiering för flera lagrings konton till ett valv.

Här följer stegen för att skapa en säkerhets kopierings princip för drift säkerhets kopiering av dina blobbar:

1. I säkerhets kopierings valvet navigerar du till **säkerhets kopierings principer** och väljer **+ Lägg** till för att skapa en säkerhets kopierings princip.

    ![Säkerhets kopierings principer](./media/blob-backup-configure-manage/backup-policies.png)

1. På fliken **grundläggande** anger du ett namn för säkerhets kopierings principen och väljer **Azure-blobbar** som data källans typ. Du kan också visa information om det valda valvet.

    ![Skapa säkerhets kopierings princip](./media/blob-backup-configure-manage/create-backup-policy.png)

    >[!NOTE]
    >Även om säkerhets kopieringen är **redundans** i valvet, gäller inte redundansen för den operativa säkerhets kopian av blobbar eftersom säkerhets kopian är lokal och inga data lagras i säkerhets kopierings valvet. Säkerhets kopierings valvet här är hanterings enheten för att hjälpa dig att hantera skyddet av block-blobar i dina lagrings konton.

1. På fliken **säkerhets kopierings policy** anger du information om kvarhållning. Du ser att det redan finns en bevarande regel som kallas **standard** med en kvarhållningsperiod på 30 dagar. Om du vill redigera Retentions tiden använder du ikonen **Redigera bevarande regel** för att redigera och ange hur länge du vill att data ska bevaras. Du kan ange kvarhållning upp till 360 dagar.

    ![Fliken säkerhets kopierings princip](./media/blob-backup-configure-manage/backup-policy-tab.png)

    >[!NOTE]
    >Att återställa över långa varaktigheter kan leda till återställnings åtgärder som tar längre tid att slutföra. Dessutom baseras den tid det tar att återställa en uppsättning data baserat på antalet skriv-och borttagnings åtgärder som gjorts under återställnings perioden. Till exempel kräver ett konto med 1 000 000 objekt med 3 000 objekt som lagts till per dag och 1 000 objekt som tas bort per dag cirka två timmar för att återställa till en punkt 30 dagar tidigare. En kvarhållningsperiod och återställningen över 90 dagar tidigare skulle inte rekommenderas för ett konto med den här ändrings takten.

1. I rutan **Granska + skapa** kontrollerar du all information om principen och väljer **skapa** när du är klar för att slutföra skapandet av principen. Ett meddelande bekräftas när säkerhets kopierings principen har skapats och är redo att användas.

    ![Granska och skapa princip](./media/blob-backup-configure-manage/review-create.png)

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering

Säkerhets kopiering av blobbar har kon figurer ATS på lagrings konto nivå. Så alla blobar i lagrings kontot skyddas med drift säkerhets kopiering.

Börja konfigurera säkerhets kopiering:

1. Sök efter **Backup Center** i Sök fältet.
1. Gå till **Översikt**  ->  **+ säkerhets kopiering**.

    ![Översikt över backup Center](./media/blob-backup-configure-manage/backup-center-overview.png)

1. På fliken **initiera: Konfigurera säkerhets kopiering** väljer du **Azure-blobbar (Azure Storage)** som DataSource-typ.

    ![Starta: Konfigurera fliken säkerhets kopiering](./media/blob-backup-configure-manage/initiate-configure-backup.png)

1. På fliken **grundläggande** anger du **Azure-blobbar (Azure Storage)** som **data källans** typ och väljer det säkerhets kopierings valv som du vill koppla dina lagrings konton till. Du kan visa information om det valda valvet i fönstret.

    ![Fliken Grundläggande](./media/blob-backup-configure-manage/basics-tab.png)

1. Välj sedan den säkerhets kopierings princip som du vill använda för att ange kvarhållning. Du kan visa information om den valda principen i den nedre delen av skärmen. I kolumnen användnings data Arkiv visas den kvarhållning som definierats i principen. "Drift" innebär att data underhålls lokalt i själva käll lagrings kontot.

    ![Välj säkerhets kopierings princip](./media/blob-backup-configure-manage/choose-backup-policy.png)

    Du kan också skapa en ny säkerhets kopierings princip. Det gör du genom att välja **Skapa ny** och följa stegen nedan:

    1. Ange ett namn för den princip som du vill skapa. Se till att de andra rutorna visar rätt typ av data källa och valv namn.
    1. På fliken **säkerhets kopierings policy** väljer du ikonen Redigera bevarande regel för att redigera och ange varaktigheten för vilken du vill att data ska bevaras. Du kan ange kvarhållning upp till 360 dagar. Att återställa över långa varaktigheter kan leda till återställnings åtgärder som tar längre tid att slutföra.

        ![Skapa ny säkerhets kopierings princip](./media/blob-backup-configure-manage/new-backup-policy.png)

    1. När du är färdig väljer du **Granska + skapa** för att skapa säkerhets kopierings principen.

1. Därefter måste du välja de lagrings konton som du vill konfigurera skydd av blobbar för. Du **kan välja flera** lagrings konton samtidigt och välja.

    Se dock till att valvet som du har valt har behörighet att konfigurera säkerhets kopiering på lagrings kontona som beskrivs ovan i [bevilja behörighet till säkerhets kopierings valvet på lagrings konton](#grant-permissions-to-the-backup-vault-on-storage-accounts).

    ![Välj vilka resurser som ska säkerhets kopie ras](./media/blob-backup-configure-manage/select-resources.png)

    Säkerhets kopieringen kontrollerar om valvet har tillräcklig behörighet för att tillåta konfigurering av säkerhets kopiering på de valda lagrings kontona.

    ![Säkerhets kopiering verifierar behörigheter](./media/blob-backup-configure-manage/validate-permissions.png)

    Om verifieringen resulterar i fel (som med ett av lagrings kontona i skärm bilden) går du till de valda lagrings kontona och tilldelar lämpliga roller, som beskrivs [här](#grant-permissions-to-the-backup-vault-on-storage-accounts), och väljer sedan **omverifiera**. Det kan ta upp till 10 minuter innan den nya roll tilldelningen börjar gälla.

1. När verifieringen har slutförts för alla valda lagrings konton fortsätter du att **Granska och konfigurera** för att konfigurera säkerhets kopiering. Du ser meddelanden som informerar dig om statusen för att konfigurera skyddet och dess slut för ande.

## <a name="effects-on-backed-up-storage-accounts"></a>Effekter på säkerhetskopierade lagrings konton

När säkerhets kopiering har kon figurer ATS spåras ändringar som sker i block-blobar i lagrings kontona och data bevaras enligt säkerhets kopierings principen. Du ser följande ändringar i de lagrings konton som säkerhets kopian är konfigurerad för:

- Följande funktioner är aktiverade på lagrings kontot. Dessa kan visas på fliken **data skydd** i lagrings kontot.
  - Tidpunkt för återställning av behållare: med kvarhållning enligt vad som anges i säkerhets kopierings policyn
  - Mjuk borttagning för blobbar: med kvarhållning enligt vad som anges i säkerhets kopierings policyn + 5 dagar
  - Versions hantering för blobbar
  - BLOB Change-feed

  Om lagrings kontot som har kon figurer ATS för säkerhets kopiering redan hade  **återställning vid tidpunkten för behållare** eller **mjuk borttagning för blobbar** aktiverade (innan säkerhets kopiering har kon figurer ATS), säkerställer säkerhets kopieringen att kvarhållning är minst så som det definieras i säkerhets kopierings principen. Därför för varje egenskap:

  - Om kvarhållning i säkerhets kopierings principen är större än den kvarhållna som ursprungligen fanns i lagrings kontot, ändras lagrings kontots kvarhållning enligt säkerhets kopierings principen
  - Om kvarhållning i säkerhets kopierings principen är mindre än den kvarhållna som ursprungligen fanns i lagrings kontot: kvarhållning av lagrings kontot lämnas oförändrat vid den ursprungliga uppsättningens varaktighet.

  ![Fliken Data skydd](./media/blob-backup-configure-manage/data-protection.png)

- Ett **borttagnings lås** används av säkerhets kopian på det skyddade lagrings kontot. Låset är avsett att skydda mot händelse av oavsiktlig borttagning av lagrings kontot. Detta kan visas under **lagrings konto**  >  **Lås**.

    ![Borttagningslås](./media/blob-backup-configure-manage/delete-lock.png)

## <a name="manage-operational-backup"></a>Hantera drift säkerhets kopiering

Du kan använda Backup Center som ett enda fönster ruta för att hantera alla dina säkerhets kopior. När det gäller drift säkerhets kopiering för Azure-blobar kan du använda Backup Center för att utföra följande:

- Som vi sett ovan kan du använda den för att skapa säkerhets kopierings valv och-principer. Du kan också Visa alla valv och principer under de valda prenumerationerna.
- Backup Center ger dig ett enkelt sätt att övervaka skyddet av skyddade lagrings konton samt lagrings konton för vilka säkerhets kopiering inte har kon figurer ATS för tillfället.
- Du kan konfigurera säkerhets kopiering för alla lagrings konton med knappen **+ säkerhets kopiering** .
- Du kan initiera **återställningar** med hjälp av knappen Återställ och spåra återställningar med hjälp av **säkerhets kopierings jobb**. Mer information om hur du utför [återställningar finns i återställa Azure-blobar](blob-backup-support-matrix.md).
- Analysera säkerhets kopierings användningen med hjälp av säkerhets kopierings rapporter.

    ![Backup Center](./media/blob-backup-configure-manage/backup-center.png)

Mer information finns i [Översikt över säkerhets kopierings Center (för hands version)](backup-center-overview.md).

## <a name="next-steps"></a>Nästa steg

- [Återställa Azure-blobbar](blob-restore.md)
