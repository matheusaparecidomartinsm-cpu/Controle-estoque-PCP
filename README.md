# Controle Estoque PCP

Site de controle diário de estoque do PCP da ProFood Embalagens, com os produtos JIT (franquias) e os genéricos em abas separadas.

## O que o site faz

- **Visual estilo Power BI**: barra fixa no topo com as abas (Visão geral, Ações de hoje, Produtos JIT, Genéricos, O.S em aberto), filtros na lateral (busca, situação, O.S, franquia) que valem para tabelas, gráficos e relatórios, KPIs no topo de cada página e gráficos em Chart.js 4.4.1.
- **Visão geral**: KPIs do dia, rupturas previstas nas próximas 8 semanas, itens por situação, situação por franquia, disponível × consumo, % em dia por franquia, franquias mais críticas e os itens mais urgentes.
- **O.S em aberto**: uma linha por O.S com os produtos ligados, O.S por setor, situação (no prazo, sem previsão, atrasada) e entregas previstas por semana.

- **Ações de hoje** (aba que abre primeiro): três listas do que precisa ser feito. *Precisa abrir OS*: itens sem OS que, contando a entrega pendente, acabam antes do prazo de produção mais a margem de segurança, com a quantidade sugerida em caixas (consumo do período menos disponível e entrega pendente). *OS atrasadas*: OS com a previsão vencida. *OS sem previsão*: itens com OS aberta, sem data, que acabam antes do prazo. O prazo (padrão 60 dias) e a margem (padrão 15 dias) ficam na própria tela; dá para usar o LeadTime da planilha item por item. Baixa as listas em Excel.
  - *Abrir a próxima O.S*: para materiais com O.S em andamento, data limite para abrir a próxima = (disponível + o que falta chegar da O.S) ÷ (consumo com folga de 10%) − prazo de produção − margem. Aparece 7 dias antes; a sugestão cobre prazo + margem sem passar do teto de cobertura (120 dias). Selo "reta final" quando é a única O.S e já está em corte e vinco ou depois.
  - *Estoque acima do teto*: O.S que deixam o material com mais dias de cobertura do que o teto.
  - Na tabela, a coluna "Próxima O.S" mostra a data limite ou "Abrir agora".

- Mostra cada produto em uma linha, como na planilha de monitoramento: descrição, média mensal, quantidade por caixa, estoque, empenhado, disponível, dias, ruptura, OS, última movimentação, previsão e observação.
- Calcula o disponível (estoque − empenhado), os dias de estoque (disponível ÷ média mensal × 30), os dias contando a entrega pendente e a data de ruptura a partir da data da importação.
- **Importar estoque**: lê um arquivo .xlsx, .xls ou .csv e atualiza, pelo código, os produtos que já estão no site (JIT e genéricos). Produtos da planilha que não estão no site ficam de fora, a não ser que se marque a opção de adicioná-los. O formato do dia a dia é a exportação do sistema (aba "Export"): Produto ("código - descrição"), Estoque, Venda Empenhada, Média Mensal, Entrega Pendente e Dias Estoque Atual ("Estoque Incalculavel" vira "Sem média"). A planilha de monitoramento antiga (Cód Novo, Descrição produto, Quantidade por caixa, OS, Última Movimentação, Previsão, Observação) também é aceita. Campos que a planilha não traz, como OS e previsão, ficam como estavam.
- **Importar OS**: selecionar juntas (Ctrl) a planilha de OS do sistema (O.S, Título, QtdOS, Produzido, Abertura, Status, Previsão) e a de Observações (O.S, OBSERVACOES). A OS liga ao produto pelo código do título ("6304.5451 ..." → 5451) e pelos códigos "modelo.código" escritos nas observações, que é onde ficam as OS com vários produtos. Cada produto fica com a OS em aberto mais recente; Status vira a última movimentação ("Não iniciada" quando ainda não tem apontamento) e a observação vira a linha do produto na OS. As outras OS em aberto aparecem no painel do produto. Uma OS sem status e com produção apontada conta como concluída. Produto sem OS no site só ganha OS aberta nos últimos 30 dias da planilha, e a OS digitada à mão é mantida quando a OS cita um código antigo.
- **Relatório 45 dias**: monta o PDF A4 "Itens com até 45 dias de estoque", com os itens que têm OS aberta, tirando os códigos do relatório Prioridades O.S e o Bar do Alemão SP.
- **Relatório semanal**: compara duas fotos do estoque (por padrão, a mais recente com a de uma semana antes) e mostra o que mudou: produtos que saíram e entraram em ruptura, os que mais melhoraram e pioraram em dias de estoque, a variação do estoque em %, um gráfico de produtos por situação e outro com os maiores ganhos. Baixa em PDF A4 e em Excel.
- Registra entrada e saída de caixas no painel de cada produto.

## Onde ele roda

O site é estático (`index.html`) e está publicado na Vercel, ligado a este repositório: cada push na `main` gera um deploy novo.

Os dados ficam no Supabase:

- Tabela `estoque_pcp`: uma linha para cada lista (`jit`, `gen`), mais `os` (última importação de OS) e `config` (códigos do Prioridades O.S do relatório). O campo `data` guarda a lista em JSON.
- Tabela `estoque_pcp_historico`: uma foto por aba e por dia, gravada automaticamente (gatilho no banco) sempre que a lista de estoque muda. É de onde sai o relatório semanal.
- Tabela `estoque_pcp_permitidos`: e-mails que podem ver e alterar o estoque. As regras de acesso (RLS) bloqueiam qualquer outro usuário.
- Login com e-mail e senha do Supabase Auth. Quem digita só o usuário (ex.: `matheuspcp`) entra como `matheuspcp@profood.local`.
- A chave usada no site é a publicável do Supabase, feita para ficar no navegador; o que protege os dados são as regras de acesso.

Para liberar outra pessoa: crie o usuário no Supabase Auth e inclua o e-mail dela em `estoque_pcp_permitidos`.

## Bibliotecas

Carregadas por CDN:

- Supabase JS 2.45.4: login e banco de dados

- SheetJS (xlsx 0.18.5): leitura das planilhas
- Chart.js 4.4.1: gráficos do painel
- jsPDF 2.5.1 e jsPDF-AutoTable 3.8.2: geração do relatório em PDF
