Beancount VAT
===============================================================================

TODO: [![PyPI - Version](https://img.shields.io/pypi/v/beancount_vat)](https://pypi.org/project/beancount-vat/)
TODO: [![PyPI - Downloads](https://img.shields.io/pypi/dm/beancount_vat)](https://pypi.org/project/beancount-share/)
TODO: [![PyPI - Wheel](https://img.shields.io/pypi/wheel/beancount_vat)](https://pypi.org/project/beancount-share/)
[![License](https://img.shields.io/pypi/l/beancount_vat)](https://choosealicense.com/licenses/agpl-3.0/)
[![Linting](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)

A beancount plugin to calculate the amount of recoverable VAT on purchases based on the gros amount including vat from a mass bank statement import. Inspired and build upon the the beancount_share plugin and of course beancount.

`#vat` plugin uses tag syntax to add info to the transaction:

Usecase: calculate Recoverable VAT based on amount including vat
================================================================


Add a `#vat-21` tag to calculate the recoverable vat for this transaction based on the amount including 21% 
VAT to splits initial the transaction into 2 transactions: 
One for the recoverable () 21% value added tax, one for the Nett price of the transaction.
(if the total amount including 21% vat is 242 EURO, then the base amount excluding VAT was 200 EURO and the Value added tax (VAT) is 200*21% = 42 EURO. 

 - VAT = VALUE INCLUDED VAT * (VAT % / 100+VAT%) 
 - VAT = 242 (21/121) = 42


How to use
-----------------------------------------------------------------------

Import your bank statements (using smart-importer) and then #Manually add a VAT-percentage
Tag your transaction simply with a tag + name, like `#vat-21`:

```
2020-01-01 * "Office Equipment" "Buy office equipment" #vat-21
    Assets:Bank               - 242.00 EUR
    Expenses:Office:Equipment
```

What happens
-----------------------------------------------------------------------

The transaction will get transformed into 2 transactions. 

```
2020-01-01 * "Office Equipment" "Buy office equipment" #vat-21
    Assets:Bank                        - 242.00 EUR
    Expenses:Office:Equipment            200.00 EUR
    Assets:Debitors:RecoverableVat        42.00 EUR ; 200*21% Value added tax = 42 EUR
```


And then of course recover the paid VAT
-----------------------------------------------------------------------

Use the calculated `Assets:Debitors:RecoverableVat` from the report over a 
certain tax period.



Install
===============================================================================

```
pip3 install beancount_vat --user
```

Or copy to path used for python. For example, `$HOME/.local/lib/python3.7/site-packages/beancount_vat/*` would do on Debian. If in doubt, look where `beancount` folder is and copy next to it.








Setup
===============================================================================

> Please read the elaborate version at the [Beancount docs](https://docs.google.com/document/d/1MjSpGoJVdgyg8rhKD9otSKo4iSD2VkSYELMWDBbsBiU/edit).

Add the plugin like this:

```
plugin "beancount_vat.vat" "{}"
```

Done. If you want to use custom configuration (read below), then you put it inside those `{}` brackets.


Configuration
===============================================================================

Note: **Do NOT use double-quotes within the configuration!** The configuration is a Python dict, not a JSON object.

This is the default configuration in full. Providing it equals to providing no configuration at all.

```
plugin "beancount_vat.vat" "{
    'mark_name': 'vat',
    'meta_name': 'VAT', ; or None to disable informative meta generation
    'account_debtors': 'Assets:Debtors:VAT',
    'account_creditors': 'Liabilities:Creditors:VAT',
    'open_date': '1970-01-01', ; or None to disable entry creation
    'quantize': '0.01'
}"
```

Note that `meta_name` and `open_date` may also be set to `None` - former to disable informative meta generation, and latter to disable `open` entry creation. Example:



Development
===============================================================================

Please see Makefile and inline comments.
