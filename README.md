# 📦 Conciliador de NF-e: Bling para Shopee

> **Solução 100% Client-Side (no navegador) para conciliação em massa de chaves de acesso de NF-e do Bling para a planilha modelo de Upload em Massa da Shopee.**

[![Licença MIT](https://img.shields.io/badge/licença-MIT-green.svg)](LICENSE)
[![Zero Servidor](https://img.shields.io/badge/arquitetura-100%25%20client--side-blue.svg)](#-privacidade-e-segurança-fiscal)
[![SheetJS](https://img.shields.io/badge/excel-SheetJS-orange.svg)](https://sheetjs.com/)
[![JSZip](https://img.shields.io/badge/descompactação-JSZip-yellow.svg)](https://stuk.github.io/jszip/)

---

## 🎯 Por que este projeto existe?

Quando ocorre instabilidade na API entre o ERP (**Bling**) e o marketplace (**Shopee**), o envio automático de notas fiscais pode falhar, impedindo a liberação de etiquetas de envio e atrasando os pedidos.

A Shopee disponibiliza a rotina de **"Upload de NF-e em Massa"**, onde o vendedor baixa uma planilha Excel contendo a coluna `ID do pedido` e precisa preencher a coluna `Chave de Acesso` (44 dígitos numéricos) de cada nota autorizada.

Fazer esse preenchimento manualmente, abrindo nota por nota ou copiando e colando dezenas/centenas de chaves, é demorado e sujeito a erros. Além disso, o **Microsoft Excel costuma converter números longos em notação científica** (ex: `3,52601E+43`), corrompendo a chave e fazendo a Shopee rejeitar o arquivo.

**Este projeto resolve esse problema em segundos:**
1. Lê a planilha modelo exportada pela Shopee (`.xlsx`).
2. Lê as notas fiscais emitidas no Bling (aceita pacote `.zip`, múltiplos arquivos `.xml` ou pasta inteira).
3. Cruza os dados automaticamente em memória ($O(1)$) associando o ID do pedido do marketplace com a chave fiscal correspondente.
4. Gera uma nova planilha Excel **estritamente protegida contra notação científica**, pronta para upload imediato no painel da Shopee.

---

## 🔒 Privacidade e Segurança Fiscal

- **Zero Servidor / Zero Backend:** Toda a leitura, extração de dados e geração do Excel ocorre **exclusivamente na memória RAM do seu navegador**.
- **Seus dados fiscais nunca saem do seu computador:** Não há envio para APIs externas, servidores em nuvem ou bancos de dados de terceiros.
- **Funciona 100% Offline:** Você pode clonar o repositório, desligar a internet e utilizar normalmente.

---

## ✨ Principais Funcionalidades

- **Múltiplos formatos de entrada:**
  - Planilha da Shopee (`.xlsx`, `.xls`, `.csv`).
  - Notas do Bling em `.zip` (pacote compactado exportado do ERP), pasta inteira ou múltiplos `.xml`.
- **Extração com Fallback Inteligente (Layout Padrão SEFAZ Modelo 55):**
  - **Chave de Acesso:** Busca na tag oficial `<chNFe>`, com fallback para o atributo `Id` de `<infNFe>` e busca regex de 44 dígitos.
  - **ID do Pedido Shopee:** Detecta nas informações complementares (`<infCpl>` com regex `Pedido Loja: ...`), na tag `<xPed>` dos itens ou em observações do contribuinte (`<obsCont>`).
- **Painel de Auditoria Visual:**
  - Tabela interativa com status em tempo real (*Conciliado* ou *Pendente*).
  - Exibição do número da NF, série, data de emissão, nome do cliente e valor total da nota.
  - Botão de cópia rápida (📋) para chaves individuais de 44 dígitos.
  - Caixa de busca instantânea e filtros rápidos (*Todos*, *Conciliados*, *Pendentes*).
- **Proteção Rígida Anti-Notação Científica:**
  - Garante células de texto puro (`cell.t = 's'` e `cell.z = '@'`), impedindo que o Excel trunque os últimos dígitos da chave.
- **Exportação Dual:**
  - **Baixar Planilha Shopee (Selecionados):** Gera o arquivo `.xlsx` com apenas 2 colunas (`ID do pedido` e `Chave de Acesso`), contendo apenas os pedidos com nota encontrada para evitar erros de validação da Shopee.
  - **Planilha Completa:** Exporta todos os pedidos da planilha original preenchendo as chaves encontradas.
  - **Relatório de Auditoria:** Planilha gerencial completa contendo NF, Série, Destinatário, CPF/CNPJ, Valor e Status.
- **Botão de Demonstração Integrado:**
  - Permite testar todo o fluxo com 1 clique usando pedidos e XMLs simulados, sem necessidade de carregar arquivos reais.
- **Reset de Estado:**
  - Permite limpar a memória e processar múltiplos lotes ou contas de marketplace em sequência.

---

## 🚀 Como Usar

### Opção 1: Uso Direto (Sem Instalação)
1. Baixe ou clone este repositório:
   ```bash
   git clone https://github.com/porcodobrejo/conciliador-nfe-shopee-bling.git
   ```
2. Abra o arquivo `index.html` em qualquer navegador moderno (Chrome, Edge, Firefox, Safari ou Opera).
3. **Passo 1:** Arraste ou selecione a planilha baixada do painel da Shopee.
4. **Passo 2:** Arraste o arquivo `.zip` ou os arquivos `.xml` exportados do Bling.
5. Confira os dados na tabela e clique em **"Baixar Planilha Shopee (Selecionados)"**.
6. Faça o upload do arquivo gerado no painel de **Envio em Massa da Shopee**. Pronto!

---

## 💻 Como Hospedar no GitHub Pages (Acesso Fácil)

Se você quiser deixar o sistema acessível online via link próprio (ex: `https://porcodobrejo.github.io/conciliador-nfe-shopee-bling`):

1. Crie um repositório no seu GitHub.
2. Faça o push dos arquivos deste projeto:
   ```bash
   git init
   git add .
   git commit -m "feat: conciliador de nf-e shopee bling"
   git branch -M main
   git remote add origin https://github.com/porcodobrejo/conciliador-nfe-shopee-bling.git
   git push -u origin main
   ```
3. No seu repositório no GitHub, vá em **Settings** > **Pages**.
4. Em **Branch**, selecione `main` e a pasta `/ (root)`.
5. Clique em **Save**. Em instantes, o seu conciliador estará no ar!

---

## 📁 Estrutura do Repositório

```text
conciliador-nfe-shopee-bling/
├── index.html                 # Página principal da aplicação
├── css/
│   └── styles.css             # Estilização moderna, limpa e responsiva
├── js/
│   ├── app.js                 # Lógica de interface, conciliação e eventos
│   ├── xml-parser.js          # Leitura de XMLs SEFAZ e descompactação de ZIPs
│   └── excel-handler.js       # Leitura e geração de planilhas SheetJS anti-notação
├── vendor/
│   ├── xlsx.full.min.js       # Biblioteca SheetJS embutida (offline)
│   └── jszip.min.js           # Biblioteca JSZip embutida (offline)
├── samples/
│   ├── exemplo_planilha_shopee.xlsx  # Planilha modelo para testes
│   ├── xmls_bling_exemplo.zip        # Pacote ZIP de exemplo com XMLs SEFAZ
│   └── xmls/                         # XMLs avulsos de demonstração
├── LICENSE                    # Licença MIT
└── README.md                  # Documentação do projeto
```

---

## 🧪 Dados de Teste Inclusos

Na pasta `samples/`, você encontrará arquivos prontos para teste imediato:
- `samples/exemplo_planilha_shopee.xlsx`: Planilha com códigos de pedido no formato Shopee (`260101SHP...`).
- `samples/xmls_bling_exemplo.zip`: Pacote compactado contendo XMLs autorizados com o número do pedido na tag `<infCpl>`.

---

## 📄 Licença

Este projeto é distribuído sob a licença **MIT**. Consulte o arquivo [LICENSE](LICENSE) para obter mais informações.
