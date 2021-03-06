## <a name="virtual-network-basics"></a>虚拟网络基础知识
### <a name="what-is-an-azure-virtual-network-vnet"></a>Azure 虚拟网络 (VNet) 是什么？
你可以使用 VNet 设置和管理 Azure 中的虚拟专用网络 (VPN)，或者链接 VNet 与 Azure 中的其他 VNet，或链接你的本地 IT 基础结构，以创建混合或跨界解决方案。 你创建的每个 VNet 都具有其自己的 CIDR 块，并且只要 CIDR 块不会冲突，则可链接到其他 Vnet 和本地网络。 还可以控制 VNet 的 DNS 服务器设置并将 VNet 分离到子网中。

使用 VNet：

* 创建私有云专用的虚拟网络
  
    有时你不需要适用于解决方案的跨界配置。 创建 VNet 时，VNet 中的服务和 VM 可以在云中安全地互相直接通信。 这可以在 VNet 内安全地保存流量，但仍允许你为需要 Internet 通信的 VM 和服务配置终结点连接，作为解决方案的一部分。
* 安全地扩展数据中心
  
    借助 VNet，你可以构建传统的站点到站点 (S2S) VPN，以便安全地缩放数据中心容量。 S2S VPN 使用 IPSEC 提供企业 VPN 网关和 Azure 之间的安全连接。
* 实现混合云方案
  
    利用 VNet 可灵活地支持一系列混合云方案。 你可以安全地将基于云的应用程序连接到任何类型的本地系统，例如大型机和 Unix 系统。

### <a name="how-do-i-know-if-i-need-a-virtual-network"></a>如何知道是否需要虚拟网络？
请访问[虚拟网络概述](../articles/virtual-network/virtual-networks-overview.md)，以便查看可帮助你决定最佳网络设计选项的决策表。

### <a name="how-do-i-get-started"></a>如何开始？
请从访问[虚拟网络文档](https://azure.microsoft.com/documentation/services/virtual-network/)开始。 此页包含指向常见配置步骤的链接以及帮助你了解设计虚拟网络时需要考虑的事项的信息。

### <a name="what-services-can-i-use-with-vnets"></a>哪些服务可以与 VNet 共同使用？
VNet 可以与各种不同的 Azure 服务共同使用，例如云服务 (PaaS)、虚拟机和 Web Apps。 但是，有几个 VNet 不支持的服务。 请检查你想要使用的特定服务，并验证是否兼容。

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>没有跨界连接的情况下是否可以使用 VNet？
是的。 可以在不使用站点到站点连接的情况下使用 VNet。 如果你想要在 Azure 中运行域控制器和 SharePoint 场，此特点特别有用。

## <a name="virtual-network-configuration"></a>虚拟网络配置
### <a name="what-tools-do-i-use-to-create-a-vnet"></a>要使用哪些工具创建 VNet？
可以使用以下工具创建或配置虚拟网络：

* Azure 门户（用于经典 VNet 和 Resource Manager VNet）。
* 网络配置文件（netcfg - 仅用于经典 VNet）。 请参阅[使用网络配置文件配置虚拟网络](../articles/virtual-network/virtual-networks-using-network-configuration-file.md)。
* PowerShell（用于经典 VNet 和 Resource Manager VNet）。
* Azure CLI（用于经典 VNet 和 Resource Manager VNet）。

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>在我的 VNet 中可以使用哪些地址范围？
可以使用 [RFC 1918](http://tools.ietf.org/html/rfc1918) 中定义的公共 IP 地址范围和任何 IP 地址范围。

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>我的 VNet 中是否可以有公共 IP 地址？
是的。 有关公共 IP 地址范围的详细信息，请参阅[虚拟网络 (VNet) 中的公共 IP 地址空间](../articles/virtual-network/virtual-networks-public-ip-within-vnet.md)。 请记住，无法从 Internet 直接访问公共 IP。

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-virtual-network"></a>虚拟网络中的子网数量是否有限制？
VNet 中使用的子网数量没有限制。 所有子网都必须完全包含在虚拟网络地址空间中，并不应相互重叠。

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>使用这些子网中的 IP 地址是否有任何限制？
Azure 会保留每个子网中的某些 IP 地址。 子网的第一个和最后一个 IP 地址仅为协议一致性而保留，其他 3 个地址用于 Azure 服务。

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>VNet 和子网的最小和最大容量是多少？
我们支持的最小子网为 /29，最大为 /8（使用 CIDR 子网定义）。

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>是否可以使用 VNet 将 VLAN 引入 Azure 中？
不能。 VNet 是第 3 层重叠。 Azure 不支持任何第 2 层语义。

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>是否可以在 VNet 和子网上指定自定义路由策略？
是的。 可以使用用户定义路由 (UDR)。 有关 UDR 的详细信息，请访问[用户定义的路由和 IP 转发](../articles/virtual-network/virtual-networks-udr-overview.md)。

### <a name="do-vnets-support-multicast-or-broadcast"></a>VNet 是否支持多播或广播？
不能。 不支持多播或广播。

### <a name="what-protocols-can-i-use-within-vnets"></a>在 VNet 中可以使用哪些协议？
可以在 VNet 中使用基于 IP 的标准协议。 但是，VNet 内阻止多播、广播、在 IP 里面封装 IP 的数据包以及通用路由封装 (GRE) 数据包。 工作的标准协议包括：

* TCP
* UDP
* ICMP

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>是否可以在 VNet 中 ping 默认路由器？
没有。

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>是否可以使用 tracert 诊断连接？
没有。

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>创建 VNet 后是否可以添加子网？
是的。 只要子网地址不属于 VNet 中另一个子网的一部分，就能随时将子网添加到 VNet 中。

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>创建后是否可以修改子网的大小？
如果子网中未部署任何 VM 或服务，则可以使用 PowerShell cmdlet 或 NETCFG 文件添加、删除、展开或收缩该子网。 只要包含 VM 或服务的子网不受此更改的影响，还可以添加、删除、展开或收缩任何前缀。

### <a name="can-i-modify-subnets-after-i-created-them"></a>创建子网后是否可以对其进行修改？
是的。 可以添加、删除和修改 VNet 使用的 CIDR 块。

### <a name="can-i-connect-to-the-internet-if-i-am-running-my-services-in-a-vnet"></a>如果我在 VNet 中运行服务，是否可以连接到 Internet？
是的。 VNet 中部署的所有服务都可以连接到 Internet。 Azure 中部署的每个云服务都具有分配到它的可公开寻址的 VIP。 必须定义 PaaS 角色的输入终结点和虚拟机的终结点，以使这些服务可以接受 Internet 的连接。

### <a name="do-vnets-support-ipv6"></a>VNet 是否支持 IPv6？
不能。 目前不能将 IPv6 用于 VNet。

### <a name="can-a-vnet-span-regions"></a>VNet 是否可以跨区域？
不能。 一个 VNet 限制为单个区域。

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>是否可以将 VNet 连接到 Azure 中的另一个 VNet？
是的。 可以使用 REST API 或 Windows PowerShell 创建 VNet 到 VNet 通信。 还可以通过 VNet 对等互连连接 VNet。 有关对等互连的更多详细信息，请参阅[此处](../articles/virtual-network/virtual-network-peering-overview.md)。

## <a name="name-resolution-dns"></a>名称解析 (DNS)
### <a name="what-are-my-dns-options-for-vnets"></a>VNet 的 DNS 选项有哪些？
使用 [VM 和角色实例的名称解析](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)页上的决策表，可引导用户浏览可用的所有 DNS 选项。

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>是否可以为 VNet 指定 DNS 服务器？
是的。 可以在 VNet 设置中指定 DNS 服务器 IP 地址。 这将作为 VNet 中的所有 VM 的默认 DNS 服务器进行应用。

### <a name="how-many-dns-servers-can-i-specify"></a>可以指定多少 DNS 服务器？
最多可以指定 12 个 DNS 服务器。

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>创建网络后是否可以修改 DNS 服务器？
是的。 可以随时更改 VNet 的 DNS 服务器列表。 如果更改 DNS 服务器列表，则需要重新启动 VNet 中的每个 VM，以使其拾取新的 DNS 服务器。

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>什么是 Azure 提供的 DNS？它是否适用于 VNet？
Azure 提供的 DNS 是由 Microsoft 提供的多租户 DNS 服务。 在此服务中，Azure 会注册所有 VM 和角色实例。 此服务通过主机名为相同云服务内包含的 VM 和角色实例提供名称解析，并通过 FQDN 为相同 VNet 中的 VM 和角色实例提供名称解析。

> [!NOTE]
> 此时使用 Azure 提供的 DNS 进行跨租户名称解析时，虚拟网络中的前 100 个云服务具有限制。 如果使用自己的 DNS 服务器，此限制则不适用。
> 
> 

### <a name="can-i-override-my-dns-settings-on-a-per-vm--service-basis"></a>是否可以基于每个 VM/服务重写 DNS 设置？
是的。 您可以基于每个云服务设置 DNS 服务器，以重写默认网络设置。 但是，我们建议你使用尽可能多的整个网络的 DNS。

### <a name="can-i-bring-my-own-dns-suffix"></a>是否可以引入我自己的 DNS 后缀？
不能。 不能为 VNet 指定自定义的 DNS 后缀。

## <a name="vnets-and-vms"></a>VNet 和 VM
### <a name="can-i-deploy-vms-to-a-vnet"></a>是否可以将 VM 部署到 VNet？
是的。

### <a name="can-i-deploy-linux-vms-to-a-vnet"></a>是否可以将 Linux VM 部署到 VNet？
是的。 可以部署 Azure 支持的任何发行版的 Linux。

### <a name="what-is-the-difference-between-a-public-vip-and-an-internal-ip-address"></a>公共 VIP 与内部 IP 地址之间的区别是什么？
* 内部 IP 地址是由 DHCP 分配到 VNet 中每台 VM 的 IP 地址。 它不是面向公众的。 如果已经创建 VNet，内部 IP 地址则通过 VNet 的子网设置中指定的范围进行分配。 如果还没有 VNet，仍会分配内部 IP 地址。 内部 IP 地址在生存期内仍属于 VM，除非 VM 被释放。
* 公共 VIP 是分配到云服务或负载平衡器的公共 IP 地址。 不会将其直接分配到 VM NIC。 VIP 保留在分配到的云服务中，直到该云服务中的所有 VM 被释放或删除。 此时，VIP 将释放。

### <a name="what-ip-address-will-my-vm-receive"></a>我的 VM 将接收哪个 IP 地址？
* **内部 IP 地址** - 如果将 VM 部署到 VNet，该 VM 从你指定的内部 IP 地址池接收内部 IP 地址。 VM 使用内部 IP 地址在 VNet 内进行通信。 虽然 Azure 分配动态内部 IP 地址，但你可以为你的 VM 请求静态地址。 若要了解有关静态内部 IP 地址的详细信息，请访问[如何设置静态内部 IP](../articles/virtual-network/virtual-networks-reserved-private-ip.md)。
* **VIP** - VM 还与 VIP 相关联，不过永远不会将 VIP 直接分配到 该 VM。 VIP 是可以分配到云服务的公共 IP 地址。 还可以为云服务保留 VIP。
* **ILPIP** - 还可以配置实例层级公共 IP 地址 (ILPIP)。 ILPIP 是直接与 VM 相关联，而非云服务。 若要了解有关 ILPIP 的详细信息，请访问[实例层级公共 IP 概述](../articles/virtual-network/virtual-networks-instance-level-public-ip.md)。

### <a name="can-i-reserve-an-internal-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>是否可以为以后创建的 VM 保留内部 IP 地址？
不能。 不能保留内部 IP 地址。 如果内部 IP 地址可用，它将由 DHCP 服务器分配到 VM 或角色实例。 该 VM 可能是想要分配到的内部 IP 地址，也可能不是。 但是，可以将已创建的 VM 的内部 IP 地址更改为任何可用的内部 IP 地址。

### <a name="do-internal-ip-addresses-change-for-vms-in-a-vnet"></a>是否为 VNet 中的 VM 更改内部 IP 地址？
是的。 内部 IP 地址在生存期内仍属于 VM，除非 VM 被释放。 当 VM 被释放时，内部 IP 地址则释放，除非定义了 VM 的静态内部 IP 地址。 如果 VM 只是停机（而不是处于“停止(解除分配)”状态），IP 地址仍保持分配到 VM。

### <a name="can-i-manually-assign-ip-addresses-to-nics-in-vms"></a>是否可以手动将 IP 地址分配到 VM 中的 NIC？
不能。 不得更改 VM 的任何接口属性。 任何更改都可能导致 VM 连接丢失。

### <a name="what-happens-to-my-ip-addresses-if-i-shut-down-a-vm"></a>如果关闭 VM，IP 地址会发生什么变化？
无变化。 IP 地址（公共 VIP 和内部 IP 地址）将留在云服务或 VM 中。

> [!NOTE]
> 如果只想要关闭 VM，请不要使用管理门户执行此操作。 目前，关闭按钮会释放虚拟机。
> 
> 

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-re-deploying"></a>在无需重新部署的情况下，是否可以将 VM 从一个子网移动到 VNet 中的另一个子网？
是的。 可以在[此处](../articles/virtual-network/virtual-networks-move-vm-role-to-subnet.md)查看详细信息。

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>是否可以为我的 VM 配置静态 MAC 地址？
不能。 MAC 地址不能以静态方式配置。

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-it-has-been-created"></a>创建 MAC 后，其地址是否在 VM 中保持不变？
是，即使 VM 已停止（已解除分配）并重新启动，VM 的 MAC 地址也保持不变。

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>是否可以通过 VNet 中的 VM 连接到 Internet？
是的。 VNet 中部署的所有服务都可以连接到 Internet。 此外，Azure 中部署的每个云服务都具有分配到它的可公开寻址的 VIP。 必须定义 PaaS 角色的输入终结点和 VM 的终结点，以使这些服务可以接受 Internet 的连接。

## <a name="vnets-and-services"></a>VNet 和服务
### <a name="what-services-can-i-use-with-vnets"></a>哪些服务可以与 VNet 共同使用？
只能在 VNet 中使用计算服务。 计算服务仅限于云服务（Web 和辅助角色）和 VM。

### <a name="can-i-use-web-apps-with-virtual-network"></a>是否可以共同使用 Web Apps 和虚拟网络？
是的。 可以使用 ASE（应用服务环境）在 VNet 内部部署 Web 应用。 此外，如果为 VNet 配置了点到站点，Web 应用则可以安全地连接和访问 Azure VNet 中的资源。 有关详细信息，请参阅以下主题：

* [在应用服务环境中创建 Web 应用](../articles/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Web 应用虚拟网络集成](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)
* [将 VNet 集成和混合连接用于 Web 应用](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)
* [将 Web 应用与 Azure 虚拟网络集成](../articles/app-service-web/web-sites-integrate-with-vnet.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>是否可以在 VNet 中部署云服务与 Web 和辅助角色 (PaaS)？
是的。 可以在 VNet 中部署 PaaS 服务。

### <a name="how-do-i-deploy-paas-roles-to-a-vnet"></a>如何将 PaaS 角色部署到 VNet？
可以通过在服务配置的网络配置部分中指定 VNet 名称和角色/子网映射完成此操作。 不需要更新任何二进制文件。

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>是否可以将服务移入和移出 VNet？
不能。 不能将服务移入和移出 VNet。 必须删除并重新部署该服务，以将其移动到另一个 VNet 中。

## <a name="vnets-and-security"></a>VNet 和安全
### <a name="what-is-the-security-model-for-vnets"></a>VNet 的安全模型是什么？
VNet 相互之间以及与 Azure 基础结构中托管的其他服务之间完全孤立。 VNet 是一条信任边界。

### <a name="can-i-define-acls-or-nsgs-on-my-vnets"></a>是否可以在 VNet 上定义 ACL 或 NSG？
不能。 不能将 ACL 或 NSG 关联到 VNet。 但是，可以在已部署到 VNet 的 VM 的输入终结点上定义 ACL，并且 NSG 可与子网或 NIC 相关联。

### <a name="is-there-a-vnet-security-whitepaper"></a>是否有 VNet 安全白皮书？
是的。 可以在[此处](http://go.microsoft.com/fwlink/?LinkId=386611)下载。

## <a name="apis-schemas-and-tools"></a>API、架构和工具
### <a name="can-i-manage-vnets-from-code"></a>是否可以通过代码管理 VNet？
是的。 可以使用 REST API 管理 VNet 和跨界连接。 可在[此处](http://go.microsoft.com/fwlink/?LinkId=296833)找到更多信息。

### <a name="is-there-tooling-support-for-vnets"></a>是否有 VNet 的工具支持？
是的。 PowerShell 和命令行工具可用于各种平台。 可在[此处](http://go.microsoft.com/fwlink/?LinkId=317721)找到更多信息。

