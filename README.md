# APISales

API desenvolvida para gerenciar as vendas de um estabelecimento contendo produtos, categorias, usuários, pedidos, carrinhos de compras e geração de relatórios para o dono do estabeleciemnto.

Desenvolvida usando: 
- C# 12
- .NET 8
- ASP.NET Core
- Entity Framework Core
- FluentValidation
- FluentEmail
- AutoMapper
- PostgreSQL e MySQL
- Redis
- RabbitMQ
- Docker 
- XUnit
- NSubstitute


A motivação do projeto surgiu de um problema que o estabelecimento comercial da familia da minha namorada tinha para organizar os pedidos de final de ano. O aumento do fluxo de pedidos e pessoas nessas datas acabava sobrecarregando todo mundo e as anotações em papeis não eram mais suficientes.

## Detalhes Técnicos
A API consta com um sistema de autenticação e autorização usando **tokens JWT** com papeis [ADMIN, EMPLOYEE, CUSTOMER] que os usuários podem realizar dentro do sistema.
Apenas o ADMIN pode designar os papeis de EMPLOYEE e ADMIN para outros usuários.

Exemplos de endpoints que a API disponibiliza:
```bash
POST api/Auth/login - realiza o login com as credenciais de email e senha (criptografada) e recebe o token de acesso;

GET api/Products/ - busca por todos os produtos
GET api/Products?name=coca - busca por produtos que tem a palava 'coca' no nome
POST api/Products/ - cria um produto com base no body abaixo:
{
    name: "coca cola",
    description: "coca cola lata 350ml",
    value: 5.0,
    TypeValue: 1,
    stockQuantity: 10,
    imageUrl: "coca_cola_image.jpge",
    categoryId: 1
}

POST api/Orders/GenerateReport/{date:datetime} - gera um relatório de vendas de um certo dia
```

## Instalação
### Padrão
Não há muita necessidade de dificultar a instalação local dessa API, basta clonar no repositório e garantir que os pacotes estão instalados corretamente.

```bash
git clone git@github.com:migueltotti/Sales-API.git
```

Dentro da pasta do repositorio clonado execute o comando para garantir que todas as dependencias estão instaladas.
```bash
dotnet restore
```

Depois disso, basta configurar algumas váriaveis de ambiente customizadas para cada situação.
Para isso vá na camada de API e crie o arquivo .env, copie as váriaveis de ambientes existentes no arquivo .env.sample e altere seus valores.

```bash
# MySql
MYSQL_CONNECTION_STRING=PAST_YOUR_MYSQL_CONNECTION_STRING_HERE

# SendGrid
SENDGRID_API_KEY=PAST_YOUR_SANDGRID_API_KEY_HERE

# Email Sender
FROM_EMAIL=PAST_YOUR_EMAIL_HERE
FROM_NAME=PAST_YOUR_EMAIL_NAME_HERE

# JWT Secret Key
SECRET_KEY=PAST_YOUR_JWT_SECRET_KEY_HERE

# OpenSSL Keys
PRIVATE_KEY=PAST_YOUR_OPENSSL_SECRET_KEY_HERE
PUBLIC_KEY=PAST_YOUR_OPENSSL_SECRET_KEY_HERE
```

- O sendgrid é uma ferramenta externa de envio de emails necessária para enviar o relatório de pedidos gerado por data.<br>
- As chaves privadas e públicas servem para a criptografia das senhas dos usuários. **NUNCA EXPONHA A CHAVE PRIVADA!!**<br>
- A chave secreta do JWT também não deve ser compartilhada e serve para assinar o token gerado pela API.

Depois de configuar as váriaveis de ambiente é preciso adicionar a string de conexão do redis, do rabbitMQ e passar os valores de expiração do token, do refresh token, do emissor e da audiencia no arquivo appsettings.json localizado na camada de API.

```json
{
  "ConnectionStrings": {
    "Redis": "redis:6379",
    "RabbitMQ": "amqp://rabbit:5672"
  },
  "JWT": {
    "ValidAudience": "xxxx",
    "ValidIssuer": "yyyyy",
    "TokenValidityInMinutes": 30,
    "RefreshTokenValidityInMinutes": 60
  },
}
```

### Docker
Podemos ainda facilitar a utilização da API usando docker e docker-compose.
