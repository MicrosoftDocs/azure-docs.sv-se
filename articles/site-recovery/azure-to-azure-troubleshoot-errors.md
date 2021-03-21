---
title: Felsöka Azure VM-replikering i Azure Site Recovery
description: Felsök fel vid replikering av virtuella Azure-datorer för haveri beredskap.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: 6d61a44e671c43754fa7cccbe8ea8fe54eeba387
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97900424"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Felsöka problem med replikering av virtuella datorer i Azure till Azure-scenarier

Den här artikeln beskriver hur du felsöker vanliga fel i Azure Site Recovery vid replikering och återställning av virtuella Azure-datorer (VM) från en region till en annan. Mer information om konfigurationer som stöds finns i [support mat ris för replikering av virtuella Azure-datorer](azure-to-azure-support-matrix.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problem med resurs kvoter i Azure (felkod 150097)

Se till att din prenumeration är aktive rad för att skapa virtuella Azure-datorer i mål regionen som du planerar att använda som katastrof återställnings region (DR). Din prenumeration behöver tillräcklig kvot för att skapa virtuella datorer av de nödvändiga storlekarna. Som standard väljer Site Recovery en storlek för virtuell måldator som är samma som storleken på den virtuella käll datorn. Om den matchande storleken inte är tillgänglig väljer Site Recovery automatiskt den närmast tillgängliga storleken.

Om det inte finns någon storlek som stöder den virtuella käll konfigurationen visas följande meddelande:

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>Möjliga orsaker

- Ditt prenumerations-ID har inte Aktiver ATS för att skapa virtuella datorer i mål regionens plats.
- Ditt prenumerations-ID är inte aktiverat eller har inte tillräcklig kvot för att skapa specifika VM-storlekar på platsen för mål regionen.
- Ingen lämplig storlek på virtuell måldator hittades för den virtuella käll datorns nätverkskort (NIC), för prenumerations-ID: t i mål regionens plats.

### <a name="fix-the-problem"></a>Åtgärda problemet

Kontakta [Azures fakturerings support](../azure-portal/supportability/resource-manager-core-quotas-request.md) för att aktivera din prenumeration för att skapa virtuella datorer av de nödvändiga storlekarna på mål platsen. Försök sedan utföra åtgärden igen.

Om mål platsen har en kapacitets begränsning inaktiverar du replikering till den platsen. Aktivera sedan replikering till en annan plats där prenumerationen har tillräcklig kvot för att skapa virtuella datorer av de nödvändiga storlekarna.

## <a name="trusted-root-certificates-error-code-151066"></a>Betrodda rot certifikat (felkod 151066)

Om inte alla de senaste betrodda rot certifikaten finns på den virtuella datorn kan det hända att jobbet för att aktivera replikering för Site Recovery Miss förväntas. Autentisering och auktorisering av Site Recovery tjänst anrop från den virtuella datorn fungerar inte utan dessa certifikat.

Om jobbet aktivera replikering Miss lyckas visas följande meddelande:

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>Möjlig orsak

De betrodda rot certifikat som krävs för auktorisering och autentisering finns inte på den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

#### <a name="windows"></a>Windows

För en virtuell dator som kör Windows-operativsystemet installerar du de senaste Windows-uppdateringarna så att alla betrodda rot certifikat finns på den virtuella datorn. Följ den vanliga processen för hantering av Windows Update-eller certifikat uppdatering i din organisation för att hämta de senaste rot certifikaten och den uppdaterade listan över återkallade certifikat på de virtuella datorerna.

- Om du befinner dig i en frånkopplad miljö följer du standard processen för Windows Update i din organisation för att hämta certifikaten.
- Om de nödvändiga certifikaten inte finns på den virtuella datorn, kan anropen till tjänsten Site Recovery inte utföras av säkerhets skäl.

Kontrol lera att problemet är löst genom att gå till `login.microsoftonline.com` från en webbläsare på den virtuella datorn.

Mer information finns i [Konfigurera betrodda rötter och otillåtna certifikat](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11)).

#### <a name="linux"></a>Linux

Följ de rikt linjer som tillhandahålls av distributören av Linux-operativsystemet för att hämta de senaste betrodda rot certifikaten och den senaste listan över återkallade certifikat på den virtuella datorn.

Eftersom SUSE Linux använder symboliska länkar, eller symlinks, för att underhålla en certifikat lista, följer du dessa steg:

1. Logga in som **rot** användare. Hash-symbolen ( `#` ) är standard kommando tolken.

1. Kör det här kommandot om du vill ändra katalogen:

   `cd /etc/ssl/certs`

1. Kontrol lera om Symantec-rotcertifikatet för rot certifikat finns:

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - Om Symantecs CA-certifikat inte hittas kör du följande kommando för att ladda ned filen. Kontrol lera om det finns några fel och följ rekommenderade åtgärder för nätverks fel.

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. Kontrol lera om Baltimore rot certifikat finns:

   `ls Baltimore_CyberTrust_Root.pem`

   - Om Baltimore rot certifikat utfärdare inte hittas kör du det här kommandot för att ladda ned certifikatet:

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. Kontrol lera om DigiCert_Global_Root_CA certifikatet finns:

   `ls DigiCert_Global_Root_CA.pem`

    - Om DigiCert_Global_Root_CA inte hittas kör du följande kommandon för att ladda ned certifikatet:

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. Om du vill uppdatera certifikat mottagarens hashar för de nyligen hämtade certifikaten, kör du återhash-skriptet:

   `c_rehash`

1. Kör följande kommandon för att kontrol lera om mottagar hashar som symlinks har skapats för certifikaten:

   ```shell
   ls -l | grep Baltimore
   ```

   ```Output
   lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem

   -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem
   ```

   ```shell
   ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

   lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
   ```

   ```shell
   ls -l | grep DigiCert_Global_Root
   ```

   ```Output
   lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem

   -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem
   ```

1. Skapa en kopia av filen _VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem_ med fil namnet _b204d74a. 0_:

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. Skapa en kopia av filen _Baltimore_CyberTrust_Root. pem_ med fil namnet _653b494a. 0_:

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. Skapa en kopia av filen _DigiCert_Global_Root_CA. pem_ med fil namnet _3513523f. 0_:

   `cp DigiCert_Global_Root_CA.pem 3513523f.0`

1. Kontrol lera att filerna finns:

   ```shell
   ls -l 653b494a.0 b204d74a.0 3513523f.0
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0

   -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0

   -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0
   ```

## <a name="outbound-urls-or-ip-ranges-error-code-151037-or-151072"></a>Utgående URL: er eller IP-intervall (felkod 151037 eller 151072)

För att Site Recovery replikering ska fungera krävs utgående anslutning till vissa URL: er från den virtuella datorn. Om den virtuella datorn ligger bakom en brand vägg eller använder regler för nätverks säkerhets grupper (NSG) för att kontrol lera utgående anslutningar kan du stöta på något av dessa problem. Även om vi fortsätter att stödja utgående åtkomst via URL: er, stöds inte längre listan över tillåtna IP-intervall.

#### <a name="possible-causes"></a>Möjliga orsaker

- Det går inte att upprätta en anslutning till Site Recovery slut punkter på grund av ett matchnings problem i Domain Name System (DNS).
- Det här problemet är vanligare under skyddet när du har redundansväxlats på den virtuella datorn men det går inte att komma åt DNS-servern från haveri beredskap (DR).

#### <a name="fix-the-problem"></a>Åtgärda problemet

Om du använder anpassad DNS måste du kontrol lera att DNS-servern är tillgänglig från Disaster Recovery-regionen.

Kontrol lera om den virtuella datorn använder en anpassad DNS-inställning:

1. Öppna **virtuella datorer** och välj den virtuella datorn.
1. Navigera till **inställningarna** för virtuella datorer och välj **nätverk**.
1. I **virtuellt nätverk/undernät** väljer du länken för att öppna det virtuella nätverkets resurs sida.
1. Gå till **Inställningar** och välj **DNS-servrar**.

Försök att komma åt DNS-servern från den virtuella datorn. Om DNS-servern inte är tillgänglig kan du göra den tillgänglig genom att antingen redundansväxla DNS-servern eller skapa en plats mellan DR-nätverket och DNS.

> [!NOTE]
> Om du använder privata slut punkter måste du se till att de virtuella datorerna kan matcha de privata DNS-posterna.

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-fel.":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problem 2: Site Recovery konfiguration misslyckades (151196)

#### <a name="possible-cause"></a>Möjlig orsak

Det går inte att upprätta en anslutning till Microsoft 365-slutpunkter för autentisering och identitet IP4.

#### <a name="fix-the-problem"></a>Åtgärda problemet

Azure Site Recovery nödvändig åtkomst till Microsoft 365 IP-intervall för autentisering.
Om du använder Azure nätverks säkerhets grupp (NSG) regler/brand Väggs-proxy för att kontrol lera utgående nätverks anslutning på den virtuella datorn ska du kontrol lera att du använder [Azure Active Directory (AAD) service tag](../virtual-network/network-security-groups-overview.md#service-tags) -baserad NSG-regel för att tillåta åtkomst till AAD. Vi stöder inte längre IP-adressbaserade NSG-regler.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problem 3: Site Recovery konfiguration misslyckades (151197)

#### <a name="possible-cause"></a>Möjlig orsak

Det går inte att upprätta en anslutning till Azure Site Recovery tjänstens slut punkter.

#### <a name="fix-the-problem"></a>Åtgärda problemet

Om du använder Azure nätverks säkerhets grupp (NSG) regler/brand Väggs-proxy för att kontrol lera utgående nätverks anslutning på den virtuella datorn, måste du använda service märken. Vi har inte längre stöd för att använda en lista över tillåtna IP-adresser via NSG: er för Azure Site Recovery.

### <a name="issue-4-replication-fails-when-network-traffic-uses-on-premises-proxy-server-151072"></a>Problem 4: replikeringen Miss lyckas när nätverks trafiken använder en lokal proxyserver (151072)

#### <a name="possible-cause"></a>Möjlig orsak

Inställningarna för den anpassade proxyn är ogiltiga och mobilitets tjänst agenten identifierade inte proxyinställningarna automatiskt från Internet Explorer (IE).

#### <a name="fix-the-problem"></a>Åtgärda problemet

1. Mobilitets tjänst agenten identifierar proxyinställningarna från IE i Windows och `/etc/environment` på Linux.
1. Om du föredrar att bara ange proxy för mobilitets tjänsten kan du ange proxyinformation i _ProxyInfo. conf_ på:

   - **Linux**: `/usr/local/InMage/config/`
   - **Windows**: `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo. conf_ ska ha proxyinställningarna i följande _ini_ -format.

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Mobilitets tjänst agenten stöder bara **oautentiserade proxyservrar**.

### <a name="more-information"></a>Mer information

Om du vill ange [nödvändiga URL: er](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) eller de [IP-adressintervall som krävs](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)följer du anvisningarna i [om nätverk i Azure till Azure-replikering](azure-to-azure-about-networking.md).

## <a name="disk-not-found-in-vm-error-code-150039"></a>Disken hittades inte i den virtuella datorn (felkod 150039)

En ny disk som är ansluten till den virtuella datorn måste initieras. Om disken inte hittas visas följande meddelande:

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>Möjliga orsaker

- En ny datadisk har kopplats till den virtuella datorn men initierades inte.
- Datadisken i den virtuella datorn rapporterar inte korrekt Logical Unit Number (LUN)-värdet som disken var kopplad till den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

Kontrol lera att data diskarna har initierats och försök sedan igen.

- **Windows**: [Anslut och initiera en ny disk](../virtual-machines/windows/attach-managed-disk-portal.md).
- **Linux**: [initiera en ny datadisk i Linux](../virtual-machines/linux/add-disk.md).

Kontakta supporten om problemet kvarstår.

## <a name="multiple-disks-available-for-protection-error-code-153039"></a>Flera diskar är tillgängliga för skydd (felkod 153039)

### <a name="possible-causes"></a>Möjliga orsaker

- En eller flera diskar har nyligen lagts till på den virtuella datorn efter skyddet.
- En eller flera diskar initierades efter skyddet av den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

Om du vill göra replikeringsstatus för den virtuella datorn felfritt igen kan du välja att skydda diskarna eller stänga av varningen.

#### <a name="to-protect-the-disks"></a>Skydda diskarna

1. Gå till **replikerade objekt**  >  _namn diskar för virtuella datorer_  >  .
1. Välj den oskyddade disken och välj sedan **Aktivera replikering**:

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="Aktivera replikering på VM-diskar.":::

#### <a name="to-dismiss-the-warning"></a>Ignorera varningen

1. Gå till **replikerade objekt**  >  _namn på virtuell dator_.
1. Välj varningen i avsnittet **Översikt** och välj sedan **OK**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="Ignorera varning om ny disk.":::

## <a name="vm-removed-from-vault-completed-with-information-error-code-150225"></a>Den virtuella datorn har tagits bort från valvet med information (felkod 150225)

När Site Recovery skyddar den virtuella datorn skapas länkar på den virtuella käll datorn. När du tar bort skyddet eller inaktiverar replikering tar Site Recovery bort dessa länkar som en del av rensnings jobbet. Om den virtuella datorn har ett resurs lås slutförs rensnings jobbet med informationen. Informationen anger att den virtuella datorn har tagits bort från Recovery Services valvet, men att vissa av de inaktuella länkarna inte kunde rensas på käll datorn.

Du kan ignorera den här varningen om du aldrig planerar att skydda den här virtuella datorn igen. Men om du behöver skydda den här virtuella datorn senare följer du stegen i det här avsnittet för att rensa länkarna.

> [!WARNING]
> Om du inte gör rensningen:
>
> - När du aktiverar replikering med hjälp av Recovery Services valvet visas inte den virtuella datorn.
> - Om du försöker skydda den virtuella datorn med hjälp av haveri beredskap för en **virtuell dator** kan  >    >  åtgärden **inte utföras eftersom det inte går att aktivera meddelande replikeringen på grund av befintliga inaktuella resurs länkar på den virtuella datorn**.

### <a name="fix-the-problem"></a>Åtgärda problemet

> [!NOTE]
> Site Recovery inte tar bort den virtuella käll datorn eller påverkar den på något sätt när du utför de här stegen.

1. Ta bort låset från den virtuella datorn eller resurs gruppen för den virtuella datorn. I följande bild måste resurs låset på den virtuella datorn med namnet `MoveDemo` tas bort:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Ta bort låset från den virtuella datorn.":::

1. Hämta skriptet för att [ta bort en föråldrad Site Recovery-konfiguration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Kör skriptet _Cleanup-stale-asr-config-Azure-VM.ps1_. Ange **prenumerations-ID**, **resurs grupp för virtuell dator** och **VM-namn** som parametrar.
1. Om du uppmanas att ange Azure-autentiseringsuppgifter anger du dem. Kontrol lera sedan att skriptet körs utan problem.

## <a name="replication-not-enabled-on-vm-with-stale-resources-error-code-150226"></a>Replikering har inte Aktiver ATS på den virtuella datorn med inaktuella resurser (felkod 150226)

### <a name="possible-causes"></a>Möjliga orsaker

Den virtuella datorn har en inaktuell konfiguration från föregående Site Recovery skydd.

En föråldrad konfiguration kan ske på en virtuell Azure-dator om du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och sedan:

- Du har inaktiverat replikering, men den virtuella käll datorn hade ett resurs lås.
- Du har tagit bort Site Recovery valvet utan att uttryckligen inaktivera replikering på den virtuella datorn.
- Du har tagit bort resurs gruppen som innehåller Site Recovery valvet utan att uttryckligen inaktivera replikering på den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

> [!NOTE]
> Site Recovery inte tar bort den virtuella käll datorn eller påverkar den på något sätt när du utför de här stegen.

1. Ta bort låset från den virtuella datorn eller resurs gruppen för den virtuella datorn. I följande bild måste resurs låset på den virtuella datorn med namnet `MoveDemo` tas bort:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Ta bort låset från den virtuella datorn.":::

1. Hämta skriptet för att [ta bort en föråldrad Site Recovery-konfiguration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Kör skriptet _Cleanup-stale-asr-config-Azure-VM.ps1_. Ange **prenumerations-ID**, **resurs grupp för virtuell dator** och **VM-namn** som parametrar.
1. Om du uppmanas att ange Azure-autentiseringsuppgifter anger du dem. Kontrol lera sedan att skriptet körs utan problem.

## <a name="cant-select-vm-or-resource-group-in-enable-replication-job"></a>Det går inte att välja VM eller resurs grupp i Aktivera migreringsjobb

### <a name="issue-1-the-resource-group-and-source-vm-are-in-different-locations"></a>Problem 1: resurs gruppen och den virtuella käll datorn finns på olika platser

Site Recovery kräver för närvarande resurs gruppen för käll regionen och de virtuella datorerna på samma plats. Om de inte är det kan du inte hitta den virtuella datorn eller resurs gruppen när du försöker tillämpa skyddet.

Som en lösning kan du aktivera replikering från den virtuella datorn i stället för Recovery Services valvet. Gå till **käll dator**  >  **Egenskaper**  >  **haveri återställning** och aktivera replikeringen.

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>Problem 2: resurs gruppen ingår inte i den valda prenumerationen

Du kanske inte kan hitta resurs gruppen vid tidpunkten för skyddet om resurs gruppen inte ingår i den valda prenumerationen. Kontrol lera att resurs gruppen tillhör den prenumeration som du använder.

### <a name="issue-3-stale-configuration"></a>Problem 3: inaktuell konfiguration

Du kanske inte ser den virtuella datorn som du vill aktivera för replikering om det finns en inaktuell Site Recovery konfiguration på den virtuella Azure-datorn. Det här tillståndet kan inträffa om du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och sedan:

- Du har tagit bort Site Recovery valvet utan att uttryckligen inaktivera replikering på den virtuella datorn.
- Du har tagit bort resurs gruppen som innehåller Site Recovery valvet utan att uttryckligen inaktivera replikering på den virtuella datorn.
- Du har inaktiverat replikering, men den virtuella käll datorn hade ett resurs lås.

### <a name="fix-the-problem"></a>Åtgärda problemet

> [!NOTE]
> Se till att uppdatera `AzureRM.Resources` modulen innan du använder skriptet som nämns i det här avsnittet. Site Recovery inte tar bort den virtuella käll datorn eller påverkar den på något sätt när du utför de här stegen.

1. Ta bort låset, om det finns, från den virtuella datorn eller resurs gruppen för den virtuella datorn. I följande bild måste resurs låset på den virtuella datorn med namnet `MoveDemo` tas bort:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Ta bort låset från den virtuella datorn.":::

1. Hämta skriptet för att [ta bort en föråldrad Site Recovery-konfiguration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Kör skriptet _Cleanup-stale-asr-config-Azure-VM.ps1_. Ange **prenumerations-ID**, **resurs grupp för virtuell dator** och **VM-namn** som parametrar.
1. Om du uppmanas att ange Azure-autentiseringsuppgifter anger du dem. Kontrol lera sedan att skriptet körs utan problem.

## <a name="unable-to-select-a-vm-for-protection"></a>Det går inte att välja en virtuell dator för skydd

### <a name="possible-cause"></a>Möjlig orsak

Ett tillägg har installerats på den virtuella datorn i ett tillstånd där det inte gick att svara

### <a name="fix-the-problem"></a>Åtgärda problemet

Gå till inställningar för **Virtual Machines**-  >    >  **tillägg** och Sök efter eventuella tillägg i felaktigt tillstånd. Avinstallera eventuella misslyckade tillägg och försök sedan igen för att skydda den virtuella datorn.

## <a name="vm-provisioning-state-isnt-valid-error-code-150019"></a>Etablerings statusen för den virtuella datorn är inte giltig (felkod 150019)

Om du vill aktivera replikering på den virtuella datorn måste etablerings statusen vara **klar**. Följ de här stegen för att kontrol lera etablerings statusen:

1. I Azure Portal väljer du **Resursläsaren** från **alla tjänster**.
1. Expandera listan **prenumerationer** och välj din prenumeration.
1. Expandera listan **ResourceGroups** och välj den virtuella datorns resurs grupp.
1. Expandera listan **resurser** och välj den virtuella datorn.
1. Kontrol lera fältet **provisioningState** i instans visningen på höger sida.

### <a name="fix-the-problem"></a>Åtgärda problemet

- Om **ProvisioningState** **Miss lyckas** kan du kontakta supporten med information om fel sökning.
- Om **ProvisioningState** **uppdateras** kan ett annat tillägg distribueras. Kontrol lera om det finns pågående åtgärder på den virtuella datorn, vänta tills de har slutförts och Site Recovery försök sedan att aktivera replikeringen igen.

## <a name="unable-to-select-target-vm"></a>Det går inte att välja virtuell måldator

### <a name="issue-1-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Problem 1: den virtuella datorn är ansluten till ett nätverk som redan har mappats till ett mål nätverk

Om den virtuella käll datorn är en del av ett virtuellt nätverk och en annan virtuell dator i samma virtuella nätverk redan har mappats till ett nätverk i mål resurs gruppen, är List rutan nätverks val inte tillgänglig (visas nedtonad) som standard under konfigurationen för haveri beredskap.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="Listan över nätverks val är inte tillgänglig.":::

### <a name="issue-2-you-previously-protected-the-vm-and-then-you-disabled-the-replication"></a>Problem 2: du har tidigare skyddat den virtuella datorn och sedan inaktiverade replikeringen

Om du inaktiverar replikering av en virtuell dator tas inte nätverks mappningen bort. Mappningen måste tas bort från det Recovery Services valv där den virtuella datorn skyddades. Välj **Recovery Services valvet** och gå till **Hantera**  >  **Site Recovery-infrastruktur**  >  **för nätverks mappning av virtuella Azure-datorer**  >  .

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="Ta bort nätverks mappning.":::

Mål nätverket som konfigurerades under installationen av haveri beredskap kan ändras efter den inledande installationen och när den virtuella datorn är skyddad. **Ändra nätverks mappningen** genom att välja nätverks namn:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="Ändra nätverks mappning.":::


## <a name="com-or-vss-error-code-151025"></a>COM+ eller VSS (felkod 151025)

När ett fel uppstår i COM+ eller tjänsten Volume Shadow Copy (VSS) visas följande meddelande:

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>Möjliga orsaker

- COM+-system tillämpnings tjänsten är inaktive rad.
- Tjänsten Volume Shadow Copy är inaktive rad.

### <a name="fix-the-problem"></a>Åtgärda problemet

Ange COM+-systemprogram och tjänsten Volume Shadow Copy till automatiskt eller manuellt start läge.

1. Öppna konsolen tjänster i Windows.
1. Kontrol lera att COM+-systemprogrammet och tjänsten Volume Shadow Copy inte är inställt på **inaktive rad** som **Start metod**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="Kontrol lera starttyp för COM plus system program och tjänsten Volume Shadow Copy.":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Hanterad disk storlek stöds inte (felkod 150172)

När det här felet uppstår visas följande meddelande:

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>Möjlig orsak

Disken är mindre än den storlek som stöds på 1024 MB.

### <a name="fix-the-problem"></a>Åtgärda problemet

Kontrol lera att disk storleken ligger inom det storleks intervall som stöds och försök sedan igen.

## <a name="protection-not-enabled-when-grub-uses-device-name-error-code-151126"></a>Skydd är inte aktiverat när GRUB använder enhets namnet (felkod 151126)

### <a name="possible-causes"></a>Möjliga orsaker

Konfigurationsfilerna för Linux Grand Unified startGRUB (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/Boot/grub2/grub.cfg_ eller _/etc/default/grub_) kan ange de faktiska enhets namnen i stället för UUID-värden (Universal Unique Identifier) för `root` `resume` parametrarna och. Site Recovery kräver UUID: er eftersom enhets namn kan ändras. Vid omstart kanske det inte finns någon virtuell dator med samma namn vid redundansväxling, vilket leder till problem.

Följande exempel är rader från GRUB-filer där enhets namn visas i stället för nödvändiga UUID: er:

- Fil _/Boot/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Fil: _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>Åtgärda problemet

Ersätt varje enhets namn med motsvarande UUID:

1. Hitta enhetens UUID genom att köra kommandot `blkid <device name>` . Exempel:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Ersätt enhets namnet med dess UUID i formaten `root=UUID=<UUID>` och `resume=UUID=<UUID>` . Till exempel skulle raden från _/boot/grub/menu.lst_ se ut som på följande rad:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Gör om skyddet.

## <a name="protection-failed-because-grub-device-doesnt-exist-error-code-151124"></a>Skyddet misslyckades eftersom GRUB-enheten inte finns (felkod 151124)

### <a name="possible-cause"></a>Möjlig orsak

Konfigurationsfilerna för GRUB (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/Boot/grub2/grub.cfg_ eller _/etc/default/grub_) kan innehålla parametrarna `rd.lvm.lv` eller `rd_LVM_LV` . Dessa parametrar identifierar de LVM-enheter (Logical Volume Manager) som ska identifieras vid start. Om dessa LVM-enheter inte finns startar inte det skyddade systemet och kommer att fastna i Start processen. Samma problem visas också med den virtuella redundansväxlingen. Här följer några exempel:

- Fil: _/Boot/grub2/grub.cfg_ på RHEL7:

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- Fil: _/etc/default/grub_ på RHEL7:

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- Fil: _/boot/grub/menu.lst_ på RHEL6:

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

I varje exempel måste GRUB identifiera två LVM-enheter med namnen `root` och `swap` från volym gruppen `rootvg` .

### <a name="fix-the-problem"></a>Åtgärda problemet

Om LVM-enheten inte finns skapar du den eller tar bort motsvarande parametrar från GRUB-konfigurationsfilerna. Försök sedan igen för att aktivera skyddet.

## <a name="mobility-service-update-finished-with-warnings-error-code-151083"></a>Mobilitets tjänsten har uppdaterats med varningar (felkod 151083)

Tjänsten Site Recovery Mobility har många komponenter, en av dessa kallas filter driv rutinen. Filter driv rutinen läses in i system minnet endast under omstarten av systemet. När en mobilitets tjänst uppdatering innehåller ändringar av filter driv rutinen, uppdateras datorn men du ser fortfarande en varning om att vissa korrigeringar kräver en omstart. Varningen visas eftersom filter driv rutins korrigeringarna endast börjar gälla när den nya filter driv rutinen läses in, vilket bara sker under en omstart.

> [!NOTE]
> Detta är endast en varning. Den befintliga replikeringen fortsätter att fungera även efter den nya agent uppdateringen. Du kan välja att starta om när du vill ha fördelarna med den nya filter driv rutinen, men den gamla filter driv rutinen fortsätter att fungera om du inte startar om.
>
> Förutom filter driv rutinen börjar fördelarna med eventuella andra förbättringar och korrigeringar i mobilitets tjänstens uppdatering att gälla utan att behöva starta om.

## <a name="protection-not-enabled-if-replica-managed-disk-exists"></a>Skydd är inte aktiverat om replik hanterad disk finns

Felet uppstår när den hanterade replik disken redan finns, utan förväntade taggar, i mål resurs gruppen.

### <a name="possible-cause"></a>Möjlig orsak

Det här problemet kan uppstå om den virtuella datorn tidigare skyddades och när replikeringen inaktiverades togs inte replik disken bort.

### <a name="fix-the-problem"></a>Åtgärda problemet

Ta bort replik disken som identifierats i fel meddelandet och försök igen med det misslyckade skydds jobbet.

## <a name="enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137"></a>Det gick inte att aktivera skydd eftersom installations programmet inte kan hitta rot disken (felkod 151137)

Det här felet inträffar för Linux-datorer där operativ system disken är krypterad med Azure Disk Encryption (ADE). Detta är bara ett giltigt problem i agent version 9,35.

### <a name="possible-causes"></a>Möjliga orsaker

Installations programmet kan inte hitta rot disken som är värd för rot fil systemet.

### <a name="fix-the-problem"></a>Åtgärda problemet

Åtgärda problemet genom att följa stegen nedan.

1. Hitta agent bitarna under katalogen _/var/lib/waagent_ på RHEL-och CentOS-datorer med kommandot nedan: <br>

    `# find /var/lib/ -name Micro\*.gz`

   Förväntad utdata:

    `/var/lib/waagent/Microsoft.Azure.RecoveryServices.SiteRecovery.LinuxRHEL7-1.0.0.9139/UnifiedAgent/Microsoft-ASR_UA_9.35.0.0_RHEL7-64_GA_30Jun2020_release.tar.gz`

2. Skapa en ny katalog och ändra katalogen till den nya katalogen.
3. Extrahera agent filen som finns i det första steget här, med kommandot nedan:

    `tar -xf <Tar Ball File>`

4. Öppna filen _prereq_check_installer.jspå_ och ta bort följande rader. Spara filen efter det.

    ```
       {
          "CheckName": "SystemDiskAvailable",
          "CheckType": "MobilityService"
       },
    ```
5. Anropa installations programmet med kommandot: <br>

    `./install -d /usr/local/ASR -r MS -q -v Azure`
6. Om installations programmet lyckas kan du försöka aktivera replikerings jobbet igen.

## <a name="next-steps"></a>Nästa steg

[Replikera virtuella Azure-datorer till en annan Azure-region](azure-to-azure-how-to-enable-replication.md)