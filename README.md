class User:
    def __init__(self, username):
        self.username = username
        self.balance = 0.0

    def add_balance(self, amount):
        if amount <= 0:
            return "Amount must be positive."
        self.balance += amount
        return f"${amount:.2f} added. New balance: ${self.balance:.2f}"

    def make_payment(self, amount, recipient):
        if amount <= 0:
            return "Payment amount must be positive."
        if self.balance < amount:
            return "Insufficient funds."
        self.balance -= amount
        recipient.balance += amount
        return f"Payment of ${amount:.2f} sent to {recipient.username}. New balance: ${self.balance:.2f}"

    def check_balance(self):
        return f"{self.username}'s balance: ${self.balance:.2f}"


# --- Main System Logic ---
users = {}

def register_user(username):
    if username in users:
        return "Username already exists."
    users[username] = User(username)
    return f"User '{username}' registered successfully."

def find_user(username):
    return users.get(username, None)

def main():
    print("Welcome to SimplePay!\n")

    while True:
        print("\nOptions:")
        print("1. Register")
        print("2. Add Balance")
        print("3. Make Payment")
        print("4. Check Balance")
        print("5. Exit")

        choice = input("Choose an option: ").strip()

        if choice == "1":
            name = input("Enter username to register: ").strip()
            print(register_user(name))

        elif choice == "2":
            name = input("Username: ").strip()
            user = find_user(name)
            if user:
                try:
                    amt = float(input("Amount to add: "))
                    print(user.add_balance(amt))
                except ValueError:
                    print("Invalid amount.")
            else:
                print("User not found.")

        elif choice == "3":
            sender_name = input("Sender username: ").strip()
            recipient_name = input("Recipient username: ").strip()
            try:
                amount = float(input("Amount to send: "))
                sender = find_user(sender_name)
                recipient = find_user(recipient_name)

                if not sender or not recipient:
                    print("Sender or recipient not found.")
                else:
                    print(sender.make_payment(amount, recipient))
            except ValueError:
                print("Invalid amount.")

        elif choice == "4":
            name = input("Username: ").strip()
            user = find_user(name)
            if user:
                print(user.check_balance())
            else:
                print("User not found.")

        elif choice == "5":
            print("Thanks for using SimplePay. Goodbye!")
            break
        else:
            print("Invalid option. Try again.")

if __name__ == "__main__":
    main()
