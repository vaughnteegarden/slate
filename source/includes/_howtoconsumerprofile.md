## Consumer Profile Management


Once you have established a session, you have access to the consumer's records. These include:

* Consumer Profile - Via the `ConsumerProfileManager`. Name, email, and device profile (phone info) for the consumer
* Address Book - Via the `AddressbookManager`. A collection of postal addresses, to be used for ship-to and bill-to purposes.
* Phone Book - Via the `PhonebookManager`. A collection of phone numbers associated with the profile. 
* Favorites - Via the `FavouriteManager`. Reserved for future functionality, "Favorites" are collections of unique devices that can be topped up.  A favorite can represent a mobile phone, a tollway transponder, or other device/account.
* Wallet - Via the `WalletManager`. Wallet lets you store credit card info securely, and lets the consumer maintain the list of cards. There can be multiple cards.

There are no specific flows to consider when managing the customer profile and associated records.

AddressbookManager, FavouriteManager, PhonebookManager and WalletManager support the following CRUD operations: `create`, `update`, `delete`, `getAll`, `get`.

ProfileManager supports only `update` and `get`.