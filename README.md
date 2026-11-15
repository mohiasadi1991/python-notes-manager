import json
from pathlib import Path
from datetime import datetime

FILE = Path("notes.json")

def load_notes():
    if FILE.exists():
        try:
            return json.loads(FILE.read_text(encoding="utf-8"))
        except json.JSONDecodeError:
            print("Notes file is invalid.")
    return []

def save_notes(notes):
    FILE.write_text(
        json.dumps(notes, indent=4, ensure_ascii=False),
        encoding="utf-8"
    )

def add_note(notes):
    title = input("Note title: ").strip()
    content = input("Note content: ").strip()

    if not title or not content:
        print("Title and content cannot be empty.")
        return

    notes.append({
        "title": title,
        "content": content,
        "created_at": datetime.now().strftime(
            "%Y-%m-%d %H:%M"
        )
    })

    save_notes(notes)
    print("Note saved successfully!")

def view_notes(notes):
    if not notes:
        print("No notes found.")
        return

    for index, note in enumerate(notes, 1):
        print(f"\n{index}. {note['title']}")
        print(f"   {note['content']}")
        print(f"   Created: {note['created_at']}")

def search_notes(notes):
    keyword = input("Search keyword: ").strip().lower()

    if not keyword:
        print("Please enter a keyword.")
        return

    results = [
        note for note in notes
        if keyword in note["title"].lower()
        or keyword in note["content"].lower()
    ]

    view_notes(results)

def delete_note(notes):
    view_notes(notes)

    if not notes:
        return

    try:
        index = int(input("\nNote number to delete: ")) - 1

        if 0 <= index < len(notes):
            removed = notes.pop(index)
            save_notes(notes)
            print(f"Deleted: {removed['title']}")
        else:
            print("Invalid note number.")

    except ValueError:
        print("Please enter a valid number.")

def main():
    notes = load_notes()

    while True:
        print("\n=== Python Notes Manager ===")
        print("1. Add note")
        print("2. View notes")
        print("3. Search notes")
        print("4. Delete note")
        print("5. Exit")

        choice = input("Choose an option: ")

        if choice == "1":
            add_note(notes)
        elif choice == "2":
            view_notes(notes)
        elif choice == "3":
            search_notes(notes)
        elif choice == "4":
            delete_note(notes)
        elif choice == "5":
            print("Goodbye!")
            break
        else:
            print("Invalid option.")

if __name__ == "__main__":
    main()
