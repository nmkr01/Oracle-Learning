📅 Day 2 Agenda (2.5–3 Hours)
        Duration	Topic
        20 min	What is a Journal?
        30 min	Journal Header & Journal Lines
        25 min	Debit & Credit
        20 min	Journal Sources
        20 min	Journal Categories
        20 min	Journal Status
        20 min	Posting Process
        20 min	Real Business Example
        20 min	Oracle Fusion Hands-on
        15 min	REST APIs
        15 min	Assignment
        15 min	Quiz
------

Part 1 – What is a Journal?

    Imagine you purchased a laptop.
    Should Oracle simply reduce your cash?
    No.

    Accounting follows Double Entry Bookkeeping.
    Every transaction must have:
        One Debit
        One Credit

    Oracle stores this accounting entry as a Journal.

Definition : A Journal is an accounting document that records every financial transaction using Debit and Credit entries.

Think of it as an accounting voucher.
Real Example
-----
Your company buys a laptop worth ₹50,000.
Oracle creates:
    Account	Debit	Credit
    Computer Expense	₹50,000	
    Cash		₹50,000

This is called a Journal.
------------

Part 2 – Journal Structure

Every Journal has two parts.
Journal Header
    Contains general information.
        Example:
        ---
        Journal Name
        Accounting Date
        Ledger
        Period
        Journal Source
        Journal Category
        Currency
        Status

Think of it as the cover page.

Journal Lines
    Contains the accounting entries.
        Example:

        Account	Debit	Credit
        Expense	50,000	
        Cash		50,000
        Easy Analogy

Imagine writing a cheque.
The cheque itself is the Journal Header.
The amount, account, and details are the Journal Lines.
------------

Part 3 – Debit and Credit

This confuses many beginners.
Don't memorize it—understand it.

Example:

    Company buys office chairs.
    Payment made immediately.
        Accounting becomes:
        Account	Debit	Credit
        Furniture Expense	₹1,00,000	
        Bank		₹1,00,000

    Why?
    Because:
    Expense increases → Debit
    Bank decreases → Credit

    Golden Rule (Simple)
    Type	    Increase	Decrease
    --------------------------------
    Asset	    Debit	    Credit
    Expense	    Debit	    Credit
    Liability	Credit	    Debit
    Revenue	    Credit	    Debit
    Equity	    Credit	    Debit

We'll revisit this many times, so don't worry if it doesn't stick immediately.
------------

Part 4 – Journal Source

    Oracle wants to know:
    "Who created this Journal?"
    That's the Source.

        Examples:
        ----
        Source	    Created By
        Payables	AP Module
        Receivables	AR Module
        Purchasing	Procurement
        Inventory	Inventory
        Manual	    User
        Spreadsheet	Excel Upload

        Example
        ----
        Supplier Invoice
        ↓
        AP Module
        ↓
        Creates Journal
        ↓
        Source = Payables

Part 5 – Journal Category

    Source tells who created it.
    Category tells why it was created.

    Examples:
    ----
    Category	Meaning
    Purchase	Supplier Purchase
    Payment	Supplier Payment
    Adjustment	Manual Correction
    Accrual	Month End
    Receipt	Customer Receipt

Interview Question

Difference between Source and Category?
Answer:
    Source = Who created the Journal.
    Category = Why the Journal was created.
    Part 6 – Journal Status

Every Journal moves through stages.

    Created
    ↓
    Validated
    ↓
    Approved (if approval enabled)
    ↓
    Posted

Created
Journal exists.

Validated
Oracle checks:
    Accounts exist.
    Period is open.
    Debit = Credit.

Approved
If approval workflow is enabled.

Posted
Journal balances are transferred into the General Ledger.
Only Posted journals affect financial reports.
------------

Part 7 – Posting

Posting means:
    Oracle transfers the Journal balances into the Ledger.
    Before Posting:
    Journal exists.
    After Posting:
    Balance Sheet changes.
    Profit & Loss changes.

Real Business Scenario

A supplier sends an invoice for ₹5,00,000.

    Flow:
        Supplier
            ↓
        Invoice
            ↓
        Accounts Payable
            ↓
        Accounting
            ↓
        Journal Created
            ↓
        Approval
            ↓
        Posting
            ↓
        General Ledger
            ↓
        Financial Reports

This is one of the most common ERP integration flows.
----------

Oracle Fusion Hands-on

    Navigate to:
        General Accounting
    Search:
        Manage Journals
    Open any Journal and identify:
        Journal Name
        Ledger
        Period
        Source
        Category
        Status
        Debit Amount
        Credit Amount

    Then click Lines and identify:
        Account Combination
        Debit
        Credit
---------

REST APIs
    Try exploring (if available in your environment):
    GET /fscmRestApi/resources/latest/journals

    Observe:
        Journal Name
        Ledger
        Status
        Accounting Date
        Period
----------

OIC Integration Scenario

    Imagine an external Payroll system sends salary details.
    Flow:
        Payroll System
            │
            ▼
        Oracle Integration Cloud (OIC)
            │
            ▼
        Transform Payload
            │
            ▼
        Oracle ERP Journal REST API
            │
            ▼
        Journal Created
            │
            ▼
        Posted to GL
    This is a very common integration pattern in Oracle implementations.
--------

Interview Questions
----
    What is a Journal?
    Explain the Journal lifecycle.
    What is Journal Posting?
    Difference between Source and Category?
    Why are Journal Lines required?
    Can Debit and Credit be unequal?
    What validations happen before Posting?
    Which journals affect financial reports?
    How would you create a Journal through OIC?
    Which ERP REST API would you use to create a Journal?

Key Takeaways
----
    Journal         → Records every accounting transaction.
    Journal Header  → General information.
    Journal Lines   → Debit and Credit entries.
    Source          → Who created the Journal.
    Category        → Why the Journal was created.
    Posting         → Updates the General Ledger.

    Only Posted Journals impact financial reports.

