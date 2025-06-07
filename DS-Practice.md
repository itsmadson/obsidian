
# فصل پنج - صف (Queue)

# سوال 1-5: پیاده‌سازی صف با استفاده از لیست
class Queue:
    def __init__(self):
        self.queue = []
    
    def enqueue(self, item):
        """اضافه کردن عنصر به انتهای صف"""
        self.queue.append(item)
    
    def dequeue(self):
        """حذف عنصر از ابتدای صف"""
        if self.is_empty():
            return None
        return self.queue.pop(0)
    
    def is_empty(self):
        return len(self.queue) == 0
    
    def size(self):
        return len(self.queue)

# تست
q = Queue()
q.enqueue(1)
q.enqueue(2)
q.enqueue(3)
print(f"حجم صف: {q.size()}")
print(f"خروج از صف: {q.dequeue()}")

# سوال 2-5: تفاوت بین صف و آرایه
print("\n--- تفاوت صف و آرایه ---")
print("صف: FIFO (First In, First Out) - عنصر اول وارد شده، اول خارج می‌شود")
print("آرایه: دسترسی تصادفی به عناصر با استفاده از ایندکس")

# سوال 3-5: الگوریتم جستجوی BFS برای پیاده‌سازی صف
from collections import deque

def bfs_search(graph, start):
    """پیاده‌سازی BFS با استفاده از صف"""
    visited = set()
    queue = deque([start])
    visited.add(start)
    result = []
    
    while queue:
        node = queue.popleft()
        result.append(node)
        
        # بررسی همسایگان
        for neighbor in graph.get(node, []):
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    
    return result

# تست BFS
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D', 'E'],
    'C': ['A', 'F'],
    'D': ['B'],
    'E': ['B', 'F'],
    'F': ['C', 'E']
}

print(f"\nنتیجه BFS از گره A: {bfs_search(graph, 'A')}")

# سوال 4-5: الگوریتم معکوس کردن ریشه
def reverse_string_with_queue(s):
    """معکوس کردن رشته با استفاده از صف"""
    queue = Queue()
    
    # اضافه کردن حروف به صف
    for char in s:
        queue.enqueue(char)
    
    # خروج از صف و ساخت رشته معکوس
    reversed_str = ""
    while not queue.is_empty():
        reversed_str = queue.dequeue() + reversed_str
    
    return reversed_str

print(f"\nمعکوس 'سلام': {reverse_string_with_queue('سلام')}")

# سوال 5-5: مدیریت صف در موزیک پلیر
class MusicPlayer:
    def __init__(self):
        self.playlist = Queue()
        self.current_song = None
    
    def add_song(self, song):
        """اضافه کردن آهنگ به صف پخش"""
        self.playlist.enqueue(song)
        print(f"آهنگ '{song}' به صف پخش اضافه شد")
    
    def play_next(self):
        """پخش آهنگ بعدی"""
        if not self.playlist.is_empty():
            self.current_song = self.playlist.dequeue()
            print(f"در حال پخش: {self.current_song}")
        else:
            print("صف پخش خالی است")
    
    def show_playlist(self):
        """نمایش صف پخش"""
        print(f"آهنگ‌های باقی‌مانده: {self.playlist.queue}")

# تست موزیک پلیر
player = MusicPlayer()
player.add_song("آهنگ ۱")
player.add_song("آهنگ ۲")
player.add_song("آهنگ ۳")
player.play_next()
player.show_playlist()

# سوال 6-5: ساختار داده پشته (Stack)
class Stack:
    def __init__(self):
        self.stack = []
    
    def push(self, item):
        """اضافه کردن به پشته"""
        self.stack.append(item)
    
    def pop(self):
        """حذف از پشته"""
        if not self.is_empty():
            return self.stack.pop()
        return None
    
    def is_empty(self):
        return len(self.stack) == 0
    
    def peek(self):
        """نگاه به عنصر بالای پشته"""
        if not self.is_empty():
            return self.stack[-1]
        return None

# فصل شش - درخت (Tree)

# سوال 1-6: تبدیل عبارت postfix به infix
def postfix_to_infix(postfix):
    """تبدیل عبارت postfix به infix"""
    stack = Stack()
    
    for char in postfix:
        if char.isalnum():  # اگر عدد یا حرف باشد
            stack.push(char)
        else:  # اگر عملگر باشد
            operand2 = stack.pop()
            operand1 = stack.pop()
            expression = f"({operand1}{char}{operand2})"
            stack.push(expression)
    
    return stack.pop()

print(f"\n--- فصل شش - درخت ---")
print(f"تبدیل 'ab+c*' به infix: {postfix_to_infix('ab+c*')}")

# سوال 2-6: تجزیه عبارت لیستی معکوس
def reverse_list_expression(expr):
    """تجزیه و معکوس کردن عبارت لیستی"""
    stack = Stack()
    result = []
    
    for char in expr:
        if char == '(':
            stack.push(char)
        elif char == ')':
            temp = []
            while not stack.is_empty() and stack.peek() != '(':
                temp.append(stack.pop())
            if not stack.is_empty():
                stack.pop()  # حذف '('
            result.extend(temp)
        else:
            stack.push(char)
    
    while not stack.is_empty():
        result.append(stack.pop())
    
    return ''.join(result)

# سوال 3-6: تبدیل عبارت ریاضی به درخت
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

def build_expression_tree(postfix):
    """ساخت درخت عبارت از postfix"""
    stack = []
    
    for char in postfix:
        node = TreeNode(char)
        
        if char in ['+', '-', '*', '/']:
            node.right = stack.pop()
            node.left = stack.pop()
        
        stack.append(node)
    
    return stack[0] if stack else None

def inorder_traversal(root):
    """پیمایش درخت به صورت inorder"""
    if root:
        inorder_traversal(root.left)
        print(root.value, end=' ')
        inorder_traversal(root.right)

# تست درخت عبارت
print(f"\nساخت درخت عبارت از 'ab+c*':")
tree = build_expression_tree('ab+c*')
print("پیمایش inorder:")
inorder_traversal(tree)

# سوال 4-6: جستجوی دودویی در درخت
class BinarySearchTree:
    def __init__(self):
        self.root = None
    
    def insert(self, value):
        """درج عنصر در درخت جستجوی دودویی"""
        if self.root is None:
            self.root = TreeNode(value)
        else:
            self._insert_recursive(self.root, value)
    
    def _insert_recursive(self, node, value):
        if value < node.value:
            if node.left is None:
                node.left = TreeNode(value)
            else:
                self._insert_recursive(node.left, value)
        else:
            if node.right is None:
                node.right = TreeNode(value)
            else:
                self._insert_recursive(node.right, value)
    
    def search(self, value):
        """جستجو در درخت"""
        return self._search_recursive(self.root, value)
    
    def _search_recursive(self, node, value):
        if node is None or node.value == value:
            return node
        
        if value < node.value:
            return self._search_recursive(node.left, value)
        else:
            return self._search_recursive(node.right, value)

# تست BST
print(f"\n\n--- درخت جستجوی دودویی ---")
bst = BinarySearchTree()
values = [50, 30, 70, 20, 40, 60, 80]
for val in values:
    bst.insert(val)

search_result = bst.search(40)
print(f"جستجوی عدد 40: {'یافت شد' if search_result else 'یافت نشد'}")

# سوال 5-6: کوچک‌ترین و بزرگ‌ترین عنصر در درخت
def find_min(node):
    """یافتن کوچک‌ترین عنصر"""
    while node.left:
        node = node.left
    return node.value

def find_max(node):
    """یافتن بزرگ‌ترین عنصر"""
    while node.right:
        node = node.right
    return node.value

print(f"کوچک‌ترین عنصر: {find_min(bst.root)}")
print(f"بزرگ‌ترین عنصر: {find_max(bst.root)}")

# سوال 6-6: محاسبه ارتفاع درخت
def tree_height(node):
    """محاسبه ارتفاع درخت"""
    if node is None:
        return -1
    
    left_height = tree_height(node.left)
    right_height = tree_height(node.right)
    
    return max(left_height, right_height) + 1

print(f"ارتفاع درخت: {tree_height(bst.root)}")

# سوال 7-6: تفاوت بین درخت و لیست
print(f"\n--- تفاوت درخت و لیست ---")
print("درخت: ساختار سلسله‌مراتبی، جستجوی سریع (O(log n) در BST)")
print("لیست: ساختار خطی، جستجوی کند (O(n))")
print("درخت برای جستجو و مرتب‌سازی بهتر است")
print("لیست برای دسترسی ترتیبی و عملیات ساده بهتر است")