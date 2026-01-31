# A professional accounting script to manage digital ledgers with automatic CSV data persistence.
import csv
import os
from datetime import datetime

class LedgerFlow:
    def __init__(self, filename="accounts.csv"):
        self.filename = filename
        if not os.path.exists(self.filename):
            with open(self.filename, 'w', newline='') as f:
                writer = csv.writer(f)
                writer.writerow(["Date", "Description", "Category", "Debit", "Credit", "Balance"])

    def record_entry(self, desc, category, amount, entry_type):
        """entry_type: 'credit' (income) or 'debit' (expense)"""
        balance = self._get_current_balance()
        debit = amount if entry_type == "debit" else 0
        credit = amount if entry_type == "credit" else 0
        new_balance = balance + credit - debit
        
        with open(self.filename, 'a', newline='') as f:
            writer = csv.writer(f)
            writer.writerow([datetime.now().strftime("%Y-%m-%d"), desc, category, debit, credit, new_balance])
        print(f"✅ Record Added: {desc} | Balance: {new_balance}")

    def _get_current_balance(self):
        try:
            with open(self.filename, 'r') as f:
                lines = list(csv.reader(f))
                return float(lines[-1][-1]) if len(lines) > 1 else 0.0
        except (IndexError, ValueError): return 0.0

# Quick Usage
db = LedgerFlow()
db.record_entry("Client Payment", "Freelance", 2500, "credit")
db.record_entry("Office Rent", "Fixed Cost", 800, "debit")
