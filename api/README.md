# Contrato HTTP do MVP

Base: backend `e0d47f2`, em 27/10/2026. [OpenAPI](openapi.json) descreve os mesmos endpoints. Base local `http://localhost:8080`; todos os caminhos abaixo começam em `/api/v1`. O contrato é documental: não instala Swagger UI nem adiciona `/v3/api-docs` ao servidor.

## Autenticação e acesso

Somente `POST /usuarios` e `POST /auth/login` são públicos. Demais operações exigem `Authorization: Bearer <accessToken>`. Token HS256, emissor e expiração verificados; conta e perfil atual no banco são conferidos nas operações. Não há refresh token, revogação ou logout no backend. Logout local remove a sessão do navegador.

| Método e caminho | Acesso | Sucesso | Erros relevantes |
| --- | --- | --- | --- |
| POST `/usuarios` | Público; cria cidadão | 201 `UsuarioResponse` | 400, 409 |
| POST `/auth/login` | Público | 200 `LoginResponse` | 400, 401 |
| GET `/auth/me` | Próprio usuário | 200 `UsuarioResponse` | 401 |
| POST `/ocorrencias` | Autenticado; autor vem do token | 201 `OcorrenciaResponse` | 400, 401 |
| GET `/ocorrencias` | Cidadão: próprias; administrador: todas | 200 `OcorrenciaPaginaResponse` | 400, 401 |
| GET `/ocorrencias/{id}` | Mesmo escopo da listagem | 200 `OcorrenciaResponse` | 400, 401, 404 |
| PATCH `/ocorrencias/{id}/status` | Administrador | 200 `OcorrenciaResponse` | 400, 401, 403, 404, 409 |
| PATCH `/ocorrencias/{id}/prioridade` | Administrador | 200 `OcorrenciaResponse` | 400, 401, 403, 404 |
| GET `/ocorrencias/indicadores` | Administrador | 200 contagens globais | 401, 403 |
| POST `/ocorrencias/{id}/fotografia` | Somente autor | 201 `FotografiaResponse` + `Location` | 400, 401, 403, 404, 409, 413, 503 |
| GET `/ocorrencias/{id}/fotografia` | Autor ou administrador | 200 bytes JPEG/PNG | 400, 401, 404, 503 |

Esses são erros tratados nos fluxos do MVP. Rejeições de método, mídia ou protocolo pelo framework podem produzir outros códigos; não assumir `ApiError` para qualquer falha de infraestrutura/proxy.

## Cadastro, login e usuário

Cadastro:

```json
{"nome":"Pessoa de demonstração","email":"cidadao@example.com","senha":"SenhaDemo123","telefone":null}
```

Nome obrigatório até 100 caracteres; email válido até 150; senha de 8 a 72 caracteres; telefone opcional até 20. Cadastro não promove perfil, mesmo com campos extras. Email duplicado retorna 409. Senha é armazenada como hash BCrypt, nunca retornada.

Login: `{"email":"cidadao@example.com","senha":"SenhaDemo123"}`. Resposta ilustrativa:

```json
{
  "accessToken":"<token retornado pelo servidor>",
  "tokenType":"Bearer",
  "expiresIn":3600,
  "usuario":{
    "id":"11111111-1111-4111-8111-111111111111",
    "nome":"Pessoa de demonstração",
    "email":"cidadao@example.com",
    "perfil":"CIDADAO",
    "telefone":null,
    "criadoEm":"2026-10-27T10:00:00"
  }
}
```

`GET /auth/me` retorna apenas o objeto `usuario` nesse formato. `expiresIn` é duração em segundos. Os exemplos são fictícios e não provisionam contas.

## Ocorrências

Criação JSON:

```json
{"categoria":"BURACO","descricao":"Buraco próximo à praça","latitude":-19.9167,"longitude":-43.9345,"enderecoAproximado":"Praça central"}
```

Categorias: `BURACO`, `ILUMINACAO`, `LIXO`, `ALAGAMENTO`, `CALCADA`, `SINALIZACAO`, `AMBIENTAL`. Descrição obrigatória não branca; o DTO do backend não define máximo de 1000 caracteres, embora a interface possa impor esse limite. Latitude de -90 a 90; longitude de -180 a 180; ambas obrigatórias. Endereço opcional até 255. Autor, status e prioridade são controlados pelo servidor.

Resposta ilustrativa de criação:

```json
{
  "id":"22222222-2222-4222-8222-222222222222",
  "usuarioId":"11111111-1111-4111-8111-111111111111",
  "categoria":"BURACO",
  "descricao":"Buraco próximo à praça",
  "latitude":-19.9167,
  "longitude":-43.9345,
  "enderecoAproximado":"Praça central",
  "status":"ENVIADA",
  "prioridade":null,
  "dataCriacao":"2026-10-27T10:10:00",
  "dataAtualizacao":null,
  "possuiFotografia":false
}
```

Não há nome, email ou telefone do autor. Datas do DTO são `LocalDateTime`, sem offset; não presumir UTC nem acrescentar `Z` ao interpretar datas antigas. JWT usa instantes. Padronizar todas as datas com offset seria mudança futura.

Listagem: `GET /ocorrencias?pagina=0&tamanho=20&status=ENVIADA&prioridade=ALTA`. Página começa em zero; tamanho de 1 a 100, padrão 20. Filtros opcionais por igualdade, combinados. Ordenação fixa `dataCriacao DESC, id DESC`. Não há filtro HTTP por categoria/autor nem ordenação arbitrária.

```json
{"conteudo":[],"pagina":0,"tamanho":20,"totalElementos":0,"totalPaginas":0}
```

Cada item é `OcorrenciaResponse`. Página vazia mantém totais. Percorrer `totalPaginas`; não tratar resposta como array direto. Ocorrência inexistente ou de outro cidadão recebe o mesmo 404.

Atualizações independentes:

```text
PATCH /ocorrencias/{id}/prioridade   {"prioridade":"ALTA"}
PATCH /ocorrencias/{id}/status       {"status":"EM_ANALISE"}
```

Prioridade: `BAIXA`, `MEDIA`, `ALTA`; não removível com `null`. Repetição tem sucesso sem mudar a data. Status: somente `ENVIADA → EM_ANALISE → RESOLVIDA`; repetir, saltar, voltar ou reabrir retorna 409. Os dois PATCHs não são uma transação conjunta. Mudanças efetivas atualizam `dataAtualizacao`.

Indicadores: `GET /ocorrencias/indicadores` retorna `{"enviadas":0,"emAnalise":0,"resolvidas":0}`. Inteiros de 64 bits, globais, sem filtros/paginação. Total é a soma dos três campos; não existe campo HTTP `totalOcorrencias`.

## Fotografias

Criar a ocorrência primeiro; depois enviar **uma** foto usando o ID retornado:

```powershell
curl.exe -X POST "http://localhost:8080/api/v1/ocorrencias/<id>/fotografia" -H "Authorization: Bearer <token>" -F "foto=@C:/caminho/foto.jpg;type=image/jpeg"
```

Não definir `Content-Type` manualmente com `FormData`: o cliente gera o boundary. Resposta 201 contém `id`, `tipoConteudo`, `tamanhoBytes`, `largura`, `altura`; `Location` aponta para o endpoint protegido da fotografia, sem URL pública.

JPEG/PNG com conteúdo real compatível, até 5 MiB, até 8192 pixels por lado e 16 milhões de pixels. A imagem é regravada sem metadados, com chave UUID gerada pelo servidor. Multipart: 5 MiB por arquivo e 6 MiB por requisição. Excesso rejeitado pelo parser retorna 413; validação de conteúdo retorna 400.

Consulta retorna bytes, `Content-Type: image/jpeg` ou `image/png`, `Cache-Control: no-store, private`, `X-Content-Type-Options: nosniff`. Usar busca autenticada e URL temporária do navegador; `<img>` direto não envia Bearer.

`possuiFotografia` só é verdadeiro em `DISPONIVEL`. Não há substituição ou exclusão por endpoint. Upload duplicado/pendente retorna 409; Storage desabilitado/indisponível pode retornar 503. Falha na foto não desfaz a ocorrência: guardar o ID e repetir somente a foto. Resultado incerto exige consultar detalhe: indicador verdadeiro confirma disponibilidade; falso não distingue ausência de recuperação pendente.

## Erros

```json
{"status":404,"erro":"Not Found","mensagem":"Ocorrência não encontrada.","caminho":"/api/v1/ocorrencias/22222222-2222-4222-8222-222222222222","timestamp":"2026-10-27T10:20:00"}
```

Usar status e contexto, sem depender de comparação da frase inteira. `timestamp` não contém offset. A distinção 403/404 depende da operação e das [regras](../business-rules/REGRAS-MVP.md).

Fontes: [controllers e DTOs](https://github.com/Zelar-App/zelar.app-backend/tree/e0d47f21e0439437c031d7610de12dd4dbbb1cd2/src/main/java/app/zelar), [tratamento de erros](https://github.com/Zelar-App/zelar.app-backend/blob/e0d47f21e0439437c031d7610de12dd4dbbb1cd2/src/main/java/app/zelar/shared/exception/GlobalExceptionHandler.java).
