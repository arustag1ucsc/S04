# Section 4: Code Smells
Group Members: Sydney Osako, Anika Rustagi, & Alicia Zhang
## Mysterious Name

## Long Function

## Primitive Obsession
The primitive obsession code smell is the habit or reliance of using built in primitives such as int, char, float, boolean, string, etc., for complex concepts, when they can be better represented through their own types or objects. 

Primitive Obsession typically arises when developers decide that using a primitive is the simplest way to quickly reach solution. Primitives are easy to create, work with, and easily used through built in methods which is why they may be thought of first before creating a new type or object.

Primitive Obsession can be problematic because a primitive can store a value but cannot understand it. Examples are emails, phone numbers, date ranges, and money. While these could be stored in a string or an integer, they have their own formats and rules that a primitive knows nothing about, such as an email needing an '@', a US phone number having exactly 10 digits, or a date range needing its start to come before its end. A plain primitive enforces none of these standards, so invalid values can exist and travel through the program unnoticed. Checks are then required wherever the value is used. This also results in extra code written only to check and format values that a dedicated type could handle once.

**Prevention and Refactoring:**
- Check if variables require special logic, comparison, formatting rules
- Think about the reusability and improved testability a variable would have
- If the code needs to format, compare, or calculate with the value, that behavior belongs in an object with the data

**Refactoring existing code:**
- Replace primitive with object: wrap a single primitive that has rules or behavior in a class
- Replace Type Code with Subclasses, then Replace Conditional with Polymorphism: Make a separate class for each type, so each type can handle its own behavior.
- Introduce Parameter Object: Put the values into one object and pass that object.

**Code Example (Python):**

Before:
```
def add_user(name, email):
    if "@" not in email:
        raise ValueError("invalid email")
    print("Registered", name, email)

def send_receipt(email):
    if "@" not in email:
        return
    print("Receipt sent to", email)
```
- @ check is used in two different places, this can cause problems if the rule needs to changes. This could result in a invalid email to go through.

After:
```
class Email:
    def __init__(self, value):
        if "@" not in value:
            raise ValueError("invalid email")
        self.value = value

def add_user(name, email: Email):
    print("Registered", name, email.value)

def send_receipt(email: Email):
    print("Receipt sent to", email.value)

add_user("J", Email("J@gmail.com"))

```
- Refactored with Replace Primitive with Object: Now the rule is in one place and checks for an '@' this prevents any invalid emails from being created.