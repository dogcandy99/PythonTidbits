
import re

# The data source: A list of dictionaries representing individual patient records.
medical_records = [
    {
        'patient_id': 'P1001',
        'age': 34,
        'gender': 'Female',
        'diagnosis': 'Hypertension',
        'medications': ['Lisinopril'],
        'last_visit_id': 'V2301',
    },
    {
        'patient_id': 'p1002',
        'age': 47,
        'gender': 'male',
        'diagnosis': 'Type 2 Diabetes',
        'medications': ['Metformin', 'Insulin'],
        'last_visit_id': 'v2302',
    },
    {
        'patient_id': 'P1003',
        'age': 29,
        'gender': 'female',
        'diagnosis': 'Asthma',
        'medications': ['Albuterol'],
        'last_visit_id': 'v2303',
    },
    {
        'patient_id': 'p1004',
        'age': 56,
        'gender': 'Male',
        'diagnosis': 'Chronic Back Pain',
        'medications': ['Ibuprofen', 'Physical Therapy'],
        'last_visit_id': 'V2304',
    }
]

def find_invalid_records(patient_id, age, gender, diagnosis, medications, last_visit_id):
    """
    Checks individual fields of a medical record against specific business rules.
    
    Returns:
        list: A list of keys (field names) that failed validation.
    """
    constraints = {
        # patient_id must be a string starting with 'p' followed by digits (case insensitive)
        'patient_id': isinstance(patient_id, str)
        and re.fullmatch('p\d+', patient_id, re.IGNORECASE), [cite: 3, 4]
     
        # age must be an integer and at least 18
        'age': isinstance(age, int) and age >= 18, [cite: 4]
        
        # gender must be a string and specifically 'male' or 'female'
        'gender': isinstance(gender, str) and gender.lower() in ('male', 'female'), [cite: 4]
        
        # diagnosis can be a string or empty (None)
        'diagnosis': isinstance(diagnosis, str) or diagnosis is None, [cite: 4]
        
        # medications must be a list containing only strings
        'medications': isinstance(medications, list)
        and all([isinstance(i, str) for i in medications]), [cite: 4]
        
        # last_visit_id must be a string starting with 'v' followed by digits
        'last_visit_id': isinstance(last_visit_id, str)
        and re.fullmatch('v\d+', last_visit_id, re.IGNORECASE) [cite: 4]
    }
    
    # Identify which rules returned 'False' and return those field names
    return [key for key, value in constraints.items() if not value] [cite: 5]

def validate(data):
    """
    Validates a collection of medical records for correct structure and data integrity.
    
    Args:
        data: The list/tuple of records to validate.
        
    Returns:
        bool: True if all records are valid, False otherwise.
    """
    # Ensure the input is a sequence (list or tuple) [cite: 5]
    is_sequence = isinstance(data, (list, tuple))

    if not is_sequence:
        print('Invalid format: expected a list or tuple.')
        return False
        
    is_invalid = False
    # The set of keys every valid dictionary MUST contain
    key_set = set(
        ['patient_id', 'age', 'gender', 'diagnosis', 'medications', 'last_visit_id']
    ) [cite: 5]

    for index, dictionary in enumerate(data):
        # Step 1: Check if the item in the list is actually a dictionary [cite: 6]
        if not isinstance(dictionary, dict):
            print(f'Invalid format: expected a dictionary at position {index}.')
            is_invalid = True
            continue

        # Step 2: Ensure the dictionary has all required keys and no extra ones [cite: 6, 7]
        if set(dictionary.keys()) != key_set:
            print(
                f'Invalid format: {dictionary} at position {index} has missing and/or invalid keys.'
            )
            is_invalid = True
            continue

        # Step 3: Check the actual content of the fields using our constraint function 
        invalid_records = find_invalid_records(**dictionary)
        for key in invalid_records:
            val = dictionary[key]
            print(f"Unexpected format '{key}: {val}' at position {index}.")
            is_invalid = True    

    # Final result: If any error was flagged during the loop, return False [cite: 8]
    if is_invalid:
        return False
        
    print('Valid format.')
    return True

# Execute the validation
validate(medical_records)
