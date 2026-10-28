# Section 4: Code Smells

Group Members: Sydney Osako, Anika Rustagi, & Alicia Zhang

## Mysterious Name

The mysterious name code smell is the practice of giving variables, functions, methods, classes, etc. names that are obscure, non-descriptive, inscrutable, misleading, or otherwise unhelpful.

Mysterious names can arise for a multitude of reasons. One reason is that the programmer never came up with proper names to begin with (possibly because the names were meant to be temporary, or they assumed that they would be able to remember what everything was for and that nobody else would ever look at it) and never renamed anything. Another is that the name was suitable at the time it was first written/thought of, but whatever was named has since been rewritten or changed such that the name no longer fits. It could also be that a developer does not want to make their names long and so gives short names that mislead or do not fully describe the named items.

Mysterious names are an issue for comprehensibility. For persons who may want to work on/with a piece of code, if the names in it are vague or confusing, the programmers may have difficulty figuring out how to use the code written, or what does what and how. They might even spend more time trying to untangle the meaning of the code than they do working with/on it or may find the task of understanding the code to be too difficult. This issue isn't limited to developers working with/on code they did not write, however. If a developer wants to revisit some code they wrote a long time ago, they may not fully remember the details of how everything works, so if they come back to code with mysterious names, they may have the same difficulty deciphering it as a person who didn't ever work on it.

**Prevention and Refactoring**

- Don't be afraid to rename things if they need a new name
- If a name isn't easy to come up with, consider fixing the code itself, as an inability to come up with a name can signal a much bigger issue

**Refactoring existing code:**

- Change Function Declaration: Rename functions when you find their names confusing and choose parameters that fit for the extent of what you want the function to do
- Rename Variable: The more a variable is used, and the more important it is to the code, the more important it is that the variable has a good name, so rename variables with vague names if they meet either condition.
- Rename Field: Rename fields if it makes them easier to understand and rename all accessors and methods related to the field accordingly, and test the code.

**Code Example (C):**

Before:

```C
int area(int a, int b) {
    return a * b;
}
```

- This is in a file with a bunch of other functions that calculate other types of mathematical equations, including the areas of other shapes. "area" then is not a specific enough name for the function. Similarly, while a and b are fine enough parameter names for something like this, a and b are used as parameters for other functions in the code who use them differently or for which a and b have different meanings than they do in this function.

After:

```C
int rectangleArea(int x, int y) {
    return x * y;
}
```

- Refactored with Change Function Declaration: I renamed the function to be more specific so that it can't be confused as to what shape's area it calculates, and so that it is clear how this function differs from the functions who calculate the area for different objects. Also, a and b were renamed to x and y to avoid confusion and also because they just make more sense as variables for a mathematical function like this.

## Long Function

The long function code smell happens when a function does too many things or includes too much code, making it difficult to understand. Shorter functions are usually easier to read, reuse, and change because each one can focus on a specific task. While splitting a function into smaller ones means the reader may have to look at other functions to understand everything, clear and meaningful names help explain their purpose without needing to read every line.

Long functions can arise when programmers keep adding new logic to an existing function instead of separating parts into their own functions. This can make the code harder to maintain because calculations, conditions, and loops are all mixed together. The main issue is not just how many lines a function has, but how difficult it is to understand what it does and how it does it.

**Prevention and Refactoring:**

- Try to keep functions focused on one clear purpose and give them names that describe what they do.
- If you feel the need to add a comment explaining a block of code, consider turning that block into a clearly named function instead.
- Look for related calculations, conditionals, and loops that can be separated into smaller functions, even if the extracted code is only one line.

**Refactoring existing code:**

- Extract Function: Move a group of related statements into its own function with a meaningful name. This is usually the first and most useful way to shorten long functions.
- Replace Temp with Query: Replace unnecessary temporary variables with functions that calculate their values, making extraction easier.
- Introduce Parameter Object or Preserve Whole Object: Reduce the number of parameters needed when moving code into a separate function.
- Decompose Conditional: Move complicated conditional logic into smaller, clearly named functions. If the same condition is handled repeatedly, consider Replace Conditional with Polymorphism.
- Split Loop: Separate a loop that is doing more than one task into loops or functions with distinct purposes.
- Replace Function with Command: If too many temporary variables or parameters still make extraction difficult, consider putting the operation into a command object.

**Code Example (JavaScript):**

Before:

```javascript
function processOrder(order) {
    let subtotal = 0;
    for (const item of order.items) {
        subtotal += item.price * item.quantity;
    }

    let discount = 0;
    if (order.isMember) {
        discount = subtotal * 0.1;
    }

    let shipping = 5;
    if (subtotal >= 50) {
        shipping = 0;
    }

    return subtotal - discount + shipping;
}
```

- This function calculates the subtotal, applies a member discount, and decides the shipping cost all in one place. Although the steps are not especially complicated, having several responsibilities in one function makes it harder to quickly understand or change a specific rule.

After:

```javascript
function calculateSubtotal(items) {
    return items.reduce((sum, item) => sum + item.price * item.quantity, 0);
}

function calculateMemberDiscount(subtotal, isMember) {
    return isMember ? subtotal * 0.1 : 0;
}

function calculateShipping(subtotal) {
    return subtotal >= 50 ? 0 : 5;
}

function processOrder(order) {
    const subtotal = calculateSubtotal(order.items);
    const discount = calculateMemberDiscount(subtotal, order.isMember);
    const shipping = calculateShipping(subtotal);
    return subtotal - discount + shipping;
}
```

- Refactored with Extract Function: The subtotal, discount, and shipping calculations are now in separate functions with names that explain their purpose. The `processOrder` function is easier to follow because it shows the overall process without mixing all the details together. The behavior stays the same, but each rule can now be updated more easily.

## Primitive Obsession

The primitive obsession code smell is the habit or reliance of using built in primitives such as int, char, float, boolean, string, etc., for complex concepts, when they can be better represented through their own types or objects.

Primitive Obsession typically arises when developers decide that using a primitive is the simplest way to quickly reach solution. Primitives are easy to create, work with, and easily used through built in methods which is why they may be thought of first before creating a new type or object.

Primitive Obsession can be problematic because a primitive can store a value but cannot understand it. Examples are emails, phone numbers, date ranges, and money. While these could be stored in a string or an integer, they have their own formats and rules that a primitive knows nothing about, such as an email needing an '@', a US phone number having exactly 10 digits, or a date range needing its start to come before its end. A primitive dosen't enforce these standards, so invalid values can exist and travel through the program. Checks are also then required wherever the value is used. This also results in extra code written only to check and format values that a dedicated type could handle once.

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

```Python
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

```Python
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
