# OpenRefine Recipes

This repository is a collection of recipes used for data cleaning.


### Canadian Postal Code Formatting

Will insert a space between the two groups of three characters. Matches two groups of three word characters without a space + Matches two groups of three word characters with a space between them.

    value.replace(/(\w{3})(\w{3})|(\w{3}) (\w{3})/, "$1$3 $2$4")


### Canadian Province Code Formatting

Will abbreviate full Canadian province name to abbreviated form.

    province_map = {
        "Alberta": "AB",
        "British Columbia": "BC",
        "Manitoba": "MB",
        "New Brunswick": "NB",
        "Newfoundland and Labrador": "NL",
        "Nova Scotia": "NS",
        "Ontario": "ON",
        "Prince Edward Island": "PE",
        "Quebec": "QC",
        "Saskatchewan": "SK",
        "Northwest Territories": "NT",
        "Nunavut": "NU",
        "Yukon": "YT"
    }
    
    return province_map.get(value, value)  # 'value' is the cell value in OpenRefine


### Street type mappings

Will abbreviate full street/road designation to abbreviated form.

    street_types = {
        "Street": "St",
        "Road": "Rd",
        "Avenue": "Ave",
        "Boulevard": "Blvd",
        "Drive": "Dr",
        "Lane": "Ln",
        "Court": "Ct",
        "Green": "Grn",
        "Place": "Pl",
        "Crescent": "Cres",
        # Add more mappings as needed
    }
    
    # Abbreviate street types in the address
    found = False
    for full, abbrev in street_types.items():
        if full in value:
            value = value.replace(full, abbrev)
            found = True
            break  # Stop after the first replacement
    
    # Output the modified value, or the original value if no replacement was made
    return value if found else value


### Leading and Trailing Characters

Will remove special characters (!@#$%^&*()<>?:"{}|[]\;',./) from the start and end of a field.

    value.replace(/(^[\p{P}\p{S}]+|[\p{P}\p{S}]+$)/, "")
