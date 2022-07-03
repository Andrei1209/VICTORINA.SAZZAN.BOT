# VICTORINA.SAZZAN.BOT
Я Сазанович Андрей группа №143
VICTORINA.SAZZAN.BOT - это бот, представлющей собой викторину на тему 'python'
написаный на библиотеке telebot. Новый пораграф программа состоит из трёх Message Handler'ов.
1. ```/start```
```python
@bot.message_handler(commands=['start'])
def send_welcome(message):
    bot.send_message(message.chat.id, "Hello, answer a few questions pls", reply_markup=markup)
    temp_list = list(filter(lambda x: x['chat'].id == message.chat.id, chats))

    if len(temp_list) > 0:
        list(filter(lambda x: x['chat'].id == message.chat.id, chats))[0]['progress'] = 0
        list(filter(lambda x: x['chat'].id == message.chat.id, chats))[0]['is_done'] = False
    else:
        chats.append({
            "chat": message.chat,
            "progress": 0,
            "is_done": False
        })
    answers = types.ReplyKeyboardMarkup(resize_keyboard=True, row_width=2)
    first = questions[0]['v'][0]
    second = questions[0]['v'][1]
    third = questions[0]['v'][2]
    fourth = questions[0]['v'][3]
    answers.add(first, second, third, fourth)
    bot.send_message(message.chat.id, questions[0]['q'], reply_markup=answers)
```
2. ```/question```
```python
@bot.message_handler(commands=['question'])
def send_active_question(message):
    progress = list(filter(lambda x: x['chat'].id == message.chat.id, chats))[0]['progress']
    if progress == len(questions) - 1:
        send_welcome(message)
    else:
        answers = types.ReplyKeyboardMarkup(resize_keyboard=True, row_width=2)
        first = questions[progress]['v'][0]
        second = questions[progress]['v'][1]
        third = questions[progress]['v'][2]
        fourth = questions[progress]['v'][3]
        answers.add(first, second, third, fourth)
        bot.send_message(message.chat.id, str(questions[progress]['q']), reply_markup=answers)
```
3. Любое сообщение
```python
@bot.message_handler()
def any_message(message):
    if message.content_type == 'text' and message.text[0] != '/':
        progress = list(filter(lambda x: x['chat'].id == message.chat.id, chats))[0]['progress']
        is_done = list(filter(lambda x: x['chat'].id == message.chat.id, chats))[0]['is_done']

        if is_done:
            bot.send_message(message.chat.id, 'Молодец, ты уже прошел тест, напиши /start чтобы пройти заново!', reply_markup=markup)
        else:
            question = questions[progress]
            if message.text == question['a']:
                bot.send_message(message.chat.id, 'Правильно :)')

                if progress == len(questions) - 1:
                    bot.send_message(message.chat.id, 'Поздравляю, ты закончил викторину:)', reply_markup=markup)
                    list(filter(lambda x: x['chat'].id == message.chat.id, chats))[0]['is_done'] = True
                else:
                    list(filter(lambda x: x['chat'].id == message.chat.id, chats))[0]['progress'] += 1
                    progress = list(filter(lambda x: x['chat'].id == message.chat.id, chats))[0]['progress']
                    answers = types.ReplyKeyboardMarkup(resize_keyboard=True, row_width=2)
                    first = questions[progress]['v'][0]
                    second = questions[progress]['v'][1]
                    third = questions[progress]['v'][2]
                    fourth = questions[progress]['v'][3]
                    answers.add(first, second, third, fourth)
                    bot.send_message(message.chat.id, questions[progress]['q'], reply_markup=answers)
            else:
                answers = types.ReplyKeyboardMarkup(resize_keyboard=True, row_width=2)
                first = questions[progress]['v'][0]
                second = questions[progress]['v'][1]
                third = questions[progress]['v'][2]
                fourth = questions[progress]['v'][3]
                answers.add(first, second, third, fourth)
                bot.send_message(message.chat.id, 'Попробуй еще раз :(', reply_markup=answers)
```
Сохранияем прогресс пользователя в массиве chats.
У каждого вопроса есть 4 ответа, но только один из них правильный, которые всплывают над полем ввода.

После правильного ответа выводит сообщение ```Правильно:)```

Если ответ был не правильный то выводиться сообщение ```Попробуй еще раз:(```

После прохождения викторины Выводиться сообщение ```Поздравляю, ты закончил викторину:)```

Чтобы пройти еще раз, надо написать команду ```start``` или ```question```

Вопросы представлены словарем с параметрами ```q```(вопрос), ```a```(правильный ответ) и ```v``` (варианты ответа)
```python
questions = [
    {"q": "Обозночение функции в python", "a": "def", "v": ['func', 'functions', 'void', 'def']},
    {"q": "Какого типа питон", "a": "Динамически типизированный", "v": ['Динамически типизированный', 'Не типизированный', 'Низкоуровневый', 'Только функциональный']},
    {"q": "Когда был создан python?", "a": "1994", "v": ['2001', '1994', '2003', '1999']},
    {"q": "Кто создал python?", "a": "Гвидо ван Россум", "v": ['Пушкин', 'Гвидо ван Россум', 'Бьёрн Страуструп', 'Джеймс Гослинг']},
    {"q": "Что делает функция return?", "a": "Возвращает значение", "v": ['Возвращает значение', 'Заглушка', 'Переводит в начало блока', 'Возвращает строку']},
    {"q": "Что такое lambda в Python", "a": "Одноразовая анонимная функция", "v": ['Cчитает количество элементов', 'Принимает итерации', 'Принимающий аргументы и возвращающий значение', 'Одноразовая анонимная функция']},
    {"q": "Как нельзя обращаться к элементам dict()", "a": "[int]", "v": ['Через точку(.)', '.get()', '[\'str\']', '[int]']},
]
```

