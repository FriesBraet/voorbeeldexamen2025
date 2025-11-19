import csv


class Node:
    def __init__(self, task_name: str, duration: int, priority: int):
        self.task_name = task_name
        self.duration = duration
        self.priority = priority
        self.next = None

class LinkedList:
    def __init__(self):
        self.head = None
        self.tail = None
        self.size = 0

    def add_task(self, task_name: str, task_duration: int, priority: int):
        new_node = Node(task_name, task_duration, priority)
        if self.tail is None:
            self.head = self.tail = new_node
        else:
            self.tail.next = new_node
            self.tail = new_node
        self.size += 1


    def remove_task(self, task_name):
        current = self.head
        previous = None

        while current is not None and current.task_name != task_name:
            previous = current
            current = current.next

        if current is None:
            return None

        if previous is None:
            self.head = current.next
        else:
            previous.next = current.next

        if current == self.tail:
            self.tail = previous

        self.size -= 1
        return current

    def display_tasks(self):
        if self.size == 0:
            print("There is no task")
            return
        current = self.head
        while current is not None:
            print(f"Task: {current.task_name}, Duration: {current.duration}, Priority: {current.priority}")
            current = current.next

    def find_task(self, task_name):
        current = self.head
        while current is not None:
            if current.task_name == task_name:
                return current
            current = current.next
        return None

    def calculate_total_duration(self):
        duration = 0
        current = self.head
        while current is not None:
            duration += current.duration
            current = current.next
        return duration

    def read_tasks_from_csv(self, file_path: str):
        with open(file_path, mode="r", encoding="utf-8", newline="") as file:
            reader = csv.reader(file)

            next(reader, None)

            for row in reader:
                task_name = row[0]
                duration = int(row[1])
                priority = int(row[2])
                self.add_task(task_name, duration, priority)

    def reorder_tasks_by_priority(self):
        if self.size <= 1:
            return

        sorted_head = None
        current = self.head

        while current is not None:
            next_node = current.next
            current.next = None
            sorted_head = self.sorted_insert_by_priority(sorted_head, current)
            current = next_node

        self.head = sorted_head

        tail = self.head
        while tail is not None and tail.next is not None:
            tail = tail.next
        self.tail = tail

    def sorted_insert_by_priority(self, head, node):
        if head is None or node.priority < head.priority:
            node.next = head
            return node

        current = head
        while current.next is not None and current.next.priority <= node.priority:
            current = current.next

        node.next = current.next
        current.next = node
        return head

    def reorder_tasks_by_priority_duration(self):
        if self.size <= 1:
            return

        sorted_head = None
        current = self.head

        while current is not None:
            next_node = current.next
            current.next = None
            sorted_head = self.sorted_insert_by_priority_duration(sorted_head, current)
            current = next_node

        self.head = sorted_head

        tail = self.head
        while tail is not None and tail.next is not None:
            tail = tail.next
        self.tail = tail

    def sorted_insert_by_priority_duration(self, head, node):
        if (head is None or node.priority < head.priority or
            (node.priority == head.priority and node.duration < head.duration)):
            node.next = head
            return node

        current = head
        while (current.next is not None and
               (current.next.priority < node.priority or
                (current.next.priority == node.priority and current.next.duration <= node.duration))):
            current = current.next

        node.next = current.next
        current.next = node
        return head

if __name__ == "__main__":
    ll = LinkedList()

    ll.read_tasks_from_csv("tasks.csv")

    print("ORIGINELE TAKEN:")
    ll.display_tasks()
    print()

    ll.reorder_tasks_by_priority()
    print("GESORTEERD OP PRIORITY:")
    ll.display_tasks()
    print()

    ll = LinkedList()
    ll.read_tasks_from_csv("tasks.csv")

    ll.reorder_tasks_by_priority_duration()
    print("GESORTEERD OP PRIORITY + DURATION:")
    ll.display_tasks()

    print("\nTotale duur:", ll.calculate_total_duration())
# voorbeeldexamen2025
