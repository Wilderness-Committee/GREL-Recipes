# GREL Recipes

This repository is a collection of GREL recipes used for data cleaning.


### Canadian Postal Code Formatting

Will insert a space between the two groups of three characters. Matches two groups of three word characters without a space + Matches two groups of three word characters with a space between them.

    value.replace(/(\w{3})(\w{3})|(\w{3}) (\w{3})/, "$1$3 $2$4")