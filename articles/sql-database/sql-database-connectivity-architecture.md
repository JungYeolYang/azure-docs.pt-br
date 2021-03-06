---
title: Direcionando o tráfego do Azure para o Banco de Dados SQL do Azure e o SQL Data Warehouse | Microsoft Docs
description: Este documento explica a arquitetura de onnectivity Azcure SQL para conexões de banco de dados de dentro do Azure ou de fora do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 04/03/2019
ms.openlocfilehash: 4ff6cc0ba18074f353eb5b99af7052edd658a80e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799262"
---
# <a name="azure-sql-connectivity-architecture"></a>Arquitetura de conectividade do SQL do Azure

Este artigo explica a arquitetura de conectividade do Banco de Dados SQL do Azure e do SQL Data Warehouse, além de como os diferentes componentes funcionam para direcionar o tráfego para a instância do SQL do Azure. Esses componentes de conectividade funcionam para direcionar o tráfego de rede para o Banco de Dados SQL do Azure ou o SQL Data Warehouse com clientes que se conectam dentro e fora do Azure. Este artigo também fornece exemplos de script para alterar o modo como ocorre a conectividade e as considerações relacionadas à alteração das configurações de conectividade padrões.

## <a name="connectivity-architecture"></a>Arquitetura de conectividade

O diagrama a seguir fornece uma visão geral de alto nível da arquitetura de conectividade do Banco de Dados SQL do Azure.

![visão geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-overview.png)

As seguintes etapas descrevem como uma conexão é estabelecida com um banco de dados SQL do Azure:

- Os clientes se conectam ao gateway, que tem um endereço IP público e escuta na porta 1433.
- O gateway, dependendo da política de conexão efetiva, redireciona o tráfego ou usa um proxy para ele para o cluster de banco de dados correto.
- No cluster de banco de dados, o tráfego é encaminhado para o banco de dados SQL do Azure apropriado.

## <a name="connection-policy"></a>Política de Conexão

O Banco de Dados SQL do Azure oferece suporte às três opções a seguir para a configuração de diretiva de conexão de um servidor do Banco de Dados SQL:

- **Redirecionamento (recomendado):** Os clientes estabelecem conexões diretamente com o nó que hospeda o banco de dados. Para habilitar a conectividade, os clientes devem permitir a todos os endereços IP do Azure na região usando grupos de segurança de rede (NSG) com as regras de firewall de saída [marcas de serviço](../virtual-network/security-overview.md#service-tags)) para portas de 11000-11999, não apenas o IP do gateway do banco de dados SQL endereços na porta 1433. Como os pacotes vão diretamente para o banco de dados, a latência e o rendimento melhoraram o desempenho.
- **Proxy:** Nesse modo, todas as conexões usam um proxy por meio dos gateways do Banco de Dados SQL do Azure. Para habilitar a conectividade, o cliente precisa ter regras de firewall de saída que permitam apenas os endereços IP do gateway do Banco de Dados SQL do Azure (geralmente, dois endereços IP por região). A escolha desse modo pode resultar em maior latência e menor rendimento, dependendo da natureza da carga de trabalho. É altamente recomendável usar a política de conexão `Redirect` em relação à política de conexão `Proxy` para a menor latência e maior taxa de transferência.
- **Padrão:** Essa é a política de conexão em vigor em todos os servidores após a criação, a menos que você altere explicitamente a política de conexão para `Proxy` ou `Redirect`. A política efetiva depende se as conexões se originam no Azure (`Redirect`) ou fora do Azure (`Proxy`).

## <a name="connectivity-from-within-azure"></a>Conectividade de dentro do Azure

Se você estiver se conectando de dentro do Azure, as conexões terão uma política de conexão de `Redirect` por padrão. Uma política de `Redirect` significa que, após a sessão TCP ser estabelecida com o Banco de Dados SQL do Azure, a sessão do cliente será redirecionada para o cluster de banco de dados correto com uma alteração do IP virtual de destino daquele do gateway do Banco de Dados SQL do Azure para aquele do cluster. Depois disso, todos os pacotes seguintes fluem diretamente para o cluster, ignorando o gateway do Banco de Dados SQL do Azure. O diagrama a seguir ilustra esse fluxo de tráfego.

![visão geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Conectividade de fora do Azure

Se você estiver se conectando de fora do Azure, as conexões terão uma política de conexão de `Proxy` por padrão. Uma política de `Proxy` significa que a sessão TCP é estabelecida por meio do gateway do Banco de Dados SQL do Azure e todos os pacotes seguintes fluem por meio do gateway. O diagrama a seguir ilustra esse fluxo de tráfego.

![visão geral da arquitetura](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Endereços IP de gateway do Banco de Dados SQL do Azure

Para se conectar a um banco de dados SQL do Azure a partir de recursos locais, você precisa permitir o tráfego de rede de saída para o gateway do Banco de Dados SQL do Azure para a sua região do Azure. As conexões fluem somente por meio do gateway ao se conectar no modo de `Proxy`, que é o padrão ao se conectar por meio de recursos locais.

A tabela a seguir lista os IPs primários e secundários do gateway do Banco de Dados SQL do Azure para todas as regiões de dados. Para algumas regiões, há dois endereços IP. Nessas regiões, o endereço IP primário é o endereço IP atual do gateway e o segundo endereço IP é um endereço IP de failover. O failover é o endereço para o qual podemos pode mover seu servidor para manter a alta disponibilidade do serviço. Para essas regiões, é recomendável que você permita a saída para ambos os endereços IP. O segundo endereço IP pertence à Microsoft e não escutará nenhum serviço até que ele seja ativado pelo Banco de Dados SQL do Azure para aceitar conexões.

| Nome da Região | Endereço IP primário | Endereço IP secundário |
| --- | --- |--- |
| Leste da Austrália | 13.75.149.87 | 40.79.161.1 |
| Sudeste da Austrália | 191.239.192.109 | 13.73.109.251 |
| Sul do Brasil | 104.41.11.5 | |
| Canadá Central | 40.85.224.249 | |
| Leste do Canadá | 40.86.226.166 | |
| Centro dos EUA | 23.99.160.139 | 13.67.215.62 |
| Leste da China 1 | 139.219.130.35 | |
| Leste da China 2 | 40.73.82.1 | |
| Norte da China 1 | 139.219.15.17 | |
| Norte da China 2 | 40.73.50.0 | |
| Ásia Oriental | 191.234.2.139 | 52.175.33.150 |
| Leste dos EUA 1 | 191.238.6.43 | 40.121.158.30 |
| Leste dos EUA 2 | 191.239.224.107 | 40.79.84.180 * |
| França Central | 40.79.137.0 | 40.79.129.1 |
| Alemanha Central | 51.4.144.100 | |
| Nordeste da Alemanha | 51.5.144.179 | |
| Centro da Índia | 104.211.96.159 | |
| Sul da Índia | 104.211.224.146 | |
| Oeste da Índia | 104.211.160.80 | |
| Leste do Japão | 191.237.240.43 | 13.78.61.196 |
| Oeste do Japão | 191.238.68.11 | 104.214.148.156 |
| Coreia Central | 52.231.32.42 | |
| Sul da Coreia | 52.231.200.86 |  |
| Centro-Norte dos EUA | 23.98.55.75 | 23.96.178.199 |
| Norte da Europa | 191.235.193.75 | 40.113.93.91 |
| Centro-Sul dos Estados Unidos | 23.98.162.75 | 13.66.62.124 |
| Sudeste da Ásia | 23.100.117.95 | 104.43.15.0 |
| Sul do Reino Unido | 51.140.184.11 | |
| Oeste do Reino Unido | 51.141.8.11| |
| Centro-Oeste dos EUA | 13.78.145.25 | |
| Europa Ocidental | 191.237.232.75 | 40.68.37.158 |
| Oeste dos EUA 1 | 23.99.34.75 | 104.42.238.205 |
| Oeste dos EUA 2 | 13.66.226.202 | |
||||

\* **OBSERVAÇÃO:** O *Leste dos EUA 2* também tem um endereço IP terciário igual a `52.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Alterar a política de conexão do Banco de Dados SQL do Azure

Para alterar a política de conexão de Banco de Dados SQL do Azure para um servidor do Banco de Dados SQL do Azure, use o comando [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

- Se a política de conexão for definida como `Proxy`, todos os pacotes de rede fluirão por meio do gateway do Banco de Dados SQL do Azure. Para essa configuração, você precisa permitir a saída para o IP de gateway do Banco de Dados SQL do Azure. O uso de uma configuração igual a `Proxy` resulta em uma latência maior do que uma configuração igual a `Redirect`.
- Se a política de conexão estiver definida como `Redirect`, todos os pacotes de rede fluirão diretamente para o cluster do banco de dados. Para essa configuração, você precisa permitir a saída para vários IPs.

## <a name="script-to-change-connection-settings-via-powershell"></a>Script para alterar as configurações de conexão via PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é compatível com o banco de dados SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

> [!IMPORTANT]
> Este script requer o [módulo do Azure PowerShell](/powershell/azure/install-az-ps).

O script do PowerShell a seguir mostra como alterar a política de conexão.

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Script para mudar as configurações de conexão pela CLI do Azure

> [!IMPORTANT]
> Este script requer a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

O script da CLI a seguir mostra como alterar a política de conexão.

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $id

# Update connection policy
az resource update --ids $id --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como alterar a política de conexão de Banco de Dados SQL do Azure para um servidor do Banco de Dados SQL do Azure, consulte [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Para obter mais informações sobre o comportamento de conexão do Banco de Dados SQL do Azure para clientes que usam ADO.NET 4.5 ou uma versão mais recente, consulte [Portas depois da 1433 para ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Para obter informações sobre a visão geral do desenvolvimento de aplicativos em geral, consulte [Visão Geral do Desenvolvimento de Aplicativos do Banco de Dados SQL](sql-database-develop-overview.md).
