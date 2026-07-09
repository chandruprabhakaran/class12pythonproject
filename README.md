from tkinter import *
from tkinter import ttk, messagebox
import matplotlib.pyplot as plt

expenses = []

def add_expense():
    date = date_entry.get()
    amount = amount_entry.get()
    category = category_var.get()

    if date == "" or amount == "" or category == "":
        messagebox.showerror("Error", "Fill all fields")
        return

    try:
        amount = float(amount)
    except:
        messagebox.showerror("Error", "Enter valid amount")
        return

    expenses.append([date, amount, category])

    tree.insert("", END, values=(date, amount, category))

    date_entry.delete(0, END)
    amount_entry.delete(0, END)
    category_var.set("")

def show_category():
    total = {}

    for item in expenses:
        category = item[2]
        amount = item[1]

        if category in total:
            total[category] += amount
        else:
            total[category] = amount

    result = ""

    for cat in total:
        result += f"{cat} : ₹{total[cat]}\n"

    messagebox.showinfo("Category Total", result)

def save_to_file(date, amount, category):
    file = open("expenses.txt", "a")
    file.write(f"{date}\t₹{amount}\t{category}\n")
    file.close()

def pie_chart():

    if len(expenses) == 0:
        messagebox.showerror("Error", "No Expense Data")
        return

    total = {}

    for item in expenses:
        category = item[2]
        amount = item[1]

        if category in total:
            total[category] += amount
        else:
            total[category] = amount

    labels = list(total.keys())
    values = list(total.values())

    plt.figure(figsize=(6,6))
    plt.pie(values, labels=labels, autopct="%1.1f%%", startangle=90)
    plt.title("Expense by Category")
    plt.show()

root = Tk()
root.title("Expense Tracker")
root.geometry("700x500")

Label(root, text="Date").grid(row=0, column=0, padx=10, pady=10)

date_entry = Entry(root)
date_entry.grid(row=0, column=1)

Label(root, text="Amount").grid(row=1, column=0)

amount_entry = Entry(root)
amount_entry.grid(row=1, column=1)

Label(root, text="Category").grid(row=2, column=0)

category_var = StringVar()

category_box = ttk.Combobox(root, textvariable=category_var)
category_box["values"] = (
    "Food",
    "Travel",
    "Shopping",
    "Bills",
    "Salary",
    "Office",
    "Other"
)
category_box.grid(row=2, column=1)

Button(root, text="Add Expense", command=add_expense).grid(row=3, column=0, pady=10)

Button(root, text="Show Category Total", command=show_category).grid(row=3, column=1)

Button(root, text="Show Pie Chart", command=pie_chart).grid(row=3, column=2)

columns = ("Date", "Amount", "Category")

tree = ttk.Treeview(root, columns=columns, show="headings", height=12)

for col in columns:
    tree.heading(col, text=col)
    tree.column(col, width=180)

tree.grid(row=4, column=0, columnspan=3, padx=10, pady=20)

root.mainloop()
