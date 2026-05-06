# 📦 EstoqueMax — Controle de Inventário Mobile

> Aplicativo web progressivo (PWA) desenvolvido para realizar contagem de estoque com leitura de código de barras pela câmera, controle de validades e exportação de arquivos para integração com sistemas de gestão.

[![HTML](https://img.shields.io/badge/HTML5-E34F26?style=flat&logo=html5&logoColor=white)](https://developer.mozilla.org/en-US/docs/Web/HTML)
[![CSS](https://img.shields.io/badge/CSS3-1572B6?style=flat&logo=css3&logoColor=white)](https://developer.mozilla.org/en-US/docs/Web/CSS)
[![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat&logo=javascript&logoColor=black)](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
[![PWA](https://img.shields.io/badge/PWA-5A0FC8?style=flat&logo=pwa&logoColor=white)](https://web.dev/progressive-web-apps/)
[![IndexedDB](https://img.shields.io/badge/IndexedDB-003B57?style=flat&logo=sqlite&logoColor=white)](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API)

---

## 🌐 Demo ao Vivo

| Portal | Link |
|---|---|
| 📦 EstoqueMax App | [heberthaugusto.github.io/portfolio-app-estoquemax](https://heberthaugusto.github.io/portfolio-app-estoquemax/) |

> ℹ️ Para a experiência completa, acesse pelo **Chrome no Android** e instale como app via "Adicionar à tela inicial" → "Instalar". A leitura de código de barras pela câmera requer acesso via HTTPS.

---

## 🎯 Sobre o Projeto

O **EstoqueMax** nasceu da necessidade real de uma ferramenta de contagem de inventário que resolvesse um problema específico: registrar o mesmo produto com **múltiplas datas de validade distintas** — funcionalidade ausente nos aplicativos comerciais disponíveis.

O resultado é um PWA que funciona como um aplicativo nativo no Android, capaz de ler códigos de barras pela câmera, identificar produtos por um catálogo importável, registrar quantidades por validade e exportar os dados em formatos prontos para integração com sistemas de gestão (ERP/WMS).

---

## ✨ Funcionalidades Implementadas

### 📷 Contagem de Estoque
- Leitura automática de código de barras pela câmera traseira do dispositivo
- Identificação instantânea do produto pelo catálogo via IndexedDB (busca por índice)
- Inserção manual de código de barras com busca simultânea por SKU ou nome
- Suporte a produtos **sem data de validade** (campo opcional)
- Um mesmo produto pode ter **múltiplas validades** registradas separadamente
- Produtos não cadastrados no catálogo são registrados pelo código lido
- Edição e exclusão de lançamentos após registro
- Botão flutuante de acesso rápido ao scanner

### 📂 Catálogo de Produtos
- Importação de catálogo via arquivo `.txt` com separador `|` (pipeline)
- Suporte a catálogos grandes (testado com +60.000 produtos)
- Importação em lotes assíncronos com barra de progresso — sem travamento
- Cadastro manual de produtos com código de barras, nome, SKU, categoria e unidade
- Busca por nome, código de barras ou SKU em tempo real
- Busca por SKU com priorização de correspondência exata

### 📄 Exportação
- Gera **dois arquivos `.txt`** simultaneamente ao exportar:
  - `NOME_validade.txt` — 4 colunas separadas por `|`: código, nome, validade, quantidade
  - `NOME.txt` — 2 colunas: código (14 chars, padding direito) + quantidade (5 dígitos, zeros à esquerda)
- Integração com **Google Drive** via Web Share API nativa do Android
- Nome do arquivo configurável pelo campo "Nome da Contagem"
- Prévia do relatório em tela antes de exportar

### 🕐 Histórico
- Registro automático das últimas **20 contagens exportadas**
- Possibilidade de recarregar uma contagem anterior na tela de trabalho
- Confirmação de segurança ao sobrescrever contagem em andamento

### ⚙️ Outras Funcionalidades
- Funciona **100% offline** após instalação
- Dados persistidos localmente via **IndexedDB** (catálogo) e **localStorage** (contagens)
- Suporte a PWA: instalável na tela inicial, abre sem barra do navegador
- Service Worker para cache e funcionamento offline
- Interface responsiva otimizada para uso com uma mão

---

## 🏗️ Arquitetura e Decisões Técnicas

| Decisão | Justificativa |
|---|---|
| **PWA (sem framework)** | Instalável como app nativo sem loja de aplicativos. Vanilla JS mantém o app em um único arquivo `.html`, facilitando distribuição e atualização via GitHub Pages. |
| **IndexedDB para catálogo** | localStorage tem limite de ~5 MB — insuficiente para catálogos com +60k produtos (~9,5 MB). IndexedDB suporta centenas de MB sem limite prático. |
| **localStorage para contagens** | Contagens ativas têm poucos registros. localStorage é síncrono e mais simples para dados pequenos e frequentemente acessados. |
| **Busca por índice IDB** | Com 14k–60k produtos, cursor scan seria lento. Índices IDB permitem lookup O(log n) por barcode/SKU/nome — busca instantânea. |
| **html5-qrcode** | Biblioteca madura para leitura de códigos de barras (EAN-13, EAN-8, Code 128, QR Code) via câmera no browser, sem app nativo. |
| **Importação assíncrona em lotes** | Evita travamento da UI ao importar arquivos grandes. Processa 2.000 registros por tick do event loop com feedback de progresso. |
| **Web Share API** | Permite compartilhar arquivos diretamente para o Google Drive (ou qualquer app instalado) sem intermediários — menos cliques para o usuário. |
| **GitHub Pages** | Hospedagem estática gratuita com HTTPS, necessário para acesso à câmera no Chrome. |

---

## 📐 Formato dos Arquivos

### Arquivo de Catálogo (importação)
```
CÓDIGO_DE_BARRAS|NOME DO PRODUTO|SKU
7896051115090|LEITE CONDENSADO ITAMBE 1KG|572
7891008405019|BALA TOFEE GAROTO 200G|1234
2686|FITA CETIM LISA 10M|2686
```

### Arquivo de Exportação — Validades (`NOME_validade.txt`)
```
7896051115090|LEITE CONDENSADO ITAMBE 1KG|09-01-27|48
7891008405019|BALA TOFEE GAROTO 200G|26-11-26|72
```

### Arquivo de Exportação — Coletor (`NOME.txt`)
```
7896051115090 ;00048
7891008405019 ;00072
```
> Código de barras: 14 caracteres (padding com espaços à direita)
> Quantidade: 5 dígitos (padding com zeros à esquerda)

---

## 🛠️ Stack Tecnológica

```
Frontend:        HTML5 · CSS3 · JavaScript (ES2022)
Armazenamento:   IndexedDB · localStorage
Scanner:         html5-qrcode (CDN)
Tipografia:      Space Grotesk · JetBrains Mono (Google Fonts)
Hospedagem:      GitHub Pages
PWA:             Web App Manifest · Service Worker
Compartilhamento: Web Share API
```

---

## 📁 Estrutura do Projeto

```
portfolio-app-estoquemax/
├── index.html       Aplicativo completo (single-file PWA)
├── manifest.json    Configuração PWA (nome, ícone, tema)
├── sw.js            Service Worker (cache offline)
├── icon-192.png     Ícone para tela inicial (192×192)
├── icon-512.png     Ícone para splash screen (512×512)
└── README.md        Este arquivo
```

---

## 📱 Como Instalar no Android

1. Acesse o link do app pelo **Chrome**
2. Toque nos **3 pontinhos** (⋮) → **"Instalar app"**
3. Confirme a instalação
4. O app aparece na tela inicial e abre em tela cheia, sem barra do navegador

> Na primeira abertura após instalação, o Service Worker faz cache dos arquivos para uso offline.

---

## ⚙️ Como Fazer Deploy

1. Clone o repositório:
```bash
git clone https://github.com/heberthaugusto/portfolio-app-estoquemax.git
```

2. Faça upload dos 5 arquivos no repositório GitHub:
```
index.html · manifest.json · sw.js · icon-192.png · icon-512.png
```

3. Ative o **GitHub Pages**:
   - Settings → Pages → Source: Deploy from branch → `main` / `root`

4. Acesse em:
```
https://SEU_USUARIO.github.io/portfolio-app-estoquemax/
```

> Nenhuma dependência de servidor, banco de dados externo ou variável de ambiente. Tudo roda no browser do usuário.

---

## 🔒 Privacidade e Dados

Todos os dados (catálogo, contagens, histórico) são armazenados **exclusivamente no dispositivo do usuário** via IndexedDB e localStorage. Nenhuma informação é enviada para servidores externos. O app não requer login, conta ou conexão com a internet após o primeiro carregamento.

---

## 👤 Autor

Desenvolvido por **Heberth Augusto**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=flat&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/heberth-dornela-ti/)
[![GitHub](https://img.shields.io/badge/GitHub-100000?style=flat&logo=github&logoColor=white)](https://github.com/heberthaugusto)

---

*Projeto desenvolvido utilizando a **Claude IA** como assistente de desenvolvimento.*
