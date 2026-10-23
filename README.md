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

O site é estático (`index.html`) e está publicado na Vercel, ligado a este repositório: cada push na `main` gera um deploy novo.

Os dados ficam no Supabase:

- Tabela `estoque_pcp`: uma linha para cada lista (`jit`, `gen`), mais `os` (última importação de OS) e `config` (códigos do Prioridades O.S do relatório). O campo `data` guarda a lista em JSON.
- Tabela `estoque_pcp_permitidos`: e-mails que podem ver e alterar o estoque. As regras de acesso (RLS) bloqueiam qualquer outro usuário.
- Login com e-mail e senha do Supabase Auth. Quem digita só o usuário (ex.: `matheuspcp`) entra como `matheuspcp@profood.local`.
- A chave usada no site é a publicável do Supabase, feita para ficar no navegador; o que protege os dados são as regras de acesso.

Para liberar outra pessoa: crie o usuário no Supabase Auth e inclua o e-mail dela em `estoque_pcp_permitidos`.

## Bibliotecas

Carregadas por CDN:

- Supabase JS 2.45.4: login e banco de dados

- SheetJS (xlsx 0.18.5): leitura das planilhas
- jsPDF 2.5.1 e jsPDF-AutoTable 3.8.2: geração do relatório em PDF
