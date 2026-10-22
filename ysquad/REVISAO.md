# Revisão do EffectCraft pro uso da ySquad (17/10/2026)

Versão 0.10.0 (upstream `storytold/effectcraft`, fase alfa, primeiro commit em 01/10/2026). Medido
no nosso uso real: cena de motion por script → `.mov` com transparência → montada no FilmCraft.

## Pronto e funcionando bem

| O quê | Prova |
|---|---|
| Compila no Mac | release em 9 min, saída no SSD |
| Script no formato do After (ExtendScript) | cena de 2 camadas montada em 0,1 s, com os nomes internos do After (`ADBE Glo2`, `ADBE Vector Shape - Rect`) |
| Quadros-chave, escala, posição, opacidade | mola de 0 a 115 a 100 %, barra deslizando, saída suave: conferido quadro a quadro |
| ProRes 4444 com transparência | `yuva444p12le`, 1080×1920, 90 quadros, 7 s pra 3 s de cena |
| Ponte com o FilmCraft | `montar_video.py --motion` montou a cena sobre o corte `corte_motion` em 71 s, com legenda por cima |
| Fonte da marca | Druk aplicada pelo nome interno (PostScript) |
| Acento em PT-BR | Ê Ç Ã corretos; quando a fonte não tem a letra, só ela troca de fonte |
| Servidor de ferramentas (MCP) | `effectcraft-headless` e `effectcraft` conectados |
| Testes | 481 passaram, 0 falharam (script, export, text, render, lottie) |
| Exportar Lottie (animação leve pra site) | sai, mas tira o brilho e avisa |

## Precisa ajustar

1. **Fonte errada passa calada.** Nome que não existe (ex.: `Druk-Super-Trial`, nome do arquivo) é
   aceito, o script devolve o nome como se valesse e o quadro sai na fonte padrão. O After marca
   como faltando. Fazer: aviso no script/CLI e um atalho que aceite nome do arquivo ou família.
2. **Quem posiciona a cena.** A receita do FilmCraft sobe 220 px todo clipe de `--motion` (pra não
   pisar na legenda). Cena desenhada já no lugar certo no EffectCraft sobe de novo. Fazer: combinar
   uma regra só (cena em quadro cheio, no lugar final, e a receita só encaixa).
3. **Placa de vídeo na primeira vez.** O 1º render com `--gpu` leva ~35 s preparando; os seguintes
   7 s. Em cena simples a placa não ganhou do processador. Usar o processador por padrão.
4. **Druk de teste sem acento.** Os arquivos `*-Trial` não têm Ê Ç Ã; a Druk Medium completa tem.
   Pra peça com acento: Druk completa ou outra fonte da marca.

## Precisa revisar (ainda não medido)

- Moldes reaproveitáveis (`.ectemplate` + `--values`): o caminho natural pro Nolan gerar a mesma
  cena com textos diferentes. Não testado.
- Efeitos mais pesados que usamos no motion (desfoque de movimento, 3D, partículas, máscaras de
  texto animadas): só o brilho foi testado.
- Velocidade em cena carregada (muitas camadas, 4K).
- Estabilidade do app com janela (`--control 9877`): só a linha de comando foi usada.
- Fidelidade com o After: o projeto só mediu um caso (suavização de curva); os efeitos não foram
  comparados quadro a quadro. Projeto `.aep` não abre (não importa pra nós: criamos do zero).

## Segunda rodada (17/10/2026): o que depende da filmagem

Teste em 3 s do vídeo do Ruan (1080×1920, 25 fps, `sony4k_60s`).

| Caso | Resultado |
|---|---|
| Rastreio (texto preso ao relógio) | ✅ 75 quadros em <1 s; o alvo precisa ficar no quadro |
| Efeito na filmagem (Gaussian Blur, Turbulent Displace) | ✅ ~0,2 s por quadro |
| Texto atrás da pessoa (Roto Brush + MobileSAM) | ✅ pessoa inteira, rosto opaco, ~60 s pros 75 quadros; borda do cabelo dura |
| Object Matte + MobileSAM | ⚠️ cabeça perfeita, perde parte da camiseta |
| Vídeo do celular girado | ✅ importa em pé (2160×3840) |

Armadilhas achadas (não são defeito do programa, mas não estão na documentação):
1. Na linha de comando a escolha do modelo vale só dentro do mesmo `run` (a CLI não guarda
   preferências: `apps/effectcraft-cli/src/main.rs:351-366`). Sem `roto.model.select` no mesmo
   `run`, cai no método clássico calado, e um recorte congelado com MobileSAM é refeito no clássico.
2. Vários pontos num `roto.stroke` viram uma linha contínua (`crates/track/src/roto/mod.rs:311`):
   um traço de fundo cruzou o rosto e abriu o buraco. Use um traço por ponto.
3. Exportar pelo `renderQueue.render` ou `comp.saveFrameAs` dentro do mesmo `run`, não pelo
   `render-frame` solto.
Receita que funcionou: skill `effectcraft-cena`, seção "Recorte da pessoa".

## Versão oficial 0.15.0 (22/10/2026)

O que mudou, pelo nosso uso:
- **Útil:** molde por linha de comando aceita trocar uma mídia por caminho de arquivo (`--values`,
  #1780), o que deixa o mesmo molde rodar sobre outro trecho de filmagem; `render --progress` mostra o
  andamento quando quem chama é um programa (o Nolan); quadro de MP4 com tempo arredondado lido no
  ritmo certo, sem quadro repetido (#1442, filmagem de celular); `setValue` recusa valor com número
  errado de partes (antes passava calado); presets de suavização de entrada e saída nos quadros-chave
  (#1329); `anim.applyPreset` acha efeito pelo nome interno do After (#1746); máscara que cresce além
  da curva vira forma cheia; caminhos de forma viram curvas editáveis (#932); correções que evitam
  travar (cor, máscara, amostragem de imagem, importação de SVG, glTF e FBX).
- **Neutro:** expressões (`propertyGroup()`), alças de 3D na tela, seletor de texto arrastável,
  menus em japonês, fontes variáveis, camada guia escondida em comp aninhada.
- **Não precisamos:** robôs de verificação do repositório (SonarCloud, CodeRabbit, Dependabot),
  Windows, Linux, versão web, exportação EXR.

Testes refeitos no vídeo do Ruan (`sony4k_60s`, 3 s, 1080×1920, 25 fps): cena de motion (mola,
barra, brilho, saída) ✅ exportação 1,2 s (antes 7 s); rastreio ✅ <1 s; Gaussian Blur ✅; texto
atrás da pessoa com Roto Brush + MobileSAM ✅ 58 s; molde com texto e cor trocados ✅. Testes das partes que usamos (script, export, text, render, lottie, track, cli): 613 passaram, 0 falharam.

Continua igual: fonte com nome errado (`Druk-Super-Trial`) ainda é aceita calada (item 1 de
"Precisa ajustar"). As três armadilhas do recorte da pessoa continuam valendo.
