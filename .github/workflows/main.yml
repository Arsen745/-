import telebot
from telebot import types
import sqlite3
import random

bot = telebot.TeleBot('5840360159:AAHkmTbJCz9if4RhquKncnbVoWFUEQR-fNU')

# Функция для получения соединения с базой данных
def get_database_connection():
    return sqlite3.connect('mydatabase.db', check_same_thread=False)

# Функция для создания таблицы users, если ее не существует
def get_random_user(gender):
    try:
        conn = get_database_connection()
        cursor = conn.cursor()

        cursor.execute("SELECT name, instagram, phone_number FROM users WHERE gender = ? ORDER BY RANDOM() LIMIT 1", (gender,))
        random_user = cursor.fetchone()

        return random_user

    except Exception as e:
        print(f"An error occurred while fetching a random user data: {e}")

    finally:
        conn.close()

# Вызов функции для создания таблицы


# Функция для сохранения данных пользователя в базу данных
# Функция для сохранения данных пользователя в базу данных
def save_user_info(chat_id, name, gender, instagram=None, phone_number=None):
    try:
        with get_database_connection() as connection:
            cursor = connection.cursor()

            cursor.execute("INSERT INTO users (chat_id, name, gender, instagram, phone_number) VALUES (?, ?, ?, ?, ?)",
                           (chat_id, name, gender, instagram, phone_number))

            connection.commit()
    except Exception as e:
        print(f"An error occurred while saving user info: {e}")

# Функция для получения соединения с базой данных
def get_database_connection():
    return sqlite3.connect('mydatabase.db', check_same_thread=False)

# Обработчик команды /start
# Обработчик команды /start
# Обработчик команды /start
@bot.message_handler(commands=['start'])
def start(message):
    markup = types.ReplyKeyboardMarkup(row_width=2, one_time_keyboard=True)
    item_boy = types.KeyboardButton('Мальчик')
    item_girl = types.KeyboardButton('Девочка')
    markup.add(item_boy, item_girl)

    bot.send_message(message.chat.id, "Привет хочешь найти свою любовь❤️! Ты мальчик💁‍♂️ или девочка💁‍♀️?", reply_markup=markup)
    bot.register_next_step_handler(message, process_gender_step)

# Словарь для хранения состояний пользователей
user_states = {}



# Обработчик выбора пола
# Обработчик выбора пола
# Обработчик выбора пола
def process_gender_step(message):
    chat_id = message.chat.id
    gender = message.text.lower()

    user_states[chat_id] = {'gender': gender}

    markup = types.ReplyKeyboardRemove()
    bot.send_message(chat_id, f"Ты выбрал(а) {gender}. Теперь напиши свое имя:")
    bot.register_next_step_handler(message, process_name_step)




# Обработчик ввода имени
# Обработчик ввода имени
def process_name_step(message):
    chat_id = message.chat.id
    name = message.text

    user_data = user_states.get(chat_id, {})

    user_data['name'] = name

    if user_data['gender'] == 'мальчик':
        bot.send_message(chat_id, "Теперь напиши свой инстаграм:")
        bot.register_next_step_handler(message, process_instagram_step)
    elif user_data['gender'] == 'девочка':
        bot.send_message(chat_id, "Теперь напиши свой инстаграм:")
        bot.register_next_step_handler(message, process_instagram_step)
    else:
        bot.send_message(chat_id, "Что-то пошло не так. Неверный пол в данных пользователя.")

# Обработчик ввода инстаграма для девочек
def process_instagram_step_for_girls(message):
    chat_id = message.chat.id
    instagram = message.text

    user_data = user_states[chat_id]
    user_data['instagram'] = instagram

    # Дополнительные шаги для девочек (например, можно спросить еще что-то)
    bot.send_message(chat_id, "Дополнительные шаги для девочек...")
    # Или перейти к следующему шагу (например, поиск пары)
    bot.register_next_step_handler(message, process_find_pair_step)


# Обработчик ввода инстаграма
# Обработчик ввода инстаграма
def process_instagram_step(message):
    chat_id = message.chat.id
    instagram = message.text

    user_data = user_states[chat_id]
    user_data['instagram'] = instagram

    if user_data['gender'] == 'мальчик':
        bot.send_message(chat_id, "Теперь напиши свой номер телефона:")
        bot.register_next_step_handler(message, process_phone_step)
    elif user_data['gender'] == 'девочка':
        bot.send_message(chat_id, "Теперь напиши свой номер телефона:")
        bot.register_next_step_handler(message, process_phone_step_for_girls)
    else:
        bot.send_message(chat_id, "Что-то пошло не так. Неверный пол в данных пользователя.")


# Обработчик ввода номера телефона для девочек
def process_phone_step_for_girls(message):
    chat_id = message.chat.id
    phone_number = message.text

    user_data = user_states[chat_id]
    user_data['phone_number'] = phone_number

    save_user_info(chat_id, **user_data)

    # Добавляем вопрос после завершения регистрации
    markup = types.ReplyKeyboardMarkup(row_width=2, one_time_keyboard=True)
    item_yes = types.KeyboardButton('Да')
    item_no = types.KeyboardButton('Нет')
    markup.add(item_yes, item_no)

    bot.send_message(chat_id, "Спасибо! Твои данные сохранены. Хочешь найти себе пару?💖✌️", reply_markup=markup)
    bot.register_next_step_handler(message, process_find_pair_step)

# Обработчик для кнопки "Дать еще"
@bot.message_handler(func=lambda message: True)
def handle_another_one(message):
    chat_id = message.chat.id
    user_data = user_states.get(chat_id)

    if user_data and 'another_one' in user_data:
        answer = message.text.lower()

        if answer == 'да':
            process_find_pair_step(message)
        elif answer == 'нет':
            bot.send_message(chat_id, "Окей, если передумаешь, можешь всегда вернуться.")
        else:
            gender = user_data.get('gender', '').lower()

            if gender == 'мальчик':
                send_random_girl(chat_id)
            elif gender == 'девочка':
                send_random_boy(chat_id)
            else:
                bot.send_message(chat_id, "Что-то пошло не так. Неверный пол в данных пользователя.")
    else:
        # Обрабатываем команду "другой"
        if message.text.lower() == 'другой':
            process_find_pair_step(message)
        else:
            # Обрабатываем все остальные сообщения (включая номер телефона и инстаграм)
            process_name_step(message)

def send_random_girl(chat_id):
    random_girl = get_random_user('девочка')
    if random_girl:
        name, instagram, phone_number = random_girl
        bot.send_message(chat_id, f"😍😍 Мы нашли для тебя девочку по имени {name}. Её инстаграм: {instagram}, номер телефона: {phone_number}, \n посмотрите другое пишите 'другой")
    else:
        bot.send_message(chat_id, "К сожалению, нет подходящей девочки в базе данных.")

def send_random_boy(chat_id):
    random_boy = get_random_user('мальчик')
    if random_boy:
        name, instagram, phone_number = random_boy
        bot.send_message(chat_id, f"😍😍 Мы нашли для тебя мальчика по имени {name}. Его инстаграм: {instagram}, номер телефона: {phone_number}, \n посмотрите другое пишите 'другой'")
    else:
        bot.send_message(chat_id, "К сожалению, нет подходящего мальчика в базе данных.")

# Обработчик ввода номера телефона
# Обработчик ввода номера телефона
def process_phone_step(message):
    chat_id = message.chat.id
    phone_number = message.text

    user_data = user_states[chat_id]
    user_data['phone_number'] = phone_number

    save_user_info(chat_id, **user_data)

    # Добавляем вопрос после завершения регистрации
    markup = types.ReplyKeyboardMarkup(row_width=2, one_time_keyboard=True)
    item_yes = types.KeyboardButton('Да')
    item_no = types.KeyboardButton('Нет')

    markup.add(item_yes, item_no)

    bot.send_message(chat_id, f"Спасибо! Твои данные сохранены. Хочешь найти себе пару?", reply_markup=markup)
    bot.register_next_step_handler(message, process_find_pair_step)

# Обработчик выбора поиска пары
# Обработчик выбора поиска пары
# Обработчик выбора поиска пары
def process_find_pair_step(message):
    chat_id = message.chat.id
    answer = message.text.lower()

    user_data = user_states.get(chat_id, {})

    if answer == 'да':
        user_data['another_one'] = True  # Добавляем информацию, что пользователь хочет "Дать еще"
        gender = user_data.get('gender', '').lower()

        if gender == 'мальчик':
            send_random_girl(chat_id)
        elif gender == 'девочка':
            send_random_boy(chat_id)
        else:
            bot.send_message(chat_id, "Что-то пошло не так. Неверный пол в данных пользователя.")
    elif answer == 'нет':
        bot.send_message(chat_id, "Окей, если передумаешь, можешь всегда вернуться.")
    elif answer == 'дать еще':
        process_find_pair_step(message)
    else:
        bot.send_message(chat_id, "Пожалуйста, выбери 'Да', 'Нет' или 'Дать еще'.")
        bot.register_next_step_handler(message, process_find_pair_step)


# Функция для получения информации о последней девочке из базы данных
def get_last_girl():
    try:
        conn = get_database_connection()
        cursor = conn.cursor()

        cursor.execute("SELECT name, instagram, phone_number FROM users WHERE gender = 'девочка' ORDER BY user_id DESC LIMIT 1")
        last_girl = cursor.fetchone()

        return last_girl

    except Exception as e:
        print(f"An error occurred while fetching last girl data: {e}")

    finally:
        conn.close()

# Функция для получения информации о последнем мальчике из базы данных
def get_last_boy():
    try:
        conn = get_database_connection()
        cursor = conn.cursor()

        cursor.execute("SELECT name, instagram, phone_number FROM users WHERE gender = 'мальчик' ORDER BY user_id DESC LIMIT 1")
        last_boy = cursor.fetchone()

        return last_boy

    except Exception as e:
        print(f"An error occurred while fetching last boy data: {e}")

    finally:
        conn.close()

# Запуск бота
bot.polling(none_stop=True)
