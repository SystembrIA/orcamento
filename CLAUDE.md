# Orçamento — instruções para o Claude

Painel de controle de gastos do Pedro, publicado em https://systembria.github.io/orcamento/.
Quem pede as mudanças é o Michael (dono). Responda sempre em português, em linguagem simples,
sem jargão técnico.

## Acesso

O painel se chama **Conciliação** e abre pelo hub (repositório `SystembrIA/hub`), no atalho
"Conciliação", que aparece só para os logins `justadm` (Michael) e `pedro` (Pedro Henrique, filho
dele). No painel só entram os logins da lista `USUARIOS_OK` no topo do `index.html` (esses dois).

## Publicação: faça tudo sozinho (autorizado pelo Michael)

O Michael autorizou que as mudanças sejam publicadas **sem pedir confirmação**. Em cada pedido:

1. Faça a mudança no `index.html` (o app inteiro está nesse arquivo).
2. Teste no navegador (veja "Como testar") no computador **e** no celular.
3. Faça o commit e o push na branch de trabalho da sessão.
4. Abra o pull request para `main` e **faça o merge você mesmo** (merge commit).
5. Espere o GitHub Pages publicar (1–3 min) e confira no site que a versão nova está no ar.
6. Só então conte ao Michael o que mudou.

Se a branch de trabalho tiver um PR já mergeado, recomece a branch a partir da `main` atual antes
de continuar. Nunca faça force push na `main`.

## Nunca

- Nunca grave no repositório faturas, extratos, planilhas ou qualquer dado pessoal (valores,
  finais de cartão, nomes de estabelecimentos das faturas). O repositório vira o site público.
  Arquivos que o Michael mandar servem só para entender o layout e criar a regra de leitura.
- Nunca escreva no Firebase de verdade durante os testes (é o banco compartilhado do hub).

## Como testar

- Chromium e Playwright já vêm instalados (`/opt/node22/lib/node_modules/playwright`).
- Abra o arquivo com `file://`: nesse modo o login do hub não é exigido e o app usa o
  `localStorage` em vez do Firebase.
- **Sempre bloqueie** os scripts do Firebase e do hub com `context.route(/firebase|just-burger-producao/, …)`
  respondendo um script vazio, para não tocar no banco real.
- Se a CDN falhar por certificado, use `ignoreHTTPSErrors: true` no contexto.
- Teste o celular com `devices['iPhone 13']` e confira que a página não passa da largura da tela.

## Regras do app que precisam continuar valendo

- **Fatura do Itaú: ler sempre e somente os gastos do Pedro.** O app escolhe sozinho o(s)
  cartão(ões) cujo titular tem "PEDRO" no nome (`TITULAR_CONTROLADO`), sem perguntar. Os outros
  titulares e os encargos da fatura (juros, multa) ficam de fora. O IOF das compras internacionais
  dele entra. Identifique pelo nome, não pelo final do cartão (o final muda quando o cartão é trocado).
- A leitura da fatura Itaú é guiada pelos **títulos dos blocos** (compras nacionais, internacionais,
  totais por cartão), não pela posição na página. As colunas são detectadas página a página. A lista
  de um titular pode começar numa coluna e continuar na coluna/página seguinte. Confira sempre a soma
  lida contra o total impresso na fatura.
- **Parcelas:** ao importar "3/10", lance a parcela atual e já crie as parcelas seguintes nos meses
  seguintes. O bloco "Compras parceladas – próximas faturas" é ignorado (senão duplica).
- **Não duplicar:** reimportar a mesma fatura (ou a do mês seguinte) marca como "já lançado" o que
  já existe, inclusive parcelas geradas antes.
- **Dois jeitos de ver o mês:** "Vencendo no mês" (o que vence/é pago no mês — padrão) e "Gastos do
  mês" (pela data da compra). Parcelas contam no mês de cada parcela; gasto sem data fica no mês em
  que vence.
- **Computador, 1ª linha (verde-petróleo):** abas Lançamentos · Gráficos · Linha do tempo · Consultar e o
  botão "Importar arquivo", que abre direto a escolha do arquivo.
- **Computador, 1ª linha também tem** "Lançar despesa" e "Lançar gasto fixo" (abrem o passo a passo).
- **Computador, 2ª linha:** Vencendo no mês · Gastos do mês · Tudo · Cartão de crédito · Débito · ‹ mês › ·
  Hoje · Limpar mês. Sem texto explicativo embaixo.
- **Lista:** uma só, "Lançamentos", em ordem de data, com coluna Tipo (Fixo/Supérfluo) — no celular
  também uma lista só.
- **Gasto fixo** (botão "Lançar gasto fixo") repete todo mês no mesmo dia e valor, gravado para 24
  meses (`recorrente`, `recGrupo`); "parar" na coluna Parcelas (ou "Parar de repetir" no celular)
  apaga os meses seguintes.
- **Celular (até 640px):** só as abas Lançamentos e Gráficos, a troca de mês, dois botões grandes
  lado a lado — "Lançar despesa" e "Lançar gasto fixo" — e a lista em cartões. O lançamento é um passo a passo em tela cheia, um
  campo por tela: categoria (cartões, filtra ao digitar, mais usadas primeiro) → nome (sugere os
  nomes já usados) → valor → data (Hoje/Ontem/outra) → cartão de crédito ou conta corrente, que já
  salva. Tocar num gasto da lista abre o formulário de edição.
- **Link de lançamento (JARVIS):** `?lancar=1&desc=…&valor=…&cat=…&data=hoje|ontem|dd/mm&meio=cartao|conta`
  abre o passo a passo já preenchido e só grava depois do toque em "Salvar". Compra no cartão
  depois do dia 19 (`DIA_FECHAMENTO_CARTAO`) vai para a fatura do mês seguinte. Na importação, um
  gasto igual (mesma data e valor) lançado à mão aparece como "já lançado à mão" e não duplica. "Hoje", "Limpar mês", Linha do tempo, Consultar, resumo e importação ficam só no computador.
- **Gráficos:** clicar/tocar numa barra abre abaixo os lançamentos daquela barra.
- **Base de dados** (aba Consultar, só no computador): "Guardar cópia e zerar lançamentos" grava uma
  cópia completa em `orcamento/copias` e só depois apaga `orcamento/items`; cada cópia pode ser
  restaurada. Categorias aprendidas e renda nunca são apagadas. Zerar/restaurar é feito pelo Michael
  no site, com o login dele — não mexa no banco por fora.
- Categorias no estilo Mobills/Organizze; o app aprende a categoria quando o Michael corrige um gasto.
