# Relatório de Auditoria Técnica - Front-End Senior (Foco: Engenharia & UX)

**Data:** 19/02/2025
**Projeto:** Portal ESF Catalão (React + Vite + Firebase)
**Auditor:** Jules (AI Senior Engineer)

---

## 1. Resumo Executivo

A auditoria técnica avaliou o projeto focando em manutenibilidade, escalabilidade e performance, desconsiderando restrições de Design System governamental.

O projeto apresenta uma estrutura organizada e boas práticas modernas (Vite, Lazy Loading), mas possui **débitos técnicos críticos** que ameaçam sua evolução. O uso disseminado de estilos inline (impedindo temas globais) e a duplicação de lógica para responsividade são os maiores ofensores. Além disso, foram identificados gargalos de performance no consumo do Firebase.

---

## 2. Classificação de Problemas

### 🔴 Alta Gravidade (Crítico - Engenharia & Arquitetura)

#### 2.1. Uso Abusivo de Estilos Inline (Clean Code / CSS)
**Descrição:** Dezenas de arquivos (`Login.jsx`, `Vacinas.jsx`) aplicam estilos visuais diretamente no JSX, sobrescrevendo o CSS global.
**Contexto:** `style={{ fontFamily: 'Arial, "Helvetica Neue", Helvetica, sans-serif' }}` aparece repetidamente.
**Impacto Técnico:**
- **Bloqueio de Evolução Visual:** É impossível alterar a tipografia do site globalmente sem editar centenas de arquivos.
- **Peso do Bundle:** Repetição desnecessária de strings de estilo aumenta o tamanho do JavaScript.
- **Conflito de Especificidade:** Inline styles vencem classes CSS, dificultando o uso de utilitários do Tailwind.
**Recomendação:** Remover **todos** os estilos inline e definir a família de fontes no `tailwind.config.js`.

#### 2.2. Componentização Anti-Padrão (Nested Components)
**Descrição:** Declaração de componentes (`InfoBox`, `Alert`) dentro do corpo de outro componente ou arquivo de página.
**Impacto Técnico:**
- **Performance:** O React recria a definição da função a cada renderização, forçando o "remount" dos componentes filhos e perdendo estado/foco.
- **Reutilização:** Impede que outras páginas usem esses elementos comuns.
**Recomendação:** Mover imediatamente para `src/components/common/`.

#### 2.3. Responsividade via Duplicação de DOM
**Descrição:** Em vez de usar CSS responsivo, o código duplica o conteúdo HTML: um bloco para `hidden md:block` (Desktop) e outro para `md:hidden` (Mobile).
**Exemplo:** Tabela de horários em `Vacinas.jsx`.
**Impacto Técnico:**
- **Risco de Inconsistência:** Alterar uma informação requer editar dois lugares diferentes.
- **Manutenção Dobrada:** Qualquer ajuste de layout deve ser replicado.
**Recomendação:** Usar classes utilitárias (ex: `grid-cols-1 md:grid-cols-3`) para adaptar o *mesmo* HTML a diferentes telas.

---

### 🟡 Média Gravidade (Performance & Dados)

#### 2.4. Filtragem de Dados no Cliente (Firebase)
**Descrição:** A função `buscarCampanhas` baixa todos os documentos da coleção e depois filtra por data (`dataFim`) via JavaScript no navegador (`campanhas.filter(...)`).
**Impacto Técnico:**
- **Custo & Performance:** Conforme o banco cresce, o usuário baixa megabytes de dados inúteis, consumindo banda e aumentando a conta do Firebase (leituras desnecessárias).
**Recomendação:** Implementar índices compostos no Firestore e realizar a filtragem (`where('dataFim', '>=', new Date())`) diretamente na query do banco de dados.

#### 2.5. Hardcoded Hex Colors
**Descrição:** Uso de cores hexadecimais soltas (ex: `#003882`) em vez de tokens do Tailwind (`bg-primary-800`).
**Impacto:** Dificulta a manutenção de uma identidade visual consistente e impede a implementação fácil de temas (Dark Mode/Alto Contraste).
**Recomendação:** Padronizar cores no `tailwind.config.js`.

#### 2.6. Conteúdo "Chumbado" no Código
**Descrição:** Tabelas de horários, endereços e telefones estão escritos diretamente no JSX das páginas.
**Recomendação:** Extrair para constantes (`src/data/constants.js`) ou mover para o CMS (Firestore).

---

### 🟢 Baixa Gravidade (Polimento)

#### 2.7. Componentes "Deuses"
**Descrição:** `Home.jsx` acumula muitas responsabilidades (Hero, Galeria, Contato).
**Recomendação:** Refatorar em componentes menores (`HomeHero`, `HomeContact`) para melhorar a legibilidade.

#### 2.8. Acessibilidade Básica
**Descrição:** Links de redes sociais e botões de ícone sem `aria-label`.
**Recomendação:** Adicionar descrições textuais para leitores de tela.

---

## 3. Plano de Ação (Prioridades)

1.  **Saneamento do Código (Semana 1):**
    - [ ] Remover estilos inline de fontes.
    - [ ] Extrair componentes aninhados para arquivos globais.
2.  **Performance (Semana 1):**
    - [ ] Otimizar query do Firestore em `campanhasService.js` para filtrar data no servidor.
3.  **Arquitetura (Semana 2):**
    - [ ] Centralizar dados estáticos (telefones/horários) em arquivos de configuração.
    - [ ] Refatorar layouts duplicados (Mobile/Desktop) para usar CSS responsivo.

---

## 4. Conclusão

O projeto possui uma base tecnológica moderna, mas precisa de rigor na engenharia de software. A correção dos estilos inline e da arquitetura de componentes é urgente para garantir que o sistema possa escalar e ser mantido por uma equipe profissional.
