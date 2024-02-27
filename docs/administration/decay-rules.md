# Decay rules

Decay rules are used to update automatically indicators' score in order to represent their lifecycle.

## Configuration

Decay rules can be configured in the "Settings > Customization > Decay rule" menu.

There are built-in decay rules that can't be modified and are applied by default to indicators depending on their main observable type.
Decay rules are applied following an order : from highest to lowest (lowest being 0).

You can create new decay rules with higher order to apply them along with (or instead of) the built-in rules.

Once you have created a new decay rule, you will be able to view its details, along with a life curve graph showing the score evolution over time.

You will also be able to edit your rule, change all its parameters and order, activate or deactivate it (only activated rules are applied), or delete it.

## Decay Rule Manager

Decay rules are only applied, and indicators' score updated, if decay rule manager is enabled (enabled by default). 