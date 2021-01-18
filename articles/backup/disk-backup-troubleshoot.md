---
title: Fel sökning av säkerhets kopierings fel i Azure disk backup
description: Lär dig hur du felsöker säkerhets kopierings fel i Azure disk backup
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 0a2ef1ea20ee8d6b7a3f32e244d3e00f3add80a2
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98558842"
---
# <a name="troubleshooting-backup-failures-in-azure-disk-backup-in-preview"></a>Fel sökning av säkerhets kopierings fel i Azure disk Backup (i för hands version)

>[!IMPORTANT]
>Azure disk Backup är i för hands version utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). För region tillgänglighet, se [support mat ris](disk-backup-support-matrix.md).
>
>[Fyll i det här formuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) om du vill registrera dig för för hands versionen.

Den här artikeln innehåller felsöknings information om säkerhets kopierings-och återställnings problem med Azure disk. Mer information om tillgänglighet för [Azure disk backup](disk-backup-overview.md) , stödda scenarier och begränsningar finns i [support mat ris](disk-backup-support-matrix.md).

## <a name="common-issues-faced-with-azure-disk-backup"></a>Vanliga problem med Azure disk backup

### <a name="error-code-usererrorsnapshotrgsubscriptionmismatch"></a>Felkod: UserErrorSnapshotRGSubscriptionMismatch

Fel meddelande: en ogiltig prenumeration har valts för data lagret för ögonblicks bilder

Rekommenderad åtgärd: diskar och disk ögonblicks bilder lagras i samma prenumeration. Du kan välja valfri resurs grupp för att lagra disk ögonblicks bilderna i prenumerationen. Välj samma prenumeration som käll disken. Mer information finns i [support mat ris](disk-backup-support-matrix.md).

### <a name="error-code-usererrorsnapshotrgnotfound"></a>Felkod: UserErrorSnapshotRGNotFound

Fel meddelande: det gick inte att utföra åtgärden eftersom det inte finns någon resurs grupp för ögonblicks bild data lager.

Rekommenderad åtgärd: skapa resurs gruppen och ange de behörigheter som krävs för den. Mer information finns i [Konfigurera säkerhets kopiering](backup-managed-disks.md#configure-backup).

### <a name="error-code-usererrormanageddisknotfound"></a>Felkod: UserErrorManagedDiskNotFound

Fel meddelande: det gick inte att utföra åtgärden eftersom den hanterade disken inte längre finns.

Rekommenderad åtgärd: säkerhets kopieringarna fortsätter att Miss lyckas eftersom käll disken kan ha tagits bort eller flyttats till en annan plats. Använd den befintliga återställnings punkten för att återställa disken om den tas bort av misstag. Om disken flyttas till en annan plats konfigurerar du säkerhets kopieringen för disken.

### <a name="error-code-usererrornotenoughpermissionondisk"></a>Felkod: UserErrorNotEnoughPermissionOnDisk

Fel meddelande: Azure Backup tjänsten kräver ytterligare behörigheter på disken för att utföra åtgärden.

Rekommenderad åtgärd: ge säkerhets kopierings valvets hanterade identitet rätt behörigheter på disken. Läs [dokumentationen](backup-managed-disks.md) för att ta reda på vilka behörigheter som krävs av den hanterade identiteten för säkerhets kopierings valvet och hur du anger den.

### <a name="error-code-usererrornotenoughpermissiononsnapshotrg"></a>Felkod: UserErrorNotEnoughPermissionOnSnapshotRG

Fel meddelande: Azure Backup tjänsten kräver ytterligare behörigheter för resurs gruppen för ögonblicks bild data Arkiv för att utföra den här åtgärden.

Rekommenderad åtgärd: ge säkerhets kopierings valvets hanterade identitet rätt behörigheter för resurs gruppen för ögonblicks bild data lager. Resurs gruppen för ögonblicks bild data arkiv är den plats där disk ögonblicks bilderna lagras. Läs [dokumentationen](backup-managed-disks.md) för att ta reda på vilket är resurs gruppen, vilka behörigheter som krävs av säkerhets kopierings valvets hanterade identitet och hur du anger den.

### <a name="error-code-usererrordiskbackupdiskormsipermissionsnotpresent"></a>Felkod: UserErrorDiskBackupDiskOrMSIPermissionsNotPresent

Fel meddelande: ogiltig disk eller Azure Backup tjänst kräver ytterligare behörighet på disken för att utföra åtgärden

Rekommenderad åtgärd: säkerhets kopieringarna fortsätter att Miss lyckas eftersom käll disken kan ha tagits bort eller flyttats till en annan plats. Använd den befintliga återställnings punkten för att återställa disken om den tas bort av misstag. Om disken flyttas till en annan plats konfigurerar du säkerhets kopieringen för disken. Om disken inte har tagits bort eller flyttats beviljar du säkerhets kopierings valvets hanterade identitet rätt behörigheter på disken. Läs [dokumentationen](backup-managed-disks.md) för att ta reda på vilka behörigheter som krävs av säkerhets kopierings valvets hanterade identitet och hur du anger den.

### <a name="error-code-usererrordiskbackupsnapshotrgormsipermissionsnotpresent"></a>Felkod: UserErrorDiskBackupSnapshotRGOrMSIPermissionsNotPresent

Fel meddelande: det gick inte att utföra åtgärden eftersom ögonblicks bilds data lager resurs gruppen inte längre finns. Eller Azure Backup tjänsten kräver ytterligare behörigheter för resurs gruppen för ögonblicks bild data Arkiv för att utföra den här åtgärden.

Rekommenderad åtgärd: skapa en resurs grupp och bevilja säkerhets kopierings valvets hanterade identitet rätt behörigheter för resurs gruppen för ögonblicks bild data lager. Resurs gruppen för ögonblicks bild data arkiv är den plats där disk ögonblicks bilderna lagras. Läs [dokumentationen](backup-managed-disks.md) för att förstå vad som är resurs gruppen, vilka behörigheter som krävs av säkerhets kopierings valvets hanterade identitet och hur du anger den.

### <a name="error-code-usererrordiskbackupauthorizationfailed"></a>Felkod: UserErrorDiskBackupAuthorizationFailed

Fel meddelande: den hanterade identiteten för säkerhets kopierings valvet saknar de behörigheter som krävs för att utföra åtgärden.

Rekommenderad åtgärd: ge säkerhets kopierings valvets hanterade identitet rätt behörigheter på disken som ska säkerhets kopie ras och i resurs gruppen för ögonblicks bilds data lager där ögonblicks bilderna lagras. Läs [dokumentationen](backup-managed-disks.md) för att ta reda på vilka behörigheter som krävs av säkerhets kopierings valvets hanterade identitet och hur du anger den.

### <a name="error-code-usererrorsnapshotrgormsipermissionsnotpresent"></a>Felkod: UserErrorSnapshotRGOrMSIPermissionsNotPresent

Fel meddelande: det gick inte att utföra åtgärden eftersom ögonblicks bilds data lager resurs gruppen inte längre finns. Eller, Azure Backup tjänsten kräver ytterligare behörigheter för resurs gruppen för ögonblicks bild data Arkiv för att utföra den här åtgärden.

Rekommenderad åtgärd: skapa resurs gruppen och bevilja säkerhets kopierings valvets hanterade identitet rätt behörigheter för resurs gruppen för ögonblicks bild data lager. Resurs gruppen för ögonblicks bild data arkiv är den plats där ögonblicks bilderna lagras. Läs [dokumentationen](backup-managed-disks.md) för att förstå vad som är resurs gruppen, vilka behörigheter som krävs av säkerhets kopierings valvets hanterade identitet och hur du anger den.

### <a name="error-code-usererroroperationalstoreparametersnotprovided"></a>Felkod: UserErrorOperationalStoreParametersNotProvided

Fel meddelande: det gick inte att utföra åtgärden eftersom ingen resurs grupp för ögonblicks bild data lager har angetts

Rekommenderad åtgärd: Ange resurs grupps parametern för ögonblicks bild data lager. Resurs gruppen för ögonblicks bild data arkiv är den plats där disk ögonblicks bilderna lagras. Mer information finns i [dokumentationen](backup-managed-disks.md).

### <a name="error-code-usererrorinvalidoperationalstoreresourcegroup"></a>Felkod: UserErrorInvalidOperationalStoreResourceGroup

Fel meddelande: den angivna resurs gruppen för ögonblicks bild data lager är ogiltig

Rekommenderad åtgärd: Ange en giltig resurs grupp för data lagret för ögonblicks bilder. Resurs gruppen för ögonblicks bild data arkiv är den plats där disk ögonblicks bilderna lagras. Mer information finns i [dokumentationen](backup-managed-disks.md).

### <a name="error-code-usererrordiskbackupdisktypenotsupported"></a>Felkod: UserErrorDiskBackupDiskTypeNotSupported

Fel meddelande: disk typen stöds inte

Rekommenderad åtgärd: se [support mat ris](disk-backup-support-matrix.md) på scenarier och begränsningar som inte stöds.

### <a name="error-code-usererrorsamenamediskalreadyexists"></a>Felkod: UserErrorSameNameDiskAlreadyExists

Fel meddelande: det gick inte att återställa eftersom det redan finns en disk med samma namn i den valda mål resurs gruppen

Rekommenderad åtgärd: Ange ett annat disk namn för återställning. Mer information finns i [återställa Azure-Managed disks](restore-managed-disks.md).

### <a name="error-code-usererrorrestoretargetrgnotfound"></a>Felkod: UserErrorRestoreTargetRGNotFound

Fel meddelande: det gick inte att utföra åtgärden eftersom mål resurs gruppen inte finns.

Rekommenderad åtgärd: Ange en giltig resurs grupp som ska återställas. Mer information finns i [återställa Azure-Managed disks](restore-managed-disks.md).

### <a name="error-code-usererrornotenoughpermissionontargetrg"></a>Felkod: UserErrorNotEnoughPermissionOnTargetRG

Fel meddelande: Azure Backup tjänsten kräver ytterligare behörigheter för mål resurs gruppen för att utföra åtgärden.

Rekommenderad åtgärd: ge säkerhets kopierings valvets hanterade identitet rätt behörigheter för mål resurs gruppen. Mål resurs gruppen är den valda platsen där disken ska återställas. Se dokumentationen för [återställning](restore-managed-disks.md) för att ta reda på vilka behörigheter som krävs av säkerhets kopierings valvets hanterade identitet och hur du anger den.

### <a name="error-code-usererrorsubscriptiondiskquotalimitreached"></a>Felkod: UserErrorSubscriptionDiskQuotaLimitReached

Fel meddelande: åtgärden misslyckades eftersom max gränsen för disk kvoten har nåtts för prenumerationen.

Rekommenderad åtgärd: Läs mer i [Azure-prenumerationen och tjänst begränsningar och kvot dokumentation](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) eller kontakta Microsoft Support för ytterligare vägledning.

### <a name="error-code-usererrordiskbackuprestorergormsipermissionsnotpresent"></a>Felkod: UserErrorDiskBackupRestoreRGOrMSIPermissionsNotPresent

Fel meddelande: det gick inte att utföra åtgärden eftersom mål resurs gruppen inte finns. Eller Azure Backup tjänsten kräver ytterligare behörigheter för mål resurs gruppen för att utföra den här åtgärden.

Rekommenderad åtgärd: Ange en giltig resurs grupp som ska återställas och ge säkerhets kopierings valvets hanterade identitet rätt behörigheter för mål resurs gruppen. Mål resurs gruppen är den valda platsen där disken ska återställas. Se dokumentationen för [återställning](restore-managed-disks.md) för att ta reda på vilka behörigheter som krävs av säkerhets kopierings valvets hanterade identitet och hur du anger den.

### <a name="error-code-usererrordeskeyvaultkeydisabled"></a>Felkod: UserErrorDESKeyVaultKeyDisabled

Fel meddelande: Key Vault-nyckeln som används för disk krypterings uppsättningen är inte i aktiverat läge.

Rekommenderad åtgärd: Aktivera Key Vault-nyckeln som används för disk krypterings uppsättningen. Se begränsningarna i [support mat ris](disk-backup-support-matrix.md).

### <a name="error-code-usererrormsipermissionsnotpresentondes"></a>Felkod: UserErrorMSIPermissionsNotPresentOnDES

Fel meddelande: Azure Backup tjänsten måste ha behörighet att komma åt disk krypterings uppsättningen som används med disken.

Rekommenderad åtgärd: ge läsar åtkomst till säkerhets kopierings valvets hanterade identitet till disk krypterings uppsättningen (DES).

### <a name="error-code-usererrordeskeyvaultkeynotavailable"></a>Felkod: UserErrorDESKeyVaultKeyNotAvailable

Fel meddelande: Key Vault-nyckeln som används för disk krypterings uppsättningen är inte tillgänglig.

Rekommenderad åtgärd: kontrol lera att nyckel valvs nyckeln som används för disk krypterings uppsättningen inte är inaktive rad eller borttagen.

### <a name="error-code-usererrordisksnapshotnotfound"></a>Felkod: UserErrorDiskSnapshotNotFound

Fel meddelande: diskens ögonblicks bild för återställnings punkten har tagits bort.

Rekommenderad åtgärd: ögonblicks bilder lagras i resurs gruppen ögonblicks bild data lager i din prenumeration. Det är möjligt att ögonblicks bilden som är relaterad till den valda återställnings punkten kan ha tagits bort eller flyttats från den här resurs gruppen. Överväg att använda en annan återställnings punkt för återställning. Följ även de rekommenderade rikt linjerna för att välja resurs grupp för ögonblicks bilder som nämns i [återställnings dokumentationen](restore-managed-disks.md).

### <a name="error-code-usererrorsnapshotmetadatanotfound"></a>Felkod: UserErrorSnapshotMetadataNotFound

Fel meddelande: diskens ögonblicks bilds-metadata för den här återställnings punkten har tagits bort

Rekommenderad åtgärd: Använd en annan återställnings punkt för att återställa. Mer information finns i dokumentationen för [återställning](restore-managed-disks.md).

### <a name="error-code-usererrormaxconcurrentoperationlimitreached"></a>Felkod: UserErrorMaxConcurrentOperationLimitReached

Fel meddelande: det går inte att starta åtgärden eftersom det maximala antalet tillåtna samtidiga åtgärder har nåtts för den här åtgärds typen.

Rekommenderad åtgärd: vänta tills föregående åtgärder har slutförts.

## <a name="next-steps"></a>Nästa steg

- [Support mat ris för Azure disk backup](disk-backup-support-matrix.md)
