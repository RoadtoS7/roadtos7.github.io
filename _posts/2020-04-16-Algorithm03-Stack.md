---
title: "DataStructure01 - SinglyLinkedList(단일 연결리스트)"
date: "2020-04-16 20:37:17 -400"
categories: Algorithm
---

## Stack
### 1. List 로 구현
```
class Stack(object):
  def __init__(self):
    self.data = []
    
  def push(self, data):
    self.data.append(data)
  
  def pop(self):
    if len(self.data) == 0:
      return -1
    else:
      return self.data.pop()
      
  def peek(self):
    if len(self.data) == 0:
      return -1
    else:
      return self.data[-1]
  
```

### 2. SinglyLinkedList로 구현
```
class Node:
  def __init__(self,data):
    self.data = data
    self.head = None
    
    
class Stack(object):
  def __init__(self):
    self.head = None
    
  def push(self, data):
      newNode = Node(data)
      newNode.next = self.head
      self.head = newNode
      
  def pop(self):
    if self.head:
      data = self.head.data
      self.head = self.head.next
      return data
    else:
      return -1
      
  def peek(self):
    if self.head:
      data = self.head.data
      return data
    else:
      return -1
      
  def print(self):
    cur = self.head
    string = ""
    while cur:
      string += str(cur.data)
      if cur.next:
        string += "=>"
      cur = cur.next
       
```

## 3. test code
```
  stack = Stack()
  stack.push(1)
  stack.push(2)
  stack.push(3)
  print(s.peek()) # 3
  print(s.pop()) # 3
  print(s.pop()) # 2
  print(s.pop()) # 1
  print(s.pop()) # -1 
```
