---
title: SQL Server Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning | Microsoft Docs
description: DBMS-distribution för SAP-arbetsbelastning för SQL Server på Azure Virtual Machines
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e73bc3791ceb75685275af99f888136315c6e50d
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505568"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>SQL Server Azure Virtual Machines DBMS-distribution för SAP NetWeaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide_general.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms_guide_sqlserver.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md 
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab 
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e 
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc 
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d 
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f 
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca 
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b 
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam 
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/eresources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md




Det här dokumentet omfattar flera olika områden att tänka på när du distribuerar SQL Server för SAP-arbetsbelastningar i Azure IaaS. Som ett villkor för det här dokumentet bör du läsa dokument [överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar](dbms_guide_general.md) samt andra guider i [SAP-arbetsbelastningen i Azure-dokumentationen](./get-started.md). 



> [!IMPORTANT]
> Omfånget för det här dokumentet är Windows-versionen på SQL Server. SAP stöder inte Linux-versionen av SQL Server med någon av SAP-program varan. Dokumentet diskuterar inte Microsoft Azure SQL Database, som är en plattform som ett tjänst erbjudande för Microsoft Azures plattformen. Diskussionen i det här dokumentet är på väg att köra SQL Server-produkten eftersom den är känd för lokala distributioner i Azure Virtual Machines, vilket utnyttjar infrastrukturen som en tjänst funktion i Azure. Databas funktionerna och funktionerna mellan dessa två erbjudanden är olika och ska inte blandas med varandra. Se även: <https://azure.microsoft.com/services/sql-database/>
> 
>

I allmänhet bör du överväga att använda de senaste SQL Server versionerna för att köra SAP-arbetsbelastningar i Azure IaaS. De senaste SQL Server-versionerna erbjuder bättre integrering i några av Azure-tjänsterna och-funktionerna. Eller har ändringar som optimerar åtgärder i en Azure IaaS-infrastruktur.

Vi rekommenderar att du läser [den här][virtual-machines-sql-server-infrastructure-services] dokumentationen innan du fortsätter.

I följande avsnitt samlas delar av delar av dokumentationen under länken ovan samman och anges. Information kring SAP nämns också och vissa begrepp beskrivs i detalj. Vi rekommenderar dock starkt att du går igenom dokumentationen ovan innan du läser den SQL Server-/regionsspecifika dokumentationen.

Det finns vissa SQL Server i IaaS information som du bör känna till innan du fortsätter:

* **Stöd för SQL-versioner**: för SAP-kunder stöds SQL Server 2008 R2 och högre på Microsoft Azure virtuella datorn. Tidigare versioner stöds inte. Läs den här allmänna [support policyn](https://support.microsoft.com/kb/956893) för mer information. I allmänhet stöds SQL Server 2008 av Microsoft också. Men på grund av betydande funktioner för SAP, som introducerades med SQL Server 2008 R2, är SQL Server 2008 R2 den lägsta versionen för SAP. I allmänhet bör du överväga att använda de senaste SQL Server versionerna för att köra SAP-arbetsbelastningar i Azure IaaS. De senaste SQL Server-versionerna erbjuder bättre integrering i några av Azure-tjänsterna och-funktionerna. Eller har ändringar som optimerar åtgärder i en Azure IaaS-infrastruktur. Därför är papperet begränsat till SQL Server 2016 och SQL Server 2017.
* **SQL-prestanda**: Microsoft Azure värdbaserade Virtual Machines fungerar bra jämfört med andra offentliga moln erbjudanden, men enskilda resultat kan variera. Se [metod tipsen för artikeln prestanda för SQL Server i Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md).
* **Använda avbildningar från Azure Marketplace**: Det snabbaste sättet att distribuera en ny Microsoft Azure virtuell dator är att använda en avbildning från Azure Marketplace. Det finns avbildningar på Azure Marketplace, som innehåller de senaste SQL Server versionerna. Avbildningarna där SQL Server redan har installerats kan inte användas direkt för SAP NetWeaver-program. Orsaken är att standard SQL Servers sorteringen installeras i dessa avbildningar och inte sorteringen som krävs av SAP NetWeaver-system. För att kunna använda sådana bilder, se stegen som beskrivs i kapitlet [med en SQL Server avbildning från Microsoft Azure Marketplace][dbms-guide-5.6]. 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Rekommendationer för VM/VHD-struktur för SAP-relaterade SQL Server distributioner
I enlighet med den allmänna beskrivningen ska SQL Server körbara filer finnas eller installeras i system enheten för den virtuella datorns OS-disk (enhet C: \) .  Normalt används de flesta SQL Server System databaserna inte på en hög nivå av SAP NetWeaver-arbetsbelastningar. Därför kan system databaserna för SQL Server (Master, msdb och Model) finnas kvar på C:\ enheten också. Ett undantag bör vara tempdb, som i fallet med SAP-arbetsbelastningar, kan kräva antingen högre data volym eller I/O-åtgärdernas volym. I/O-arbetsbelastning, som inte ska användas för OS-VHD: n. Följande steg bör utföras för sådana system:


* Med alla SAP-certifierade VM-typer (se SAP anmärkning [1928533]), förutom A-seriens virtuella datorer, tempdb-data och loggfiler kan placeras på den icke-sparade D:\ kombinationsenhet. 
* Du rekommenderas dock att använda flera tempdb-datafiler. Var medveten om D:\ enhets volymer är olika beroende på den virtuella dator typen. För exakta storlekar på D:\ enhet för de olika virtuella datorerna kontrollerar du artikel [storlekarna för virtuella Windows-datorer i Azure](../../sizes.md).

Dessa konfigurationer gör det möjligt för tempdb att förbruka mer utrymme än system enheten kan tillhandahålla. Icke-permanent D:\ enheten erbjuder också bättre I/O-latens och data flöde (med undantag för virtuella datorer i A-serien). För att fastställa rätt tempdb-storlek kan du kontrol lera tempdb-storlekarna på befintliga system. 

>[!NOTE]
> Om du placerar tempdb-datafilerna och logg filen i en mapp på D:\ enhet som du har skapat måste du kontrol lera att mappen finns efter en omstart av datorn. Eftersom D:\ enheten initieras nyligen efter att en virtuell dator har startats om alla fil-och katalog strukturer rensas bort. En möjlighet att återskapa eventuella katalog strukturer på D:\ enheten innan SQL Servers tjänstens början dokumenteras i [den här artikeln](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).

En VM-konfiguration, som kör SQL Server med en SAP-databas och där tempdb-data och tempdb-loggfiler placeras på D:\ enheten skulle se ut så här:

![Diagram över enkel disk konfiguration för virtuell dator för SQL Server](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

Diagrammet ovan visar ett enkelt ärende. Som eluded i artikeln [överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](dbms_guide_general.md), antal och storlek för Premium Storage diskar är beroende av olika faktorer. Men i allmänhet rekommenderar vi att:

- Använda lagrings utrymmen för att skapa ett eller ett litet antal volymer, som innehåller SQL Server-datafilerna. Anledningen till att den här konfigurationen är att i real tid finns det flera SAP-databaser med olika storleks databas filer med olika I/O-arbetsbelastningar.
- Använd lagrings utrymmen för att tillhandahålla tillräckligt med IOPS och för SQL Server transaktions logg filen. Potentiell IOPS-belastning är ofta den GUID-rad som används för storleks ändring av transaktions logg volymen och inte den potentiella volymen hos SQL Server transaktions volymen
- Använd D:\drive för tempdb så länge prestanda är tillräckligt hög. Om den övergripande arbets belastningen är begränsad i prestanda genom tmepdb som finns på D:\ enhet du kan behöva överväga att flytta tempdb till separata Premium Storage diskar enligt rekommendationerna i [den här artikeln](../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md).


### <a name="special-for-m-series-vms"></a>Special för virtuella datorer i M-serien
För virtuella datorer i Azure M-serien kan svars tiden som skrivs till transaktions loggen minskas av faktorer, jämfört med Azure Premium Storage prestanda, när Azure Skrivningsaccelerator används. Därför bör du Distribuera Azure-Skrivningsaccelerator för de virtuella hård diskarna som utgör volymen för SQL Server transaktions loggen. Information kan läsas i dokumentet [Skrivningsaccelerator](../../how-to-enable-write-accelerator.md).
  

### <a name="formatting-the-disks"></a>Formatera diskarna
För SQL Server ska NTFS-block storleken för diskar som innehåller SQL Server data och loggfiler vara 64 kB. Du behöver inte formatera D:\ kombinationsenhet. Den här enheten levereras i förväg.

För att se till att det inte går att initiera datafilerna genom att nollställa innehållet i filerna bör du se till att den användar kontext som SQL Server tjänsten körs i har en viss behörighet. Användare i gruppen Windows-administratörer har vanligt vis dessa behörigheter. Om SQL Server-tjänsten körs i användar kontexten för en användare som inte är Windows-administratör, måste du tilldela användaren användar rättigheten att **utföra volym underhålls aktiviteter**.  Se informationen i den här artikeln i Microsoft Knowledge Base: <https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>Påverkan av databas komprimering
I konfigurationer där I/O-bandbredden kan bli en begränsnings faktor kan varje mått minska IOPS för att öka den arbets belastning som kan köras i ett IaaS-scenario som Azure. Om du ännu inte har utfört det rekommenderar vi att du använder SQL Server sid komprimering av både SAP och Microsoft innan du laddar upp en befintlig SAP-databas till Azure.

Rekommendationen att utföra databas komprimering innan överföring till Azure görs av två orsaker:

* Mängden data som ska överföras är lägre.
* Komprimerings Körningens varaktighet är kortare än att en kan använda starkare maskin vara med fler processorer eller högre I/O-bandbredd eller mindre I/O-latens lokalt.
* Mindre storlek på databaser kan leda till lägre kostnader för diskallokering

Databas komprimering fungerar också i Azure Virtual Machines som lokalt. Mer information om hur du komprimerar befintliga SAP-NetWeaver SQL Server-databaser finns i artikeln [förbättrade SAP Compression-verktyg MSSCOMPRESS](/archive/blogs/saponsqlserver/improved-sap-compression-tool-msscompress). 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 och nyare databasfiler som lagras direkt på Azure Blob Storage
SQL Server 2014 och senare versioner öppnar du möjligheten att lagra databasfiler direkt i Azure Blob Store utan "wrapper" för en virtuell hård disk runt dem. I synnerhet med att använda standard Azure Storage eller mindre VM-typer, kan du med den här typen av distribution aktivera scenarier där du kan undvika de begränsningar i IOPS som skulle verkställas av ett begränsat antal diskar som kan monteras till vissa mindre VM-typer. Det här sättet att distribuera fungerar för användar databaser, men inte för system databaser i SQL Server. Den fungerar även för data-och loggfiler för SQL Server. Om du vill distribuera en SAP SQL Server-databas på det här sättet i stället för att "figursätta" den i VHD: er, Tänk på följande:

* Det lagrings konto som används måste finnas i samma Azure-region som det som används för att distribuera den virtuella dator SQL Server körs i.
* Överväganden tidigare om distribution av virtuella hård diskar över olika Azure Storage-konton gäller även för den här distributions metoden. Innebär att I/O-åtgärderna räknas mot gränserna för det Azure Storage kontot.
* I stället för att räkna med den virtuella datorns lagrings-I/O-kvot kommer trafiken mot lagrings blobbar som representerar SQL Server data och loggfiler att redovisas i den virtuella datorns nätverks bandbredd för den angivna VM-typen. För nätverks-och lagrings bandbredd för en viss VM-typ, se artikel [storlekarna för virtuella Windows-datorer i Azure](../../sizes.md).
* Som ett resultat av att du skickar fil-I/O via nätverks kvoten får du en utökning av lagrings kvoten för det mesta och med att bara använda den totala bandbredden för den virtuella datorn delvis.
* Prestanda målen IOPS och I/O som Azure Premium Storage har för de olika disk storlekarna gäller inte längre. Även om blobbar som du har skapat finns på Azure Premium Storage. Målen är dokumenterade i artikeln högpresterande [Premium Storage och hanterade diskar för virtuella datorer](../../windows/disks-types.md#premium-ssd). Som ett resultat av att placera SQL Server datafiler och loggfiler direkt på blobbar som lagras på Azure Premium Storage, kan prestanda egenskaperna vara olika jämfört med virtuella hård diskar på Azure-Premium Storage.
* Värdbaserade cachelagring som är tillgängligt för Azure Premium Storage-diskar är inte tillgängligt när du placerar SQL Server-datafiler direkt på Azure-blobar.
* På virtuella datorer i M-serien kan Azure Skrivningsaccelerator inte användas för att stödja under millisekunder-skrivningar mot logg filen för SQL Servers transaktioner. 

Information om den här funktionen finns i artikeln [SQL Server datafiler i Microsoft Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-2017)

Rekommendationen för produktions system är att undvika den här konfigurationen och i stället välja placeringar av SQL Server data och loggfiler i Azure Premium Storage VHD: er i stället för direkt på Azure-blobar.


## <a name="sql-server-2014-buffer-pool-extension"></a>SQL Server 2014-buffertpooltillägget
SQL Server 2014 introducerade en ny funktion som kallas [buffertpooltillägget](/sql/database-engine/configure-windows/buffer-pool-extension?view=sql-server-2017). Den här funktionen utökar bufferten för SQL Server, som lagras i minnet med en sekundär cache som backas upp av lokala SSD på en server eller virtuell dator. Buffertpooltillägget ger en större arbets mängd med data i minnet. Jämfört med åtkomst till Azure standard Storage är åtkomsten till tillägget för bufferten, som lagras på lokala SSD av en virtuell Azure-dator, många faktorer snabbare. Om du jämför buffertpooltillägget för Azure Premium Storage Read cache, som rekommenderas för SQL Server datafiler, förväntas inga betydande fördelar för buffertpooltillägget. Orsak är att både cacheminnen (SQL Server buffertpooltillägget och Premium Storage Read cache) använder de lokala diskarna i Azure Compute-noden.

Erfarenheter som erhålls under tiden med SQL Server buffertpooltillägget med SAP-arbetsbelastning är blandade och tillåter fortfarande inte tydliga rekommendationer för huruvida de ska användas i samtliga fall. Det idealiska fallet är att den arbets uppsättning som SAP-programmet kräver passar i huvud minnet. Med Azure samtidigt som de virtuella datorer som levereras med upp till 4 TB minne, bör det vara möjligt att ha den aktiva arbets minnet. Därför är användningen av buffertpooltillägget begränsad till vissa sällsynta fall och bör inte vara en vanlig väska.  

## <a name="backuprecovery-considerations-for-sql-server"></a>Att tänka på vid säkerhets kopiering/återställning för SQL Server
När du distribuerar SQL Server till Azure måste du granska din säkerhets kopierings metod. Även om systemet inte är ett produktions system måste SAP-databasen som är värd för SQL Server säkerhets kopie ras regelbundet. Eftersom Azure Storage behåller tre avbildningar är en säkerhets kopia nu mindre viktig när det gäller att kompensera en lagrings krasch. Prioritets orsaken för att underhålla en lämplig säkerhets kopierings-och återställnings plan är mer att du kan kompensera för logiska/manuella fel genom att tillhandahålla återställnings funktioner för tidpunkt. Målet är att antingen använda säkerhets kopieringar för att återställa databasen till en viss tidpunkt eller använda säkerhets kopieringarna i Azure för att dirigera ett annat system genom att kopiera den befintliga databasen. 

För att kunna titta på olika SQL Server säkerhets kopierings möjligheter i Azure läser du artikeln [säkerhets kopiering och återställning av SQL Server i azure Virtual Machines](../../../azure-sql/virtual-machines/windows/backup-restore.md). Artikeln täcker flera olika möjligheter.

### <a name="manual-backups"></a>Manuella säkerhets kopior
Det finns flera möjligheter att utföra manuella säkerhets kopieringar genom att:

1. Köra vanliga SQL Server säkerhets kopieringar på direktanslutna Azure-diskar. Den här metoden har fördelen att du har säkerhets kopiorna tillgängliga snabbt för att uppdatera systemet och bygga upp nya system som kopior av befintliga SAP-system
2.  SQL Server 2012 CU4 och högre kan säkerhetskopiera databaser till en Azure Storage-URL.
3.  Säkerhets kopior av fil-och ögonblicks bilder för databasfiler i Azure Blob Storage. Den här metoden fungerar bara när SQL Server data och loggfiler finns i Azure Blob Storage

Den första metoden är känd och används i många fall även i den lokala världen. Dock lämnar den aktiviteten för att lösa säkerhets kopierings platsen för längre tid. Eftersom du inte vill behålla säkerhets kopiorna i 30 eller fler dagar i den lokalt anslutna Azure Storage måste du antingen använda Azure Backup tjänster eller ett annat säkerhets kopierings-eller återställnings verktyg från tredje part som innehåller åtkomst och bevarande hantering för dina säkerhets kopior. Eller så skapar du en stor fil server i Azure med hjälp av Windows lagrings utrymmen.

Den andra metoden beskrivs närmare i artikeln [SQL Server säkerhets kopiering till URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017). Olika versioner av SQL Server har vissa variationer i den här funktionen. Därför bör du ta en titt på dokumentationen för din specifika SQL Server versions kontroll. Viktigt att Observera att den här artikeln innehåller en mängd olika begränsningar. Du har antingen möjlighet att utföra säkerhets kopieringen mot:

- En enda Azure Page-BLOB, som sedan begränsar säkerhets kopians storlek till 1000 GB. Detta begränsar också det data flöde som du kan uppnå.
- Flera (upp till 64) Azure block-blobar som möjliggör en teoretisk säkerhets kopierings storlek på 12 TB. Tester med kund databaser visar dock att maximal säkerhets kopierings storlek kan vara mindre än den teoretiska gränsen. I det här fallet ansvarar du för att hantera kvarhållning av säkerhets kopior och även komma åt o säkerhets kopieringarna.


### <a name="automated-backup-for-sql-server"></a>Automatiserad säkerhetskopiering för SQL Server
Automatisk säkerhets kopiering tillhandahåller en automatisk säkerhets kopierings tjänst för SQL Server Standard-och Enterprise-versioner som körs i en virtuell Windows-dator i Azure. Den här tjänsten tillhandahålls av [SQL Server IaaS agent Extension](../../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md), som installeras automatiskt på SQL Server avbildningar av virtuella Windows-datorer i Azure Portal. Om du distribuerar egna OS-avbildningar med SQL Server installerade måste du installera VM-tilläggen separat. De steg som krävs beskrivs i den här [artikeln](../../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md).

Mer information om funktionerna i den här metoden finns i följande artiklar:

- SQL Server 2014: [Automatisk säkerhets kopiering för SQL Server 2014 Virtual Machines (Resource Manager)](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md)
- SQL Server 2016/2017: [Automatisk säkerhets kopiering v2 för Azure Virtual Machines (Resource Manager)](../../../azure-sql/virtual-machines/windows/automated-backup.md)

I dokumentationen kan du se att funktionerna med de nyare SQL Server-versionerna har förbättrats. Mer information om SQL Server automatiserade säkerhets kopieringar publiceras i artikeln [SQL Server hanterad säkerhets kopiering till Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017). Den teoretiska storleks gränsen för säkerhets kopiering är 12 TB.  De automatiserade säkerhets kopieringarna kan vara en lämplig metod för säkerhets kopierings storlekar på upp till 12 TB. Eftersom flera blobbar skrivs till parallellt kan du vänta på ett data flöde som är större än 100 MB/SEK. 
 

### <a name="azure-backup-for-sql-server-vms"></a>Azure Backup för SQL Server virtuella datorer
Den här nya metoden för SQL Server säkerhets kopior erbjuds från och med juni 2018 som offentlig för hands version av Azure Backup Services. Metoden för att säkerhetskopiera SQL Server är samma som andra verktyg från tredje part som använder, nämligen att SQL Server VSS/VDI-gränssnittet för att strömma säkerhets kopior till en målplats. I det här fallet är mål platsen Azure Recovery Service-valvet.

En mer än detaljerad beskrivning av den här säkerhets kopierings metoden, som lägger till flera fördelar med centrala konfigurationer för säkerhets kopiering, övervakning och administration finns [här](../../../backup/backup-azure-sql-database.md). 


### <a name="third-party-backup-solutions"></a>Säkerhets kopierings lösningar från tredje part
För ett stort antal SAP-kunder fanns det ingen möjlighet att börja om och införa fullständiga nya säkerhets kopierings lösningar för den del av deras SAP-landskap som kördes på Azure. Därför behövde de befintliga säkerhets kopierings lösningarna användas och utökas till Azure. Att utöka befintliga säkerhets kopierings lösningar till Azure fungerar vanligt vis bra med de flesta av de viktigaste leverantörerna i det här utrymmet. 


## <a name="using-a-sql-server-image-out-of-the-microsoft-azure-marketplace"></a><a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Använda en SQL Server avbildning från Microsoft Azure Marketplace
Microsoft erbjuder virtuella datorer på Azure Marketplace, som redan innehåller versioner av SQL Server. För SAP-kunder som behöver licenser för SQL Server och Windows kan det vara en möjlighet att använda dessa avbildningar för att ta fram behovet av licenser genom att skapa virtuella datorer med SQL Server redan installerat. Följande överväganden måste göras för att du ska kunna använda sådana avbildningar för SAP:

* SQL Server icke-utvärderings versioner hämtar högre kostnader än en "Windows-only"-VM som distribuerats från Azure Marketplace. Se de här artiklarna för att jämföra priser: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> och <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/> . 
* Du kan bara använda SQL Server-versioner som stöds av SAP.
* Sorteringen av SQL Server-instansen, som installeras i de virtuella datorer som erbjuds i Azure Marketplace, är inte sorteringen SAP NetWeaver kräver att SQL Server-instansen körs. Du kan ändra sorteringen genom att följa anvisningarna i följande avsnitt.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Ändra SQL Server sortering för Microsoft Windows/SQL Server VM
Eftersom SQL Server avbildningar på Azure Marketplace inte har kon figurer ATS för att använda sorteringen, vilket krävs av SAP NetWeaver-program, måste den ändras direkt efter distributionen. För SQL Server kan denna ändring av sorteringen utföras med följande steg så snart den virtuella datorn har distribuerats och en administratör kan logga in på den distribuerade virtuella datorn:

* Öppna ett Windows-kommando fönster som administratör.
* Ändra katalogen till C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Kör kommandot: Setup.exe/QUIET/ACTION = REBUILDDATABASE/INSTANCENAME = MSSQLSERVER/SQLSYSADMINACCOUNTS = `<local_admin_account_name`>/SQLCOLLATION = SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> är kontot, som definierades som administratörs konto vid distribution av den virtuella datorn för första gången via galleriet.

Processen bör bara ta några minuter. Utför följande steg för att kontrol lera om steget har slutförts med rätt resultat:

* Öppna SQL Server Management Studio.
* Öppna ett frågefönster.
* Kör kommandot sp_helpsort i SQL Server Master-databasen.

Det önskade resultatet bör se ut så här:

```output
Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data
```

Om resultatet skiljer sig kan du avbryta distributionen av SAP och undersöka varför installations kommandot inte fungerade som förväntat. Det finns **inte** stöd för distribution av SAP NetWeaver-program till SQL Server-instans med olika SQL Server-kodsidor än det som anges ovan.

## <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server hög tillgänglighet för SAP i Azure
Med hjälp av SQL Server i Azure IaaS-distributioner för SAP har du flera olika möjligheter att lägga till för att distribuera DBMS-skiktet med hög tillgänglighet. Som det beskrivs i [överväganden för azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar](dbms_guide_general.md) , tillhandahåller Azure olika service avtal för en enskild virtuell dator och ett par med virtuella datorer som distribueras i en Azures tillgänglighets uppsättning. Antagande är att du driver upp till SLA för drift tid för dina produktions distributioner som kräver distribution i Azures tillgänglighets uppsättningar. I sådana fall måste du distribuera minst två virtuella datorer i en sådan tillgänglighets uppsättning. En virtuell dator kommer att köra den aktiva SQL Server-instansen. Den andra virtuella datorn kommer att köra den passiva instansen

### <a name="sql-server-clustering-using-windows-scale-out-file-server"></a>SQL Server klustring med Windows-skalbar fil Server
Med Windows Server 2016 lanserade Microsoft [Lagringsdirigering](/windows-server/storage/storage-spaces/storage-spaces-direct-overview). SQL Server FCI-klustring stöds baserat på Lagringsdirigering distribution. Information finns i artikeln [konfigurera SQL Server redundanskluster på Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md). Lösningen kräver en Azure Load Balancer och hanterar den virtuella IP-adressen för kluster resurserna. SQL Server databasfiler lagras i lagrings utrymmen. Därför är det en förutsättning att du måste skapa lagrings utrymmen för Windows baserat på Azure Premium Storage. Eftersom den här lösningen har stöd för för länge än finns det inga kända SAP-kunder som använder den här lösningen i SAP-produktions scenarier.  

### <a name="sql-server-log-shipping"></a>SQL Server logg överföring
En av metoderna för hög tillgänglighet (HA) är SQL Server logg överföring. Om de virtuella datorerna som ingår i HA-konfigurationen har fungerande namn matchning, finns det inga problem och installationen i Azure skiljer sig inte från alla inställningar som görs lokalt. Vad gäller att ställa in logg överföring och principerna kring logg överföring. Information om SQL Server logg överföring finns i artikeln [om logg överföring (SQL Server)](/sql/database-engine/log-shipping/about-log-shipping-sql-server?view=sql-server-2017).

Den SQL Server logg leverans funktionen användes i Azure för att uppnå hög tillgänglighet i en Azure-region. I följande scenarier har SAP-kunder använt logg överföring tillsammans med Azure:

- Katastrof återställnings scenarier från en Azure-region till en annan Azure-region
- Konfiguration av haveri beredskap från lokal plats till en Azure-region
- Klipp ut-scenarier från lokala datorer till Azure. I dessa fall används logg överföring för att synkronisera den nya DBMS-distributionen i Azure med det pågående produktions systemet lokalt. Vid tidpunkten för styckningen stängs produktionen av och det säkerställs att de senaste och senaste säkerhets kopiorna av transaktions loggen har överförts till Azure-DBMS-distributionen. Sedan öppnas Azure DBMS-distributionen för produktion.  



### <a name="database-mirroring"></a>Databasspegling
Databas spegling som stöds av SAP (se SAP NOTE [965908]) förlitar sig på att definiera en REDUNDANSPARTNERSERVERN i SAP-anslutningssträngen. För de kors lokala fallen antar vi att de två virtuella datorerna finns i samma domän och att användar kontexten för de två SQL Server instanserna körs under en domän användare och har tillräcklig behörighet i de två SQL Server instanser som ingår. Konfigurationen av databas spegling i Azure skiljer sig därför inte mellan en typisk lokal installation/konfiguration.

Vid distributioner med enbart moln är den enklaste metoden att låta en annan domän konfigurera i Azure ha dessa DBMS-VM: ar (och idealiskt dedikerade virtuella SAP-datorer) i en domän.

Om en domän inte är möjlig kan en använda certifikat för databas speglingens slut punkter enligt beskrivningen här: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

En själv studie kurs om hur du konfigurerar databas spegling i Azure hittar du här: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>Ständig aktivering av SQL Server
Eftersom Always on stöds för SAP lokalt (se SAP anmärkning [1772688]), stöds det i kombination med SAP i Azure. Det finns några saker att tänka på när du distribuerar SQL Server tillgänglighets gruppens lyssnare (ska inte förväxlas med Azures tillgänglighets uppsättning) eftersom Azure vid denna tidpunkt inte tillåter att ett AD/DNS-objekt skapas eftersom det är möjligt lokalt. Därför är vissa olika installations steg nödvändiga för att lösa det speciella beteendet i Azure.

Några saker att tänka på när du använder en tillgänglighets grupps lyssnare är:

* Det går bara att använda en lyssnare för tillgänglighets grupper med Windows Server 2012 eller högre som gäst operativ system för den virtuella datorn. För Windows Server 2012 måste du kontrol lera att den här korrigerings filen tillämpas: <https://support.microsoft.com/kb/2854082> 
* För Windows Server 2008 R2 finns inte den här korrigeringen och Always on måste användas på samma sätt som databas spegling genom att ange en redundansrelation i anslutnings strängen (görs via SAP-standardvärdet. PFL-parametern databaser/MSS/server-se SAP NOTE [965908]).
* När du använder en tillgänglighets grupps lyssnare måste de virtuella datorerna i databasen vara anslutna till en dedikerad Load Balancer. För att undvika att Azure tilldelar nya IP-adresser i fall där båda VM: ar stängs av, bör en tilldela statiska IP-adresser till nätverks gränssnitten för de virtuella datorerna i konfigurationen Always On (definiera en statisk IP-adress beskrivs i [den här][virtual-networks-reserved-private-ip] artikeln)
* Det krävs särskilda steg när du skapar WSFC-klustrets konfiguration där klustret behöver en speciell IP-adress, eftersom Azure med dess aktuella funktion skulle tilldela kluster namnet samma IP-adress som den nod som klustret skapas på. Det innebär att en manuell åtgärd måste utföras för att tilldela klustret en annan IP-adress.
* Tillgänglighets gruppens lyssnare kommer att skapas i Azure med TCP/IP-slutpunkter, som tilldelas de virtuella datorerna som kör de primära och sekundära replikerna i tillgänglighets gruppen.
* Det kan finnas behov av att skydda dessa slut punkter med ACL: er.

Detaljerad dokumentation om hur du distribuerar Always on med SQL Server i Azure VM-listor som:

- [Vi presenterar SQL Server Always on-tillgänglighetsgrupper på virtuella Azure-datorer](../../../azure-sql/virtual-machines/windows/availability-group-overview.md).
- [Konfigurera en tillgänglighets grupp som alltid är tillgänglig på virtuella Azure-datorer i olika regioner](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md).
- [Konfigurera en belastningsutjämnare för en Always on-tillgänglighets grupp i Azure](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md).

>[!NOTE]
> Om du konfigurerar Azure Load Balancer för den virtuella IP-adressen för tillgänglighets gruppens lyssnare kontrollerar du att DirectServerReturn har kon figurer ATS. Om du konfigurerar det här alternativet minskas svars tiden för nätverks fördröjningen mellan SAP-program skiktet och DBMS-skiktet. 

SQL Server Always On är den vanligaste funktionen för hög tillgänglighet och haveri beredskap som används i Azure för SAP-arbetsbelastnings distributioner. De flesta kunder använder Always on för hög tillgänglighet inom en enda Azure-region. Om distributionen är begränsad till två noder har du två alternativ för anslutning:

- Använda tillgänglighets gruppens lyssnare. Med tillgänglighets gruppens lyssnare måste du distribuera en Azure Load Balancer. Detta är vanligt vis standard metoden för distribution. SAP-program konfigureras för att ansluta mot tillgänglighets gruppens lyssnare och inte mot en enskild nod
- Använda anslutnings parametrarna för SQL Server databas spegling. I så fall måste du konfigurera anslutningarna för SAP-programmen på ett sätt där båda namnen på noderna namnges. Exakt information om en sådan konfiguration av SAP-sidan finns dokumenterad i SAP Obs [#965908](https://launchpad.support.sap.com/#/notes/965908). Genom att använda det här alternativet behöver du inte konfigurera en lyssnare för tillgänglighets grupp. Och med att det inte finns någon Azure Load Balancer för SQL Server hög tillgänglighet. Därför är nätverks fördröjningen mellan SAP-programlagret och DBMS-skiktet lägre eftersom den inkommande trafiken till SQL Server-instansen inte dirigeras via Azure Load Balancer. Men återkallande, det här alternativet fungerar bara om du begränsar din tillgänglighets grupp så att den omfattar två instanser. 

Ett fåtal kunder utnyttjar SQL Server alltid funktioner för ytterligare haveri beredskap mellan Azure-regioner. Flera kunder använder också möjligheten att utföra säkerhets kopieringar från en sekundär replik. 

## <a name="sql-server-transparent-data-encryption"></a>SQL Server transparent datakryptering
Det finns ett antal kunder som använder SQL Server [Transparent datakryptering (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) när de distribuerar SAP SQL Server-databaser i Azure. Den SQL Server TDE-funktionen stöds fullt ut av SAP (se SAP Obs! [#1380493](https://launchpad.support.sap.com/#/notes/1380493)). 

### <a name="applying-sql-server-tde"></a>Använda SQL Server TDE
I de fall där du utför en heterogen migrering från en annan DBMS, som körs lokalt, till Windows/SQL Server som körs i Azure, bör du skapa en tom mål databas i SQL Server i förväg. Som nästa steg ska du tillämpa SQL Server TDE-funktioner. Även om du fortfarande kör ditt produktions system lokalt. Anledningen till att du vill utföra i den här sekvensen är att processen för att kryptera den tomma databasen kan ta en stund. SAP import-processerna importerar sedan data till den krypterade databasen under drift stopps fasen. Behovet av att importera till en krypterad databas har lägre tids påverkan än att kryptera databasen efter export fasen i fasen drift. Negativa upplevelser var som möjligt när du försöker tillämpa TDE med SAP-arbetsbelastning som körs ovanpå databasen. Därför kommer rekommendationen att behandla distributionen av TDE som en aktivitet som behöver utföras utan SAP-arbetsbelastning på den aktuella databasen.

I de fall där du flyttar SAP SQL Server-databaser från lokala enheter till Azure rekommenderar vi att du testar den infrastruktur som du kan använda för att få krypteringen att tillämpas snabbast. Tänk på följande:

- Du kan inte definiera hur många trådar som används för att tillämpa data kryptering i databasen. Antalet trådar är större beroende av antalet disk volymer som SQL Server data och loggfiler distribueras över. Innebär att de mer distinkta volymerna (enhets beteckningar), desto fler trådar används parallellt för att utföra krypteringen. En sådan konfiguration strider mot en bit med tidigare disk konfigurations förslag på att skapa ett eller ett mindre antal lagrings utrymmen för SQL Server databasfiler i virtuella Azure-datorer. En konfiguration med ett litet antal volymer skulle leda till ett litet antal trådar som kör krypteringen. En Encryption-kryptering läser fler än 64 KB, krypterar den och skriver sedan en post i transaktions logg filen, vilket talar om att den krypterade. Det innebär att belastningen på transaktions loggen är måttlig.
- I äldre SQL Server-versioner fick komprimerings komprimeringen inte längre effektivitet när du krypterade SQL Server-databasen. Detta kan leda till ett problem när ditt abonnemang var att kryptera din SQL Server databas lokalt och sedan kopiera en säkerhets kopia till Azure för att återställa databasen i Azure. SQL Server säkerhets kopierings komprimering uppnår vanligt vis ett komprimerings förhållande på faktor 4.
- Med SQL Server SQL Server 2016 introducerade vi nya funktioner som gör det möjligt att komprimera krypterade databaser på ett effektivt sätt. Mer information finns i [den här bloggen](/archive/blogs/sqlcat/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases) .
 
Genom att behandla programmet för TDE-kryptering utan några till små SAP-arbetsbelastningar bör du testa den specifika konfigurationen på om det är bättre att tillämpa TDE på din SAP-databas lokalt eller att göra det i Azure. I Azure har du mer flexibilitet när det gäller överetablerings infrastruktur och krymper infrastrukturen när TDE har tillämpats.

### <a name="using-azure-key-vault"></a>Använda Azure Key Vault
Azure erbjuder tjänsten för en [Key Vault](https://azure.microsoft.com/services/key-vault/) att lagra krypterings nycklar. SQL Server på den andra sidan erbjuder en koppling för att utnyttja Azure Key Vault som Arkiv för TDE-certifikaten.

Mer information om hur du använder Azure Key Vault för SQL Server TDE-listor som:

- [Utöknings bar nyckel hantering med hjälp av Azure Key Vault (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server?view=sql-server-2017).
- [SQL Server TDE utöknings bar nyckel hantering med Azure Key Vault installations steg](/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault?view=sql-server-2017).
- [SQL Server-anslutning underhåll & fel sökning](/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting?view=sql-server-2017).
- [Fler frågor från kunder om SQL Server Transparent datakryptering – TDE + Azure Key Vault](/archive/blogs/saponsqlserver/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault).


>[!IMPORTANT]
>Med hjälp av SQL Server TDE, särskilt med Azure Key Vault, rekommenderar vi att du använder de senaste korrigeringarna av SQL Server 2014, SQL Server 2016 och SQL Server 2017. Orsak är att baserat på kundfeedback, optimeringar och korrigeringar som tillämpats på koden. Du kan till exempel kontrol lera [KBA #4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm).
>  

## <a name="general-sql-server-for-sap-on-azure-summary"></a><a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Översikt över allmänt SQL Server för SAP på Azure
Det finns många rekommendationer i den här hand boken och vi rekommenderar att du läser den mer än en gång innan du planerar din Azure-distribution. I allmänhet bör du, trots, se till att följa det främsta allmänna DBMS: en för Azure-/regionsspecifika rekommendationer:

1. Använd den senaste DBMS-versionen, som SQL Server 2017, som har flest fördelar i Azure. 
2. Planera dina SAP-system med landskap i Azure för att utjämna layouten för datafiler och Azure-begränsningar:
   * Det finns inte för många diskar, men det finns tillräckligt med utrymme för att se till att du kan komma åt dina nödvändiga IOPS.
   * Om du inte använder Managed Disks bör du komma ihåg att IOPS också är begränsat per Azure Storage konto och att lagrings kontona är begränsade i varje Azure-prenumeration ([Mer information][azure-resource-manager/management/azure-subscription-service-limits]). 
   * Du kan bara ta bort flera diskar om du behöver få ett högre data flöde.
3. Installera aldrig program vara eller Lägg till filer som kräver persistence på D:\ enheten är inte permanent och allt på den här enheten går förlorad vid en omstart av Windows.
4. Använd inte diskcachelagring för Azure standard Storage.
5. Använd inte Azure geo-replikerade Azure standard Storage-konton.  Använd lokalt redundant för DBMS-arbetsbelastningar.
6. Använd din DBMS-leverantörs HA/DR-lösning för att replikera databas data.
7. Använd alltid namn matchning, förlita dig inte på IP-adresser.
8. Använd SQL Server TDE och Använd de senaste SQL Server korrigeringarna.
9. Använd högsta möjliga databas komprimering. Vilket är sid komprimering för SQL Server.
10. Var noga med att använda SQL Server avbildningar från Azure Marketplace. Om du använder SQL Server en måste du ändra instans sorteringen innan du installerar något SAP NetWeaver-system på den.
11. Installera och konfigurera SAP Host Monitoring för Azure enligt beskrivningen i [distributions guiden][deployment-guide].