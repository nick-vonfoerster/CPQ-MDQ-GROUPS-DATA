# Salesforce DX Project: Next Steps

CPQ Groups Uplift Package

OVERVIEW
This package extends Salesforce CPQ by enabling full Multi-Dimensional Quoting (MDQ) functionality using native Quote Line Groups. It replaces the legacy MDQ model with a more scalable and flexible solution that leverages the power of Quote Line Groups to manage segmented pricing, term-based quoting, and dynamic product structures.

Why Quote Line Groups? Traditional MDQ in Salesforce CPQ is limited by rigid segment structures, UI performance issues, and lack of extensibility—particularly in complex or high-volume quoting environments. Quote Line Groups overcome these challenges by offering:
* Scalability: Groups support large volumes of quote lines and segments without degrading performance or requiring custom objects for each term or year.
* Flexibility: Users can dynamically add, remove, and reorder segments as needed, with more intuitive grouping and labeling.
* Maintainability: Administrators can configure and extend group behavior using standard CPQ tools (Price Rules, Product Rules, and custom fields), reducing reliance on Apex or complex workarounds.
* Improved UX: Groups provide a clearer and more maintainable quote structure, aligning better with customer expectations and sales workflows.
* Simplified Integration: Group-based quoting aligns more closely with billing, contracts, and order management systems, making downstream processes more consistent and easier to integrate.
With this uplift package, organizations can replicate MDQ functionality within the more modern and robust Quote Line Group framework—paving the way for faster quoting, better governance, and long-term CPQ scalability.

Setup VS Code/Git: https://neuraflash.atlassian.net/wiki/spaces/NTech/pages/2623078401/How+to+Setup+VS+Code 

Developer setup (manual)

* merge/rebase from managed-integration to your dev branch
* pull your dev branch (remote -> local)
* Setup project-scratch-def.json file - Example below (Need multi-currency enabled)
====================================================================

{
  "orgName": “your company",
  "edition": "Developer",
  "features": ["EnableSetPasswordInApi", "MultiCurrency"],
  "settings": {
  "currencySettings":{
      "enableMultiCurrency": true
    },
  "lightningExperienceSettings": {
      "enableS1DesktopEnabled": true
    },
    "mobileSettings": {
      "enableS1EncryptedStoragePref2": false
    }
  }
}

====================================================================
* Create your DE Scratch Org (SO)
    1. Command + Shift + P
    2. Create Default Scratch Org
    3. Select project-scratch-def.json file
    4. Enter in Alias of org
    5. Enter in number of days before expiration
* Enable Field History Tracking on the Account and Opportunity objects
* Install Managed Packages (see below)
* Post Install Config Setup

Installing Managed Packages in your Scratch Org (SO)
* sfdx force:package:install --package [package id] -w [expiration # of days] -o [org alias]
    * Salesforce CPQ Package ID: 04t6T000000t6RQQAY (Package version Summer '25 v256.0)
        * If another version needs to be installed navigate to https://install.steelbrick.com/ > Right click on the Production or Sandbox links and copy the link > paste the link into a notepad and retrieve the ID from the URL
* QLGAmendRenew Package ID: 04tHs000000G0DwIAK
* Navigate Everywhere Package ID: 04t5G000003rUhrQAE

Post Install Configuration
        
        * Post Install, you must Authorize new calculation service. This step requires logging and performing the following step: Setup > Installed Packages > Salesforce CPQ Configure > Pricing and Calculation > Click Authorize new calculation service
            * If “Use Integration User for Calculations” is true, uncheck the setting > Save > then authorize calculation service
        * Further CPQ Configurations after authorizing calculation service
            * Line Editor:
                * Visualize Product Hierarchy = true
                * Enable Multi-Line Delete = true
            * Pricing and Calculation
                * Calculate Immediately = true
                * Use Legacy Calculator = false
            * Subscription and Renewals
                * Renewal Model = Contract Based
                * Subscription Prorate Precision = Monthly + Daily
                * Subscription Term Unit = Month
                * Include Net-new Products in Maintenance
                * PoT Renewals (Contracting from Orders)
                * Legacy Amend/Renew Service = false
            * Order
                * Allow Multiple Orders = true
                * Default Order Start Date = Quote Start Date
            * Additional Settings
                * Triggers Disabled = true 
                    * Triggers need to be disabled until the metadata/data is pushed. The triggers will be enabled again post push

Assign the following permission sets to your default org User.
* sfdx force:user:permset:assign --permsetname SBQQ__SteelBrickCPQAdmin
* sfdx force:user:permset:assign --permsetname SBQQ__SteelBrickCPQUser

Push Data/Metadata
* PACKAGE.XML: sf project deploy start -x manifest/package.xml -o [org alias]   
* PRICE RULES: sfdx force:data:tree:import -o [org alias] -p "./data/SBQQ__PriceRule__c-SBQQ__PriceCondition__c-SBQQ__PriceAction__c-plan.json" 
* CUSTOM SCRIPT: sfdx force:data:tree:import -o [org alias] -f “./data/SBQQ__CustomScript__c.json”   
    * Post installation of the custom script you will need to navigate to Setup > Installed Packages > Plugins
        * Quote Calculator Plugin: QuoteLine Escalation - KB4
        * QLE Custom Action Plugin: Clone Line Script   

## How Do You Plan to Deploy Your Changes?

Do you want to deploy a set of changes, or create a self-contained application? Choose a [development model](https://developer.salesforce.com/tools/vscode/en/user-guide/development-models).

## Configure Your Salesforce DX Project

The `sfdx-project.json` file contains useful configuration information for your project. See [Salesforce DX Project Configuration](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_ws_config.htm) in the _Salesforce DX Developer Guide_ for details about this file.

## Read All About It

- [Salesforce Extensions Documentation](https://developer.salesforce.com/tools/vscode/)
- [Salesforce CLI Setup Guide](https://developer.salesforce.com/docs/atlas.en-us.sfdx_setup.meta/sfdx_setup/sfdx_setup_intro.htm)
- [Salesforce DX Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_intro.htm)
- [Salesforce CLI Command Reference](https://developer.salesforce.com/docs/atlas.en-us.sfdx_cli_reference.meta/sfdx_cli_reference/cli_reference.htm)
