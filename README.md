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
        'Abbey': 'Abbey',
         'Acres': 'Acres',
         'Alley': 'Alley',
         'Avenue': 'Ave',
         'Bay': 'Bay',
         'Beach': 'Beach',
         'Bend': 'Bend',
         'Boulevard': 'Blvd',
         'By-pass': 'Bypass',
         'Byway': 'Byway',
         'Campus': 'Campus',
         'Cape': 'Cape',
         'Centre': 'Ctr',
         'Chase': 'Chase',
         'Circle': 'Cir',
         'Circuit': 'Circt',
         'Close': 'Close',
         'Common': 'Common',
         'Concession': 'Conc',
         'Corners': 'Crnrs',
         'Court': 'Crt',
         'Cove': 'Cove',
         'Crescent': 'Cres',
         'Crossing': 'Cross',
         'Cul-de-sac': 'Cds',
         'Dale': 'Dale',
         'Dell': 'Dell',
         'Diversion': 'Divers',
         'Downs': 'Downs',
         'Drive': 'Dr',
         'End': 'End',
         'Esplanade': 'Espl',
         'Estates': 'Estate',
         'Expressway': 'Expy',
         'Extension': 'Exten',
         'Farm': 'Farm',
         'Field': 'Field',
         'Forest': 'Forest',
         'Freeway': 'Fwy',
         'Front': 'Front',
         'Gardens': 'Gdns',
         'Gate': 'Gate',
         'Glade': 'Glade',
         'Glen': 'Glen',
         'Green': 'Green',
         'Grounds': 'Grnds',
         'Grove': 'Grove',
         'Harbour': 'Harbr',
         'Heath': 'Heath',
         'Heights': 'Hts',
         'Highlands': 'Hghlds',
         'Highway': 'Hwy',
         'Hill': 'Hill',
         'Hollow': 'Hollow',
         'Inlet': 'Inlet',
         'Island': 'Island',
         'Key': 'Key',
         'Knoll': 'Knoll',
         'Landing': 'Landng',
         'Lane': 'Lane',
         'Limits': 'Lmts',
         'Line': 'Line',
         'Link': 'Link',
         'Lookout': 'Lkout',
         'Loop': 'Loop',
         'Mall': 'Mall',
         'Manor': 'Manor',
         'Maze': 'Maze',
         'Meadow': 'Meadow',
         'Mews': 'Mews',
         'Moor': 'Moor',
         'Mount': 'Mount',
         'Mountain': 'Mtn',
         'Orchard': 'Orch',
         'Parade': 'Parade',
         'Park': 'Pk',
         'Parkway': 'Pky',
         'Passage': 'Pass',
         'Path': 'Path',
         'Pathway': 'Ptway',
         'Pines': 'Pines',
         'Place': 'Pl',
         'Plateau': 'Plat',
         'Plaza': 'Plaza',
         'Point': 'Pt',
         'Port': 'Port',
         'Private': 'Pvt',
         'Promenade': 'Prom',
         'Quay': 'Quay',
         'Ramp': 'Ramp',
         'Range': 'Rg',
         'Ridge': 'Ridge',
         'Rise': 'Rise',
         'Road': 'Rd',
         'Route': 'Rte',
         'Row': 'Row',
         'Run': 'Run',
         'Square': 'Sq',
         'Street': 'St',
         'Subdivision': 'Subdiv',
         'Terrace': 'Terr',
         'Thicket': 'Thick',
         'Towers': 'Towers',
         'Townline': 'Tline',
         'Trail': 'Trail',
         'Turnabout': 'Trnabt',
         'Vale': 'Vale',
         'Via': 'Via',
         'View': 'View',
         'Village': 'Villge',
         'Villas': 'Villas',
         'Vista': 'Vista',
         'Walk': 'Walk',
         'Way': 'Way',
         'Wharf': 'Wharf',
         'Wood': 'Wood',
         'Wynd': 'Wynd'
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

### Street Number Position Clean

Will correct case for number place. Example: 1st Place, 2ND Prize, 3rD Option, 4TH Choice.


    import re
    
    def format_position_abbreviations(text):
        """
        Regular expression to find position abbreviations:
        - \b asserts position at a word boundary,
        - \d+ matches one or more digits,
        - (?:st|nd|rd|th) is a non-capturing group for the suffixes,
        - \b asserts position at a word boundary,
        - flags=re.IGNORECASE makes the match case-insensitive.
        """
        pattern = r'\b\d+(?:st|nd|rd|th)\b'
        
        # Function to convert matched string to lowercase
        def to_lowercase(match):
            return match.group().lower()
        
        # Replace all found matches with their lowercase version
        formatted_text = re.sub(pattern, to_lowercase, text, flags=re.IGNORECASE)
        return formatted_text
    
    # Example usage
    # Assuming 'value' is a string variable that contains the text to be formatted
    value = "Example: 1st Place, 2ND Prize, 3rD Option, 4TH Choice."
    formatted_text = format_position_abbreviations(value)
    print(formatted_text)
