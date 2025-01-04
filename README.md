        tk.Button(frame, text="Show Balance", command=self.show_balance).grid(row=4, column=0, columnspan=2, pady=10)

    def create_history_frame(self, frame):
        self.history_tree = ttk.Treeview(frame, columns=("Date", "Type", "Amount"), show="headings")
        self.history_tree.heading("Date", text="Date")
        self.history_tree.heading("Type", text="Type")
        self.history_tree.heading("Amount", text="Amount")
        self.history_tree.pack(fill=tk.BOTH, expand=True, padx=5, pady=5)

        tk.Button(frame, text="Show History", command=self.show_transaction_history).pack(pady=10)

    def create_account(self):
        owner = self.owner_var.get().strip()
        account_type = self.account_type_var.get()
        if not owner or not account_type:
            messagebox.showerror("Error", "Owner name and account type are required.")
            return
        account_number = self.bank.create_account(owner, account_type)
        messagebox.showinfo("Account Created", f"Account Number: {account_number}")

    def deposit(self):
        account_number = self.account_number_var.get()
        account = self.bank.get_account(account_number)
        if not account:
            messagebox.showerror("Error", "Account not found.")
            return
        try:
            amount = float(self.amount_var.get())
            if account.deposit(amount):
                messagebox.showinfo("Success", f"Deposited ${amount:.2f}.")
            else:
                messagebox.showerror("Error", "Invalid amount.")
        except ValueError:
            messagebox.showerror("Error", "Enter a valid amount.")

    def withdraw(self):
        account_number = self.account_number_var.get()
        account = self.bank.get_account(account_number)
        if not account:
            messagebox.showerror("Error", "Account not found.")
            return
        try:
            amount = float(self.amount_var.get())
            if account.withdraw(amount):
                messagebox.showinfo("Success", f"Withdrew ${amount:.2f}.")
            else:
                messagebox.showerror("Error", "Insufficient balance.")
        except ValueError:
            messagebox.showerror("Error", "Enter a valid amount.")

    def request_loan(self):
        account_number = self.account_number_var.get()
        account = self.bank.get_account(account_number)
        if not account:
            messagebox.showerror("Error", "Account not found.")
            return
        try:
            amount = float(self.amount_var.get())
            if account.request_loan(amount):
                messagebox.showinfo("Success", f"Loan of ${amount:.2f} granted.")
            else:
                messagebox.showerror("Error", "Invalid loan amount.")
        except ValueError:
            messagebox.showerror("Error", "Enter a valid amount.")

    def repay_loan(self):
        account_number = self.account_number_var.get()
        account = self.bank.get_account(account_number)
        if not account:
            messagebox.showerror("Error", "Account not found.")
            return
        try:
            amount = float(self.amount_var.get())
            if account.repay_loan(amount):
                messagebox.showinfo("Success", f"Repaid ${amount:.2f} of the loan.")
            else:
                messagebox.showerror("Error", "Insufficient funds.")
        except ValueError:
            messagebox.showerror("Error", "Enter a valid amount.")

    def show_balance(self):
        account_number = self.account_number_var.get()
        account = self.bank.get_account(account_number)
        if not account:
            messagebox.showerror("Error", "Account not found.")
            return
        balance = account.get_balance()
        loan_balance = account.get_loan_balance()
        messagebox.showinfo("Balance", f"Current Balance: ${balance:.2f}\nLoan Balance: ${loan_balance:.2f}")

    def show_transaction_history(self):
        account_number = self.account_number_var.get()
        account = self.bank.get_account(account_number)
        if not account:
            messagebox.showerror("Error", "Account not found.")
            return
        for row in self.history_tree.get_children():
            self.history_tree.delete(row)
        for transaction in account.get_transaction_history():
            self.history_tree.insert("", tk.END, values=(transaction.timestamp, transaction.transaction_type, transaction.amount))

    def on_closing(self):
        self.bank.save_data()
        self.root.destroy()


root = tk.Tk()
app = BankingApp(root)
root.mainloop()
