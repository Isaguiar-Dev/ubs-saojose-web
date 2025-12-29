# Relatório de Auditoria Técnica - Front-End Senior (Foco: Engenharia & UX)

**Data:** 19/02/2025
**Projeto:** Portal ESF Catalão (React + Vite + Firebase)
**Auditor:** Jules (AI Senior Engineer)

---

## 1. Resumo Executivo

A auditoria analisou o código sob a ótica de engenharia de software, manutenibilidade e experiência do usuário (UX), desconsiderando restrições de Design System governamental.

O projeto demonstra boas escolhas tecnológicas (Vite, Tailwind, Lazy Loading), porém sofre de **débitos técnicos severos** que comprometem a escalabilidade e a manutenção a longo prazo. O uso excessivo de estilos inline e a falta de separação de responsabilidades (componentes definidos dentro de páginas) são os pontos mais críticos.

---

## 2. Classificação de Problemas

### 🔴 Alta Gravidade (Crítico - Engenharia & Clean Code)

#### 2.1. Uso Abusivo de Estilos Inline (Anti-Padrão React/CSS)
**Descrição:** Diversos componentes forçam estilos visuais diretamente na tag HTML via `style={{...}}`, especialmente para fontes.
**Contexto:** Arquivos como `Login.jsx`, `Vacinas.jsx`, `EstoqueVacinas.jsx` contêm centenas de repetições de `fontFamily: 'Arial, "Helvetica Neue"...'`.
**Impacto Técnico:**
- **Manutenibilidade Zero:** Alterar a fonte do site exigiria editar manualmente centenas de linhas em dezenas de arquivos.
- **Bloat de Código:** Aumenta desnecessariamente o tamanho do arquivo transferido.
- **Specificity Wars:** Estilos inline têm precedência sobre classes CSS/Tailwind, tornando difícil sobrescrever estilos quando necessário.
**Recomendação:** Remover todos os atributos `style` e configurar a fonte globalmente no `tailwind.config.js` ou `index.css`.

#### 2.2. Componentização Incorreta (Declarações Aninhadas)
**Descrição:** Definição de componentes auxiliares (ex: `PageContainer`, `InfoBox`, `Alert`) dentro do escopo do arquivo da página (ex: `Vacinas.jsx`).
**Impacto Técnico:**
- **Performance:** O React pode recriar esses componentes a cada renderização da página pai, causando perda de estado e processamento inútil.
- **Reutilização Nula:** Um `Alert` criado dentro de `Vacinas.jsx` não pode ser usado em `Home.jsx`, gerando duplicação de código.
- **Testabilidade:** Impossível testar esses componentes isoladamente.
**Recomendação:** Extrair esses componentes para arquivos próprios em `src/components/common/`.

---

### 🟡 Média Gravidade (Importante - Manutenibilidade & Consistência)

#### 2.3. Hardcoded Hex Colors vs. Tailwind Tokens
**Descrição:** Uso frequente de cores hexadecimais arbitrárias (ex: `#003882`) em vez da paleta definida no Tailwind (`bg-primary-800`).
**Impacto:**
- **Inconsistência Visual:** Pequenas variações de tom (ex: um azul `#003880` e outro `#003882`) passam despercebidas no código mas degradam o polimento visual.
- **Dificuldade de Tema:** Impossibilita "Dark Mode" ou mudanças de branding futuras sem refatoração massiva.
**Recomendação:** Padronizar todas as cores no `tailwind.config.js` e usar apenas classes (ex: `text-blue-900`).

#### 2.4. Dados "Chumbados" no JSX (Hardcoded Content)
**Descrição:** Tabelas de horários, listas de endereços e telefones estão escritos diretamente no código da interface.
**Impacto:** Desenvolvedores precisam atuar para mudar um número de telefone. Isso mistura "Dados" com "Apresentação".
**Recomendação:** Mover esses dados para arquivos JSON/JavaScript de configuração ou para o banco de dados.

#### 2.5. Duplicação de Código para Responsividade
**Descrição:** Em `Vacinas.jsx`, o conteúdo da tabela de horários é duplicado: existe uma estrutura HTML para Desktop e outra totalmente separada para Mobile.
**Impacto:** Risco altíssimo de divergência de informação (atualizar o horário no desktop e esquecer do mobile).
**Recomendação:** Usar CSS (Grid/Flex) para adaptar o *mesmo* conteúdo HTML para diferentes telas.

---

### 🟢 Baixa Gravidade (Melhorias de Polimento)

#### 2.6. Componentes Extensos ("God Components")
**Descrição:** `Home.jsx` possui ~450 linhas, misturando lógica de galeria, carrossel e apresentação.
**Solução:** Quebrar em sub-componentes menores (`HomeHero`, `HomeContact`, `HomeGallery`) para facilitar a leitura.

#### 2.7. Acessibilidade (Labels e Foco)
**Descrição:** Alguns botões de ícone (ex: redes sociais no rodapé) não possuem texto legível para leitores de tela (`aria-label`).
**Solução:** Adicionar `aria-label` descritivos.

---

## 3. Checklist de Ação Prioritária

1.  **Limpeza de Código (Refatoração Imediata):**
    - [ ] **Eliminar estilos inline:** Remover `style={{ fontFamily... }}` de todo o projeto. Deixar o Tailwind gerenciar a tipografia.
    - [ ] **Extrair Componentes:** Mover `InfoBox`, `Alert`, `PageContainer` de dentro das páginas para a pasta `components`.

2.  **Organização Visual:**
    - [ ] **Auditoria de Cores:** Substituir hexadecimais soltos por classes do Tailwind para garantir consistência visual profissional.

3.  **Arquitetura de Dados:**
    - [ ] **Centralizar Informações:** Criar um arquivo constante para telefones/endereços para evitar alterações manuais em múltiplos arquivos.

---

## 4. Conclusão Final

O site tem uma aparência profissional e moderna, mas o código "por trás das cortinas" apresenta fragilidades de engenharia que dificultarão o crescimento do projeto.

A prioridade absoluta deve ser a **limpeza do código (Clean Code)**: remover estilos inline e organizar a estrutura de componentes. Isso elevará o projeto de um "protótipo funcional" para um produto de software profissional e manutenível.
