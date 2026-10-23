# Controle Estoque PCP

Site de controle diário de estoque do PCP da ProFood Embalagens, com os produtos JIT (franquias) e os genéricos em abas separadas.

## O que o site faz

- Mostra cada produto em uma linha, como na planilha de monitoramento: descrição, média mensal, quantidade por caixa, estoque, empenhado, disponível, dias, ruptura, OS, última movimentação, previsão e observação.
- Calcula o disponível (estoque − empenhado), os dias de estoque (disponível ÷ média mensal × 30) e a data de ruptura a partir da data da importação.
- **Importar planilha de estoque**: lê um arquivo .xlsx, .xls ou .csv, mostra uma prévia e substitui a lista da aba aberta. As colunas são reconhecidas pelo nome (Cód Novo, Descrição produto, Média Mensal, Quantidade por caixa, Estoque, Empenhado, Dias, OS, Última Movimentação, Previsão, Observação).
- **Importar planilha de OS**: liga cada OS ao produto pelo código e atualiza OS, última movimentação e previsão.
- **Gerar relatório de 45 dias**: monta o PDF A4 "Itens com até 45 dias de estoque", com os itens que têm OS aberta, tirando os códigos do relatório Prioridades O.S e o Bar do Alemão SP.
- Registra entrada e saída de caixas no painel de cada produto.

## Onde ele roda

O `index.html` foi feito para rodar como página publicada no claude.ai. Ele usa recursos dessa plataforma que não existem fora dela:

- `db`: guarda as listas de produtos e as OS importadas.
- `user`: libera os botões de importar só para quem pode editar.
- `downloads`: entrega o PDF do relatório.

Aberto direto do GitHub (ou pelo GitHub Pages), o layout carrega, mas os dados não, e aparece o aviso "Os dados não carregaram nesta visualização". Para rodar fora do claude.ai, é preciso trocar esse armazenamento por um banco próprio (por exemplo, Supabase) e o download por um link comum.

## Bibliotecas

Carregadas pelo cdnjs:

- SheetJS (xlsx 0.18.5): leitura das planilhas
- jsPDF 2.5.1 e jsPDF-AutoTable 3.8.2: geração do relatório em PDF
