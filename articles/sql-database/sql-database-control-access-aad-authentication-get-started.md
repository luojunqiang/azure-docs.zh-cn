---
title: "AAD 身份验证：Azure SQL 数据库防火墙、身份验证和访问权限 | Microsoft 文档"
description: "本操作方法指南介绍如何在 SQL Server Management Studio 和 Transact-SQL 中使用授予 Azure SQL 数据库服务器和数据库访问权限与控制权限的服务器级和数据库级防火墙规则、Azure Active Directory 身份验证、登录名、用户与数据库角色。"
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 67797b09-f5c3-4ec2-8494-fe18883edf7f
ms.service: sql-database
ms.custom: security
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: ca679a820eefc7acbb08eed6b8f809f46aacd3a3
ms.contentlocale: zh-cn
ms.lasthandoff: 04/15/2017


---
# <a name="azure-ad-authentication-access-and-database-level-firewall-rules"></a>Azure AD 身份验证、访问和数据库级防火墙规则
本操作方法指南介绍如何在 SQL Server Management Studio 中使用授予 Azure SQL 数据库服务器和数据库访问权限与许可权限的 Azure Active Directory 身份验证、登录名、用户与数据库角色。 学习内容：

- 查看 master 数据库和用户数据库中的用户权限
- 基于 Azure Active Directory 身份验证创建登录名和用户
- 向用户授予服务器范围的权限和特定于数据库的权限
- 以非管理员用户身份登录到用户数据库
- 针对数据库用户创建数据库级防火墙规则
- 针对服务器管理员创建服务器级防火墙规则

**用时估计**：完成本操作方法指南大约需要 45 分钟（假设满足先决条件）。

## <a name="prerequisites"></a>先决条件

* **一个 Azure 帐户**。 需要一个 Azure 帐户。 可以[建立一个免费 Azure 帐户](https://azure.microsoft.com/free/)或[激活 Visual Studio 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/)。 

* **Azure 创建权限**。 必须能够使用属于订阅所有者成员或参与者角色成员的帐户连接到 Azure 门户。 有关基于角色的访问控制 (RBAC) 的详细信息，请参阅 [Getting started with access management in the Azure portal](../active-directory/role-based-access-control-what-is.md)（Azure 门户中的访问管理入门）。

* **SQL Server Management Studio**。 可以通过[下载 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 下载并安装最新版本的 SQL Server Management Studio (SSMS)。 连接到 Azure SQL 数据库时，请始终使用最新版本的 SSMS，因为其中会不断地发布新功能。

* **基服务器和数据库** 若要安装和配置在本操作方法指南中使用的服务器和两个数据库，请单击“部署到 Azure”按钮。 单击该按钮会打开“从模板部署”边栏选项卡；创建新的资源组，为将要创建的新服务器提供“管理员登录密码”：

   [![下载](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fsqldbtutorial.blob.core.windows.net%2Ftemplates%2Fsqldbgetstarted.json)

   > [!NOTE]
   > 根据需要完成 SQL Server 身份验证的相关操作方法指南：[SQL 身份验证、登录名和用户帐户、数据库角色、权限、服务器级防火墙规则和数据库级防火墙规则](sql-database-control-access-sql-authentication-get-started.md) - 但是，本操作方法指南讲解的一些概念未在本文中复述。 如果在同一台计算机（使用相同的 IP 地址）上完成了这篇相关操作方法指南，则无需执行本操作方法指南中与服务器和数据库级防火墙相关的过程。正因如此，这些过程在本操作方法指南中标记为可选。 此外，本操作方法指南中的屏幕截图内容假设读者已完成这篇相关操作方法指南。 
   >

* 已创建并填充 Azure Active Directory。 有关详细信息，请参阅[将本地标识与 Azure Active Directory 集成](../active-directory/active-directory-aadconnect.md)、[将自己的域名添加到 Azure AD](../active-directory/active-directory-add-domain.md)、[Microsoft Azure 现在支持与 Windows Server Active Directory 联合](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)、[管理 Azure AD 目录](https://msdn.microsoft.com/library/azure/hh967611.aspx)、[使用 Windows PowerShell 管理 Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx) 和[混合标识所需端口和协议](../active-directory/active-directory-aadconnect-ports.md)。

> [!NOTE]
> 本操作方法指南帮助读者了解以下学习主题的内容：[SQL 数据库访问和控制](sql-database-control-access.md)、[登录名、用户和数据库角色](sql-database-manage-logins.md)、[主体](https://msdn.microsoft.com/library/ms181127.aspx)、[数据库角色](https://msdn.microsoft.com/library/ms189121.aspx)、[SQL 数据库防火墙规则](sql-database-firewall-configure.md)和 [Azure Active Directory 身份验证](sql-database-aad-authentication.md)。 
>  

## <a name="sign-in-to-the-azure-portal-using-your-azure-account"></a>使用 Azure 帐户登录到 Azure 门户
使用 [现有订阅](https://account.windowsazure.com/Home/Index)，按照以下步骤连接到 Azure 门户。

1. 打开所选的浏览器并连接到 [Azure 门户](https://portal.azure.com/)。
2. 登录到 [Azure 门户](https://portal.azure.com/)。
3. 在“登录”  页面上，提供订阅的凭据。
   
## <a name="provision-an-azure-active-directory-admin-for-your-sql-logical-server"></a>为 SQL 逻辑服务器预配 Azure Active Directory 管理员

本操作方法指南部分介绍如何在 Azure 门户中查看有关逻辑服务器安全配置的信息。

1. 打开逻辑服务器的“SQL Server”边栏选项卡，在“概述”页中查看信息。 可以看到，Azure Active Directory 管理员尚未配置。

   ![Azure 门户中的服务器管理员帐户](./media/sql-database-control-access-aad-authentication-get-started/sql_admin_portal.png)

2. 在“概要”窗格中单击“未配置”，打开“Active Directory 管理员”边栏选项卡。

   ![AAD 边栏选项卡](./media/sql-database-control-access-aad-authentication-get-started/aad_blade.png)

3. 单击“设置管理员”打开“添加管理员”边栏选项卡，然后选择一个 Active Directory 用户或组帐户作为服务器的 Active Directory 管理员。

   ![选择 AAD 管理员帐户](./media/sql-database-control-access-aad-authentication-get-started/aad_admin.png)

4. 单击“选择”，然后单击“保存”。

   ![保存选定的 AAD 管理员帐户](./media/sql-database-control-access-aad-authentication-get-started/aad_admin_save.png)

> [!NOTE]
> 若要查看此服务器的连接信息，请转到[连接 SSMS](sql-database-connect-query-ssms.md)。 在本系列操作方法指南中，完全限定的服务器名称为“sqldbtutorialserver.database.windows.net”。
>

## <a name="connect-to-sql-server-using-sql-server-management-studio-ssms"></a>使用 SQL Server Management Studio (SSMS) 连接到 SQL 服务器

1. 使用“下载 SQL Server Management Studio”下载并安装最新版本的 SSMS（如果尚未这样做）。[](https://msdn.microsoft.com/library/mt238290.aspx) 为保持最新状态，最新版本的 SSMS 会提示你有新版本可供下载。

2. 安装后，在 Windows 搜索框中键入 **Microsoft SQL Server Management Studio**，然后单击 **Enter** 打开 SSMS。

   ![SQL Server Management Studio](./media/sql-database-connect-query-ssms/ssms.png)

3. 在“连接到服务器”对话框中选择一种 Active Directory 身份验证方法，然后提供相应的身份验证信息。 有关选择方法的信息，请参阅 [Azure Active Directory 身份验证](sql-database-aad-authentication.md)和 [Azure AD MFA 的 SSMS 支持](sql-database-ssms-mfa-authentication.md)。

   ![使用 AAD 连接到服务器](./media/sql-database-control-access-aad-authentication-get-started/connect_to_server_with_aad.png)

4. 输入所需的信息，以便能够使用 SQL Server 身份验证和服务器管理员帐户连接到 SQL Server。

5. 单击“连接”。

   ![已使用 AAD 连接到服务器](./media/sql-database-control-access-aad-authentication-get-started/connected_to_server_with_aad.png)

## <a name="view-the-server-admin-account-and-its-permissions"></a>查看服务器管理员帐户及其权限 
本操作方法指南部分介绍如何查看有关 master 数据库和用户数据库中服务器管理员帐户及其权限的信息。

1. 在对象资源管理器中，依次展开“数据库”、“系统数据库”、“master”、“安全性”、“用户”。 可以看到，在 master 数据库中已经为 Active Directory 管理员创建了一个用户帐户。 另外可以看到，并没有为 Active Directory 管理员用户帐户创建登录名。

   ![AAD 管理员的 master 数据库用户帐户](./media/sql-database-control-access-aad-authentication-get-started/master_database_user_account_for_AAD_admin.png)

   > [!NOTE]
   > 有关显示的其他用户帐户的信息，请参阅[主体](https://msdn.microsoft.com/library/ms181127.aspx)。
   >

2. 在对象资源管理器中右键单击“master”，然后单击“新建查询”打开一个连接到 master 数据库的查询窗口。
3. 在查询窗口中执行以下查询，返回有关执行查询的用户的信息。 可以看到，针对执行此查询的用户帐户返回了 user@microsoft.com（稍后使用此过程查询用户数据库时，将看到不同的结果）。

   ```
   SELECT USER;
   ```

   ![在 master 数据库中选择用户查询](./media/sql-database-control-access-aad-authentication-get-started/select_user_query_in_master_database.png)

4. 在查询窗口中执行以下查询，返回有关 Active Directory 管理员用户权限的信息。 可以看到，Active Directory 管理员用户有权连接到 master 数据库、创建登录名和用户、从 sys.sql_logins 表中选择信息，以及将用户添加到 dbmanager 和 dbcreator 数据库角色。 这些权限是对授予公共角色的权限的补充（所有用户从这些公共角色权限继承权限，例如，从特定的表中选择信息的权限）。 有关详细信息，请参阅[权限](https://msdn.microsoft.com/library/ms191291.aspx)。

   ```
   SELECT prm.permission_name
      , prm.class_desc
      , prm.state_desc
      , p2.name as 'Database role'
      , p3.name as 'Additional database role' 
   FROM sys.database_principals p
   JOIN sys.database_permissions prm
      ON p.principal_id = prm.grantee_principal_id
      LEFT JOIN sys.database_principals p2
      ON prm.major_id = p2.principal_id
      LEFT JOIN sys.database_role_members r
      ON p.principal_id = r.member_principal_id
      LEFT JOIN sys.database_principals p3
      ON r.role_principal_id = p3.principal_id
   WHERE p.name = 'user@microsoft.com';
   ```

   ![master 数据库中的 AAD 管理员权限](./media/sql-database-control-access-aad-authentication-get-started/aad_admin_permissions_in_master_database.png)

6. 在对象资源管理器中，依次展开“blankdb”、“安全性”、“用户”。 可以看到，此数据库中不存在名为 user@microsoft.com 的用户帐户。

   ![blankdb 中的用户帐户](./media/sql-database-control-access-aad-authentication-get-started/user_accounts_in_blankdb.png)

7. 在对象资源管理器中右键单击“blankdb”，然后单击“新建查询”。

8. 在查询窗口中执行以下查询，返回有关执行查询的用户的信息。 可以看到，已针对执行此查询的用户帐户返回了 dbo（默认情况下，服务器管理员登录名将映射到每个用户数据库中的 dbo 用户帐户）。

   ```
   SELECT USER;
   ```

   ![在 blankdb 数据库中选择用户查询](./media/sql-database-control-access-aad-authentication-get-started/select_user_query_in_blankdb_database.png)

9. 在查询窗口中执行以下查询，返回有关 dbo 用户权限的信息。 请注意，dbo 是公共角色的成员，也是 db_owner 固定数据库角色的成员。 有关详细信息，请参阅[数据库级角色](https://msdn.microsoft.com/library/ms189121.aspx)。

   ```
   SELECT prm.permission_name
      , prm.class_desc
      , prm.state_desc
      , p2.name as 'Database role'
      , p3.name as 'Additional database role' 
   FROM sys.database_principals AS p
   JOIN sys.database_permissions AS prm
      ON p.principal_id = prm.grantee_principal_id
      LEFT JOIN sys.database_principals AS p2
      ON prm.major_id = p2.principal_id
      LEFT JOIN sys.database_role_members r
      ON p.principal_id = r.member_principal_id
      LEFT JOIN sys.database_principals AS p3
      ON r.role_principal_id = p3.principal_id
   WHERE p.name = 'dbo';
   ```

   ![blankdb 数据库中的服务器管理员权限](./media/sql-database-control-access-aad-authentication-get-started/aad_admin_permissions_in_blankdb_database.png)

10. （可选）针对 AdventureWorksLT 用户数据库重复前面三个步骤。

## <a name="create-a-new-user-in-the-adventureworkslt-database-with-select-permissions"></a>在 AdventureWorksLT 数据库中创建具有 SELECT 权限的新用户

本操作方法指南部分介绍如何基于 Azure AD 用户的用户主体名称或者 Azure AD 组的显示名称在 AdventureWorksLT 数据库中创建一个用户帐户，测试此用户作为公共角色成员的权限，向此用户授予 SELECT 权限，然后再次测试此用户的权限。

> [!NOTE]
> 数据库级用户（[包含的用户](https://msdn.microsoft.com/library/ff929188.aspx)）可以提高数据库的可移植性，后续的操作方法指南将介绍此功能。
>

1. 在对象资源管理器中右键单击“AdventureWorksLT”，然后单击“新建查询”打开一个连接到 AdventureWorksLT 数据库的查询窗口。
2. 执行以下语句，在 AdventureWorksLT 数据库内为 Microsoft 域中名为 aaduser1 的用户创建一个用户帐户。

   ```
   CREATE USER [aaduser1@microsoft.com]
   FROM EXTERNAL PROVIDER;
   ```
   ![AdventureWorksLT 中的新用户 aaduser1@microsoft.com](./media/sql-database-control-access-aad-authentication-get-started/new_user_aaduser1@microsoft.com_aw.png)

3. 在查询窗口中执行以下查询，返回有关 user1 的权限的信息。 可以看到，user1 拥有的权限无非就是从公共角色继承的权限。

   ```
   SELECT prm.permission_name
      , prm.class_desc
      , prm.state_desc
      , p2.name as 'Database role'
      , p3.name as 'Additional database role' 
   FROM sys.database_principals AS p
   JOIN sys.database_permissions AS prm
      ON p.principal_id = prm.grantee_principal_id
      LEFT JOIN sys.database_principals AS p2
      ON prm.major_id = p2.principal_id
      LEFT JOIN sys.database_role_members r
      ON p.principal_id = r.member_principal_id
      LEFT JOIN sys.database_principals AS p3
      ON r.role_principal_id = p3.principal_id
   WHERE p.name = 'aaduser1@microsoft.com';
   ```

   ![用户数据库中的新用户权限](./media/sql-database-control-access-aad-authentication-get-started/new_user_permissions_in_user_database.png)

4. 执行以下查询，尝试以 user1 的身份查询 AdventureWorksLT 数据库中的某个表。

   ```
   EXECUTE AS USER = 'aaduser1@microsoft.com';  
   SELECT * FROM [SalesLT].[ProductCategory];
   REVERT;
   ```

   ![无 select 权限](./media/sql-database-control-access-aad-authentication-get-started/no_select_permissions.png)

5. 执行以下语句，向 user1 授予对 SalesLT 架构中 ProductCategory 表的 SELECT 权限。

   ```
   GRANT SELECT ON OBJECT::[SalesLT].[ProductCategory] to [aaduser1@microsoft.com];
   ```

   ![授予 select 权限](./media/sql-database-control-access-aad-authentication-get-started/grant_select_permissions.png)

6. 执行以下查询，尝试以 user1 的身份查询 AdventureWorksLT 数据库中的某个表。

   ```
   EXECUTE AS USER = 'aaduser1@microsoft.com';  
   SELECT * FROM [SalesLT].[ProductCategory];
   REVERT;
   ```

   ![select 权限](./media/sql-database-control-access-aad-authentication-get-started/select_permissions.png)

## <a name="create-a-database-level-firewall-rule-for-adventureworkslt-database-users"></a>针对 AdventureWorksLT 数据库用户创建数据库级防火墙规则

> [!NOTE]
> 如果完成了 SQL Server 身份验证相关操作方法指南中的等效过程 [SQL 身份验证和授权](sql-database-control-access-sql-authentication-get-started.md)，且正在使用具有相同 IP 地址的同一台计算机进行学习，则不需要完成此过程。
>

在本操作方法指南部分中，你要尝试使用新用户帐户通过一台具有不同 IP 地址的计算机登录，以服务器管理员身份创建数据库级防火墙规则，然后使用这个新的数据库级防火墙规则成功登录。 

> [!NOTE]
> [数据库级防火墙规则](sql-database-firewall-configure.md)可以提高数据库的可移植性，后续的操作方法指南将介绍此功能。
>

1. 在尚未创建服务器级防火墙规则的另一台计算机上，打开 SQL Server Management Studio。

   > [!IMPORTANT]
   > 请始终使用最新版本的 SSMS（[下载 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)）。 
   >

2. 在“连接到服务器”窗口中，输入服务器名称和身份验证信息，以便使用 aaduser1@microsoft.com 帐户通过 SQL Server 身份验证进行连接。 
    
   ![在不使用防火墙规则的情况下以 aaduser1@microsoft.com 身份进行连接 1](./media/sql-database-control-access-aad-authentication-get-started/connect_aaduser1_no_rule1.png)

3. 在“连接到服务器”对话框中单击“选项”，指定要连接到的数据库，然后在“连接属性”选项卡上的“连接到数据库”下拉框中键入“AdventureWorksLT”。
   
   ![在不使用防火墙规则的情况下以 aaduser1 身份进行连接 2](./media/sql-database-control-access-aad-authentication-get-started/connect_aaduser1_no_rule2.png)

4. 单击“连接”。 此时会出现一个对话框，告知从中尝试连接到 SQL 数据库的计算机上不存在允许访问该数据库的防火墙规则。 根据前面在配置防火墙时执行的步骤，会出现两种不同形式的对话框，但一般情况下会显示第一种形式的对话框。

   ![在不使用防火墙规则的情况下以 user1 身份进行连接 3](./media/sql-database-control-access-aad-authentication-get-started/connect_aaduser1_no_rule3.png)

   ![在不使用防火墙规则的情况下以 user1 身份进行连接 4](./media/sql-database-control-access-aad-authentication-get-started/connect_aaduser1_no_rule4.png)

   > [!NOTE]
   > 最新版本的 SSMS 提供相应的功能让订阅所有者和参与者登录到 Microsoft Azure 以及创建服务器级防火墙规则。
   > 

4. 复制此对话框中的客户端 IP 地址，以便在步骤 7 中使用。
5. 单击“取消”，但不要关闭“连接到服务器”对话框。
6. 切换回到已创建服务器级防火墙规则的计算机，然后使用服务器管理员帐户连接到服务器。
7. 在连接到 AdventureWorksLT 数据库的新查询窗口中，以服务器管理员的身份执行以下语句，通过使用步骤 4 中的 IP 地址执行 [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx)，来创建数据库级防火墙：

   ```
   EXEC sp_set_database_firewall_rule @name = N'AdventureWorksLTFirewallRule', 
     @start_ip_address = 'x.x.x.x', @end_ip_address = 'x.x.x.x';
   ```

   ![添加数据库级防火墙规则 4](./media/sql-database-control-access-aad-authentication-get-started/aaduser1_add_rule_aw.png)

8. 再次切换计算机，然后在“连接到服务器”对话框中单击“连接”，以 aaduser1 身份连接到 AdventureWorksLT。 

9. 在对象资源管理器，依次展开“数据库”、“AdventureWorksLT”、“表”。 可以看到，user1 仅有权查看单个表，即 **SalesLT.ProductCategory** 表。 

10. 在对象资源管理器中右键单击“SalesLT.ProductCategory”，然后单击“选择前 1000 行”。   

## <a name="next-steps"></a>后续步骤
- 有关 SQL 数据库中的访问和控制的概述，请参阅 [SQL 数据库访问和控制](sql-database-control-access.md)。
- 有关 SQL 数据库中的登录名、用户和数据库角色的概述，请参阅[登录名、用户和数据库角色](sql-database-manage-logins.md)。
- 有关数据库主体的详细信息，请参阅[主体](https://msdn.microsoft.com/library/ms181127.aspx)。
- 有关数据库角色的详细信息，请参阅[数据库角色](https://msdn.microsoft.com/library/ms189121.aspx)。
- 有关 SQL 数据库中的防火墙规则的详细信息，请参阅 [SQL 数据库防火墙规则](sql-database-firewall-configure.md)。


