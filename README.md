# Salesforce Apex Trigger – Prevent Duplicate Account Names

This repository contains an Apex Trigger example that prevents users from creating duplicate `Account` records based on the `Name` field in Salesforce.

## Objective

To ensure data quality by avoiding the creation of Accounts with the same name using a `before insert` trigger.

## Trigger Description

- **Trigger Name:** `PreventDuplicateAccount`
- **Object:** `Account`
- **Event:** `before insert`
- **Functionality:**  
  - Collects all Account names being inserted  
  - Queries existing Account records with the same names  
  - Adds an error message if a duplicate is found, preventing the record from being saved

## Code

```apex
trigger PreventDuplicateAccount on Account (before insert) {
    Set<String> accNames = new Set<String>();
    for(Account acc : Trigger.new) {
        accNames.add(acc.Name);
    }

    Map<String, Account> existingAccs = new Map<String, Account>();
    for(Account acc : [SELECT Name FROM Account WHERE Name IN :accNames]) {
        existingAccs.put(acc.Name, acc);
    }

    for(Account acc : Trigger.new) {
        if(existingAccs.containsKey(acc.Name)) {
            acc.addError('An Account with this name already exists.');
        }
    }
}
