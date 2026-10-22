# Monitor · Núcleo Vertentes & Módulo Guardiões

Plataforma oficial de monitoramento estratégico e governança de EJs do Núcleo Vertentes (FEJEMG).

Acesse online: [https://vivasse.github.io/reuni-oregional-vertentes/](https://vivasse.github.io/reuni-oregional-vertentes/)

---

## 🛡️ Módulo Guardiões

O módulo Guardiões conecta o ecossistema de dados das EJs do Núcleo Vertentes (faturamento, % da meta, gap, esperado até o mês, ECM, CSAT, Colaborativo e Cluster) a uma camada completa de governança e acompanhamento tático:

1. **Estrutura por Squads e Guardiões:**
   - **Squad Yann (Líder Yann):** Yann, Gustavo, Julia, Débora (13 EJs)
   - **Squad Guilherme (Líder Guilherme):** Guilherme, Ian, Marcos, Ana Clara (15 EJs)
   - **Squad Castanha (Líder Castanha):** Ana Luiza, Luiza, Kennedy (8 EJs)
   - **Sem Guardião inicial:** 11 EJs para atribuição direta pela plataforma.
   - O Squad de cada EJ decorre automaticamente do Guardião atribuído.

2. **Indicadores em Tempo Real:**
   - Nenhum indicador das EJs precisa ser digitado manualmente: todos são lidos em tempo real a partir da planilha oficial sincronizada (`D`) através do código da EJ (`cod`).
   - Badge `#cod` visível na tabela principal, na busca, no drawer lateral e na tabela de Guardiões.

3. **Governança & Gestão de Demandas:**
   - **5 KPIs Globais:** Total de EJs, % com Guardião, % Metas em Dia, Desatualizadas (&gt;30 dias) e Pendências Abertas.
   - **Cards de Squad Interativos:** Mostram faturamento consolidado do squad, distribuição visual de status (Meta Batida, Verde, Amarelo, Vermelho), lista de guardiões com contagem de EJs e filtro interativo ao clicar no card.
   - **Perfil do Guardião & Transferência em Lote:** Permite visualizar todas as EJs do guardião, suas pendências e transferir múltiplas EJs selecionadas para outro guardião em um clique.
   - **Atas de Reuniões Quinzenais:** Registro cronológico de reuniões por squad com data, pauta principal e encaminhamentos/decisões.
   - **Extensão do Drawer da EJ:** No painel lateral de qualquer EJ, é possível atribuir/trocar guardião, editar contato/presidente, atualizar código da EJ, gerenciar checklist de pendências e registrar acompanhamentos.

4. **Sincronização Nuvem (Firebase) & Cache Offline:**
   - Banco principal: Firebase Realtime Database (`https://roadmap-76391-default-rtdb.firebaseio.com/`).
   - Nó do módulo guardiões: `guardioes_mod` (independente e desacoplado de `monitor/latest`).
   - Carga inicial idempotente: executa automaticamente se a base estiver vazia, sem duplicar dados nem apagar registros existentes.
   - Cache local (`localStorage`): carregamento instantâneo mesmo sem conexão.

---

## 🔒 Regras de Segurança do Firebase (`roadmap-76391`)

Para proteger os dados e permitir que apenas usuários autorizados realizem alterações (com login Google integrado), configure as regras no console do Firebase:

### Realtime Database (`database.rules.json`)
```json
{
  "rules": {
    "monitor": {
      ".read": true,
      ".write": "auth != null"
    },
    "guardioes_mod": {
      ".read": true,
      ".write": "auth != null"
    }
  }
}
```
*Permite leitura pública para membros do núcleo acompanharem o dashboard e restringe escrita a usuários autenticados via Google.*

### Cloud Firestore (`firestore.rules`)
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /monitor/{document=**} {
      allow read: if true;
      allow write: if request.auth != null;
    }
    match /guardioes_mod/{document=**} {
      allow read: if true;
      allow write: if request.auth != null;
    }
    match /{document=**} {
      allow read, write: if request.auth != null;
    }
  }
}
```

---

## 📊 Relatório de Divergências Iniciais

Durante a integração da base de dados com o modelo de governança, foram identificadas as seguintes particularidades mapeadas para acompanhamento:

### 1. EJs Sem Código Inicial na Planilha de Origem
Três EJs da lista de acompanhamento não possuíam código numérico na alimentação inicial. Foram criados identificadores temporários estáveis que já estão vinculados aos seus respectivos guardiões e podem ser atualizados para o código definitivo a qualquer momento através do drawer da EJ:
- **Alpha:** `temp_alpha` (vinculada a Yann · Squad Yann)
- **Estoma Care:** `temp_estoma_care` (vinculada a Ana Luiza · Squad Castanha)
- **ATP Jr.:** `temp_atp_jr` (inicialmente sem guardião)

> *Como atualizar:* Abra o perfil da EJ clicando sobre o nome dela, altere o campo **"Código da EJ"** para o número definitivo e salve. Os vínculos e pendências serão migrados automaticamente.

### 2. EJs Sem Guardião Inicial (11 EJs)
As seguintes 11 EJs constavam sem guardião atribuído na distribuição inicial:
- `#428`
- `#3477`
- `#558`
- `#1744`
- `#1795`
- `#2341`
- `#2877`
- `#3197`
- `#3323`
- `#3395`
- `ATP Jr.` (`temp_atp_jr`)

> *Como atribuir:* Na aba Guardiões, filtre por "Sem Squad / Sem Guardião", clique no botão **"Acompanhar"** de cada EJ e selecione o Guardião responsável no dropdown.

### 3. EJs com Faturamento em Vermelho (Atenção Prioritária)
EJs que se encontram abaixo da meta escalonada para o mês de corte atual e requerem plano de ação prioritário dos guardiões e líderes de squad:
- EJs com maior desvio relativo para a meta anual (ex: gaps superiores listados no painel de Maiores Gaps).
- 4 pendências prioritárias já foram cadastradas na carga inicial com foco em levar membros ao ECM da FEJEMG (EJs `#1193`, `#2737`, `#1219` e `#220`).