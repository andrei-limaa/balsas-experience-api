# Balsas Experience API

API REST de experiências turísticas em Balsas-MA, criada com Node.js e Express para o laboratório da Aula 07.

## Executar

Requer Node.js 22 ou superior.

```sh
npm install
npm run dev
```

Para executar sem reinício automático, use `npm start`. No PowerShell, caso a política de execução bloqueie `npm`, use `npm.cmd run dev` ou `npm.cmd start`.

A API responde em http://localhost:3000. A variável de ambiente `PORT` permite escolher outra porta. O arquivo `.env` não é carregado automaticamente.

## Publicar no Render

Configure Node.js 22.12 ou superior, `DATABASE_URL` com a URL do Neon e `NODE_ENV=production` nas variáveis de ambiente do Render. Não publique o arquivo `.env`.

- Build Command: `npm ci --include=dev && npm run build`
- Start Command: `npm run start`
- Health Check Path: `/health`

O build gera o Prisma Client usando `prisma7.config.ts`. Para aplicar as migrações ao banco, execute `npm run migrate:deploy` em uma etapa de implantação com as dependências de desenvolvimento instaladas. Esse comando altera o esquema do banco; o start não executa migrações nem seed automaticamente.

O servidor utiliza a porta fornecida pelo Render e escuta em `0.0.0.0`. As rotas atuais continuam usando JSON em memória; a configuração do Prisma está disponível em `src/prisma.js`, mas não é utilizada por essas rotas. `/health` verifica a API, sem consultar o banco.

## Endpoints

| Método | Caminho | Resposta |
| --- | --- | --- |
| GET | `/` | Identificação da API |
| GET | `/health` | Saúde da aplicação |
| GET | `/atracoes` | Lista de atrações |
| GET | `/atracoes?categoria=Lazer` | Filtro por categoria |
| GET | `/atracoes?tag=cachoeira` | Filtro por tag |
| GET | `/atracoes/:id` | Uma atração ou 404 |
| POST | `/atracoes` | Cria uma atração, retorna 201 e Location |
| PUT | `/atracoes/:id` | Substitui uma atração |
| DELETE | `/atracoes/:id` | Remove uma atração, retorna 204 sem corpo |

Os filtros podem ser combinados e ignoram maiúsculas e minúsculas. POST e PUT exigem `nome`, `categoria` e `descricao`, com corpo JSON e `Content-Type: application/json`.

```json
{
  "nome": "Parque de Lazer",
  "categoria": "Lazer",
  "descricao": "Espaço destinado ao lazer e à convivência.",
  "tags": ["lazer", "família"]
}
```

Sem os campos opcionais, a localização será Balsas/MA, a distância será `null` e as tags serão `[]`. PUT substitui o conteúdo anterior mantendo o id do endereço.

As oito atrações iniciais vêm de `src/data/atracoes.json`. Os dados são didáticos e as alterações ficam apenas em memória: reiniciar o servidor restaura a lista inicial.

## Organização e testes

`src/app.js` configura o servidor e os erros; `src/routes` define os endpoints; `src/controllers` aplica as regras; `src/data` contém os dados iniciais.

Abra `requisicoes.http` com a extensão REST Client do VS Code e execute as requisições em sequência com o servidor recém-iniciado.
