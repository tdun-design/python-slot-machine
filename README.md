# Python Slot Machine

## Description
A Python-based command-line slot machine game that allows users to deposit money,
place bets on multiple lines, spin the machine, and calculate winnings based on
matching symbols.

## Features
- User deposit and balance tracking
- Configurable betting limits
- Multiple betting lines
- Randomized slot spins
- Winnings calculation based on symbol values
- Input validation for all user entries

## Technologies Used
- Python
- Random module
- Lists and dictionaries

## How to Run
1. Clone the repository
2. Run the program using:
```bash
python slot_machine.py




import random

# ---------------------------------------------
# Slot Machine Program
# This program simulates a simple 3x3 slot machine.
# The user deposits money, chooses how many lines
# to bet on, places a bet per line, spins the slots,
# and either wins or loses money based on matches.
# ---------------------------------------------

# Game configuration constants
# MAX_LINES = maximum number of lines a user can bet on
# MIN_BET / MAX_BET = minimum and maximum bet per line
# ROWS / COLS = dimensions of the slot machine
MAX_LINES = 3
MIN_BET = 1
MAX_BET = 100

ROWS = 3
COLS = 3

# symbol_count controls how frequently each symbol appears
# More frequent symbols are easier to hit but pay less
symbol_count = {
    "A": 2,
    "B": 4,
    "C": 6,
    "D": 8,
}

# symbol_value controls the payout multiplier for each symbol
# Rarer symbols pay more when a full line matches
symbol_value = {
    "A": 5,
    "B": 4,
    "C": 3,
    "D": 2,
}

# ---------------------------------------------
# Checks each bet line for matching symbols
# If all symbols in a row match, winnings are calculated
# Returns the total winnings and the winning line numbers
# ---------------------------------------------
def check_winnings(columns, lines, bet, values):
    winnings = 0
    winnings_lines = []

    for line in range(lines):
        symbol = columns[0][line]
        for column in columns:
            symbol_to_check = column[line]
            if symbol != symbol_to_check:
                break
        else:
            winnings += values[symbol] * bet
            winnings_lines.append(line + 1)

    return winnings, winnings_lines      

# ---------------------------------------------
# Generates a random slot machine spin
# Builds each column using symbol frequency
# Returns a list of columns representing the slots
# ---------------------------------------------
def get_slot_machine_spin(rows, cols, symbols):
    all_symbols = []
    for symbol, symbol_count in symbols.items():
        for _ in range(symbol_count):
            all_symbols.append(symbol)
            
    columns = []
    for _ in range(cols):
        column = []
        current_symbols = all_symbols[:]
        for _ in range(rows):
            value = random.choice(current_symbols)
            current_symbols.remove(value)
            column.append(value)
        columns.append(column)
    
    return columns    

# ---------------------------------------------
# Prints the slot machine in a readable format
# Displays rows horizontally with column separators
# ---------------------------------------------
def print_slot_machine(columns):
    for row in range(len(columns[0])):
        for i, column in enumerate(columns):
            if i != len(columns) - 1:
                print(column[row], end=" | ")
            else:
                print(column[row], end="")
        print()        

# ---------------------------------------------
# Prompts the user to deposit money
# Ensures the amount entered is a valid positive number
# ---------------------------------------------
def deposit():
    while True:
        amount = input("what would you like to deposit $")
        if amount.isdigit():
            amount = int(amount)
            if amount > 0:
                break
            else:
                print("amount must be greater then 0. ")
        else:
            print("Please enter a number. ")
    
    return amount        

# ---------------------------------------------
# Asks the user how many lines they want to bet on
# Validates the input is within the allowed range
# ---------------------------------------------
def get_number_of_lines():
    while True:
        lines = input("Enter the number of lines to bet on (1-" + str(MAX_LINES) + ")? ")
        if lines.isdigit():
            lines = int(lines)
            if 1 <= lines <= MAX_LINES:
                break
            else:
                print("Enter a valid number of lines. ")
        else:
            print("Please enter a number. ")
    
    return lines

# ---------------------------------------------
# Prompts the user for a bet amount per line
# Ensures the bet is within the allowed limits
# ---------------------------------------------
def get_bet():
    while True:
        amount = input("what would you like to bet on each line? $")
        if amount.isdigit():
            amount = int(amount)
            if MIN_BET <= amount <= MAX_BET:
                break
            else:
                print(f"amount must be between ${MIN_BET} - ${MAX_BET}. ")
        else:
            print("Please enter a number. ")
    
    return amount

# ---------------------------------------------
# Handles one full slot machine spin
# Collects bets, spins the machine, prints results,
# and returns the net balance change
# ---------------------------------------------
def spin(balance):
    lines = get_number_of_lines()
    while True:
        bet = get_bet()
        total_bet = bet * lines
        
        if total_bet > balance:
            print(f"You do not have enough to bet that amount, your current balance is: ${balance}")
        else:
            break
    
    print(f"you are betting ${bet} on {lines} lines. Total bet is equal to: ${total_bet}")
    
    slots = get_slot_machine_spin(ROWS, COLS, symbol_count)
    print_slot_machine(slots)

    winnings, winnings_lines = check_winnings(slots, lines, bet, symbol_value)
    print(f"you won {winnings}. ")
    print("you won on lines:", *winnings_lines)

    # Net balance change = winnings minus total bet
    return winnings - total_bet

# ---------------------------------------------
# Main game loop
# Continues until the user chooses to quit
# Tracks and updates the player's balance
# ---------------------------------------------
def main():
    balance = deposit()
    while True:
        print(f"current balance is ${balance}")
        answer = input("press enter to play (q to quit). ")
        if answer == "q":
            break
        balance += spin(balance)
    print(f"you left with ${balance}")

main()
