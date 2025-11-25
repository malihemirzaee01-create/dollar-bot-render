from telegram import Update
from telegram.ext import Application, CommandHandler, ContextTypes
import requests
from bs4 import BeautifulSoup
import os

# توکن خودت رو اینجا بذار (از @BotFather گرفتی)
TOKEN = "7981243567:AAH4fJ9kLmNpQrStUvWxYz_1aBcDeFgHiJk"  # ← عوض کن

def get_dollar_price():
    try:
        url = "https://www.tgju.org/currency"
        headers = {'User-Agent': 'Mozilla/5.0'}
        r = requests.get(url, headers=headers, timeout=10)
        soup = BeautifulSoup(r.text, 'html.parser')
        tag = soup.find("span", {"data-market-row": "price_dollar_rl"})
        if tag:
            price = tag.text.strip().replace(",", "")
            return f"دلار آمریکا: {price} تومان"
        else:
            return "قیمت موقتاً در دسترس نیست"
    except:
        return "خطا در دریافت قیمت"

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("سلام! برای قیمت دلار بنویس /price")

async def price(update: Update, context: ContextTypes.DEFAULT_TYPE):
    msg = await update.message.reply_text("در حال دریافت...")
    await msg.edit_text(get_dollar_price())

# اجرا
app = Application.builder().token(TOKEN).build()
app.add_handler(CommandHandler("start", start))
app.add_handler(CommandHandler("price", price))

print("ربات در حال اجراست...")
app.run_polling()
