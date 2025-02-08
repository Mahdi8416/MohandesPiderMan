from typing import Final
from datetime import datetime
from telegram import Update
from telegram.ext import Application, CommandHandler, ContextTypes

TOKEN: Final = "7640759690:AAGfkevDxt_9kQuvzg6aVHl02OPZ5A52zNk"
BOT_USERNAME: Final = "@engipiderman_bot"

MY_TELEGRAM_ID = "999077616"

WAITING_FOR_STYLE, WAITING_FOR_RAPPER, WAITING_FOR_SONG = range(3)

user_membership_status = {}
user_state = {}

ho3ein_tracks = {
    "دلخوشی": {
        "file": r"D:\Ho3ein\Songs\Ho3ein-Delkhoshi-(Ft-BigRez).mp3",
        "artist": "Ho3ein, BigRez"
    },
    "دیدگاه": {
        "file": r"D:\Ho3ein\Songs\Ho3ein - Didgah.mp3",
        "artist": "Ho3ein"
    },
    "پر": {
        "file": r"D:\Ho3ein\Songs\Ho3ein Por Ft BigRez.mp3",
        "artist": "Ho3ein, BigRez"
    },
    "حقیقت": {
        "file": r"D:\Ho3ein\Songs\Ho3ein - Haghighat.mp3",
        "artist": "Ho3ein"
    },
    "خط کشیدم دورم": {
        "file": r"D:\Ho3ein\Songs\Ho3ein - Khat Keshidam Doram .mp3",
        "artist": "Ho3ein"
    },
    "خفن": {
        "file": r"D:\Ho3ein\Songs\Ho3ein - Khafan.mp3",
        "artist": "Ho3ein"
    },
    "بمولا": {
        "file": r"D:\Ho3ein\Songs\Ho3ein-Be-mula-(Ft-EpiCure-Masin)-128.mp3",
        "artist": "Ho3ein, EpiCure, Masin"
    },
    "خواب راحت": {
        "file": r"D:\Ho3ein\Songs\Ho3ein Ft Bidad - Khaabe Rahat .mp3",
        "artist": "Ho3ein, Bidad"
    },
    "شبا": {
        "file": r"D:\Ho3ein\Songs\Sadegh - Shaba.flac",
        "artist": "Ho3ein, Sadegh"
    },
    "ردپا": {
        "file": r"D:\Ho3ein\Songs\Ho3ein - Radepa.flac",
        "artist": "Ho3ein, Sadegh"
    },
    "تنهایی": {
        "file": r"D:\Ho3ein\Songs\Ho3ein - Tanhayi.flac",
        "artist": "Ho3ein, Sadegh"
    },
    "سنگین": {
        "file": r"D:\Ho3ein\Songs\Sangin.flac",
        "artist": "Ho3ein, Sadegh"
    },
    "جفت کفش": {
        "file": r"D:\Ho3ein\Songs\Joft kafsh.flac",
        "artist": "Ho3ein, Sadegh"
    },
    "چپ3": {
        "file": r"D:\Ho3ein\Songs\Ho3ein-Chap3.mp3",
        "artist": "Ho3ein"
    },
    "چپ3 ورژن جدید": {
        "file": r"D:\Ho3ein\Songs\Ho3ein-Chap3(New version).mp3",
        "artist": "Ho3ein"
    }
}

rap_artists = ["حصین", "پیشرو", "یاس", "سورنا"]
pop_artists = ["محمد اصفهانی", "محمود کریمی"]
traditional_artists = ["هایده", "معین", "بیژن مرتضوی"]


async def start_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("سلام به ربات مهندسپایدرمن خوش آمدید, لطفا ابتدا عضو ربات شوید. \n/membership")


async def membership_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user_id = update.message.from_user.id

    keyboard = [
        [InlineKeyboardButton("عضویت", callback_data="membership")],
        [InlineKeyboardButton("ترک عضویت", callback_data="leave_membership")],
        [InlineKeyboardButton("🔙", callback_data="back")]
    ]

    await update.message.reply_text("جهت عضویت/ترک عضویت کلیک کنید:", reply_markup=InlineKeyboardMarkup(keyboard))


async def download_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user_id = update.message.from_user.id
    if user_membership_status.get(user_id, False):
        keyboard = [
            [InlineKeyboardButton("رپ", callback_data="rap")],
            [InlineKeyboardButton("پاپ", callback_data="pop")],
            [InlineKeyboardButton("سنتی", callback_data="traditional")],
            [InlineKeyboardButton("🔙", callback_data="back")]
        ]
        await update.message.reply_text("چه سبک موسیقی‌ای می‌خواهی؟", reply_markup=InlineKeyboardMarkup(keyboard))
        user_state[user_id] = WAITING_FOR_STYLE
    else:
        await update.message.reply_text(
            "برای دسترسی به این قابلیت ابتدا باید عضو ربات شوید. دستور /membership را وارد کنید.")


async def handle_callback_query(update: Update, context: ContextTypes.DEFAULT_TYPE):
    query = update.callback_query
    try:
        user_id = query.from_user.id

        if query.data == "membership":
            if user_membership_status.get(user_id, False):
                await query.message.reply_text("شما قبلاً عضو ربات شدید.")
                await query.message.edit_reply_markup(reply_markup=None)
            else:
                user_membership_status[user_id] = True
                await query.message.reply_text("تبریک! شما عضو ربات مهندسپایدرمن شدید.")
                await query.message.edit_reply_markup(reply_markup=None)

                membership_date = datetime.now()
                date_str = membership_date.strftime("%Y-%m-%d")
                day_of_week = membership_date.strftime("%A")
                time_str = membership_date.strftime("%H:%M:%S")

                await context.bot.send_message(MY_TELEGRAM_ID,
                                               f"A new user became a member of the EngineerPiderMan robot.\n"
                                               f"{'-' * 40}\n"
                                               f"Specifications:\n\n"
                                               f"Name: {query.from_user.full_name}\nNumeric ID: {query.from_user.id}\n"
                                               f"Username: @{query.from_user.username}\n"
                                               f"{'-' * 40}\n"
                                               f"Membership Date: {date_str}\n"
                                               f"Membership Day: {day_of_week}\n"
                                               f"Membership Time: {time_str}\n")

        elif query.data == "leave_membership":
            if not user_membership_status.get(user_id, False):
                await query.message.reply_text("تو که عضو ربات نیستی دلقک.")
                await query.message.edit_reply_markup(reply_markup=None)
            else:
                user_membership_status[user_id] = False
                await query.message.reply_text(
                    "شما ربات را ترک کردید. دیگر نمی‌توانید از امکانات مهندسپایدرمن استفاده کنید.")
                await query.message.edit_reply_markup(reply_markup=None)

                membership_date = datetime.now()
                date_str = membership_date.strftime("%Y-%m-%d")
                day_of_week = membership_date.strftime("%A")
                time_str = membership_date.strftime("%H:%M:%S")
                await context.bot.send_message(MY_TELEGRAM_ID,
                                               f"A user has left the EngineerPiderMan robot..\n"
                                               f"{'-' * 40}\n"
                                               f"Specifications:\n\n"
                                               f"Name: {query.from_user.full_name}\nNumeric ID: {query.from_user.id}\n"
                                               f"Username: @{query.from_user.username}\n"
                                               f"{'-' * 40}\n"
                                               f"Membership Date: {date_str}\n"
                                               f"Membership Day: {day_of_week}\n"
                                               f"Membership Time: {time_str}\n")

        elif query.data == "back":
            await query.message.reply_text(
                "شما به منوی اصلی برگشتید. تا زمانی که عضو نشده‌اید، نمی‌توانید از دستور /download استفاده کنید.",
                reply_markup=ReplyKeyboardRemove(selective=False))
            await query.message.edit_reply_markup(reply_markup=None)

        elif query.data in ["rap", "pop", "traditional"]:
            if user_membership_status.get(user_id, False):
                if user_state.get(user_id) == WAITING_FOR_STYLE:
                    if query.data == "rap":
                        artists = rap_artists
                    elif query.data == "pop":
                        artists = pop_artists
                    else:
                        artists = traditional_artists

                    keyboard = [
                        [InlineKeyboardButton(artist, callback_data=f"artist_{artist}") for artist in artists],
                        [InlineKeyboardButton("🔙", callback_data="back")]
                    ]
                    await query.message.reply_text("کدام خواننده را می‌خواهید؟",
                                                   reply_markup=InlineKeyboardMarkup(keyboard))
                    user_state[user_id] = WAITING_FOR_RAPPER
                    await query.message.edit_reply_markup(reply_markup=None)
            else:
                await query.message.reply_text(
                    "برای استفاده از این قابلیت ابتدا باید عضو ربات شوید. دستور /membership را وارد کنید.")

        elif query.data.startswith("artist_"):
            artist = query.data.split("_")[1]
            if artist == "حصین":
                tracks = ho3ein_tracks
            else:
                tracks = {}

            # تنظیم دکمه‌ها به صورت عمودی و در هر سطر دو دکمه
            track_buttons = []
            track_list = list(tracks.keys())
            for i in range(0, len(track_list), 2):
                row = []
                row.append(InlineKeyboardButton(track_list[i], callback_data=f"song_{track_list[i]}"))
                if i + 1 < len(track_list):
                    row.append(InlineKeyboardButton(track_list[i + 1], callback_data=f"song_{track_list[i + 1]}"))
                track_buttons.append(row)

            track_buttons.append([InlineKeyboardButton("🔙", callback_data="back")])

            # send button to choose song
            await query.message.reply_text("کدام آهنگ را می‌خواهید؟", reply_markup=InlineKeyboardMarkup(track_buttons))
            user_state[user_id] = WAITING_FOR_SONG
            # delete last button
            await query.message.edit_reply_markup(reply_markup=None)

        elif query.data.startswith("song_"):
            song = query.data.split("_")[1]
            if song in ho3ein_tracks:
                track_info = ho3ein_tracks[song]
                track_file = track_info["file"]
                artist = track_info["artist"]

                # ارسال پیام "لطفاً صبر کنید"
                await query.message.reply_text("لطفاً صبر کنید، در حال ارسال فایل برای شما...")

                caption = f"Track Name: {song}\nArtists: {artist}\n\n{BOT_USERNAME}"

                # ارسال فایل
                with open(track_file, 'rb') as audio_file:
                    await query.message.reply_audio(audio=audio_file, caption=caption)

                # ارسال پیام "بفرمایید"
                await query.message.reply_text("بفرمایید!")
                await query.message.edit_reply_markup(reply_markup=None)

        await query.answer()
    except Exception as e:
        print(f"Error occurred: {e}")


async def error(update: Update, context: ContextTypes.DEFAULT_TYPE):
    print(f'Update {update} caused error {context.error}')


def main():
    application = Application.builder().token(TOKEN).build()

    application.add_handler(CommandHandler("start", start_command))
    application.add_handler(CommandHandler("membership", membership_command))
    application.add_handler(CommandHandler("download", download_command))

    application.add_handler(CallbackQueryHandler(handle_callback_query))

    application.add_error_handler(error)

    application.run_polling()


if __name__ == '__main__':
    main()
