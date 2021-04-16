---
title: Om säkerhetskopiering av Azure-filresurs
description: Lär dig hur du kommer tillbaka till Azure-filresurser i Recovery Services-valvet
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: c4f9dd816ace2c9aec8f48207fbce88acf34e24a
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516823"
---
# <a name="about-azure-file-share-backup"></a>Om säkerhetskopiering av Azure-filresurs

Säkerhetskopiering av Azure-filresurs är en inbyggd, molnbaserad säkerhetskopieringslösning som skyddar dina data i molnet och eliminerar ytterligare underhållskostnader som ingår i lokala säkerhetskopieringslösningar. Tjänsten Azure Backup integreras smidigt med Azure File Sync och gör att du kan centralisera både filresursdata och säkerhetskopior. Med den här enkla, tillförlitliga och säkra lösningen kan du konfigurera skydd för företagets filresurser i några enkla steg med en garanti för att du kan återställa dina data i alla katastrofscenarscenarion.

## <a name="key-benefits-of-azure-file-share-backup"></a>Viktiga fördelar med säkerhetskopiering av Azure-filresurs

* **Ingen infrastruktur:** Ingen distribution krävs för att konfigurera skydd för dina filresurser.
* **Anpassad kvarhållning:** Du kan konfigurera säkerhetskopior med kvarhållning per dag/vecka/månad/år enligt dina behov.
* **Inbyggda hanteringsfunktioner:** Du kan schemalägga säkerhetskopieringar och ange önskad kvarhållningsperiod utan extra omkostnader för datarening.
* **Omedelbar återställning:** Säkerhetskopiering av Azure-filresurs använder ögonblicksbilder av filresursen så att du bara kan välja de filer som du vill återställa direkt.
* **Aviseringar och rapportering:** Du kan konfigurera aviseringar för säkerhetskopierings- och återställningsfel och använda rapportlösningen som tillhandahålls av Azure Backup för att få insikter om säkerhetskopior i dina filresurser.
* **Skydd mot oavsiktlig borttagning av filresurser:** Azure Backup [](../storage/files/storage-files-prevent-file-share-deletion.md) funktionen mjuk borttagning på lagringskontonivå med en kvarhållningsperiod på 14 dagar. Även om en illvillig aktör tar bort filresursen bevaras filresursens innehåll och återställningspunkter (ögonblicksbilder) under en konfigurerbar kvarhållningsperiod, vilket möjliggör lyckad och fullständig återställning av källinnehåll och ögonblicksbilder utan dataförlust.

## <a name="architecture"></a>Arkitektur

![Arkitektur för säkerhetskopiering av Azure-filresurs](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>Så här fungerar säkerhetskopieringsprocessen

1. Det första steget i att konfigurera säkerhetskopiering för Azure-filresurser är att skapa ett Recovery Services-valv. Valvet ger dig en samlad vy över de säkerhetskopior som konfigurerats för olika arbetsbelastningar.

2. När du har skapat ett valv Azure Backup tjänsten de lagringskonton som kan registreras med valvet. Du kan välja det lagringskonto som är värd för de filresurser som du vill skydda.

3. När du har valt lagringskontot visar Azure Backup-tjänsten en lista över de filresurser som finns i lagringskontot och lagrar deras namn i katalogen för hanteringslagret.

4. Sedan konfigurerar du säkerhetskopieringspolicyn (schema och kvarhållning) enligt dina krav och väljer de filresurser som ska säkerhetskopieras. Tjänsten Azure Backup registrerar scheman i kontrollplanet för att göra schemalagda säkerhetskopieringar.

5. Baserat på den angivna principen utlöser Azure Backup-schemaläggaren säkerhetskopieringar vid den schemalagda tiden. Som en del av jobbet skapas ögonblicksbilden av filresursen med hjälp av API:et för filresurs. Endast URL:en för ögonblicksbilder lagras i metadatalagret.

    >[!NOTE]
    >Filresursdata överförs inte till Backup-tjänsten eftersom Backup-tjänsten skapar och hanterar ögonblicksbilder som ingår i ditt lagringskonto och säkerhetskopior inte överförs till valvet.

6. Du kan återställa innehållet i Azure-filresursen (enskilda filer eller hela resursen) från ögonblicksbilder som är tillgängliga på källfilresursen. När åtgärden har utlösts hämtas URL:en för ögonblicksbilden från metadatalagret och data listas och överförs från källögonblicksbild till valfri målfilresurs.

7. Om du använder Azure File Sync anger Backup-tjänsten sökvägarna för filerna som återställs till Azure File Sync-tjänsten, som sedan utlöser en process för identifiering av bakgrundsändring för dessa filer. Alla filer som har ändrats synkroniseras till serverslutpunkten. Den här processen sker parallellt med den ursprungliga återställningen till Azure-filresursen.

8. Övervakningsdata för säkerhetskopierings- och återställningsjobbet skickas till Azure Backup Monitoring Service. På så sätt kan du övervaka molnsäkerhetskopior för dina filresurser på en enda instrumentpanel. Du kan också konfigurera aviseringar eller e-postmeddelanden när säkerhetskopieringshälsan påverkas. E-postmeddelanden skickas via Azures e-posttjänst.

## <a name="backup-costs"></a>Kostnader för säkerhetskopiering

Det finns två kostnader som är kopplade till säkerhetskopieringslösningen för Azure-filresursen:

1. **Kostnad för ögonblicksbildlagring:** Lagringsavgifter för ögonblicksbilder debiteras tillsammans med Azure Files användning enligt prisinformationen som anges [här](https://azure.microsoft.com/pricing/details/storage/files/)

2. **Avgift för skyddad** instans: Från och med 1 september 2020 debiteras kunder en avgift för skyddade instanser enligt prisinformationen som anges [här.](https://azure.microsoft.com/pricing/details/backup/) Avgiften för skyddade instanser beror på den totala storleken på skyddade filresurser i ett lagringskonto.

Om du vill få detaljerade uppskattningar för att laddade upp Azure-filresurser kan [du ladda ned den detaljerade Azure Backup prisberäknaren](https://aka.ms/AzureBackupCostEstimates).  

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [backar upp Azure-filresurser](backup-afs.md)
* Få svar på [frågor om att Azure Files](backup-azure-files-faq.yml)
