---
title: "SAP in Azure: guida alla pianificazione e all'implementazione"
description: Guida alla pianificazione e all'implementazione di macchine virtuali di Azure per SAP NetWeaver
author: MSSedusch
manager: juergent
tags: azure-resource-manager
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/17/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: 98cc41b735a5a25f84bed4b443b735fadb0925e5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668020"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Guida alla pianificazione e all'implementazione di macchine virtuali di Azure per SAP NetWeaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
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

[dbms-guide]:dbms-guide.md
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

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

[msdn-set-Azvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-az-ps
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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines/windows/index.yml
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-windows.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-windows-capture-image-resource-manager]:../../windows/capture-image.md
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-create-upload-vhd-oracle]:../../linux/oracle-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#format-and-mount-the-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics-windows]:../../windows/multiple-nics.md
[virtual-networks-multiple-nics-linux]:../../linux/multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-about-vpngateways.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image



Microsoft Azure consente alle aziende di acquisire risorse di calcolo e di archiviazione in poco tempo, senza lunghi cicli di approvvigionamento. Il servizio Macchine virtuali di Azure consente alle aziende di distribuire in Azure applicazioni classiche, ad esempio applicazioni basate su SAP NetWeaver, e di estenderne l'affidabilità e la disponibilità senza risorse aggiuntive in locale. I servizi Macchine virtuali di Azure supportano anche la connettività cross-premise, che consente alle aziende di integrare in modo attivo le Macchine virtuali di Azure nei propri domini locali, i propri cloud privati e nel panorama applicativo del sistema SAP.
Questo white paper illustra i concetti fondamentali relativi alle Macchine virtuali di Microsoft Azure e fornisce considerazioni dettagliate sulla pianificazione e sull'implementazione per installazioni di SAP NetWeaver in Azure. È quindi necessario leggere questo documento prima di avviare le distribuzioni effettive di SAP NetWeaver in Azure.
Questo documento è complementare alla documentazione relativa all'installazione di SAP e alle note SAP, che rappresentano le risorse principali per le installazioni e le distribuzioni del software SAP su piattaforme specifiche.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="summary"></a>Summary
Cloud computing è un termine ampiamente diffuso che sta assumendo un'importanza sempre più rilevante nel settore IT, dalle piccole imprese fino alle grandi aziende e alle multinazionali.

Microsoft Azure è la piattaforma di servizi cloud di Microsoft che offre un'ampia gamma di nuove possibilità. I clienti possono infatti effettuare rapidamente il provisioning e il deprovisioning di applicazioni come servizi sul cloud, senza essere soggetti a eventuali limiti tecnici o di budget. Anziché investire tempo e denaro nell'infrastruttura hardware, le aziende possono concentrarsi sull'applicazione, sui processi aziendali e sui vantaggi per clienti e utenti.

Grazie ai servizi inclusi in Macchine virtuali di Microsoft Azure, Microsoft offre una piattaforma di infrastruttura distribuita come servizio (IaaS) completa. Le applicazioni basate su SAP NetWeaver sono supportate in Macchine virtuali di Azure (IaaS). Questo white paper descrive come pianificare e implementare applicazioni basate su SAP NetWeaver nella piattaforma Microsoft Azure.

Il documento stesso è incentrato su due aspetti principali:

* La prima parte illustra due modelli di distribuzione supportati per applicazioni basate su SAP NetWeaver in Azure. Descrive anche la gestione generale di Azure, con particolare attenzione alle distribuzioni SAP.
* La seconda parte illustra in modo dettagliato l'implementazione dei diversi scenari illustrati nella prima parte.

Per altre risorse, vedere il capitolo [Risorse][planning-guide-1.2] in questo documento.

### <a name="definitions-upfront"></a>Definizioni
Nel documento vengono usati i termini seguenti.

* IaaS: Infrastructure as a Service, Infrastruttura distribuita come servizio
* PaaS: Piattaforma distribuita come servizio
* SaaS: Software as a Service, Software come un servizio
* Componente SAP: singola applicazione SAP, ad esempio ECC, BW, Solution Manager o S/4HANA.  I componenti SAP possono essere basati su tecnologie ABAP o Java tradizionali o su un'applicazione non basata su NetWeaver, ad esempio Business Objects.
* Ambiente SAP: raggruppamento logico di uno o più componenti SAP per una funzione aziendale, ad esempio sviluppo, servizio di controllo della qualità, formazione, ripristino di emergenza o produzione.
* Panorama applicativo SAP: questo termine si riferisce a tutte le risorse SAP presenti nell'ambiente IT di un cliente. Il panorama applicativo SAP include tutti gli ambienti di produzione e non produzione.
* Sistema SAP: combinazione del livello DBMS e del livello applicazione, costituito ad esempio da un sistema di sviluppo SAP ERP, un sistema di test SAP BW, un sistema di produzione SAP CRM e così via. Nelle distribuzioni di Azure non è supportata la divisione di questi due livelli tra ambiente locale e Azure. Un sistema SAP deve quindi essere distribuito o in locale o in Azure. È tuttavia possibile distribuire i diversi sistemi di un panorama applicativo SAP in Azure o in locale. È ad esempio possibile distribuire i sistemi di sviluppo e test SAP CRM in Azure e il sistema di produzione SAP CRM in locale.
* Cross-premise o ibrido: indica uno scenario in cui le VM vengono distribuite in una sottoscrizione di Azure con connettività da sito a sito, multisito o ExpressRoute tra i data center locali e Azure. Nella documentazione comune su Azure questi tipi di distribuzioni vengono definiti anche scenari cross-premise o ibridi. La connessione consente di estendere i domini locali, l'istanza locale di Active Directory/OpenLDAP e il DNS locale in Azure. Il panorama applicativo locale viene esteso alle risorse di Azure della sottoscrizione. Questa estensione consente alle macchine virtuali di fare parte del dominio locale. Gli utenti di dominio del dominio locale possono accedere ai server e possono eseguire servizi in queste VM, ad esempio i servizi DBMS. La comunicazione e la risoluzione dei nomi tra VM distribuite in locale e VM distribuite in Azure sono consentite. Questo è il caso più comune e quasi esclusivo di distribuzione delle risorse SAP in Azure. Per altre informazioni, vedere [questo][vpn-gateway-cross-premises-options] articolo e [questo][vpn-gateway-site-to-site-create].
* Estensione di monitoraggio di Azure, monitoraggio avanzato ed estensione di Azure per SAP: indicano lo stesso elemento, ovvero un'estensione di macchina virtuale che deve essere distribuita dall'utente per fornire alcuni dati di base sull'infrastruttura di Azure all'agente host SAP. Nelle note SAP può essere indicata come estensione di monitoraggio o monitoraggio avanzato. In Azure è indicata come **estensione di Azure per SAP**.

> [!NOTE]
> Le distribuzioni cross-premise o ibride di sistemi SAP in cui le macchine virtuali di Azure che eseguono sistemi SAP sono membri di un dominio locale sono supportate per i sistemi SAP di produzione. Le configurazioni cross-premise o ibride sono supportate per la distribuzione parziale o completa di panorami applicativi SAP in Azure. Anche l'esecuzione del panorama applicativo SAP completo in Azure richiede che queste VM siano parte di un dominio locale e di ADS/OpenLDAP.
>
>



### <a name="resources"></a><a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Risorse
Il punto di ingresso per il carico di lavoro SAP nella documentazione di Azure è [qui](./get-started.md). Partendo da questo punto di ingresso, è possibile trovare numerosi articoli che trattano gli argomenti seguenti:

- SAP NetWeaver e Business One in Azure
- Guide DBMS di SAP per vari sistemi DBMS in Azure
- Disponibilità elevata e ripristino di emergenza per il carico di lavoro SAP in Azure
- Indicazioni specifiche per l'esecuzione di SAP HANA in Azure
- Indicazioni specifiche di Azure HANA in istanze Large per DBMS di SAP HANA


> [!IMPORTANT]
> Dove possibile, viene usato un collegamento alle guide all'installazione SAP o ad altra documentazione SAP di riferimento. Per informazioni di riferimento su InstGuide-01, vedere <http://service.sap.com/instguides>. Relativamente ai prerequisiti, al processo di installazione o ai dettagli delle funzionalità SAP specifiche, leggere sempre attentamente la documentazione e le guide SAP perché i documenti Microsoft trattano solo determinate attività per il software SAP installato e gestito in una macchina virtuale di Microsoft Azure.
>
>

Le note seguenti su SAP sono correlate all'argomento relativo a SAP in Azure:

| Numero della nota | Titolo |
| --- | --- |
| [1928533] |Applicazioni SAP in Azure: Prodotti e dimensioni supportate |
| [2015553] |SAP in Microsoft Azure: prerequisiti per il supporto |
| [1999351] |Risoluzione dei problemi del monitoraggio avanzato di Azure per SAP |
| [2178632] |Metriche chiave del monitoraggio per SAP in Microsoft Azure |
| [1409604] |Virtualizzazione in Windows: monitoraggio avanzato |
| [2191498] |SAP in Linux con Azure: monitoraggio avanzato |
| [2243692] |Linux in una macchina virtuale di Microsoft Azure (IaaS): problemi delle licenze SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: Note sull'installazione |
| [2002167] |Red Hat Enterprise Linux 7.x: installazione e aggiornamento |
| [2069760] |Installazione e aggiornamento di Oracle Linux 7.x SAP |
| [1597355] |Raccomandazione sullo spazio di scambio per Linux |

Vedere anche il [wiki in SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contenente tutte le note SAP per Linux.

Le limitazioni predefinite generali e le limitazioni massime delle sottoscrizioni di Azure sono disponibili in [questo articolo][azure-resource-manager/management/azure-subscription-service-limits-subscription].

## <a name="possible-scenarios"></a>Scenari possibili
SAP è spesso considerata una delle applicazioni più cruciali nelle aziende. L'architettura e le operazioni di queste applicazioni sono in genere complesse ed è quindi importante assicurare il rispetto dei requisiti per la disponibilità e le prestazioni.

Le aziende devono quindi valutare con attenzione quale provider di servizi cloud scegliere per l'esecuzione di tali processi aziendali business critical. Azure è la piattaforma cloud pubblico ideale per le applicazioni SAP e i processi aziendali business critical. Data la grande varietà dell'infrastruttura di Azure, attualmente quasi tutti i sistemi SAP NetWeaver e S/4HANA esistenti possono essere ospitati in Azure. Azure fornisce macchine virtuali con molti terabyte di memoria e più di 200 CPU. Oltre ad Azure offre [istanze large di Hana](./hana-overview-architecture.md), che consentono distribuzioni Hana con scalabilità verticale fino a 24 tb e SAP Hana distribuzioni con scalabilità orizzontale fino a 120 TB. Si può affermare che oggi quasi tutti gli scenari SAP locali possono essere eseguiti anche in Azure.

Per una descrizione semplice degli scenari e per alcuni scenari non supportati, vedere il documento [Carico di lavoro SAP negli scenari supportati da macchine virtuali di Azure](./sap-planning-supported-configurations.md).

Controllare questi scenari e alcune delle condizioni indicate come non supportate nella documentazione di riferimento durante la pianificazione e lo sviluppo dell'architettura che si vuole distribuire in Azure.

In generale, il modello di distribuzione più comune è uno scenario cross-premise, come illustrato nella figura seguente

![VPN con connettività da sito a sito (cross-premise)][planning-guide-figure-300]

Molti clienti applicano un modello di distribuzione cross-premise perché consente di estendere in modo molto trasparente tutte le applicazioni in locale in Azure usando Azure ExpressRoute e di considerare Azure come data center virtuale. Man mano che sempre più asset vengono spostati in Azure, l'infrastruttura distribuita di Azure e l'infrastruttura di rete cresceranno e le risorse locali si ridurranno di conseguenza, in modo del tutto trasparente per gli utenti e le applicazioni.

Per distribuire correttamente i sistemi SAP in IaaS di Azure o IaaS generica, è importate comprendere le differenze significative tra le offerte di outsourcer o provider di servizi di hosting tradizionali e le offerte IaaS. Mentre il provider di servizi di hosting o l'outsourcer tradizionale adatta l'infrastruttura (rete, risorse di archiviazione e tipo di server) al carico di lavoro che un cliente vuole ospitare, la caratterizzazione del carico di lavoro e la scelta dei componenti di Azure appropriati per macchine virtuali, risorse di archiviazione e rete per le distribuzioni IaaS è responsabilità del cliente o del partner.

Per raccogliere i dati per la pianificazione della distribuzione in Azure, è importante:

- Valutare quali prodotti SAP possono essere eseguiti nelle macchine virtuali di Azure
- Valutare quali versioni specifiche del sistema operativo sono supportate con macchine virtuali di Azure specifiche per i prodotti SAP
- Valutare quali versioni del sistema di gestione di database sono supportate per i prodotti SAP con macchine virtuali di Azure specifiche
- Valutare se alcune delle versioni del sistema operativo/sistema di gestione di database necessarie richiedono di eseguire la versione SAP, l'aggiornamento del pacchetto per il supporto e gli aggiornamenti del kernel per ottenere una configurazione supportata
- Valutare se è necessario passare a sistemi operativi diversi per eseguire la distribuzione in Azure.

Per informazioni dettagliate sui componenti SAP in Azure supportati, sulle unità dell'infrastruttura di Azure supportate e sulle versioni del sistema operativo e del sistema di gestione di database correlate, vedere l'articolo [Software SAP supportato per le distribuzioni di Azure](./sap-supported-product-on-azure.md). I risultati ottenuti dalla valutazione delle versioni valide di SAP, del sistema operativo e del sistema di gestione di database hanno un impatto notevole sul lavoro richiesto per spostare i sistemi SAP in Azure. I risultati di questa valutazione consentono di stabilire se sarà richiesto un considerevole lavoro di preparazione qualora siano necessari aggiornamenti della versione di SAP o modifiche dei sistemi operativi.


## <a name="azure-regions"></a><a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Aree di Azure
I servizi di Azure di Microsoft vengono raccolti nelle aree di Azure. Un'area di Azure è un data center o una raccolta di data center che contengono l'hardware e l'infrastruttura che esegue e ospita i diversi servizi di Azure. Questa infrastruttura include un numero elevato di nodi che fungono da nodi di calcolo o da nodi di archiviazione oppure eseguono funzionalità di rete.

Per un elenco delle diverse aree di Azure, vedere l'articolo [Aree geografiche di Azure](https://azure.microsoft.com/global-infrastructure/geographies/). Non tutte le aree di Azure offrono gli stessi servizi. A seconda del prodotto SAP che si vuole eseguire e del sistema operativo e del sistema di gestione di database correlati, è possibile che una determinata area non offra i tipi di macchine virtuali richiesti. Questo vale in particolare per l'esecuzione di SAP HANA, in cui in genere sono necessarie macchine virtuali della serie M/Mv2. Queste famiglie di macchine virtuali vengono distribuite solo in un subset delle aree. Per informazioni sui tipi di macchine virtuali, sui tipi di archiviazione di Azure o sugli altri servizi di Azure disponibili nelle singole aree, visitare il sito [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/). Quando si inizia la pianificazione e si pensa di scegliere una determinata area primaria e magari anche una determinata area secondaria, prima di tutto si deve stabilire se i servizi necessari sono disponibili in tali aree.

### <a name="availability-zones"></a>Zone di disponibilità
Per diverse aree di Azure è stato implementato il concetto delle cosiddette zone di disponibilità. Le zone di disponibilità sono località separate fisicamente entro un'area di Azure. Ogni zona di disponibilità è costituita da uno o più data center dotati di impianti indipendenti per l'energia, il raffreddamento e la rete. Ad esempio, la distribuzione di due macchine virtuali in due zone di disponibilità di Azure e l'implementazione di un framework a disponibilità elevata per il sistema di gestione di database SAP o i servizi centrali SAP offrono il contratto di servizio migliore in Azure. Per questo specifico contratto di servizio per le macchine virtuali in Azure, vedere la versione più recente di [Contratto di servizio per Macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Poiché le aree di Azure si sono sviluppate ed estese rapidamente negli ultimi anni, la topologia delle aree di Azure, il numero di data center fisici, la distanza tra tali data center e la distanza tra le zone di disponibilità di Azure possono essere diversi e può esserlo anche la latenza di rete.

Il principio delle zone di disponibilità non si applica al servizio specifico [HANA in istanze Large](./hana-overview-architecture.md). Per informazioni sui contratti di servizio per HANA in istanze Large, vedere l'articolo [Contratto di servizio per SAP HANA in istanze Large di Azure](https://azure.microsoft.com/support/legal/sla/sap-hana-large/)


### <a name="fault-domains"></a><a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Domini di errore
I domini di errore rappresentano un'unità fisica di errore, strettamente correlata all'infrastruttura fisica contenuta nei data center. Anche se è possibile considerare un pannello fisico o un rack come dominio di errore, non è disponibile alcun mapping diretto uno-a-uno tra i due.

Quando si distribuiscono più macchine virtuali come parte di un sistema SAP nei servizi Macchine virtuali di Microsoft Azure, è possibile influenzare il controller di infrastruttura di Azure in modo da distribuire l'applicazione in domini di errore diversi, rispettando quindi i requisiti più elevati dei contratti di servizio relativi alla disponibilità. L'utente non ha alcun controllo diretto sulla distribuzione di domini di errore in un'unità di scala di Azure, ovvero una raccolta di centinaia di nodi di calcolo o di nodi di archiviazione e di risorse di rete, oppure sull'assegnazione di macchine virtuali a un dominio di errore specifico. Per fare in modo che il controller di infrastruttura di Azure distribuisca un set di macchine virtuali in diversi domini di errore, è necessario assegnare un set di disponibilità di Azure alle macchine virtuali durante la fase di distribuzione. Per altre informazioni sui set di disponibilità di Azure, vedere il capitolo [Set di disponibilità di Azure][planning-guide-3.2.3] in questo documento.


### <a name="upgrade-domains"></a><a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Domini di aggiornamento
I domini di aggiornamento rappresentano un'unità logica utile per determinare il modo in cui viene aggiornata una macchina virtuale in un sistema SAP, costituito da istanze di SAP in esecuzione in più macchine virtuali. Quando si verifica un aggiornamento, Microsoft Azure esegue il processo di aggiornamento dei domini di aggiornamento, uno alla volta. Suddividendo le macchine virtuali in diversi domini di aggiornamento durante la fase di distribuzione, è possibile proteggere parzialmente il sistema SAP da potenziali tempi di inattività. Per imporre ad Azure la distribuzione delle macchine virtuali in un sistema SAP suddivise in diversi domini di aggiornamento, è necessario impostare un attributo specifico in fase di distribuzione di ogni VM. Analogamente ai domini di errore, un'unità di scala di Azure viene divisa in più domini di aggiornamento. Per fare in modo che il controller di infrastruttura di Azure distribuisca un set di macchine virtuali in diversi domini di aggiornamento, è necessario assegnare un set di disponibilità di Azure alle VM durante la fase di distribuzione. Per altre informazioni sui set di disponibilità di Azure, vedere il capitolo [Set di disponibilità di Azure][planning-guide-3.2.3] più avanti.


### <a name="azure-availability-sets"></a><a name="18810088-f9be-4c97-958a-27996255c665"></a>Set di disponibilità di Azure
Le macchine virtuali di Azure in un set di disponibilità di Azure verranno distribuite dal controller di infrastruttura di Azure in diversi domini di errore e di aggiornamento. Lo scopo della distribuzione in diversi domini di errore e di aggiornamento consiste nell'evitare che tutte le macchine virtuali di un sistema SAP vengano arrestate in caso di manutenzione dell'infrastruttura o in caso di errore in un dominio di errore. Per impostazione predefinita, le macchine virtuali non fanno parte di un set di disponibilità. La partecipazione di una macchina virtuale a un set di disponibilità viene definita in fase di distribuzione oppure successivamente mediante una riconfigurazione e una ridistribuzione di una macchina virtuale.

Per informazioni sul concetto di set di disponibilità di Azure e sulla correlazione tra i set di disponibilità e i domini di errore e di aggiornamento, vedere [questo articolo][virtual-machines-manage-availability].

Quando si definiscono i set di disponibilità e si prova a combinare macchine virtuali di famiglie diverse in un set di disponibilità, è possibile che si verifichino problemi che impediscono di includere un determinato tipo di macchina virtuale in tale set di disponibilità. Il motivo è che il set di disponibilità è associato a un'unità di scala che contiene un determinato tipo di host di calcolo e un determinato tipo di host di calcolo può eseguire solo determinati tipi di famiglie di macchine virtuali. Se ad esempio si crea un set di disponibilità, si sceglie un tipo di macchina virtuale della famiglia Esv3 e la si distribuisce nel set di disponibilità e quindi si prova a distribuire una seconda macchina virtuale della famiglia M, la seconda allocazione verrà rifiutata perché le macchine virtuali della famiglia Esv3 non vengono eseguite nello stesso hardware host delle macchine virtuali della famiglia M. Lo stesso problema può verificarsi quando si prova a ridimensionare le macchine virtuali e a convertire una macchina virtuale della famiglia Esv3 in un tipo di macchina virtuale della famiglia M. Nel caso di ridimensionamento in una famiglia di macchine virtuali che non può essere ospitata nello stesso hardware host, è necessario arrestare tutte le macchine virtuali del set di disponibilità e ridimensionarle per poterle eseguire nell'altro tipo di computer host. Per informazioni sui contratti di servizio delle macchine virtuali distribuite in un set di disponibilità, vedere l'articolo [Contratto di servizio per Macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

Il principio di set di disponibilità e di dominio di errore e di aggiornamento correlato non si applica al servizio specifico [HANA in istanze Large](./hana-overview-architecture.md). Per informazioni sui contratti di servizio per HANA in istanze Large, vedere l'articolo [Contratto di servizio per SAP HANA in istanze Large di Azure](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

> [!IMPORTANT]
> I concetti di zone di disponibilità di Azure e di set di disponibilità di Azure si escludono a vicenda. Ciò significa che è possibile distribuire due o più macchine virtuali in una zona di disponibilità specifica o in un set di disponibilità di Azure, ma non in entrambi.

### <a name="azure-paired-regions"></a>Aree abbinate di Azure
Azure offre coppie di aree di Azure in cui la replica di determinati dati è abilitata tra le coppie di aree fisse. L'associazione di aree è documentata nell'articolo [continuità aziendale e ripristino di emergenza (BCdR): aree abbinate di Azure](../../../best-practices-availability-paired-regions.md). Come illustrato nell'articolo, la replica dei dati è legata ai tipi di archiviazione di Azure che possono essere configurati per la replica nell'area abbinata. Vedere anche l'articolo [ridondanza di archiviazione in un'area secondaria](../../../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region). I tipi di archiviazione che consentono tale replica sono tipi di archiviazione, che non sono adatti per il carico di lavoro DBMS. Di conseguenza, l'usabilità della replica di archiviazione di Azure è limitata all'archiviazione BLOB di Azure, ad esempio per scopi di backup, o ad altri scenari di archiviazione a latenza elevata. Quando si verificano le aree abbinate e i servizi che si vuole usare come area primaria o secondaria, è possibile che si verifichino situazioni in cui i servizi di Azure e/o i tipi di VM che si intende usare nell'area primaria non sono disponibili nell'area abbinata. In alternativa, è possibile che si verifichi una situazione in cui l'area abbinata di Azure non è accettabile per motivi di conformità dei dati. In questi casi, è necessario usare un'area non abbinata come area di ripristino di emergenza o secondaria. In tal caso, è necessario prestare attenzione alla replica di alcuni dei dati che Azure avrebbe replicato autonomamente. Un esempio di come replicare il Active Directory e il DNS nell'area di ripristino di emergenza è descritto nell'articolo [configurare il ripristino di emergenza per Active Directory e DNS](../../../site-recovery/site-recovery-active-directory.md)


## <a name="azure-virtual-machine-services"></a>Servizi di macchine virtuali di Azure
Azure offre un'ampia gamma di macchine virtuali che è possibile selezionare per la distribuzione. Non sono necessari acquisti iniziali di tecnologia e infrastruttura. L'offerta di servizio Macchine virtuali di Azure semplifica la manutenzione e la gestione delle applicazioni offrendo risorse di calcolo e di archiviazione su richiesta per ospitare, ridimensionare e gestire applicazioni Web e applicazioni connesse. La gestione dell'infrastruttura viene automatizzata con una piattaforma progettata per la disponibilità elevata e il ridimensionamento dinamico per adeguarsi alle esigenze di utilizzo, con la possibilità di scegliere tra diversi modelli di prezzi.

![Posizionamento di Servizi Macchine virtuali di Microsoft Azure][planning-guide-figure-400]

Le macchine virtuali di Azure consentono di distribuire immagini server personalizzate in Azure come istanze IaaS. In alternativa, è possibile scegliere tra una vasta gamma di immagini di sistema operativo disponibili nella raccolta di immagini di Azure.

Dal punto di vista operativo, il servizio Macchine virtuali di Azure offre un'esperienza analoga alle macchine virtuali distribuite in locale. L'utente è responsabile dell'amministrazione, delle operazioni e anche dell'applicazione di patch del sistema operativo specifico in esecuzione in una macchina virtuale di Azure e delle applicazioni di tale macchina virtuale. Microsoft non fornisce altri servizi oltre all'hosting di tale macchina virtuale nell'infrastruttura di Azure (infrastruttura distribuita come servizio). Per il carico di lavoro SAP distribuito dal cliente, Microsoft non propone altre offerte oltre alle offerte IaaS.

La piattaforma Microsoft Azure è una piattaforma multi-tenant. Di conseguenza le risorse di archiviazione, di rete e di calcolo che ospitano le macchine virtuali di Azure sono, con alcune eccezioni, condivise tra i tenant. Una logica intelligente di limitazioni e quote viene usata per impedire a un tenant di influire drasticamente sulle prestazioni di un altro tenant. In particolare per la certificazione della piattaforma Azure per SAP HANA, Microsoft deve dimostrare a SAP l'isolamento delle risorse nei casi in cui più macchine virtuali possono essere eseguite nello stesso host a intervalli regolari. Anche se la logica in Azure prova a limitare le varianze rilevate nella larghezza di banda, le piattaforme con un livello elevato di condivisione tendono a presentare varianze per la disponibilità di risorse/larghezza di banda superiori a quelle che i clienti potrebbero riscontrare nelle distribuzioni locali. Occorre tenere in considerazione la probabilità che un sistema SAP in Azure presenti varianze superiori rispetto a un sistema locale.

### <a name="azure-virtual-machines-for-sap-workload"></a>Macchine virtuali di Azure per il carico di lavoro SAP

Per il carico di lavoro SAP, la selezione è stata limitata a famiglie di macchine virtuali diverse, più adatte per il carico di lavoro SAP e il carico di lavoro SAP HANA. Per trovare il tipo corretto di macchina virtuale e la capacità di eseguire il carico di lavoro SAP, vedere il documento [Software SAP supportato per le distribuzioni di Azure](./sap-supported-product-on-azure.md).

> [!NOTE]
> I tipi di macchine virtuali certificati per il carico di lavoro SAP non effettuano l'overprovisioning delle risorse di CPU e memoria.

Oltre alla selezione dei tipi di VM puramente supportati, è anche necessario controllare se tali tipi di VM sono disponibili in un'area specifica in base ai [prodotti](https://azure.microsoft.com/global-infrastructure/services/)del sito disponibili in base all'area. ma è ancora più importante valutare se:

- Le risorse di CPU e di memoria per diversi tipi di macchine virtuali
- La larghezza di banda per operazioni di I/O al secondo di diversi tipi di macchine virtuali
- La funzionalità di rete dei diversi tipi di macchine virtuali
- Il numero di dischi che è possibile collegare
- La possibilità di utilizzare determinati tipi di archiviazione di Azure

rispondono alle proprie esigenze. La maggior parte di tali dati per uno specifico tipo di macchina virtuale si trova [qui (Linux)][virtual-machines-sizes-linux] e [qui (Windows)][virtual-machines-sizes-windows].

Per il modello di determinazione dei prezzi sono disponibili diverse opzioni, ad esempio:

- Pagamento in base al consumo
- Prenotazione di un anno
- Prenotazione di tre anni
- Prezzi del servizio Spot

Il prezzo di ognuna delle diverse offerte di servizio in base al sistema operativo e alle aree è indicato nei siti [Prezzi di Macchine virtuali Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Prezzi di Macchine virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Per informazioni dettagliate e per conoscere la flessibilità delle istanze riservate di un anno e di tre anni, vedere questi articoli:

- [Informazioni sulle prenotazioni di Azure](../../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Flessibilità di dimensioni delle macchine virtuali con le istanze di macchina virtuale riservate](../../reserved-vm-instance-size-flexibility.md)
- [Come viene applicato lo sconto per la prenotazione di Azure alle macchine virtuali](../../../cost-management-billing/manage/understand-vm-reservation-charges.md)

Per altre informazioni sui prezzi del servizio Spot, vedere l'articolo [Macchine virtuali Spot di Azure](https://azure.microsoft.com/pricing/spot/). I prezzi dello stesso tipo di macchina virtuale possono essere diversi a seconda dell'area di Azure. Per alcuni clienti, poteva essere vantaggioso eseguire la distribuzione in un'area di Azure meno costosa.

Anche il concetto di host dedicato è proprio di Azure. Il concetto di host dedicato offre un maggiore controllo sui cicli di patch eseguiti da Azure. È possibile programmare l'applicazione delle patch in base alle proprie pianificazioni. Questa offerta è destinata specificamente ai clienti con un carico di lavoro che potrebbe non seguire il normale ciclo dei carichi di lavoro. Per informazioni sui concetti alla base delle offerte di host dedicato di Azure, vedere l'articolo [Host dedicato di Azure](../../dedicated-hosts.md). Questa offerta è supportata per il carico di lavoro SAP e viene scelta da diversi clienti SAP che vogliono un maggiore controllo sull'applicazione di patch all'infrastruttura e sugli eventuali piani di manutenzione di Microsoft. Per altre informazioni su come Microsoft esegue la manutenzione e applica le patch all'infrastruttura di Azure che ospita le macchine virtuali, vedere l'articolo [Manutenzione per macchine virtuali in Azure](../../maintenance-and-updates.md).

#### <a name="generation-1-and-generation-2-virtual-machines"></a>Macchine virtuali di prima e di seconda generazione
L'hypervisor di Microsoft riesce a gestire due diverse generazioni di macchine virtuali. Tale formati sono chiamati **Generazione 1** e **Generazione 2**. La **generazione 2** è stata introdotta nel 2012 con l'hypervisor di Windows Server 2012. Azure ha iniziato a usare macchine virtuali di prima generazione. Quando si distribuiscono macchine virtuali di Azure, per impostazione predefinita viene ancora usato il formato Generazione 1. Nel frattempo è possibile distribuire anche macchine virtuali con il formato Generazione 2. L'articolo [Supporto per le macchine virtuali di seconda generazione in Azure](../../generation-2.md) elenca le famiglie di macchine virtuali di Azure che possono essere distribuite come macchine virtuali di seconda generazione. Questo articolo elenca anche le principali differenze funzionali delle macchine virtuali di seconda generazione, che possono essere eseguite nel cloud privato Hyper-V e in Azure. Ancora più importante, questo articolo elenca anche le differenze funzionali tra le macchine virtuali di prima generazione e le macchine virtuali di seconda generazione, che vengono eseguite in Azure.

> [!NOTE]
> Esistono differenze funzionali tra le macchine virtuali di prima e di seconda generazione eseguite in Azure. Per un elenco di tali differenze, vedere l'articolo [Supporto per le macchine virtuali di seconda generazione in Azure](../../generation-2.md).

Non è possibile trasferire una macchina virtuale esistente da una generazione all'altra. Per modificare la generazione della macchina virtuale, è necessario distribuire una nuova macchina virtuale della generazione desiderata e reinstallare il software in esecuzione nella macchina virtuale della generazione. Questa modifica interessa solo l'immagine VHD di base della macchina virtuale e non ha alcun impatto sui dischi dati o sulle condivisioni NFS o SMB connesse. Dischi dati, NFS o condivisioni SMB originariamente assegnati, ad esempio, in una macchina virtuale di prima generazione.

> [!NOTE]
> La distribuzione di macchine virtuali della famiglia Mv1 come macchine virtuali di seconda generazione è possibile a partire dall'inizio di maggio 2020. In questo modo è possibile trasferire macchine virtuali dalla famiglia Mv1 alla famiglia Mv2 e viceversa.


### <a name="storage-microsoft-azure-storage-and-data-disks"></a><a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Archiviazione: Archiviazione di Microsoft Azure e dischi dati
Le macchine virtuali di Microsoft Azure utilizzano diversi tipi di archiviazione. Quando si implementa SAP nei servizi Macchine virtuali di Azure, è importante comprendere le differenze tra questi due tipi principali di archiviazione:

* Archiviazione volatile, non permanente.
* Archiviazione permanente.

Le macchine virtuali di Azure offrono dischi non persistenti dopo la distribuzione di una macchina virtuale. In caso di riavvio di una VM, tutto il contenuto delle unità viene cancellato. È di conseguenza importante che i file di dati e i file registro/di rollforward dei database non vengano mai posizionati in tali unità non persistenti. Potrebbero esserci delle eccezioni per alcuni database, dove queste unità non persistenti potrebbero essere adatte per gli spazi di tabella di tempdb e temp. Evitare invece di usare queste unità nelle VM serie A poiché, con questa famiglia di VM, le unità non persistenti sono limitate in termini di velocità effettiva. Per altre informazioni, leggere l'articolo [Understanding the temporary drive on Windows Azure Virtual Machines](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines) (Informazioni sull'unità temporanea nelle macchine virtuali Windows di Azure)

---
> ![Logo Windows.][Logo_Windows] Windows
>
> In una VM di Azure, D:\ è un'unità non persistente supportata da alcuni dischi locali nel nodo di calcolo di Azure. Il fatto che sia non persistente implica che tutte le modifiche apportate al contenuto dell'unità D:\ vengono perse al riavvio della macchina virtuale. Per "tutte le modifiche" si intendono i file memorizzati, le directory create, le applicazioni installate, ecc.
>
> ![Logo Linux.][Logo_Linux] Linux
>
> Nelle VM Linux di Azure viene montata automaticamente in /mnt/resource un'unità non persistente supportata da dischi locali nel nodo di calcolo di Azure. Il fatto che sia non persistente implica che tutte le modifiche apportate al contenuto in /mnt/resource vengono perse al riavvio della VM. Per "tutte le modifiche" si intendono i file memorizzati, le directory create, le applicazioni installate, ecc.
>
>

#### <a name="azure-storage-accounts"></a>Account di archiviazione di Azure

Quando si distribuiscono servizi o macchine virtuali in Azure, la distribuzione di dischi rigidi virtuali e immagini di VM è organizzata in unità definite account di archiviazione di Azure. Gli [account di archiviazione di Azure](../../../storage/common/storage-account-overview.md) presentano limitazioni in IOPS, velocità effettiva o dimensioni che possono contenere. In passato queste limitazioni, documentate in:

- [Obiettivi di scalabilità per gli account di archiviazione standard](../../../storage/common/scalability-targets-standard-account.md)
- [Obiettivi di scalabilità per gli account di archiviazione BLOB di pagine Premium](../../../storage/blobs/scalability-targets-premium-page-blobs.md)

ha svolto un ruolo importante nella pianificazione di una distribuzione SAP in Azure. Era necessario gestire il numero di dischi salvati in modo permanente in un account di archiviazione. È necessario gestire gli account di archiviazione e infine creare nuovi account di archiviazione per creare dischi salvati in modo permanente.

Negli ultimi anni, l'introduzione di [Azure Managed disks](../../managed-disks-overview.md) ha sollevato tali attività. La raccomandazione per le distribuzioni SAP consiste nell'usare Azure Managed disks invece di gestire gli account di archiviazione di Azure. Azure Managed disks distribuisce i dischi tra diversi account di archiviazione, quindi non vengono superati i limiti dei singoli account di archiviazione.

In un account di archiviazione è presente un tipo di cartella denominato "container" che può essere usato per raggruppare determinati dischi in contenitori specifici.

In Azure un nome disco/VHD segue la connessione di denominazione seguente che deve fornire un nome univoco per il disco rigido virtuale in Azure:

`http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>`

La stringa precedente deve identificare in modo univoco il disco/disco rigido virtuale archiviato in archiviazione di Azure.


#### <a name="azure-persisted-storage-types"></a>Tipi di archiviazione salvati in modalità permanente di Azure
Azure offre un'ampia gamma di opzioni di archiviazione permanente che possono essere usate per il carico di lavoro SAP e componenti specifici dello stack SAP. Per altri dettagli, vedere il documento  [archiviazione di Azure per carichi di lavoro SAP](./planning-guide-storage.md).


### <a name="microsoft-azure-networking"></a><a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Rete di Microsoft Azure

Microsoft Azure offrirà un'infrastruttura di rete che consente il mapping di tutti gli scenari da realizzare con il software SAP. Ecco le funzionalità disponibili:

* Accesso dall'esterno direttamente alle macchine virtuali tramite Servizi terminal di Windows o ssh/VNC
* Accesso ai servizi e a porte specifiche usate dalle applicazioni entro le macchine virtuali
* Comunicazioni interne e risoluzione dei nomi tra un gruppo di macchine virtuali distribuite come macchine virtuali di Azure
* Connettività cross-premise tra la rete locale di un cliente e la rete di Azure
* Connettività tra aree o data center di Azure tra i siti di Azure

Altre informazioni sono disponibili qui: <https://azure.microsoft.com/documentation/services/virtual-network/>

Esistono diverse possibilità per configurare la risoluzione di nomi e IP in Azure. È anche disponibile un servizio DNS di Azure, che può essere usato invece di configurare un server DNS personalizzato. Altre informazioni sono disponibili in [questo articolo][virtual-networks-manage-dns-in-vnet] e in [questa pagina](https://azure.microsoft.com/services/dns/).

Per gli scenari cross-premise o ibridi ci si basa sul fatto che le istanze locali di AD/OpenLDAP/DNS sono state estese tramite VPN o connessione privata ad Azure. Per determinati scenari illustrati in questo documento, può essere necessario che in Azure sia installata una replica di AD/OpenLDAP.

La rete e la risoluzione dei nomi sono parti fondamentali della distribuzione di database per un sistema SAP, quindi questo concetto viene discusso in maggior dettaglio nella [Guida alla distribuzione DBMS][dbms-guide].

##### <a name="azure-virtual-networks"></a>Reti virtuali di Azure

La creazione di una rete virtuale di Azure consente di definire l'intervallo di indirizzi degli indirizzi IP privati allocati dalla funzionalità DHCP di Azure. Negli scenari cross-premise, l'intervallo di indirizzi IP definito viene comunque allocato usando DHCP di Azure. La risoluzione dei nomi di dominio avviene tuttavia eseguita in locale, presupponendo che le VM facciano parte di un dominio locale, e sarà quindi possibile risolvere indirizzi che si trovano al di là di diversi servizi cloud di Azure.

Ogni macchina virtuale di Azure deve essere connessa a una rete virtuale.

Per informazioni dettagliate, vedere [questo articolo][resource-groups-networking] e [questa pagina](https://azure.microsoft.com/documentation/services/virtual-network/).


> [!NOTE]
> Per impostazione predefinita, dopo la distribuzione di una macchina virtuale non è possibile modificare la configurazione della rete virtuale. Le impostazioni di TCP/IP devono essere lasciate sul server DHCP di Azure. Il comportamento predefinito prevede l'assegnazione di IP dinamici.
>
>

L'indirizzo MAC della scheda di rete virtuale potrebbe cambiare, ad esempio dopo il ridimensionamento, e il sistema operativo guest Windows o Linux rileverà la nuova scheda di rete e userà automaticamente il DHCP per assegnare gli indirizzi IP e DNS in questo caso.

##### <a name="static-ip-assignment"></a>Assegnazione di indirizzi IP statici
È possibile assegnare indirizzi IP fissi o riservati a macchine virtuali in una rete virtuale di Azure. L'esecuzione di VM in una rete virtuale di Azure offre la possibilità di sfruttare i vantaggi di questa funzionalità se necessario o richiesto per alcuni scenari. L'assegnazione di indirizzi IP rimane valida nel corso dell'intera esistenza della macchina virtuale, indipendentemente dal fatto che la VM sia in esecuzione o che sia stata arrestata. Sarà quindi necessario tenere in considerazione il numero complessivo di macchine virtuali, in esecuzione e arrestate, durante la definizione dell'intervallo di indirizzi IP per la rete virtuale. L'indirizzo IP rimane assegnato fino all'eliminazione della macchina virtuale e della rispettiva interfaccia di rete o fino al nuovo annullamento dell'assegnazione dell'indirizzo IP. Per altre informazioni, vedere [questo articolo][virtual-networks-static-private-ip-arm-pportal].

> [!NOTE]
> È consigliabile assegnare indirizzi IP statici tramite Azure alle singole schede di interfaccia di rete virtuali. Non è opportuno assegnare indirizzi IP statici all'interno del sistema operativo guest a una scheda di interfaccia di rete virtuale. Alcuni servizi di Azure, come Backup di Azure, si basano sul fatto che almeno la scheda di interfaccia di rete virtuale primaria sia impostata su DHCP e non su indirizzi IP statici. Vedere anche il documento [Risolvere i problemi relativi al backup delle macchine virtuali di Azure](../../../backup/backup-azure-vms-troubleshoot.md#networking).
>
>

##### <a name="multiple-nics-per-vm"></a>Più schede di interfaccia di rete per ogni VM

È possibile definire più schede di interfaccia di rete virtuali (vNIC, Virtual Network Interface Card) per una macchina virtuale di Azure. La possibilità di usare più vNIC consente di iniziare a configurare la separazione del traffico di rete, ad esempio per indirizzare il traffico client tramite una vNIC e il traffico back-end tramite una seconda vNIC. In base al tipo di macchina virtuale, sono previste diverse limitazioni per il numero di vNIC che possono essere assegnate a una macchina virtuale. Per informazioni dettagliate precise, funzionalità e limitazioni, vedere questi articoli:

* [Creare una macchina virtuale Windows con più schede di interfaccia di rete][virtual-networks-multiple-nics-windows]
* [Creare una macchina virtuale Linux con più schede di interfaccia di rete][virtual-networks-multiple-nics-linux]
* [Distribuire macchine virtuali con più NIC tramite un modello][virtual-network-deploy-multinic-arm-template]
* [Distribuire macchine virtuali con più NIC tramite PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Distribuire macchine virtuali con più NIC tramite l'interfaccia della riga di comando di Azure][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Connettività da sito a sito

La soluzione cross-premise è costituita da macchine virtuali di Azure e locali collegate tramite una connessione VPN trasparente e permanente. Questo diventerà probabilmente il modello di distribuzione SAP più comune in Azure. Si presuppone che le procedure e i processi operativi relativi alle istanze SAP in Azure debbano funzionare in modo trasparente. In altri termini, dovrebbe essere possibile ottenere output da questi sistemi e usare il sistema STMS (SAP Correction and Transport System) per trasferire le modifiche da un sistema di distribuzione in Azure a un sistema di test distribuito in locale. Per altre informazioni sulla modalità da sito a sito, vedere [questo articolo][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>Dispositivo tunnel VPN

Per creare una connessione da sito a sito da un data center locale a un data center di Azure, è necessario ottenere e configurare un dispositivo VPN oppure usare il servizio routing e accesso remoto (RRAS), introdotto come componente software con Windows Server 2012.

* [Creare una rete virtuale con una connessione VPN da sito a sito usando PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [Informazioni sui dispositivi VPN per le connessioni di gateway VPN da sito a sito][vpn-gateway-about-vpn-devices]
* [Domande frequenti sul gateway VPN][vpn-gateway-vpn-faq]

![Connessione da sito a sito tra rete locale e Azure][planning-guide-figure-600]

La figura precedente mostra due sottoscrizioni di Azure con intervalli secondari di indirizzi IP riservati per l'uso nelle reti virtuali in Azure. La connettività dalla rete locale ad Azure viene stabilita tramite VPN.

#### <a name="point-to-site-vpn"></a>VPN da punto a sito

La VPN da punto a sito richiede che ogni computer client si connetta ad Azure mediante la propria VPN. La connettività da punto a sito non risulta pratica per gli scenari SAP esaminati. Non vengono quindi forniti altri riferimenti alla connettività VPN da punto a sito.

Ulteriori informazioni sono disponibili qui
* [Configurare una connessione da punto a sito a una rete virtuale usando il portale di Azure](../../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Configurare una connessione da punto a sito a una rete virtuale usando PowerShell](../../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

#### <a name="multi-site-vpn"></a>VPN multisito

Azure offre attualmente anche la possibilità di creare connettività VPN multisito per una sottoscrizione di Azure. In precedenza una singola sottoscrizione era limitata a una connessione VPN da sito a sito. Questa limitazione è stata rimossa grazie alle connessioni VPN multisito per una singola sottoscrizione. Questo consente di usare più aree di Azure per una sottoscrizione specifica tramite le configurazioni cross-premise.

Per altre informazioni, vedere [questo articolo][vpn-gateway-create-site-to-site-rm-powershell]

#### <a name="vnet-to-vnet-connection"></a>Connessione da rete virtuale a rete virtuale

Se si usa una VPN multisito, è necessario configurare una rete virtuale di Azure separata in ogni area. Spesso è tuttavia necessario che i componenti software delle diverse aree comunichino tra loro. Queste comunicazioni non dovrebbero essere idealmente indirizzate da un'area di Azure all'ambiente locale e quindi all'altra area di Azure. Per semplificare, Azure offre la possibilità di configurare una connessione da una rete virtuale di Azure in un'area a un'altra rete virtuale di Azure ospitata in un'altra area. Questa funzionalità viene definita connessione da rete virtuale a rete virtuale. Altri dettagli su questa funzionalità sono disponibili qui: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure-expressroute"></a>Connessione privata ad Azure ExpressRoute

Microsoft Azure ExpressRoute consente la creazione di connessioni private tra i data center di Azure e l'infrastruttura locale del cliente o in un ambiente con condivisione percorso dati. ExpressRoute viene offerto da diversi provider di VPN MPLS (con scambio di pacchetto) o da altri provider di servizi di rete. Le connessioni ExpressRoute non sfruttano la rete Internet pubblica. Le connessioni ExpressRoute offrono un livello di sicurezza superiore, maggiore affidabilità tramite più circuiti paralleli, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali.

Altri dettagli su Azure ExpressRoute e sulle offerte sono disponibili qui:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

ExpressRoute abilita più sottoscrizioni di Azure tramite un circuito ExpressRoute, come illustrato qui

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Tunneling forzato in caso di scenari cross-premise
Per le macchine virtuali aggiunte a domini locali tramite connessioni da sito a sito, da punto a sito o ExpressRoute, è necessario assicurarsi che le impostazioni del proxy Internet vengano distribuite anche per tutti gli utenti in tali macchine virtuali. Per impostazione predefinita, il software in esecuzione in queste VM o gli utenti che usano un browser per accedere a Internet non passano attraverso il proxy aziendale, ma si connettono direttamente a Internet tramite Azure. L'impostazione del proxy, tuttavia, non costituisce una soluzione perfetta per l'indirizzamento del traffico attraverso il proxy aziendale, poiché il software e i servizi sono responsabili della verifica del proxy. Se il software in esecuzione nella VM non esegue la verifica o se un amministratore modifica le impostazioni, il traffico verso Internet può essere reindirizzato di nuovo direttamente a Internet tramite Azure.

Per evitare questa connettività Internet diretta, è possibile configurare il tunneling forzato con connettività da sito a sito tra l'ambiente locale e Azure. La descrizione dettagliata della funzionalità di tunneling forzato è disponibile qui <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

Il tunneling forzato con ExpressRoute viene abilitato dai clienti mediante l'annuncio di una route predefinita tramite le sessioni di peering BGP ExpressRoute.

#### <a name="summary-of-azure-networking"></a>Riepilogo della rete di Azure

Questo capitolo include molti aspetti importanti relativi ai servizi di rete di Azure. Ecco un riepilogo degli aspetti principali:

* Le reti virtuali di Azure consentono di inserire una struttura di rete nella distribuzione di Azure. Le reti virtuali possono essere isolate tra loro o con l'aiuto dei gruppi di sicurezza di rete il traffico tra le reti virtuali può essere controllato.
* È possibile usare le reti virtuali di Azure per assegnare intervalli di indirizzi IP a macchine virtuali o assegnare indirizzi IP fissi alle VM.
* Per configurare una connessione da sito a sito o da punto a sito, è necessario creare prima di tutto una rete virtuale di Azure.
* Dopo la distribuzione di una macchina virtuale, non è più possibile cambiare la rete virtuale assegnata alla VM

### <a name="quotas-in-azure-virtual-machine-services"></a>Quote nei servizi macchine virtuali di Azure
È importante chiarire che l'infrastruttura di archiviazione e di rete è condivisa tra macchine virtuali che eseguono un'ampia varietà di servizi nell'infrastruttura di Azure. Come nei data center personali del cliente, il provisioning eccessivo di alcune delle risorse dell'infrastruttura viene effettuato a un certo livello. La piattaforma Microsoft Azure usa quote di dischi, CPU, rete e di altro tipo per limitare l'utilizzo di risorse e preservare prestazioni coerenti e deterministiche.  I diversi tipi di VM (A5, A6 e così via) prevedono quote diverse per il numero di dischi e per le risorse di CPU, RAM e rete.

> [!NOTE]
> Le risorse di CPU e di memoria dei tipi di macchine virtuali supportati da SAP vengono preallocate nei nodi host. Dopo la distribuzione della VM, le risorse sull'host sono quindi disponibili in base a quanto definito dal tipo di macchina virtuale.
>
>

Quando si esegue la pianificazione per SAP e se ne definiscono le dimensioni in soluzioni di Azure, è necessario tenere in considerazione le quote per ogni dimensione di macchina virtuale. Le quote delle macchine virtuali sono descritte [qui (Linux)][virtual-machines-sizes-linux] e [qui (Windows)][virtual-machines-sizes-windows].

Le quote descritte rappresentano i valori massimi teorici.  Il limite di IOPS per ogni disco può essere eseguito con le operazioni di I/o di dimensioni ridotte (8 KB), ma potrebbe non essere raggiungibile con I/o di grandi dimensioni (1 MB).  Il limite di IOPS viene applicato a livello dei singoli dischi.

È possibile usare l'albero delle decisioni seguente come strumento approssimativo per decidere se un sistema SAP è adatto ai servizi inclusi in Macchine virtuali di Azure e alle relative funzionalità oppure se un sistema esistente deve essere configurato in modo diverso per essere distribuito in Azure.

![Albero delle decisioni relativo alla possibilità di distribuire SAP in Azure][planning-guide-figure-700]

1. le informazioni più importanti da cui iniziare sono i requisiti SAPS per uno specifico sistema SAP. I requisiti SAPS devono essere suddivisi tra la parte DBMS e la parte dell'applicazione SAP, anche se il sistema SAP è già distribuito in locale in una configurazione a 2 livelli. Per i sistemi esistenti, i valori SAPS correlati all'hardware in uso spesso possono essere determinati o stimati in base agli attuali benchmark SAP. I risultati sono disponibili [qui](https://sap.com/about/benchmark.html). Per i sistemi SAP appena distribuiti, è necessario eseguire un esercizio di ridimensionamento per determinare i requisiti SAPS del sistema.
1. per i sistemi esistenti, è necessario misurare il volume e le operazioni I/O al secondo nel server DBMS. Per i sistemi appena pianificati, l'esercizio di ridimensionamento per il nuovo sistema deve fornire anche un'idea approssimativa dei requisiti I/O sul lato DBMS. In caso di dubbi, è opportuno eseguire un modello di verifica.
1. confrontare i requisiti SAPS per il server DBMS con i valori SAPS forniti dai diversi tipi di macchine virtuali di Azure. Le informazioni sui valori SAPS dei diversi tipi di VM di Azure sono documentate nella nota SAP [1928533]. È opportuno concentrarsi prima di tutto sulla macchina virtuale DBMS perché, nella maggior parte delle distribuzioni, il livello di database è il livello di un sistema SAP NetWeaver per cui viene aumentato il numero di istanze. Al contrario, il livello applicazione SAP può essere scalato orizzontalmente. Se nessuno dei tipi di macchine virtuali di Azure supportati da SAP riesce a offrire i valori SAPS richiesti, il carico di lavoro del sistema SAP pianificato non può essere eseguito in Azure. È necessario distribuire il sistema locale o modificare il volume del carico di lavoro per il sistema.
1. come documentato [qui (Linux)][virtual-machines-sizes-linux] e [qui (Windows)][virtual-machines-sizes-windows], Azure applica una quota di operazioni di I/O al secondo a ogni disco a prescindere dall'uso dell'archiviazione Standard o dell'archiviazione Premium. A seconda del tipo di macchina virtuale, il numero di dischi che può essere montato varia. Di conseguenza, è possibile calcolare un numero massimo di operazioni di I/O al secondo che può essere ottenuto con i singoli tipi di macchine virtuali. A seconda del layout del file di database, è possibile eseguire lo striping dei dischi, in modo da ottenere un solo volume nel sistema operativo guest. Tuttavia, se il volume corrente di operazioni di I/O al secondo di un sistema SAP distribuito supera i limiti calcolati per il tipo di macchina virtuale più esteso di Azure e se non è possibile alcuna compensazione aggiungendo altra memoria, gli effetti sul carico di lavoro del sistema SAP possono essere significativi. In questi casi, si arriva a un punto in cui non è opportuno distribuire il sistema in Azure.
1. soprattutto nei sistemi SAP distribuiti in locale in configurazioni a 2 livelli, è probabile che il sistema debba essere configurato in Azure con una configurazione a 3 livelli. In questo passaggio è necessario controllare se nel livello applicazione SAP è presente un componente che non consente la scalabilità orizzontale e non può essere adattato alle risorse di CPU e memoria offerte dai diversi tipi di macchine virtuali di Azure. Se è effettivamente presente un componente di questo tipo, il sistema SAP e il relativo carico di lavoro non possono essere distribuiti in Azure. Tuttavia, se è possibile aumentare il numero di istanze per i componenti dell'applicazione SAP in più macchine virtuali di Azure, il sistema può essere distribuito in Azure.

se i componenti dei livelli applicazione SAP e DBMS possono essere eseguiti nelle macchine virtuali di Azure, è necessario definire la configurazione per:

* Numero di macchine virtuali di Azure
* Tipi di macchine virtuali per i singoli componenti
* Numero di dischi rigidi virtuali nella macchina virtuale DBMS per fornire un numero sufficiente di operazioni di I/O al secondo

## <a name="managing-azure-assets"></a>Gestione delle risorse di Azure

### <a name="azure-portal"></a>Portale di Azure

Il portale di Azure è una delle tre interfacce con cui gestire le distribuzioni di macchine virtuali di Azure. Le attività di gestione di base, come la distribuzione di macchine virtuali da immagini, possono essere eseguite dal portale di Azure. Un'altra attività che il portale di Azure riesce a gestire in modo ottimale è la creazione di account di archiviazione, reti virtuali e altri componenti di Azure. Tuttavia, funzionalità quali il caricamento di dischi rigidi virtuali da locale ad Azure o la copia di un disco rigido virtuale in Azure sono attività che richiedono strumenti di terze parti o l'amministrazione con PowerShell o l'interfaccia della riga di comando.

![Portale di Microsoft Azure: panoramica delle macchine virtuali][planning-guide-figure-800]


Le attività di amministrazione e configurazione per l'istanza della macchina virtuale possono essere eseguite dal portale di Azure.

Oltre a riavviare e arrestare una macchina virtuale, è possibile collegare, scollegare e creare dischi dati per l'istanza della macchina virtuale, acquisire l'istanza per la preparazione dell'immagine e configurare le dimensioni dell'istanza della macchina virtuale.

Il portale di Azure fornisce funzionalità di base per distribuire e configurare le macchine virtuali e molti altri servizi di Azure. Tuttavia non tutte le funzionalità disponibili sono coperte dal portale di Azure, ad esempio, non è possibile:

* Caricare i dischi rigidi virtuali in Azure
* Copiare macchine virtuali


### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Gestione con i cmdlet di Microsoft Azure PowerShell

Windows PowerShell è un framework potente ed estensibile che è stato ampiamente adottato dai clienti che distribuiscono grandi quantità di sistemi in Azure. Dopo l'installazione dei cmdlet di PowerShell in un desktop, un portatile o una stazione di gestione dedicata, i cmdlet di PowerShell possono essere eseguiti in modalità remota.

Il processo di abilitazione di un desktop/portatile locale per l'uso dei cmdlet di Azure PowerShell e di configurazione dei cmdlet per l'uso con le sottoscrizioni di Azure è descritto in [questo articolo][powershell-install-configure].

Informazioni più dettagliate su come installare, aggiornare e configurare i cmdlet di Azure PowerShell sono disponibili anche in [questo capitolo della Guida alla distribuzione][deployment-guide-4.1].

In base all'esperienza dei clienti, finora PowerShell (PS) è risultato senza dubbio lo strumento più potente per distribuire le macchine virtuali e per creare istruzioni personalizzate durante la distribuzione di macchine virtuali. Tutti i clienti che eseguono istanze SAP in Azure usano i cmdlet di PS per integrare le attività di gestione che eseguono nel portale di Azure oppure usano i cmdlet in modo esclusivo per gestire le distribuzioni in Azure. I cmdlet specifici di Azure condividono la stessa convenzione di denominazione di oltre 2000 altri cmdlet correlati a Windows, quindi l'uso di questi cmdlet risulta estremamente semplice per gli amministratori di Windows.

Vedere l'esempio qui: <https://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>


L'estensione di Azure per SAP (vedere il capitolo [Estensione di Azure per SAP][planning-guide-9.1] in questo documento) può essere distribuita solo con PowerShell o l'interfaccia della riga di comando. È quindi necessario installare e configurare PowerShell o l'interfaccia della riga di comando quando si distribuisce o si amministra un sistema SAP NetWeaver in Azure.

Con l'introduzione di nuove funzionalità in Azure, verranno aggiunti nuovi cmdlet di PS che richiedono un aggiornamento dei cmdlet. Per questo motivo, è opportuno visitare il sito Download di Azure <https://azure.microsoft.com/downloads/> almeno una volta al mese per scaricare una nuova versione dei cmdlet. La nuova versione viene installata su quella precedente.

Per un elenco generale dei comandi di PowerShell correlati ad Azure, vedere qui: <https://docs.microsoft.com/powershell/azure/>.

### <a name="management-via-microsoft-azure-cli-commands"></a>Gestione con i comandi dell'interfaccia della riga di comando di Microsoft Azure

PowerShell potrebbe non essere la soluzione ottimale per i clienti che usano Linux e vogliono gestire le risorse di Azure. Microsoft offre l'interfaccia della riga di comando di Azure come alternativa.
L'interfaccia della riga di comando di Azure fornisce un insieme di comandi open source e multipiattaforma per utilizzare la piattaforma Azure. Fornisce gran parte delle funzionalità disponibili nel portale di Azure.

Per informazioni sull'installazione, la configurazione e l'uso dei comandi dell'interfaccia della riga di comando per eseguire attività di Azure, vedere

* [Installare l'interfaccia della riga di comando classica di Azure][xplat-cli]
* [Distribuire e gestire le macchine virtuali usando modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure][../../linux/create-ssh-secured-vm-from-template.md]
* [Usare l'interfaccia della riga di comando classica di Azure per Mac, Linux e Windows con Azure Resource Manager][xplat-cli-azure-resource-manager]

Vedere anche il capitolo [Interfaccia della riga di comando di Azure per macchine virtuali Linux][deployment-guide-4.5.2] nella [Guida alla distribuzione][planning-guide] per informazioni su come usare l'interfaccia della riga di comando di Azure per distribuire l'estensione di Azure per SAP.


## <a name="first-steps-planning-a-deployment"></a>Primi passaggi per la pianificazione di una distribuzione
Il primo passaggio della pianificazione della distribuzione NON è quello di verificare la disponibilità di macchine virtuali per l'esecuzione di SAP. Il primo passaggio, che può richiedere molto tempo, ma è molto importante, consiste nel collaborare con i team dell'azienda responsabili della conformità e della sicurezza per stabilire quali siano le condizioni limite per la distribuzione di un determinato tipo di carico di lavoro SAP o processo aziendale nel cloud pubblico. Se la società ha già distribuito altro software in Azure, il processo può essere semplice. Se la società affronta questa procedura per la prima volta, è possibile che sia necessario dedicare più tempo per stabilire le condizioni di limite e le condizioni di sicurezza che consentono di ospitare nel cloud pubblico determinati dati SAP e processi aziendali SAP.

Per un elenco delle offerte di conformità di Microsoft, vedere [Offerte di conformità Microsoft](/microsoft-365/compliance/offering-home).

Altre problematiche, ad esempio la crittografia dei dati inattivi o altre forme di crittografia nel servizio Azure, sono documentate in [Panoramica della crittografia in Azure](../../../security/fundamentals/encryption-overview.md).

Non sottovalutare questa fase del progetto durante la pianificazione. Solo dopo aver trovato un accordo e aver stabilito delle regole su questo argomento, sarà possibile procedere al passaggio successivo, ovvero la pianificazione dell'architettura di rete distribuita in Azure.


## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Diversi modi per distribuire le macchine virtuali per SAP in Azure

In questo capitolo, vengono descritte le diverse modalità per distribuire una macchina virtuale in Azure. Gli argomenti trattati includono le varie procedure di preparazione e la gestione dei dischi rigidi virtuali e delle macchine virtuali in Azure.

### <a name="deployment-of-vms-for-sap"></a>Distribuzione di VM per SAP

Microsoft Azure offre diversi modi per distribuire le VM e i dischi associati. È quindi importante comprendere le differenze perché le operazioni di preparazione delle macchine virtuali possono variare in base al metodo di distribuzione. In generale, verranno analizzati gli scenari seguenti:

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Spostamento di una VM da locale ad Azure con un disco non generalizzato

Si può pianificare di spostare un sistema SAP specifico da locale ad Azure. A tale scopo è possibile caricare in Azure il disco rigido virtuale contenente il sistema operativo, i file binari SAP e gli eventuali file binari DBMS nonché i dischi rigidi virtuali con i file di dati e di log del sistema DBMS. A differenza dello [scenario 2 seguente][planning-guide-5.1.2], il nome host, il SID di SAP e gli account utente SAP vengono mantenuti nella macchina virtuale di Azure perché sono stati configurati nell'ambiente locale. Di conseguenza, non è necessario generalizzare l'immagine. Vedere il capitolo [Preparazione per lo spostamento di una macchina virtuale da locale ad Azure con un disco non generalizzato][planning-guide-5.2.1] di questo documento per le operazioni di preparazione locali e il caricamento di macchine virtuali non generalizzate o di dischi rigidi virtuali in Azure. Leggere il capitolo [Scenario 3: Spostamento di una macchina virtuale da locale usando un disco rigido virtuale di Azure non generalizzato con SAP][deployment-guide-3.4] nella [Guida alla distribuzione][deployment-guide] per conoscere i passaggi dettagliati della distribuzione di un'immagine in Azure.

Un'altra opzione che non verrà illustrata in dettaglio in questa guida è l'uso di Azure Site Recovery per replicare i server applicazioni SAP NetWeaver e i servizi centrali SAP NetWeaver in Azure. Non è consigliabile usare Azure Site Recovery per il livello di database e usare invece meccanismi di replica specifici del database, ad esempio la replica di sistema HANA. Per altre informazioni, vedere il capitolo [proteggere SAP](../../../site-recovery/site-recovery-workload.md#protect-sap) della Guida alle [informazioni sul ripristino di emergenza per le app locali](../../../site-recovery/site-recovery-workload.md) .

#### <a name="deploying-a-vm-with-a-customer-specific-image"></a><a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Distribuzione di una VM con un'immagine specifica del cliente

Le immagini disponibili in Azure Marketplace potrebbero non soddisfare le esigenze a causa di specifici requisiti di patch riguardanti la versione del sistema operativo o DBMS in uso. Per questo motivo, potrebbe essere necessario creare una macchina virtuale usando una propria immagine privata di macchina virtuale del sistema operativo o DBMS, che in seguito potrà essere distribuita diverse volte. Per preparare un'immagine privata di questo tipo per la duplicazione, considerare gli elementi seguenti:

---
> ![Logo Windows.][Logo_Windows] Windows
>
> Per informazioni dettagliate, vedere: <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> Le impostazioni di Windows, ad esempio il SID e il nome host di Windows, devono essere astratte o generalizzate nella macchina virtuale locale con il comando sysprep.
>
>
> ![Logo Linux.][Logo_Linux] Linux
>
> Seguire i passaggi descritti in questi articoli per [SUSE][virtual-machines-linux-create-upload-vhd-suse], [Red Hat][virtual-machines-linux-redhat-create-upload-vhd] o [Oracle Linux][virtual-machines-linux-create-upload-vhd-oracle] per preparare un disco rigido virtuale per il caricamento in Azure.
>
>

---
Se sono già stati installati contenuti SAP nella VM locale, in particolare per sistemi a 2 livelli, è possibile adattare le impostazioni di sistema SAP dopo la distribuzione della VM di Azure con la procedura di ridenominazione dell'istanza supportata da SAP Software Provisioning Manager (nota SAP [1619720]). Vedere i capitoli [Preparazione per la distribuzione di una macchina virtuale con un'immagine specifica del cliente per SAP][planning-guide-5.2.2] e [Caricamento di un disco rigido virtuale da locale ad Azure][planning-guide-5.3.2] di questo documento per le operazioni di preparazione locali e il caricamento di una macchina virtuale generalizzata in Azure. Leggere il capitolo [Scenario 2: Distribuzione di una macchina virtuale con un'immagine personalizzata per SAP][deployment-guide-3.3] nella [Guida alla distribuzione][deployment-guide] per i passaggi dettagliati della distribuzione di un'immagine in Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Distribuzione di una VM da Azure Marketplace

Per distribuire la macchina virtuale si sceglie di usare un'immagine di VM offerta da Microsoft o terze parti in Azure Marketplace. Dopo aver distribuito la VM in Azure, si installa il software SAP e/o DBMS all'interno della VM usando le stesse linee guida e gli stessi strumenti usati in un ambiente locale. Per una descrizione più dettagliata della distribuzione, vedere il capitolo [Scenario 1: Distribuzione di una macchina virtuale da Azure Marketplace per SAP][deployment-guide-3.2] nella [Guida alla distribuzione][deployment-guide].

### <a name="preparing-vms-with-sap-for-azure"></a><a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Preparazione di VM con SAP per Azure

Prima di caricare le macchine virtuali in Azure, è necessario assicurarsi che le macchine virtuali e i dischi rigidi virtuali soddisfino determinati requisiti. Esistono piccole differenze a seconda del metodo di distribuzione che viene usato.

#### <a name="preparation-for-moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Preparazione per lo spostamento di una VM da locale ad Azure con un disco non generalizzato

Un metodo di distribuzione comune consiste nello spostare in Azure una macchina virtuale esistente che esegue un sistema SAP in locale. Questa macchina virtuale e il sistema SAP nella macchina virtuale devono essere eseguiti in Azure usando lo stesso nome host e, probabilmente, lo stesso SID di SAP. In questo caso, il sistema operativo guest della macchina virtuale non deve essere generalizzato per più distribuzioni. Se la rete locale viene estesa in Azure, nella macchina virtuale è anche possibile usare gli stessi account di dominio di quelli usati in precedenza in locale.

I requisiti per la preparazione del disco della VM di Azure sono:

* In origine, il disco rigido virtuale contenente il sistema operativo può avere una dimensione massima di 127 GB. Questa limitazione è stata eliminata alla fine del mese di marzo 2015. A questo punto il disco rigido virtuale contenente il sistema operativo può avere dimensioni fino a 1 TB come qualsiasi altro disco rigido virtuale ospitato di archiviazione di Azure.
* Deve essere nel formato disco rigido virtuale fisso. Il disco o i dischi rigidi virtuali dinamici in formato VHDx non sono ancora supportati in Azure. I dischi rigidi virtuali dinamici vengono convertiti in dischi rigidi virtuali statici quando si carica il disco rigido virtuale con i cmdlet di PowerShell o l'interfaccia della riga di comando
* Anche i dischi rigidi virtuali montati nella macchina virtuale e che devono essere montati nuovamente nella macchina virtuale di Azure devono essere in formato disco rigido virtuale fisso. Leggere [questo articolo](../../managed-disks-overview.md) per i limiti di dimensioni dei dischi dati. I dischi rigidi virtuali dinamici vengono convertiti in dischi rigidi virtuali statici quando si carica il disco rigido virtuale con i cmdlet di PowerShell o l'interfaccia della riga di comando
* Aggiungere un altro account locale con privilegi di amministratore, che può essere usato dal supporto tecnico Microsoft o assegnato come contesto in cui eseguire servizi e applicazioni finché non viene distribuita la macchina virtuale e non possono essere usati utenti più adatti.
* Aggiungere altri account locali come quelli che potrebbero essere richiesti per lo scenario di distribuzione specifico.

---
> ![Logo Windows.][Logo_Windows] Windows
>
> In questo scenario non è necessaria la generalizzazione (sysprep) della macchina virtuale per caricare e distribuire la macchina virtuale in Azure.
> Accertarsi che non venga utilizzata l’unità D:\.
> Impostare il montaggio automatico dei dischi collegati come descritto nel capitolo [Impostazione del montaggio automatico per dischi collegati][planning-guide-5.5.3] di questo documento.
>
> ![Logo Linux.][Logo_Linux] Linux
>
> In questo scenario non è necessaria la generalizzazione (waagent -deprovision) della macchina virtuale per caricare e distribuire la macchina virtuale in Azure.
> Assicurarsi che /mnt/resource non sia usato e che TUTTI i dischi siano montati con UUID. Per il disco del sistema operativo assicurarsi che la voce bootloader rifletta anche il montaggio basato su UUID.
>
>

---
#### <a name="preparation-for-deploying-a-vm-with-a-customer-specific-image-for-sap"></a><a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Preparazione per la distribuzione di una VM con un'immagine specifica del cliente per SAP

I file dei dischi rigidi virtuali che contengono un sistema operativo generalizzato vengono archiviati in contenitori negli account di archiviazione di Azure o come immagini di dischi gestiti. È possibile distribuire una nuova macchina virtuale da tale immagine facendo riferimento all'immagine del disco rigido virtuale o del disco gestito come origine nei file del modello di distribuzione, come descritto nel capitolo [Scenario 2: Distribuzione di una macchina virtuale con un'immagine personalizzata per SAP][deployment-guide-3.3] della [Guida alla distribuzione][deployment-guide].

I requisiti per la preparazione dell'immagine di VM di Azure sono:

* In origine, il disco rigido virtuale contenente il sistema operativo può avere una dimensione massima di 127 GB. Questa limitazione è stata eliminata alla fine del mese di marzo 2015. A questo punto il disco rigido virtuale contenente il sistema operativo può avere dimensioni fino a 1 TB come qualsiasi altro disco rigido virtuale ospitato di archiviazione di Azure.
* Deve essere nel formato disco rigido virtuale fisso. Il disco o i dischi rigidi virtuali dinamici in formato VHDx non sono ancora supportati in Azure. I dischi rigidi virtuali dinamici vengono convertiti in dischi rigidi virtuali statici quando si carica il disco rigido virtuale con i cmdlet di PowerShell o l'interfaccia della riga di comando
* Anche i dischi rigidi virtuali montati nella macchina virtuale e che devono essere montati nuovamente nella macchina virtuale di Azure devono essere in formato disco rigido virtuale fisso. Leggere [questo articolo](../../managed-disks-overview.md) per i limiti di dimensioni dei dischi dati. I dischi rigidi virtuali dinamici vengono convertiti in dischi rigidi virtuali statici quando si carica il disco rigido virtuale con i cmdlet di PowerShell o l'interfaccia della riga di comando
* Aggiungere altri account locali come quelli che potrebbero essere richiesti per lo scenario di distribuzione specifico.
* Se l'immagine contiene un'installazione di SAP NetWeaver ed è probabile che il nome host venga modificato rispetto al nome originale al momento della distribuzione di Azure, si consiglia di copiare le versioni più recenti del DVD di SAP Software Provisioning Manager nel modello. Questo consentirà di usare facilmente la funzionalità di ridenominazione SAP fornita per adattare il nome host modificato e/o per modificare il SID del sistema SAP all'interno dell'immagine di VM distribuita non appena viene avviata una nuova copia.

---
> ![Logo Windows.][Logo_Windows] Windows
>
> Verificare che l'unità D:\ non sia usata. Impostare il montaggio automatico dei dischi collegati come descritto nel capitolo [Impostazione del montaggio automatico per dischi collegati][planning-guide-5.5.3] di questo documento.
>
> ![Logo Linux.][Logo_Linux] Linux
>
> Assicurarsi che /mnt/resource non sia usato e che TUTTI i dischi siano montati con UUID. Per il disco del sistema operativo assicurarsi che la voce bootloader rifletta anche il montaggio basato su UUID.
>
>

---
* L'interfaccia utente grafica (GUI) SAP può essere preinstallata in questo modello per scopi di amministrazione e installazione.
* È possibile installare altri software necessari per eseguire correttamente le macchine virtuali in scenari cross-premise purché siano compatibili con la ridenominazione della macchina virtuale.

Se la macchina virtuale è stata preparata in modo sufficiente per essere generica ed eventualmente indipendente da account/utenti non disponibili nello scenario di distribuzione di Azure di destinazione, viene eseguito l'ultimo passaggio di preparazione per la generalizzazione di questa immagine.

##### <a name="generalizing-a-vm"></a>Generalizzazione di una macchina virtuale
---
> ![Logo Windows.][Logo_Windows] Windows
>
> L'ultimo passaggio consiste nell'accedere a una macchina virtuale con un account amministratore. Aprire una finestra di comando di Windows come *amministratore*. Accedere a %windir%\windows\system32\sysprep ed eseguire sysprep.exe.
> Viene visualizzata una finestra di piccole dimensioni. È importante selezionare l'opzione **Generalizza**, deselezionata per impostazione predefinita, e modificare l'opzione di arresto dall'impostazione predefinita "Riavvia" ad "Arresta". Questa procedura presuppone che il processo sysprep venga eseguito in locale nel sistema operativo guest di una macchina virtuale.
> Per eseguire la procedura con una macchina virtuale già in esecuzione in Azure, seguire i passaggi in [questo articolo](../../windows/capture-image-resource.md).
>
> ![Logo Linux.][Logo_Linux] Linux
>
> [Come acquisire una macchina virtuale Linux da usare come modello di Resource Manager][capture-image-linux-step-2-create-vm-image]
>
>

---
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Trasferimento di macchine virtuali e dischi rigidi virtuali tra il sistema locale e Azure
Il caricamento di immagini di VM e dischi in Azure con il portale di Azure non è consentito, quindi è necessario usare i cmdlet di Azure PowerShell o l'interfaccia della riga di comando. In alternativa, è possibile usare lo strumento "AzCopy". Lo strumento può copiare i dischi rigidi virtuali tra il sistema locale e Azure, in entrambe le direzioni. Può anche copiare i dischi rigidi virtuali tra le diverse aree di Azure. Per informazioni sul download e sull'uso di AzCopy, vedere [la documentazione][storage-use-azcopy] .

Una terza alternativa prevede l'uso di diversi strumenti per l'interfaccia utente grafica di terze parti. Tuttavia, assicurarsi che questi strumenti supportino i BLOB di pagine di Azure. In questo caso, è necessario usare l'archiviazione BLOB di pagine di Azure. Le differenze sono descritte qui: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>. Anche gli strumenti forniti da Azure sono efficaci per la compressione delle macchine virtuali e dei dischi rigidi virtuali da caricare. Si tratta di un fattore importante perché una compressione efficace riduce il tempo di caricamento, che varia comunque in base al collegamento per il caricamento in Internet dalla struttura locale e all'area di distribuzione di Azure di destinazione. È ragionevole supporre che il caricamento di una macchina virtuale o di un disco rigido virtuale da una località Europa ai data center di Azure basati negli Stati Uniti richiederà più tempo rispetto al caricamento delle stesse macchine virtuali/dischi rigidi virtuali in data center di Azure Europai.

#### <a name="uploading-a-vhd-from-on-premises-to-azure"></a><a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Caricamento di un disco rigido virtuale da locale ad Azure
Per caricare una macchina virtuale o un disco rigido virtuale esistente dalla rete locale, è necessario che siano soddisfatti i requisiti elencati nel capitolo [Preparazione per lo spostamento di una macchina virtuale da locale ad Azure con un disco non generalizzato][planning-guide-5.2.1] di questo documento.

Questa macchina virtuale non deve essere generalizzata e può essere caricata nello stato e nella forma in cui si trova dopo l'arresto sul lato locale. Lo stesso vale per i dischi rigidi virtuali aggiuntivi che non contengono sistemi operativi.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Caricamento di un disco rigido virtuale e conversione in un disco di Azure
Lo scopo di questo scenario è caricare un file VHD, con o senza sistema operativo incluso, e montarlo in una macchina virtuale come disco dati o usarlo come disco del sistema operativo. Il processo si articola in più passaggi

**PowerShell**

* Accedere alla sottoscrizione con *Connect-AzAccount*
* Impostare la sottoscrizione del contesto con *Set-AzContext* e il parametro SubscriptionId o SubscriptionName. Vedere <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Caricare il disco rigido virtuale in un account di Archiviazione di Azure con *Add-AzVhd*. Vedere <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* (Facoltativo) Creare un disco gestito dal disco rigido virtuale usando *New-AzDisk*. Vedere <https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk>
* Impostare il disco del sistema operativo di una nuova configurazione di macchina virtuale sul disco rigido virtuale o sul disco gestito con *Set-AzVMOSDisk*. Vedere <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
* Creare una nuova macchina virtuale dalla configurazione di macchina virtuale con *New-AzVM*. Vedere <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>
* Aggiungere un disco dati a una nuova macchina virtuale con *Add-AzVMDataDisk*. Vedere <https://docs.microsoft.com/powershell/module/az.compute/add-Azvmdatadisk>

**Interfaccia della riga di comando di Azure**

* Accedere alla sottoscrizione con *az login*
* Selezionare la propria sottoscrizione con *az account set --subscription `<subscription name or id`>*
* Caricare il disco rigido virtuale con *az storage blob upload*. Vedere [Uso dell'interfaccia della riga di comando di Azure con archiviazione di Azure][storage-azure-cli]
* (Facoltativo) Creare un disco gestito dal disco rigido virtuale usando *az disk create*. Vedere https://docs.microsoft.com/cli/azure/disk
* Creare una nuova VM specificando il disco rigido virtuale o il disco gestito caricato come disco del sistema operativo con *azure vm create* e il parametro *--attach-os-disk*
* Aggiungere un disco dati a una nuova VM con *az vm disk attach* e il parametro *--new*

**Modello**

* Caricare il disco rigido virtuale con PowerShell o con l'interfaccia della riga di comando di Azure
* (Facoltativo) Creare un disco gestito dal disco rigido virtuale con PowerShell, l'interfaccia della riga di comando di Azure o il portale di Azure
* Distribuire la VM con un modello JSON facendo riferimento al disco rigido virtuale, come mostrato in [questo modello JSON di esempio](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) o utilizzando i Managed Disks, come visualizzato [in questo esempio di modello JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Distribuzione di un'immagine di VM
Per caricare una macchina virtuale o un disco rigido virtuale esistente dalla rete locale, in modo da usarlo come immagine di macchina virtuale di Azure, è necessario che siano soddisfatti i requisiti elencati nel capitolo [Preparazione per la distribuzione di una macchina virtuale con un'immagine specifica del cliente per SAP][planning-guide-5.2.2] di questo documento.

* Usare *sysprep* in Windows o *waagent-deprovision* in Linux per generalizzare la macchina virtuale. Vedere [Documentazione tecnica su Sysprep](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) per Windows o [Come acquisire una macchina virtuale Linux da usare come modello di Resource Manager][capture-image-linux-step-2-create-vm-image] per Linux
* Accedere alla sottoscrizione con *Connect-AzAccount*
* Impostare la sottoscrizione del contesto con *Set-AzContext* e il parametro SubscriptionId o SubscriptionName. Vedere <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Caricare il disco rigido virtuale in un account di Archiviazione di Azure con *Add-AzVhd*. Vedere <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* (Facoltativo) Creare un'immagine del disco gestito dal disco rigido virtuale usando *New-AzImage*. Vedere <https://docs.microsoft.com/powershell/module/az.compute/new-Azimage>
* Impostare il disco del sistema operativo di una nuova configurazione di VM sul
  * disco rigido virtuale usando *Set-AzVMOSDisk -SourceImageUri -CreateOption fromImage*. Vedere <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
  * immagine del disco gestito usando *Set-AzVMSourceImage*. Vedere <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmsourceimage>
* Creare una nuova macchina virtuale dalla configurazione di macchina virtuale con *New-AzVM*. Vedere <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>

**Interfaccia della riga di comando di Azure**

* Usare *sysprep* in Windows o *waagent-deprovision* in Linux per generalizzare la macchina virtuale. Vedere [Documentazione tecnica su Sysprep](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) per Windows o [Come acquisire una macchina virtuale Linux da usare come modello di Resource Manager][capture-image-linux-step-2-create-vm-image] per Linux
* Accedere alla sottoscrizione con *az login*
* Selezionare la propria sottoscrizione con *az account set --subscription `<subscription name or id`>*
* Caricare il disco rigido virtuale con *az storage blob upload*. Vedere [Uso dell'interfaccia della riga di comando di Azure con archiviazione di Azure][storage-azure-cli]
* (Facoltativo) Creare un'immagine del disco gestito dal disco rigido virtuale usando *az image create*. Vedere https://docs.microsoft.com/cli/azure/image
* Creare una nuova VM specificando l’immagine del disco rigido virtuale o del disco gestito caricato come disco del sistema operativo con *azure vm create* e il parametro *--image*

**Modello**

* Usare *sysprep* in Windows o *waagent-deprovision* in Linux per generalizzare la macchina virtuale. Vedere [Documentazione tecnica su Sysprep](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) per Windows o [Come acquisire una macchina virtuale Linux da usare come modello di Resource Manager][capture-image-linux-step-2-create-vm-image] per Linux
* Caricare il disco rigido virtuale con PowerShell o con l'interfaccia della riga di comando di Azure
* (Facoltativo) Creare un'immagine del disco gestito dal disco rigido virtuale con PowerShell, l'interfaccia della riga di comando di Azure o il portale di Azure
* Distribuire la VM con un modello JSON facendo riferimento all’immagine del disco rigido virtuale, come mostrato in [questo modello JSON di esempio](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) o utilizzando l’immagine del disco gestito, come visualizzato [in questo esempio di modello JSON](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>Download dei dischi rigidi virtuali o dei dischi gestiti in locale
L'infrastruttura distribuita come servizio (IaaS) di Azure non consente solo di caricare dischi rigidi virtuali e sistemi SAP, ma anche di spostare i sistemi SAP da Azure ai sistemi locali.

Durante il download, i dischi rigidi virtuali o i Managed Disks non possono essere attivi. Anche quando si scaricano i dischi montati sulle macchine virtuali, la macchina virtuale deve essere arrestata e deallocata. Se si vuole scaricare solo il contenuto del database da usare per impostare un nuovo sistema locale e se si vuole che il sistema in Azure resti operativo durante il download e la configurazione del nuovo sistema, è possibile evitare un lungo tempo di inattività eseguendo un backup compresso del database in un disco e scaricando solo questo disco invece della macchina virtuale di base del sistema operativo.

#### <a name="powershell"></a>PowerShell

* Scaricando un disco gestito è necessario prima di tutto accedere al BLOB sottostante del disco gestito. Successivamente, si può copiare il BLOB sottostante su un nuovo account di archiviazione e scaricare il BLOB da questo account di archiviazione.

  ```powershell
  $access = Grant-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

* Scaricando un disco rigido virtuale dopo che il sistema SAP è stato arrestato e la macchina virtuale è stata arrestata, è possibile usare il cmdlet di PowerShell `Save-AzVhd` nella destinazione locale per scaricare nuovamente i dischi rigidi virtuali nell'ambiente locale. Per farlo, è necessario l'URL del disco rigido virtuale, che si trova nella "sezione Archiviazione" del portale di Azure, raggiungibile dall'account di archiviazione e dal contenitore di archiviazione in cui è stato creato il disco rigido virtuale, ed è necessario sapere dove copiare il disco rigido virtuale.

  A questo punto, è possibile usare il comando definendo il parametro SourceUri come URL del disco rigido virtuale per il download e LocalFilePath come percorso fisico del disco rigido virtuale, includendo il relativo nome. Il comando può avere un aspetto simile al seguente:

  ```powershell
  Save-AzVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Per altri dettagli sul cmdlet Save-AzVhd, vedere qui <https://docs.microsoft.com/powershell/module/az.compute/save-Azvhd>.

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
* Scaricando un disco gestito è necessario prima di tutto accedere al BLOB sottostante del disco gestito. Successivamente, si può copiare il BLOB sottostante su un nuovo account di archiviazione e scaricare il BLOB da questo account di archiviazione.

  ```azurecli
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

* Scaricando un disco rigido virtuale dopo che il sistema SAP è stato arrestato e la macchina virtuale è stata arrestata, è possibile usare il comando dell'interfaccia della riga di comando di Azure `_azure storage blob download_` nella destinazione locale per scaricare nuovamente i dischi rigidi virtuali nell'ambiente locale. Per farlo, sono necessari il nome e il contenitore del disco rigido virtuale, che si trovano nella "sezione Archiviazione" del portale di Azure, raggiungibile dall'account di archiviazione e dal contenitore di archiviazione in cui è stato creato il disco rigido virtuale, ed è necessario sapere dove copiare il disco rigido virtuale.

  A questo punto, è possibile usare il comando definendo i parametri blob e container del disco rigido virtuale per il download e la destinazione come percorso fisico di destinazione del disco rigido virtuale, includendo il relativo nome. Il comando può avere un aspetto simile al seguente:

  ```azurecli
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Trasferimento di VM e dischi in Azure

#### <a name="copying-sap-systems-within-azure"></a>Copia di sistemi SAP in Azure

È probabile che un sistema SAP o anche un server DBMS dedicato che supporta un livello dell'applicazione SAP sia costituito da diversi dischi, che contengono il sistema operativo con i file binari o i file di dati e di log del database SAP. Le funzionalità di copia e di salvataggio su disco locale dei dischi in Azure non hanno un meccanismo di sincronizzazione in grado di eseguire snapshot di più dischi in modo coerente. Quindi, lo stato dei dischi copiati o salvati, anche di quelli montati nella stessa macchina virtuale, risulta diverso. Ciò significa che, in uno scenario in cui sono presenti dati e file di log diversi contenuti nei vari dischi, il database risulterà incoerente.

**Conclusione: per copiare o salvare dischi che fanno parte della configurazione di un sistema SAP, è necessario arrestare sia il sistema SAP che la macchina virtuale distribuita. Solo a questo punto si può copiare o scaricare il set di dischi per creare una copia del sistema SAP in Azure o in locale.**

I dischi dati possono essere archiviati come file del disco rigido virtuale nell'account di archiviazione di Azure e possono essere collegati direttamente a una macchina virtuale o essere usati come immagine. In questo caso, il disco rigido virtuale viene copiato in un'altra posizione prima di essere collegato alla macchina virtuale. Il nome completo del file VHD deve essere univoco in Azure. Come già indicato in precedenza, il nome è un tipo di nome in tre parti simile a:

`http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>`

I dischi dati possono essere anche Managed Disks. In questo caso, il disco gestito utilizzato per creare un nuovo disco gestito prima che venga collegato alla macchina virtuale. Il nome del disco gestito deve essere univoco all'interno di un gruppo di risorse.

##### <a name="powershell"></a>PowerShell

È possibile usare i cmdlet di Azure PowerShell per copiare un disco rigido virtuale, come mostrato in [questo articolo][storage-powershell-guide-full-copy-vhd]. Per creare un nuovo disco gestito, usare New-AzDiskConfig e New-AzDisk, come illustrato nell'esempio seguente.

```powershell
$config = New-AzDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

È possibile usare l'interfaccia della riga di comando di Azure per copiare un disco rigido virtuale. Per creare un nuovo disco gestito, utilizzare *az disk create* come mostrato nell’esempio seguente.

```azurecli
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Strumenti di archiviazione di Azure

* <https://storageexplorer.com/>

Edizioni professionali degli strumenti di esplorazione di archiviazione di Azure sono disponibili qui:

* <https://www.cerebrata.com/>
* <https://clumsyleaf.com/products/cloudxplorer>

La copia di un disco rigido virtuale all'interno di un account di archiviazione è un processo che richiede solo pochi secondi, analogo a quello di un hardware SAN che crea snapshot con copia lenta e copia su scrittura. Dopo aver creato una copia del file VHD, è possibile collegarla a una macchina virtuale o usarla come immagine per collegare le copie del file VHD alle macchine virtuali.

##### <a name="powershell"></a>PowerShell

```powershell
# attach a vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a copy of the vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzVM

# attach a copy of the managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzVM
```

##### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli

# attach a vhd to a vm
az vm unmanaged-disk attach --resource-group <resource group name> --vm-name <vm name> --vhd-uri <path to vhd>

# attach a managed disk to a vm
az vm disk attach --resource-group <resource group name> --vm-name <vm name> --disk <managed disk id>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.

# attach a copy of a managed disk to a vm
az disk create --name <new disk name> --resource-group <resource group name> --location <location of target virtual machine> --source <source managed disk id>
az vm disk attach --disk <new disk name or managed disk id> --resource-group <resource group name> --vm-name <vm name> --caching <caching option> --lun <lun, for example 0>
```

#### <a name="copying-disks-between-azure-storage-accounts"></a><a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Copia di dischi tra account di archiviazione di Azure
Questa attività non può essere eseguita nel portale di Azure. È possibile usare i cmdlet di Azure PowerShell, l'interfaccia della riga di comando di Azure o un browser di archiviazione di terze parti. I cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando possono creare e gestire i BLOB, inclusa la possibilità di copiare in modo asincrono i BLOB tra gli account di archiviazione e tra le aree all'interno della sottoscrizione di Azure.

##### <a name="powershell"></a>PowerShell
È inoltre possibile copiare i dischi rigidi virtuali tra le sottoscrizioni. Per altre informazioni, vedere [questo articolo][storage-powershell-guide-full-copy-vhd].

Il flusso di base della logica dei cmdlet di PS è simile al seguente:

* Creare un contesto per l'account di archiviazione di **origine** con *New-AzStorageContext*. Vedere <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Creare un contesto per l'account di archiviazione di **destinazione** con *New-AzStorageContext*. Vedere <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Avviare la copia con

```powershell
Start-AzStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Controllare lo stato della copia in un ciclo con

```powershell
Get-AzStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Collegare il nuovo disco rigido virtuale a una macchina virtuale come descritto in precedenza.

Per alcuni esempi, vedere [questo articolo][storage-powershell-guide-full-copy-vhd].

##### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
* Avviare la copia con

```azurecli
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Controllare lo stato se la copia è ancora in un ciclo con

```azurecli
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Collegare il nuovo disco rigido virtuale a una macchina virtuale come descritto in precedenza.

### <a name="disk-handling"></a>Gestione del disco

#### <a name="vmdisk-structure-for-sap-deployments"></a><a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Struttura di macchina virtuale/disco per le distribuzioni SAP

In teoria, la gestione della struttura di una macchina virtuale e dei dischi associati dovrebbe essere semplice. Nelle installazioni locali, i clienti hanno sviluppato diverse procedure per strutturare un'installazione server.

* Un disco di base, che contiene il sistema operativo e tutti i file binari di DBMS e/o SAP. Dal 2015 marzo, questo disco può avere dimensioni fino a 1 TB anziché restrizioni precedenti che lo limitano a 127 GB.
* Uno o più dischi, che contengono il file di log del sistema di gestione di database SAP e il file di log dell'area di archiviazione temporanea del sistema di gestione di database, se supportato da tale sistema. Se i requisiti per le operazioni di I/O al secondo del log del database sono elevati, è necessario eseguire lo striping di più dischi per raggiungere il volume di operazioni di I/O al secondo richiesto.
* Numero di dischi contenenti uno o due file del database SAP e file di dati temporanei di DBMS, se supportati dal sistema DBMS.

![Configurazione di riferimento di una VM IaaS di Azure per SAP][planning-guide-figure-1300]


---
> ![Logo Windows.][Logo_Windows] Windows
>
> Molti clienti hanno adottato configurazioni in cui, ad esempio, i file binari SAP e DBMS non sono stati installati nell'unità c:\ in cui è stato installato il sistema operativo. I motivi alla base di questa configurazione sono diversi, ma, a uno sguardo più attento, questa scelta spesso risale a 10-15 anni fa ed è stata determinata dalle dimensioni ridotte delle unità e dalla necessità di ottenere altro spazio per gli aggiornamenti del sistema operativo. Oggi queste condizioni non si verificano quasi più, perché è possibile eseguire il mapping dell'unità c:\ in macchine virtuali o in dischi con volumi di grandi dimensioni. Per mantenere semplice la struttura delle distribuzioni, è consigliabile seguire il modello di distribuzione seguente per i sistemi SAP NetWeaver in Azure
>
> Il file di paging del sistema operativo Windows deve trovarsi nell'unità D: (disco non persistente)
>
> ![Logo Linux.][Logo_Linux] Linux
>
> Posizionare il file di scambio di Linux in /mnt/mnt/resource in Linux, come descritto in [questo articolo][virtual-machines-linux-agent-user-guide]. Il file di scambio può essere configurato nel file di configurazione di /etc/waagent.conf dell'agente Linux. Aggiungere o modificare le impostazioni seguenti:
>
>

```console
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Per attivare le modifiche, è necessario riavviare l'agente Linux con

```console
sudo service waagent restart
```

Per ulteriori informazioni sulle dimensioni consigliate per il file di scambio, vedere la nota SAP [1597355]

---
Il numero di dischi usati per i file di dati DBMS e il tipo di archiviazione di Azure in cui sono ospitati questi dischi devono essere determinati dai requisiti per le operazioni di I/O al secondo e dalla latenza richiesta. Le quote esatte sono descritte in [questo articolo (Linux)][virtual-machines-sizes-linux] e in [questo articolo (Windows)][virtual-machines-sizes-windows].

L'esperienza nelle distribuzioni SAP degli ultimi due anni ha insegnato alcune lezioni, che possono essere riepilogate come segue:

* Il traffico di operazioni di I/O al secondo per i vari file di dati non è sempre lo stesso perché i sistemi esistenti del cliente potrebbero avere file di dati di dimensioni diverse che rappresentano i relativi database SAP. Di conseguenza, è risultato più efficace l'uso di una configurazione RAID in più dischi per posizionare i file di dati definiti dai LUN. In alcune situazioni, in particolare con l'archiviazione standard di Azure, la frequenza di operazioni di I/O al secondo ha raggiunto la quota di un singolo disco nel log delle transazioni DBMS. In questi scenari è consigliabile usare l'archiviazione Premium o, in alternativa, aggregare più dischi dell'archiviazione Standard con una striscia software.

---
> ![Logo Windows.][Logo_Windows] Windows
>
> * [Performance best practices for SQL Server in Azure Virtual Machines][virtual-machines-sql-server-performance-best-practices] (Procedure consigliate sulle prestazioni per SQL Server nelle macchine virtuali di Azure)
>
> ![Logo Linux.][Logo_Linux] Linux
>
> * [Configurare RAID software in Linux][virtual-machines-linux-configure-raid]
> * [Configurare LVM in una macchina virtuale Linux in Azure][virtual-machines-linux-configure-lvm]
>
>

---
* L'archiviazione Premium mostra prestazioni notevolmente migliori, soprattutto per le scritture del log delle transazioni critiche. Per gli scenari SAP che devono offrire prestazioni paragonabili a quelle di produzione, si consiglia di adottare serie di macchine virtuali in grado di usare l'archiviazione Premium di Azure.

Tenere presente che il disco, che contiene il sistema operativo, e come consigliato, i file binari di SAP e il database (VM di base), non è più limitato a 127 GB. Ora può avere dimensioni fino a 1 TB. Questo spazio dovrebbe essere sufficiente per contenere tutti i file necessari, inclusi, ad esempio, i log dei processi batch SAP.

Per altri suggerimenti e dettagli, in particolare per le macchine virtuali di DBMS, vedere la [Guida alla distribuzione di DBMS][dbms-guide]

#### <a name="disk-handling"></a>Gestione del disco

Nella maggior parte degli scenari è necessario creare altri dischi per distribuire il database SAP nella macchina virtuale. Nel capitolo [Struttura di macchina virtuale/dischi per le distribuzioni SAP][planning-guide-5.5.1] di questo documento sono state presentate alcune considerazioni sul numero di dischi. Il portale di Azure consente di collegare e scollegare i dischi dopo aver distribuito una macchina virtuale di base. I dischi possono essere collegati o scollegati sia quando la macchina virtuale è attiva e in esecuzione che quando è arrestata. Quando si collega un disco, il portale di Azure offre di collegare un disco vuoto o un disco esistente che in quello specifico momento non è collegato ad altre macchine virtuali.

**Nota**: i dischi possono essere collegati in qualsiasi momento a una sola macchina virtuale.

![Collegare e scollegare dischi con l'archiviazione standard di Azure][planning-guide-figure-1400]

Durante la distribuzione di una nuova macchina virtuale, è possibile decidere se utilizzare i Managed Disks o collocare i dischi su account di archiviazione di Azure. Per utilizzare l’archiviazione Premium, è consigliabile utilizzare i Managed Disks.

Successivamente, è necessario decidere se creare un nuovo disco vuoto o se selezionare un disco esistente caricato in precedenza da collegare subito alla macchina virtuale.

**IMPORTANTE**: **NON** usare la memorizzazione nella cache dell'host con l'archiviazione Standard di Azure, ma mantenere l'impostazione predefinita NESSUNA. Con l'archiviazione Premium di Azure, è consigliabile abilitare la memorizzazione nella cache di lettura se la caratteristica I/O è generalmente di lettura, come il normale traffico I/O per i file di dati del database. Nel caso del file di log delle transazioni del database, la memorizzazione nella cache non è consigliata.

---
> ![Logo Windows.][Logo_Windows] Windows
>
> [Come collegare un disco dati nel portale di Azure][virtual-machines-linux-attach-disk-portal]
>
> Se i dischi sono collegati, è necessario accedere alla macchina virtuale per aprire Gestione disco di Windows. Se il montaggio automatico non è abilitato come consigliato nel capitolo [Impostazione del montaggio automatico per dischi collegati][planning-guide-5.5.3], il volume appena collegato deve essere portato online e inizializzato.
>
> ![Logo Linux.][Logo_Linux] Linux
>
> Se i dischi sono collegati, è necessario accedere alla macchina virtuale e inizializzare i dischi come descritto in [questo articolo][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
>
>

---
Se il nuovo disco è vuoto, è necessario anche formattarlo. Per la formattazione, in particolare per i file di dati e di log DBMS, si applicano le stesse raccomandazioni fornite per le distribuzioni bare metal di DBMS.

Un account di archiviazione di Azure non fornice risorse infinite in termini di volume di I/O, operazioni di I/O al secondo e volume di dati. In genere le macchine virtuali DBMS sono quelle più interessate dal problema. Potrebbe essere preferibile usare un account di archiviazione separato per ogni macchina virtuale se si hanno poche macchine virtuali con volumi I/O elevati da distribuire in modo da rimanere entro il limite del volume per l'account di archiviazione di Azure. In caso contrario, si deve capire come bilanciare queste macchine virtuali tra i diversi account di archiviazione senza raggiungere il limite per ogni singolo account di archiviazione. Altri dettagli sono riportati nella [Guida alla distribuzione di DBMS][dbms-guide]. Tenere presenti queste limitazioni anche per le macchine virtuali pure del server applicazioni SAP o per altre macchine virtuali che potrebbero richiedere altri dischi rigidi virtuali. Queste restrizioni non sono applicabili se si utilizza un disco gestito. Per utilizzare l’archiviazione Premium, è consigliabile utilizzare un disco gestito.

Un altro argomento rilevante per gli account di archiviazione riguarda la replica geografica dei dischi rigidi virtuali in un account di archiviazione. La replica geografica è abilitata o disabilitata al livello di account di archiviazione e non al livello di macchina virtuale. Se la replica geografica è abilitata, i dischi rigidi virtuali all'interno dell'account di archiviazione vengono replicati in un altro data center di Azure della stessa area. Prima di prendere una decisione, è necessario considerare la restrizione seguente:

La replica geografica di Azure funziona in locale in ogni disco rigido virtuale di una macchina virtuale e non replica gli I/O in ordine cronologico tra più dischi rigidi virtuali in una macchina virtuale. Di conseguenza, il disco rigido virtuale che rappresenta la macchina virtuale di base ed eventuali altri dischi rigidi virtuali collegati alla macchina virtuale vengono replicati in modo indipendente. Ciò significa che le modifiche nei vari dischi rigidi virtuali non vengono sincronizzate. Il fatto che gli I/O vengano replicati indipendentemente dall'ordine di scrittura implica che la replica geografica non è significativa per i server di database che contengono database distribuiti in più dischi rigidi virtuali. Oltre a DBMS, potrebbero esserci altre applicazioni in cui i processi scrivono o manipolano i dati in dischi rigidi virtuali diversi e in cui è importante mantenere l'ordine delle modifiche. Se questo è un requisito, la replica geografica in Azure non dovrebbe essere abilitata. Se si deve o si vuole usare la replica geografica solo in determinati set di macchine virtuali, è possibile suddividere le macchine virtuali e i dischi rigidi virtuali correlati in account di archiviazione diversi con replica geografica abilitata o disabilitata.

#### <a name="setting-automount-for-attached-disks"></a><a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Impostazione del montaggio automatico per dischi collegati
---
> ![Logo Windows.][Logo_Windows] Windows
>
> Per le macchine virtuali create da immagini o dischi personali, è necessario verificare ed eventualmente impostare il parametro di montaggio automatico. L'impostazione di questo parametro consente alla macchina virtuale di rimontare automaticamente le unità collegate/montate dopo un riavvio o una ridistribuzione in Azure.
> Il parametro è impostato per le immagini fornite da Microsoft in Azure Marketplace.
>
> Per impostare il montaggio automatico, vedere la documentazione relativa al file eseguibile della riga di comando diskpart.exe qui:
>
> * [Opzioni della riga di comando di DiskPart](/previous-versions/windows/it-pro/windows-xp/bb490893(v=technet.10))
> * [Montaggio automatico](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc753703(v=ws.11))
>
> La finestra della riga di comando di Windows deve essere aperta come amministratore.
>
> Se i dischi sono collegati, è necessario accedere alla macchina virtuale per aprire Gestione disco di Windows. Se il montaggio automatico non è abilitato come consigliato nel capitolo [Impostazione del montaggio automatico per dischi collegati][planning-guide-5.5.3], il volume appena collegato deve essere portato online e inizializzato.
>
> ![Logo Linux.][Logo_Linux] Linux
>
> È necessario inizializzare un disco vuoto appena collegato, come descritto in [questo articolo][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> È anche necessario aggiungere nuovi dischi a /etc/fstab.
>
>

---
### <a name="final-deployment"></a>Distribuzione finale

Per la distribuzione finale e la procedura precisa da applicare, in particolare per quanto riguarda la distribuzione dell'estensione di Azure per SAP, vedere la [Guida alla distribuzione][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Accesso ai sistemi SAP in esecuzione in macchine virtuali di Azure

Per gli scenari in cui si intende connettersi a questi sistemi SAP attraverso la rete Internet pubblica tramite l'interfaccia utente grafica SAP, è necessario applicare le procedure seguenti.

Più avanti in questo documento verrà illustrato un altro scenario importante, ovvero la connessione a sistemi SAP nelle distribuzioni cross-premise, che contengono una connessione da sito a sito (tunnel VPN) o la connessione Azure ExpressRoute tra i sistemi locali e Azure.

### <a name="remote-access-to-sap-systems"></a>Accesso remoto ai sistemi SAP

Con Azure Resource Manager, non esistono più endpoint predefiniti come nel modello classico precedente. Tutte le porte di una macchina virtuale Azure Resource Manager sono aperte, purché:

1. Non siano definiti gruppi di sicurezza di rete per la subnet o l'interfaccia di rete. Il traffico di rete verso le macchine virtuali di Azure sia protetto con i cosiddetti "Gruppi di sicurezza di rete". Per altre informazioni, vedere [Che cos'è un gruppo di sicurezza di rete][virtual-networks-nsg]
2. Non sia definito Azure Load Balancer per l'interfaccia di rete

Vedere la differenza di architettura tra il modello classico e ARM, come descritto in [questo articolo][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-over-the-internet"></a>Configurazione della connettività del sistema SAP e dell'interfaccia utente grafica SAP tramite Internet

Vedere questo articolo, che descrive i dettagli di questo argomento:

<https://docs.microsoft.com/archive/blogs/saponsqlserver/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure>

#### <a name="changing-firewall-settings-within-vm"></a>Modifica delle impostazioni del firewall all'interno di una macchina virtuale

Potrebbe essere necessario configurare il firewall nelle macchine virtuali per consentire il traffico in ingresso verso il sistema SAP.

---
> ![Logo Windows.][Logo_Windows] Windows
>
> Per impostazione predefinita, Windows Firewall all'interno di una macchina virtuale distribuita di Azure è attivato. A questo punto è necessario consentire l'apertura della porta SAP, in caso contrario l'interfaccia utente grafica SAP non potrà connettersi.
> A tale scopo, effettuare l'operazione seguente:
>
> * Aprire **Impostazioni avanzate** in Pannello di controllo\Sistema e Sicurezza\Windows Firewall.
> * Fare clic con il pulsante destro del mouse su Regole connessioni in entrata e scegliere **Nuova regola**.
> * Nella procedura guidata seguente scegliere di creare una nuova regola **Porta**.
> * Nel passaggio successivo della procedura guidata lasciare l'impostazione su TCP e digitare il numero della porta da aprire. L'ID dell'istanza SAP è 00, quindi verrà usato 3200. Se l'istanza ha un numero diverso, è necessario aprire la porta definita in precedenza in base al numero di istanza.
> * Nella parte successiva della procedura guidata lasciare selezionata l'opzione **Consenti connessione**.
> * Nel passaggio successivo della procedura guidata è necessario definire se la regola si applica al tipo di rete Dominio, Privato o Pubblico. Se necessario, modificarlo in base alle esigenze. Tuttavia, per la connessione con l'interfaccia utente grafica SAP dall'esterno attraverso la rete pubblica è necessario che la regola sia applicata alla rete pubblica.
> * Nell'ultimo passaggio della procedura guidata assegnare un nome alla regola e salvare premendo **Fine**.
>
> La regola viene applicata immediatamente.
>
> ![Definizione delle regole delle porte][planning-guide-figure-1600]
>
> ![Logo Linux.][Logo_Linux] Linux
>
> Le immagini di Linux in Azure Marketplace non abilitano il firewall iptables per impostazione predefinita e la connessione al sistema SAP dovrebbe funzionare. Se è stato abilitato iptables o un altro firewall, vedere la documentazione di iptables o del firewall usato per consentire il traffico TCP in ingresso alla porta 32xx, dove xx è il numero del sistema SAP.
>
>

---
#### <a name="security-recommendations"></a>Suggerimenti per la sicurezza

L'interfaccia utente grafica SAP non si connette immediatamente alle istanze di SAP (porta 32xx) in esecuzione, ma si connette prima al processo del server dei messaggi SAP (porta 36xx). In passato, la stessa porta era usata dal server dei messaggi per la comunicazione interna verso le istanze dell'applicazione. Per impedire la comunicazione accidentale dei server applicazioni locali con un server dei messaggi in Azure, le porte di comunicazione possono essere modificate. È consigliabile modificare la comunicazione interna tra il server dei messaggi SAP e le relative istanze dell'applicazione e un numero di porta diverso in sistemi clonati da sistemi locali, ad esempio un clone di sviluppo per i test di progetto e così via. Questa operazione può essere eseguita con il parametro di profilo predefinito:

> rdisp/msserv_internal
>
>

come documentato in [Impostazioni di sicurezza per il server messaggi SAP](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)


### <a name="single-vm-with-sap-netweaver-demotraining-scenario"></a><a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Scenario di formazione/dimostrativo di una singola VM con SAP NetWeaver

![Esecuzione di singoli sistemi dimostrativi SAP delle macchine virtuali con gli stessi nomi di macchina virtuale, isolati nei servizi cloud di Azure][planning-guide-figure-1700]

In questo scenario viene implementato un tipico scenario di sistema di training/demo in cui l'intero scenario di training/demo è contenuto all'interno di un'unica macchina virtuale. Si presuppone che la distribuzione venga eseguita con i modelli di immagine di VM. Si suppone inoltre che più macchine virtuali di formazione/dimostrative debbano essere distribuite con macchine virtuali con lo stesso nome. I sistemi di training complessivamente non hanno la connettività agli asset locali e sono l'opposto di una distribuzione ibrida.

Si presuppone che sia stata creata un'immagine di macchina virtuale come descritto nelle sezioni del capitolo [Preparazione di macchine virtuali con SAP per Azure][planning-guide-5.2] di questo documento.

La sequenza di eventi per implementare lo scenario è simile alla seguente:

##### <a name="powershell"></a>PowerShell

* Creare un nuovo gruppo di risorse per ogni scenario di formazione/dimostrativo

```powershell
$rgName = "SAPERPDemo1"
New-AzResourceGroup -Name $rgName -Location "North Europe"
```

* Creare un nuovo account di archiviazione, se non si vogliono utilizzare i Managed Disks

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Creare una nuova rete virtuale per ogni scenario di formazione/dimostrativo consentire l'utilizzo dello stesso nome host e degli indirizzi IP. La rete virtuale è protetta da un gruppo di sicurezza di rete che consente solo il traffico verso la porta 3389 per abilitare l'accesso al Desktop remoto e verso la porta 22 per SSH.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Creare un nuovo indirizzo IP pubblico da usare per accedere alla macchina virtuale da Internet

```powershell
# Create a public IP address with a DNS name
$pip = New-AzPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Creare una nuova interfaccia di rete per la macchina virtuale

```powershell
# Create a new Network Interface
$nic = New-AzNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Creare una macchina virtuale. Per questo scenario, tutte le macchine virtuali avranno lo stesso nome. Anche il SID di SAP delle istanze di SAP NetWeaver in queste macchine virtuali sarà lo stesso. Nel gruppo di risorse di Azure il nome della macchina virtuale deve essere univoco, ma in diversi gruppi di risorse di Azure è possibile eseguire macchine virtuali con lo stesso nome. L'account predefinito 'Administrator' di Windows o 'root' di Linux non è valido. È quindi necessario definire un nuovo nome utente dell'amministratore con una password. Devono essere definite anche le dimensioni della macchina virtuale.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Facoltativamente, aggiungere altri dischi e ripristinare il contenuto necessario. Tutti i nomi dei BLOB (URL per i BLOB) devono essere univoci all'interno di Azure.

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzVM

# Optional: Attach additional Managed Disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzVM
```

##### <a name="cli"></a>CLI

L'esempio di codice seguente può essere usato in Linux. Per Windows, usare PowerShell come descritto in precedenza oppure adattare l'esempio per usare %rgName% anziché $rgName e impostare la variabile di ambiente con il comando di Windows *set*.

* Creare un nuovo gruppo di risorse per ogni scenario di formazione/dimostrativo

```azurecli
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Creare un nuovo account di archiviazione.

```azurecli
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Creare una nuova rete virtuale per ogni scenario di formazione/dimostrativo consentire l'utilizzo dello stesso nome host e degli indirizzi IP. La rete virtuale è protetta da un gruppo di sicurezza di rete che consente solo il traffico verso la porta 3389 per abilitare l'accesso al Desktop remoto e verso la porta 22 per SSH.

```azurecli
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Creare un nuovo indirizzo IP pubblico da usare per accedere alla macchina virtuale da Internet

```azurecli
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Creare una nuova interfaccia di rete per la macchina virtuale

```azurecli
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Creare una macchina virtuale. Per questo scenario, tutte le macchine virtuali avranno lo stesso nome. Anche il SID di SAP delle istanze di SAP NetWeaver in queste macchine virtuali sarà lo stesso. Nel gruppo di risorse di Azure il nome della macchina virtuale deve essere univoco, ma in diversi gruppi di risorse di Azure è possibile eseguire macchine virtuali con lo stesso nome. L'account predefinito 'Administrator' di Windows o 'root' di Linux non è valido. È quindi necessario definire un nuovo nome utente dell'amministratore con una password. Devono essere definite anche le dimensioni della macchina virtuale.

```azurecli
#####
# Create virtual machines using storage accounts
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password

#####
# Create virtual machines using Managed Disks
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
```

```azurecli
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd> --authentication-type password

#####
# Create a new virtual machine with a Managed Disk Image
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id> --authentication-type password
```

* Facoltativamente, aggiungere altri dischi e ripristinare il contenuto necessario. Tutti i nomi dei BLOB (URL per i BLOB) devono essere univoci all'interno di Azure.

```azurecli
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Modello

È possibile usare i modelli di esempio nel repository Azure-QuickStart-templates su GitHub.

* [Macchina virtuale semplice di Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Macchina virtuale semplice di Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Macchina virtuale dall'immagine](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Implementare un set di macchine virtuali che comunicano all'interno di Azure

Questo scenario non ibrido è uno scenario tipico a scopo di training e dimostrativo in cui il software che rappresenta lo scenario demo/di training viene distribuito in più macchine virtuali. I vari componenti installati nelle diverse macchine virtuali devono comunicare tra loro. Anche in questo scenario non è necessaria alcuna comunicazione di rete locale o scenario cross-premise.

Questo scenario è un'estensione dell'installazione descritta nel capitolo [Scenario di formazione/dimostrativo di una singola macchina virtuale con SAP NetWeaver][planning-guide-7.1] di questo documento. In questo caso, verranno aggiunte più macchine virtuali a un gruppo di risorse esistente. Nell'esempio seguente lo scenario di formazione è costituito da una macchina virtuale ASCS/SCS SAP, da una macchina virtuale che esegue un sistema DBMS e da una macchina virtuale di un'istanza del server applicazioni SAP.

Prima di compilare questo scenario, è necessario considerare le impostazioni di base come descritto nello scenario precedente.

#### <a name="resource-group-and-virtual-machine-naming"></a>Denominazione dei gruppi di risorse e delle macchine virtuali

Tutti i nomi dei gruppi di risorse devono essere univoci. Sviluppare il proprio schema di denominazione delle risorse, ad esempio `<rg-name`>-suffisso.

Il nome delle macchine virtuali deve essere univoco all'interno del gruppo di risorse.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Configurare la rete per la comunicazione tra le diverse macchine virtuali

![Set di VM all'interno di una rete virtuale di Azure][planning-guide-figure-1900]

Per impedire conflitti di denominazione con cloni dello stesso scenario di formazione/dimostrativo, è necessario creare una rete virtuale di Azure per ogni scenario. La risoluzione dei nomi DNS verrà fornita da Azure oppure è possibile configurare il proprio server DNS all'esterno di Azure (l'argomento non verrà ulteriormente trattato in questo documento). In questo scenario non viene configurato alcun DNS personalizzato. Per tutte le macchine virtuali all'interno di una rete virtuale di Azure viene abilitata la comunicazione usando nomi host.

I motivi che giustificano la separazione degli scenari di formazione o dimostrativi in base alle reti virtuali e non solo ai gruppi di risorse potrebbero essere i seguenti:

* Il panorama applicativo SAP, così com'è configurato, richiede un proprio AP/OpenLDAP e un server di dominio deve far parte di ciascuno degli scenari.
* Il panorama applicativo SAP, così com'è configurato, include componenti che devono interagire con indirizzi IP fissi.

Altri dettagli sulle reti virtuali di Azure e su come definirle sono disponibili in [questo articolo][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Distribuzione di VM SAP con connettività di rete aziendale (cross-premise)

Si esegue un panorama applicativo SAP e si vuole suddividere la distribuzione tra bare metal per i server DBMS di fascia alta, ambienti virtualizzati locali per i livelli dell'applicazione e sistemi SAP configurati a 2 livelli di dimensioni inferiori e IaaS di Azure. Il presupposto di base è che i sistemi SAP all'interno di un panorama applicativo SAP debbano comunicare tra loro e con molti altri componenti software distribuiti nell'azienda, indipendentemente dalla propria modalità di distribuzione. Inoltre, non devono essere presenti differenze introdotte dalla modalità di distribuzione per l'utente finale che si connette con SAP GUI o altre interfacce. Queste condizioni possono essere soddisfatte solo quando i servizi Active Directory/Open LDAP e DNS locali vengono estesi ai sistemi di Azure usando la connettività da sito a sito/multisito o connessioni private come Azure ExpressRoute.



### <a name="scenario-of-an-sap-landscape"></a>Scenario di un panorama applicativo SAP

Lo scenario cross-premise o ibrido può essere descritto approssimativamente come nella rappresentazione grafica seguente:

![Connettività da sito a sito tra asset locali e di Azure][planning-guide-figure-2100]

Il requisito minimo è l'uso di protocolli di comunicazione protetta, ad esempio SSL/TLS per l'accesso dal browser o connessioni basate su VPN per l'accesso dal sistema ai servizi di Azure. Il presupposto è che le aziende gestiscano la connessione VPN tra la propria rete aziendale e Azure in modo diverso. Alcune aziende possono semplicemente aprire tutte le porte, altre potrebbero adottare un approccio più selettivo riguardo alle scelta delle porte da aprire e così via.

Le porte di comunicazione SAP tipiche sono elencate nella tabella seguente. Sostanzialmente, è sufficiente aprire la porta del gateway SAP.

<!-- sapms is prefix of a SAP service name and not a spelling error -->

| Service | Nome della porta | Esempio `<nn`> = 01 | Intervallo predefinito (min-max) | Comment |
| --- | --- | --- | --- | --- |
| Dispatcher |sapdp`<nn>` vedere * |3201 |3200 - 3299 |Dispatcher di SAP, usato dall'interfaccia utente grafica di SAP per Windows e Java |
| Server messaggi |sapms`<sid`> vedere ** |3600 |sapms libero`<anySID`> |sid = SAP-System-ID |
| Gateway |sapgw`<nn`> vedere * |3301 |libero |Gateway SAP, usato per le comunicazioni CPIC e RFC |
| Router SAP |sapdp99 |3299 |libero |Solo i nomi del servizio CI (istanza centrale) possono essere riassegnati in /etc/services specificando un valore arbitrario dopo l'installazione. |

*) nn = numero istanza SAP

\**) sid = SAP-System-ID

Informazioni più dettagliate sulle porte necessarie per i diversi prodotti o servizi SAP in base ai prodotti SAP sono disponibili qui: <https://scn.sap.com/docs/DOC-17124>.
Questo documento fornisce informazioni che consentono di aprire nel dispositivo VPN le porte dedicate necessarie per gli scenari e i prodotti SAP specifici.

Quando si distribuiscono macchine virtuali in tale scenario è possibile applicare altre misure di sicurezza creando un [gruppo di sicurezza di rete][virtual-networks-nsg] per definire le regole di accesso.



#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Stampa su una stampante di rete locale dall'istanza SAP in Azure

##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Stampa su TCP/IP in uno scenario cross-premise

La configurazione delle stampanti di rete locali basate su TCP/IP in una VM di Azure è complessivamente uguale a quella della rete aziendale, presupponendo che sia disponibile un tunnel VPN da sito a sito o una connessione ExpressRoute.

---
> ![Logo Windows.][Logo_Windows] Windows
>
> A tale scopo, effettuare l'operazione seguente:
>
> * Alcune stampanti di rete sono dotate di una configurazione guidata che semplifica la configurazione della stampante in una VM di Azure. Se non è stato distribuito alcun software di procedura guidata con la stampante, la modalità manuale di configurazione della stampante prevede la creazione di una nuova porta stampante TCP/IP.
> * Aprire Pannello di controllo -> Dispositivi e stampanti -> Aggiungi stampante
> * Scegliere Aggiungi una stampante usando un nome host o un indirizzo TCP/IP
> * Digitare l'indirizzo IP della stampante
> * Porta stampante standard 9100
> * Se necessario, installare manualmente il driver della stampante appropriato.
>
> ![Logo Linux.][Logo_Linux] Linux
>
> * Come per Windows, seguire la procedura standard di installazione di una stampante di rete.
> * Seguire le guide pubbliche di Linux per [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) o [Red Hat e Oracle Linux](https://access.redhat.com/documentation/red_hat_enterprise_linux/6/html/deployment_guide/sec-printer_configuration) su come aggiungere una stampante.
>
>

---
![Stampa di rete][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Stampante basata su host su SMB (stampante condivisa) in uno scenario cross-premise

Per impostazione predefinita le stampanti basate su host non sono compatibili con la rete. Tuttavia, una stampante basata su host può essere condivisa tra computer in una rete, a condizione che sia connessa a un computer acceso. Connettere la rete aziendale da sito a sito o con ExpressRoute e condividere la stampante locale. Il protocollo SMB usa NetBIOS anziché DNS come nome del servizio. Il nome host NetBIOS può essere diverso dal nome host DNS. Il caso standard prevede che il nome host NetBIOS e il nome host DNS siano identici. L'uso del dominio DNS non ha senso nello spazio dei nomi NetBIOS. Di conseguenza, il nome host DNS completo costituito dal nome host DNS e dal dominio DNS non deve essere usato nello spazio dei nomi NetBIOS.

La condivisione della stampante è identificata da un nome univoco nella rete:

* Nome host dell'host SMB (sempre necessario).
* Nome della condivisione (sempre necessario).
* Nome del dominio se la condivisione della stampante non si trova nello stesso dominio del sistema SAP.
* Inoltre, un nome utente e una password potrebbero essere necessari per accedere alla condivisione della stampante.

Procedura:

---
> ![Logo Windows.][Logo_Windows] Windows
>
> Condividere la stampante locale.
> Nella macchina virtuale di Azure, aprire Esplora risorse e digitare il nome di condivisione della stampante.
> Un'installazione guidata stampante illustrerà in modo dettagliato il processo di installazione.
>
> ![Logo Linux.][Logo_Linux] Linux
>
> Di seguito sono riportati alcuni esempi di documentazione sulla configurazione delle stampanti di rete in Linux o sull'inclusione di un capitolo relativo alla stampa in Linux. La configurazione è uguale a quella di una macchina virtuale Linux di Azure, a condizione che la macchina virtuale faccia parte di una VPN:
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL o Oracle Linux <https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html-single/system_administrators_guide/index#sec-Starting_Print_Settings_Config>
>
>

---
##### <a name="usb-printer-printer-forwarding"></a>Stampante USB (inoltro stampante)

In Azure la possibilità di Servizi Desktop remoto di fornire agli utenti l'accesso ai propri dispositivi stampante locale in una sessione remota non è disponibile.

---
> ![Logo Windows.][Logo_Windows] Windows
>
> Altri dettagli sulla stampa con Windows sono disponibili qui: <https://technet.microsoft.com/library/jj590748.aspx>.
>
>

---
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integrazione dei sistemi SAP in Azure in Correction and Transport System (TMS) cross-premise

SAP Change and Transport System (TMS) deve essere configurato per esportare e importare la richiesta di trasporto tra sistemi nel panorama applicativo. Si presuppone che le istanze di sviluppo di un sistema SAP (DEV) si trovino in Azure, mentre il controllo di qualità (QA) e i sistemi di produzione (PRD) siano locali. Inoltre, si presuppone che sia presente una directory di trasporto centrale.

##### <a name="configuring-the-transport-domain"></a>Configurazione del dominio di trasporto

Configurare il dominio di trasporto sul sistema designato come controller di dominio di trasporto, come descritto in [Configurazione del controller del dominio di trasporto](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). Verrà creato un utente di sistema TMSADM e sarà generata la destinazione RFC necessaria. È possibile controllare queste connessioni RFC usando la transazione SM59. La risoluzione dei nomi host deve essere abilitata nel dominio di trasporto.

Procedura:

* In questo scenario il sistema QAS locale sarà il controller di dominio di CTS. Chiamare la transazione STMS. Viene visualizzata la finestra di dialogo TMS. Viene visualizzata la finestra di dialogo Configure Transport Domain (questa finestra di dialogo viene visualizzata solo se non è stato ancora configurato un dominio di trasporto).
* Assicurarsi che l'utente TMSADM creato automaticamente sia autorizzato (SM59 -> ABAP Connection -> TMSADM@E61.DOMAIN_E61 -> Details -> Utilities(M) -> Authorization Test). La schermata iniziale della transazione STMS dovrebbe indicare che il sistema SAP ora sta funzionando come controller di dominio di trasporto, come illustrato di seguito:

![Schermata iniziale della transazione STMS sul controller di dominio][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Inclusione dei sistemi SAP nel dominio di trasporto

La sequenza di inclusione di un sistema SAP in un dominio di trasporto è simile alla seguente:

* Nel sistema DEV in Azure passare al sistema di trasporto (Client 000) e chiamare la transazione STMS. Scegliere Other Configuration dalla finestra di dialogo e continuare con Include System in Domain. Specificare il controller di dominio come host di destinazione (vedere[Inclusione di sistemi SAP nel dominio di trasporto](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). Il sistema ora è in attesa di essere incluso nel dominio di trasporto.
* Per motivi di sicurezza, è necessario tornare al controller di dominio per confermare la richiesta. Scegliere System Overview e Approve of the waiting system. Quindi, confermare la richiesta; la configurazione verrà distribuita.

Il sistema SAP ora contiene le informazioni necessarie su tutti gli altri sistemi SAP nel dominio di trasporto. Allo stesso tempo, i dati degli indirizzi del nuovo sistema SAP vengono inviati a tutti gli altri sistemi SAP e il sistema SAP viene immesso nel profilo di trasporto del programma di controllo del trasporto. Verificare il corretto funzionamento di RFC e dell'accesso alla directory di trasporto del dominio.

Continuare con la configurazione del sistema di trasporto come di consueto seguendo la descrizione fornita nella documentazione [Sistema di trasporto e modifica](https://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm).

Procedura:

* Assicurarsi che il sistema STMS locale sia configurato correttamente.
* Assicurarsi che il nome host del controller di dominio di trasporto possa essere risolto dalla macchina virtuale in Azure e viceversa.
* Chiamare la transazione STMS -> Other Configuration -> Include System in Domain.
* Confermare la connessione nel sistema TMS locale.
* Configurare le route, i gruppi e i livelli di trasporto, come di consueto.

Negli scenari cross-premise connessi da sito a sito, la latenza tra sistema locale e Azure può essere ancora significativa. Se si segue la sequenza del trasporto di oggetti nei sistemi di sviluppo e test fino alla produzione o si valuta la possibilità di applicare trasporti o pacchetti di supporto nei vari sistemi, si comprende che, in base alla posizione della directory di trasporto centrale, per alcuni dei sistemi si verificherà una latenza elevata della lettura o scrittura dei dati nella directory di trasporto centrale. Si tratta di una situazione simile a quella delle configurazioni del panorama applicativo SAP, dove diversi sistemi sono distribuiti in diversi data center posizionati a una notevole distanza l'uno dall'altro.

Per ovviare a problemi come la latenza e fare in modo che i sistemi eseguano rapidamente le operazioni di lettura o scrittura verso o dalla directory di trasporto, è possibile configurare due domini di trasporto STMS (uno da usare in locale e l'altro da usare con i sistemi in Azure) e collegarli. Consultare questa documentazione, che illustra i principi alla base di questo concetto in SAP TMS: <https://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm> .

Procedura:

* Configurare un dominio di trasporto in ogni posizione (in locale e in Azure) usando la transazione STMS <https://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Collegare i domini con un collegamento di dominio e verificare che siano collegati.
  <https://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Distribuire la configurazione nel sistema collegato.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>Traffico RFC tra le istanze SAP in Azure e locali (cross-premise)

Traffico RFC tra i sistemi, che sono locali e devono funzionare in Azure. Per configurare una connessione, chiamare la transazione SM59 in un sistema di origine in cui è necessario definire una connessione RFC verso il sistema di destinazione. La configurazione è simile a quella standard di una connessione RFC.

Si presuppone che nello scenario cross-premise le macchine virtuali, che eseguono sistemi SAP che devono comunicare tra loro, si trovino nello stesso dominio. Quindi, la configurazione di una connessione RFC tra sistemi SAP non è diversa dalla procedura di configurazione e dagli input degli scenari locali.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Accesso a condivisioni file locali da istanze SAP che si trovano in Azure e viceversa

Le istanze di SAP che si trovano in Azure devono accedere alle condivisioni file disponibili nell'ambiente aziendale. Inoltre, le istanze di SAP locali devono accedere alle condivisioni file che si trovano in Azure. Per abilitare le condivisioni file, è necessario configurare le autorizzazioni e le opzioni di condivisione nel sistema locale. Assicurarsi di aprire le porte della connessione VPN o ExpressRoute tra Azure e il data center.

## <a name="supportability"></a>Facilità di supporto

### <a name="azure-extension-for-sap"></a><a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Estensione di Azure per SAP

Per inserire una parte delle informazioni sull'infrastruttura di Azure dei sistemi SAP cruciali nelle istanze dell'agente host SAP, installate nelle macchine virtuali, è necessario installare un'estensione di Azure (macchina virtuale) per SAP per le macchine virtuali distribuite. Considerato che le esigenze di SAP erano specifiche per le applicazioni SAP, Microsoft ha deciso di non implementare genericamente la funzionalità richiesta in Azure, ma di lasciare che siano i clienti a occuparsi della distribuzione delle configurazioni e delle estensioni delle macchine virtuali necessarie per le loro macchine virtuali in esecuzione in Azure. Tuttavia, la gestione della distribuzione e del ciclo di vita dell'estensione della macchina virtuale di Azure verrà, nella maggior parte dei casi, automatizzata da Azure.

#### <a name="solution-design"></a>Progettazione della soluzione

La soluzione sviluppata per consentire all'agente host SAP di ottenere le informazioni necessarie si basa sull'architettura del framework dell'agente di macchine virtuali e delle estensioni delle macchine virtuali di Azure. L'idea alla base del framework dell'agente VM e delle estensioni VM di Azure è di consentire l'installazione delle applicazioni software disponibili nella raccolta di estensioni VM di Azure all'interno di una VM. Il principio alla base di questo concetto è di consentire (in casi come l'estensione di Azure per SAP), la distribuzione di funzionalità speciali in una macchina virtuale e la configurazione del software in fase di distribuzione.

L'agente VM di Azure che consente la gestione di specifiche estensioni VM di Azure all'interno della VM viene inserito nelle VM di Windows per impostazione predefinita durante la creazione di VM nel portale di Azure. Nel caso di SUSE o Red Hat o Oracle Linux, l'agente VM fa già parte dell'immagine di Azure Marketplace. Nel caso in cui si voglia caricare una macchina virtuale Linux da locale in Azure, è necessario installare manualmente l'agente di macchine virtuali.

I blocchi predefiniti di base della soluzione per fornire informazioni sull'infrastruttura di Azure all'agente host SAP in Azure sono simili ai seguenti:

![Componenti dell'estensione di Microsoft Azure][planning-guide-figure-2400]

Come illustrato nel diagramma a blocchi precedente, una parte della soluzione è ospitata nell'immagine di macchina virtuale di Azure e nella raccolta di estensioni di Azure, che è un archivio replicato globalmente gestito da Azure Operations. È responsabilità del team congiunto SAP/MS impegnato nell'implementazione di SAP collaborare con Azure Operations alla pubblicazione di nuove versioni dell'estensione di Azure per SAP.

Quando si distribuisce una nuova macchina virtuale Windows, l'Agente di macchine virtuali di Azure viene aggiunto automaticamente alla macchina virtuale. Questo agente ha la funzione di coordinare il caricamento e la configurazione delle estensioni di Azure delle macchine virtuali. Per le macchine virtuali Linux, l'agente di macchine virtuali di Azure fa già parte dell'immagine del sistema operativo di Azure Marketplace.

È presente, tuttavia, un passaggio che deve ancora essere eseguito dal cliente, cioè l'abilitazione e la configurazione della raccolta delle prestazioni. Il processo correlato alla configurazione viene automatizzato con uno script di PowerShell o un comando dell'interfaccia della riga di comando. Lo script di PowerShell può essere scaricato nello Script Center di Microsoft Azure come descritto nella [Guida alla distribuzione][deployment-guide].

L'architettura complessiva dell'estensione di Azure per SAP ha un aspetto simile al seguente:

![Estensione di Azure per SAP ][planning-guide-figure-2500]

**Per la procedura dettagliata sull'uso di questi cmdlet di PowerShell o dei comandi dell'interfaccia della riga di comando durante le distribuzioni, seguire le istruzioni fornite nella [Guida alla distribuzione][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integrazione di un'istanza SAP ubicata in Azure in SAProuter

Le istanze SAP in esecuzione in Azure devono essere accessibili anche da SAProuter.

![Connessione di rete SAP-router][planning-guide-figure-2600]

SAProuter abilita la comunicazione TCP/IP tra i sistemi partecipanti se non esiste alcuna connessione IP diretta. Questo offre il vantaggio di non richiedere alcuna connessione end-to-end tra i partner di comunicazione a livello di rete. SAProuter è in ascolto sulla porta 3299 per impostazione predefinita.
Per connettere le istanze di SAP con SAProuter è necessario assegnare il nome di stringa e dell'host SAProuter a ogni tentativo di connessione.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver AS Java

Fino a questo momento ci si è concentrati su SAP NetWeaver in generale o sullo stack ABAP di SAP NetWeaver. In questa piccola sezione sono riportate considerazioni specifiche per lo stack SAP Java. Una delle più importanti applicazioni basate esclusivamente su SAP NetWeaver Java è SAP Enterprise Portal. Altre applicazioni basate su SAP NetWeaver come SAP PI e SAP Solution Manager usano gli stack SAP NetWeaver ABAP e Java, quindi è sicuramente necessario considerare alcuni aspetti specifici correlati anche allo stack SAP NetWeaver Java.

### <a name="sap-enterprise-portal"></a>SAP Enterprise Portal

L'installazione di un portale SAP in una macchina virtuale di Azure non è diversa da un'installazione locale se si distribuiscono scenari cross-premise. Dato il DNS viene effettuato in locale, le impostazioni delle porte delle singole istanze possono essere eseguite in base alla configurazione locale. Le raccomandazioni e le restrizioni descritte in precedenza in questo documento si applicano ad applicazioni come SAP Enterprise Portal o allo stack SAP NetWeaver Java in generale.

![SAP Portal esposto][planning-guide-figure-2700]

Uno scenario di distribuzione speciale di alcuni clienti riguarda l'esposizione diretta di SAP Enterprise Portal a Internet mentre l'host della macchina virtuale è connesso alla rete aziendale con tunnel VPN da sito a sito o ExpressRoute. Per tale scenario, è necessario assicurarsi che determinate porte siano aperte e non bloccate da un firewall o da un gruppo di sicurezza di rete.

L'URI del portale iniziale è http(s):`<Portalserver`>:5XX00/irj. Il formato del valore della porta è documentato da SAP in <https://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>.

![Configurazione dell'endpoint][planning-guide-figure-2800]

Se si vuole personalizzare l'URL e/o le porte di SAP Enterprise Portal, consultare la documentazione seguente:

* [Modificare l'URL del portale](https://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Modificare i numeri di porta predefiniti e i numeri di porta del portale](https://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Disponibilità elevata e ripristino di emergenza per SAP NetWeaver in esecuzione in macchine virtuali di Azure

### <a name="definition-of-terminologies"></a>Definizione dei termini

L'espressione **disponibilità elevata** è in genere correlata a un set di tecnologie che riduce al minimo le interruzioni IT assicurando la continuità aziendale dei servizi IT tramite componenti ridondanti, a tolleranza di errore o protetti con failover all'interno dello **stesso** data center. Nel caso di questo documento, all'interno di un'unica area di Azure.

Anche il **ripristino di emergenza** ha la funzione di ridurre al minimo le interruzioni dei servizi IT e il relativo ripristino, ma tra data center **diversi**, in genere ubicati a centinaia di chilometri di distanza. Nel caso di questo documento, tra aree di Azure diverse all'interno della stessa area geopolitica o come stabilito dal cliente.

### <a name="overview-of-high-availability"></a>Panoramica della disponibilità elevata

È possibile suddividere la discussione sulla disponibilità elevata SAP in Azure in due parti:

* **Disponibilità elevata dell'infrastruttura di Azure**, relativa, ad esempio, al calcolo (VM), alla rete, all'archiviazione e così via e i relativi vantaggi associati all'aumento della disponibilità delle applicazioni SAP.
* **Disponibilità elevata delle applicazioni SAP**, relativa, ad esempio, ai componenti software SAP:
  * Server applicazioni SAP
  * Istanza di SAP ASCS/SCS
  * Server di database

e al modo in cui è possibile combinarli con la disponibilità elevata dell'infrastruttura di Azure.

La disponibilità elevata di SAP in Azure presenta alcune differenze rispetto alla disponibilità elevata di SAP in un ambiente fisico o virtuale locale. Il documento di SAP seguente descrive le configurazioni a disponibilità elevata standard di SAP in ambienti virtualizzati in Windows: <https://scn.sap.com/docs/DOC-44415>. In Linux non è disponibile alcuna configurazione a disponibilità elevata di SAP integrata in SAPinst come quella disponibile per Windows. Altre informazioni sulla disponibilità elevata di SAP in locale per Linux sono disponibili qui: <https://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>Disponibilità elevata dell'infrastruttura di Azure

Al momento è previsto un contratto di servizio a singola VM del 99,9%. Per avere un'idea di come potrebbe presentarsi la disponibilità di una singola macchina virtuale, è possibile compilare il prodotto dei diversi contratti di servizio di Azure disponibili: <https://azure.microsoft.com/support/legal/sla/>.

La base per il calcolo è di 30 giorni al mese o 43.200 minuti. Di conseguenza, un tempo di inattività dello 0,05% corrisponde a 21,6 minuti. Come di consueto, la disponibilità dei diversi servizi dovrà essere moltiplicata nel modo seguente:

(Disponibilità servizio n. 1/100) * (Disponibilità servizio n. 2/100) * (Disponibilità servizio n. 3/100)

Ad esempio:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 o una disponibilità complessiva del 99,75%.

#### <a name="virtual-machine-vm-high-availability"></a>Disponibilità elevata delle macchine virtuali

Esistono due tipi di eventi della piattaforma Azure che possono incidere sulla disponibilità delle macchine virtuali: la manutenzione pianificata e la manutenzione non pianificata.

* Gli eventi di manutenzione pianificata sono aggiornamenti periodici effettuati da Microsoft sulla piattaforma Azure sottostante per migliorare l'affidabilità, la sicurezza e le prestazioni complessive dell'infrastruttura della piattaforma su cui sono in esecuzione le macchine virtuali.
* Gli eventi di manutenzione non pianificata hanno luogo quando si verifica un guasto dell'hardware o dell'infrastruttura fisica sottostante la macchina virtuale. Può trattarsi, ad esempio, di errori della rete locale, guasti di un disco locale o altri errori a livello di rack. Quando viene rilevato un errore di questo tipo, la piattaforma Azure esegue automaticamente la migrazione della macchina virtuale dal server fisico non integro su cui è in esecuzione a un server fisico integro. Anche se rari, questi eventi possono anche richiedere il riavvio della macchina virtuale.

Per altri dettagli, vedere [disponibilità di macchine virtuali Windows in Azure](../../manage-availability.md) e [disponibilità di macchine virtuali Linux in Azure](../../manage-availability.md).

#### <a name="azure-storage-redundancy"></a>Ridondanza di Archiviazione di Azure

I dati dell'account di archiviazione di Microsoft Azure vengono sempre replicati per assicurare durabilità e disponibilità elevata nonché il rispetto del contratto di servizio di Archiviazione di Azure anche in caso di errori hardware temporanei.

Considerato che Archiviazione di Azure mantiene tre immagini dei dati per impostazione predefinita, RAID5 o RAID1 tra più dischi di Azure non sono necessari.

Per altri dettagli, vedere [ridondanza di archiviazione di Azure](../../../storage/common/storage-redundancy.md).

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Uso del riavvio delle macchine virtuali dell'infrastruttura di Azure per ottenere una disponibilità più elevata delle applicazioni SAP

Se si decide di non usare funzionalità come WSFC (Windows Server Failover Clustering) o Pacemaker su Linux (attualmente supportato solo per SLES 12 e versioni successive), il riavvio delle VM di Azure viene usato per proteggere un sistema SAP da inattività pianificate e non pianificate dell'infrastruttura di server fisici di Azure e della piattaforma Azure complessiva sottostante.

> [!NOTE]
> È importante ricordare che il riavvio delle VM di Azure protegge principalmente le VM e non le applicazioni. Il riavvio delle macchine virtuali non offre una disponibilità elevata per le applicazioni SAP, ma offre un certo livello di disponibilità dell'infrastruttura e quindi, indirettamente, una disponibilità più elevata dei sistemi SAP. Inoltre, non è disponibile alcun contratto di servizio relativo al tempo che sarà necessario per il riavvio di una VM dopo un'interruzione dell'host pianificata o non pianificata. Questo metodo di disponibilità elevata non è quindi adatto per i componenti fondamentali di un sistema SAP come (A)SCS o DBMS.
>
>

Un altro elemento importante dell'infrastruttura per la disponibilità elevata è l'archiviazione. Ad esempio, il contratto di servizio di Archiviazione di Azure prevede una disponibilità del 99,9%. Se si effettua la distribuzione di tutte le VM con i relativi dischi in un singolo account di archiviazione di Azure, la potenziale indisponibilità di Archiviazione di Azure causerà l'indisponibilità di tutte le VM presenti in tale account di archiviazione di Azure e anche di tutti i componenti SAP in esecuzione all'interno di tali VM.

Anziché inserire tutte le VM virtuali in un singolo account di archiviazione di Azure, è possibile anche usare account di archiviazione dedicati per ogni VM e, in questo modo, aumentare la disponibilità complessiva delle VM e delle applicazioni SAP usando più account di archiviazione di Azure indipendenti.

Azure Managed disks viene inserito automaticamente nel dominio di errore della macchina virtuale a cui sono collegati. Se si collocano due macchine virtuali in un set di disponibilità e si utilizzano i Managed Disks, la piattaforma si occupa anche della distribuzione dei Managed Disks in diversi domini di errore. Per utilizzare l’archiviazione Premium, è vivamente consigliabile utilizzare anche i Managed Disks.

Un'architettura di esempio di un sistema SAP NetWeaver che usa la disponibilità elevata dell'infrastruttura di Azure e gli account di archiviazione potrebbe avere un aspetto simile al seguente:

![Diagramma che illustra un sistema SAP NetWeaver che usa la disponibilità elevata e gli account di archiviazione dell'infrastruttura di Azure.][planning-guide-figure-2900]

Un'architettura di esempio di un sistema SAP NetWeaver che usa la disponibilità elevata dell'infrastruttura di Azure e i Managed Disks potrebbe avere un aspetto simile al seguente:

![Uso della disponibilità elevata dell'infrastruttura di Azure per ottenere una disponibilità più elevata delle applicazioni SAP][planning-guide-figure-2901]

Per i componenti SAP critici, sono stati ottenuti fino a questo momento i risultati seguenti:

* Disponibilità elevata di server applicazioni SAP

  Le istanze dei server applicazioni SAP sono componenti ridondanti. Ogni istanza dei server applicazioni SAP viene distribuita nella relativa macchina virtuale, che è in esecuzione in un dominio di errore e di aggiornamento di Azure diverso. Vedere i capitoli [Domini di errore][planning-guide-3.2.1] e [Domini di aggiornamento][planning-guide-3.2.2]. Ciò è garantito dall'uso dei set di disponibilità di Azure. Vedere il capitolo [Set di disponibilità di Azure][planning-guide-3.2.3]. La potenziale indisponibilità pianificata o non pianificata di un dominio di errore o di aggiornamento di Azure causerà l'indisponibilità di un numero limitato di VM con le relative istanze dei server applicazioni SAP.

  Ogni istanza dei server applicazioni SAP è posizionata nel relativo account di archiviazione di Azure. La potenziale indisponibilità di un account di archiviazione di Azure causa l'indisponibilità di una sola macchina virtuale con la relativa istanza dei server applicazioni SAP. Tenere presente, tuttavia, che esiste un limite al numero di account di archiviazione di Azure che è possibile avere all'interno di una sottoscrizione di Azure. Per garantire l'avvio automatico dell'istanza di (A)SCS dopo il riavvio della macchina virtuale, assicurarsi di impostare il parametro di avvio automatico nel profilo di avvio dell'istanza di (A)SCS descritto nel capitolo [Uso dell'avvio automatico per le istanze di SAP][planning-guide-11.5].
  Per altri dettagli, vedere anche il capitolo [disponibilità elevata per i server applicazioni SAP][planning-guide-11.4.1] .

  Anche se si utilizzano i Managed Disks, i dischi vengono archiviati anche sull’account di archiviazione di Azure e possono essere non disponibili in caso di disservizio della risorsa di archiviazione.

* *più elevata* dell'istanza di SAP (A)SCS

  In questo caso il riavvio delle VM di Azure viene usato per proteggere la VM con l'istanza di SAP (A)SCS installata. Nel caso di inattività pianificata o non pianificata dei server di Azure, le VM verranno riavviate in un altro server disponibile. Come indicato in precedenza, il riavvio delle VM di Azure protegge principalmente le VM e non le applicazioni, in questo caso l'istanza di (A) SCS. Con il riavvio delle macchine virtuali, si otterrà indirettamente una disponibilità più elevata dell'istanza di SAP (A)SCS. Per garantire l'avvio automatico dell'istanza di (A)SCS dopo il riavvio della macchina virtuale, assicurarsi di impostare il parametro di avvio automatico nel profilo di avvio dell'istanza di (A)SCS descritto nel capitolo [Uso dell'avvio automatico per le istanze di SAP][planning-guide-11.5]. Ciò significa che l'istanza di (A)SCS come singolo punto di errore (SPOF) in esecuzione in una singola VM sarà il fattore determinante per la disponibilità dell'intero panorama applicativo SAP.

* *più elevata* del server DBMS

  In questo caso, analogamente al caso d'uso dell'istanza di SAP (A)SCS, il riavvio delle macchine virtuali di Azure viene usato per proteggere la macchina virtuale con il software DBMS installato e si ottiene una disponibilità più elevata del software DBMS.
  DBMS in esecuzione in una singola VM è anche uno SPOF ed è il fattore determinante per la disponibilità dell'intero panorama applicativo SAP.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Disponibilità elevata delle applicazioni SAP su IaaS di Azure

Per ottenere una disponibilità elevata dei sistemi SAP completi, è necessario proteggere tutti i componenti dei sistemi SAP critici (ad esempio, i server applicazioni SAP ridondanti) e i componenti univoci (ad esempio, i singoli punti di errore) come l'istanza di SAP (A)SCS e DBMS.

#### <a name="high-availability-for-sap-application-servers"></a><a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Disponibilità elevata per i server applicazioni SAP

Per le istanze di server/finestre di dialogo delle applicazioni SAP, non è necessario considerare una specifica soluzione a disponibilità elevata. La disponibilità elevata si ottiene con la ridondanza che deve quindi essere presente in livelli sufficienti nelle varie macchine virtuali. Le macchine virtuali devono essere tutte posizionate nel set di disponibilità di Azure per evitare che vengano aggiornate durante il periodo di inattività dovuto alla manutenzione pianificata. La funzionalità di base che poggia su diversi domini di aggiornamento e di errore all'interno di un'unità di scala di Azure è stata già introdotta nel capitolo [Domini di aggiornamento][planning-guide-3.2.2]. I set di disponibilità di Azure sono stati presentati nel capitolo [Set di disponibilità di Azure][planning-guide-3.2.3] di questo documento.

Non esiste alcun numero infinito di domini di errore e di aggiornamento che può essere usato da un set di disponibilità di Azure all'interno di un'unità di scala di Azure. Ciò significa che se si inseriscono molte macchine virtuali in un set di disponibilità, prima o poi una macchina virtuale finirà nello stesso dominio di aggiornamento o di errore.

Se si distribuiscono poche istanze dei server applicazioni SAP nelle relative VM dedicate e si suppone che siano presenti cinque domini di aggiornamento, alla fine emerge il quadro seguente. Il numero massimo effettivo di domini di errore e di aggiornamento all'interno di un set di disponibilità potrebbe cambiare in futuro:

![Disponibilità elevata dei i server applicazioni SAP in Azure][planning-guide-figure-3000]


#### <a name="high-availability-for-sap-central-services-on-azure"></a>Disponibilità elevata per SAP Central Services in Azure

Per l'architettura a disponibilità elevata di SAP Central Services in Azure, vedere l'articolo [Architettura e scenari di disponibilità elevata per SAP NetWeaver](./sap-high-availability-architecture-scenarios.md) per le informazioni relative alla voce. L'articolo fa riferimento a descrizioni più dettagliate per gli specifici sistemi operativi.

#### <a name="high-availability-for-the-sap-database-instance"></a>Disponibilità elevata per l'istanza del database SAP

La tipica configurazione del DBMS di SAP a disponibilità elevata si basa su due VM DBMS in cui la funzionalità di disponibilità elevata DBMS viene usata per replicare i dati dall'istanza di DBMS attiva alla seconda VM in un'istanza di DBMS passiva.

La funzionalità di disponibilità elevata e ripristino di emergenza per DBMS in generale nonché per DBMS specifici è descritta nella [Guida alla distribuzione di DBMS][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Disponibilità elevata end-to-end per il sistema SAP completo

Di seguito sono riportati due esempi di architettura SAP NetWeaver a disponibilità elevata in Azure, uno per Windows e l'altro per Linux.

Solo dischi non gestiti: I concetti illustrati di seguito potrebbero richiedere piccoli compromessi quando si distribuiscono molti sistemi SAP e il numero di VM distribuite supera il limite massimo di account di archiviazione per ogni sottoscrizione. In tali casi, i dischi rigidi virtuali delle VM devono essere combinati all'interno di un account di archiviazione. In genere questa operazione viene effettuata combinando i dischi rigidi virtuali delle VM del livello dell'applicazione SAP con sistemi SAP diversi.  Sono stati anche combinati diversi dischi rigidi virtuali delle VM DBMS diverse su sistemi SAP diversi in un account di archiviazione di Azure. Tenere quindi presenti i limiti di operazioni I/O al secondo degli account di Archiviazione di Azure (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>)


##### <a name="windows-logologo_windows-ha-on-windows"></a>![Logo Windows.][Logo_Windows] Disponibilità elevata in Windows

![Diagramma che illustra l'architettura a disponibilità elevata dell'applicazione SAP NetWeaver con SQL Server in Azure IaaS.][planning-guide-figure-3200]

I costrutti di Azure seguenti vengono usati per il sistema SAP NetWeaver, allo scopo di ridurre al minimo l'impatto esercitato dai problemi dell'infrastruttura e dall'applicazione di patch agli host:

* Il sistema completo viene distribuito in Azure (obbligatorio-livello DBMS, istanza (A) SCS e il livello dell'applicazione completo deve essere eseguito nello stesso percorso).
* Il sistema completo viene eseguito all'interno di una sottoscrizione di Azure (obbligatorio).
* Il sistema completo viene eseguito all'interno di una rete virtuale di Azure (obbligatorio).
* La separazione delle macchine virtuali di un unico sistema SAP in tre set di disponibilità è possibile anche quando tutte le macchine virtuali appartengono alla stessa rete virtuale.
* Ogni livello (ad esempio DBMS, ASCS, server applicazioni) deve usare un set di disponibilità dedicato.
* Tutte le macchine virtuali che eseguono istanze di DBMS di un sistema SAP sono incluse in un unico set di disponibilità. Si suppone che siano presenti più VM che eseguono istanze di DBMS per ciascun sistema perché vengono usate le funzionalità di disponibilità elevata dei DBMS nativi, come SQL Server AlwaysOn oppure Oracle Data Guard.
* Tutte le VM che eseguono istanze di DBMS usano il proprio account di archiviazione. I file di dati e di log di DBMS vengono replicati da un account di archiviazione a un altro account di archiviazione usando le funzioni a disponibilità elevata di DBMS per la sincronizzazione dei dati. L'indisponibilità di un account di archiviazione causa l'indisponibilità di un nodo del cluster di SQL Windows, ma non dell'intero servizio di SQL Server.
* Tutte le macchine virtuali che eseguono istanze di (A)SCS di un sistema SAP sono incluse in un unico set di disponibilità. All'interno di tali VM è configurato Windows Server Failover Cluster (WSFC), per proteggere l'istanza di (A)SCS.
* Tutte le VM che eseguono istanze di (A)SCS usano il proprio account di archiviazione. I file delle istanze di (A)SCS e le cartelle globali SAP vengono replicate da un account di archiviazione a un altro account di archiviazione usando la replica SIOS DataKeeper. L'indisponibilità di un account di archiviazione causa l'indisponibilità di un nodo del cluster di Windows (A)SCS, ma non dell'intero servizio di (A)SCS.
* TUTTE le macchine virtuali che rappresentano il livello del server applicazioni SAP sono incluse in un terzo set di disponibilità.
* TUTTE le VM che eseguono server applicazioni SAP usano il proprio account di archiviazione. L'indisponibilità di un account di archiviazione causa l'indisponibilità di un server applicazioni SAP, mentre altri server applicazioni SAP continuano a essere eseguiti.

Nella figura seguente è illustrato lo stesso scenario utilizzando Managed Disks.

![Architettura a disponibilità elevata dell'applicazione SAP NetWeaver con SQL Server in IaaS di Azure][planning-guide-figure-3201]

##### <a name="linux-logologo_linux-ha-on-linux"></a>![Logo Linux.][Logo_Linux] Disponibilità elevata in Linux

L'architettura per la disponibilità elevata SAP in Linux su Azure è essenzialmente identica a quella di Windows descritta in precedenza. Vedere la nota SAP [1928533] per un elenco delle soluzioni a disponibilità elevata supportate.

### <a name="using-autostart-for-sap-instances"></a><a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Uso dell'avvio automatico per le istanze di SAP

SAP ha reso disponibile la funzionalità che consente di avviare le istanze di SAP immediatamente dopo l'avvio del sistema operativo nella VM. I passaggi esatti sono documentati nell'articolo della Knowledge Base SAP [1909114]. Tuttavia, SAP sconsiglia di continuare a usare questa impostazione perché non offre alcun controllo sull'ordine dei riavvii delle istanze, supponendo che siano interessate più di una VM o vengano eseguite più istanze per ogni VM. Presupponendo uno scenario tipico di Azure di un'istanza del server applicazioni SAP in una macchina virtuale e il caso di una singola macchina virtuale che viene alla fine riavviata, l'avvio automatico non è critico e può essere abilitato aggiungendo il parametro seguente:

`Autostart = 1`

Nel profilo di avvio dell'istanza di SAP ABAP e/o di Java.

> [!NOTE]
> Il parametro di avvio automatico può presentare anche alcuni inconvenienti. Nello specifico, il parametro attiva l'avvio di un'istanza di SAP ABAP o di Java quando viene avviato il servizio di dell'istanza Windows/Linux correlato. Questo si verifica sicuramente all'avvio dei sistemi operativi. Tuttavia, i riavvii dei servizi SAP sono comuni anche per la funzionalità di gestione del ciclo di vita di SAP come SUM o altri aggiornamenti. Queste funzionalità non prevedono il riavvio automatico di un'istanza. Quindi, il parametro di avvio automatico deve essere disabilitato prima di eseguire tali attività. Inoltre, il parametro di avvio automatico non deve essere usato per le istanze di SAP in cluster, ad esempio ASCS/SCS/CI.
>
>

Maggiori informazioni sull'avvio automatico per le istanze di SAP sono disponibili qui:

* [Avviare/arrestare SAP con l'avvio/l'arresto del server Unix](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Avviare e arrestare gli agenti di gestione di SAP NetWeaver](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Come abilitare l'avvio automatico di Hana Database](http://sapbasisinfo.com/blog/2016/08/15/enabling-autostart-of-sap-hana-database-on-server-boot-situation/)

### <a name="larger-3-tier-sap-systems"></a>Sistemi SAP a 3 livelli di maggiori dimensioni
Gli aspetti di disponibilità elevata delle configurazioni SAP a 3 livelli sono già stati esaminati nelle sezioni precedenti. Ora verrà esaminato il caso dei sistemi con requisiti del server DBMS troppo elevati per essere posizionati in Azure, ma con un livello dell'applicazione SAP che potrebbe essere distribuito in Azure.

#### <a name="location-of-3-tier-sap-configurations"></a>Posizione delle configurazioni SAP a 3 livelli
La suddivisione del livello di applicazione stesso o dell'applicazione e del livello DBMS tra distribuzione locale e distribuzione in Azure non è supportata. Un sistema SAP deve essere completamente distribuito in locale OPPURE in Azure. Inoltre, non è possibile eseguire alcuni server applicazioni in locale e altri in Azure. Questa premessa rappresenta il punto iniziale della discussione. Inoltre, non è possibile distribuire componenti DBMS di un sistema SAP e il livello del server applicazioni SAP in due diverse aree di Azure. Ad esempio, DBMS negli Stati Uniti occidentali e il livello dell'applicazione negli Stati Uniti centrali. Il motivo per il quale tali configurazioni non sono supportate è la sensibilità di latenza dell'architettura di SAP NetWeaver.

Tuttavia, nel corso dello scorso anno i partner dei data center hanno sviluppato condivisioni percorso nelle aree di Azure. Queste condivisioni percorso sono spesso vicine ai data center fisici di Azure all'interno di un'area di Azure. La breve distanza e la connessione degli asset nella condivisione percorso tramite ExpressRoute in Azure possono causare una latenza inferiore a 2 millisecondi. In tali casi, è possibile individuare il livello DBMS (inclusa la SAN/NAS di archiviazione) in tale condivisione percorso e il livello dell'applicazione SAP in Azure. [Istanze Large di HANA](./hana-overview-architecture.md).

### <a name="offline-backup-of-sap-systems"></a>Backup offline di sistemi SAP
In base alla configurazione SAP scelta (livello 2 o 3), potrebbe essere necessario eseguire un backup. del contenuto della VM stessa oltre che del database. I backup correlati ai DBMS dovrebbero essere eseguiti con metodi di database. Una descrizione dettagliata dei diversi database è disponibile nella [Guida alla distribuzione DBMS][dbms-guide]. D'altra parte, i dati SAP possono essere sottoposti a backup in modalità offline (incluso il contenuto del database) come descritto in questa sezione oppure online come descritto nella sezione successiva.

Il backup offline richiederebbe fondamentalmente un arresto della VM usando il portale di Azure e una copia del disco della VM di base, oltre a tutti i dischi collegati alla VM. Questo permetterebbe di mantenere un'immagine temporizzata della VM e del relativo disco. Si consiglia di copiare i backup in un account di archiviazione di Azure diverso. Di conseguenza si applica la procedura descritta nel capitolo [Copia di dischi tra account di archiviazione di Azure][planning-guide-5.4.2] di questo documento.


Un ripristino dello stato comporterebbe l'eliminazione della VM di base nonché dei relativi dischi originali e dei dischi montati, la copia dei dischi salvati nell'account di archiviazione originale o nel gruppo di risorse per i dischi gestiti e quindi la ridistribuzione del sistema.
Questo articolo illustra un esempio di come generare lo script di questo processo in PowerShell: <https://www.westerndevs.com/_/azure-snapshots/>

Assicurarsi di installare una nuova licenza SAP poiché il ripristino di un backup di VM come descritto in precedenza crea una nuova chiave hardware.

### <a name="online-backup-of-an-sap-system"></a>Backup online di un sistema SAP

Il backup del DBMS viene eseguito con metodi specifici del DBMS, come descritto nella [Guida alla distribuzione DBMS][dbms-guide].

Altre VM all'interno del sistema SAP possono essere sottoposte a backup usando la funzionalità Backup macchina virtuale di Azure. La funzionalità Backup delle macchine virtuali di Azure è un metodo standard per eseguire il backup di una macchina virtuale completa in Azure. Il backup di Azure archivia i backup in Azure e consente il ripristino di una VM.

> [!NOTE]
> Al mese di dicembre 2015 il backup delle macchine VM NON mantiene l'ID univoco della VM usato per le licenze SAP. Ciò significa che il ripristino da un backup di VM richiede l'installazione di una nuova chiave di licenza SAP perché la VM ripristinata è considerata una nuova VM e non una sostituzione di quella precedente che era stata salvata.
>
> ![Logo Windows.][Logo_Windows] Windows
>
> In teoria anche le VM che eseguono database possono essere sottoposte a backup in modo coerente se il sistema DBMS supporta il Servizio Copia Shadow del volume di Windows <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx> come, ad esempio, SQL Server.
> Tenere presente, tuttavia, che in base ai backup di VM di Azure, i ripristini temporizzati dei database non sono possibili. Si consiglia, quindi, di eseguire backup di database usando la funzionalità DBMS invece del backup delle VM di Azure.
>
> Per acquisire familiarità con la funzionalità Backup delle macchine virtuali di Azure, iniziare da questo articolo: <https://docs.microsoft.com/azure/backup/backup-azure-vms>.
>
> Altre possibilità prevedono l'uso di una combinazione di Microsoft Data Protection Manager installato in una VM di Azure e del backup di Azure per eseguire il backup e il ripristino di database. Altre informazioni sono disponibili qui: <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>.
>
> ![Logo Linux.][Logo_Linux] Linux
>
> In Linux non esiste alcuna funzionalità equivalente al servizio Copia Shadow del volume di Windows. Quindi, è possibile eseguire solo backup coerenti con i file, ma non con l'applicazione. Il backup del DBMS di SAP deve essere eseguito usando la funzionalità DBMS. Il file system che include i dati correlati a SAP può essere salvato, ad esempio usando tar come descritto qui: <https://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure come sito di ripristino di emergenza per panorami applicativi SAP di produzione

Dalla metà del 2014, le estensioni ai vari componenti di Hyper-V, System Center e Azure consentono l'utilizzo di Azure come sito di ripristino di emergenza per le macchine virtuali in esecuzione in locale basate su Hyper-V.

Per una descrizione dettagliata di come distribuire questa soluzione, vedere il blog seguente: <https://docs.microsoft.com/archive/blogs/saponsqlserver/protecting-sap-solutions-with-azure-site-recovery>.

## <a name="summary-for-high-availability-for-sap-systems"></a>Riepilogo per la disponibilità elevata per i sistemi SAP

I punti chiave della disponibilità elevata per i sistemi SAP in Azure sono i seguenti:

* Attualmente il singolo punto di errore di SAP non può essere protetto esattamente nello stesso modo delle distribuzioni locali. Il motivo è legato al fatto che i cluster di dischi condivisi non possono ancora essere integrati in Azure senza l'uso di software di terze parti.
* Per il livello DBMS è necessario usare la funzionalità DBMS che non si basa sulla tecnologia del cluster di dischi condivisi. Informazioni dettagliate sono fornite nella [Guida alla distribuzione DBMS][dbms-guide].
* Per ridurre al minimo l'impatto dei problemi all'interno dei domini di errore nell'infrastruttura di Azure o nella manutenzione di host, è consigliabile usare i set di disponibilità di Azure:
  * È consigliabile avere un set di disponibilità per il livello di applicazione SAP.
  * È consigliabile avere un set di disponibilità separato per il livello DBMS di SAP.
  * NON è consigliabile applicare lo stesso set di disponibilità per le macchine virtuali di diversi sistemi SAP.
  * Si consiglia di utilizzare Managed Disks Premium.
* Per le finalità di backup del livello DBMS di SAP, consultare la [Guida alla distribuzione DBMS][dbms-guide].
* Il backup delle istanze delle finestra di dialogo SAP ha poco senso perché in genere la ridistribuzione di semplici istanze di dialogo costituisce un processo più veloce.
* Ha invece senso il backup della macchina virtuale che contiene la directory globale del sistema SAP e di tutti i profili delle varie istanze. Tale backup deve essere eseguito con Windows Backup oppure, ad esempio, con tar in Linux. Dato che esistono differenze tra Windows Server 2008 (R2) e Windows Server 2012 (R2) e che il backup risulta più semplice usando le release più recenti di Windows Server, è consigliabile eseguire Windows Server 2012 (R2) come sistema operativo guest Windows.

## <a name="next-steps"></a>Passaggi successivi
Leggere gli articoli:

- [Distribuzione di Macchine virtuali di Azure per SAP NetWeaver](./deployment-guide.md)
- [Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP](./dbms_guide_general.md)
- [Configurazioni e operazioni dell'infrastruttura SAP HANA in Azure](/- azure/virtual-machines/workloads/sap/hana-vm-operations)
