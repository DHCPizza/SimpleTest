# SimpleTest.py


Super Simple Multiple Choice Easy to setup Test / 'Flash Card' system.
1) Randomizes questions each time
2) Keeps track of "score"
3) Can answer A-D or 1-4
4) Easily add questions and answers to CSV file

![Exmaple](https://github.com/DHCPizza/SimpleTest/blob/main/Images/example.jpg)

![CSV Exmaple](https://github.com/DHCPizza/SimpleTest/blob/main/Images/csv-example.jpg)

```python
import csv
import random
import codecs
import os

def load_questions(filename):
    """
    Load questions from a CSV file.

    Args:
        filename (str): The path to the CSV file.

    Returns:
        list: A list of dictionaries representing the loaded questions.
    """
    questions = []
    with codecs.open(filename, 'r', encoding='utf-8-sig') as csvfile:
        reader = csv.DictReader(csvfile)
        for row in reader:
            row = {key.strip(): value.strip() for key, value in row.items()}
            if any(row.values()):  # Skip empty rows
                questions.append(row)
    if not questions:
        print("No questions found in the file.")
    return questions

def get_random_question(questions, used_questions):
    """
    Get a random question that has not been used before.

    Args:
        questions (list): The list of available questions.
        used_questions (list): The list of questions that have been used.

    Returns:
        dict or None: A randomly selected question or None if no more questions are available.
    """
    remaining_questions = [q for q in questions if q not in used_questions]
    if not remaining_questions:
        return None
    return random.choice(remaining_questions)

def prompt_for_answer(question, question_number, total_questions):
    """
    Prompt the user to answer the given question.

    Args:
        question (dict): The question to be answered.
        question_number (int): The current question number.
        total_questions (int): The total number of questions.

    Returns:
        str: The user's answer(s) to the question.
    """
    os.system('cls' if os.name == 'nt' else 'clear')
    print(f"Question {question_number}/{total_questions}:\n" + question["Question"])
    print("Options:")
    print("A. " + question["Option A"])
    print("B. " + question["Option B"])
    print("C. " + question["Option C"])
    print("D. " + question["Option D"])

    valid_answers = ["A", "B", "C", "D", "1", "2", "3", "4"]
    answer = input("Your answer(s): ").upper()
    while not all(a in valid_answers for a in answer):
        print("Invalid input. Please enter letter(s) (A-D) or number(s) between 1 and 4.")
        answer = input("Your answer(s): ").upper()

    if answer.isdigit():
        answer = "".join(chr(ord("A") + int(a) - 1) for a in answer)

    return answer

def check_answer(question, answer):
    """
    Check if the user's answer is correct.

    Args:
        question (dict): The question to check the answer against.
        answer (str): The user's answer(s) to the question.

    Returns:
        bool: True if the answer is correct, False otherwise.
    """
    expected_answer = question["Answer"]
    return set(answer) == set(expected_answer)

def main():
    """
    The main entry point of the program.
    """
    filename = "./sample-questions.csv"  # Replace with the actual filename
    questions = load_questions(filename)
    random.shuffle(questions)  # Shuffle the questions

    if not questions:
        return

    score = 0
    total_questions = len(questions)
    used_questions = []

    for i in range(total_questions):
        question = get_random_question(questions, used_questions)

        if question is None:
            print("No more questions remaining.")
            break

        used_questions.append(question)
        answer = prompt_for_answer(question, i + 1, total_questions)

        if check_answer(question, answer):
            print("Correct!")
            score += 1
        else:
            print("Incorrect. The correct answer is: " + question["Answer"])

        input("Press Enter to continue...")  # Wait for user to press Enter

    os.system('cls' if os.name == 'nt' else 'clear')  # Clear screen before displaying final score
    print("Quiz completed!")
    print("Your score: " + str(score) + "/" + str(total_questions))

if __name__ == "__main__":
    main()
```
