# Aplica-o-Serveless
Aplicação Serveless DIO


# Criação do Banco de Dados:

No portal Azure, crie um Azure SQL Database.
Configure o servidor SQL e registre o nome de usuário e senha.
executar o script schema.sql e criar a tabela Clientes.

## Crie uma nova aplicação Azure Functions:

### Instale a CLI do Azure Functions para desenvolvimento local.

Utilize o codigo:
func init my-serverless-app --worker-runtime node
cd my-serverless-app
func new --template "HTTP trigger" --name ClienteAPI

Edite o arquivo ClienteAPI/index.js (ou run.py para Python) para incluir conexões ao banco de dados:

javascript
const sql = require("mssql");

const config = {
    user: process.env.DB_USER,
    password: process.env.DB_PASSWORD,
    server: process.env.DB_SERVER, // Exemplo: "seu-servidor.database.windows.net"
    database: process.env.DB_NAME,
    options: {
        encrypt: true,
        enableArithAbort: true,
    },
};

module.exports = async function (context, req) {
    try {
        // Conectar ao banco
        await sql.connect(config);

        // Consultar clientes ativos
        const result = await sql.query`SELECT ClienteID, Nome, Email, DataCadastro FROM Clientes WHERE Status = 'ativo'`;
        
        context.res = {
            status: 200,
            body: result.recordset,
        };
    } catch (err) {
        context.res = {
            status: 500,
            body: `Erro: ${err.message}`,
        };
    }
};
Atualize as variáveis de ambiente no arquivo local.settings.json:
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "FUNCTIONS_WORKER_RUNTIME": "node",
        "DB_USER": "seu-usuario",
        "DB_PASSWORD": "sua-senha",
        "DB_SERVER": "seu-servidor.database.windows.net",
        "DB_NAME": "seu-banco"
    }
}

Execute o projeto localmente:
func start
Acesse a função via navegador ou Postman:
http://localhost:7071/api/ClienteAPI

### Implantação na Azure
Criação do Function App:

##### No portal Azure, crie um Function App.
Escolha o plano de consumo para escalabilidade automática.
func azure functionapp publish nome-da-sua-aplicacao

# Acesse a URL do Azure Functions gerada (geralmente no formato https://<nome-da-aplicacao>.azurewebsites.net/api/ClienteAPI).
# Realize chamadas HTTP para obter clientes, consultar pelo email ou contar ativos e inativos.

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Extensões Futuras
Adicionar endpoints:

POST: Inserir novos clientes.
PUT: Atualizar dados de um cliente.
DELETE: Remover clientes.
Monitoramento:

Use o Application Insights para rastrear erros e desempenho.
Documentação:

Integre com Swagger ou Azure API Management para documentar sua API.
