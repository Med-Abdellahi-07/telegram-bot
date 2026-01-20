from telegram import Update, InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import ApplicationBuilder, CommandHandler, CallbackQueryHandler, MessageHandler, filters, ContextTypes

TOKEN = "PUT_YOUR_NEW_TOKEN_HERE"

ALLOWED_USERS = ["ST25000", "ST25001", "ST25003"]

FILES = {
    "Algèbre linéaire et applications": {
        "Chapitre 1": "BQACAgQAAxkBAAOOaWuBmblWgyA53XWsBlQpoYvaVKkAApMrAAIDq2FTx1LXORVtlWs4BA",
        "Chapitre 2": "BQACAgQAAxkBAAOnaWuKEG21ZzmSX5jJeB2JWVxygLAAAq4rAAIDq2FTP6KXwisqMKc4BA",
        "TD 1": "BQACAgQAAxkBAAIBy2lt5QE23QrP0dtDDUjYDm1jgrB7AAJeHAACaQNxU9hnH7PrnsxsOAQ",
        "TD 2": "BQACAgQAAxkBAAIBzGlt5QFIT9DQVIquTdSQ1uHuJ7I8AAJfHAACaQNxUz9_ZPH9JqsYOAQ",
        "Livre": "BQACAgQAAxkBAAIB3mlt-WfrIVG0S-4rUnrM3xf_sC8VAAKBHAACaQNxUzmuUQah6mrZOAQ"
    },

    "Anglais": {
        "Niveau A1": "BQACAgQAAxkBAAIB3Wlt-JpxXq1VSepO0oBhCGOmqZzkAAJ-HAACaQNxU6Lp1d68aCGfOAQ",
        "Niveau A2": "BQACAgQAAxkBAAIB3Glt-GKd7qRSgESVktNdLzDee9KfAAJ9HAACaQNxU9lxQz5NH_IHOAQ"
    }
}

USER_STATUS = {}

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("أدخل الرقم التعريفي:")

async def check_matricule(update: Update, context: ContextTypes.DEFAULT_TYPE):
    if update.message.text in ALLOWED_USERS:
        USER_STATUS[update.effective_user.id] = True
        keyboard = [[InlineKeyboardButton(k, callback_data=k)] for k in FILES]
        await update.message.reply_text("اختر المادة:", reply_markup=InlineKeyboardMarkup(keyboard))
    else:
        await update.message.reply_text("غير مصرح.")

async def choose_subject(update: Update, context: ContextTypes.DEFAULT_TYPE):
    query = update.callback_query
    await query.answer()

    subject = query.data
    keyboard = [[InlineKeyboardButton(k, callback_data=f"{subject}|{k}")] for k in FILES[subject]]
    await query.message.reply_text("اختر الدرس:", reply_markup=InlineKeyboardMarkup(keyboard))

async def send_file(update: Update, context: ContextTypes.DEFAULT_TYPE):
    query = update.callback_query
    await query.answer()

    subject, item = query.data.split("|")
    await query.message.reply_document(FILES[subject][item])

app = ApplicationBuilder().token(TOKEN).build()

app.add_handler(CommandHandler("start", start))
app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, check_matricule))
app.add_handler(CallbackQueryHandler(choose_subject, pattern="^(?!.*\\|).+"))
app.add_handler(CallbackQueryHandler(send_file, pattern=".*\\|.*"))

print("Bot running...")
app.run_polling()# telegram-bot
Bot  telegram 
