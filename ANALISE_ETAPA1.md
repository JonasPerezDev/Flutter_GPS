# Análise da Etapa 1 – Interface e funcionalidades fictícias

## 1. Mensagens de informação no Debug Console

| Ação no app | Mensagem no console | Método que dispara | Gatilho na interface |
|---|---|---|---|
| Botão **Add Waypoint** | `Voce clicou no botão Add Waypoint` | `adicionarWaypoint()` | `FilledButton.icon(onPressed: adicionarWaypoint)` |
| **LongPress** em um waypoint | `Voce acionou o Navegar para WP00X` | `navegarPara(waypoint)` | `ListTile(onLongPress: ...)` |
| Botão **STOP** | `Voce parou o Navegar para WP00X` | `pararNavegacao()` | `FloatingActionButton.extended(onPressed: pararNavegacao)` |
| **Swipe** (direita para esquerda) | `Voce acionou o excluir Waypoint WP00X` | `excluirWaypoint(waypoint)` | `Dismissible(onDismissed: ...)` |

Observação: o `STOP` só imprime quando existe um waypoint em navegação (`if (navegandoPara != null)`).

## 2. Métodos que retornam Widget

| Método | O que faz |
|---|---|
| `KGpsLocatorApp.build` | Raiz do app. Devolve o `MaterialApp` com título, tema Material 3 (`ColorScheme.fromSeed`) e a `HomePage` como tela inicial. |
| `HomePage.createState` / `_HomePageState.build` | `_HomePageState.build` monta o `Scaffold` completo: `AppBar`, corpo, botão flutuante e barra inferior. |
| `Compass.build` | Devolve um `SizedBox` de 280x280 com um `CustomPaint` que usa o `CompassPainter` para desenhar a bússola girada conforme o `heading`. |
| `_buildLocationInfo()` | Card com Latitude, Longitude e Altitude. Usa `Padding` externo (`EdgeInsets.symmetric(horizontal: 16)`), `Card`, `Padding` interno (`EdgeInsets.all(12)`) e uma `Column` de `_infoRow`. |
| `_buildNavigationInfo()` | Card exibido só durante a navegação. Mostra "NAVEGANDO PARA", o nome do waypoint e o texto fixo de azimute/distância. |
| `_buildWaypointList()` | `ListView.builder` que cria a lista sob demanda. Cada item é um `Dismissible` (swipe para excluir) contendo um `ListTile` (long press para navegar). |
| `_infoRow(label, value)` | Linha reutilizável: `Row` com `spaceBetween`, rótulo em negrito à esquerda e valor à direita. |

## 3. Componentes usados e para que servem

- **`Scaffold`**: estrutura básica de uma tela Material (appBar, body, floatingActionButton, bottomNavigationBar).
- **`AppBar`**: barra de título no topo.
- **`SafeArea`**: evita que o conteúdo fique embaixo de notch, barra de status ou barra de gestos.
- **`Column` / `Row`**: organizam filhos na vertical / horizontal. `mainAxisAlignment: spaceBetween` empurra o primeiro filho para o início e o último para o fim.
- **`SizedBox(height: n)`**: espaço vazio de altura fixa entre widgets. Com `width`/`height` também define o tamanho de um filho (caso da bússola).
- **`Expanded`**: faz o filho ocupar todo o espaço restante da `Column`. É necessário para colocar um `ListView` dentro dela sem erro de altura infinita.
- **`if (navegandoPara != null) widget`**: elemento condicional dentro da lista de filhos. Só mostra o card de navegação quando há destino.
- **`Padding` + `EdgeInsets`**:
  - `EdgeInsets.all(12)`: mesmo espaçamento nos quatro lados.
  - `EdgeInsets.symmetric(horizontal: 16)`: mesmo valor na esquerda e na direita.
  - `EdgeInsets.symmetric(vertical: 3)`: mesmo valor em cima e embaixo.
  - `EdgeInsets.only(right: 20)`: espaçamento apenas à direita.
- **`Card`**: superfície elevada com cantos arredondados; agrupa informações.
- **`Text` / `TextStyle`**: texto e seu estilo (tamanho, negrito). `toStringAsFixed(n)` formata o número com `n` casas decimais.
- **`FilledButton.icon`**: botão preenchido com ícone e rótulo (Add Waypoint).
- **`FloatingActionButton.extended`**: botão flutuante com ícone e texto (STOP). É `null` quando não há navegação, então some da tela.
- **`bottomNavigationBar`**: área fixa na parte inferior, aqui usada para o botão Add Waypoint.
- **`ListView.builder`**: lista rolável que constrói apenas os itens visíveis (`itemCount` + `itemBuilder`).
- **`ListTile`**: linha padrão de lista com `leading`, `title`, `subtitle` e gestos como `onLongPress`.
- **`Dismissible`**: permite arrastar o item para removê-lo. `direction: endToStart` aceita só o arraste da direita para a esquerda; `background` é o que aparece atrás do item; `key` identifica cada item de forma única; `onDismissed` é chamado ao concluir o gesto.
- **`Container` com `alignment: Alignment.centerRight`**: fundo do swipe com o ícone de lixeira alinhado à direita.
- **`Icon` / `Icons.*`**: ícones do Material (localização, lixeira, stop, adicionar local).
- **`CustomPaint` + `CustomPainter`**: permitem desenhar livremente no `Canvas`. O `CompassPainter` desenha o círculo, os ticks a cada 5 graus e as letras N/E/S/W; `canvas.save/translate/rotate/restore` giram a rosa dos ventos de acordo com o `heading`; `shouldRepaint` só redesenha quando o `heading` muda.
- **`setState`**: avisa o Flutter que o estado mudou para reconstruir a tela (usado em `navegarPara` e `pararNavegacao`).

## 4. Observação sobre a exclusão

O `Dismissible` exige que o item seja removido da lista assim que o swipe termina; caso contrário o Flutter lança a exceção "A dismissed Dismissible widget is still part of the tree". Por isso `excluirWaypoint` também remove o item de `waypoints` dentro de um `setState`, além de imprimir a mensagem pedida.
