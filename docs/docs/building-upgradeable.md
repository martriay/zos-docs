---
id: building-upgradeable
title: Building upgradeable applications
sidebar_label: Building upgradeable apps
---

After installing ZeppelinOS and setting up a `zos` project as described in the [Setup](setup.md) guide, you are ready to create an upgradeable application.

You can start by writing the contracts as you would normally do, but replacing constructors with
`initialize` functions using the modifier from `zos-lib`. To install the lib:

    npm install zos-lib

A simple contract would look like this:

    import "zos-lib/contracts/migrations/Migratable.sol";

    contract MyContract is Migratable {
      uint256 public x;

      function initialize(uint256 _x) isInitializer("MyContract", "0") public {
        x = _x;
      }
    }

Compile it with:

    npx truffle compile

## Initial Deployment

Register all the contract implementations of the first version of your
project running:

    zos add <contract_name_1>
    zos add <contract_name_2>
    ...
    zos add <contract_name_n>

In our example, run:

    zos add MyContract

Push your application to the desired network by running:

    zos push --network <network>

which will create a `package.zos.<network>.json` file with all the information specific to the chosen network. If you are working in a local development network like [Ganache](http://truffleframework.com/ganache/), you will need to [configure](http://truffleframework.com/docs/getting_started/project#alternative-migrating-with-ganache) your `truffle.js` file before running this command.

Now you can create an upgradeable version of each of your contracts with:

    zos create <contract_name_1> --network <network>
    zos create <contract_name_2> --network <network>
    ...
    zos create <contract_name_n> --network <network>

This command takes an optional `--init` flag to call an initialization/migration
function after you create the contract.

In our simple example we want to call the initialize function, with some value
(_e.g._: 42) thus:

    zos create MyContract --init initialize --args 42 --network development

After these simple steps, your upgradeable application is now on-chain!

## Upgrading your contracts

If, at a later stage, you want to upgrade your smart contracts' code in order to fix a bug or add a new feature, you can do it seamlessly using ZeppelinOS. 

First, make the desired changes to your contracts, recompile them, and push them to the network:

    npx truffle compile
    zos push --network <network>

Then, upgrade the already deployed contracts:

    zos upgrade <contract_name_1> --network <network>
    zos upgrade <contract_name_2> --network <network>
    ...
    zos upgrade <contract_name_n> --network <network>

In our simple example:

    zos upgrade MyContract --network development

The address of the upgraded contracts is the same as before, but the code has been changed to the new
version.

_Voilà!_ You have deployed and upgraded an application using ZeppelinOS. 

If you want to use the ZeppelinOS standard libraries, please follow our [Using the stdlib in your app](building-stdlib.md) guide. You can also check the API reference with [all the available commands of zos](climain.md).
