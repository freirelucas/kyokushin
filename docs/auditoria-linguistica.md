# Auditoria de riqueza linguística — Atlas Kyokushin

Data: 30/09/2026 · Escopo: todo o conteúdo textual de `index.html` (léxico `NODES`, 54 técnicas, 22 kata, 21 renraku, requisitos de faixa e os textos renderizados de todas as abas).

## Resumo

O Atlas tem um português denso e variado e uma etimologia cuidadosa nos 42 nós do léxico. O ponto fraco estava na **ponte entre o japonês e o português**:

- 18 das 47 palavras que formam os nomes das técnicas não tinham tradução.
- As técnicas e os kata não tinham grafia japonesa.
- A pronúncia lia romaji com voz japonesa.
- O vocabulário básico do dojo (contagem, comandos, etiqueta) estava ausente.

Este PR corrige a parte mecânica. As decisões de terminologia estão listadas no fim para validação com o sensei.

| Indicador | Antes | Depois |
|---|---|---|
| Palavras dos nomes com tradução | 29/47 (62%) | 47/47 (100%) |
| Técnicas com grafia japonesa | 0/54 | 54/54 |
| Kata com kanji e sentido do nome | 0/22 com kanji; sentido só em Garyu e Tsuki no Kata | 22/22 (2 marcados como leitura debatida) |
| Técnicas pronunciadas a partir de kanji | 0/54 | 54/54 |
| Vocabulário de dojo e contagem | contagem 0; Mokuso, Seiza e Dojo 0; Rei 1 | 33 termos com kanji e tradução |
| Kanji marcados com `lang="ja"` | não | sim (léxico, técnicas, kata, vocabulário) |
| Idioma da página | `pt` | `pt-BR` |

## Método

1. O app foi carregado num navegador headless para extrair as estruturas de dados e o texto renderizado de cada aba.
2. Cada nome de técnica foi decomposto em palavras, e cada palavra foi conferida contra o léxico.
3. As variantes de grafia e os termos de comando e etiqueta foram contados no código-fonte.
4. Para a riqueza lexical do português, foram calculados o índice de Guiraud (tipos/√tokens), a proporção de hápax (palavras que aparecem uma vez só) e o MTLD (medida de diversidade que não depende do tamanho do texto; limiar 0,72, média das duas direções).
5. Os achados sobre o japonês foram conferidos com a leitura dos kanji. Os pontos em que as escolas divergem estão marcados como **verificar**.

## 1. Riqueza lexical do português

| Campo | Tokens | Tipos | Guiraud | Hápax | MTLD |
|---|---:|---:|---:|---:|---:|
| Léxico (`NODES.note`) | 1.065 | 447 | 13,7 | 63% | 73 |
| Kata: nota + silêncios | 821 | 368 | 12,8 | 65% | 81 |
| Técnicas: descrição | 841 | 324 | 11,2 | 54% | 99 |
| Renraku: nota | 311 | 180 | 10,2 | 71% | 109 |
| Técnicas: execução | 730 | 261 | 9,7 | 52% | 70 |
| Kata: roteiros | 2.237 | 401 | 8,5 | 47% | 48 |

**Leitura.** Os textos explicativos têm diversidade alta (MTLD entre 70 e 110), o que é bom para prosa técnica. Os roteiros de kata são mais repetitivos (MTLD 48), mas isso é esperado: são comandos em sequência.

**Pontos de repetição:**
- "de dentro para fora" aparece 9 vezes nas técnicas.
- "em arco" e "em linha reta" funcionam como fórmulas.
- Algumas aberturas de frase se repetem, como "Ambos os…" (5×), "Mão aberta, …" (4×) e "Pulso curva…" (4×).

Não é um defeito, mas variar o vocabulário de trajetória ajudaria a diferenciar técnicas vizinhas.

**Estratégia de tradução inconsistente.** Das 54 glosas (as traduções curtas sob o nome da técnica), 16 nomeiam só a arma e omitem a ação. Exemplos:
- "Tettsui Uchi → punho martelo"
- "Koken Uchi → pulso curvado"
- "Nukite → lança de dedos"
- "Shuto Hizo Uchi → mão faca na costela"

As outras 38 descrevem o golpe ("soco alto", "chute lateral"). *Recomendação:* padronizar em "ação + arma + alvo", por exemplo "golpe com punho martelo".

**Correções feitas neste PR:**

| Antes | Depois | Motivo |
|---|---|---|
| "Introduce Uraken em combinação" | "Introduz o Uraken…" | palavra em inglês |
| "Alvo: solar plexus" | "Alvo: plexo solar" | inglês; o resto do app usa "plexo solar" (5×) |
| "requer contacto" | "requer contato" | forma de Portugal num texto em português do Brasil |
| "nas viragens laterais" | "nos giros laterais" | idem |
| "A lateral da mão mimética da lâmina" | "A lateral da mão imita a lâmina" | uso impróprio de "mimética" |
| "de dentro p/ fora", "muda p/ zenkutsu" | "para" | abreviação em texto corrido |
| "bloqueio duplo alto+baixo" (Uchi Uke Gedan Barai) | "bloqueio interno + varredura baixa" | o nome não diz "alto"; tradução literal |
| "As 23 formas" | contagem automática (22) | número errado |

**Jargão em inglês mantido.** Uppercut (6×), snap (8×), clinch (5×), timing (2×) e footwork são vocabulário corrente nas academias. *Recomendação:* traduzir na primeira ocorrência de cada seção, por exemplo "clinch (corpo a corpo)".

## 2. Japonês

### 2.1 Lacunas lexicais — corrigido

Estas 18 palavras aparecem em nomes de técnicas, mas não tinham tradução em lugar nenhum. Entraram no novo `GLOSS_EXTRA` com kanji e sentido:

ago 顎 · hiji 肘 · juji 十字 · kagi 鉤 · kake 掛け · kakiwake 掻き分け · kin 金 · morote 諸手 · nidan 二段 · osae 押さえ · otoshi 落とし · sakotsu 鎖骨 · sayu 左右 · shita 下 · tate 縦 · teisho 底掌 · uchikomi 打ち込み · ura 裏

O Dicionário e o painel de detalhe agora mostram cada técnica **palavra por palavra** (romaji, kanji e tradução).

### 2.2 Homonímia de "uchi" — parcialmente corrigido

O léxico só conhece **打ち** (golpear). Mas em Chudan Uchi Uke, Uchi Mawashi Geri, Uchi Uke Gedan Barai e no primeiro "Uchi" de Shuto Uchi Uchi, a palavra é **内** (de dentro), o antônimo de Soto 外, que existe no léxico.

A decomposição agora distingue os dois sentidos: 内 antes de outra palavra, 打ち no fim do nome. **Não corrigido:** a fórmula dessas técnicas continua errada, porque falta o nó de direção "uchi (内)":

- **Shuto Uchi Uchi** registra a direção **Soto**, que é o oposto do nome. A própria descrição do app diz "Uchi aqui é direção (interno→externo)".
- **Chudan Uchi Uke** e **Uchi Uke Gedan Barai** registram **Mae**.

*Recomendação:* criar o nó `uchi_nai` (内, direção) e reanotar as 4 técnicas. Isso altera a constelação e as contagens da aba Matemática, por isso ficou fora deste PR.

### 2.3 Sinonímia não explicada — verificar

| Par | Ocorrências | Situação |
|---|---|---|
| **Empi 猿臂 / Hiji 肘** (cotovelo) | Empi 32 · Hiji 13 | O léxico, as técnicas de kata e os renraku usam Empi. Os roteiros de kata usam **Hiji Ate**, que é a forma habitual no Kyokushin (Empi é mais associado ao Shotokan). A glosa de Hiji agora explica o sinônimo. *Recomendação:* adotar Hiji Ate como forma principal e Empi como variante. |
| **Shotei 掌底 / Teisho 底掌** (base da palma) | 11 · 10 | Mesmos kanji em ordem inversa. A glosa agora explica. |
| **Jodan Uke / Age Uke** | 27 · 22 | O kihon diz Jodan Uke e os kata dizem Age Uke, sem aviso de que são a mesma defesa. |
| **Uraken Gammen / Uraken Shomen Uchi** | 7 · 4 | Shomen 正面 (frente) aparece nos renraku sem glosa. |
| **Gammen / Ganmen** 顔面 | 7 · 3 | Romanizações diferentes (Hepburn tradicional × moderno). **Unificado em "Gammen"**, a forma do resto do app. Efeito extra: o passo "Uraken Gammen Uchi" do Gekisai Dai agora vira link para a técnica, o que antes não acontecia. |

### 2.4 Romanização

O app não usa mácron (chūdan, jōdan, kōken, shōtei, kokutsu → kōkutsu). Isso é coerente internamente, mas apaga as vogais longas, que mudam o sentido em japonês. *Recomendação:* manter o nome sem mácron e mostrar a forma com mácron no detalhe.

**Kanji variante:** o nó Mawashi usa 廻し; a grafia corrente é 回し. As duas estão corretas.

### 2.5 Pronúncia — corrigido

O botão 🔊 mandava o romaji ("Seiken Chudan Tsuki") para a voz japonesa do navegador. Vozes japonesas leem mal o alfabeto latino. Agora a voz recebe os kanji ("正拳中段突き"), tanto nas técnicas quanto nos nomes de kata (54/54 e 22/22).

**Limite:** a leitura de kanji pela síntese de voz depende do sistema. Compostos raros podem sair com leitura errada. *Recomendação:* acrescentar um campo `kana` (em hiragana) para leitura garantida.

### 2.6 Vocabulário de dojo — corrigido

Antes, a contagem de 1 a 10 aparecia 0 vezes; Mokuso, Seiza e Dojo, 0; Rei e Kamae, 1 cada. Esse é o conteúdo típico da parte teórica de qualquer exame de faixa.

Entrou o `DOJO_VOCAB` (comandos, etiqueta, formas de treino e contagem), com kanji, numa seção recolhível da aba Exame. As perguntas de teoria do simulado agora sorteiam entre 93 termos: o léxico, o glossário complementar e o vocabulário de dojo.

### 2.7 Termos usados sem definição — recomendação

- **Posturas:** Heisoku (11×), Musubi (12×), Shiko (4×) e Tsuru Ashi (1×) aparecem nos roteiros, mas não estão entre os 5 dachi do léxico. Fudo Dachi, a guarda de kumite do Kyokushin, não aparece.
- **Deslocamentos:** fumi-dashi, kosa-ashi, oi-ashi, tenshin, tai sabaki e kaiten são cobrados nos requisitos da verde e da marrom, mas não são nós. O eixo Ido tem só ayumi, okuri e tobi.
- **Outros:** Ibuki (6×), Hikite e Dojo Kun.

### 2.8 Pontos a verificar com o sensei

- **Saifa × Saiha (最破):** a literatura da IKO costuma grafar "Saiha".
- **Uchi × Soto Mawashi Geri:** o app define Uchi = de dentro para fora. A convenção inversa existe em outras escolas. A definição do app é coerente com o próprio Uchi Uke / Soto Uke, mas convém confirmar a do dojo.
- **Seiken Kagi Tsuki:** está anotado como **jodan**. No kihon Kyokushin, o kagi tsuki costuma ser executado em **chudan**.
- **Yantsu (安三) e Seienchin (征遠鎮):** o sentido do nome é debatido. Os cards trazem a marca "leitura debatida".

## 3. O que este PR altera no app

- `GLOSS_EXTRA`, `UCHI_IN`, `DOJO_VOCAB`, `KATA_NAME` e os auxiliares `techWords`, `techWordsHtml`, `techSpeech` e `jaSpan`.
- Painel "palavra por palavra" no Dicionário e no detalhe da técnica.
- Kanji e sentido nos cards de kata.
- Seção "Vocabulário do dojo" na aba Exame.
- Teoria do simulado com vocabulário ampliado.
- `lang="pt-BR"` na página e `lang="ja"` nos kanji.
- Contagens de técnicas e kata calculadas a partir dos dados.
- As correções de português da tabela da seção 1.

## 4. Próximos passos, por impacto

1. Nó `uchi (内)` e reanotação de Shuto Uchi Uchi, Chudan Uchi Uke, Uchi Uke Gedan Barai e Uchi Mawashi Geri (§2.2).
2. Decidir a forma principal de Empi/Hiji, Jodan/Age Uke e Shotei/Teisho, e registrar a variante (§2.3).
3. Padronizar as 16 glosas que nomeiam só a arma (§1).
4. Campo `kana` para pronúncia garantida e mácron no detalhe (§2.4, §2.5).
5. Posturas e deslocamentos faltantes no léxico (§2.7).
6. Validar os pontos da §2.8 com o sensei.
