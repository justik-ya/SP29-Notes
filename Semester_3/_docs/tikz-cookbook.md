# Рецепты иллюстраций

Все заготовки скомпилированы на шаблоне и работают как есть. Стили (`vx`, `ed`,
`arr`, `cell`, `blk`, `lbl`, `lblS`, `brace`, `notestyle`) определены в
`common/tikz-styles.tex` — используй их вместо локальных цветов и толщин, иначе
рисунки в одном конспекте разъедутся по стилю.

## Содержание

1. [Принципы](#принципы)
2. [Граф](#граф)
3. [Автомат и ориентированный граф](#автомат-и-ориентированный-граф)
4. [Дерево](#дерево)
5. [Массив, лента, фигурная скобка](#массив-лента-фигурная-скобка)
6. [График функции](#график-функции)
7. [Столбчатая диаграмма](#столбчатая-диаграмма)
8. [Коммутативная диаграмма](#коммутативная-диаграмма)
9. [Числовая прямая](#числовая-прямая)
10. [Блок-схема](#блок-схема)
11. [Сетка ячеек по формуле](#сетка-ячеек-по-формуле)

## Принципы

**Рисунок должен нести одну мысль.** Если на доске нарисован клубок, разбей его
на два рисунка или выдели цветом только то, ради чего картинка нужна.

**Цветом выделяется смысл, а не украшение.** `figA` — основное/выделенное,
`figC` — плохой случай или контрпример, `figGray` и `edDash` — отброшенное,
неактивное, вспомогательное. Чёрный — нейтральный фон структуры.

**Подписи внутри рисунка короткие**, объяснение — в `\caption`.

**Не масштабируй `[scale=...]`**: толщина линий и кегль подписей поедут
относительно друг друга. Задавай `x=` и `y=` или расстояния между узлами.

## Граф

```latex
\begin{tikzpicture}[node distance=14mm]
  \node[vx]                                (a) {$a$};
  \node[vx, right=of a]                    (b) {$b$};
  \node[vx, above right=8mm and 14mm of b] (c) {$c$};
  \node[vx, below right=8mm and 14mm of b] (d) {$d$};
  \node[vx acc, right=of c]                (e) {$e$};
  \draw[ed]     (a) -- node[lblS, above] {$3$} (b);
  \draw[edA]    (b) -- (c);
  \draw[ed]     (b) -- (d);
  \draw[edDash] (c) -- (d);
  \draw[edA]    (c) -- (e);
\end{tikzpicture}
```

`vx` — обычная вершина, `vx fill` — залитая (посещённая), `vx acc` — акцентная.
`edA` — выделенное ребро, `edDash` — отброшенное. Вес ребра ставь узлом на самом
ребре, а не отдельной нодой рядом: при правке координат подпись уедет.

## Автомат и ориентированный граф

```latex
\begin{tikzpicture}[node distance=20mm]
  \node[vx]              (q0) {$q_0$};
  \node[vx, right=of q0] (q1) {$q_1$};
  \node[vx acc, right=of q1, double, double distance=1pt] (q2) {$q_2$};
  \draw[arr]  ($(q0)+(-10mm,0)$) -- (q0);            % входная стрелка
  \draw[arrA] (q0) -- node[lblS, above] {$a$} (q1);
  \draw[arr]  (q1) to[bend left=40] node[lblS, above] {$a$} (q0);
  \draw[arr]  (q2) to[out=60, in=120, looseness=6] node[lblS, above] {$b$} (q2);
\end{tikzpicture}
```

Петля — `to[out=..., in=..., looseness=...]`. Встречные рёбра разводи через
`bend left`/`bend right`, иначе они лягут друг на друга.

## Дерево

```latex
\begin{tikzpicture}[
  level distance=12mm,
  level 1/.style={sibling distance=32mm},
  level 2/.style={sibling distance=16mm}]
  \node[vx] {$8$}
    child {node[vx] {$3$}
      child {node[vx] {$1$}}
      child {node[vx fill] {$6$}}}
    child {node[vx] {$10$}
      child {node[vx] {$9$}}
      child {node[vx] {$14$}}};
\end{tikzpicture}
```

`sibling distance` задаётся отдельно для каждого уровня, иначе нижний ряд
слипается. Для рекурсивных картинок (поддерево, свёрнутое в треугольник) рисуй
треугольник: `\draw[ed] (n) -- ++(-8mm,-12mm) -- ++(16mm,0) -- cycle;`.

## Массив, лента, фигурная скобка

```latex
\begin{tikzpicture}[x=9mm]
  \foreach \i/\v in {0/4, 1/7, 2/7, 3/1, 4/9, 5/2}{
    \node[cell] (c\i) at (\i,0) {$\v$};
    \node[lblS, below=1pt of c\i] {\i};}
  \node[cell fill] at (2,0) {$7$};                 % подсветка поверх
  \draw[brace] (2.45,0.4) -- (0.45,0.4)
    node[midway, above=5pt, lblS] {обработано};
\end{tikzpicture}
```

Скобка `brace` рисуется **от правого конца к левому** — иначе смотрит не в ту
сторону. `cell empty` — пунктирная ячейка для незанятой памяти.

## График функции

```latex
\begin{tikzpicture}
  \begin{axis}[notestyle, xlabel={$x$}, ylabel={$f(x)$},
    domain=0.2:6, samples=120, xmin=0, xmax=6.3, ymin=-1.2, ymax=2.2]
    \addplot[figA, mark=none] {ln(x)};
    \addplot[figC, dashed, mark=none] {x/3 - 0.5};
    \addplot[only marks, figC, mark=*, mark size=2pt] coordinates {(1.5,0.0)};
    \legend{$\ln x$, касательная}
  \end{axis}
\end{tikzpicture}
```

`notestyle` уже задаёт оси, сетку, кегль подписей и цветовой цикл. Всегда
ставь `xmin/xmax/ymin/ymax` руками: автоматические пределы обрезают то, ради
чего график нарисован.

## Столбчатая диаграмма

```latex
\begin{tikzpicture}
  \begin{axis}[notestyle, height=4.4cm,
    xlabel={размер входа}, ylabel={время, мс},
    symbolic x coords={100,1000,10000}, xtick=data, ymin=0,
    enlarge x limits=0.3]
    \addplot[ybar, bar width=10pt, fill=figA!25, draw=figA]
      coordinates {(100,2) (1000,21) (10000,240)};
  \end{axis}
\end{tikzpicture}
```

`ybar` ставится в опции `\addplot`, не оси (иначе столбцами станут все графики,
включая линии-ориентиры). Без `enlarge x limits` крайние столбцы прилипают к
рамке и первый становится не виден.

## Коммутативная диаграмма

```latex
\begin{tikzcd}[column sep=large, row sep=large]
  A \arrow[r, "f"] \arrow[d, "g"'] & B \arrow[d, "h"] \\
  C \arrow[r, "k"']                & D
\end{tikzcd}
```

Штрих после подписи (`"g"'`) переносит её на другую сторону стрелки.
Работает благодаря `\catcode`\"=12` в преамбуле — polyglossia иначе делает
кавычку активной и ломает синтаксис.

## Числовая прямая

```latex
\begin{tikzpicture}[x=14mm]
  \draw[arr] (-0.4,0) -- (5.2,0);
  \foreach \x/\l in {1/{a}, 3/{b}}{
    \draw[ed] (\x,-1.6pt) -- (\x,1.6pt);
    \node[lblS, below=2pt] at (\x,0) {$\l$};}
  \draw[edA] (1,0) -- (3,0);
  \fill[white, draw=figA, line width=0.8pt] (1,0) circle (2pt);  % выколотая
  \fill[figA] (3,0) circle (2pt);                                % включённая
\end{tikzpicture}
```

## Блок-схема

```latex
\begin{tikzpicture}[node distance=7mm]
  \node[blk]             (f) {Fetch};
  \node[blk, right=of f] (d) {Decode};
  \node[blk, right=of d] (x) {Execute};
  \node[blk, right=of x] (w) {Write\\back};
  \foreach \a/\b in {f/d, d/x, x/w} \draw[arrA] (\a) -- (\b);
  \draw[arr] (w.south) -- ++(0,-7mm)
    -| node[lblS, pos=0.25, below] {hazard} (d.south);
\end{tikzpicture}
```

Обратные связи веди через `-|` и `|-`: прямые углы читаются лучше кривых.

## Сетка ячеек по формуле

Когда содержимое ячеек вычисляется (биты, таблица динамики, матрица), не
выписывай значения руками — считай в `pgfmath`:

```latex
\begin{tikzpicture}[x=0.95cm, y=0.8cm]
  \foreach \v in {0,...,8}{
    \node[lblS, anchor=east] at (-0.55, -\v) {$\v$};
    \foreach \b in {0,1,2,3}{
      \pgfmathtruncatemacro{\bit}{mod(floor(\v/(2^\b)),2)}
      \pgfmathtruncatemacro{\fl}{(\v>0) && (mod(\v-1,2^\b)==(2^\b-1)) ? 1 : 0}
      \ifnum\fl=1
        \node[cell, fill=figA!15, draw=figA, text=figA] at (3-\b, -\v) {\bit};
      \else
        \node[cell, text=inkSoft] at (3-\b, -\v) {\bit};
      \fi}}
\end{tikzpicture}
```

Так рисунок остаётся правильным при изменении размера таблицы, а опечатку в
значении внести невозможно. Шаг сетки должен быть чуть больше `minimum size`
ячейки, иначе границы сливаются в сплошную линию.
