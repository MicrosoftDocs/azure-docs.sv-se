---
title: Synkronisera Azure Active Directory användare till HDInsight-kluster
description: Synkronisera autentiserade användare från Azure Active Directory till ett HDInsight-kluster.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: a3307c5d677028c17717c0d591790f0373f131d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931623"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Synkronisera Azure Active Directory-användare med ett HDInsight-kluster

[HDInsight-kluster med Enterprise Security Package (ESP)](./domain-joined/hdinsight-security-overview.md) kan använda stark autentisering med Azure Active Directory-användare (Azure AD), samt använda *Azure RBAC-principer (rollbaserad åtkomst kontroll)* . När du lägger till användare och grupper i Azure AD kan du synkronisera de användare som behöver åtkomst till klustret.

## <a name="prerequisites"></a>Förutsättningar

Om du inte redan har gjort det [skapar du ett HDInsight-kluster med Enterprise Security Package](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

## <a name="add-new-azure-ad-users"></a>Lägg till nya Azure AD-användare

Om du vill visa dina värdar öppnar du Ambari-webbgränssnittet. Varje nod kommer att uppdateras med nya obevakade uppgraderings inställningar.

1. Från [Azure Portal](https://portal.azure.com)navigerar du till den Azure AD-katalog som är kopplad till ditt ESP-kluster.

2. Välj **alla användare** på menyn till vänster och välj sedan **ny användare**.

    ![Azure Portal användare och grupper alla](./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png)

3. Slutför det nya användar formuläret. Välj grupper som du har skapat för att tilldela kluster baserade behörigheter. I det här exemplet skapar du en grupp med namnet "HiveUsers" som du kan tilldela nya användare till. I [exempel anvisningarna](./domain-joined/apache-domain-joined-configure-using-azure-adds.md) för att skapa ett ESP-kluster ingår att lägga till två grupper `HiveUsers` och `AAD DC Administrators` .

    ![Välj grupper i fönstret Azure Portal användare](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png)

4. Välj **Skapa**.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Använd Apache Ambari-REST API för att synkronisera användare

Användargrupper som angetts när klustret skapades synkroniseras vid den tidpunkten. Användarsynkronisering sker automatiskt en gång i timmen. Om du vill synkronisera användarna direkt, eller om du vill synkronisera en annan grupp än de grupper som anges när klustret skapas, använder du Ambari-REST API.

Följande metod använder POST med Ambari-REST API. Mer information finns i [Hantera HDInsight-kluster med hjälp av Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

1. Använd [SSH-kommandot](hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till klustret. Redigera kommandot nedan genom att ersätta `CLUSTERNAME` med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Efter autentisering anger du följande kommando:

    ```bash
    curl -u admin:PASSWORD -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events"
    ```

    Svaret bör se ut så här:

    ```json
    {
      "resources" : [
        {
          "href" : "http://<ACTIVE-HEADNODE-NAME>.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

1. Om du vill se synkroniseringsstatus kör du ett nytt `curl` kommando:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```

    Svaret bör se ut så här:

    ```json
    {
      "href" : "http://<ACTIVE-HEADNODE-NAME>.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
      "Event" : {
        "id" : 1,
        "specs" : [
          {
            "sync_type" : "existing",
            "principal_type" : "groups"
          }
        ],
        "status" : "COMPLETE",
        "status_detail" : "Completed LDAP sync.",
        "summary" : {
          "groups" : {
            "created" : 0,
            "removed" : 0,
            "updated" : 0
          },
          "memberships" : {
            "created" : 1,
            "removed" : 0
          },
          "users" : {
            "created" : 1,
            "removed" : 0,
            "skipped" : 0,
            "updated" : 0
          }
        },
        "sync_time" : {
          "end" : 1497994072182,
          "start" : 1497994071100
        }
      }
    }
    ```

1. Det här resultatet visar att statusen är **slutförd**, att en ny användare skapades och att användaren tilldelades ett medlemskap. I det här exemplet tilldelas användaren till den synkroniserade LDAP-gruppen "HiveUsers", eftersom användaren lades till i samma grupp i Azure AD.

    > [!NOTE]  
    > Den föregående metoden synkroniserar bara de Azure AD-grupper som anges i egenskapen **åtkomst användar grupp** för domän inställningarna när klustret skapas. Mer information finns i  [skapa ett HDInsight-kluster](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Verifiera den nyligen tillagda Azure AD-användaren

Öppna [Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) för att kontrol lera att den nya Azure AD-användaren har lagts till. Öppna Ambari-webbgränssnittet genom att bläddra till **`https://CLUSTERNAME.azurehdinsight.net`** . Ange användar namn och lösen ord för kluster administratören.

1. Från Ambari-instrumentpanelen väljer du **Hantera Ambari** under **Administratörs** menyn.

    ![Apache Ambari-instrumentpanel hantera Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png)

2. Välj **användare** under meny gruppen användar-och **grupp hantering** till vänster på sidan.

    ![HDInsight-användare och grupper-menyn](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png)

3. Den nya användaren ska visas i tabellen användare. Typen anges till `LDAP` i stället för  `Local` .

    ![Översikt över HDInsight AAD-användare](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Logga in på Ambari som den nya användaren

När den nya användaren (eller någon annan domän användare) loggar in på Ambari, använder de sina fullständiga användar namn och domänautentiseringsuppgifter för Azure AD.  Ambari visar ett användaralias som är visnings namnet för användaren i Azure AD.
Det nya exempel användaren har användar namnet `hiveuser3@contoso.com` . I Ambari visas den här nya användaren som, `hiveuser3` men användaren loggar in på Ambari som `hiveuser3@contoso.com` .

## <a name="see-also"></a>Se även

* [Konfigurera Apache Hive principer i HDInsight med ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [Hantera HDInsight-kluster med ESP](./domain-joined/apache-domain-joined-manage.md)
* [Auktorisera användare till Apache Ambari](hdinsight-authorize-users-to-ambari.md)