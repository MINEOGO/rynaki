# rynaki Module Documentation

This documentation provides a comprehensive, detailed overview of the `rynaki` module, which acts as a Python wrapper for the Akinator game API. It allows you to programmatically start a game, answer questions, and retrieve Akinator's final guess.

## Installation

Install the module via `pip` from your terminal.

```python
pip install rynaki
```

## Full Usage Example

```python
import rynaki

# Initialize the Akinator game in English for characters
aki = rynaki.Akinator(lang="en", theme="characters")

# Start the game and get the first question
question = aki.start_game()
print(f"Q: {question}")

# Main game loop continues until Akinator has found a character
while aki.name is None:
    # Get user input. For simplicity, we use a basic input prompt.
    # In a real app, you might use a GUI with buttons.
    answer = input("Your answer (y/n/idk/p/pn): ").lower()
    if answer not in ["y", "n", "idk", "p", "pn"]:
        print("Invalid input. Please try again.")
        continue
    
    # Post the answer to the API and get the next question
    aki.post_answer(answer)

    # Display the new question if the game is not over
    if aki.name is None:
        print(f"Q: {aki.question} ({aki.progression:.2f}%)")

# Print the final guess from Akinator
print("\n--- Akinator's Guess ---")
print(f"Character: {aki.name}")
print(f"Description: {aki.description}")
print(f"Photo: {aki.photo}")
```

---

## Classes

### ::: rynaki.AkinatorError
A custom exception class used to signal errors specific to the `rynaki` module. This helps in distinguishing module-related issues from general Python errors. It is raised for invalid API responses, incorrect answer formats, or if a method is called at an inappropriate time (e.g., `go_back()` at the first question).

### ::: rynaki.Akinator
The primary class for managing and interacting with an Akinator game session.

#### `__init__`
```python
__init__(self, theme: str = "characters", lang: str = "ar", child_mode: bool = False)
```
Initializes a new Akinator game session. This sets up the necessary HTTP client and configures the session based on the provided arguments.

**Parameters:**

*   **`theme`** (`str`, optional): Specifies the game's subject category. Must be one of: `"characters"`, `"objects"`, or `"animals"`. Defaults to `"characters"`.
*   **`lang`** (`str`, optional): A two-letter country code for the game's language (e.g., `"en"`, `"es"`, `"fr"`, `"ar"`). Defaults to `"ar"`.
*   **`child_mode`** (`bool`, optional): If `True`, enables a child-safe mode that filters questions and results. Defaults to `False`.

---

#### Methods

##### `start_game`
```python
start_game(self) -> str
```
Begins a new game session. It resets any prior game state and fetches the first question from the Akinator API.

**Returns:**
*   `str`: The first question.

##### `post_answer`
```python
post_answer(self, answer: str) -> dict
```
Submits an answer to the current question and advances the game. This method updates the internal state with the next question or, if the game is over, with the final guess.

**Parameters:**
*   **`answer`** (`str`): The answer, which must be one of: `'y'`, `'n'`, `'idk'`, `'p'`, or `'pn'`.

**Returns:**
*   `dict`: The raw JSON response from the API.

**Raises:**
*   `AkinatorError`: If the answer format is invalid or if the API request fails.

##### `go_back`
```python
go_back(self) -> dict
```
Reverts the game to the previous question. This is useful for correcting a mistaken answer.

**Returns:**
*   `dict`: The raw API response for the previous state.

**Raises:**
*   `AkinatorError`: If called on the first question (step 0).

##### `exclude`
```python
exclude(self) -> dict
```
Used after Akinator makes an incorrect guess. This method tells the API to discard the last proposition and provide an alternative guess or a new question.

**Returns:**
*   `dict`: The raw API response with the new game state.

**Raises:**
*   `AkinatorError`: If the API request fails.

---

#### Public Instance Attributes

These attributes store the live state of the game and are accessible for reading.

*   **`question`** (`str`): The current question text.
*   **`step`** (`int`): The current question number, starting from 0.
*   **`progression`** (`float`): The game's completion percentage (e.g., 85.5).
*   **`name`** (`str` | `None`): The guessed name. Is `None` until a guess is made.
*   **`description`** (`str` | `None`): A short description of the guessed entity. Is `None` until a guess is made.
*   **`photo`** (`str` | `None`): A URL to an image of the guess. Is `None` until a guess is made.
