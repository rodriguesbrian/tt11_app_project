# Análise de Funil e Experimento A/A/B – Aplicativo Mobile

Este projeto analisa logs de eventos de um aplicativo mobile para:

1. Entender o comportamento do usuário via funil de conversão.
2. Validar a consistência de um teste A/A.
3. Avaliar o impacto de uma alteração de fonte via experimento A/B.

A pergunta central é:

> A mudança testada realmente melhora conversão ou estamos observando ruído estatístico?

---

# 📂 Base de Dados

Arquivo:

`/datasets/logs_exp_us.csv`

Colunas originais:

- EventName — nome do evento  
- DeviceIDHash — identificador único do usuário  
- EventTimestamp — timestamp do evento  
- ExpId — grupo experimental (246, 247 = controle; 248 = teste)

---

# 1️⃣ Preparação dos Dados

### Etapas realizadas:

- Renomeação de colunas para:
  - `event_name`
  - `user_id`
  - `event_time`
  - `group`
- Conversão de `event_time` para datetime
- Criação de:
  - `event_datetime`
  - `event_date`
- Verificação de:
  - Valores ausentes
  - Duplicatas
  - Tipos inconsistentes
- Validação da presença dos três grupos experimentais

---

# 2️⃣ Exploração Inicial

### 📊 Volume de dados

- Total de eventos
- Total de usuários únicos
- Média de eventos por usuário

---

### 📅 Período de cobertura

- Data mínima
- Data máxima
- Histograma por data e hora

Análise crítica:

Verificar se o início do período contém poucos eventos (dados incompletos).  
Identificar ponto de estabilização do volume.

Definir novo intervalo de análise (período realmente representativo).

---

### 📉 Impacto da limpeza temporal

- Eventos removidos
- Usuários removidos
- Proporção perdida

Validação de que os três grupos continuam representados.

---

# 3️⃣ Funil de Eventos

---

## 📌 Frequência de eventos

- Contagem total por `event_name`
- Ordenação decrescente

---

## 📌 Usuários por evento

- Número de usuários únicos por evento
- Proporção sobre total de usuários

Permite identificar:

- Etapas principais do produto
- Eventos marginais

---

## 📌 Ordem lógica do funil

Identificação da sequência típica, por exemplo:

1. Tela principal
2. Tela de produto
3. Carrinho
4. Pagamento

Nem todos os eventos fazem parte do funil principal — excluir eventos auxiliares.

---

## 📊 Conversão entre etapas

Para cada transição A → B:

Conversão = usuários em B / usuários em A

Identificar:

- Onde ocorre maior queda
- Taxa final de conclusão (início → pagamento)

---

# 4️⃣ Análise do Experimento

---

## 📌 Distribuição de usuários por grupo

Contagem de usuários em:

- Grupo 246 (Controle)
- Grupo 247 (Controle)
- Grupo 248 (Teste)

Validação de balanceamento.

---

# Teste A/A – Validação do Experimento

---

## 📌 Comparação 246 vs 247

Objetivo:

Garantir que os mecanismos de divisão e cálculo estão corretos.

Procedimento:

- Selecionar evento mais popular
- Calcular proporção de usuários que executaram o evento em cada grupo
- Teste estatístico de proporções (z-test)

Repetir para todos os eventos.

Se houver diferenças significativas frequentes → problema na randomização.

---

# Teste A/B – Grupo 248 vs Controles

---

## 📌 Comparação individual

- 248 vs 246
- 248 vs 247

Para cada evento:

- Número de usuários
- Proporção
- Teste de significância

---

## 📌 Comparação com controles combinados

Unir 246 + 247.

Comparar:

- Controle combinado vs 248

Interpretação:

- Impacto consistente?
- Impacto apenas em eventos intermediários?
- Impacto no pagamento final?

---

# Controle de Erro Estatístico

---

## 📌 Número de testes realizados

Para cada evento:
- A/A
- A/B (3 comparações)

Total de testes = eventos × comparações

---

## 📌 Ajuste de significância

Se α = 0.1:

1 em cada 10 testes pode ser falso positivo.

Aplicar correção:

- Bonferroni (α / número de testes)
ou
- Ajuste mais conservador

Reexecutar testes se necessário.

---

# 📊 Métricas Principais

- Total de eventos
- Usuários únicos
- Eventos por usuário
- Conversão por etapa
- Conversão final
- Teste z para proporções
- p-value ajustado

---

# 📌 Conclusões Esperadas

1. O funil mostra claramente onde usuários abandonam.
2. O teste A/A confirma (ou não) robustez experimental.
3. O grupo teste apresenta (ou não) melhoria estatisticamente significativa.
4. A decisão final é baseada em:
   - Significância
   - Tamanho do efeito
   - Impacto na conversão final

---

# 🧠 Decisão Final

Possibilidades:

- Implementar mudança
- Rejeitar mudança
- Reexecutar experimento com ajustes

Decisão fundamentada em:

- Consistência estatística
- Robustez do funil
- Risco de falso positivo

---

# 📦 Como Executar

1. Clone o repositório:

```bash
git clone https://github.com/rodriguesbrian/tt11_app_project
cd tt11_app_project