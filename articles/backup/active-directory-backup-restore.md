---
title: Säkerhetskopiera och återställa Active Directory
description: Lär dig hur du säkerhetskopierar och återställer Active Directory-domänkontrollanter.
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8db2dab605e90e4748b11a632d6651c23d631b6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98733561"
---
# <a name="back-up-and-restore-active-directory-domain-controllers"></a>Säkerhetskopiera och återställa Active Directory domänkontrollanter

Att säkerhetskopiera Active Directory och se till att lyckade återställningar i fall av skada, kompromisser eller haveri är en viktig del av Active Directory underhållet.

Den här artikeln beskriver de rätta procedurerna för att säkerhetskopiera och återställa Active Directory domänkontrollanter med Azure Backup, oavsett om de är virtuella Azure-datorer eller lokala servrar. I den här artikeln beskrivs ett scenario där du måste återställa en hel domänkontrollant till dess tillstånd vid säkerhets kopieringen. Information om vilket återställnings scenario som passar dig bäst finns i [den här artikeln](/windows-server/identity/ad-ds/manage/ad-forest-recovery-determine-how-to-recover).  

>[!NOTE]
> Den här artikeln beskriver inte hur du återställer objekt från [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Information om hur du återställer Azure Active Directory användare finns i [den här artikeln](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="best-practices"></a>Bästa praxis

- Se till att minst en domänkontrollant har säkerhetskopierats. Om du säkerhetskopierar fler än en domänkontrollant ser du till att alla de som innehar [FSMO-roller (Flexible Single Master operation)](/windows-server/identity/ad-ds/plan/planning-operations-master-role-placement) säkerhets kopie ras.

- Säkerhetskopiera Active Directory ofta. Säkerhets kopian ska aldrig vara större än tombstone-livstiden (som standard 60 dagar), eftersom objekt som är äldre än tombstone-livstiden blir "tombstone" och inte längre anses giltiga.

- Ha en klar katastrof återställnings plan som innehåller instruktioner om hur du återställer domän kontrol Lanterna. Om du vill förbereda för att återställa en Active Directory skog läser du [Active Directory skogs återställnings guide](/windows-server/identity/ad-ds/manage/ad-forest-recovery-guide).

- Om du behöver återställa en domänkontrollant och har en återstående fungerande domänkontrollant i domänen, kan du skapa en ny server i stället för att återställa från en säkerhets kopia. Lägg till **Active Directory Domain Services** Server rollen till den nya servern så att den blir en domänkontrollant i den befintliga domänen. Sedan replikeras Active Directory-data till den nya servern. Om du vill ta bort den tidigare domänkontrollanten från Active Directory följer du stegen [i den här artikeln](/windows-server/identity/ad-ds/deploy/ad-ds-metadata-cleanup) för att utföra rensning av metadata.

>[!NOTE]
>Azure Backup innehåller inte återställning på objekt nivå för Active Directory. Om du vill återställa borttagna objekt och du har åtkomst till en domänkontrollant använder du [Active Directory pappers korgen](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#ad_recycle_bin_mgmt). Om den metoden inte är tillgänglig kan du använda säkerhets kopian av domänkontrollanten för att återställa de borttagna objekten med **ntdsutil.exe** -verktyget som beskrivs [här](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac).
>
>Information om hur du utför en auktoritativ återställning av SYSVOL finns i [den här artikeln](/windows-server/identity/ad-ds/manage/ad-forest-recovery-authoritative-recovery-sysvol).

## <a name="backing-up-azure-vm-domain-controllers"></a>Säkerhetskopiera Azure VM-domänkontrollanter

Om domänkontrollanten är en virtuell Azure-dator kan du Säkerhetskopiera servern med hjälp av [Azure VM backup](backup-azure-vms-introduction.md).

Läs om [operativa överväganden för virtualiserade](/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v#operational-considerations-for-virtualized-domain-controllers) domänkontrollanter för att säkerställa att lyckade säkerhets kopieringar (och framtida återställningar) av dina Azure VM-domänkontrollanter.

## <a name="backing-up-on-premises-domain-controllers"></a>Säkerhetskopiera lokala domänkontrollanter

Om du vill säkerhetskopiera en lokal domänkontrollant måste du säkerhetskopiera serverns system tillstånds data.

- Följ [dessa instruktioner](backup-azure-system-state.md)om du använder mars.
- Följ [dessa instruktioner](backup-mabs-system-state-and-bmr.md)om du använder MABS (Azure Backup Server).

>[!NOTE]
> Det går inte att återställa lokala domänkontrollanter (antingen från system tillstånd eller från virtuella datorer) till Azure-molnet. Om du vill ha möjlighet att redundansväxla från en lokal Active Directory miljö till Azure bör du överväga att använda [Azure Site Recovery](../site-recovery/site-recovery-active-directory.md).

## <a name="restoring-active-directory"></a>Återställa Active Directory

Active Directory data kan återställas i ett av två lägen: **auktoritativa** eller icke- **auktoritativa**. I en auktoritativ återställning åsidosätter de återställda Active Directory data de data som finns på de andra domän kontrol Lanterna i skogen.

I det här scenariot återskapar vi dock en domänkontrollant i en befintlig domän, så en icke- **auktoritativ** återställning bör utföras.

Under återställningen kommer servern att startas i DSRM-läge (Directory Services Restore Mode). Du måste ange administratörs lösen ord för återställnings läget för katalog tjänster.

>[!NOTE]
>Om DSRM-lösenordet är glömt kan du återställa det med hjälp av [de här anvisningarna](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc754363(v=ws.11)).

### <a name="restoring-azure-vm-domain-controllers"></a>Återställa domänkontrollanter i Azure VM

Information om hur du återställer en Azure VM-domänkontrollant finns i [återställa virtuella datorer](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)i domänkontrollanten.

Om du återställer en virtuell dator med en virtuell domänkontrollant eller flera virtuella domänkontrollanter i en enda domän, återställer du dem som vilken annan virtuell dator som helst. Återställnings läge för katalog tjänster (DSRM) är också tillgängligt, så alla Active Directory återställnings scenarier är lönsamma.

Om du behöver återställa en virtuell dator med en virtuell domänkontrollant i en konfiguration för flera domäner återställer du diskarna och skapar en virtuell dator [med hjälp av PowerShell](backup-azure-vms-automation.md#restore-the-disks).

Om du återställer den sista återstående domänkontrollanten i domänen eller återställer flera domäner i en skog, rekommenderar vi en [skogs återställning](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).

>[!NOTE]
> Virtualiserade domänkontrollanter, från Windows 2012 och senare, använder [virtualiseringsbaserad skydd](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100#virtualization-based-safeguards). Med dessa skydd förstår Active Directory om den virtuella datorn är en domänkontrollant och utför nödvändiga steg för att återställa Active Directory data.

### <a name="restoring-on-premises-domain-controllers"></a>Återställa lokala domänkontrollanter

Om du vill återställa en lokal domänkontrollant följer du anvisningarna i för att återställa system tillstånd till Windows Server med hjälp av rikt linjerna för [särskilda överväganden vid återställning av system tillstånd på en](backup-azure-restore-system-state.md#special-considerations-for-system-state-recovery-on-a-domain-controller)domänkontrollant.

## <a name="next-steps"></a>Nästa steg

- [Support mat ris för Azure Backup](backup-support-matrix.md)