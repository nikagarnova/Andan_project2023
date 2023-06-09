![picture by Kandinsky 2.1. promt: капибары-детективы ищут фильмы](https://img2.rudalle.ru/images/3d/d1/ca/3dd1ca4674c9453c948751d0c15f671c_00000.jpg)
# Неопознанные капибары в поисках хороших фильмов

Привет! Мы делаем проект по анализу фильмов из [огромного рейтинга imdb]([url](https://www.imdb.com/search/title/?title_type=feature)). Изучаем, что влияет на успешность фильма, а что отталкивает зрителей.

**Навигация по гиту:**

[часть 1. парсинг](https://github.com/nikagarnova/Andan_project2023/blob/main/pt_1_parsing.ipynb) — здесь рассказали, как собирали данные для проекта

[часть 2. первая работа с данными, визуализации и начало лингвистического анализа](https://github.com/nikagarnova/Andan_project2023/blob/main/pt_2_data.ipynb) — здесь обработали данные, посмотрели, какие жанры наиболее популярны и выяснили, в каких жанрах чаще всего встречаютя описания с упоминанием приключений, любви, убийств или войны.

-> как мы собирали данные для тематических блоков и отбирали ненужные слова, [рассказали тут](https://github.com/nikagarnova/Andan_project2023/blob/main/additional_wordlists.ipynb)

[часть 3. работа с лингвистическими данными](https://github.com/nikagarnova/Andan_project2023/blob/main/pt_3_lingvojokes.ipynb) — продолжили анализировать показатели, посмотрели на квантили

[предсказание, относится фильм к драме или нет](https://github.com/nikagarnova/Andan_project2023/blob/main/%D0%9F%D1%80%D0%BE%D0%B5%D0%BA%D1%82_6%20(2).ipynb) — собрали три модели, последняя самая классная

комментарии к этой части:

код 1: предсказательная модель, относится ли фильм к жанру “Drama” или нет, построенная только на данных исходной таблицы (два рейтинга, год релиза,  количество голосов и продолжительность). Результат accuracy =  0.61 ; roc_auc =  0.60 ; precision =  0.57 ; recall =  0.93. Результаты невысокие, за исключением recal. Это означает, что модель имеет относительно низкую точность, но высокую полноту,  то есть классифицирует избыточное число примеров как положительные.

код 2: в данном коде мы добавляем столбцы с бинарными значениями отсутствия/наличия лексикона каждого жанра. То есть создается список из 20 самых популярных слов в описаниях фильмов каждого жанра (за исключением слов, не имеющих смысла), а затем описание каждого фильма проверяется на наличие/отсутствие хотя бы одного слова из этого списка). Также создаются бинарные столбцы, показывающие наличие/отсутствие лексикона про войну, любовь, смерть/убийства и путешествия. Также удаляются все столбцы с оригинальными, изначальными данными. То есть предсказательная модель работает исключительно на новых созданных бинарных данных, анализирующих лексикон. Модель показывает результаты: accuracy =  0.60 ; roc_auc =  0.60 ; precision =  0.60 ; recall =  0.63, что сопоставимо с изначальной моделью. Предсказательная мощность модели низкая, но сам факт того, что можно исключительно по текстовому анализу описания построить модель, выдающую не рандомизированные результаты отношения фильма к жанру (тем более к драме, при наличии романтических фильмов и мюзиклов в выборке), не может не радовать.

код 3: теперь объединим наши две модели, то есть добавим к изначальным данным (рейтингов, года выпуска фильма, продолжительности и количеству голосов) наши созданные бинарные переменные анализа лексикона описания каждого фильма. Результат: accuracy =  0.72 ; roc_auc =  0.72 ; precision =  0.70 ; recall =  0.78. Метрики показывают, что точность выросла более чем на 0.1 по сравнению с обеими предыдущими моделями. Значит вместе анализ числовых характеристик фильм и лексических характеристик описания фильма работает на порядок лучше, чем по отдельности.
Здесь хочется обратить внимание на точность предсказания: 72% при том, что никакой естественной/настоящей корреляции у жанра с данными нет. Для тестов подобного рода это высокая точность. Также хочется обратить внимание на то, что числовые и бинарные (лексические переменные) не спутались и ухудшили оценку при объединенной модели, а улучшили. 

[моделька определяет принадлежность фильма к очень хорошим](https://github.com/nikagarnova/Andan_project2023/blob/main/%D0%9F%D1%80%D0%BE%D0%B5%D0%BA%D1%82_7.ipynb) — Мы хотим построить модель, предсказывающую является ли фильм очень хорошим - то есть относится ли он к лучшим 20% фильмам по рейтингу.
Мы узнали самые популярные непересекающиеся слова в каждом квинтиле (то есть вывели по 100 самых популярных слов для каждого квинтиля и потом удалили все неуникальные слова. Если слово “magic” встречалось в 1ом и 2ом квинтиле оно удалялось из обоих списков). После этого для каждого слова мы создали отдельный столбец с бинарными значениями - 0 , если этого слова нет в описании и 1 - если есть.
Все вышесказанное было сделано по тренировочной выборке (то есть лексика только из тренировочной выборки). После этого столбцы были добавлены и в тестовую выборку и была запущена логистическая регрессия с поиском solver’a. Результат: accuracy =  0.84 ; roc_auc =  0.68 ; precision =  0.79 ; recall =  0.39. Все показатели кроме recall говорят о достаточном качестве теста. Он обладает высокой точностью, но низкой полнотой. Если этот тест действительно использовать для поиска хорошего фильма, то вероятность того, что он посоветует вам плохой фильм низкая, но при это высока вероятность, что он пропустит хороший и не посоветует его вам.

модели, предсказывающие оценки в рейтингах:

[рабочий imbd score](https://github.com/nikagarnova/Andan_project2023/blob/main/%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82_imbd.ipynb) — рабочий, но загрузили в 0:05

[imbd](https://github.com/nikagarnova/Andan_project2023/blob/main/%D0%9F%D1%80%D0%BE%D0%B5%D0%BA%D1%82%201.2.ipynb) — бесполезно смотреть, случайно закинули не запущенный файл

[metascore](https://github.com/nikagarnova/Andan_project2023/blob/main/%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82_%D0%BC%D0%B5%D1%82%D0%B0%D1%81%D0%BA%D0%BE%D1%80.ipynb)

Здесь мы хотим построить модели, предсказывающие рейтинг фильма по остальным переменным (включая второй рейтинг). Мы используем несколько моделей для того, чтобы определить, какая из них лучше справится с задачей в данной ситуации.
Видно, что наилучшую возможную оценку imbd_rate предлагает GradientBoostingRegressor,  R-squared = 0.6778, что означает, что модель объясняет 67.8% вариации зависимых данных. Наилучшую оценку metascore_rate предлагает SVR-model, R-squared = 0.55, что значительно хуже оценки imbd_rate .

Обе модели свидетельствуют о несильной взаимосвязанности рейтингов между собой.




Дополнительно:

[список фильмов после парсинга](https://github.com/nikagarnova/Andan_project2023/blob/main/list.csv)

[список фильмов после обработки, которые используем дальше везде](https://github.com/nikagarnova/Andan_project2023/blob/main/result.csv)
