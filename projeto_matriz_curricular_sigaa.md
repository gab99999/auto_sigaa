# Projeto: Sistema de Comparação e Cadastro de Matriz Curricular (SIGAA)

**Contexto:** Estágio na Diretoria de Atividades Acadêmicas (DAA) — UFCAT.
**Objetivo:** Agilizar o processo de comparação entre matrizes curriculares (antiga vs nova) e, posteriormente, automatizar a aplicação dos dados no SIGAA.
**Fonte do fluxo manual atual:** PDF "Cadastro de Matriz Curricular" (DAA/UFCAT).

---

## Decisões já tomadas

- Linguagem: Python
- Automação web: Playwright (API **síncrona**, sem necessidade de async por enquanto)
- Critério de diferença entre matrizes: **qualquer mudança** conta (granularidade de caractere, inclusive pontuação)
- Estratégia de flexibilidade a seguir: **Nível 1** primeiro (config JSON com seletores fixos) — Nível 2 (agente com LLM lendo o DOM) é evolução futura, não prioridade agora
- Fonte de dados da primeira etapa: ainda em definição (PDF ou scraping do próprio SIGAA)

---

## Fase 0 — Levantamento
- [ ] Conseguir acesso a ambiente de teste/homologação do SIGAA (**não treinar em produção**)
- [ ] Percorrer manualmente o fluxo completo do PDF uma vez
- [ ] Anotar campos obrigatórios e formatos específicos (ex: padrão do código da matriz)
- [ ] Abrir DevTools (F12) e levantar seletores (`id`, `name`) de cada campo, tela por tela:
  - [ ] Tela "Dados Gerais"
  - [ ] Tela "Núcleos e Componentes"
  - [ ] Tela "Componentes Curriculares" (adicionar/remover por período)
  - [ ] Tela "Resumo/Confirmação"
- [ ] Definir lista final de campos do modelo `Disciplina`/`MatrizCurricular` com base no levantamento real

## Fase 1 — Modelo de dados (POO)
- [ ] Criar classe `Disciplina` (`@dataclass`) com os campos levantados
- [ ] Criar classe `MatrizCurricular` (lista de `Disciplina` + dados gerais: curso, turno, código, cargas horárias)
- [ ] Testar `__eq__` manualmente (duas instâncias quase idênticas, 1 caractere de diferença) e confirmar que são detectadas como diferentes

## Fase 2 — Comparação (core do sistema)
- [ ] Escrever `comparar_matrizes(antiga, nova)` → retorna: removidas / adicionadas / alteradas
- [ ] Usar `difflib` para mostrar o que mudou campo a campo nas alteradas
- [ ] Testar com dados fictícios (2-3 matrizes "na mão", sem PDF nem SIGAA ainda)
- [ ] **Ponto de validação:** confirmar 100% de confiabilidade antes de seguir para a Fase 3

## Fase 3 — Extração de dados reais
- [ ] Escolher a primeira fonte real (PDF ou scraping de consulta do SIGAA)
- [ ] Escrever o "tradutor" da fonte → modelo `Disciplina` (isolado da lógica de comparação)
- [ ] Rodar a Fase 2 com dados reais extraídos aqui; corrigir divergências de parsing

## Fase 4 — Aplicação no SIGAA (automação)
- [ ] Instalar Playwright, testar exemplo básico (preencher um campo, navegar até a tela de cadastro)
- [ ] Criar classe abstrata `SistemaAcademico` (`abc.ABC`) com métodos: `preencher_dados_gerais`, `adicionar_componente`, `confirmar`
- [ ] Implementar `SigaaMatrizCurricular(SistemaAcademico)` usando os seletores da Fase 0
- [ ] Testar cadastro de **uma disciplina simples**, ponta a ponta, em ambiente de teste, acompanhando visualmente
- [ ] Expandir para cadastrar a lista completa (apenas itens "adicionados"/"alterados" vindos da comparação)

## Fase 5 — Integração e revisão
- [ ] Ligar as pontas: comparar → gerar lista do que precisa ser aplicado → aplicar via Playwright
- [ ] Adicionar etapa de confirmação manual antes de submeter (sem automação 100% cega em dado oficial)
- [ ] Documentar o fluxo completo para o relatório do estágio

## Fase 6 — Futuro (opcional)
- [ ] Avaliar investir no Nível 2: agente que lê o DOM e mapeia campos via LLM (dificuldade estimada: ~75-85 vs ~35-40 do Nível 1)

---

## Notas e aprendizados (ir preenchendo conforme avança)
-

## Bloqueios / dúvidas em aberto
-
