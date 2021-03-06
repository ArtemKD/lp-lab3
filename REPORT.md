#№ Отчет по лабораторной работе №3
## по курсу "Логическое программирование"

## Решение задач методом поиска в пространстве состояний

### студент: Козлов А. Д.

## Результат проверки

| Преподаватель | Дата | Оценка |
|-------------------|--------------|---------------|
| Сошников Д.В. | | |
| Левинская М.А.| | |

> *Комментарии проверяющих (обратите внимание, что более подробные комментарии возможны непосредственно в репозитории по тексту программы)*

## Введение

Графы в Prolog организуются путем задания так называемого предиката перехода от точки А, к точки B, таким простым и удобным способом можно задавать графы разной сложности. Также как и в других языках, в Prolog можно организовать различные алгоритмы обхода этих графов. Благодаря этому Prolog крайне удобен для решения задач с пространством состояний, где требуется найти переход от одного состояния к другом. На первый взгляд покажется, что подобные задачи не имеют ничего общего с графами и алгоритмами их обходах. Но, задав определённые правила, условия перехода из одного состояния к другому, получаем обычный граф, по которому нужно пройтись и найти путь к искомому состоянию.
Часто в программировании графы задаются матрицей смежностей, что является не самым удобным представлением графа, так как зачастую вершины графов несут за собой другую информацию, помимо связей с другими вершинами, в связи с этим в матричном представлении графов очень часто требуется самостоятельно связывать вершины, с дополнительной информацией. В Prolog благодаря структурным термам и связям в графах, которые задаются специальными предикатами, данный недостаток отсутствует.

## Задание

Вдоль доски расположено 7 лунок, в которых лежат 3 черных и 3 белых шара. Передвинуть черные шары на место белых, а белые – на место черных. Шар можно передвинуть в соседнюю с ним пустую лунку, либо лунку находящуюся непосредственно за ближайшим шаром

## Принцип решения

## Результаты

Для решения данной задачи используем структурный терм, в котором будут хранится два списка. Оба списка представляют из себя набор шаров, расположенных в определённом порядке слева от пустой лунки и справа от пустой лунки. Для решения данной задачи, будем использовать структурный терм, в котором будут хранится два списка. Оба списка представляют из себя набор шаров, расположенных в определённом порядке слева от пустой лунки и справа от пустой лунки. Пример : `step([black, black, black], [white, white, white])` - первоначальное положение шаров.

Далее, так как требуется переместить все белые шары на места черных, а черные – на места белых, то есть найти переход от одного состояния к другому, нужно задать специальные правила перехода, применив которые к исходному состоянию, можем получить набор новых состояний.

Собственно, в задании разрешено перемещать шары в соседнюю пустую лунку, и в пустую лунку, если лунку находится за соседним шаром (эти правила задают следующие предикаты)

```prolog
...
n_step(step(Left, Right), step(ResLeft, ResRight)):-
length(Left, LeftLength),
rem_el_by_index(Left, ResLeft, LeftLength, Elem),
ResRight = [Elem|Right].
n_step(step(Left, Right), step(ResLeft, ResRight)):-
Right = [Head|ResRight],
append(Left, [Head], ResLeft).
n_step(step(Left, Right), step(ResLeft, ResRight)):-
length(Left, LeftLength),
PreIndex is LeftLength - 1,
rem_el_by_index(Left, ResLeft, PreIndex, Deleted),
ResRight = Right.
n_step(step(Left, Right), step(ResLeft, ResRight)):-
rem_el_by_index(Right, ResRight, 2, Deleted),
append(Left, [Deleted], ResLeft).
...
```

Пример использования:
```prolog
?- n_step(step([b,b],[w,w]), step(Left, Right)).
Left = [b],
Right = [b, w, w] ;
Left = [b, b, w],
Right = [w] ;
Left = [b],
Right = [b, w, w] ;
Left = [b, b, w],
Right = [w] ;
```

Для решения данной задачи требуется реализовать несколько алгоритмов обхода графов. Для данной задачи применим только алгоритм поиск в глубину, так как при решении генерируется очень много различных переходов.

```prolog
...
dfs([X|T], X, [X|T]).
dfs(P, F,L):-
tprolong(P,P1),
dfs(P1, F,L).
...
tprolong([X|T], [Y,
X|T]):-
n_step(X,Y),
not(member(Y, [X|T])).
```

Результат работы программы:
```prolog
?- task3().
[black,black,black] [white,white,white]
[black,black] [black,white,white,white]
[black,black,white] [black,white,white]
[black,black] [white,black,white,white]
[black] [black,white,black,white,white]
[black,white] [black,black,white,white]
[black] [white,black,black,white,white]
[] [black,white,black,black,white,white]
[white] [black,black,black,white,white]
[white,black] [black,black,white,white]
[white,black,black] [black,white,white]
[white,black,black,black] [white,white]
[white,black,black,black,white] [white]
[white,black,black,white] [black,white]
[white,black,black] [white,black,white]
[white,black] [black,white,black,white]
[white] [black,black,white,black,white]
[] [white,black,black,white,black,white]
[black] [white,black,white,black,white]
[black,white] [black,white,black,white]
[black,white,black] [white,black,white]
[black,white,black,white] [black,white]
[black,white,black,white,black] [white]
[black,white,black,white,black,white] []
[black,white,black,white,white] [black]
[black,white,black,white] [white,black]
[black,white,black] [white,white,black]
[black,white] [black,white,white,black]
[black] [white,black,white,white,black]
[] [black,white,black,white,white,black]
[white] [black,black,white,white,black]
[white,black] [black,white,white,black]
[white,black,black] [white,white,black]
[white,black,black,white] [white,black]
[white,black,white] [black,white,black]
[white,black] [white,black,white,black]
[white] [black,white,black,white,black]
[] [white,black,white,black,white,black]
[black] [white,white,black,white,black]
[black,white] [white,black,white,black]
[black,white,white] [black,white,black]
[black,white,white,black] [white,black]
[black,white,white,black,white] [black]
[black,white,white,black,white,black] []
[black,white,white,black,black] [white]
[black,white,white,black] [black,white]
[black,white,white] [black,black,white]
[black,white] [white,black,black,white]
[black] [white,white,black,black,white]
[] [black,white,white,black,black,white]
[white] [black,white,black,black,white]
[white,black] [white,black,black,white]
[white,black,white] [black,black,white]
[white,black,white,black] [black,white]
[white,black,white,black,black] [white]
[white,black,white,black,black,white] []
[white,black,white,black,white] [black]
[white,black,white,white] [black,black]
[white,black,white] [white,black,black]
[white,black] [white,white,black,black]
[white] [black,white,white,black,black]
[white,white] [black,white,black,black]
[white,white,black] [white,black,black]
[white,white,black,white] [black,black]
[white,white,white] [black,black,black]
Time to solve: 0.0019960403442382812
The count of steps: 65
true .
```

## Выводы

При выполнении данной лабораторной работы я изучил способы построения графов и деревьев на языке Prolog, различные способы обхода графов, смог увидеть и понять основные плюсы и недостатки алгоритмов поиска в глубину (например, удобство в условиях ограниченной памяти), в ширину для нахождения кратчайшего пути и итерационным заглублением, получил практику работы со списками.
Стало очевидным преимущество языка Prolog в задании графов различной сложности их обхода за счет простой системы правил перехода от одного состояния к другим. Так же хочу выделить особенное удобство в использовании структурных терм, которые позволяют связать различные данные определенной семантикой, позволяют сделать код более удобным и читаемым.
