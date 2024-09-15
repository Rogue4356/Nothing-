import tkinter as tk
from tkinter import simpledialog, messagebox
import json

class AccountManagerApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Account Manager")

        self.username = ""
        self.password = ""

        self.create_login_screen()

    def create_login_screen(self):
        self.login_frame = tk.Frame(self.root)
        self.login_frame.pack(pady=20)

        self.username_label = tk.Label(self.login_frame, text="Username:")
        self.username_label.pack()
        self.username_entry = tk.Entry(self.login_frame)
        self.username_entry.pack()

        self.password_label = tk.Label(self.login_frame, text="Password:")
        self.password_label.pack()
        self.password_entry = tk.Entry(self.login_frame, show='*')
        self.password_entry.pack()

        self.login_button = tk.Button(self.login_frame, text="Login", command=self.login)
        self.login_button.pack(pady=10)

    def login(self):
        username = self.username_entry.get()
        password = self.password_entry.get()
        # Basic authentication (replace with your own authentication logic)
        if username == "user" and password == "pass":
            self.username = username
            self.password = password
            self.login_frame.pack_forget()
            self.create_main_screen()
        else:
            messagebox.showerror("Login Failed", "Invalid username or password.")

    def create_main_screen(self):
        self.main_frame = tk.Frame(self.root)
        self.main_frame.pack(pady=20)

        self.label = tk.Label(self.main_frame, text="Account Manager")
        self.label.pack(pady=10)

        self.search_label = tk.Label(self.main_frame, text="Search:")
        self.search_label.pack(pady=5)

        self.search_entry = tk.Entry(self.main_frame)
        self.search_entry.pack(pady=5)
        self.search_entry.bind('<KeyRelease>', self.search_accounts)

        self.account_listbox = tk.Listbox(self.main_frame)
        self.account_listbox.pack(pady=10)

        self.add_button = tk.Button(self.main_frame, text="Add Account", command=self.add_account)
        self.add_button.pack(side=tk.LEFT, padx=5)

        self.edit_button = tk.Button(self.main_frame, text="Edit Account", command=self.edit_account)
        self.edit_button.pack(side=tk.LEFT, padx=5)

        self.switch_button = tk.Button(self.main_frame, text="Switch Account", command=self.switch_account)
        self.switch_button.pack(side=tk.LEFT, padx=5)

        self.remove_button = tk.Button(self.main_frame, text="Remove Account", command=self.remove_account)
        self.remove_button.pack(side=tk.LEFT, padx=5)

        self.load_accounts()

    def add_account(self):
        account_name = simpledialog.askstring("Input", "Enter account name:")
        if account_name:
            self.account_listbox.insert(tk.END, account_name)
            self.save_accounts()

    def edit_account(self):
        selected = self.account_listbox.curselection()
        if selected:
            old_name = self.account_listbox.get(selected[0])
            new_name = simpledialog.askstring("Edit Account", f"Edit account name (current: {old_name}):")
            if new_name:
                self.account_listbox.delete(selected[0])
                self.account_listbox.insert(selected[0], new_name)
                self.save_accounts()
        else:
            messagebox.showwarning("No Selection", "Please select an account to edit.")

    def switch_account(self):
        selected = self.account_listbox.curselection()
        if selected:
            account_name = self.account_listbox.get(selected[0])
            messagebox.showinfo("Account Switched", f"Switched to account: {account_name}")
        else:
            messagebox.showwarning("No Selection", "Please select an account to switch.")

    def remove_account(self):
        selected = self.account_listbox.curselection()
        if selected:
            if messagebox.askyesno("Confirm", "Are you sure you want to remove this account?"):
                self.account_listbox.delete(selected[0])
                self.save_accounts()
        else:
            messagebox.showwarning("No Selection", "Please select an account to remove.")

    def save_accounts(self):
        try:
            accounts = list(self.account_listbox.get(0, tk.END))
            with open('accounts.json', 'w') as file:
                json.dump(accounts, file)
        except Exception as e:
            messagebox.showerror("Error", f"An error occurred while saving accounts: {e}")

    def load_accounts(self):
        try:
            with open('accounts.json', 'r') as file:
                accounts = json.load(file)
                for account in accounts:
                    self.account_listbox.insert(tk.END, account)
        except FileNotFoundError:
            pass  # No file to load; start with an empty list
        except Exception as e:
            messagebox.showerror("Error", f"An error occurred while loading accounts: {e}")

    def search_accounts(self, event):
        query = self.search_entry.get().lower()
        self.account_listbox.delete(0, tk.END)
        for account in self.all_accounts:
            if query in account.lower():
                self.account_listbox.insert(tk.END, account)

if __name__ == "__main__":
    root = tk.Tk()
    app = AccountManagerApp(root)
    root.mainloop()
