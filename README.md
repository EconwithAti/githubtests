import logging
from telegram import InlineKeyboardButton, InlineKeyboardMarkup, Update, WebAppInfo
from telegram.ext import Application, CommandHandler, CallbackQueryHandler, ContextTypes

# --- CONFIGURATION ---
# Replace this token if you ever regenerate it in BotFather
TOKEN = "8321215708:AAG8POOA4p2-ceOUMKlPtJYLDwaDKOLL3LI"

# Your specific Replit URL
LUDO_URL = "https://ludo-master-1--atakiltitadesse.replit.app"

# Enable logging to see activity in GSM Hosting Console
logging.basicConfig(
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', 
    level=logging.INFO
)
logger = logging.getLogger(__name__)

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Sends the Main Grid Menu"""
    keyboard = [
        # Row 1: Play Games (triggers the selection menu)
        [InlineKeyboardButton("Play Games 🎮", callback_data='choose_game')],
        
        # Row 2: Deposit and Withdraw
        [
            InlineKeyboardButton("Deposit 💰", callback_data='deposit'),
            InlineKeyboardButton("Withdraw 💰", callback_data='withdraw')
        ],
        
        # Row 3: Transfer and Profile
        [
            InlineKeyboardButton("Transfer ↔️", callback_data='transfer'),
            InlineKeyboardButton("My Profile 👤", callback_data='profile')
        ],
        
        # Row 4: Transactions and Balance
        [
            InlineKeyboardButton("Transactions 📜", callback_data='transactions'),
            InlineKeyboardButton("Balance 💰", callback_data='balance')
        ],
        
        # Row 5: Support and Group
        [
            InlineKeyboardButton("Join Group ↗️", url="https://t.me/your_group_link"),
            InlineKeyboardButton("Contact Us", callback_data='contact')
        ]
    ]
    reply_markup = InlineKeyboardMarkup(keyboard)
    
    await update.message.reply_text(
        "<b>Welcome to Ludo Master!</b>\nSelect an option from the menu below:",
        reply_markup=reply_markup,
        parse_mode='HTML'
    )

async def handle_clicks(update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Handles all button interactions"""
    query = update.callback_query
    await query.answer()
    
    # 1. GAME SELECTION MENU
    if query.data == 'choose_game':
        game_keyboard = [
            # This opens the Ludo Game as a Mini App
            [InlineKeyboardButton("🎲 Ludo Master", web_app=WebAppInfo(url=LUDO_URL))],
            [InlineKeyboardButton("🃏 Other Game (Coming Soon)", callback_data='soon')],
            [InlineKeyboardButton("⬅️ Back to Main Menu", callback_data='back_to_main')]
        ]
        await query.message.edit_text(
            "<b>🎮 Game Selection</b>\nChoose a game to start playing:",
            reply_markup=InlineKeyboardMarkup(game_keyboard),
            parse_mode='HTML'
        )

    # 2. BACK TO MAIN MENU
    elif query.data == 'back_to_main':
        # Simply re-calls the main menu layout
        keyboard = [
            [InlineKeyboardButton("Play Games 🎮", callback_data='choose_game')],
            [InlineKeyboardButton("Deposit 💰", callback_data='deposit'), InlineKeyboardButton("Withdraw 💰", callback_data='withdraw')],
            [InlineKeyboardButton("Transfer ↔️", callback_data='transfer'), InlineKeyboardButton("My Profile 👤", callback_data='profile')],
            [InlineKeyboardButton("Transactions 📜", callback_data='transactions'), InlineKeyboardButton("Balance 💰", callback_data='balance')],
            [InlineKeyboardButton("Join Group ↗️", url="https://t.me/your_group_link"), InlineKeyboardButton("Contact Us", callback_data='contact')]
        ]
        await query.message.edit_text(
            "<b>Main Menu</b>\nSelect an option to continue:",
            reply_markup=InlineKeyboardMarkup(keyboard),
            parse_mode='HTML'
        )

    # 3. PLACEHOLDER RESPONSES FOR OTHER BUTTONS
    elif query.data == 'profile':
        await query.message.reply_text(f"👤 <b>Your Profile</b>\nName: {query.from_user.first_name}\nID: <code>{query.from_user.id}</code>", parse_mode='HTML')
    
    elif query.data == 'balance':
        await query.message.reply_text("💰 <b>Your Balance</b>\nCurrent: 0.00 ETB", parse_mode='HTML')

    elif query.data in ['deposit', 'withdraw', 'transfer', 'transactions', 'contact']:
        await query.message.reply_text(f"This feature (<b>{query.data}</b>) is coming soon!", parse_mode='HTML')

if __name__ == '__main__':
    # Initialize the Bot
    app = Application.builder().token(TOKEN).build()
    
    # Add handlers for /start and button clicks
    app.add_handler(CommandHandler("start", start))
    app.add_handler(CallbackQueryHandler(handle_clicks))
    
    print("Bot is LIVE. Send /start to @Atigame_Ludo_Bot")
    app.run_polling(drop_pending_updates=True)
