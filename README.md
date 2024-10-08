import telebot
from telebot import types

# Replace with your bot's token
API_TOKEN = '7560965137:AAH2nBfNwfgxfi0ItjcL7Lwy9vhqcEstMGY'
bot = telebot.TeleBot(API_TOKEN)

# Updated club data
clubs = {
    'Technical': ['Trinity 🛠️', 'Technical Board 🔧'],
    'Non-Technical': ['Cultural Board 🎨', 'DDQ 🎭']
}

# Contact information (No images for now)
club_info = {
    'Trinity 🛠️': {
        'Faculty': {
            'Name': 'Prof. Kuldeep Hule',
            'Contact': '1234567890'
        },
        'Mentor': {
            'Names': [
                ('Harsh Bisht', '9103204360'),
                ('Aryan Dabholkar', '7906799482'),
                ('Abrish', '6787453091'),
                ('Yuvraj Singh', '8876593452')
            ]
        },
        'Secretary': {
            'Names': [
                ('Nitin Mahala', '9462137180'),
                ('Roshnee Gouda', '8080870414')
            ]
        },
        'Joint Secretary': {
            'Names': [
                ('Abhishek Kumar', '8923302946'),
                ('Gaurav Pilania', '9991267063'),
                ('Raj Kumar', '9034428909'),
                ('Palak Kundu', '8708234825'),
                ('Gaurav Kumar Singh', '7061526180'),
                ('Salit Yadav', '6398931639'),
                ('Sana Kamirkar', '9980520349'),
                ('Summanyu Nayak', '9903273669')
            ]
        }
    },
    'Technical Board 🔧': {
        'Faculty': {
            'Name': 'Dr. PB Karandikar',
            'Contact': '1234567890'
        },
        'Mentor': {
            'Names': [
                ('Vansh Vatsal', '7620310340'),
                ('Anushna Pawar', '8667543781'),
                ('Aditya Tiwari', '9053182559'),
                ('Ayush Bhadoria', '9461500498')
            ]
        },
        'Secretary': {
            'Names': [
                ('Nabajit Das', '8509442084'),
                ('Nisha Dhaka', '7689690324')
            ]
        },
        'Joint Secretary': {
            'Names': [
                ('Sneha', '6387029949'),
                ('Sameer Sekhawat', '8708442966'),
                ('Shashank Tiwari', '8882465015'),
                ('Shreya', '7651965112'),
                ('Aditya Raj', '9140992004')
            ]
        }
    },
    'Cultural Board 🎨': {
        'Faculty': {
            'Name': 'Dr. Preeti Warrior',
            'Contact': '1234567890'
        },
        'Mentor': {
            'Names': [
                ('Ankana Sardar', '8995423162'),
                ('Aryan Dabholkar', '7906799482'),
                ('Simran', '7387883171'),
                ('Arnav Singh', '9411678749')
            ]
        },
        'Secretary': {
            'Names': [
                ('Satyam Sathpati', '7297881135'),
                ('Pallavi Shirshat', '8197761199')
            ]
        },
        'Joint Secretary': {
            'Names': [
                ('Piyush Kumar', '9256710877'),
                ('Sandeep Mondal', '9257666997'),
                ('Harita', '7989681871'),
                ('Mukul Rewar', '9256564140')
            ]
        }
    },
    'DDQ 🎭': {
        'Faculty': {
            'Name': 'Mr. Manoj Khaladkar',
            'Contact': '1234567890'
        },
        'Mentor': {
            'Names': [
                ('Pankaj Rai', '6266017512'),
                ('Tarush Pandey', '8284058266'),
                ('Aryan Dabholkar', '7906799482'),
                ('Reema Singh', '7654892389')
            ]
        },
        'Secretary': {
            'Names': [
                ('Chetan Singh', '9602866736'),
                ('Rajat Singh', '7985001490')
            ]
        },
        'Joint Secretary': {
            'Names': [
                ('Sohila Kaur', '9999078811'),
                ('Millan Patra', '9156348972'),
                ('Mothitesh Thakur', '8988907990'),
                ('Gaurav Yadav', '6367401971')
            ]
        }
    }
}

# Start command
@bot.message_handler(commands=['start'])
def start(message):
    markup = types.ReplyKeyboardMarkup(one_time_keyboard=True, resize_keyboard=True)

    # Add emoji for a visually pleasing effect
    markup.add('🛠️ Technical', '🎨 Non-Technical')

    msg = bot.reply_to(message, "👋 Welcome! Please choose the type of club you're interested in:", reply_markup=markup)
    bot.register_next_step_handler(msg, process_club_type)

# Handle club type selection
def process_club_type(message):
    club_type = message.text.replace('🛠️ ', '').replace('🎨 ', '')

    if club_type not in ['Technical', 'Non-Technical']:
        bot.reply_to(message, "❌ Invalid selection. Please choose either 'Technical' or 'Non-Technical'.")
        return

    markup = types.ReplyKeyboardMarkup(one_time_keyboard=True, resize_keyboard=True)

    for club in clubs[club_type]:
        markup.add(club)

    msg = bot.reply_to(message, f"📋 Select a club from {club_type} clubs:", reply_markup=markup)
    bot.register_next_step_handler(msg, process_club_name)

# Handle club name selection
def process_club_name(message):
    club_name = message.text

    if club_name not in club_info.keys():
        bot.reply_to(message, "❌ Invalid club. Please select a valid club.")
        return

    markup = types.ReplyKeyboardMarkup(one_time_keyboard=True, resize_keyboard=True)
    markup.add('👩‍🏫 Faculty in-charge', '🎓 BE mentors', '📋 Club secretaries', '🤝 Club joint secretaries')
    msg = bot.reply_to(message, f"👍 You selected {club_name}. What information would you like?", reply_markup=markup)
    bot.register_next_step_handler(msg, lambda msg: process_club_options(msg, club_name))

# Handle club options selection
def process_club_options(message, club_name):
    option = message.text
    valid_options = ['👩‍🏫 Faculty in-charge', '🎓 BE mentors', '📋 Club secretaries', '🤝 Club joint secretaries']

    if option not in valid_options:
        bot.reply_to(message, "❌ Invalid option. Please choose a valid one.")
        return

    if option == '👩‍🏫 Faculty in-charge':
        info = club_info[club_name]['Faculty']
        send_person_info(message, info)
    elif option == '🎓 BE mentors':
        for mentor in club_info[club_name]['Mentor']['Names']:
            send_person_info(message, {'Name': mentor[0], 'Contact': mentor[1]})
    elif option == '📋 Club secretaries':
        for secretary in club_info[club_name]['Secretary']['Names']:
            send_person_info(message, {'Name': secretary[0], 'Contact': secretary[1]})
    else:  # 🤝 Club joint secretaries
        for joint_secretary in club_info[club_name]['Joint Secretary']['Names']:
            send_person_info(message, {'Name': joint_secretary[0], 'Contact': joint_secretary[1]})

    # Ask if they need further assistance or end session
    markup = types.ReplyKeyboardMarkup(one_time_keyboard=True, resize_keyboard=True)
    markup.add('✅ Yes', '❌ No')
    msg = bot.reply_to(message, "Do you need further assistance?", reply_markup=markup)
    bot.register_next_step_handler(msg, process_end)

# Helper function to send person info
def send_person_info(message, person_info):
    bot.send_message(message.chat.id, f"📞 {person_info['Name']}: {person_info['Contact']}")

# Handle end of session
def process_end(message):
    if message.text == '✅ Yes':
        start(message)
    else:
        bot.reply_to(message, "👋 Thank you for using the club bot. Do visit again!")

# Polling
bot.infinity_polling()
