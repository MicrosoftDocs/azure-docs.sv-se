---
title: Konfigurera inställningar för valv diagnostik i skala
description: Konfigurera inställningar för Log Analytics diagnostik för alla valv i ett angivet omfång med Azure Policy
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 55461937381f7551c42714c835d4755ab65f175b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92171523"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>Konfigurera inställningar för valv diagnostik i skala

Den rapporterings lösning som tillhandahålls av Azure Backup utnyttjar Log Analytics (LA). För att data för ett angivet valv ska skickas till LA måste en [diagnostisk inställning](./backup-azure-diagnostic-events.md) skapas för det valvet.

Ofta kan det vara en besvärlig uppgift att lägga till en diagnostisk inställning manuellt per valv. Dessutom måste alla nya valv som skapats också ha aktiverade diagnostikinställningar för att kunna visa rapporter för det här valvet.

För att förenkla skapandet av diagnostikinställningar i skala (med LA som mål) är Azure Backup en inbyggd [Azure policy](../governance/policy/index.yml). Den här principen lägger till en LA-diagnostiktest till alla valv i en specifik prenumeration eller resurs grupp. I följande avsnitt finns anvisningar om hur du använder den här principen.

## <a name="supported-scenarios"></a>Scenarier som stöds

* Du kan tillämpa principen på en gång för alla Recovery Services valv i en viss prenumeration (eller till en resurs grupp i prenumerationen). Användaren som tilldelar principen måste ha **ägar** åtkomst till den prenumeration som principen är tilldelad till.

* Arbets ytan LA som anges av användaren (som diagnostikdata ska skickas till) kan finnas i en annan prenumeration än de valv som principen är tilldelad till. Användaren måste ha **läsare**, **deltagare** eller **ägar** åtkomst till den prenumeration där den angivna La-arbetsytan finns.

* Hanterings gruppens omfång stöds inte för tillfället.

* Den inbyggda principen är för närvarande inte tillgänglig i nationella moln.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="assigning-the-built-in-policy-to-a-scope"></a>Tilldela den inbyggda principen till ett omfång

Följ stegen nedan om du vill tilldela principen för valv i det begärda omfånget:

1. Logga in på Azure Portal och gå till instrument panelen för **principer** .
2. Välj **definitioner** på den vänstra menyn för att få en lista över alla inbyggda principer i Azure-resurser.
3. Filtrera listan för **kategori = säkerhets kopiering**. Leta upp principen med namnet **[för hands version]: Distribuera diagnostikinställningar för Recovery Services valv till Log Analytics arbets yta för resursbaserade kategorier**.

    ![Fönstret princip definition](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. Välj namnet på principen. Du omdirigeras till den detaljerade definitionen för den här principen.

    ![Detaljerad princip definition](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. Välj knappen **tilldela** högst upp i fönstret. Detta omdirigerar dig till fönstret **tilldela princip** .

6. Under **grunderna** väljer du de tre punkterna bredvid fältet **omfång** . Det här öppnar ett höger kontext fönster där du kan välja prenumerationen för den princip som ska tillämpas på. Du kan också välja en resurs grupp, så att principen endast tillämpas för valv i en viss resurs grupp.

    ![Grundläggande princip tilldelning](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. Under **parametrar** anger du följande information:

    * **Profil namn** – namnet som ska tilldelas till diagnostikinställningar som skapats av principen.
    * **Log Analytics arbets yta** – Log Analytics arbets ytan som diagnostikinställningar ska associeras med. Diagnostikdata för alla valv i omfånget för princip tilldelningen flyttas till den angivna LA-arbetsytan.

    * **Namn på undantags kod (valfritt) och undantags tag gen värde (valfritt)** – du kan välja att exkludera valv som innehåller ett visst taggnamn och värde från princip tilldelningen. Om du t. ex. **inte** vill att en diagnostisk inställning ska läggas till i de valv som har taggen "isTest" inställd på värdet "Ja" måste du ange "isTest" i fältet **undantags taggnamn** och ja i fältet **värde för undantags kod** . Om något (eller båda) av dessa två fält lämnas tomt tillämpas principen på alla relevanta valv oavsett vilka taggar de innehåller.

    ![Parametrar för princip tilldelning](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **Skapa en reparations uppgift** – när principen har tilldelats ett omfång får alla nya valv som skapats i det området automatiskt de inställningar som kon figurer ATS (inom 30 minuter från tidpunkten för skapandet av valvet). Om du vill lägga till en diagnostisk inställning i befintliga valv i omfånget kan du utlösa en reparations aktivitet vid princip tilldelnings tiden. Om du vill utlösa en reparations uppgift markerar du kryss rutan **skapa en reparations uppgift**.

    ![Reparation av princip tilldelning](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. Gå till fliken **Granska + skapa** och välj **skapa**.

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>Under vilka villkor kommer reparations uppgiften att gälla ett valv?

Reparations uppgiften tillämpas på valv som inte är kompatibla enligt definitionen av principen. Ett valv är icke-kompatibelt om det uppfyller något av följande villkor:

* Det finns ingen diagnostik-inställning för valvet.
* Det finns diagnostikinställningar för valvet, men ingen av inställningarna har **alla** resursbaserade händelser aktiverade med La som mål och **resurs** som marker ATS i växlingen.

Så även om en användare har ett valv med händelsen AzureBackupReport aktive rad i AzureDiagnostics-läge (som stöds av säkerhets kopierings rapporter), kommer reparations aktiviteten fortfarande att gälla för det här valvet, eftersom det resursbaserade läget är det rekommenderade sättet att skapa diagnostikinställningar, som [går framåt](./backup-azure-diagnostic-events.md#legacy-event).

Om en användare har ett valv med endast en delmängd av de sex resursbaserade händelserna aktive rad, kommer reparations uppgiften att gälla för valvet, eftersom säkerhets kopierings rapporter fungerar som förväntat endast om alla sex resursbaserade händelser är aktiverade.

> [!NOTE]
>
> Om ett valv har en befintlig diagnostisk inställning med en **delmängd av resurs specifika** kategorier som är aktiverade, konfigurerat för att skicka data till en viss La-arbetsytan, till exempel "arbets yta x", kommer reparations aktiviteten att Miss Miss läge (endast för det valvet) om mål gruppen La i princip tilldelningen är **samma** arbets yta x.
>
>Detta beror på att om händelserna som har Aktiver ATS av två olika diagnostikinställningar på samma resurs **överlappar** i något formulär, kan inställningarna inte ha samma arbets yta som målet. Du måste lösa det här felet manuellt, genom att navigera till relevant valv och konfigurera en diagnostisk inställning med en annan LA-arbetsyta som mål.
>
> Observera att reparations aktiviteten **inte** Miss fungerar om den befintliga diagnostik inställningen bara är AzureBackupReport aktive rad med arbets ytan X som mål, eftersom det i detta fall inte överlappar de händelser som Aktiver ATS av den befintliga inställningen och de händelser som aktive ras av den inställning som skapats av reparations aktiviteten.

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du använder säkerhets kopierings rapporter](./configure-reports.md)
* [Läs mer om Azure Policy](../governance/policy/index.yml)
* [Använd Azure Policy för att automatiskt aktivera säkerhets kopiering för alla virtuella datorer i ett prioriterat område](./backup-azure-auto-enable-backup.md)
