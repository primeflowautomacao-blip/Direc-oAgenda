# Norte — Sistema Pessoal

Aplicação pessoal (diário, agenda, metas, tempo, decisões, registo privado,
hábitos, revisões, "Meu Norte"...) num único ficheiro `index.html`, pronta
para GitHub Pages, com dados persistidos no Supabase.

## 1. Criar o projeto no Supabase

1. Cria uma conta em https://supabase.com e um novo projeto.
2. Abre **SQL Editor** → cola todo o conteúdo de `schema.sql` → **Run**.
   Isto cria todas as tabelas, índices e políticas de segurança (RLS).
3. Vai a **Project Settings → API** e copia:
   - **Project URL**
   - **anon public key** (nunca a `service_role` — essa nunca deve ir
     para o frontend)

## 2. Ligar a aplicação ao Supabase

Abre `index.html`, procura a secção `CONFIGURAÇÃO` perto do topo do
`<script type="module">` e substitui:

```js
const SUPABASE_URL = 'https://SEU-PROJETO.supabase.co';
const SUPABASE_ANON_KEY = 'SUA_CHAVE_ANON_PUBLICA';
```

pelos valores reais copiados no passo anterior. Guarda o ficheiro.

## 3. Código de acesso

O código inicial é **201017** (constante `ACCESS_CODE` no mesmo bloco de
configuração). Podes alterá-lo diretamente no código sempre que quiseres.

Nota importante sobre segurança: este código protege o *acesso visual* à
interface (impede acesso casual), mas a `anon key` do Supabase por si só
já obedece às políticas RLS definidas no `schema.sql`. Para uma aplicação
verdadeiramente pessoal isto é adequado; se um dia quiseres proteção mais
forte (ex: várias pessoas, dados muito sensíveis), o próximo passo seria
ativar o **Supabase Auth** (login com email/password) e trocar as
políticas RLS para usarem `auth.uid()`.

## 4. Publicar no GitHub Pages

1. Cria um repositório (idealmente **privado**, já que o `index.html`
   contém o URL e a `anon key` do teu Supabase, mesmo sendo uma chave
   "pública").
2. Faz commit de `index.html` (podes ignorar `schema.sql` e este
   `README.md`, ou incluí-los — não afetam a aplicação).
3. Em **Settings → Pages**, escolhe a branch `main` e a pasta `/root`.
4. Guarda. Ao fim de 1–2 minutos a aplicação estará disponível no URL
   que o GitHub Pages indicar.

## 5. Sessão / bloqueio

Por predefinição, o desbloqueio dura apenas enquanto a página está
aberta (sem `localStorage`) — ao recarregar a página, pede o código de
novo. Se preferires que a sessão persista entre recarregamentos no teu
telemóvel/computador (fora do preview do Claude), troca estas duas
linhas no bloco `LOCK SCREEN`:

```js
function unlockApp(){
  state.unlocked = true;
  // localStorage.setItem('norte_unlocked_at', Date.now()); // ativa se quiseres persistência
  ...
```

e no arranque da aplicação, antes de mostrar o ecrã de bloqueio, lê esse
valor e chama `unlockApp()` se ainda estiver dentro do período que
definires (ex: 12 horas).

## 6. Estrutura de dados

Todas as tabelas estão descritas e comentadas em `schema.sql`. Resumo:

| Tabela | Módulo |
|---|---|
| `journal_entries` | Diário |
| `quick_notes` | Pensamentos rápidos |
| `private_logs` | Registo privado (factos vs. interpretação) |
| `decisions` | Decisões |
| `goals` | Metas (com `parent_id` para sub-metas, ex. Inglês → Input/Output) |
| `time_logs` | Registos de tempo (manual e cronómetro), ligados a `goals` |
| `tasks` | Tarefas |
| `events` | Agenda |
| `prayers`, `bible_studies`, `gratitude_entries` | Espiritual |
| `habits`, `habit_logs` | Hábitos |
| `weekly_reviews`, `monthly_reviews` | Revisões |
| `meu_norte` | Meu Norte (linha única) |

## 7. O que está incluído nesta primeira versão

Totalmente funcional, com CRUD real no Supabase: Dashboard, Diário,
Pensamentos rápidos, Registo privado, Decisões, Metas (incl. Inglês
Input/Output), Controlo de tempo (manual + cronómetro), Estudos,
Tarefas, Agenda, Espiritual (Orações, Bíblia, Gratidão), Hábitos,
Timeline, Pesquisa global, Estatísticas (gráficos), Revisões semanal e
mensal, Meu Norte, Configurações com exportação JSON/CSV.

Ideias para uma próxima iteração: anexos em registos privados,
importação de backup, dashboard personalizável (arrastar/soltar),
notificações locais, modo claro (atualmente é dark mode fixo — pode ser
adicionado como um segundo tema com uma variável `data-theme`).

## 8. Dados de exemplo

Esta versão não insere dados de demonstração automaticamente — abre já
"vazia" e pronta para os teus próprios dados, para não teres de limpar
nada.
