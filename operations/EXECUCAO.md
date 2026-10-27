# Execução e configuração

Versões de referência no [índice](../README.md). Objetivo: reproduzir o MVP em ambiente local; este guia não executa instalação nem altera banco compartilhado.

## Pré-requisitos

- Git; JDK 21 configurado em `JAVA_HOME` e no IntelliJ; Maven Wrapper do backend.
- Node.js 24 e npm; instalar dependências pelo `package-lock.json`.
- PostgreSQL acessível. PostgreSQL 17 é a versão dos ensaios e do serviço temporário de CI, não uma alegação de suporte exclusivo.
- Bucket privado no Supabase apenas para validar fotos. O restante funciona com fotos desabilitadas.

Clonar os repositórios backend e frontend e selecionar os commits do índice para reprodução exata, ou atualizar `develop` e registrar os novos SHAs antes de ensaiar. Não misturar versões sem rever contratos.

## Backend e banco

Criar banco vazio e usuário com permissões de criar/aplicar migrations no **ambiente local escolhido**. Não apontar ensaios destrutivos para produção ou banco compartilhado. Configure no ambiente do processo ou em Run/Debug Configurations → Environment variables, no IntelliJ:

| Variável | Valor de referência / finalidade |
| --- | --- |
| DB_URL | `jdbc:postgresql://localhost:5432/zelar` se o banco estiver nessa máquina/porta |
| DB_USERNAME / DB_PASSWORD | Credenciais do PostgreSQL escolhido |
| JWT_SECRET | Pelo menos 32 bytes aleatórios em Base64 |
| JWT_ISSUER | `zelar.app` por padrão |
| JWT_TOKEN_DURATION | `PT1H` por padrão |
| SERVER_PORT | `8080` por padrão |
| SUPABASE_PUBLISHABLE_KEY | Campo previsto no modelo; não substitui JWT nem secret do Storage; pode ficar vazio quando não utilizado |
| FOTOGRAFIAS_HABILITADAS | `false` inicialmente |

`localhost` só está correto se o banco estiver no mesmo host do processo. Supabase remoto exige URL/usuário/senha correspondentes ao projeto e ao modo de conexão. Não presumir que credencial do Storage seja senha PostgreSQL.

Gerar JWT_SECRET no PowerShell, sem reutilizar exemplos públicos:

```powershell
$jwtBytes = New-Object byte[] 32
$jwtRng = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$jwtRng.GetBytes($jwtBytes)
$jwtRng.Dispose()
$env:JWT_SECRET = [Convert]::ToBase64String($jwtBytes)
```

Esse valor vale somente para o processo/filhos desse terminal. Para IntelliJ, configurar a mesma chave no processo da aplicação. Não colar em commits, apresentações, screenshots ou variáveis `VITE_*`.

Na raiz do backend:

```powershell
.\mvnw.cmd clean verify
.\mvnw.cmd spring-boot:run
```

Linux/macOS: `./mvnw`. Verificar no log Flyway V1–V5 e inicialização com `spring.jpa.hibernate.ddl-auto=validate`. Não trocar para `update` para mascarar schema incompatível. O Spring Boot iniciado sozinho não lê `.env` automaticamente: copiar `.env.example` não basta para o Maven/IntelliJ.

## Administrador de demonstração

Configurar `ADMIN_EMAIL`, `ADMIN_PASSWORD` (8–72 caracteres) e opcionalmente `ADMIN_NOME`; iniciar a aplicação uma vez. Sem email/senha, nenhuma conta é criada; somente uma variável preenchida interrompe startup. Email inválido também interrompe. Se o email já existir, não atualiza senha nem promove cidadão. Usar email próprio da demonstração com dados fictícios, sem divulgar senha no Git.

Após provisionar, remover as variáveis administrativas e reiniciar. A conta já está persistida. Login usa o endpoint normal. Não há conta/senha global padrão distribuída pela V4.

## Frontend em modo HTTP

Na raiz do frontend, copiar `.env.example` para `.env.local` e ajustar:

```dotenv
VITE_API_MODE=http
VITE_API_BASE_URL=
API_PROXY_TARGET=http://localhost:8080
```

```powershell
npm ci
npm run dev
```

Abrir a URL exibida pelo Vite, normalmente `http://localhost:5173`. Base vazia usa `/api` na mesma origem; Vite encaminha à API. Reiniciar Vite após alterar ambiente. `mock` não acessa banco/API real.

Verificações do frontend: `npm run lint`, `npm test`, `npm run build`. `npm run preview` serve build estático e não fornece o proxy `/api` configurado em `server.proxy`. Publicação precisa de reverse proxy para a API ou origem/CORS deliberadamente configurados, além de HTTPS. Segredos nunca entram no build frontend.

Para câmera/GPS em celular, avaliar contexto seguro, permissão e conectividade. O projeto permite `VITE_HTTPS=true` para desenvolvimento com certificado autoassinado; isso exige confiança no certificado no dispositivo e não é certificado de produção. Não marcar teste físico como feito apenas por passar no navegador do computador.

## Fotografias no Supabase

Bucket privado `ocorrencias-fotografias`, restrito a JPEG/PNG e 5 MiB. Configuração do backend:

```dotenv
FOTOGRAFIAS_HABILITADAS=true
SUPABASE_STORAGE_URL=https://seu-projeto.supabase.co
SUPABASE_STORAGE_BUCKET=ocorrencias-fotografias
SUPABASE_STORAGE_SECRET_KEY=
```

Preencher a última variável localmente com secret key de servidor `sb_secret_...`. A URL é a origem HTTPS do projeto, sem anexar rotas de objeto. Chave pública, JWT do Zelar e senha do banco têm finalidades diferentes. Reiniciar a API. O bucket foi informado como existente, mas configuração efetiva, acesso e limpeza real ainda precisam ser validados pelo [roteiro](../quality/ROTEIRO-INTEGRACAO.md).

O frontend de referência ainda bloqueia upload HTTP; testar endpoint diretamente não conclui integração da interface. Seguir [contrato](../api/README.md) para upload e consulta autenticada. Timeouts do adaptador: 5 s conexão e 20 s leitura, sem redirecionamento e sem retry automático de upload.

## Docker Compose

O Compose do backend contém apenas a API, usando PostgreSQL externo. Configurar `.env` a partir do exemplo, então `docker compose up --build`. Dentro do container, `localhost` aponta ao próprio container; no Docker Desktop, `host.docker.internal` pode alcançar o host. O mapeamento é `8080:8080`: trocar SERVER_PORT sem alinhar o mapeamento pode tornar a API inacessível. O Compose não publica o frontend.

## Diagnóstico

| Sintoma | Conferir |
| --- | --- |
| Startup falha por variável | Ambiente do processo real; `.env` não é importado pelo Spring sozinho |
| Erro de conexão | Host, porta, banco, usuário, senha e rede; não alterar migration por falha de rede |
| Flyway/checksum/validate falha | Versão do código e histórico do banco; não apagar histórico nem reescrever migration aplicada |
| 401 | Bearer, chave/emissor/duração, login novo e existência da conta |
| 403 / 404 | Perfil atual, autoria e regra da operação |
| 409 em foto | Vínculo existente/pendente; consultar indicador e aguardar recuperação quando necessário |
| 503 em foto | Habilitação, URL/chave/bucket, rede e disponibilidade; não reenviar a ocorrência |
| Erro de proxy | API ativa e API_PROXY_TARGET; em build estático, configurar roteamento real |

Fonte adicional: [README do backend na versão conferida](https://github.com/Zelar-App/zelar.app-backend/blob/e0d47f21e0439437c031d7610de12dd4dbbb1cd2/README.md).
