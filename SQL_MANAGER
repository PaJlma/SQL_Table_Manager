from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.common.keys import Keys
import time
import pymysql

def sqlconnector(): #Соединение с базой данных
    dbname = input('Введите название базы данных MySQL: ')

    try:
        connection = pymysql.connect(
            host='gendalf.cf',
            port=3308,
            user='root',
            password='1234567890',  # <------------- Подключение к БД SQL
            database=dbname,
            cursorclass=pymysql.cursors.DictCursor
        )
        print('Connected\n\n\n\n\n\n')
        commandinputer(connection)
        return connection
    except:
        print('Неверное имя базы данных')
        sqlconnector()

def commandinputer(connection): #Ввод команд
    print('Список доступных команд:\n\ncreatetable - создать таблицу\nfirstparse - заполнение ПУСТОЙ таблицы первичными данными\n\n')
    command = input('Input command: ')

    if command == 'createtable':
        tablename = input('Название таблицы: ')
        try:
            with connection.cursor() as cursor:
                create_table_query = f"CREATE TABLE `{tablename}`(id int AUTO_INCREMENT," \
                                      " name text(8000)," \
                                      " price text(8000)," \
                                      " href text(8000), PRIMARY KEY (id));"
                cursor.execute(create_table_query)
            print('Table created succesfully')
            commandinputer(connection)
        finally:
            connection.close()

    elif command == 'firstparse':
        dbnamefirstparse = input('Введите название ПУСТОЙ таблицы, которую нужно заполнить данными: ')
        print('Ожидайте...')
        options = webdriver.ChromeOptions()
        #options.add_argument('--headless')
        options.add_argument('--no-sandbox')
        options.add_argument('window-size=1400,600')
        options.add_argument('user-agent=Mozilla/5.0 (Windows NT 6.3; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.45 Safari/537.36')  # Смена user-agent
        chrome_prefs = {}
        options.experimental_options["prefs"] = chrome_prefs
        chrome_prefs["profile.default_content_settings"] = {"images": 2}
        chrome_prefs["profile.managed_default_content_settings"] = {"images": 2}

        try:
            url = 'https://www.dns-shop.ru/catalog/17a8a01d16404e77/smartfony/?order=2&groupBy=avails&stock=now-today-tomorrow-later'
            wd = webdriver.Chrome('chromedriver', options=options)

            wd.get(url)
            time.sleep(2)
            # wd.get(url)
            # time.sleep(2)

            ##############################################################################
            try:
                wd.find_element(By.CLASS_NAME, 'w-choose-city-widget').click()
                time.sleep(2)
                wd.find_element(By.CLASS_NAME, 'form-control').send_keys('Ростов-на-Дону')
                wd.find_element(By.LINK_TEXT, 'Ростов-на-Дону').click()
                time.sleep(2)
            except:  # <------------- Этот блок - выбор города
                wd.find_element(By.CLASS_NAME, 'location-icon').click()
                time.sleep(2)
                wd.find_element(By.CLASS_NAME, 'form-control').send_keys('Ростов-на-Дону')
                wd.find_element(By.LINK_TEXT, 'Ростов-на-Дону').click()
                time.sleep(2)
            ##############################################################################

            wd.find_element(By.CLASS_NAME, 'pagination-widget__show-more-btn').click()  # Расширить колличество смартфонов
            time.sleep(2)
            box = wd.find_elements(By.CLASS_NAME, 'catalog-product')[:30]  # Первые 30 дорогих смартфонов
            number = 1
            for i in box:
                name = '\'' + i.find_element(By.CLASS_NAME, 'catalog-product__name').text + '\''  # Поиск Названия
                price = '\'' + i.find_element(By.CLASS_NAME, 'product-buy__price').text + '\''  # Поиск Цены
                href = '\'' + i.find_element(By.CLASS_NAME, 'catalog-product__name').get_attribute('href') + '\''  # Поиск Ссылки

                # wd.execute_script('''window.open("https://www.google.com", "_blank");''') # Открытие новой владки
                # wd.switch_to.window(wd.window_handles[1]) # Переключение на эту вкладку
                # wd.get(href) # Открытие в этой вкладке страницы со смартфоном

                # time.sleep(2)
                # index = wd.find_element(By.CLASS_NAME, 'product-card-top__code').text.replace('Код товара: ', '') # Поиск Индекса
                # wd.close() # Закрытие этой вкладки
                # time.sleep(2)
                # wd.switch_to.window(wd.window_handles[0]) # Переключение обратно на первую вкладку

                with connection.cursor() as cursor:
                    insert_query = f"INSERT INTO `{dbnamefirstparse}` (name, price, href) VALUES ({name}, {price}, {href})"
                    cursor.execute(insert_query)
                    connection.commit()

        except Exception as ex:
            print(ex)

        finally:
            wd.quit()

        print('Таблица успешно заполнена\n\n\n\n\n\n')
        commandinputer(connection)

    else:
        print('Неизвестная команда')
        commandinputer(connection)

sqlconnector()
