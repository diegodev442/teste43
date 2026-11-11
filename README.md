# Clube DG · DG Shop

Código-fonte do clube de fidelidade, exportado da versão publicada em 11/11/2026.

## Enviar ao GitHub

1. Extraia este ZIP no computador.
2. Crie um repositório no GitHub. Prefira **Private**, para manter o código restrito.
3. No repositório vazio, escolha **uploading an existing file** ou **Add file → Upload files**.
4. Envie o conteúdo da pasta extraída, incluindo as pastas `web`, `worker`, `scripts`, `drizzle`, `data`, `dist` e `.openai`, além dos arquivos da raiz.
5. Confirme em **Commit changes**. Não envie apenas o ZIP: extraia antes.

Este pacote não contém o histórico Git, credenciais de publicação, senhas, cookies, dados reais de clientes ou cópia do banco de produção. Os registros sintéticos em testes e na prévia servem somente para desenvolvimento.

## O que está incluído

- Catálogo com 108 recompensas: 38 itens de assinaturas/apps/cupom e 70 jogos Steam offline.
- Cupom de 50% por 1 ponto, em primeiro; Netflix e YouTube em destaque.
- Layout responsivo, logos existentes e preferência de modo escuro.
- Cadastro, foto, ranking, recibos e atalhos de atendimento no WhatsApp.
- Códigos de uso único, estoque, pedidos de resgate e painel de clientes.
- Administração por e-mail verificado, com crédito, desconto e reset de pontos; motivo obrigatório e trilha de alterações.
- Senha adicional do clube, sessão protegida, bloqueio após tentativas e opção de bloquear conta.

## Estrutura

| Caminho | Finalidade |
| --- | --- |
| `web/index.html` | Estrutura e estilos da interface |
| `web/client.js` | Interações e integração com a API |
| `web/assets/` | Imagens e logos locais |
| `worker/api.mjs` | API e validações no servidor |
| `worker/catalog.mjs` | Produtos, pontos e informações do catálogo |
| `drizzle/` | Migrações do banco D1; não são os dados do banco |
| `scripts/build.mjs` | Gera o Worker com a página e os assets incorporados |
| `scripts/test-api.mjs` | Testes com banco temporário |
| `scripts/preview.mjs` | Prévia local com dados fictícios |
| `dist/server/index.js` | Worker gerado da versão exportada |
| `.openai/hosting.json` | Identidade e configuração da hospedagem Sites existente |

O bloco de script na página HTML é substituído durante o build. Edite `web/client.js` para alterar a lógica ativa do navegador.

## Executar localmente

Requer Node.js 22.13+ com suporte a `node:sqlite` (ou versão mais recente). Não há dependências externas de execução neste projeto.

```sh
npm run build
npm test
npm run dev -- --host 127.0.0.1 --port 4173
```

Abra `http://127.0.0.1:4173`. A prévia usa uma conta administrativa sintética e um banco em memória; alterações nela desaparecem ao reiniciar. Essa sessão de desenvolvimento não é publicada pelo build.

## Hospedagem e domínio

**Subir o código no GitHub não publica automaticamente este sistema.** GitHub Pages serve arquivos estáticos e não executa a API Worker ou o banco D1 deste projeto.

A hospedagem atual continua sendo Sites, com banco Cloudflare D1, binding `DB` e autenticação fornecida pela plataforma. É possível conectar um domínio próprio à hospedagem existente sem migrar os dados por causa do repositório.

Para hospedar em outra infraestrutura, é necessário adaptar a identidade/autenticação, configurar o backend e o banco, aplicar as migrações e planejar a transferência dos dados. **Não exponha este Worker diretamente em outro servidor aceitando cabeçalhos `oai-authenticated-user-*` enviados pelo visitante.** Esses cabeçalhos só são confiáveis quando fornecidos pela plataforma autenticadora atual. A senha do clube é uma proteção adicional à identidade da plataforma; não substitui o login por Google independente.

Não foi incluído um workflow de deploy externo: é necessário definir primeiro a hospedagem definitiva e a autenticação compatível.

## Variáveis de produção

Configure na hospedagem, nunca no GitHub como arquivos de segredo:

| Variável | Uso |
| --- | --- |
| `ADMIN_EMAILS` | E-mails verificados autorizados, separados por vírgula |
| `ADMIN_EMAIL` | Alternativa de administrador único, usada se ADMIN_EMAILS não estiver configurada |
| `DEMO_POINTS` | Crédito de demonstração exclusivo dos administradores |
| `PUBLIC_DEMO` | `true` mantém visitantes comuns na demonstração isolada |
| `DB` | Binding de banco D1 definido na hospedagem |

As permissões atuais e os valores secretos ficam no serviço de hospedagem, separados do repositório. Alterar o e-mail de contato de um cadastro não concede administração.

## Banco e demonstração

A exportação leva somente o esquema e as migrações. Clientes, códigos, fotos, sessões, resgates, saldos e ajustes de produção continuam no banco da hospedagem atual. Não apague nem substitua esse banco durante a configuração do GitHub.

A demonstração pública permanece ativa na configuração atual. Cadastros e pontos do teste de cliente ficam somente na sessão do navegador e não aparecem como clientes reais no painel. Liberar operação real exige configurar `PUBLIC_DEMO`, estoque e fluxo de acesso adequadamente.

## Controle de pontos

Em **Clientes cadastrados → Gerenciar pontos**, administradores podem adicionar, descontar ou zerar o saldo real, informando o motivo. O histórico registra administrador, data, saldo anterior e saldo final. Os ajustes entram no ranking e na validação de resgates. O reset preserva códigos já utilizados e resgates, impedindo reutilização das chaves.

As migrações devem ser aplicadas na ordem registrada em `drizzle/meta/_journal.json`. O cupom é entregue pela equipe; não há integração automática com o checkout da loja.
