---
title: Azure HDInsight-arkitektur med Enterprise Security Package
description: Lär dig hur du planerar säkerheten för Azure HDInsight med Enterprise Security Package.
ms.reviewer: omidm
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/11/2020
ms.openlocfilehash: 404cd8652a4daf602f3326696ef5df2c3a23a548
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943315"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Använda Enterprise Security Package i HDInsight

Standard Azure HDInsight-klustret är ett kluster med en enda användare. Det är lämpligt för de flesta företag som har mindre program grupper som skapar stora data arbets belastningar. Varje användare kan skapa ett dedikerat kluster på begäran och förstöra det när det inte behövs längre.

Många företag har flyttats till en modell där IT-team hanterar kluster och flera program team delar kluster. Dessa större företag behöver ha åtkomst till flera användare till varje kluster i Azure HDInsight.

HDInsight förlitar sig på en populär identitets leverantör – Active Directory--på ett hanterat sätt. Genom att integrera HDInsight med [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/overview.md)kan du komma åt klustren med hjälp av dina domänautentiseringsuppgifter.

De virtuella datorerna (VM) i HDInsight är domänanslutna i den angivna domänen. Alla tjänster som körs på HDInsight (Apache Ambari, Apache Hive Server, Apache Ranger, Apache Spark Thrift-Server och andra) fungerar sömlöst för den autentiserade användaren. Administratörer kan sedan skapa starka Auktoriseringsprinciper genom att använda Apache Ranger för att tillhandahålla rollbaserad åtkomst kontroll för resurser i klustret.

## <a name="integrate-hdinsight-with-active-directory"></a>Integrera HDInsight med Active Directory

Apache Hadoop med öppen källkod förlitar sig på Kerberos-protokollet för autentisering och säkerhet. Därför är HDInsight-klusternoder med Enterprise Security Package (ESP) anslutna till en domän som hanteras av Azure AD DS. Kerberos-säkerhet har kon figurer ATS för Hadoop-komponenterna i klustret.

Följande saker skapas automatiskt:

- Ett tjänst huvud namn för varje Hadoop-komponent
- En dators huvud namn för varje dator som är ansluten till domänen
- En organisationsenhet (OU) för varje kluster för att lagra dessa tjänster och dator huvud namn

För att sammanfatta måste du konfigurera en miljö med:

- En Active Directory domän (hanteras av Azure AD DS). **Domän namnet måste innehålla 39 tecken eller mindre för att fungera med Azure HDInsight.**
- Säkert LDAP (LDAPs) som är aktiverade i Azure AD DS.
- Korrekt nätverks anslutning från det virtuella HDInsight-nätverket till det virtuella Azure AD DS-nätverket, om du väljer separata virtuella nätverk för dem. En virtuell dator i det virtuella HDInsight-nätverket bör ha en rad syn för Azure AD DS via peering av virtuella nätverk. Om HDInsight och Azure AD DS distribueras i samma virtuella nätverk tillhandahålls anslutningen automatiskt och ingen ytterligare åtgärd krävs.

## <a name="set-up-different-domain-controllers"></a>Konfigurera olika domänkontrollanter

HDInsight har för närvarande endast stöd för Azure AD DS som den huvudsakliga domänkontrollanten som används i klustret för Kerberos-kommunikation. Men andra komplexa Active Directory-inställningar är möjliga, så länge en sådan konfiguration leder till aktivering av Azure AD DS för HDInsight-åtkomst.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure AD DS](../../active-directory-domain-services/overview.md) tillhandahåller en hanterad domän som är helt kompatibel med Windows Server Active Directory. Microsoft tar hand om att hantera, korrigera och övervaka domänen i en installation med hög tillgänglighet (HA). Du kan distribuera klustret utan att oroa dig för att underhålla domänkontrollanter.

Användare, grupper och lösen ord synkroniseras från Azure AD. Den enkelriktade synkroniseringen från Azure AD-instansen till Azure AD DS gör det möjligt för användare att logga in i klustret med samma företags uppgifter.

Mer information finns i [Konfigurera HDInsight-kluster med ESP med hjälp av Azure AD DS](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Lokala Active Directory eller Active Directory på virtuella IaaS-datorer

Om du har en lokal Active Directory instans eller mer komplexa Active Directory-inställningar för din domän kan du synkronisera dessa identiteter med Azure AD med hjälp av Azure AD Connect. Du kan sedan aktivera Azure AD DS på den Active Directory klienten.

Eftersom Kerberos förlitar sig på lösen ords-hashar måste du [aktivera hash-synkronisering av lösen ord på Azure AD DS](../../active-directory-domain-services/tutorial-create-instance.md).

Om du använder Federation med Active Directory Federation Services (AD FS) (AD FS) måste du aktivera hash-synkronisering av lösen ord. (För en rekommenderad installation, se [den här videon](https://youtu.be/qQruArbu2Ew).) Hash-synkronisering av lösen ord hjälper till med haveri beredskap, vilket innebär att din AD FS-infrastruktur Miss lyckas, och det ger även läckt skydd. Mer information finns i [aktivera hash-synkronisering av lösen ord med Azure AD Connect Sync](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

Att använda lokala Active Directory eller Active Directory endast på virtuella IaaS-datorer, utan Azure AD och Azure AD DS, är inte en konfiguration som stöds för HDInsight-kluster med ESP.

Om Federation används och lösen ordets hash-värden synkroniseras korrekt, men du får autentiseringsfel, kontrollerar du om Cloud Password Authentication har Aktiver ATS för PowerShell-tjänstens huvud namn. Om inte, måste du ange en [HRD-princip (Home sfär Discovery)](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) för din Azure AD-klient. För att kontrol lera och ange HRD-principen:

1. Installera för hands versionen av [Azure AD PowerShell-modulen](/powershell/azure/active-directory/install-adv2).

   ```powershell
   Install-Module AzureAD
   ```

2. Anslut med autentiseringsuppgifter för global administratör (innehavaradministratör).

   ```powershell
   Connect-AzureAD
   ```

3. Kontrol lera om Microsoft Azure PowerShell tjänstens huvud namn redan har skapats.

   ```powershell
   Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. Om den inte finns skapar du tjänstens huvud namn.

   ```powershell
   $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
   ```

5. Skapa och koppla principen till tjänstens huvud namn.

   ```powershell
    # Determine whether policy exists
    Get-AzureADPolicy | Where {$_.DisplayName -eq "EnableDirectAuth"}

    # Create if not exists
    $policy = New-AzureADPolicy `
        -Definition @('{"HomeRealmDiscoveryPolicy":{"AllowCloudPasswordValidation":true}}') `
        -DisplayName "EnableDirectAuth" `
        -Type "HomeRealmDiscoveryPolicy"

    # Determine whether a policy for the service principal exist
    Get-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId

    # Add a service principal policy if not exist
    Add-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId `
        -refObjectID $policy.ID
   ```

## <a name="next-steps"></a>Nästa steg

- [Konfigurera HDInsight-kluster med ESP](apache-domain-joined-configure-using-azure-adds.md)
- [Konfigurera Apache Hive-principer för HDInsight-kluster med ESP](apache-domain-joined-run-hive.md)
- [Hantera HDInsight-kluster med ESP](apache-domain-joined-manage.md)