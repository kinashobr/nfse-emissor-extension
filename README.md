# NFS-e Nacional — Preenchimento automático 

Extensão de navegador (Manifest V3) que **preenche o rascunho da NFS-e** no
[Emissor Nacional](https://www.nfse.gov.br/EmissorNacional) para emitentes **ME/EPP no
Simples Nacional**.

A extensão preenche o assistente; **você confere e clica em Emitir — ela nunca emite
sozinha.**

Feita para quem emite **as mesmas notas todo mês**: você importa o XML de notas já
emitidas, a extensão guarda cada tomador como um _modelo_, e na hora de emitir basta
**escolher o tomador, conferir o valor e preencher** as três páginas do assistente
(Pessoas → Serviço → Valores).

Como roda dentro do **seu navegador, já logado**, não esbarra nas defesas do portal
(WAF, captcha) que derrubariam uma automação externa. E não pede senha nenhuma.

> Este projeto é um _fork_ de
> [thgrcarvalho/nfse-emissor-extension](https://github.com/thgrcarvalho/nfse-emissor-extension)
> (licença MIT), que originalmente só emitia exportação de serviço a partir de um perfil
> fixo por cliente. O fluxo, o painel e o escopo mudaram bastante — este README descreve
> o que o a extensão faz **hoje**.

## Como usar

### 1. Cadastrar os modelos (uma vez, ou quando algo mudar)

No painel lateral, o quadro **Tomador** tem o botão **Importar XML**. Baixe no portal o
XML de notas que você já emitiu (em _NFS-e Emitidas_) e carregue-os:

- **Vários XMLs de uma vez:** cada **tomador distinto** vira um modelo do cliente (o
  emitente é lido do próprio XML). Importar outra nota do **mesmo tomador** _atualiza_
  o modelo — útil para renovar valor, endereço ou descrição todo mês com as notas do mês
  anterior. O painel informa quantos modelos foram cadastrados e quais arquivos ignorou.
  Dica: se o cliente ainda não tinha perfil, o primeiro XML vira o ativo; depois de
  importar, **escolha o tomador no seletor** para carregar a versão mais recente do modelo.
- **Um XML só:** o painel mostra o que leu (cliente, tomador, CEPs, município, CTN,
  alíquota…) e avisos (campos que não conseguiu ler; CEP do tomador ou do prestador
  diferente do perfil atual). Escolha **Usar nesta emissão** (só desta vez) ou **Salvar
  como padrão** (vira modelo e fica salvo no navegador). No uso único, o seletor mostra o
  tomador marcado **(só esta emissão)**; escolher qualquer modelo da lista descarta o
  uso único.
- **Pela nota no portal:** abrir uma nota emitida (ícone de visualizar) e usar **Salvar
  como padrão** também funciona, mas lê menos campos que o XML (ver abaixo).

O XML é a fonte preferida porque traz até o que a página da nota não mostra: regime de
apuração do Simples Nacional, motivo da não informação do NIF, retenção do ISSQN e os
endereços.

### 2. Emitir cada nota

1. Faça login no Emissor Nacional, abra o painel e inicie a **Emissão completa**.
2. No quadro **Tomador**, **escolha o tomador** da nota. O valor do XML daquele modelo
   aparece como sugestão no campo **Valor**.
3. Informe a **data de competência** e **ajuste o valor** (é o que costuma mudar de um
   mês para o outro).
4. Em cada página (Pessoas → Serviço → Valores) clique **Preencher página atual**,
   confira e **Avançar** no portal.
5. Na tela final, revise e clique **Emitir** você mesmo.

A escolha do tomador fica como o modelo "padrão" daquele cliente até você escolher outro.
**Remover da lista** exclui um modelo (dois cliques, para evitar acidente).

## O que ela faz

- **Painel lateral** (abre pelo ícone na barra) que fica aberto durante toda a emissão.
- Mostra **quem está logado** (razão social + CNPJ) e só funciona no Emissor Nacional.
- **Um cliente por CNPJ, vários tomadores por cliente.** O painel seleciona sozinho os
  dados do CNPJ logado e **nunca preenche dados de outro cliente**; se o CNPJ não puder
  ser lido, o preenchimento é bloqueado. Serve a quem emite para um CNPJ só e ao contador
  que atende vários.
- **Modelos de tomador** criados a partir de XMLs (ver acima), com seletor no painel.
- **Endereços do XML prevalecem sobre o cadastro do portal.** O portal pré-preenche
  prestador, tomador e intermediário no Brasil com o cadastro da Receita, que pode estar
  desatualizado; quando o XML traz o endereço, a extensão sobrescreve (CEP primeiro, que
  dispara a consulta do portal, e depois logradouro, número, complemento e bairro).
- **Valor em R$ ou US$ conforme o tipo da nota.** Na nota convencional o valor já é em
  reais: o campo de câmbio some e nada é buscado no Banco Central. Na exportação, a PTAX
  de fechamento do Banco Central é buscada para a data de competência (com aviso quando
  usa o fechamento de um dia anterior — fim de semana/feriado).
- **Preenchimento que não reescreve o que já está certo.** Campos e opções que já têm o
  valor desejado (competência, "IBS/CBS: Não") não são tocados, o que evita reabrir
  diálogos de confirmação do portal. Os diálogos de troca de competência e de IBS/CBS só
  são confirmados quando uma troca realmente aconteceu.
- **Reforma IBS/CBS (2026):** responde "Não" ao "Preencher as informações IBS/CBS?",
  respeita o bloqueio do formulário do portal (preenche só depois da liberação) e
  **reconfere a competência** depois dele, porque o portal pode zerá-la. O que não for
  preenchido automaticamente é listado no painel — nada de ✓ enganoso.
- **Guarda de formato:** se a página ou o modelo não casam com uma variante reconhecida,
  a extensão **se recusa a preencher a página inteira**. Nada é preenchido pela metade.
- **Gerenciador de clientes** (tela inicial, quadro _Perfil emitente_): lista os clientes
  salvos, **exporta/importa** (o arquivo exportado inclui os modelos de tomador) e
  exclui — apagar um cliente apaga também os modelos dele.
- **Funciona em sessões que não carregaram a extensão** (aba aberta antes de instalar ou
  recarregar a extensão, ou login por procurador, que pode não disparar a injeção
  automática): se o script da página não responde, o painel o injeta sob demanda e tenta
  de novo.
- **Reconhece o cliente também no login por certificado digital**, mesmo quando o
  cabeçalho do portal vem num formato diferente (sempre exigindo um CNPJ completo e só
  dentro da barra de navegação — um CNPJ de tomador nunca é confundido com o seu).
- Preenche as três páginas do assistente sob demanda e **para na revisão**.

## Escopo suportado

A extensão preenche nota de emitente **ME/EPP no Simples Nacional**, nestes dois tipos.

### Serviço convencional (ISS devido)

- **Operação Tributável**, **sem retenção de ISSQN**, valor em R$;
- Exportação/imunidade/não incidência marcado como **Não**; sem bloco de comércio
  exterior;
- Município da prestação, CTN, código complementar municipal (se houver), descrição e
  NBS **lidos do XML**;
- PIS/COFINS e total dos tributos por **alíquota do Simples Nacional**, lidos do XML.

### Exportação de serviço (ISS não incidente)

- Tomador **no exterior** (com endereço), **no Brasil** (CPF/CNPJ — o portal busca o
  cadastro) ou **não informado**;
- **NIF** informado ou não informado (tomador no exterior);
- Telefone e e-mail do tomador (opcionais);
- **Intermediário do serviço** (opcional): no exterior, no Brasil ou não informado, com
  NIF e contato — espelhando o tomador;
- Total dos tributos por **alíquota do Simples Nacional**, por **valores por ente** ou
  por **percentuais por ente** ("não informar" não existe para ME/EPP — o portal recusa
  a emissão);
- Valor em **US$**, convertido pela PTAX; comércio exterior (modo de prestação, vínculo,
  moeda, mecanismos de apoio, MDIC).

### Tomador no Brasil, no exterior ou não informado

Vale para os dois tipos de nota. Tomador e intermediário no Brasil têm o endereço
sobrescrito pelo do XML (quando presente).

### Fora do escopo (a guarda barra antes de preencher)

- **Retenção de ISSQN** pelo tomador ou intermediário;
- **Imunidade** e **não incidência**;
- Obra e evento; regimes que não sejam Simples Nacional ME/EPP;
- **Alíquota própria de ISS** (fora do Simples): o painel só avisa para conferir no portal.

### Status de validação

**Versão do portal validada: Emissor Nacional 1.6.0.0.** O portal exibe a versão em
todas as páginas; quando ela for diferente da validada — ou não puder ser lida — o
painel mostra um aviso: o assistente pode ter mudado e cada campo preenchido merece
conferência redobrada. A fonte canônica do valor é `SUPPORTED_PORTAL_VERSION` em
`src/popup.js`.

- A **exportação** foi validada de ponta a ponta pelo projeto original (notas de teste
  emitidas na Produção Restrita).
- A **nota convencional** foi construída sobre o mapa real das páginas Serviço e Valores
  do portal 1.6.0.0 e testada com o plano de preenchimento e a guarda de formato contra
  esses mapas. Confirme na Produção Restrita antes de usar com clientes, e **sempre
  confira a tela de revisão antes de emitir**.

Variantes que apenas _acrescentem_ campos aos do formato suportado não são detectadas
pela guarda — a conferência antes de emitir continua indispensável.

## Instalação (modo desenvolvedor)

1. Opcional: `cp src/config.example.json src/config.default.json` e preencha com os seus
   dados — o arquivo real fica fora do git (gitignored). Dá para dispensar isso e criar
   os clientes só importando XMLs.
2. **Chrome / Edge:** `chrome://extensions` (ou `edge://extensions`) → ative o **Modo do
   desenvolvedor** → **Carregar sem compactação** → selecione a pasta da extensão (ou a
   pasta extraída do `.zip`). O ícone na barra abre o **painel lateral**.
   Se houver outra versão da extensão instalada, desative-a: as duas atuariam na mesma
   página.
3. Faça login no Emissor Nacional e abra o painel.
4. Importe os XMLs (seção _Como usar_) e emita.

O build (`npm run build`) gera os pacotes em `dist/`. O pacote Chrome/Edge é o usado e
testado neste fork; o pacote Firefox ainda é gerado pelo build herdado do projeto
original, mas as mudanças deste fork não foram validadas nele.

## Ferramentas de diagnóstico

- **Copiar diagnóstico** (tela de "cliente não identificado"): copia a estrutura da barra
  de navegação do portal, com todos os números trocados por `#`, para anexar a um
  relato de problema.
- **Copiar mapa desta página** (_Ferramentas de mapeamento_): lista cada campo da página
  aberta do assistente (id, tipo, rótulo, opções dos selects, visível/oculto), sem o que
  você digitou. Foi assim que os campos da nota convencional foram levantados. Está
  **oculta por padrão** no painel; para usar, retire o atributo `hidden` de
  `<details id="devTools">` em `src/popup.html`.

## Privacidade

Política completa em [PRIVACY.md](PRIVACY.md). Em resumo:

- A extensão **não pede, não vê e não armazena senhas** — ela atua na aba em que você
  já está logado.
- Os dados ficam **somente no seu navegador** (`chrome.storage`): os **perfis de cliente**
  e os **modelos de tomador** (razão social, CNPJ/CPF, endereço, códigos de serviço,
  alíquota, valor de referência) ficam no armazenamento local; o estado da emissão em
  andamento e os perfis de "uso único" ficam na sessão, e somem ao fechar o navegador.
  Os XMLs são lidos no próprio painel e não são enviados a lugar nenhum.
- Nada é enviado a terceiros; a única chamada externa é a consulta pública de câmbio na
  API do Banco Central (PTAX/Olinda), e só nas notas de exportação.
- O arquivo gerado por **Exportar** contém esses dados: guarde-o com cuidado.

## Arquitetura (en)

- `src/content.js` — isolated world: detects the page + login identity + the portal's
  displayed version, parses an emitted nota into a client profile (section-scoped,
  fail-closed on unreadable CNPJ; identity read tolerates layout variants such as
  certificate login but only inside the navbar), resolves which profile a fill may use.
  Guarded against double injection, since the panel can inject it on demand.
- `src/xml-profile.js` — builds a profile from an emitted NFS-e XML: emitter, tomador and
  intermediary (Brazil / abroad / not informed, with national or foreign address),
  service, federal taxes, and the two note kinds (export vs. ISS-devido, keyed by
  `tribISSQN`), including the emitter's and Brazilian counterparties' addresses.
- `src/fill-plan.js` — builds the ordered field operations per page (field ids and
  cascade order reverse-engineered from the portal's wizard); `ensure` ops skip fields
  that already hold the wanted value, and the competência is re-checked after the
  portal's IBS/CBS gate.
- `src/field-ops.js` — applies them via the page's own jQuery (Chosen/select2 aware),
  polling AJAX cascades, re-applying values a late rebuild wiped, and confirming portal
  dialogs only when a change actually happened.
- `src/shape-guard.js` — pre-flight: resolves which supported variant the profile
  declares (note kind, tomador locale, NIF, tributos type, ISSQN retention) and refuses
  the whole page when the page's controls or the profile don't match it — an unknown
  wizard variant is never partially filled, and unknown discriminant values fail closed.
- `src/rate.js` — BCB PTAX (fechamento, compra) lookup by competência date, with
  weekend/holiday walk-back (export notes only).
- `src/popup.*` — the side panel: identity, clients, **per-client tomador templates**
  (stored in `storage.local`, picked from a selector, imported from one or many XMLs),
  per-run inputs, fill trigger; `sendToTab` injects the content script on demand when
  nobody is listening. The fill engine + profile are injected into the page's MAIN
  world via `scripting.executeScript` — no page-observable bridge, no engine parked on
  the page.
- `src/sw.js` — opens the side panel on the toolbar-icon click.

## English summary

Browser extension (MV3) that fills the monthly NFS-e (Brazilian service invoice) draft
on the national Emissor Nacional, for ME/EPP issuers on Simples Nacional, in two note
kinds: **conventional service** (ISS due, no withholding, amounts in BRL) and **service
export** (ISS-exempt, amounts in USD at the BCB PTAX rate). Onboarding is done by
importing emitted-invoice XMLs: each distinct tomador becomes a per-client template,
picked from a selector at emission time; the amount is the only thing you normally
change. Addresses in the XML override the portal's possibly-stale registry data. It fills
the 3-page wizard; the human always reviews and clicks Emitir. Per-client data is keyed
by the logged-in CNPJ and never mixed across clients; a fill-time guard refuses whole
pages outside the supported variants. Credential-free: it operates on the already-logged-in
tab, and all data stays in local browser storage. Validated against Emissor Nacional
1.6.0.0, with an in-panel warning when the live portal version differs.

## Aviso

Projeto independente, sem qualquer vínculo com a RFB, o Serpro ou o portal NFS-e.
Confira sempre os dados preenchidos antes de emitir — a responsabilidade pela nota é
de quem emite. Licença MIT; créditos do projeto original a Thiago Carvalho
([thgrcarvalho/nfse-emissor-extension](https://github.com/thgrcarvalho/nfse-emissor-extension)).
