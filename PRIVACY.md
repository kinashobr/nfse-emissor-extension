# Política de Privacidade — NFS-e Nacional Preenchimento

_Última atualização: 18 de novembro de 2026._

## Resumo

A extensão **não coleta, não transmite e não vende nenhum dado**. Tudo o que ela
guarda fica no armazenamento do **seu** navegador, no **seu** computador.

## Quais dados a extensão guarda

### No armazenamento local do navegador (persistente)

- **Perfis de cliente** — o emitente (razão social, CNPJ, regime do Simples Nacional,
  endereço) e os dados da nota que você usa como base: município, código de serviço,
  NBS, descrição, alíquota do Simples, valor de referência.
- **Modelos de tomador** — para cada cliente, um modelo por tomador, criado por você ao
  importar XMLs de notas já emitidas (ou ao salvar uma nota como padrão): nome ou razão
  social, **CPF/CNPJ**, NIF, telefone, e-mail e **endereço** do tomador e, se houver, do
  intermediário.

Esses dados só existem porque você os importou. Podem ser excluídos a qualquer momento
(ver _Exclusão dos dados_).

### Na sessão do navegador (descartado ao fechar o navegador)

- **Estado da emissão em andamento**: competência, valor e câmbio digitados.
- **Perfil de "uso único"**: os dados de uma nota carregada com _Usar nesta emissão_.

### Arquivos que você escolhe

- **XMLs de notas** são lidos pelo próprio painel, na hora, para montar os perfis e
  modelos acima. Os arquivos não são copiados nem enviados a lugar nenhum.
- O arquivo gerado por **Exportar** (cópia de segurança dos clientes e modelos) é
  salvo por você, onde você escolher. Ele contém os dados descritos acima — guarde-o
  com cuidado.

Nenhum desses dados sai do navegador. Não há servidor da extensão, não há conta, não há
analytics, não há cookies, não há identificadores de qualquer tipo.

## Senhas e credenciais

A extensão **não pede, não lê e não armazena senhas**. Ela atua apenas na aba em que
você já está logado no Emissor Nacional (inclusive quando o login é por certificado
digital).

## Conexões de rede

A extensão se comunica com exatamente dois domínios:

1. **nfse.gov.br** — a página do Emissor Nacional já aberta no seu navegador, para ler
   a identificação de quem está logado e preencher o assistente de emissão. Nenhuma
   requisição própria é feita; a extensão só interage com a página que você está vendo.
2. **olinda.bcb.gov.br** — API pública do Banco Central, para buscar a cotação PTAX da
   data de competência. É usada **somente em notas de exportação** (valor em US$); nas
   notas em R$ nada é consultado. A requisição contém só a data — nenhum dado pessoal.

## Código remoto

Todo o código executa do pacote instalado. A extensão não baixa nem executa código
remoto (exigência do Manifest V3, que ela cumpre integralmente).

## Exclusão dos dados

- **Um tomador:** no painel, selecione-o e use _Remover da lista_.
- **Um cliente inteiro** (perfil e todos os modelos de tomador dele): tela inicial →
  _Perfil emitente_ → excluir.
- **Tudo:** desinstale a extensão — o navegador apaga todo o armazenamento dela junto.

## Contato

Dúvidas ou problemas: abra uma _issue_ no repositório do projeto.

---

## Privacy Policy (English summary)

This extension collects, transmits and sells **no data**. Client profiles (issuer name,
CNPJ tax id, address, service codes) and per-client **tomador templates** (counterparty
name, CPF/CNPJ, NIF, contact and address) are created by the user by importing emitted-
invoice XMLs, and are stored only in the browser's local extension storage; the
in-progress emission state and "use once" profiles live in session storage and vanish
when the browser closes. XML files are read locally in the panel and are never uploaded.
Everything can be deleted in the panel or by uninstalling. The backup file produced by
_Exportar_ contains this data and is saved wherever the user chooses. No passwords are
requested, read or stored — the extension operates on the already-logged-in nfse.gov.br
tab. The only external request is to the Brazilian Central Bank's public exchange-rate
API (olinda.bcb.gov.br), containing only a date, and only for export (USD) invoices.
