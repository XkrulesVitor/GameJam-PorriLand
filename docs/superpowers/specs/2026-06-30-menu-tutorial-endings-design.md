# Design — Menu, Tutorial e Saída de Finais

**Data:** 2026-06-30  
**Projeto:** PorriLand (Construct 3, game jam Overpower)

---

## Abordagem Arquitetural

Tudo implementado como **camadas extras no layout `Game` existente**, sem criar novos layouts ou event sheets. O layout já contém todos os tipos de objeto necessários.

---

## 1. Camada Menu

**Layer:** `"Menu"` — visível ao iniciar, z-order acima de todas as outras.

**Conteúdo:**
- `Filtro_Menu` — instância de `Filtro_Dilema`, cobrindo 1280×720, opacidade 220, bloqueio de cliques no jogo
- `sf_Menu_Titulo` — instância de sprite font, texto `"PORRILAND"`, posição central superior
- `btn_Menu_Jogar` — novo sprite 64×200, texto "JOGAR", posição central
- `btn_Menu_Tutorial` — novo sprite 64×200, texto "TUTORIAL", abaixo de Jogar
- `btn_Menu_Sair` — novo sprite 64×200, texto "SAIR", abaixo de Tutorial

**Comportamento:**
| Ação | Efeito |
|---|---|
| Layout inicia | `time scale = 0`, layer Menu visível |
| Clique em Jogar | `time scale = 1`, oculta layer Menu |
| Clique em Tutorial | oculta layer Menu, exibe layer Tutorial |
| Clique em Sair | `close window` |

---

## 2. Camada Tutorial

**Layer:** `"Tutorial"` — invisível por padrão, z-order acima de Menu.

**Conteúdo:**
- `Filtro_Tutorial` — instância de `Filtro_Dilema`, cobrindo tela inteira
- `Painel_Tutorial` — instância de `Painel_Dilema`, painel central opaco
- `sf_Tutorial_Titulo` — instância de `sf_Dilema_Titulo`, mostra título da página atual
- `sf_Tutorial_Corpo` — instância de `sf_Dilema_Corpo`, mostra conteúdo da página atual
- `btn_Tutorial_Proximo` — botão "PRÓXIMO" (visível só na página 1)
- `btn_Tutorial_Anterior` — botão "ANTERIOR" (visível só na página 2)
- `btn_Tutorial_Voltar` — botão "VOLTAR", sempre visível

**Variável local:** `Tutorial_Pagina` (número, valor inicial 1)

**Páginas:**

*Página 1 — O Mundo*
```
Título: O MUNDO
Corpo:
ADEPTOS — fiéis comuns. Geram Fé e se reproduzem quando felizes.
CÉTICOS — duvidam. Reduzem Fé e a Felicidade dos Adeptos próximos.
FANÁTICOS — surgem com Fé alta. Perseguem e matam.

FÉ:
0–49  Ceticismo   (Céticos se espalham)
50–70 Equilíbrio  (único estado que evolui a civilização)
71–100 Fanatismo  (Fanáticos surgem)
```

*Página 2 — Seus Poderes*
```
Título: SEUS PODERES
Corpo:
TOQUE  (2s)   — mova qualquer Porri.
RAIO   (5s)   — elimina Céticos e Fanáticos.
SUSSURRO (10s) — converte e fortalece Adeptos.

DILEMAS pausam o jogo a cada ~2 minutos.
Toda escolha tem um custo. Não existe resposta perfeita.
```

**Comportamento:**
| Ação | Efeito |
|---|---|
| Clique em Próximo | `Tutorial_Pagina = 2`, atualiza textos, oculta Próximo, exibe Anterior |
| Clique em Anterior | `Tutorial_Pagina = 1`, atualiza textos, exibe Próximo, oculta Anterior |
| Clique em Voltar | oculta layer Tutorial, exibe layer Menu |

---

## 3. Saída após Finais

Após o modal de final exibir o texto do desfecho, aparece um botão **`btn_Reiniciar`** no lugar dos botões Sim/Não.

**Quando aparece:**
- `Dilema_Tipo` = `"Final"` E nenhum sub-final ainda resolvido (O Deus Vivo, A Partida, O Senhor, O Pastor) — aparece imediatamente junto com o texto, pois esses finais não têm Sim/Não
- `Dilema_Tipo` = `"FinalEncarnacao"` — aparece após o texto da encarnação (Sim foi clicado no modal secreto)
- `Dilema_Tipo` = `"FinalPastor"` — aparece após o texto do Pastor secreto (Não foi clicado no modal secreto)

**Comportamento ao clicar:**
- `restart-layout` → layout recarrega com layer Menu visível e `time scale = 0`

**Posicionamento:** centro inferior do modal de dilema, no espaço onde Sim/Não normalmente ficam.

---

## Objetos Novos

| Objeto | Tipo base | Camada | Descrição |
|---|---|---|---|
| `btn_Menu_Jogar` | Sprite | Menu | Botão Jogar |
| `btn_Menu_Tutorial` | Sprite | Menu | Botão Tutorial |
| `btn_Menu_Sair` | Sprite | Menu | Botão Sair |
| `btn_Tutorial_Proximo` | Sprite | Tutorial | Botão próxima página |
| `btn_Tutorial_Anterior` | Sprite | Tutorial | Botão página anterior |
| `btn_Tutorial_Voltar` | Sprite | Tutorial | Botão voltar ao menu |
| `btn_Reiniciar` | Sprite | UI | Botão jogar novamente (finais) |
| `sf_Menu_Titulo` | Spritefont2 | Menu | Título PORRILAND |
| `sf_Tutorial_Titulo` | Spritefont2 | Tutorial | Título da página |
| `sf_Tutorial_Corpo` | Spritefont2 | Tutorial | Corpo da página |

Os filtros e painéis reusam instâncias dos tipos `Filtro_Dilema` e `Painel_Dilema` já existentes, nas novas camadas.

---

## Variáveis de Controle

| Variável | Tipo | Valor inicial | Uso |
|---|---|---|---|
| `Tutorial_Pagina` | número (global) | 1 | Página atual do tutorial |
