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
