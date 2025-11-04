# Kabum
N8N
# Documentação Kabum inteligence

---

## 1. **Objetivo do Workflow**

Automatizar a elaboração, pesquisa, redação, publicação e arquivamento de uma revista digital (newsletter) de tecnologia, com foco principal em Moçambique, cobrindo ainda África e mundo. Todo processo — da extração de pautas, validação de fontes, geração de conteúdo SEO, produção visual e publicação em WordPress — ocorre em pipeline, integrando diversas ferramentas e APIs, reduzindo o trabalho manual de toda equipe editorial.

---

## 2. **Visão do Fluxo**

- **Entrada**: PDFs de revistas anteriores, scheduler ou trigger manual.
- **Saída**: Revista digital em texto (Word, HTML, .txt), arquivo arquivado no Google Drive, artigo publicado via WordPress, e envio de conteúdo por e-mail.
- **Foco editorial**: Mais de 70% das matérias precisam abordar Moçambique.

---

## 3. **Arquitetura do Workflow**

### a) **Disparo & Preparação de Dados**
- **Start Workflow / Schedule Trigger**: Permite execução manual ou agendada (diária/semanal).
- **Google Drive Search PDF Files**: Busca PDFs da revista em pasta específica do Google Drive.

### b) **Download & Extração**
- **Download Files**: Baixa cada PDF para o ambiente n8n.
- **Extract From File**: Extrai texto bruto do PDF (OCR caso necessário).
- **Aggregate Results**: Consolida todos textos extraídos para processamento unificado.

### c) **Extração Avançada de Dados**
- **Extract Magazine Data (Node JS-Code)**:  
  - Funções JS customizadas processam o texto extraído para:
    - Limpar, padronizar e identificar dados (edição, data, manchete, equipe, títulos de artigos, nomes de empresas ou pessoas, estatísticas, setores, prêmios, termos técnicos, etc).
    - Transforma o PDF não estruturado em um grande JSON semântico com todos os campos essenciais da revista.
  - Garante flexibilidade e robustez, lidando com erros de OCR e inconsistências de layout.

### d) **Geração de Queries SEO**
- **AI Agent3 + Google Gemini Chat Model4**:  
  - Prompt customizado ensina a IA a gerar queries de pesquisa SEO para conteúdo local/moçambicano.
  - São geradas 100 queries estratégicas em português, priorizando fontes confiáveis e temas aderentes à linha editorial (startups, inovação governamental, fintech, entrevistas, tendências globais, etc).

### e) **Consulta, Extração e Validação de Resultados**
- **Information Extractor2 + Google Gemini Chat Model5**:  
  - Valida cada query (se existe fonte real, se retorna conteúdo em português, se é de Moçambique, etc).
  - Extrai e organiza os links reais e informações principais para as matérias.

### f) **Pesquisa Online Automatizada**
- **HTTP Request1 (API Tavily)**:  
  - Consulta API externa para coletar conteúdo real dos links e queries validados.
  - Recebe dados noticiosos em bruto (ou confidenciais) para uso direto na redação.

### g) **Geração de Conteúdo Jornalístico**
- **AI Agent4 + Google Gemini Chat Model6**:  
  - IA recebe dois insumos: 1) estrutura-modelo (template) e 2) resultados validados.
  - Produz artigos jornalísticos (em português, com formatação, tom profissional, citações e referências) fielmente baseados nos dados coletados; proíbe invenções ou links falsos.
  - Segue estilos, seções, organização, e fluxo da revista original — com separação de tópicos por região: Nacional (Moçambique), África, Internacional.

### h) **Conversão, Armazenamento e Publicação**
- **Convert to File2**:  
  - Converte resultado final em arquivo formato texto UTF-16 (p/ compatibilidade).
- **Upload file2 (Google Drive)**:  
  - Armazena automaticamente na pasta correta de arquivos da revista.
- **Send a Message2 (Gmail)**:   
  - Envia por e-mail o arquivo da edição final para os responsáveis.
- **Busca, Redimensionamento e Upload de Imagem**:  
  - Pesquisa uma imagem de destaque à revista, redimensiona para padrões do site e sobe no WordPress.
- **Publicação em WordPress**:  
  - Artigos completos são publicados via REST API do WordPress, já com as imagens destacadas.

---

## 4. **Detalhamento de Cada Etapa**

### 4.1. **Extração Estruturada de Dados (Node JS-Code)**
- Varre o texto em busca dos padrões mais relevantes para uma revista:
    - **Edição, data, manchete, equipe editorial, pessoas e empresas, títulos, termos técnicos, prêmios, instituições, estatísticas, setores, fundos e investimentos, citações, localização, segmentos do artigo, entre outros.**
    - Extrai listas de artigos, títulos e conteúdos, para alimentar buscas futuras e manter coerência de edição.
- Valida cada dado, normaliza nomes próprios, filtra caracteres especiais e remove inutilidades como anúncios.

### 4.2. **SEO Queries & Validação**
- Queries são pensadas pra fontes confiáveis (clubofmozambique.com, jornalnoticias.co.mz, Zitamar News etc) com operadores de busca otimizados (site:, "quoted", since:).
- IA valida se o link é verdadeiro e retorna resultado; se não, busca alternativa.
- Resultados recebidos são sempre atualizados, relevantes e rastreáveis.

### 4.3. **Geração de Conteúdo**
- IA segue um *template-comando* detalhado:
    - Estrutura: capa, sumário, PUBLICIDADE, artigos principais/intercalados, citações em destaque, preview, contatos.
    - Exige separação por região e cobertura obrigatória de Moçambique.
    - Comprovação dos links e fontes em todas matérias.
    - Não repete tópicos nem links; proíbe links fake ou conteúdo inventado.
- Todo artigo leva no fim o link da fonte de onde a informação foi extraída.

### 4.4. **Publicação e Notificação**
- O conteúdo é convertido em `.txt` e enviado ao responsável.  
- Upload automático mantém o histórico da revista sempre atualizado no Google Drive.
- Imagem de capa tratada e publicada no WordPress, automatizando o ciclo editorial.
- E-mails são enviados tanto para redatores quanto para clientes/leitores cadastrados.

---

## 5. **Principais Inovações e Cuidados**

- **Prioridade para Moçambique:** As automações, IA, queries e validações sempre buscam garantir que +70% do conteúdo seja nacional.
- **Rastreamento e compliance:** Toda matéria obrigatoriamente contém links reais; conteúdos são checados contra a fonte antes de publicação.
- **Template vivo e adaptável:** Estrutura pode ser customizada via prompt, novas seções podem ser incluídas conforme evolução do projeto.
- **Redação e publicação autossuficientes:** A equipe editorial só interfere para ajustes finos — todo ciclo (pauta até publicação) pode rodar sozinho.

---

## 6. **Casos de Uso & Expansão**

- **Produção semanal/mensal de revistas digitais jornalísticas automatizadas.**
- **Base para newsletters e tabloides temáticos para setores de inovação, educação ou empreendedorismo digital.**
- **Pode ser adaptado para cobrir outros países, outros idiomas, ou outras temáticas (saúde, educação, cultura digital etc) mudando apenas fontes e prompts.**

---

## 7. **Fluxograma Resumido**

Disparo ⭢ Busca PDFs Drive ⭢ Download PDFs ⭢ Extração OCR/Textual ⭢ Agregação ⭢ JS Extractor (dados estruturados) ⭢ Geração de 100 queries de pesquisa (IA) ⭢ Validação + busca links (AI + External API) ⭢ Geração de matérias com estrutura e tom da revista original (AI) ⭢ Conversão arquivo ⭢ Upload Drive ⭢ Publicação WordPress ⭢ E-mail.

---

## 8. **Observações Finais e Boas Práticas**

- **FACTOR crítico**: O workflow garante que nenhuma matéria/dado seja inventado — sempre há link/fonte.
- **Estrutura modular**: Qualquer etapa pode ser ajustada/personalizada sem interromper demais partes.
- **Total compliance**: O sistema é 100% rastreável, auditável e adaptável a novas necessidades.
- **Escalabilidade**: Fácil de ampliar para mais fontes, novos formatos de publicação e outras equipes/redatores.

---

## 9. **Manutenção**

- **Atente** para mudanças em APIs externas (Google Drive, WordPress, tavily.com).
- **Revise periodicamente** os prompts e fontes SEO, para manter a aderência e atualização editorial.
- **Monitore a qualidade dos dados extraídos**: se PDFs mudarem de padrão, reescreva funções do Node JS código de extração.

---
