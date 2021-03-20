---
title: Säkerhetsfunktioner som skyddar hybrid säkerhets kopieringar
description: Lär dig hur du använder säkerhetsfunktioner i Azure Backup för att göra säkerhets kopieringar säkrare
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/08/2017
ms.openlocfilehash: 8c671b1b54b937f518f7179bb6940f31a28a78d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94841026"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Säkerhetsfunktioner som hjälper till att skydda hybrid säkerhets kopieringar som använder Azure Backup

Problem med säkerhets problem, t. ex. skadlig kod, utpressnings program vara och intrång, ökar. De här säkerhets problemen kan vara dyra, i termer av både Money och data. För att skydda mot sådana angrepp tillhandahåller Azure Backup nu säkerhetsfunktioner som hjälper dig att skydda hybrid säkerhets kopieringar. Den här artikeln beskriver hur du aktiverar och använder dessa funktioner med hjälp av en Azure Recovery Services-agent och Azure Backup Server. Dessa funktioner omfattar bland annat:

- **Förebyggande**. Ytterligare ett lager med autentisering läggs till när en kritisk åtgärd som att ändra en lösen fras utförs. Den här verifieringen är till för att säkerställa att sådana åtgärder bara kan utföras av användare som har giltiga autentiseringsuppgifter för Azure.
- **Aviseringar**. Ett e-postmeddelande skickas till prenumerations administratören när en kritisk åtgärd som att ta bort säkerhetskopierade data utförs. Det här e-postmeddelandet garanterar att användaren snabbt meddelas om sådana åtgärder.
- **Återställning**. Borttagna säkerhets kopierings data behålls under ytterligare 14 dagar från datumet för borttagningen. Detta säkerställer återhämtning av data inom en viss tids period, så det finns ingen data förlust även om ett angrepp sker. Dessutom behålls ett större antal lägsta återställnings punkter för att skydda mot skadade data.

> [!NOTE]
> Säkerhetsfunktioner bör inte aktive ras om du använder IaaS (Infrastructure as a Service) med säkerhets kopiering av virtuella datorer. Dessa funktioner är ännu inte tillgängliga för IaaS VM-säkerhetskopiering, så att de inte har någon påverkan. Säkerhetsfunktioner ska bara aktive ras om du använder: <br/>
>
> - **Azure Backup Agent**. Lägsta agent version 2.0.9052. När du har aktiverat de här funktionerna bör du uppgradera till den här agent versionen för att utföra kritiska åtgärder. <br/>
> - **Azure Backup Server**. Lägsta Azure Backups agent version 2.0.9052 med Azure Backup Server uppdatering 1. <br/>
> - **System Center-Data Protection Manager**. Lägsta Azure Backups agent version 2.0.9052 med Data Protection Manager 2012 R2 UR12 eller Data Protection Manager 2016 UR2. <br/>

> [!NOTE]
> Dessa funktioner är endast tillgängliga för Recovery Services Vault. Alla nyligen skapade Recovery Services-valv har dessa funktioner aktiverade som standard. För befintliga Recovery Services-valv aktiverar användarna dessa funktioner med hjälp av de steg som beskrivs i följande avsnitt. När funktionerna har Aktiver ATS gäller de för alla Recovery Services agent-datorer, Azure Backup Server instanser och Data Protection Manager servrar som är registrerade med valvet. Att aktivera den här inställningen är en engångs åtgärd och du kan inte inaktivera dessa funktioner när du har aktiverat dem.
>

## <a name="enable-security-features"></a>Aktivera säkerhetsfunktioner

Om du skapar ett Recovery Services valv kan du använda alla säkerhetsfunktioner. Om du arbetar med ett befintligt valv aktiverar du säkerhetsfunktioner genom att följa dessa steg:

1. Logga in på Azure Portal med dina Azure-autentiseringsuppgifter.
2. Välj **Bläddra** och skriv **Recovery Services**.

    ![Skärm bild av Azure Portal Browse-alternativ](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    Listan över Recovery Services-valv visas. Välj ett valv i listan. Instrumentpanelen för det valda valvet öppnas.
3. I listan med objekt som visas under valvet väljer du **Egenskaper** under **Inställningar**.

    ![Skärm bild av Recovery Services valv alternativ](./media/backup-azure-security-feature/vault-list-properties.png)
4. Under **säkerhets inställningar** väljer du **Uppdatera**.

    ![Skärm bild av Recovery Services valv egenskaper](./media/backup-azure-security-feature/security-settings-update.png)

    Uppdaterings länken öppnar fönstret **säkerhets inställningar** , som innehåller en sammanfattning av funktionerna och låter dig aktivera dem.
5. I list rutan **har du konfigurerat Azure AD-Multi-Factor Authentication?** väljer du ett värde för att bekräfta om du har aktiverat [Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md). Om den är aktive rad uppmanas du att autentisera från en annan enhet (till exempel en mobil telefon) när du loggar in på Azure Portal.

   När du utför kritiska åtgärder i säkerhets kopiering måste du ange en säkerhets kod som är tillgänglig på Azure Portal. Genom att aktivera Azure AD Multi-Factor Authentication lägger du till ett säkerhets lager. Endast auktoriserade användare med giltiga autentiseringsuppgifter för Azure och autentiserade från en andra enhet kan komma åt Azure Portal.
6. Om du vill spara säkerhets inställningar väljer du **Aktivera** och sedan **Spara**. Du kan bara välja **Aktivera** när du har valt ett värde i listan **har du konfigurerat Azure AD-Multi-Factor Authentication?** i föregående steg.

    ![Skärm bild av säkerhets inställningar](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Återställa borttagna säkerhets kopierings data

Säkerhets kopieringen behåller borttagna säkerhets kopierings data under ytterligare 14 dagar och tar inte bort dem omedelbart om åtgärden **stoppa säkerhets kopiering med ta bort säkerhets kopiering** utförs. För att återställa dessa data under 14-dagars period, utför följande steg, beroende på vad du använder:

För **Azure Recovery Services agent** -användare:

1. Om datorn där säkerhets kopieringarna skedde fortfarande är tillgänglig, kan du återställa de borttagna data källorna och använda [återställnings data till samma dator](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) i Azure Recovery Services för att återställa från alla gamla återställnings punkter.
2. Om den här datorn inte är tillgänglig använder du [Återställ till en annan dator](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) för att använda en annan Azure Recovery Services-dator för att hämta dessa data.

För **Azure Backup Server** användare:

1. Om den server där säkerhets kopiorna skedde fortfarande är tillgänglig, kan du skydda de borttagna data källorna igen och använda funktionen **Återställ data** för att återställa från alla gamla återställnings punkter.
2. Om den här servern inte är tillgänglig använder du [Återställ data från en annan Azure Backup Server](backup-azure-alternate-dpm-server.md) för att använda en annan Azure Backup Server-instans för att hämta dessa data.

För **Data Protection Manager** användare:

1. Om den server där säkerhets kopiorna skedde fortfarande är tillgänglig, kan du skydda de borttagna data källorna igen och använda funktionen **Återställ data** för att återställa från alla gamla återställnings punkter.
2. Om den här servern inte är tillgänglig använder du [Lägg till extern DPM](backup-azure-alternate-dpm-server.md) för att använda en annan Data Protection Manager Server för att hämta dessa data.

## <a name="prevent-attacks"></a>Förhindra attacker

Checkar har lagts till för att se till att endast giltiga användare kan utföra olika åtgärder. Detta omfattar att lägga till ett extra lager av autentisering och upprätthålla ett minsta kvarhållningsintervall för återställnings syfte.

### <a name="authentication-to-perform-critical-operations"></a>Autentisering för att utföra kritiska åtgärder

Som en del av att lägga till ett extra lager av autentisering för kritiska åtgärder uppmanas du att ange en säkerhets-PIN-kod när du **stoppar skyddet med ta bort data** och **ändra lösen Frass** åtgärder.

> [!NOTE]
>
> För närvarande stöds inte säkerhets-PIN för att **stoppa skyddet med ta bort data** för DPM och Mabs.

För att ta emot den här PIN-koden:

1. Logga in på Azure-portalen.
2. Bläddra till **Recovery Services egenskaper för valv**  >  **Inställningar**  >  .
3. Under **säkerhets-PIN** väljer du **generera**. Då öppnas ett fönster som innehåller den PIN-kod som ska anges i användar gränssnittet för Azure Recovery Services-agenten.
    Den här PIN-koden är bara giltig i fem minuter och genereras automatiskt efter den perioden.

### <a name="maintain-a-minimum-retention-range"></a>Behåll ett minsta kvarhållningsintervall

Följande kontroller har lagts till för att säkerställa att det alltid finns ett giltigt antal tillgängliga återställnings punkter:

- För daglig kvarhållning bör minst **sju** dagars kvarhållning göras.
- För veckovis kvarhållning bör minst **fyra** veckor för kvarhållning göras.
- För månatlig kvarhållning bör minst **tre** månaders kvarhållning göras.
- För årlig kvarhållning **bör minst ett års** kvarhållning göras.

## <a name="notifications-for-critical-operations"></a>Meddelanden om kritiska åtgärder

När en kritisk åtgärd utförs, skickas vanligt vis prenumerations administratören till ett e-postmeddelande med information om åtgärden. Du kan konfigurera ytterligare e-postmottagare för dessa aviseringar med hjälp av Azure Portal.

Säkerhetsfunktionerna som nämns i den här artikeln ger skydds metoder mot riktade attacker. Det är viktigt att du kan återställa dina data om ett angrepp sker.

## <a name="troubleshooting-errors"></a>Felsöka fel

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Princip ändring |Det gick inte att ändra säkerhets kopierings principen. Fel: den aktuella åtgärden kunde inte utföras på grund av ett internt tjänst fel [0x29834]. Försök igen om en stund. Kontakta Microsoft-supporten om problemet kvarstår. |**Orsak:**<br/>Det här felet uppstår när säkerhets inställningar är aktiverade, du försöker minska kvarhållningsintervallet under de lägsta värdena som anges ovan och du använder en version som inte stöds (som stöds anges i den här artikeln). <br/>**Rekommenderad åtgärd:**<br/> I det här fallet bör du ställa in kvarhållningsperioden över den minsta kvarhållningsperioden som har angetts (sju dagar i varje dag, fyra veckor för varje vecka, tre veckor för varje månad eller ett år för varje år) för att fortsätta med principbaserad uppdatering. En önskad metod är att uppdatera säkerhets kopierings agenten, Azure Backup Server och/eller DPM-UR för att utnyttja alla säkerhets uppdateringar. |
| Ändra lösen fras |Den angivna säkerhets koden är felaktig. (ID: 100130) Ange rätt säkerhets kod för att slutföra åtgärden. |**Orsak:**<br/> Det här felet uppstår när du anger en ogiltig eller utgången säkerhets kod när du utför en kritisk åtgärd (t. ex. ändrings fras). <br/>**Rekommenderad åtgärd:**<br/> Du måste ange en giltig säkerhets kod för att slutföra åtgärden. För att få PIN-koden loggar du in på Azure Portal och navigerar till Recovery Services valv > inställningar > egenskaper > skapa säkerhets kod. Använd den här PIN-koden för att ändra lösen fras. |
| Ändra lösen fras |Åtgärden misslyckades. ID: 120002 |**Orsak:**<br/>Det här felet uppstår när säkerhets inställningar är aktiverade, du försöker ändra lösen frasen och du har en version som inte stöds (giltiga versioner som anges i den här artikeln).<br/>**Rekommenderad åtgärd:**<br/> Om du vill ändra lösen frasen måste du först uppdatera säkerhets kopierings agenten till minst version 2.0.9052, Azure Backup Server till lägsta uppdatering 1, och/eller DPM för att minst DPM 2012 R2 UR12 eller DPM 2016 UR2 (hämta länkar nedan) och sedan ange en giltig PIN-kod för säkerhet. För att få PIN-koden loggar du in på Azure Portal och navigerar till Recovery Services valv > inställningar > egenskaper > skapa säkerhets kod. Använd den här PIN-koden för att ändra lösen fras. |

## <a name="next-steps"></a>Nästa steg

- [Kom igång med Azure Recovery Services Vault](backup-azure-vms-first-look-arm.md) för att aktivera dessa funktioner.
- [Hämta den senaste Azure Recovery Services-agenten](https://aka.ms/azurebackup_agent) för att skydda Windows-datorer och skydda dina säkerhets kopierings data mot attacker.
- [Hämta den senaste Azure Backup Server](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3) för att skydda arbets belastningar och skydda dina säkerhets kopierings data mot attacker.
- [Hämta UR12 för system center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) eller [Hämta UR2 för system Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) för att skydda arbets belastningar och skydda dina säkerhets kopierings data mot attacker.
