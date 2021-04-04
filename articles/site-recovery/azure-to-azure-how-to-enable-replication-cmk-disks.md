---
title: Aktivera replikering av krypterade virtuella Azure-datorer i Azure Site Recovery
description: Den här artikeln beskriver hur du konfigurerar replikering för virtuella datorer med CMK-aktiverade diskar från en Azure-region till en annan med hjälp av Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/10/2020
ms.author: mayg
ms.openlocfilehash: 9f9052f51c5bab0ea738e9fd15d8f62f45ff0c9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146543"
---
# <a name="replicate-machines-with-customer-managed-keys-cmk-enabled-disks"></a>Replikera datorer med CMK- aktiverade (kundhanterade nycklar) diskar

Den här artikeln beskriver hur du replikerar virtuella Azure-datorer med Customer-Managed nycklar (CMK) aktiverade hanterade diskar, från en Azure-region till en annan.

## <a name="prerequisite"></a>Förutsättning
Du måste skapa disk krypterings uppsättningarna i mål regionen för mål prenumerationen innan du aktiverar replikering för dina virtuella datorer som har CMK-aktiverade hanterade diskar.

## <a name="enable-replication"></a>Aktivera replikering

I det här exemplet är den primära Azure-regionen Asien, östra och den sekundära regionen Asien, sydöstra.

1. I valvet väljer du **+ Replikera**.
2. Observera följande fält.
    - **Källa**: den virtuella datorns start punkt, som i det här fallet är **Azure**.
    - **Käll plats**: den Azure-region där du vill skydda dina virtuella datorer. I det här exemplet är käll platsen "Asien, östra".
    - **Distributions modell**: Azures distributions modell för käll datorerna.
    - **Käll prenumeration**: den prenumeration som dina virtuella käll datorer tillhör. Det kan vara vilken prenumeration som helst i samma Azure Active Directory klient organisation som Recovery Services-valvet.
    - **Resurs grupp**: resurs gruppen som de virtuella käll datorerna tillhör. Alla virtuella datorer i den valda resurs gruppen visas som skydd i nästa steg.

3. I **Virtual Machines**  >  **Välj virtuella datorer** väljer du varje virtuell dator som du vill replikera. Du kan bara välja datorer som stöder replikering. Välj sedan **OK**.

4. I **Inställningar** kan du konfigurera följande inställningar för mål platsen.

    - **Målplats**: den plats där dina virtuella käll dator data kommer att replikeras. Site Recovery innehåller en lista över lämpliga mål regioner baserade på den valda datorns plats. Vi rekommenderar att du använder samma plats som Recovery Services valvets plats.
    - **Mål prenumeration**: den mål prenumeration som används för haveri beredskap. Som standard är mål prenumerationen densamma som käll prenumerationen.
    - **Mål resurs grupp**: resurs gruppen som alla dina replikerade virtuella datorer tillhör. Som standard skapar Site Recovery en ny resurs grupp i mål regionen. Namnet hämtar `asr` suffixet. Om det redan finns en resurs grupp som har skapats av Azure Site Recovery används den igen. Du kan också välja att anpassa den, som du ser i följande avsnitt. Platsen för mål resurs gruppen kan vara valfri Azure-region förutom den region där de virtuella käll datorerna finns.
    - **Virtuellt mål nätverk**: Site Recovery skapar ett nytt virtuellt nätverk i mål regionen som standard. Namnet hämtar `asr` suffixet. Den är mappad till ditt käll nätverk och används för framtida skydd. [Läs mer](./azure-to-azure-network-mapping.md) om nätverks mappning.
    - **Mål lagrings konton (om den virtuella käll datorn inte använder hanterade diskar)**: som standard skapar Site Recovery ett nytt mål lagrings konto genom att mimicking lagrings konfigurationen för din käll dator. Om det redan finns ett lagrings konto används det igen.
    - **Hanterade replik diskar (om den virtuella käll datorn använder hanterade diskar)**: Site Recovery skapar nya replik hanterade diskar i mål regionen för att spegla den virtuella käll datorns hanterade diskar av samma lagrings typ (standard eller Premium) som den virtuella käll datorns hanterade diskar.
    - **Cache-lagrings konton**: Site Recovery behöver ett extra lagrings konto som heter *cache Storage* i käll regionen. Alla ändringar på de virtuella käll datorerna spåras och skickas till cache Storage-kontot. De replikeras sedan till mål platsen.
    - **Tillgänglighets uppsättning**: Site Recovery skapar en ny tillgänglighets uppsättning i mål regionen som standard. Namnet har `asr` suffixet. Om en tillgänglighets uppsättning som skapats av Site Recovery redan finns återanvänds den.
    - **Disk krypterings uppsättningar (des)**: Site Recovery behöver de disk krypterings uppsättningar som ska användas för replik-och mål hanterade diskar. Du måste skapa DES i mål prenumerationen och mål regionen innan du aktiverar replikeringen. Som standard är ingen DES vald. Du måste klicka på Anpassa om du vill välja en DES per käll disk.
    - **Replikeringsprincip**: definierar inställningarna för kvarhållning av återställnings punkter och frekvens för programkonsekventa ögonblicks bilder. Som standard skapar Site Recovery en ny replikeringsprincip med standardinställningar *24 timmar* för kvarhållning av återställnings punkter och *60 minuter* för programkonsekventa ögonblicks bild frekvens.

    ![Aktivera replikering för datorn med CMK-aktiverade diskar](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-enable-dr.png)

## <a name="customize-target-resources"></a>Anpassa mål resurser

Följ dessa steg om du vill ändra Site Recovery inställningarna för standard mål.

1. Välj **Anpassa** bredvid "mål prenumeration" om du vill ändra standard mål prenumerationen. Välj prenumerationen i listan över prenumerationer som är tillgängliga i Azure AD-klienten.

2. Välj **Anpassa** bredvid "resurs grupp, nätverk, lagring och tillgänglighets uppsättningar" för att ändra följande standardinställningar:
    - För **mål resurs grupp** väljer du resurs gruppen i listan över resurs grupper på mål platsen för prenumerationen.
    - För **virtuellt mål nätverk** väljer du nätverket från en lista över virtuella nätverk på mål platsen.
    - För **tillgänglighets uppsättning** kan du lägga till inställningar för tillgänglighets uppsättningar på den virtuella datorn, om de är en del av en tillgänglighets uppsättning i käll regionen.
    - För **mål lagrings konton** väljer du det konto som ska användas.

3. Välj **Anpassa** intill "inställningar för lagrings kryptering" för att välja mål-des för varje kundhanterad nyckel (CMK) som har Aktiver ATS med den hanterade käll disken. Vid tidpunkten för valet kommer du också att kunna se vilket mål nyckel valv som DES är associerat med.

4. Välj **skapa mål resurs**  >  **Aktivera replikering**.
5. När de virtuella datorerna har Aktiver ATS för replikering kan du kontrol lera de virtuella datorernas hälso status under **replikerade objekt**.

![Skärm bild som visar var du kan kontrol lera den virtuella datorns hälso status.](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-customize-target-disk-properties.png)

>[!NOTE]
>Under den inledande replikeringen kan statusen ta lite tid att uppdatera, utan att det är uppenbart förloppet. Klicka på **Uppdatera**  för att hämta den senaste statusen.

## <a name="faqs"></a>Vanliga frågor och svar

* Jag har aktiverat CMK på ett befintligt replikerat objekt, hur kan jag se till att CMK tillämpas i mål regionen också?

    Du kan ta reda på namnet på den hanterade replik disken (skapas av Azure Site Recovery i mål regionen) och koppla DES till replik disken. Men du kommer inte att kunna se information om DES i disk bladet när du har kopplat den. Alternativt kan du välja att inaktivera replikeringen av den virtuella datorn och aktivera den igen. Det ser till att du ser information om DES och nyckel valvet på bladet diskar för det replikerade objektet.

* Jag har lagt till en ny CMK-aktiverad disk till det replikerade objektet. Hur kan jag replikera den här disken med Azure Site Recovery?

    Det finns inte stöd för att lägga till en ny CMK-aktiverad disk till ett befintligt replikerat objekt. Inaktivera replikeringen och aktivera replikeringen igen för den virtuella datorn.

* Jag har aktiverat både plattforms-och kund hanterade nycklar, hur kan jag skydda mina diskar?

    Att aktivera Double Encryption med både plattforms-och Kundhanterade nycklar är suppprted av Site Recovery. Följ anvisningarna i den här artikeln för att skydda datorn. Du måste skapa en Double Encryption Enabled DES i mål regionen i förväg. När du aktiverar replikering för en sådan virtuell dator kan du ange den här DES-filen till Site Recovery.

