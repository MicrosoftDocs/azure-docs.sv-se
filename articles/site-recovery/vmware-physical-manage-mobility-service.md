---
title: Hantera mobilitets agenten för VMware/fysiska servrar med Azure Site Recovery
description: Hantera mobilitets tjänst agenten för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av tjänsten Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: d921bddf90c415cb244e2cc9ad98354392a537ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530173"
---
# <a name="manage-the-mobility-agent"></a>Hantera mobilitetsagenten 

Du konfigurerar mobilitets agenten på servern när du använder Azure Site Recovery för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure. Mobilitets agenten samordnar kommunikationen mellan den skyddade datorn, konfigurations servern/skalbar processervern och hanterar datareplikering. Den här artikeln sammanfattar vanliga uppgifter för att hantera mobilitets agenten när den har distribuerats.

>[!TIP]
>Information om hur du hämtar installations programmet för en speciell OS/Linux-distribution finns i vägledningen [här](vmware-physical-mobility-service-overview.md#locate-installer-files). Om du vill uppdatera från portalen automatiskt behöver du inte hämta installations programmet. [ASR hämtar automatiskt installations programmet från konfigurations servern och uppdaterar agenten](#update-mobility-service-from-azure-portal).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Uppdatera mobilitets tjänsten från Azure Portal

1. Innan du börjar kontrollerar du att konfigurations servern, skalbara process servrar och alla huvud mål servrar som ingår i distributionen uppdateras innan du uppdaterar mobilitets tjänsten på skyddade datorer.
    1. Från och med 9,36-versionen, för SUSE Linux Enterprise Server 11 SP3, RHEL 5, CentOS 5, Debian 7 Se till att det senaste installations programmet är [tillgängligt på konfigurations servern och den skalbara processervern](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server).
1. Öppna valvet > **replikerade objekt** i portalen.
1. Om konfigurations servern är den senaste versionen visas ett meddelande som läser "det går inte att uppdatera den nya Site Recovery-agenten. Klicka om du vill installera. "

     ![Fönstret replikerade objekt](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Klicka på meddelandet och i **agent uppdatering** väljer du de datorer som du vill uppgradera mobilitets tjänsten till. Klicka sedan på **OK**.

     ![Lista över replikerade objekt för virtuella datorer](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. Jobbet Uppdatera mobilitets tjänsten startar för var och en av de valda datorerna. Mobilitets agenten har uppdaterats till-versionen av konfigurations servern. Om konfigurations servern till exempel är i version 9,33, har mobilitets agenten på en skyddad virtuell dator också uppdaterats till 9,33 version.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Uppdatera mobilitets tjänsten via PowerShell-skript på Windows Server

Innan du börjar kontrollerar du att konfigurations servern, skalbara process servrar och alla huvud mål servrar som ingår i distributionen uppdateras innan du uppdaterar mobilitets tjänsten på skyddade datorer.

Använd följande skript för att uppgradera mobilitets tjänsten på en server via Power shell-cmdlet

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>Uppdatera mobilitets tjänsten manuellt på varje skyddad Server

1. Innan du börjar kontrollerar du att konfigurations servern, skalbara process servrar och alla huvud mål servrar som ingår i distributionen uppdateras innan du uppdaterar mobilitets tjänsten på skyddade datorer.

2. [Leta upp agent installations programmet](vmware-physical-mobility-service-overview.md#locate-installer-files) baserat på serverns operativ system.

>[!IMPORTANT]
> Använd inte den här metoden om du replikerar en virtuell Azure IaaS-dator från en Azure-region till en annan. Se [vår vägledning](azure-to-azure-autoupdate.md) för information om alla tillgängliga alternativ.

3. Kopiera installations filen till den skyddade datorn och kör den för att uppdatera mobilitets agenten.

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Uppdaterings konto som används för push-installation av mobilitets tjänsten

När du har distribuerat Site Recovery, för att aktivera push-installation av mobilitets tjänsten, angav du ett konto som Site Recovery processervern använder för att få åtkomst till datorerna och installera tjänsten när replikering har Aktiver ATS för datorn. Följ [dessa instruktioner](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)om du vill uppdatera autentiseringsuppgifterna för det här kontot.

## <a name="uninstall-mobility-service"></a>Avinstallera mobilitets tjänsten

### <a name="on-a-windows-machine"></a>På en Windows-dator

Avinstallera från användar gränssnittet eller från en kommando tolk.

- **Från användar gränssnittet**: i kontroll panelen på datorn väljer du **program**. Välj **Microsoft Azure Site Recovery mobilitets tjänst/huvud mål server**  >  **Avinstallera**.
- **Från en kommando tolk**: öppna ett kommando tolks fönster som administratör på datorn. Kör följande kommando: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>På en Linux-dator
1. Logga in som en **rot** användare på Linux-datorn.
2. I en Terminal går du till/usr/local/ASR.
3. Kör följande kommando:
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>Installera Site Recovery VSS-Provider på käll datorn

Azure Site Recovery VSS-Provider krävs på käll datorn för att generera program konsekvens punkter. Om installationen av providern inte lyckades genom push-installation följer du anvisningarna nedan för att installera den manuellt.

1. Öppna administratörens cmd-fönster.
2. Gå till installations platsen för mobilitets tjänsten. (Tex-C:\Program Files (x86) \Microsoft Azure Site Recovery\agent.)
3. Kör skriptet InMageVSSProvider_Uninstall. cmd. Detta kommer att avinstallera tjänsten om den redan finns.
4. Kör skriptet InMageVSSProvider_Install. cmd för att installera VSS-providern manuellt.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera katastrof återställning för virtuella VMware-datorer](vmware-azure-tutorial.md)
- [Konfigurera katastrof återställning för fysiska servrar](physical-azure-disaster-recovery.md)
