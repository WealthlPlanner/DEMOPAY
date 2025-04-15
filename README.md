from flask import Flask, request, render_template_string

app = Flask(__name__)
users = {}

class User:
    def __init__(self, username):
        self.username = username
        self.balance = 0.0

    def add_balance(self, amount):
        self.balance += amount

    def make_payment(self, amount, recipient):
        if self.balance < amount:
            return False
        self.balance -= amount
        recipient.balance += amount
        return True

    def check_balance(self):
        return self.balance

@app.route("/", methods=["GET", "POST"])
def home():
    message = ""
    if request.method == "POST":
        username = request.form.get("username")
        action = request.form.get("action")
        amount = request.form.get("amount", type=float)
        recipient = request.form.get("recipient")

        if action == "register":
            if username in users:
                message = "User already exists!"
            else:
                users[username] = User(username)
                message = f"User '{username}' registered."
        elif username not in users:
            message = "User not found."
        else:
            user = users[username]
            if action == "add":
                user.add_balance(amount)
                message = f"${amount:.2f} added. New balance: ${user.balance:.2f}"
            elif action == "pay":
                if recipient not in users:
                    message = "Recipient not found."
                elif user.make_payment(amount, users[recipient]):
                    message = f"Paid ${amount:.2f} to {recipient}."
                else:
                    message = "Insufficient funds."
            elif action == "check":
                message = f"{username}'s balance: ${user.check_balance():.2f}"

    return render_template_string("""
        <h2>SimplePay Web</h2>
        <form method="post">
            <input name="username" placeholder="Your username"><br>
            <input name="amount" placeholder="Amount (if needed)" type="number" step="0.01"><br>
            <input name="recipient" placeholder="Recipient (for payments)"><br>
            <button name="action" value="register">Register</button>
            <button name="action" value="add">Add Balance</button>
            <button name="action" value="pay">Make Payment</button>
            <button name="action" value="check">Check Balance</button>
        </form>
        <p>{{ message }}</p>
    """, message=message)

if __name__ == "__main__":
    app.run(debug=True)
