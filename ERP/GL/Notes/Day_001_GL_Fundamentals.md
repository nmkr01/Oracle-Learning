Part 1 — What is an ERP?

Imagine a manufacturing company.
    It has different departments:
        Sales
        Procurement
        Inventory
        Finance
        HR
        Payroll
        Projects
        Manufacturing

Without ERP:
Every department has its own software.
    Finance → Software A
    HR → Software B
    Inventory → Software C
    Sales → Software D

Nobody shares data.
Problems:
    ❌ Duplicate Data
    ❌ Manual Work
    ❌ No Real-time Reports
    ❌ Difficult Audits

Oracle ERP solves this.

                 Oracle Fusion ERP
                      │
 ┌─────────────┬─────────────┬──────────────┐
 Finance      Procurement     SCM
 HR           Projects        Sales
 Inventory    Manufacturing   Payroll

Everything stores data in ONE system.

=====================================================-------------------------------============================================

Part 2 — Oracle Fusion ERP Modules

Today we'll only understand the overview.
    Financials
    Procurement
    Projects
    SCM
    Manufacturing
    HCM
    CX
    EPM

Since you're an OIC Developer, these are the modules you'll most often integrate with:
    ✅ GL
    ✅ AP
    ✅ AR
    ✅ Procurement
    ✅ Projects

=====================================================-------------------------------============================================

Part 3 — General Ledger (GL)

This is the heart of Oracle Financials.
Every accounting transaction eventually reaches the General Ledger.

Example:
    Supplier Invoice
    ↓
    Accounts Payable
    ↓
    Accounting
    ↓
    General Ledger
    ↓
    Financial Reports

Think of GL as the company's final accounting book.
Everything ends here.

Business Example
    Company buys laptops worth ₹5,00,000.
    Procurement purchases them.
    Accounts Payable receives the invoice.
    Finance pays the supplier.

Finally,
    GL records
        Expense Account      Dr
        Cash Account         Cr

This becomes part of the Balance Sheet and Profit & Loss.

1.What is a Ledger?
    This is one of the most frequently asked interview questions.
    A Ledger is the primary accounting record for a company.

    It defines:
        Currency
        Accounting Calendar
        Chart of Accounts
        Accounting Method

    Example:

    ABC India Ledger
    Currency = INR
    Calendar = Jan-Dec
    COA = India COA
    Accounting Method = Accrual

Everything is recorded inside a Ledger.

Real Client Example
------
Suppose Tata Motors has companies in:
    India
    USA
    Germany

    Each country has different:
        Currency
        Tax Rules
        Accounting Calendar
        Chart of Accounts

    Therefore,
    they create separate Ledgers.
        India Ledger
        USA Ledger
        Germany Ledger

2.What is a Legal Entity?
    A Legal Entity is a legally registered company.
    Example:
        Infosys Ltd
        TCS Ltd
        Reliance Industries
        Apple India Pvt Ltd

    These companies file taxes independently.
    A Legal Entity can own assets, hire employees, and enter contracts.

3.What is a Business Unit?
    Business Unit represents the operational side of the organization.
    Example:
        Amazon India
            Finance BU
            Procurement BU
            Sales BU
            Manufacturing BU

    Business Units perform transactions.
    Legal Entities own them.

Easy Way to Remember
------
    Imagine a shopping mall.
    The mall owner is the Legal Entity.
    Each shop inside the mall is a Business Unit.
    The mall maintains one or more accounting books (Ledgers) to record financial transactions.

Relationship
-----
    Legal Entity
    ↓
    Business Unit
    ↓
    Transactions
    ↓
    General Ledger
    ↓
    Reports

4.What is a Chart of Accounts (COA)?
    A Chart of Accounts is the list of all accounts used for accounting.

    Example:
        1000 Cash
        1100 Bank
        2000 Supplier
        3000 Revenue
        4000 Expense
        5000 Assets

    When a Journal is created, the account comes from the COA.

    Real Example
    ----
    When you buy a laptop:
        Expense Account
        ↓
        Computer Expense
        ↓
        5000

    When you pay cash:
        Cash
        ↓
        1000
    
5.What is an Accounting Calendar?
    An Accounting Calendar tells Oracle:
        When the fiscal year starts.
        When each accounting period opens and closes.

    Example:
        Jan
        Feb
        Mar
        ...
        Dec

    Each month becomes an accounting period.

    At month-end:
        Finance closes the period.
        No more transactions can be posted to that period.

Complete Relationship Diagram
----------------
                        Enterprise
                            │
                    Legal Entity
                            │
                    Business Unit
                            │
                    General Ledger
                            │
            ┌─────────────┬─────────────┐
        Chart of Accounts   Calendar
                            │
                        Journals
                            │
                        Financial Reports
---------

Hands-on Exercise (Today's Lab)

Open your Fusion instance and explore:

    Setup and Maintenance

    Search:

    Manage Primary Ledgers

    Open the primary ledger and note:
        Ledger Name
        Currency
        Calendar
        Chart of Accounts
        Accounting Method

    Next, search:
    Manage Business Units
    Record:
        Business Unit Name
        Associated Legal Entity

    Finally, search:
    Manage Legal Entity
    Record:
        Legal Entity Name
        Registration details (if visible)
        Associated Ledger (if visible)

REST API Exploration
Open Postman
-----
    Try these endpoints if your environment exposes them:

        GET /fscmRestApi/resources/latest/ledgers
        GET /fscmRestApi/resources/latest/businessUnits
        GET /fscmRestApi/resources/latest/legalEntities

    Look at the JSON response and identify how these entities are related.

Interview Questions
-----
    What is Oracle Fusion ERP?
    Why is ERP required?
    What is General Ledger?
    What is a Ledger?
    Difference between Ledger and Legal Entity?
    Difference between Business Unit and Legal Entity?
    What is a Chart of Accounts?
    What is an Accounting Calendar?
    Can one Legal Entity have multiple Business Units?
    Why are Ledgers required?

Quiz (Don't Search — Answer from Understanding)
    Reply with your answers to these five questions:
        1.A company has offices in India and the US. Would you create one ledger or two? Why?
        2.Can two Business Units belong to the same Legal Entity?
        3.What happens if an accounting period is closed?
        4.Where does every AP invoice eventually end up?
        5.Why is a Chart of Accounts important?

⭐ Easy Revision Points
        ERP → Integrates all business departments into one system.
        General Ledger (GL) → Final accounting book of the company.
        Ledger → Defines Currency, Calendar, COA, and Accounting Method.
        Legal Entity → A legally registered company.
        Business Unit → Performs daily business transactions.
        Chart of Accounts (COA) → List of all accounting accounts.
        Accounting Calendar → Defines accounting periods (Jan, Feb, Mar, etc.).

Overall Flow
-----
Enterprise -> Legal Entity -> Business Unit -> SubLedger (AP,AR,Procurement) -> Accounting -> General Ledger -> Financial Report