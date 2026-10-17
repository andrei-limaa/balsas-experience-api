# Balsas Experience API

API REST de experiências turísticas em Balsas-MA, desenvolvida com Node.js e Express para o laboratório da Aula 07 de Programação para Web. Permite listar, consultar, cadastrar, atualizar e remover atrações, com filtros por categoria e tag. Versão do projeto: **1.0.0**.

## Identificação acadêmica

| Campo | Informação |
| --- | --- |
| Instituição | Centro Universitário Unibalsas |
| Curso | Análise e Desenvolvimento de Sistemas (ADS) |
| Disciplina | Programação para Web |
| Professor | Marcos David |
| Aluno responsável | Andrei Pereira Lima |
| Matrícula | 25.1.06139 |
| Modalidade | Trabalho individual |

## Links

- [Repositório no GitHub](https://github.com/andrei-limaa/balsas-experience-api)
- [API publicada no Render](https://balsas-experience-api-3ubq.onrender.com)
- [Saúde da API](https://balsas-experience-api-3ubq.onrender.com/health)
- [Atrações](https://balsas-experience-api-3ubq.onrender.com/atracoes)

## Tecnologias e versões

Versões das dependências verificadas no `package-lock.json`:

| Tecnologia | Versão | Uso |
| --- | --- | --- |
| Node.js | Ambiente local verificado: 24.14.1 | Execução JavaScript com módulos ES |
| Express | 5.2.1 | Servidor HTTP e rotas REST |
| Prisma CLI e Prisma Client | 7.10.0 | Modelos, migrações e cliente do banco |
| @prisma/adapter-pg | 7.10.0 | Adaptador PostgreSQL para Prisma |
| pg | 8.23.1 | Driver PostgreSQL |
| dotenv | 18.0.5 | Leitura do `.env` na configuração do Prisma |
| PostgreSQL / Neon | Versão do servidor não fixada no projeto | Banco configurado para Prisma |
| Render | Serviço de hospedagem | Publicação da API |

**Persistência atual:** os endpoints usam `src/data/atracoes.json` e mantêm alterações em memória. O projeto inclui configuração, modelos, migrações e seed para PostgreSQL, mas os controllers atuais não utilizam o banco. Reiniciar o servidor restaura as oito atrações iniciais.

## Pré-requisitos

- Git instalado.
- Node.js compatível com Prisma 7, como 22.12+ na linha 22 ou 24.x LTS, com npm.
- PostgreSQL local ou Neon para executar migrações e seed; o banco não é necessário para as rotas atuais em memória.
- Opcional: VS Code com a extensão REST Client para usar `requisicoes.http`.

## Clonar e instalar

```sh
git clone https://github.com/andrei-limaa/balsas-experience-api.git
cd balsas-experience-api
npm ci
```

`npm ci` instala as versões registradas no arquivo de lock.

## Variáveis de ambiente

Para usar o Prisma, crie `.env` na raiz do projeto, junto ao `package.json`. Exemplo com valores fictícios:

```dotenv
DATABASE_URL="postgresql://USUARIO:SENHA@HOST/BANCO?sslmode=require"
```

Obtenha a URL real no painel do Neon e substitua os valores no seu ambiente. O `.env` está no `.gitignore`; não inclua credenciais reais em commits ou na documentação.

`src/prisma.js` e `prisma7.config.ts` carregam o `.env` com dotenv. O `src/app.js` não carrega esse arquivo automaticamente. Ele utiliza `PORT` do ambiente e, na ausência dela, a porta 3000. Para carregar explicitamente um `.env` ao iniciar:

```sh
node --env-file=.env src/app.js
```

Nesse caso, você pode adicionar `PORT=3000` ao `.env`.

## Executar localmente

Desenvolvimento, com reinício ao alterar arquivos:

```sh
npm run dev
```

Inicialização normal:

```sh
npm run start
```

Acesse http://localhost:3000. Em http://localhost:3000/health, a resposta esperada é:

```json
{ "status": "ok" }
```

No PowerShell, se a política de execução bloquear `npm`, use `npm.cmd`, por exemplo: `npm.cmd run start`.

## Prisma e PostgreSQL

Com `DATABASE_URL` configurada, gere o cliente:

```sh
npm run build
```

Aplique as migrações existentes ao banco configurado:

```sh
npm run migrate:deploy
```

Para inserir os dados de exemplo no banco:

```sh
npm run seed
```

Migrações e seed alteram o banco apontado por `DATABASE_URL`. O start não executa essas operações automaticamente. Preparar o banco não muda a persistência em memória dos endpoints atuais.

## Endpoints

URL base local: `http://localhost:3000`.
URL base publicada: `https://balsas-experience-api-3ubq.onrender.com`.

| Método | Caminho | Resposta de sucesso |
| --- | --- | --- |
| GET | `/` | 200 — identificação da API |
| GET | `/health` | 200 — saúde da aplicação |
| GET | `/atracoes` | 200 — lista de atrações |
| GET | `/atracoes?categoria=Lazer` | 200 — filtro por categoria |
| GET | `/atracoes?tag=cachoeira` | 200 — filtro por tag |
| GET | `/atracoes/:id` | 200 — atração encontrada |
| POST | `/atracoes` | 201 — criação, com cabeçalho `Location` |
| PUT | `/atracoes/:id` | 200 — substituição da atração |
| DELETE | `/atracoes/:id` | 204 — remoção, sem corpo |

Os filtros podem ser combinados e ignoram maiúsculas e minúsculas. Exemplo: `/atracoes?categoria=Lazer&tag=cachoeira`.

### Corpo para criar ou atualizar

POST e PUT exigem `nome`, `categoria` e `descricao`, com corpo JSON e cabeçalho `Content-Type: application/json`:

```json
{
  "nome": "Parque de Lazer",
  "categoria": "Lazer",
  "descricao": "Espaço destinado ao lazer e à convivência.",
  "localizacao": { "cidade": "Balsas", "estado": "MA" },
  "distanciaCentroKm": 2,
  "tags": ["lazer", "família"]
}
```

Sem os campos opcionais, a localização será Balsas/MA, a distância será `null` e as tags serão `[]`. O servidor atribui o ID na criação. PUT substitui os dados anteriores mantendo o ID informado na URL.

### Erros

- `400`: ausência de campos obrigatórios em POST/PUT ou JSON malformado.
- `404`: atração inexistente ou rota não encontrada.
- `500`: erro interno no servidor.

## Testar a API

Com o servidor iniciado, abra `requisicoes.http` no VS Code com REST Client e execute as requisições em sequência. Para atualização e remoção, confira o ID da atração usada no teste.

Também é possível consultar pelo terminal:

```sh
curl http://localhost:3000/health
curl http://localhost:3000/atracoes
```

No PowerShell, use `curl.exe` se `curl` resolver para outro comando. Para testar a API publicada, substitua a URL base pelo endereço do Render. Testes de escrita alteram a memória do processo atual; reinicie o servidor para restaurar a lista inicial.

## Publicar no Render

Conecte o repositório a um Web Service do Render e configure:

| Configuração | Valor |
| --- | --- |
| Branch | `main` |
| Build Command | `npm ci --include=dev && npm run build` |
| Start Command | `npm run start` |
| Health Check Path | `/health` |
| Variáveis de ambiente | `NODE_ENV=production` e `DATABASE_URL` com a URL do Neon |

Use uma versão compatível do Node.js, como 24.x LTS. O build gera o Prisma Client usando `prisma7.config.ts`. Para aplicar migrações, execute `npm run migrate:deploy` em uma etapa de implantação com as dependências de desenvolvimento instaladas.

O servidor utiliza `process.env.PORT` e escuta em `0.0.0.0`, permitindo conexões externas. Deixe o Render fornecer a porta. Configure os segredos no painel do serviço; não envie o `.env`. `/health` verifica a API sem consultar o banco.

## Organização do projeto

| Caminho | Responsabilidade |
| --- | --- |
| `src/app.js` | Servidor Express, rotas iniciais e tratamento de erros |
| `src/routes/atracoes.routes.js` | Endpoints das atrações |
| `src/controllers/atracoes.controller.js` | Regras e manipulação das atrações em memória |
| `src/data/atracoes.json` | Oito atrações iniciais |
| `src/prisma.js` | Instância do Prisma com adaptador PostgreSQL |
| `prisma/schema.prisma` | Modelos do banco |
| `prisma/migrations/` | Migrações PostgreSQL |
| `prisma/seed.js` | Dados de exemplo para o banco |
| `prisma7.config.ts` | Configuração da CLI do Prisma |
| `requisicoes.http` | Requisições para testes manuais |
| `package.json` e `package-lock.json` | Scripts, dependências e versões instaladas |
