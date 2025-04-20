Название проекта: Парсер  фильмов с сайта kinoafisha.info
Этот проект предназначен для сбора информации о рейтингах фильмов с сайта kinoafisha.info
Скрипт собирает данные о названии фильма, жанре и рейтинге и сохраняет их в Excel-файл.

Постановка задачи
Цель — автоматически собрать 1000 рейтинговых оценок фильмов с сайта kinoafisha.info. Для этого реализован скрипт, который осуществляет парсинг страниц сайта, извлекает нужные данные и сохраняет их в файл movie_rates.xlsx.

Требования
Перед запуском убедитесь, что у вас установлены необходимые библиотеки:

requests
beautifulsoup4
pandas
openpyxl (для сохранения в Excel)

Инструкция по сборке и запуску:
1)создайте новый файл с именем collect_movies.py 
2)Установка необходимых библиотек:
pip install requests beautifulsoup4 pandas openpyxl
3)Запуск скрипта
Вставьте следующий код:

import requests
from bs4 import BeautifulSoup
import pandas as pd

def collect_movie_ratings():
    page_num = 0
    data = []
    target_count = 1000
    while len(data) < target_count:
        url = f'https://www.kinoafisha.info/rating/movies/?page={page_num}'
        html_content = requests.get(url).text

        # Создаем объект BeautifulSoup для парсинга
        soup = BeautifulSoup(html_content, 'html.parser')

        # Находим все элементы, содержащие информацию о фильмах
        entries = soup.find_all('div', class_='movieList_item movieItem movieItem-rating movieItem-position')

        # Если на странице нет элементов или их меньше, чем нужно для достижения цели
        if not entries:
            break

        for entry in entries:
            if len(data) >= target_count:
                break  # Достигли нужного количества, выходим из цикла

            div_film_name = entry.find('div', class_='movieItem_info')
            film_name = div_film_name.find('a').text if div_film_name and div_film_name.find('a') else 'Не найдено'

            div_rating = entry.find('span', class_='movieItem_itemRating miniRating miniRating-good')
            rating = div_rating.text if div_rating else 'Не найдено'

            div_genres = entry.find('div', class_='movieItem_details')
            genres = div_genres.find('span').text if div_genres and div_genres.find('span') else 'Не найдено'

            # Добавляем новую пару ключ-значение
            data.append({'название фильма': film_name, 'жанр': genres, 'рейтинг фильма': rating})

        page_num += 1  # Переходим на следующую страницу

    return data

movie_rates = collect_movie_ratings()
# Создание записи в Excel
df = pd.DataFrame(movie_rates)
df.to_excel('movie_rates.xlsx', index=False)

print("Данные успешно сохранены в movie_rates.xlsx")

Запуск:
python collect_movies.py

После выполнения скрипта в текущей папке появится файл movie_rates.xlsx с собранными данными