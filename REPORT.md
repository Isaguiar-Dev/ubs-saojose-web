# Relatório de Auditoria Técnica - Front-End Senior

**Data:** 19/02/2025
**Projeto:** Portal ESF Catalão (React + Vite + Firebase)
**Auditor:** Jules (AI Senior Engineer)

---

## 1. Resumo Executivo

O projeto apresenta uma base sólida em React com Vite e Tailwind CSS, demonstrando preocupação com performance (lazy loading) e uma estrutura de pastas organizada. No entanto, foram identificados **problemas estruturais graves (Alta Gravidade)** relacionados à **Qualidade de Código**, **Manutenibilidade** e **Adesão ao Design System GovBR**.

A violação mais crítica é o uso disseminado de estilos inline (`style={{...}}`) e definições de componentes dentro de arquivos de página, o que quebra princípios fundamentais do React e dificulta a escala do projeto.

---

## 2. Classificação de Problemas

### 🔴 Alta Gravidade (Crítico - Corrigir Imediatamente)

#### 2.1. Uso Disseminado de Estilos Inline e Fontes Hardcoded
**Descrição:** Diversos componentes e páginas forçam a fonte Arial via estilo inline, ignorando a configuração global do Tailwind e do Design System GovBR (Rawline).
**Impacto:** Quebra a consistência visual, dificulta a manutenção global (alterar a fonte exigiria editar centenas de arquivos) e aumenta o tamanho do bundle.
**Ocorrências:**
- `Login.jsx`, `Vacinas.jsx`, `EstoqueVacinas.jsx` e muitos outros.
- Código encontrado: `style={{ fontFamily: 'Arial, "Helvetica Neue", Helvetica, sans-serif' }}`.
**Solução:**
- Remover **todos** os atributos `style` relacionados a fontes.
- Garantir que `tailwind.config.js` defina `Rawline` como a fonte padrão da família `sans`.
- Usar classes utilitárias (ex: `font-sans`) se necessário.

#### 2.2. Componentização Inadequada (Anti-Padrão)
**Descrição:** Definição de componentes (como `PageContainer`, `InfoBox`, `Alert`) dentro do mesmo arquivo da página (ex: `Vacinas.jsx`).
**Impacto:** Impede a reutilização real do código, gera duplicação (o mesmo `Alert` pode estar redefinido em 10 páginas diferentes) e torna o código difícil de testar.
**Solução:**
- Mover componentes reutilizáveis para a pasta `src/components/common/` ou `src/components/layout/`.
- Importar esses componentes nas páginas.

#### 2.3. Cores Hardcoded (Violação do Design System)
**Descrição:** Uso frequente de valores Hexadecimais arbitrários (`#003882`, `#f0f7ff`) em vez dos tokens do Design System configurados no Tailwind (`bg-primary-500`, `text-neutral-700`).
**Impacto:** Inconsistência visual com a identidade GovBR e dificuldade em manter temas.
**Solução:**
- Substituir hexadecimais por classes do Tailwind configuradas no `tailwind.config.js`.
- Ex: Trocar `bg-[#003882]` por `bg-primary-800` (ou o token correspondente).

---

### 🟡 Média Gravidade (Importante - Corrigir a Médio Prazo)

#### 2.4. Conteúdo Hardcoded em JSX
**Descrição:** Tabelas de horários, listas de endereços e telefones estão "chumbados" no meio do JSX (`Vacinas.jsx`, `Footer.jsx`).
**Impacto:** Qualquer alteração de telefone ou horário requer intervenção de um desenvolvedor e deploy de código.
**Solução:**
- Extrair esses dados para arquivos de configuração (JSON/Constants) em `src/data/` ou vir do Firebase (Firestore).
- Mapear esses dados no JSX (`data.map(...)`).

#### 2.5. Duplicação de Layout (Mobile vs Desktop)
**Descrição:** Em `Vacinas.jsx`, a tabela de horários é escrita duas vezes: uma `<table>` para desktop e uma lista de `<div>` para mobile.
**Impacto:** Se o horário mudar, o desenvolvedor precisa lembrar de alterar em dois lugares. Risco alto de inconsistência.
**Solução:**
- Criar um componente único que aceite os dados e use classes CSS (Grid/Flex) para se adaptar responsivamente, sem duplicar o conteúdo no DOM.

#### 2.6. Acessibilidade (ARIA e Contraste)
**Descrição:**
- `Footer.jsx`: Links de redes sociais não possuem `aria-label`, sendo lidos apenas como "link" por leitores de tela.
- `Sidebar.jsx`: O foco do teclado não é "preso" (trapped) dentro do menu mobile quando aberto.
**Solução:**
- Adicionar `aria-label="Instagram"` nos links.
- Implementar Focus Trap no menu mobile.

---

### 🟢 Baixa Gravidade (Melhorias e Polimento)

#### 2.7. Componentes Gigantes
**Descrição:** Páginas como `Home.jsx` (~450 linhas) e `EscalasAdmin.jsx` (~560 linhas) contêm lógica de negócio, dados e apresentação misturados.
**Solução:** Extrair lógicas complexas para Custom Hooks (`useGallery`, `useEscalas`) e quebrar a UI em subcomponentes menores.

#### 2.8. Magic Numbers em CSS
**Descrição:** `Header.jsx` usa paddings fixos (`pl-[200px]`) que podem quebrar em larguras de tela não previstas.
**Solução:** Usar Grid ou Flexbox fluidos.

---

## 3. Checklist de Correção (Plano de Ação)

1.  **Limpeza Global (Clean Code):**
    - [ ] Executar um "Find & Replace" inteligente para remover a injeção inline de `fontFamily: Arial`.
    - [ ] Mover componentes internos (`InfoBox`, `Alert`) de `Vacinas.jsx` para `src/components/common`.

2.  **Design System & UI:**
    - [ ] Padronizar cores usando exclusivamente as classes `text-primary-*`, `bg-neutral-*` do Tailwind.
    - [ ] Remover gradientes que fogem do padrão GovBR (ex: `Home.jsx`) ou ajustá-los para tons mais sutis da paleta oficial.

3.  **Acessibilidade:**
    - [ ] Adicionar `aria-label` em todos os botões de ícone.
    - [ ] Validar contraste de texto em botões com fundo colorido.

4.  **Arquitetura:**
    - [ ] Criar arquivo `src/data/contact-info.js` para centralizar telefones e endereços.
    - [ ] Refatorar `Home.jsx` para consumir esses dados.

---

## 4. Conclusão

O site possui potencial e uma boa base tecnológica, mas "peca" em disciplina de código e rigor arquitetural. As correções de **Alta Gravidade** são imperativas para que o projeto seja considerado de nível "Sênior" e profissional, garantindo manutenibilidade e conformidade com os padrões governamentais.
