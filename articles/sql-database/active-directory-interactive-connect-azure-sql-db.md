---
title: Conexões de ActiveDirectoryInteractive com SQL | Microsoft Docs
description: Exemplo de código em C#, com explicações, para se conectar ao Banco de Dados SQL do Azure usando o modo SqlAuthenticationMethod.ActiveDirectoryInteractive.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi
ms.date: 03/12/2019
manager: craigg
ms.openlocfilehash: bc7274308b8a349d16866f107eac4a57e115be9e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58850179"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Conectar-se ao banco de dados SQL do Azure com autenticação multifator do Azure

Este artigo fornece um C# programa que se conecta ao banco de dados SQL. O programa usa a autenticação de modo interativo, que dá suporte a [autenticação multifator do Azure](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

Para obter mais informações sobre o suporte a autenticação multifator para as ferramentas do SQL, consulte [suporte do Azure Active Directory no SSDT SQL Server Data Tools ()](https://docs.microsoft.com/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>A autenticação multifator para o banco de dados SQL do Azure

A partir do .NET Framework versão 4.7.2, a enumeração [ `SqlAuthenticationMethod` ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) tem um novo valor: `ActiveDirectoryInteractive`. Em um cliente de C# programa, o valor de enumeração direciona o sistema a usar o modo interativo do Azure Active Directory (Azure AD) que dá suporte à autenticação multifator para se conectar a um banco de dados SQL do Azure. O usuário que executa o programa verá as seguintes caixas de diálogo:

* Uma caixa de diálogo que exibe um nome de usuário do Azure AD e solicita a senha do usuário.

   Se o domínio do usuário for federado com o Azure AD, essa caixa de diálogo não aparece, porque nenhuma senha é necessária.

   Se a política do Azure AD impõe a autenticação multifator no usuário, em seguida, duas caixas de diálogo são exibidas.

* Na primeira vez que um usuário percorre a autenticação multifator, o sistema exibe uma caixa de diálogo que solicita um número de telefone celular enviar mensagens de texto para. Cada mensagem fornece o *código de verificação* que o usuário deverá digitar na caixa de diálogo seguinte.

* Uma caixa de diálogo que solicita um código de verificação de autenticação multifator, que o sistema foi enviado para um telefone celular.

Para obter informações sobre como configurar o Azure AD para exigir a autenticação multifator, consulte [Introdução ao Azure a autenticação multifator na nuvem](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Para capturas de tela dessas caixas de diálogo, consulte [configurar a autenticação multifator para SQL Server Management Studio e o Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Você pode procurar APIs do .NET Framework com o [página do navegador de API .NET ferramenta](https://docs.microsoft.com/dotnet/api/).
>
> Você também pode pesquisar diretamente com o [opcional? termo =&lt;valor de pesquisa&gt; parâmetro](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod).

## <a name="configure-your-c-application-in-the-azure-portal"></a>Configurar seu aplicativo C# no portal do Azure

Antes de começar, você deve ter um [servidor do Banco de Dados SQL do Azure](sql-database-get-started-portal.md) criado e disponível.

### <a name="register-your-app-and-set-permissions"></a>Registrar seu aplicativo e definir permissões

Para usar a autenticação do Azure AD, o programa C# deverá se registrar como um aplicativo do Azure AD. Para registrar um aplicativo, você precisa ser administrador do Azure AD ou ter recebido uma função de *Desenvolvedor de Aplicativos* do Azure AD. Para obter mais informações sobre como atribuir funções, consulte [atribuir funções de administrador e não-administrador aos usuários com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

Concluir um registro de aplicativo gera e exibe uma **ID do aplicativo**. O programa deve incluir essa identificação para se conectar.

Para registrar e definir as permissões necessárias para seu aplicativo:

1. No portal do Azure, selecione **Azure Active Directory** > **Registros do aplicativo** > **Novo registro de aplicativo**.

    ![Registro do aplicativo](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Depois que o registro do aplicativo é criado, o **ID do aplicativo** valor é gerado e exibido.

    ![ID do aplicativo exibida](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Selecione **aplicativo registrado** > **configurações** > **as permissões necessárias** > **adicionar**.

    ![Configurações de permissões do aplicativo registrado](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Selecione **permissões necessárias** > **Add** > **selecionar uma API** > **banco de dados do SQL do Azure**.

    ![Adicionar acesso à API para o Banco de Dados SQL do Azure](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Selecione **acesso à API** > **selecionar permissões** > **permissões delegadas**.

    ![Delegar permissões à API para o Banco de Dados SQL do Azure](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Definir um administrador do Azure AD para seu servidor do Banco de Dados SQL

Para o C# programa para ser executado, um administrador do servidor SQL do Azure precisa atribuir um administrador do AD do Azure para o servidor de banco de dados SQL. 

Sobre o **SQL Server** página, selecione **administrador do Active Directory** > **definir administrador**.

Para obter mais informações sobre usuários e administradores do Azure AD para o banco de dados SQL, consulte as capturas de tela [configurar e gerenciar a autenticação do Active Directory do Azure com o banco de dados SQL](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Adicionar um usuário não administrador a um banco de dados (opcional)

Um administrador do Azure AD para um servidor do Banco de Dados SQL pode executar o programa C# de exemplo. Um usuário do Azure AD poderá executar o programa se eles estiverem no banco de dados. Um administrador do SQL do Azure AD ou um usuário do Azure AD que já exista no banco de dados e tenha a permissão `ALTER ANY USER` no banco de dados poderá adicionar um usuário.

Você pode adicionar um usuário de banco de dados com o comando SQL [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql). Um exemplo é `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Para obter mais informações, confira [Usar a Autenticação do Azure Active Directory para autenticação com o Banco de Dados SQL, a Instância Gerenciada ou o SQL Data Warehouse](sql-database-aad-authentication.md).

## <a name="new-authentication-enum-value"></a>Novo valor de enumeração de autenticação

O exemplo C# depende do namespace [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient). De especial interesse para a autenticação multifator é enum `SqlAuthenticationMethod`, que tem os seguintes valores:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Use esse valor com um nome de usuário do AD do Azure para implementar a autenticação multifator. Esse valor é o foco deste artigo. Ele produz uma experiência interativa, exibindo caixas de diálogo para a senha do usuário e, em seguida, para a validação de autenticação multifator, se a autenticação multifator é imposta sobre este usuário. Esse valor está disponível a partir do .NET Framework versão 4.7.2.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Use esse valor para uma conta *federada*. Para uma conta federada, o nome de usuário é conhecido pelo domínio do Windows. Esse método de autenticação não oferece suporte a autenticação multifator.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Use esse valor para a autenticação que requer um nome de usuário e uma senha do Azure AD. O Banco de Dados SQL do Azure faz a autenticação. Esse método não dá suporte a autenticação multifator.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Defina os valores de parâmetro de C# do portal do Azure

Para o programa C# ser executado com êxito, você precisará atribuir os valores adequados aos campos estáticos. Campos com valores de exemplo são mostradas aqui. Também mostrados são os locais de portal do Azure onde você pode obter os valores necessários.

| Nome do campo estático | Valor de exemplo | Onde encontrar no portal do Azure |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **Servidores SQL** > **Filtrar por nome** |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | **Azure Active Directory** > **Usuário** > **Novo usuário convidado** |
| Initial_DatabaseName | "myDatabase" | **Servidores SQL** > **Bancos de dados SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory** > **Registros de aplicativo** > **Pesquisar por nome** > **ID do aplicativo** |
| RedirectUri | new Uri("https://mywebserver.com/") | **O Azure Active Directory** > **registros de aplicativo** > **pesquisar por nome** > *[Your-App-registration]*  >  **Configurações** > **RedirectURIs**<br /><br />Neste artigo, qualquer valor válido é bom para o RedirectUri, porque ele não é usado aqui. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Verificar com o SQL Server Management Studio

Antes de executar o C# programa, ele é uma boa ideia verificar se o programa de instalação e as configurações estão corretas no SQL Server Management Studio (SSMS). Qualquer falha do programa C# então pode ser limitada ao código-fonte.

### <a name="verify-sql-database-firewall-ip-addresses"></a>Verificar os endereços IP do firewall do Banco de Dados SQL

Execute o SSMS no mesmo computador e no mesmo local em que você planeja executar o programa C#. Para este teste, qualquer **autenticação** modo é Okey. Se não houver nenhuma indicação de que o firewall do servidor do banco de dados não está aceitando seu endereço IP, veja [regras de firewall no nível de servidor do Banco de Dados SQL do Azure e no nível do firewall do banco de dados](sql-database-firewall-configure.md) para obter ajuda.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Verifique se a autenticação multifator do Azure Active Directory

Execute o SSMS novamente, dessa vez com a **Autenticação** definida como **Active Directory – Universal com suporte de MFA**. Essa opção exige o SSMS versão 17.5 ou posterior.

Para obter mais informações, consulte [configurar a autenticação multifator para SSMS e o Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!NOTE]
> Se você for um usuário convidado no banco de dados, você também precisará fornecer o nome de domínio do AD do Azure para o banco de dados: Selecione **opções** > **ID de locatário ou nome de domínio do AD**. Para localizar o nome de domínio no portal do Azure, selecione **Azure Active Directory** > **Nomes de domínio personalizados**. No programa C# de exemplo, não é necessário fornecer um nome de domínio.

## <a name="c-code-example"></a>Exemplo de código C#

O programa C# de exemplo depende do assembly DLL [*Microsoft.IdentityModel.Clients.ActiveDirectory*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory).

Para instalar esse pacote, no Visual Studio, selecione **Projeto** > **Gerenciar Pacotes NuGet**. Pesquise e instale **Microsoft.IdentityModel.Clients.ActiveDirectory**.

Este é um exemplo de C# código-fonte.

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your SQL DB server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

Isso é um exemplo de como o C# saída de teste.

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é compatível com o banco de dados SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
