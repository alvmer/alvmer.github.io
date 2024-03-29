## Физический метод

Существует в каком-то смысле более математически строгий способ вывода амортизационных сложностей.

Рассмотрим некоторую структуру данных $\mathcal D$. Пусть выполняется конечная цепочка из $m$ операций над ней $a_i$, причем, в процессе применения этих операций, будут изменяться внутренние состояния $S_i\in \texttt{States}$ структуры $\mathcal D$. В начале структура данных находится в стартовом состоянии $S_0$.

$$
\mathcal{D}\colon\, S_0 \xrightarrow[c_1]{a_1} S_1 \xrightarrow[c_2]{a_2} S_2 \rightarrow \ldots \xrightarrow[c_m]{a_m} S_{m}
$$

Пусть все операции $a_i$ имеют настоящую сложность $c_i$ (в худшем случае). Определим *функцию потенциала* на множестве состояний:

$$
    \varphi\colon\,\texttt{States}\to\mathbb{Z}_{\geqslant 0},
$$

причем потребуем обязательно, чтобы $\varphi(S_0) = 0$, то есть потенциал структуры данных $\mathcal{D}$ изначально равен нулю. Обратите внимание, область значений функции $\varphi$ это целые **неотрицательные числа.**

<div class="alert alert-definition">
  <img class="alert-icon" src="/assets/images/icons/study.png" alt="icon"><div class="alert-name">Определение</div> 
<em>Амортизационной сложностью (учетной стоимостью)</em> операции $a_i$ называют значение $$c'_i = c_i + \varphi(S_i) - \varphi(S_{i-1}).$$
   <a name="potential-complexity"></a>
</div>

Именно из-за наличия во всей этой терминологии <<потенциала>> структуры данных метод и называется *физическим*. Видно, что амортизационная сложность операции на самом деле равняется исходной сложности с  поправкой на разность потенциалов, а именно из потенциала состояния, в которое перешла структура данных после выполнения операции, необходимо вычесть потенциал состояния, которое было до выполнения. 

Вспомним, что цель амортизированных оценок --- суммарно накрыть сверху реальную суммарную сложность всех операций. Проверим, так ли это на самом деле, сложив все амортизированные стоимости $c'_i$ по определению физического метода.

$$
\begin{aligned}
\sum_{i=1}^m c'_i &= c_1 + \varphi(S_1) - \varphi(S_0) \\
                  &+c_2 + \varphi(S_2) - \varphi(S_1) \\
                  &+ \quad\cdots \\
                  &+c_n+ \varphi(S_n) - \varphi(S_{n-1}).
\end{aligned}
$$

Видим, что большинство слагаемых, кроме $c_i$ и самых крайних $\varphi(S_0)$ и $\varphi(S_n)$, взаимно уничтожаются. Получаем, 

$$
\sum_{i=1}^m c'_i = \sum_{i=1}^m c_i + \varphi(S_n) - \varphi(S_0).
$$

Откуда 

$$
\sum_{i=1}^m c_i = \sum_{i=1}^m c'_i + \varphi(S_0) - \varphi(S_n).
$$

Так как $\varphi(S_0) = 0$ и $\varphi(S_i) \geqslant 0$ (теперь понятно, зачем было это требовать в определении функции потенциала), получаем оценку 

$$
\sum_{i=1}^m c_i \leqslant \sum_{i=1}^m c'_i.
$$

Если мы хотим показать, например, что амортизационная сложность для операций составляет константу $\mathcal{O}(1)$, то нам нужно подобрать функцию потенциала $\varphi$ таким образом, чтобы существовало такое достаточно большое фиксированное число $T$ и $\forall\,i$ было верно $c'_i \leqslant T$.

Тогда из неравенства выше получается 

$$
\sum_{i=1}^m c_i \leqslant \sum_{i=1}^m c'_i \leqslant mT \Rightarrow \frac{\sum_{i=1}^m c_i}{m} \leqslant T.
$$

Действительно, амортизационная сложность $T$ будет давать оценку сверху на среднюю сложность по цепочке операций. 

Таким образом, на практике вся наука будет работать, если только суметь грамотно подобрать неотрицательную функцию $\varphi$ на множестве состояний структуры $\mathcal{D}$, а затем вычислить $c'$ для нужной операции и показать, что получается, что нужно. 

Интуитивно можно воспринимать потенциал состояния структуры данных как ее некоторый  <<заряд>>. Действительно, если мы совершаем быструю операцию $a_i$ с маленьким $c_i$, то потенциал $\varphi(S_i)$ должен вырасти, и обратно, если операция сложная и долгая, то структура данных как бы <<разряжается>>, и потенциал падает, тратя <<накопленную энергию>> за счет быстрых операций. Получается, если быстрых операций достаточно много, и мы накопим хороший потенциал, то у нас получится <<сгладить>> прыжок по реальной сложности $c_i$
у дорогой операции.


<div class="question">Может ли случиться такое, что $c'_i < 0?$ Если да, то в каких ситуациях?</div>

Применим физический метод к анализу цепочки `push_back` над изначально пустым вектором целых чисел. Обычно, в качестве состояния выбирают конкретные важные для анализа структуры данных параметры. В нашем случае определим состояние $S=(\texttt{size},\,\texttt{capacity})$ как пару параметров вектора. Функцию потенциалов выберем следующим образом 

$$
\varphi(S_i) = 2\times\texttt{size}_i - \texttt{capacity}_i.
$$

Заметим, что в любой момент времени для вектора верно $\texttt{size}_i \geqslant \frac{\texttt{capacity}_i}{2}$. Таким образом $\varphi(S_i)\geqslant 0$. Также $\varphi(S_0) = 2\cdot 0 - 0 = 0$. Следовательно, функция $\varphi$ удовлетворяет требованиям функции потенциала.

Вычислим теперь амортизационную сложность операции `push_back`. По определению получаем 

$$
c'_i = c_i + \varphi(S_i) - \varphi(S_{i-1}) = c_i + 2\times\texttt{size}_i - \texttt{capacity}_i - 2\times\texttt{size}_{i-1} + \texttt{capacity}_{i-1}.
$$

Здесь тоже приходится разбирать два случая. Предположим, что реаллокация не потребовалась. В таком случае $c_i=1$, так как мы просто заносим новый элемент в ячейку памяти,  $\texttt{size}\_i = \texttt{size}\_\{i-1\} + 1$, при этом $\texttt{capacity}\_i=\texttt{capacity}\_\{i-1\}$, так как эта характеристика без реаллокаций принципиально не меняется. Подставляя все это выше, получим

$$
c'_i = 1 + 2\times(\texttt{size}_{i-1} + 1) - \texttt{capacity}_{i-1} - 2\times\texttt{size}_{i-1} + \texttt{capacity}_{i-1} = 3.
$$

Теперь рассмотрим случай, когда реаллокация была сделана. Для простоты обозначений положим, что $\texttt{size}\_\{i-1\} = \texttt{capacity}\_\{i-1\} = k$. Тогда $c_i = k + 1$, так как нам необходимо сначала скопировать $k$ элементов вектора, а затем приписать туда новый. Далее $\texttt{size}_i = k + 1$, а $\texttt{capacity}_i=2k$. Получаем 

$$
c'_i = k + 1 + 2(k + 1) - 2k - 2k + k = 3.
$$
 
Таким образом, физическим методом было показано, что амортизационная сложность операции `push_back` составляет $\mathcal{O}(1)$.

{% include task.html 
task1="Проверьте, сойдется ли амортизационный анализ для той же цепочки `push_back`, если использовать функцию потенциала $\varphi(S) = 3\times \texttt{size} - \texttt{capacity}$."
label1="amort1"
tip1="Проведите разбор двух случаев"
solution1="Да, такая формула будет работать. Сделаем те же подстановки для случая без реаллокации $\varphi(S) = 1 + 3(\texttt{size}\_\{i-1\}+1) - \texttt{capacity}\_\{i-1\} - 3\cdot \texttt{size}\_\{i-1\} + \texttt{capacity}\_\{i-1\}=4=\mathcal{O}(1)$. Для случая с реаллокацией используем те же обозначения: $\varphi(S)=k + 1 + 3(k+1) - 2k - 3k + k=4=\mathcal{O}(1)$. Нетрудно догадаться, что для расширения вектора в 2 раза будет годиться любая формула вида $\varphi(S)=\lambda\texttt{size} - \texttt{capacity},\,\lambda\geqslant2$."
%}

{% include onepar_element.html type="remark" 
text="*Мы с другом провели амортизационный анализ некоторой цепочки операций и получили совершенно разные оценки. Кто из нас прав?* Правы все. Дело в том, что нет никакого правильного ответа. Есть разные анализы, использующие, например, различные функции потенциала. И, если эти анализы проведены без ошибок, то все выведенные оценки, а они могут быть разными, являются верными. Другое дело, насколько полученная оценка удовлетворяет автора анализа, насколько она уместна в той или иной ситуации."
%}

