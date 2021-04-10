---
title: Azure VM HA arkitektur och scenarier för SAP NetWeaver | Microsoft Docs
description: Arkitektur och scenarier med hög tillgänglighet för SAP NetWeaver på Azure Virtual Machines
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d689db4916758e13311ad181fb3b5d7ca9a85260
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675327"
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Arkitektur och scenarier med hög tillgänglighet för SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-suse-ascs-ha-anf]:high-availability-guide-suse-netapp-files.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:https://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:https://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:../../windows/manage-availability.md
[azure-storage-redundancy]:https://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID-konfiguration med hög tillgänglighet)


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../manage-availability.md


## <a name="terminology-definitions"></a>Terminologi definitioner

**Hög tillgänglighet**: syftar på en uppsättning tekniker som MINIMERAr IT-avbrott genom att tillhandahålla affärs kontinuitet för IT-tjänster genom redundanta, feltoleranta eller redundanta komponenter som skyddas i *samma* Data Center. I vårt fall finns data centret i en Azure-region.

**Haveri beredskap**: avser också MINIMERING av IT-tjänsteavbrott och deras återställnings funktioner, men över *olika* Data Center som kan vara hundratals mil bort från varandra. I vårt fall kan data centren finnas i olika Azure-regioner inom samma nationella politisk region eller på platser som har skapats av dig som kund.


## <a name="overview-of-high-availability"></a>Översikt över hög tillgänglighet
SAP hög tillgänglighet i Azure kan delas upp i tre typer:

* **Hög tillgänglighet för Azure-infrastruktur**: 

    Till exempel kan hög tillgänglighet inkludera beräkning (VM), nätverk eller lagring och dess fördelar för att öka tillgängligheten för SAP-program.

* Använda en **omstart av Azure Infrastructure VM för att uppnå *högre tillgänglighet* för SAP-program**: 

    Om du väljer att inte använda funktioner som Windows Server Failover Clustering (WSFC) eller pacemaker på Linux används den virtuella Azure-datorn. Det skyddar SAP-system mot planerad och oplanerad stillestånds tid för Azures fysiska server infrastruktur och den övergripande, underliggande Azure-plattformen.

* **Hög tillgänglighet för SAP-program**: 

    För att uppnå fullständig SAP-system hög tillgänglighet måste du skydda alla viktiga SAP-systemkomponenter. Exempel:
    * Redundanta SAP-programservrar.
    * Unika komponenter. Ett exempel kan vara en enskild felpunkt (SPOF) komponent, till exempel en SAP ASCS/SCS-instans eller ett databas hanterings system (DBMS).

SAP hög tillgänglighet i Azure skiljer sig från SAP hög tillgänglighet i en lokal fysisk eller virtuell miljö. Följande papper [SAP NetWeaver hög tillgänglighet och affärs kontinuitet i virtuella miljöer med VMware och Hyper-V på Microsoft Windows][sap-ha-bc-virtual-env-hyperv-vmware-white-paper] beskriver standard-SAP-konfigurationer med hög tillgänglighet i virtualiserade miljöer i Windows.

Det finns ingen sapinst-integrerad SAP-konfiguration för hög tillgänglighet för Linux på samma sätt som för Windows. Information om SAP hög tillgänglighet lokalt för Linux finns i [partner information för hög tillgänglighet][sap-ha-partner-information].

## <a name="azure-infrastructure-high-availability"></a>Hög tillgänglighet för Azure-infrastruktur

### <a name="sla-for-single-instance-virtual-machines"></a>SLA för virtuella datorer med en instans

Det finns för närvarande ett service avtal med en virtuell dator på 99,9% med Premium Storage. För att få en uppfattning om vad tillgängligheten för en enskild virtuell dator kan vara kan du bygga produkten av de olika tillgängliga [Azure Service nivå avtalen][azure-sla].

Basen för beräkningen är 30 dagar per månad eller 43 200 minuter. En drift tid på 0,05% motsvarar till exempel 21,6 minuter. Som vanligt beräknas tillgängligheten för de olika tjänsterna på följande sätt:

(Tillgänglighets tjänst #1/100) * (tillgänglighets tjänst #2/100) * (tillgänglighets tjänst #3/100) \* ...

Exempel:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 eller en övergripande tillgänglighet på 99,75%.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Flera instanser av virtuella datorer i samma tillgänglighets uppsättning
För alla virtuella datorer som har två eller fler distribuerade instanser i samma *tillgänglighets uppsättning* garanterar vi att du kommer att ha virtuell dator anslutning till minst en instans minst 99,95% av tiden.

När två eller flera virtuella datorer ingår i samma tillgänglighets uppsättning tilldelas varje virtuell dator i tillgänglighets uppsättningen en *uppdaterings domän* och en *feldomän* av den underliggande Azure-plattformen.

* **Uppdaterings domäner** garanterar att flera virtuella datorer inte startas om samtidigt under det planerade underhållet av en Azure-infrastruktur. Endast en virtuell dator startas om i taget.

* **Fel domäner** garanterar att virtuella datorer distribueras på maskin varu komponenter som inte delar en gemensam ström källa och nätverks växel. När servrar, en nätverks växel eller en ström källa genomgår en oplanerad stillestånds tid påverkas bara en virtuell dator.

Mer information finns i [Hantera tillgängligheten för virtuella Windows-datorer i Azure][azure-virtual-machines-manage-availability].

En tillgänglighets uppsättning används för att uppnå hög tillgänglighet för:

* Redundanta SAP-programservrar.  
* Kluster med två eller flera noder (t. ex. virtuella datorer) som skyddar SPOFs, till exempel en SAP-ASCS/SCS-instans eller ett DBMS.


### <a name="azure-availability-zones"></a>Azure-tillgänglighetszoner
Azure håller på att genomföra ett koncept i [Azure-tillgänglighetszoner](../../../availability-zones/az-overview.md) i olika Azure- [regioner](https://azure.microsoft.com/global-infrastructure/regions/). I Azure-regioner där Tillgänglighetszoner erbjuds, har Azure-regionerna flera data Center som är oberoende av utbudet av ström källor, kylning och nätverk. Orsak till att erbjuda olika zoner inom en enda Azure-region är att du ska kunna distribuera program över två eller tre Tillgänglighetszoner som erbjuds. Om du antar att problem i ström källor och/eller nätverk påverkar endast en infrastruktur för tillgänglighets zoner är program distributionen i en Azure-region fortfarande fullt fungerande. Till slut, med minskad kapacitet eftersom vissa virtuella datorer i en zon kan gå förlorade. Men virtuella datorer i de andra två zonerna är fortfarande igång. Azure-regionerna som erbjuder zoner visas i [Azure-tillgänglighetszoner](../../../availability-zones/az-overview.md).

Det finns några saker att tänka på när du använder Tillgänglighetszoner. Övervägande listan, t. ex.:

- Du kan inte distribuera Azures tillgänglighets uppsättningar inom en tillgänglighets zon. Du måste välja antingen en tillgänglighets zon eller en tillgänglighets uppsättning som distributions ram för en virtuell dator.
- Du kan inte använda [Basic-Load Balancer](../../../load-balancer/load-balancer-overview.md) för att skapa lösningar för redundanskluster baserat på Windows-kluster tjänster för växling vid fel eller Linux-pacemaker. I stället måste du använda [Azure standard Load Balancer SKU: n](../../../load-balancer/load-balancer-standard-availability-zones.md)
- Azure-tillgänglighetszoner inte ger garantier för vissa avstånd mellan olika zoner inom en region
- Nätverks fördröjningen mellan olika Azure-tillgänglighetszoner i olika Azure-regioner kan skilja sig från Azure region till region. Det finns fall där du som kund kan köra SAP-program lagret distribuerat över olika zoner eftersom nätverks fördröjningen från en zon till den aktiva DBMS-datorn fortfarande är acceptabel från en affärs process. Det kommer att finnas kund scenarier där svars tiden mellan den aktiva DBMS-datorn i en zon och en SAP-programinstans i en virtuell dator i en annan zon kan vara för påträngande och inte accepteras för SAP-affärsprocesserna. Därför måste distributions arkitekturerna vara olika med en aktiv/aktiv-arkitektur för programmet eller aktiv/passiv arkitektur om svars tiden är för hög.
- Att använda [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) är obligatoriskt för distribution till Azure-tillgänglighetszoner 


### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>Planerat och oplanerat underhåll av virtuella datorer

Två typer av Azure Platform-händelser kan påverka tillgängligheten för dina virtuella datorer:

* **Planerade underhålls** händelser är periodiska uppdateringar som görs av Microsoft till den underliggande Azure-plattformen. Uppdateringarna förbättrar den övergripande tillförlitligheten, prestandan och säkerheten för den plattforms infrastruktur som dina virtuella datorer körs på.

* **Oplanerade underhålls** händelser inträffar när den maskin vara eller den fysiska infrastruktur som underliggande den virtuella datorn har misslyckats på något sätt. Det kan omfatta lokala nätverks haverier, lokala diskfel eller andra rack nivå problem. När ett sådant fel upptäcks, migrerar Azure-plattformen automatiskt den virtuella datorn från den felaktiga fysiska server som är värd för den virtuella datorn till en felfri fysisk server. Sådana händelser är ovanliga, men de kan också göra att den virtuella datorn startas om.

Mer information finns i [Hantera tillgängligheten för virtuella Windows-datorer i Azure][azure-virtual-machines-manage-availability].

### <a name="azure-storage-redundancy"></a>Redundans i Azure Storage
Data i ditt lagrings konto replikeras alltid för att säkerställa hållbarhet och hög tillgänglighet, vilket uppfyller Azure Storage Service nivå avtal även i de flesta tillfälliga maskin varu haverier.

Eftersom Azure Storage behåller tre avbildningar av data som standard är användningen av RAID 5 eller RAID 1 på flera Azure-diskar onödig.

Mer information finns i [Azure Storage replikering][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Azure Managed Disks
Managed Disks är en resurs typ i Azure Resource Manager som rekommenderas att användas i stället för virtuella hård diskar (VHD) som lagras i Azure Storage-konton. Hanterade diskar justeras automatiskt med en Azures tillgänglighets uppsättning för den virtuella dator som de är kopplade till. De ökar tillgängligheten för den virtuella datorn och de tjänster som körs på den.

Mer information finns i  [Översikt över Azure Managed disks][azure-storage-managed-disks-overview].

Vi rekommenderar att du använder Managed disks eftersom de fören klar distributionen och hanteringen av dina virtuella datorer.



## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>Använda Azure-infrastruktur med hög tillgänglighet för att uppnå *högre tillgänglighet* för SAP-program

Om du väljer att inte använda funktioner som WSFC eller pacemaker på Linux (stöds för närvarande endast för SUSE Linux Enterprise Server [SLES] 12 och senare), så används den virtuella Azure-datorn. Det skyddar SAP-system mot planerad och oplanerad stillestånds tid för Azures fysiska server infrastruktur och den övergripande, underliggande Azure-plattformen.

Mer information om den här metoden finns i [använda Azure Infrastructure VM restart för att uppnå högre tillgänglighet för SAP-systemet][sap-higher-availability].

## <a name="high-availability-of-sap-applications-on-azure-iaas"></a><a name="baed0eb3-c662-4405-b114-24c10a62954e"></a> Hög tillgänglighet för SAP-program i Azure IaaS

För att uppnå fullständig SAP-system hög tillgänglighet måste du skydda alla viktiga SAP-systemkomponenter. Exempel:
  * Redundanta SAP-programservrar.
  * Unika komponenter. Ett exempel kan vara en enskild felpunkt (SPOF) komponent, till exempel en SAP ASCS/SCS-instans eller ett databas hanterings system (DBMS).

I nästa avsnitt beskrivs hur du uppnår hög tillgänglighet för alla tre viktiga SAP-system komponenter.

### <a name="high-availability-architecture-for-sap-application-servers"></a>Hög tillgänglighets arkitektur för SAP-program servrar

> Det här avsnittet gäller för:
>
> ![Windows-logotyp.][Logo_Windows] Windows och ![Linux-logotyp.][Logo_Linux] Linux
>

Du behöver vanligt vis ingen specifik lösning för hög tillgänglighet för SAP-programservern och dialog instanser. Du uppnår hög tillgänglighet genom redundans och du konfigurerar flera dialog instanser i olika instanser av virtuella Azure-datorer. Du bör ha minst två SAP-programinstanser installerade i två instanser av Azure Virtual Machines.

![Bild 1: SAP-Programserver med hög tillgänglighet][sap-ha-guide-figure-2000]

_**Bild 1:** SAP-Programserver med hög tillgänglighet_

Du måste placera alla virtuella datorer som är värdar för SAP Application Server-instanser i samma Azure-tillgänglighets uppsättning. En Azure-tillgänglighets uppsättning garanterar att:

* Alla virtuella datorer ingår inte i samma uppdaterings domän.  
    En uppdaterings domän säkerställer att de virtuella datorerna inte uppdateras samtidigt under drift stopp av planerade underhåll.

    De grundläggande funktionerna, som bygger på olika uppdaterings-och fel domäner i en Azure-Scale-enhet, har redan introducerats i avsnittet [uppdaterings domäner][planning-guide-3.2.2] .

* Alla virtuella datorer ingår inte i samma feldomän.  
    En feldomän säkerställer att virtuella datorer distribueras så att ingen enskild felpunkt påverkar tillgängligheten för alla virtuella datorer.

Antalet uppdaterings-och fel domäner som kan användas av en Azure-tillgänglighets uppsättning i en Azure-Scale-enhet är begränsad. Om du fortsätter att lägga till virtuella datorer i en enda tillgänglighets uppsättning, kommer två eller fler virtuella datorer att sluta i samma fel-eller uppdaterings domän.

Om du distribuerar några SAP Application Server-instanser i sina dedikerade virtuella datorer, förutsatt att vi har fem uppdaterings domäner visas följande bild. Det faktiska maximala antalet uppdaterings-och fel domäner i en tillgänglighets uppsättning kan ändras i framtiden:

![Bild 2: hög tillgänglighet för SAP-programservrar i en Azures tillgänglighets uppsättning ][planning-guide-figure-3000]
 _**bild 2:** hög tillgänglighet för SAP-programservrar i en Azures tillgänglighets uppsättning_

Mer information finns i [Hantera tillgängligheten för virtuella Windows-datorer i Azure][azure-virtual-machines-manage-availability].

Mer information finns i avsnittet [tillgänglighets uppsättningar][planning-guide-3.2.3] för Azure i Azure Virtual Machines planering och implementering för SAP NetWeaver-dokument.

**Endast ohanterade diskar:** Eftersom Azure Storage-kontot är en enskild felpunkt är det viktigt att ha minst två Azure Storage-konton, där minst två virtuella datorer distribueras. I en idealisk installation skulle diskarna för varje virtuell dator som kör en instans av en SAP-dialogruta distribueras i ett annat lagrings konto.

> [!IMPORTANT]
> Vi rekommenderar starkt att du använder Azure Managed disks för dina SAP-installationer med hög tillgänglighet. Eftersom Managed disks automatiskt anpassas med tillgänglighets uppsättningen för den virtuella datorn som de är kopplade till, ökar tillgängligheten för den virtuella datorn och de tjänster som körs på den.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Hög tillgänglighets arkitektur för en SAP ASCS/SCS-instans i Windows

> ![Windows-logotyp.][Logo_Windows] Windows
>

Du kan använda en WSFC-lösning för att skydda SAP ASCS/SCS-instansen. Lösningen har två varianter:

* **Klustra SAP ASCS/SCS-instansen med hjälp av klustrade delade diskar**: Mer information om den här arkitekturen finns i [kluster en SAP ASCS/SCS-instans på ett Windows-redundanskluster med hjälp av en klusterdelad disk][sap-high-availability-guide-wsfc-shared-disk].   

* **Klustra SAP ASCS/SCS-instansen med hjälp av fil resurs**: Mer information om den här arkitekturen finns i [kluster en SAP ASCS/SCS-instans i ett Windows-redundanskluster med hjälp av fil resurs][sap-high-availability-guide-wsfc-file-share].

* **Klustra SAP ASCS/SCS-instansen med hjälp av ANF SMB-resurs**: Mer information om den här arkitekturen finns i kluster [kluster en SAP ASCS/SCS-instans i ett Windows-redundanskluster med hjälp av ANF SMB-filresurs](./high-availability-guide-windows-netapp-files-smb.md).

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Hög tillgänglighets arkitektur för en SAP ASCS/SCS-instans på Linux

> ![Linux-logotyp.][Logo_Linux] Linux
> 
> Mer information om hur du klustrar SAP ASCS/SCS-instansen med hjälp av SLES Cluster Framework finns i [hög tillgänglighet för SAP-NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program][sap-suse-ascs-ha]. För alternativ HA-arkitektur på SLES, som inte kräver hög tillgänglig NFS, se [hög tillgänglighets guide för SAP NetWeaver på SUSE Linux Enterprise Server med Azure NetApp Files för SAP-program][sap-suse-ascs-ha-anf].

Mer information om hur du klustrar SAP ASCS/SCS-instansen med hjälp av Red Hat Cluster Framework finns i [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux](./high-availability-guide-rhel.md)


### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>SAP NetWeaver multi-SID-konfiguration för en klustrad SAP-ASCS/SCS-instans

> ![Windows-logotyp.][Logo_Windows] Windows
> 
> Multi-SID stöds med WSFC, med fil resurs och delad disk.
> 
> Mer information om arkitektur med hög tillgänglighet för flera SID i Windows finns i:

* [SAP ASCS/SCS-instans multi-SID hög tillgänglighet för kluster för växling vid fel i Windows Server och fil resurs][sap-ascs-ha-multi-sid-wsfc-file-share]

* [SAP ASCS/SCS-instans multi-SID hög tillgänglighet för Windows Server-redundanskluster och delad disk][sap-ascs-ha-multi-sid-wsfc-shared-disk]

> ![Linux-logotyp.][Logo_Linux] Linux
> 
> Multi-SID-klustring stöds på Linux pacemaker-kluster för SAP ASCS/ERS, begränsat till **fem** SAP-sid i samma kluster.
> Mer information om arkitektur med hög tillgänglighet för flera SID i Linux finns i:

* [HA för SAP NW på virtuella Azure-datorer på SLES för SAP-program med flera SID-guide](./high-availability-guide-suse-multi-sid.md)
* [HA för SAP NW på virtuella Azure-datorer på RHEL för SAP-program med flera SID-guide](./high-availability-guide-rhel-multi-sid.md)

### <a name="high-availability-dbms-instance"></a>DBMS-instans med hög tillgänglighet

DBMS är också en enda kontakt punkt i ett SAP-system. Du måste skydda den med en lösning med hög tillgänglighet. Följande bild visar en SQL Server AlwaysOn-lösning med hög tillgänglighet i Azure med Windows Server-redundanskluster och den interna Azure-belastningsutjämnaren. SQL Server AlwaysOn replikerar DBMS-data och loggfiler med hjälp av sin egen DBMS-replikering. I det här fallet behöver du inte klusterdelad disk, vilket fören klar hela installationen.

![Bild 3: exempel på en SAP-DBMS med hög tillgänglighet, med SQL Server AlwaysOn][sap-ha-guide-figure-2003]

_**Bild 3:** Exempel på en SAP-DBMS med hög tillgänglighet, med SQL Server AlwaysOn_

Mer information om klustring SQL Server DBMS i Azure med hjälp av Azure Resource Manager distributions modell finns i följande artiklar:

* [Konfigurera en AlwaysOn-tillgänglighetsgrupper på virtuella Azure-datorer manuellt med hjälp av Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Konfigurera en intern Azure-belastningsutjämnare för en AlwaysOn-tillgänglighetsgruppen i Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

Mer information om klustring SAP HANA DBMS i Azure med hjälp av Azure Resource Manager distributions modell finns i [hög tillgänglighet för SAP HANA på virtuella datorer i Azure][sap-hana-ha].
