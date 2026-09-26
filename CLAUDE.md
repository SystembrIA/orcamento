# Orçamento — instruções para o Claude

Painel de controle de gastos do Pedro, publicado em https://systembria.github.io/orcamento/.
Quem pede as mudanças é o Michael (dono). Responda sempre em português, em linguagem simples,
sem jargão técnico.

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
- **Celular (até 640px):** só as abas Lançamentos e Gráficos, a troca de mês, o botão grande
  "Adicionar despesa" (tipo fixo/supérfluo, descrição, categoria, valor, parcelas, data) e a lista
  em cartões. "Hoje", "Limpar mês", Linha do tempo, Consultar, resumo e importação ficam só no computador.
- **Gráficos:** clicar/tocar numa barra abre abaixo os lançamentos daquela barra.
- Categorias no estilo Mobills/Organizze; o app aprende a categoria quando o Michael corrige um gasto.
