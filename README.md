 # task_manager.py

import json
import os

class Task:
    id_counter = 1

    def __init__(self, title, completed=False):
        self.id = Task.id_counter
        self.title = title
        self.completed = completed
        Task.id_counter += 1

    def to_dict(self):
        return {
            "id": self.id,
            "title": self.title,
            "completed": self.completed
        }
    
    @classmethod
    def from_dict(cls, data):
        task = cls(data['title'], data['completed'])
        task.id = data['id']
        return task

 #Implement Task Management Functions
tasks = []

def add_task(title):
    task = Task(title)
    tasks.append(task)
    print(f"Task added: {task.title} (ID: {task.id})")

def view_tasks():
    if not tasks:
        print("No tasks available.")
        return
    for task in tasks:
        status = "✓" if task.completed else "✗"
        print(f"[ {task.id}] {task.title} - {'Completed' if task.completed else 'Pending'} [{status}]")

def delete_task(task_id):
    global tasks
    tasks = [task for task in tasks if task.id != task_id]
    print(f"Task with ID {task_id} has been deleted.")

def mark_task_complete(task_id): 
    for task in tasks:
        if task.id == task_id:
            task.completed = True
            print(f"Task with ID {task_id} marked as complete.")
            return
    print(f"Task with ID {task_id} not found.")

#File Handling
def save_tasks():
    with open("tasks.json", "w") as file:
        json.dump([task.to_dict() for task in tasks], file)
    print("Tasks saved to tasks.json")

def load_tasks():
    if os.path.exists("tasks.json"):
        with open("tasks.json", "r") as file:
            data = json.load(file)
            global tasks
            tasks = [Task.from_dict(item) for item in data]
            if tasks:
                Task.id_counter = max(task.id for task in tasks) + 1
    print("Tasks loaded from tasks.json")

 #Create a Command-Line Interface
def main():
    load_tasks()
    while True:
        print("\nTask Manager CLI")
        print("1. Add Task")
        print("2. View Tasks")
        print("3. Delete Task")
        print("4. Mark Task as Complete")
        print("5. Save and Exit")

        choice = input("Choose an option: ")
        
        if choice == "1":
            title = input("Enter task title: ")
            add_task(title)
        elif choice == "2":
            view_tasks()
        elif choice == "3":
            task_id = int(input("Enter task ID to delete: "))
            delete_task(task_id)
        elif choice == "4":
            task_id = int(input("Enter task ID to mark as complete: "))
            mark_task_complete(task_id)
        elif choice == "5":
            save_tasks()
            print("Exiting...")
            break
        else:
            print("Invalid option. Please try again.")

if __name__ == "__main__":
    main()



