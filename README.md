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
        "Abbey": "ABBEY",
        "Acres": "ACRES",
        "Alley": "ALLEY",
        "Avenue": "AVE",
        "Bay": "BAY",
        "Beach": "BEACH",
        "Bend": "BEND",
        "Boulevard": "BLVD",
        "By-pass": "BYPASS",
        "Byway": "BYWAY",
        "Campus": "CAMPUS",
        "Cape": "CAPE",
        "Centre": "CTR",
        "Chase": "CHASE",
        "Circle": "CIR",
        "Circuit": "CIRCT",
        "Close": "CLOSE",
        "Common": "COMMON",
        "Concession": "CONC",
        "Corners": "CRNRS",
        "Court": "CRT",
        "Cove": "COVE",
        "Crescent": "CRES",
        "Crossing": "CROSS",
        "Cul-de-sac": "CDS",
        "Dale": "DALE",
        "Dell": "DELL",
        "Diversion": "DIVERS",
        "Downs": "DOWNS",
        "Drive": "DR",
        "End": "END",
        "Esplanade": "ESPL",
        "Estates": "ESTATE",
        "Expressway": "EXPY",
        "Extension": "EXTEN",
        "Farm": "FARM",
        "Field": "FIELD",
        "Forest": "FOREST",
        "Freeway": "FWY",
        "Front": "FRONT",
        "Gardens": "GDNS",
        "Gate": "GATE",
        "Glade": "GLADE",
        "Glen": "GLEN",
        "Green": "GREEN",
        "Grounds": "GRNDS",
        "Grove": "GROVE",
        "Harbour": "HARBR",
        "Heath": "HEATH",
        "Heights": "HTS",
        "Highlands": "HGHLDS",
        "Highway": "HWY",
        "Hill": "HILL",
        "Hollow": "HOLLOW",
        "Inlet": "INLET",
        "Island": "ISLAND",
        "Key": "KEY",
        "Knoll": "KNOLL",
        "Landing": "LANDNG",
        "Lane": "LANE",
        "Limits": "LMTS",
        "Line": "LINE",
        "Link": "LINK",
        "Lookout": "LKOUT",
        "Loop": "LOOP",
        "Mall": "MALL",
        "Manor": "MANOR",
        "Maze": "MAZE",
        "Meadow": "MEADOW",
        "Mews": "MEWS",
        "Moor": "MOOR",
        "Mount": "MOUNT",
        "Mountain": "MTN",
        "Orchard": "ORCH",
        "Parade": "PARADE",
        "Park": "PK",
        "Parkway": "PKY",
        "Passage": "PASS",
        "Path": "PATH",
        "Pathway": "PTWAY",
        "Pines": "PINES",
        "Place": "PL",
        "Plateau": "PLAT",
        "Plaza": "PLAZA",
        "Point": "PT",
        "Port": "PORT",
        "Private": "PVT",
        "Promenade": "PROM",
        "Quay": "QUAY",
        "Ramp": "RAMP",
        "Range": "RG",
        "Ridge": "RIDGE",
        "Rise": "RISE",
        "Road": "RD",
        "Route": "RTE",
        "Row": "ROW",
        "Run": "RUN",
        "Square": "SQ",
        "Street": "ST",
        "Subdivision": "SUBDIV",
        "Terrace": "TERR",
        "Thicket": "THICK",
        "Towers": "TOWERS",
        "Townline": "TLINE",
        "Trail": "TRAIL",
        "Turnabout": "TRNABT",
        "Vale": "VALE",
        "Via": "VIA",
        "View": "VIEW",
        "Village": "VILLGE",
        "Villas": "VILLAS",
        "Vista": "VISTA",
        "Walk": "WALK",
        "Way": "WAY",
        "Wharf": "WHARF",
        "Wood": "WOOD",
        "Wynd": "WYND",
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


### Correct Name Capitalization

Accounts for Mc, Mac, O' prefixes and hyphenated names as well as acronyms (V.I.P.) and apostrophes (Beaux's vs O'Donnell)
    
       def custom_title_case(name):
        """
        Processes a string to capitalize the first letter of each word, taking into account periods, acronyms,
        hyphenated words, prefixes like Mc/Mac, and words with apostrophes correctly.
        
        Args:
        name (str): The string to be processed.
        
        Returns:
        str: The processed string with appropriate capitalization and spacing.
        """
    
        # Replace periods in acronyms and after abbreviations to maintain correct formatting
        period_placeholder = "||PERIOD||"
        name_with_placeholder = name.replace(".", period_placeholder)
    
        # Split the input into segments based on placeholder to handle sentence capitalization
        segments = name_with_placeholder.split(period_placeholder)
        capitalized_segments = []
    
        for segment in segments:
            words = segment.split()
            capitalized_words = []
            for word in words:
                if '-' in word:
                    # Capitalize each part of hyphenated words
                    parts = word.split('-')
                    capitalized_parts = [part.capitalize() for part in parts]
                    word = '-'.join(capitalized_parts)
                elif "'" in word:
                    # Special handling for apostrophes, capitalizing correctly based on following letters
                    parts = word.split("'")
                    if len(parts) > 1 and len(parts[1]) > 1:
                        # Capitalize after apostrophe only if it's not a single letter
                        parts = [parts[0].capitalize(), parts[1].capitalize()]
                    else:
                        parts = [parts[0].capitalize()] + parts[1:]
                    word = "'".join(parts)
                else:
                    # General capitalization for words without hyphens or apostrophes
                    word = word.capitalize()
    
                # Additional handling for Mc and Mac prefixes
                if word.startswith('Mc'):
                    word = 'Mc' + word[2:].capitalize()
                elif word.startswith('Mac') and len(word) > 3 and word[3].isalpha():
                    word = 'Mac' + word[3:].capitalize()
    
                capitalized_words.append(word)
    
            # Join the words back into a segment
            capitalized_segment = ' '.join(capitalized_words).strip()
            if capitalized_segment:
                # Capitalize the first letter of the segment, if not already uppercase
                capitalized_segment = capitalized_segment[0].upper() + capitalized_segment[1:]
                capitalized_segments.append(capitalized_segment)
            else:
                capitalized_segments.append('')
    
        # Join the segments back, replacing placeholders with periods
        result = period_placeholder.join(capitalized_segments).strip()
        final_result = result.replace(period_placeholder, ".")
    
        # Insert spaces after periods where necessary, except in acronyms
        corrected_result = ""
        i = 0
        while i < len(final_result):
            corrected_result += final_result[i]
            if final_result[i] == "." and i+1 < len(final_result) and final_result[i+1].isalpha() and (i+2 < len(final_result) and final_result[i+2].isalpha()):
                # Ensure space after periods when followed by two or more letters
                corrected_result += " "
            i += 1
    
        return corrected_result
    
    # Example usage:
    input_string = value
    return(custom_title_case(input_string))


### Leading and Trailing Characters

Will remove special characters (!@#$%^&*()<>?:"{}|[]\;',./) from the start and end of a field.

    value.replace(/(^[\p{P}\p{S}]+|[\p{P}\p{S}]+$)/, "")


### Phone Number Formatting

Will remove area/international calling code if present and format phone numbers as (604) 123-4567

    def formatPhoneNumber(value):
        # Strip all non-digit characters from the input
        digits = ''.join(c for c in value if c.isdigit())
        
        # Assuming US/Canada phone numbers, strip country code if present
        if len(digits) == 11 and digits.startswith("1"):
            digits = digits[1:]
        
        # Format the number with area code if it has the correct length
        if len(digits) == 10:
            # Format as (XXX) XXX-XXXX
            return "({}) {}-{}".format(digits[0:3], digits[3:6], digits[6:])
        else:
            # Return the original value if it doesn't match expected criteria
            return value
    
    return formatPhoneNumber(value)

### Address Clean-up

Will remove '.', ',' and newline characters and place address on a single line.

    def clean_address(value):
        if value is None:
            return None
        # Remove all commas and periods
        no_commas_periods = value.replace(',', '').replace('.', '')
        # Replace newlines with a space
        single_line_address = no_commas_periods.replace('\n', ' ').replace('\r', ' ')
        return single_line_address
    
    return clean_address(value)
